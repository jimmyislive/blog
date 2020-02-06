---
title: "'Remember Me' using the Go gorilla framework"
date: 2020-02-05
draft: false
tags: ["golang", "sessions", "gorilla", "rememberme"]
---
A 'Remember Me' type functionality is something that is available on most websites today. Essentially what it does is that if the checkbox is checked when logging in, the session is kept logged in for a much longer duration (say a week or month). Otherwise, when you close the browser, the session terminates and you have to log in again the next time you visit the site.

In golang, it is implemented easily using the [gorilla toolkit](https://www.gorillatoolkit.org/). In this example we will be using the [sessions package](https://www.gorillatoolkit.org/pkg/sessions). The basic idea is as follows:

* On startup, create a session store. This is a place where session information will be stored. You can store sessions inside of a cookie (some pros/cons here) or a database table (different pros/cons here). We will use a database table and a [pgstore extension](https://github.com/antonlindstrom/pgstore) to help us with that.

* When a request comes in, extract the session id from the cookie. If the session id is present in your DB, load it. The session data should tell you if the person is logged in already or not. If logged in, proceed. If not, redirect to a login page.

* When the login request comes in, check if the "remember me" box is checked. If it is not checked, set the session to expire when the browser is closed i.e. set a cookie with the MaxAge as 0. If checked, set the session to expire after a much longer duration, say a week. i.e. set a cookie with the MaxAge as 86400 * 7

* You can store any type of data you want to remember b/w sessions in the sessions store. The cookie, which is stored on the client browser, will typically contain a session id i.e. the id of the postgres row which contains your session data (don't store any confidential info like emails or user ids in the client side cookie).

We will first show the code to do the above and then show that it does not actually work ! The problem and fix is outlined next.

# The basic idea

The pseudo code for making it work is as follows:

{{< highlight go "linenos=table" >}}
var sessionStore *pgstore.PGStore

type SessionData struct {
	UserID  uint
	Auth    bool
}

func init() {
    // psqlInfo will contain ur db connection info in the format: "host=%s port=%d user=%s password=%s dbname=%s sslmode=disable"
	sessionStore, err = pgstore.NewPGStore(psqlInfo, []byte(os.Getenv("YOUR_AUTH_KEY")))
    ...
    ...
}

// Load the session in a middleware
func sessionMiddleware(next http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {

		session, err := sessionStore.Get(r, CookieName)
		if err != nil {
			// redirect the user to the home page
		}

		ctx := context.WithValue(r.Context(), SessionCtxKey, session)

		next.ServeHTTP(w, r.WithContext(ctx))
	})
}

func handleLogin(w http.ResponseWriter, r *http.Request) {
    session := r.Context().Value(SessionCtxKey).(*sessions.Session)

    // check the login here, return if no good

    // user login checks out fine
    if r.FormValue("rememberme") == "on" {
		session.Options.MaxAge = 86400 * 7 // 1 week
	} else {
		session.Options.MaxAge = 0
	}

	var data *SessionData
    data = &SessionData{
        // store anything you want here like a user_id, authenticated or not etc
    }

	session.Values["data"] = data
	err = session.Save(r, w)

    // The user is logged in now...
    http.Redirect(w, r, "/dashboard", http.StatusFound)
	return
}

{{< / highlight >}}

# But it does not work !

Only problem is, the above does not actually work. If you log in with the "remember me" checked and you examine the cookie after logging in, you will see that it is still a session cookie i.e. it will expire after the browser is closed !

The reason it is happening is that when the new request comes in after logging in, the session parameters are reset to the default of the toolkit. The session data from the DB will be there, but the options on the request session like MaxAge etc will be set to the default of the framework. e.g. 

{{< highlight go "linenos=table" >}}
func authMiddleware(next http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        session := r.Context().Value(SessionCtxKey).(*sessions.Session)

		if session.IsNew == true {
			http.Redirect(w, r, "/login", http.StatusFound)
			return
		}

		val := session.Values["data"]
		cd, ok := val.(SessionData)
		if !ok {
			http.Redirect(w, r, "/login", http.StatusFound)
			return
		}

        // At this point cd contains all the data you stored in the DB for the session
        // However the properties like session.Options.MaxAge are set to the default which is probably 0
        // i.e. you get back to a session cookie

    })
}
{{< / highlight >}}

# The fix

In order to fix this, we need to store information about the expiry time in the session data and then when we pull the session data out, update the request session properties. We can change it like so:

{{< highlight go "linenos=table" >}}

type SessionData struct {
	UserID  uint
	Auth    bool
	Expires time.Time
}

func authMiddleware(next http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        session := r.Context().Value(SessionCtxKey).(*sessions.Session)

		...

        if !cd.Expires.IsZero() {
			timeDelta := cd.Expires.Sub(time.Now()).Seconds()
			if timeDelta < 0 {
				session.Options.MaxAge = -1
			} else {
				session.Options.MaxAge = int(timeDelta)
			}

			err := session.Save(r, w)
			if err != nil {
				http.Error(w, err.Error(), http.StatusInternalServerError)
				return
			}
		}

        // From now on, the session MaxAge is correctly populated and hence the returned cookie will have the correct Expires header set

    })
}

{{< / highlight >}}

I spent a couple of hours chasing this bug down. Hopefully it saves someone else time !
