# Handlebars

Handlebars help you add character to your response. Instead of sending a static response or writing some code to generate a dynamic response, you can now simply use handlebars and let Camouflage do the work for you.

## Custom Helpers

## randomValue

Type: Custom Helper
Usage:

1. **{{randomValue}}** - Simply using randomValue will generate a 16 character alphanumeric string. ex: _9ZeBvHW5viiYuWRa_. Why? We'll let you know when we figure that out.
2. **{{randomValue type='ALPHANUMERIC'}}** - You can specify a type as well. Your choices are: 'ALPHANUMERIC', 'ALPHABETIC', 'NUMERIC' and 'UUID'. So, what will it be?
3. **{{randomValue type='NUMERIC' length=10}}** - Don't want a 16 character output? Use length to specify the length. Simple enough.
4. **{{randomValue type='ALPHABETIC' uppercase=true}}** - Finally, specify uppercase as true to get a, well, uppercase string.

## now

Type: Custom Helper
Usage:

1. **{{now}}** - Simply using now will give you date in format _YYYY-MM-DD hh:mm:ss_
2. **{{now format='MM/DD/YYYY'}}** - Format not to your liking? Use any format you'd like as long as it is supported by momentjs. We gave away the secret, didn't we? Don't ask us if you should use MM/DD/YYYY or DD/MM/YYYY. We like to stay away from the controversies.
3. **{{now format='epoch'}}** - Time since epoch in milliseconds
4. **{{now format='unix'}}** - Time since epoch in seconds

## capture

Type: Custom Helper
Usage:

1. **{{capture from='query' key='firstName'}}** - Pretty self-explanatory, but if say your endpoint looks like /hello-world?firstName=John&lastName=Wick. And your response is {"message": "Hello Wick, John"}, you can make the response dynamic by formatting your response as

```
{
    "message": "{{capture from='query' key='lastName'}}, {{capture from='query' key='firstName'}}"
}
```

2. **{{capture from='path' regex='\/users\/get\/(.*)?'}}** - For path you'd need to specify a regex to capture a value.
3. **{{capture from='body' using='jsonpath' selector='$.lastName'}}** - To capture values from request body, your options are either using='regex' or using='jsonpath'. Selector will change accordingly.

## Inbuilt Helpers

!!! note

    A variety of helpers are made available by Handlebar.js itself and Camouflage team had nothing to do with those, and we don't take credit for it. Following example just showcases how the inbuilt helpers can be used with Camouflage, more details and examples can be found just about anywhere on the internet. As far as inbuilt helpers are concerned, you can use any of them as long as it makes sense to you.

Raw HTML Request:

```
POST /hello-world HTTP/1.1
Content-Type: application/json

{
    "firstName": "Robert",
    "lastName": "Downey",
    "nicknames": [
        {
            "nickname": "Bob"
        },
        {
            "nickname": "Rob"
        }
    ]
}
```

Expected Raw HTML Response:

```
HTTP/1.1 201 OK
X-Requested-By: user-service
Content-Type: application/json

{
    "status": 201,
    "message": "User created with ID: f45a3d2d-8dfb-4fc6-a0b2-c94882cd5b91",
    "data": [
        {
            "nickname": "Bob"
        },
        {
            "nickname": "Rob"
        }
    ]
}
```

1. To create this service in camouflage, create a directory users under your ${MOCKS_DIR}. i.e. ${MOCKS_DIR}/users
2. Create a file POST.mock and add following content to the file

```
HTTP/1.1 201 OK
X-Requested-By: user-service
Content-Type: application/json

{
    "status": 201,
    "message": "User created with ID: {{randomValue type='UUID'}}",
    "data": [
        {{#each request.body.nicknames}}
            {{#if @last}}
                {
                    "nickname": "{{this.nickname}}"
                }
            {{else}}
                {
                    "nickname": "{{this.nickname}}"
                },
            {{/if}}
        {{/each}}
    ]
}
```

Explanation

1. We replaced the static UUID `f45a3d2d-8dfb-4fc6-a0b2-c94882cd5b91` with `{{randomValue type='UUID'}}`, so that this value updates on each request.
2. We wrapped our JSONObject inside **data** array with an **each** helper which iterates over **nicknames** array from request body.
3. Finally we put an if condition to check if we are at the last element of the array, we shouldn't append a comma at the end of our JSONObject, in order to get a valid JSON. If we are at any other element in the array, we'll add a comma to JSONObject.

Available inbuilt helpers are `if`, `unless`, `each`, `with`, `lookup` and `log`. More details are available at [Handlebars Documentation](https://handlebarsjs.com/guide/builtin-helpers.html){target=\_blank}