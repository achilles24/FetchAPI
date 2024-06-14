## Table of Contents

- [AbortController](#abortcontroller)
- [Handling Search with AbortController](#handling-search-with-abortcontroller)
- [React example](#react-example)
- [Debounce & AbortController](#debounce-&-abortcontroller)

## AbortController

**The AbortController interface represents a controller object that allows you to abort one or more Web requests** as and when desired.

**Constructor**
* AbortController() - Creates a new AbortController object instance.

**Instance properties**
* AbortController.signal - (Read only) **Returns an AbortSignal object instance**, which can be used to communicate with, or to abort, an asynchronous operation.

**Instance methods**
* AbortController.abort() - **Aborts an asynchronous operation before it has completed**. This is able to abort fetch requests, consumption of any response bodies, and streams.

```
let controller;
const url = "video.mp4";

const downloadBtn = document.querySelector(".download");
const abortBtn = document.querySelector(".abort");

downloadBtn.addEventListener("click", fetchVideo);

abortBtn.addEventListener("click", () => {
  if (controller) {
    controller.abort();
    console.log("Download aborted");
  }
});

function fetchVideo() {
  controller = new AbortController();
  const signal = controller.signal;
  fetch(url, { signal })
    .then((response) => {
      console.log("Download complete", response);
    })
    .catch((err) => {
      console.error(`Download error: ${err.message}`);
    });
}
```

We **first create a controller using the AbortController() constructor**, then grab a reference to its associated **AbortSignal object using the AbortController.signal** property.

When the fetch request is initiated, **we pass in the AbortSignal as an option inside the request's options objec**t (the {signal} below). 
This associates the signal and controller with the fetch request and allows us to **abort it by calling AbortController.abort()**, as seen below in the second event listener.

**When abort() is called, the fetch() promise rejects with a DOMException named AbortError.**


###How to cancel Javascript API request with AbortController

The fetch API is a JavaScript API for making HTTP requests. It is a replacement for XMLHttpRequest, which is deprecated in most browsers.
It is a promise-based API, meaning that it returns a Promise object that is resolved or rejected when the request is complete.

```
fetch(url)
  .then((response) => response.json())
  .then((data) => console.log(data))
  .catch((error) => console.error(error));
```

**This is great but sometimes we want to cancel the already initiated request.**

Suppose we have search input we want to show the suggestions as the user types. when the user types a character, we call the fetch() function 
and pass the value of the search input field as the URL parameter.

```
fetch(`${url}?q=${searchInput.value}`)
  .then((response) => response.json())
  .then((data) => console.log(data))
  .catch((error) => console.error(error));
```

**Problem:**
The fetch() function is called every time the user types a character, This is an inefficient approach as it is called multiple times and 
we are only interested in the last response.

**Solution:**
Use debounce() function to limit the number of times the fetch() function is called. It will only be called after the user has stopped typing for a certain period (100ms). 
This is a good practice to avoid unnecessary calls to the API.

**Edge case:** What if the user starts typing just after debounce() has been called. In that case, the fetch() function will be called again.
It's better than the previous approach.

**How can we handle this?**
This is where AbortController comes in handy. We can use AbortController to cancel the already initiated request(using fetch). The modern browsers come with a built-in AbortController interface.

## Handling Search with AbortController

```html
<html>
  <body>
    <input id="search" type="number" />
    <script>
      const results = [];
      const search = document.getElementById("search");
      let controller = new AbortController();
      let signal = controller.signal;

      const getPost = async (value, signal) => {
        try {
          const response = await fetch(
            `https://jsonplaceholder.typicode.com/posts/${value}`,
            { signal }
          );
          results.push(`Success: ${value}`);
        } catch (error) {
          if (error.name === "AbortError") {
            results.push("API failure");
          } else {
            console.log("Some other error");
          }
        } finally {
          console.log("Status", results);
        }
      };
      const onChange = () => {
        const value = search.value;
        if (value) {
          controller.abort();
          controller = new AbortController();
          signal = controller.signal;
          getPost(value, signal);
        }
      };
      search.onkeyup = onChange;
    </script>
  </body>
</html>
```

## React example

```javascript
useEffect(() => {
  const controller = new AbortController();
  const signal = controller.signal;
  const results = [];
  const getPost = async (value) => {
    try {
      const response = await fetch(
        `https://jsonplaceholder.typicode.com/posts/${value}`,
        { signal }
      );
      results.push(`Success: ${value}`);
    } catch (error) {
      if (error.name === "AbortError") {
        results.push("API failure");
      }
    } finally {
      console.log("Status", results);
    }
  };

  getPost(1);

  return () => {
    // Cancel the request on unmount
    controller.abort();
  };
}, []);
```

## Debounce & AbortController

**Here is the sample example for Debounce & AbortController using Fetch methods**

```html
<html>
  <body>
    <input id="search" type="number" />
    <script>
      const results = [];
      const search = document.getElementById("search");
      let controller = new AbortController();
      let signal = controller.signal;

      // Debounce function
      const debounce = (func, delay) => {
        let timeoutId;
        return (...args) => {
          clearTimeout(timeoutId);
          timeoutId = setTimeout(() => {
            func(...args);
          }, delay);
        };
      };

      const getPost = async (value, signal) => {
        try {
          const response = await fetch(
            `https://jsonplaceholder.typicode.com/posts/${value}`,
            { signal }
          );
          results.push(`Success: ${value}`);
        } catch (error) {
          if (error.name === "AbortError") {
            results.push("API failure");
          } else {
            console.log("Some other error");
          }
        } finally {
          console.log("Status", results);
        }
      };

      // Debounced version of onChange function
      const debouncedOnChange = debounce((value) => {
        if (value) {
          controller.abort();
          controller = new AbortController();
          signal = controller.signal;
          getPost(value, signal);
        }
      }, 300); // Adjust the delay as needed

      // Attach debouncedOnChange to the input event
      search.oninput = (event) => {
        const value = event.target.value;
        debouncedOnChange(value);
      };
    </script>
  </body>
</html>
```
