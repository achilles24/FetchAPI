**It provides a global fetch() method that provides an easy, logical way to fetch resources asynchronously across the network**.
**Fetch is promise-based** and provides a better alternative that can be easily used in service workers. 
Fetch also integrates advanced HTTP concepts such as CORS and other extensions to HTTP.

```
async function logMovies() {
  const response = await fetch("http://example.com/movies.json");
  const movies = await response.json();
  console.log(movies);
}
```

The simplest use of **fetch() takes one argument** — the path to the resource you want to fetch — and **does not directly return the JSON response body 
but instead returns a promise that resolves with a Response object**.
The **Response object, in turn, does not directly contain the actual JSON response body but is instead a representation of the entire HTTP response**. 
So, **to extract the JSON body content from the Response object, we use the json() method, which returns a second promise that resolves with the result of parsing the response body text as JSON**.

The **fetch() method can optionally accept a second parameter, an init object** that allows you to control a number of different settings:

```
// Example POST method implementation:
async function postData(url = "", data = {}) {
  // Default options are marked with *
  const response = await fetch(url, {
    method: "POST", // *GET, POST, PUT, DELETE, etc.
    mode: "cors", // no-cors, *cors, same-origin
    cache: "no-cache", // *default, no-cache, reload, force-cache, only-if-cached
    credentials: "same-origin", // include, *same-origin, omit
    headers: {
      "Content-Type": "application/json",
      // 'Content-Type': 'application/x-www-form-urlencoded',
    },
    redirect: "follow", // manual, *follow, error
    referrerPolicy: "no-referrer", // no-referrer, *no-referrer-when-downgrade, origin, origin-when-cross-origin, same-origin, strict-origin, strict-origin-when-cross-origin, unsafe-url
    body: JSON.stringify(data), // body data type must match "Content-Type" header
  });
  return response.json(); // parses JSON response into native JavaScript objects
}

postData("https://example.com/answer", { answer: 42 }).then((data) => {
  console.log(data); // JSON data parsed by `data.json()` call
});
```

**Checking that the fetch was successful**

An accurate check for a successful fetch() would include checking that the promise resolved, then **checking that the Response.ok property has a value of true**.

```
async function fetchImage() {
  try {
    const response = await fetch("flowers.jpg");
    if (!response.ok) {
      throw new Error("Network response was not OK");
    }
    const myBlob = await response.blob();
    myImage.src = URL.createObjectURL(myBlob);
  } catch (error) {
    console.error("There has been a problem with your fetch operation:", error);
  }
}
```

**Response objects**

 Response instances are returned when fetch() promises are resolved.

The most common response properties you'll use are:

* Response.status — An integer (default value 200) containing the response status code.
* Response.statusText — A string (default value ""), which corresponds to the HTTP status code message. Note that HTTP/2 does not support status messages.
* Response.ok — seen in use above, this is a shorthand for checking that status is in the range 200-299 inclusive. This returns a boolean value.
