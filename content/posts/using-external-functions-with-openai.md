---
title: "Using External Functions with OpenAI"
date: 2024-11-27
draft: false
tags: ["tech"]
---
An easy way to boost the capabilities of AI models is to provide it with external information that it may not have. e.g. maybe your company has some proprietary data that would never be included while training the models. But if you could provide the model with this info during inferencing, it would be able to provide more accurate results.

One way of doing that with OpenAI is via `function calls`. You define a function with all its input parameters and describe what the function does. The model can then use this info and determine when it would be useful to call the function. The client then calls the function and provides the result back to the model for the final result.

Let's clarify this with an example.

Let's say we are storing user addresses for a ride sharing or delivery app. After the ride/delivery is complete we want to mask the address so as to protect user privacy. However we still want to be able to do analytics on this info e.g. how many rides from within a zip code, which area is getting the most deliveries etc.

One option is to geomask the address i.e. add a delta to the lat/long of the original address so that the original address is not identifiable but it's still in the general vicinity. However, this masking is a function of population density in that area. For a urban area the delta needs to be small (as there is a high concentration of addresses) while in a rural area the delta probably needs to be large (as the closest neighbour may be a large distance away)

Let's ask OpenAI to help us. We will in turn help OpenAI by providing it a function call that provides population densities given a zip code, so that it can mask an address accordingly.

The address I will use is `415 Mission St, San Francisco, CA 94105` (The [salesforce tower](https://en.wikipedia.org/wiki/Salesforce_Tower))

{{< highlight python "linenos=table" >}}

client = OpenAI()

tools = [
    {
        "type": "function",
        "function": {
            "name": "population_density_by_zipcode",
            "description": "Get the population density given a zipcode. Call this whenever you want to know what the population density in a particular zip code is.",
            "parameters": {
                "type": "object",
                "properties": {
                    "zipcode": {
                        "type": "string",
                        "description": "The zip code in the address.",
                    },
                },
                "required": ["zipcode"],
                "additionalProperties": False,
            },
        }
    }
]

messages = [
        
    {"role": "system", "content": "You will be provided with an address. Geomask this address taking into account the population density in that area"},

    {"role": "user", "content": "I want to store the address 415 Mission St, San Francisco, CA 94105 such that it preserves privacy."}
]

completion = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=messages,
    tools=tools
)

print(completion.choices[0])


{{< / highlight >}}

Above, I defined a function: `population_density_by_zipcode` and described its purpose to OpenAI (not implementation). This provides OpenAI with the information that such a function is available for its use, should it choose to use it.

When the above code is run, we get the following output:

{{< highlight plaintext "linenos=table" >}}
Choice(finish_reason='tool_calls', index=0, logprobs=None, message=ChatCompletionMessage(content=None, refusal=None, role='assistant', audio=None, function_call=None, tool_calls=[ChatCompletionMessageToolCall(id='call_ORrpaCw5XXXxsJXXXXJt3Xxx', function=Function(arguments='{"zipcode":"94105"}', name='population_density_by_zipcode'), type='function')]))

{{< / highlight >}}

OpenAI is telling us that the function we described needs to be called and the output of it sent back to it. The function call parameters are described as well.

So the client then calls the function with the provided parameters. (Note that this is external to OpenAI)

Let's say the population density I got is: `5902.5400000000`

{{< highlight python "linenos=table" >}}
function_call_result_message = {
            "role": "tool",
            "content": json.dumps({
                "population_density": 3902.5400000000
            }),
            "tool_call_id": completion.choices[0].message.tool_calls[0].id
        }

messages += [completion.choices[0].message]
messages += [function_call_result_message]

final_response = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=messages
)

print(final_response.choices[0].message.content)

{{< / highlight >}}

Essentially, I replayed the message back to OpenAI, this time with the added result of the function call. Voila...

{{< highlight plaintext "linenos=table" >}}
To preserve privacy while considering the population density of 3902.54 per square mile in the area you provided, you could geomask the address as follows:

**Geomasked Address:** "Near Mission St and 1st St, San Francisco, CA 94105"

This maintains some location context while protecting the specific address.

{{< / highlight >}}

As you can see, the exact address has been masked to an approximate one. Yet it is in the same vicinity so it is still usable for any kind of analytics.

We can play around with different density values. e.g. if I decreased the population density to `2902.5400000000`, I get:

**Masked Address:** `400-500 Mission St, San Francisco, CA 94105`

If I still further decrease it to `1902.5400000000`, I get:

**Masked Address:** : `Mission St, San Francisco, CA`

In this way we can augment the capability of any model by using functions that return internal data e.g. from a proprietary DB call, API call etc
