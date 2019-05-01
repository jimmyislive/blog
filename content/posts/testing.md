---
title: "Testing with Go"
date: 2019-04-28T13:00:00
draft: false
tags: ["golang", "testing"]
---
An integral part of the development licefycle is writing tests. Write tests early, write tests often as they say. Go makes it very easy to write tests. The following post will go through the basics of testing with Go.

Let's say we have a program that counts the number of vowels, consonants, digits and everything else within an input string. It may look something like this:

{{< highlight go "linenos=table" >}}
package main

import (
        "flag"
        "fmt"
)

// CountChars counts the number of characters in a string
func CountChars(str string) (int, int, int, int) {
    vcount := 0
    ccount := 0
    dcount := 0
    ocount := 0

    for i := 0; i < len(str); i++ {
        switch str[i] {
        case 'a', 'e', 'i', 'o', 'u', 'A', 'E', 'I', 'O', 'U':
                vcount++
        case 'b', 'c', 'd', 'f', 'g', 'h', 'j', 'k', 'l', 'm', 'n', 'p', 'q', 'r',      's', 't', 'v', 'w', 'x', 'y', 'z',
             'B', 'C', 'D', 'F', 'G', 'H', 'J', 'K', 'L', 'M', 'N', 'P', 'Q', 'R', 'S', 'T', 'V', 'W', 'X', 'Y', 'Z':
                ccount++
        case '1', '2', '3', '4', '5', '6', '7', '8', '9', '0':
            dcount++
        default:
            ocount++
        }

    }

    return vcount, ccount, dcount, ocount
}

func main() {
    inputStr := flag.String("inputStr", "", "The input string")

    flag.Parse()

    vowels, consonants, digits, other := CountChars(*inputStr)
    fmt.Println("Number of vowel chars: ", vowels)
    fmt.Println("Number of consonant chars: ", consonants)
    fmt.Println("Number of digit chars: ", digits)
    fmt.Println("Number of other chars: ", other)
}
{{< / highlight >}}

Go provides a [testing](https://golang.org/pkg/testing/) package to help with writing unit tests. Unit tests are contained in files typically ending in `*_test.go`. So to test the main function, you would have `main_test.go` in the same directory and so on. Files whose names begin with `_` or `.` are ignored. There are three types of functions that are typically contained in a test file: `TestXxx`, `ExampleXxx`, `BenchmarkXxx`

# TestXxx

These are your standard unit tests. They should have a signature like: `func TestXxx(t *testing.T)`. *Note*: `Xxx` indicates the test name and has to begin with a capital letter, else no tests will get run.(typically indicated by a warning like: `testing: warning: no tests to run`)

Go has popularized [TableDrivenTests](https://github.com/golang/go/wiki/TableDrivenTests). It lists out the inputs / desired outputs for various test cases as an array of structs and then just cycles through these tests checking to see if the tests passed. It leads to very concise and easily understandable unit test code. Here is what a `main_test.go` could look like:

{{< highlight go "linenos=table" >}}
package main

import (
    "fmt"
    "testing"
)

var charTest = []struct {
    name   string
    input  string
    counts [4]int
}{
    {"all counts populated test", "hello123, world", [4]int{3, 7, 3, 2}},
    {"no digits test", "gltmx", [4]int{0, 5, 0, 0}},
    {"empty string test", "", [4]int{0, 0, 0, 0}},
    {"no letters test", "1234 ,*&", [4]int{0, 0, 4, 4}},
    {"a very long test", "skip-me", [4]int{0, 0, 0, 0}},
    {"valid string", "Mary had a l1tt3e lamb.", [4]int{5, 11, 2, 5}},
}

func TestCountChars(t *testing.T) {
    for _, test := range charTest {

        if testing.Short() && test.name == "a very long test" {
            t.Skip("skipped test as we are in a hurry")
        }

        vowels, consonants, digits, other := CountChars(test.input)
        if vowels != test.counts[0] {
            t.Errorf("%s: Vowel count mismatch: expected %d, got %d", test.name, test.counts[0], vowels)
        }
        if consonants != test.counts[1] {
            t.Errorf("%s: Consonants count mismatch: expected %d, got %d", test.name, test.counts[1], consonants)
        }
        if digits != test.counts[2] {
            t.Errorf("%s: Digits count mismatch: expected %d, got %d", test.name, test.counts[2], digits)
        }
        if other != test.counts[3] {
            t.Errorf("%s: Other count mismatch: expected %d, got %d", test.name, test.counts[3], other)
        }
    }
}
{{< / highlight >}}

# ExampleXxx

These type of tests are primarily aids to documentation. When documentation of your package is generated via `godoc`, these examples are extracted and displayed so that the user can understand things more clearly. You can see many examples in the standard [go packages](https://golang.org/pkg/math/#example_Acos)

A few points to note about these example tests:

* They take no input
* They are determined to PASS if the stdout matches the output specified in the example, else they FAIL
* If the output is not specified, the tests are compiled but not run

Here is what it would look like:

{{< highlight go "linenos=table" >}}
func ExampleCountChars() {
        vowels, consonants, digits, other := CountChars("Mary had a l1tt3e lamb.")

        fmt.Println("", vowels, consonants, digits, other)
        // Output: 5 11 2 5
}

{{< /highlight >}}

# BenchmarkXxx

These tests are used to gain timing info of functions. We essentially place the function we wish to benchmark in a loop and run it `b.N` times. (This value is determined by the benchmark runner)

{{< highlight go "linenos=table" >}}
func BenchmarkCountChars(b *testing.B) {
    for i := 0; i < b.N; i++ {
        _, _, _, _ = CountChars("Mary had a l1tt3e lamb.")
    }
}
{{< /highlight >}}

Keeping track of the benchmark results over time can be a good way to track drift in performance of your codebase.

# Running tests

An easy way to run the above test is by using a `Makefile`. It could look something like:

{{< highlight bash >}}
.PHONY : test

test:
    @cd cmd/gocountchars && go test -bench . -short -v
{{< /highlight >}}

Here we have supplied the `-short` argument to indicate that we don't want to run long tests. Hence inside our test code we can test to see if this flag is on via `testing.Short()` and act accordingly (as shown in the example above). The `-v` flag is for verbosity. Then, to run the tests: `make test`

The advantage of using Makefiles is that it can be easily integrated into other tools such as Jenkinsfile also. Running the tests will show an output like:

{{< highlight bash >}}
=== RUN   TestCountChars
--- SKIP: TestCountChars (0.00s)
    main_test.go:25: skipped test as we are in a hurry
=== RUN   ExampleCountChars
--- PASS: ExampleCountChars (0.00s)
goos: darwin
goarch: amd64
pkg: github.com/jimmyislive/gocountchars/cmd/gocountchars
BenchmarkCountChars-8   	30000000	        58.3 ns/op
PASS
ok  	github.com/jimmyislive/gocountchars/cmd/gocountchars	1.833s
{{< /highlight >}}

All code for this can be found on [Github](https://github.com/jimmyislive/gocountchars)

