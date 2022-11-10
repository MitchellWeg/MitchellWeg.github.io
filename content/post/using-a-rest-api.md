---
title: "Using a REST API"
date: "2022-11-09"
tags: ["rest-api"]
---

### What is REST?
A RESTful API, mostly truncated to a REST API, is a way to communicate with your backend. 

REST utilizes a number of methods. The most popular are: __GET__, __POST__, __PUT__, __PATCH__ and __DELETE__

- __GET__:
 most common method. This _gets_ a resource from the service.
- __POST__:
This creates a resource from on the service.
- __PUT__:
This creates or replaces a resource on the service. The difference between __PUT__ and __POST__ is that calling it multiple times has the same effect, meaning its _idempotent_.
- __PATCH__:
This updates a resource on the service.
- __DELETE__:
This deletes a resource on the service.

### Returned values from a REST API
The main reason why a REST API is used is because it can handle _requests_. a _request_ is exactly what it sounds like. You can _request_ the API to do something, and it will perform the request (as long as you have the appropriate credentials).

You can request the API to fetch an object for you using the GET method. You can request the API to store an object (e.g. store a new user after signing up) using the POST method. 

When you request the API to fetch you something, it will return it in a standardized format. Most of the times that means it will be returned in JSON. JSON (or JavaScript Object Notation) is a interchange format where objects can be encoded in text. JSON is easy and lightweight to parse. JSON is written in the following way:

```json
{
    "id": "1",
    "value": "foo",
    "array": ["i", "am", "a", "array"]
}
```

Most modern programming languages have a library to parse JSON into native objects in their respective languages. In Python you can use the JSON library:

```python
import json

json_str = """
            {
                "id": "1",
                "value": "foo",
                "array": ["i", "am", "a", "array"]
            }
         """

parsed_json = json.loads(json_str)

# This results in a dictionary:
print(parsed_json['id'])

# '1'
```

### How to use a REST API
You have a number of options on how to _consume_ a REST API. Either with a programming language, the built-in tool _cURL_, or a graphical tool like [Postman](https://www.postman.com/).

Virtually every programming language has a HTTP package. Python has __HTTP__ or the easier __requests__ library.

A snipper from the requests library:

GET request:
```python
import requests

url = 'https://my-api.com/api/get-route'

x = requests.get(url)

print(x.content)
```

POST request:
```python
import requests

url = 'https://my-api.com/api/post-route'
obj = {'somekey': 'somevalue'}

x = requests.post(url, json = obj)

print(x.content)
```

cURL can also be used. cURL is a command-line tool that can be used to query HTTP services. It also can be used to consume a REST API:

GET request:
```bash
curl https://my-api.com/api/get-route
```

POST request:
```bash
curl -X POST -H "Content-Type: application/json" \
    -d '{"somekey": "somevalue"}' \
https://my-api.com/api/post-route
```

Sometimes, JSON output can be hard to read in the terminal. For that you can use the `json_pp` (JSON Pretty Printer) tool. Simply _pipe_ the output of the command into it, and voilà:

```bash
json_pp | curl https://my-api.com/api/get-route
```

### Separate API vs Monolithic
Another way of implementing is to create a complete _monolithic_ application. This means that your frontend and logic are mostly interwoven. This has some advantages, but also a number of drawbacks.

An advantage of a monolithic application is the ease of setting it up and maintaining if the application is small. In a monolithic application, the logic and frontend are mostly interwoven; this makes it easier to change the logic since you know where to look.

This proves disastrous for scalability. When only some components in your backend receive the bulk of the traffic, those cannot be scaled up individually. When your application needs upscaling, the whole application needs to be scaled up, which would mostly be redundant.

When your API and frontend are completely separated, you're able to scale them up indivually if need be.

Another advantage of a separate API is the ability to use the best tool for the job. Need something that is simple? Use Python's [Flask](https://flask.palletsprojects.com/en/2.2.x/). Need something more performant? Use Go's [mux](https://github.com/gorilla/mux) or Rust's [rocket](https://rocket.rs/). You are not bound by just one programming language.

With a separate REST API, the backend is not bound by the frontend, but it's the other way around. The API does not decide what the UI will look like. This way, it's easier to change something in the frontend without breaking something. Since the API does not get to decide what the frontend looks like, it also can be used for multiple frontends. Meaning that if you want to have a website but also a mobile app, they both can use the same backend.