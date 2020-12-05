Here I am writing some code to understand Ajax, callback and promise.


Ajax stands for Asynchronous Javascript And XML, but because now Json is widely used, xml is just in its name. 

Ajax is great, the first time I heard it, the great guy told me Ajax is the fundamental thing of web application. At that time I didn't understand honestly, but later I did.

There are something about Asynchronous of Javascript and event loop, maybe I should create another post later. So, here I focus on Ajax related callback, promise.

So what we do if we want to make a asynchronous request:

```Javascript
let xhr = new XMLHttpRequest();
xhr.open(methodType, URL, isAsync);
xhr.send();
```

After `xhr` get something, we need to do something, so we add a callback after send:

```Javascript
function makeAjaxCall(url, methodType){
	let xhr = new XMLHttpRequest();
  xhr.open(methodType, url, true);
  xhr.send();
  xhr.onreadystatechange = function(){
  		if (xhr.readyState === 4){
         if (xhr.status === 200){
         		console.log("xhr done successfully");
            let resp = xhr.responseText;
            let respJson = JSON.parse(resp);
            document.getElementById("userid").innerHTML = respJson.login;
            document.getElementById("name").innerHTML = respJson.name;
            document.getElementById("company").innerHTML = respJson.company;
            document.getElementById("blog").innerHTML = respJson.blog;
            document.getElementById("location").innerHTML = respJson.location;
         } else {
         		console.log("xhr failed");
         }
      } else {
      		console.log("xhr processing going on");
      }
  }
  console.log("request sent succesfully");

}
let URL = "https://api.github.com/users/HanfordWu";
makeAjaxCall(URL, "GET");
```
https://jsfiddle.net/HanfordWu/nxf1cbm6/3/

output:
```Javascript
"request sent successfully"
"xhr processing going on"
"xhr processing going on"
"xhr done successfully"
```

Callback:

callback is something we will do later. It's a function, it will be run sometimes later. 

Above example, we can extract a callback function:

```Javascript
displayInfo = (resp) => {
    let respJson = JSON.parse(resp);
    document.getElementById("userid").innerHTML = respJson.login;
    document.getElementById("name").innerHTML = respJson.name;
    document.getElementById("company").innerHTML = respJson.company;
    document.getElementById("blog").innerHTML = respJson.blog;
    document.getElementById("location").innerHTML = respJson.location;
}

function makeAjaxCall(url, methodType, callback){
    let xhr = new XMLHttpRequest();
    xhr.open(methodType, url, true);
    xhr.send();
    xhr.onreadystatechange = function(){
            if (xhr.readyState === 4){
            if (xhr.status === 200){
                    console.log("xhr done successfully");
                let resp = xhr.responseText;
                callback(resp);
            } else {
                    console.log("xhr failed");
            }
        } else {
                console.log("xhr processing going on");
        }
    }
    console.log("request sent succesfully");

}
let URL = "https://api.github.com/users/HanfordWu";
makeAjaxCall(URL, "GET", displayInfo);
```

Why we need callback? Because we can pass into different callback function to do different thing. In other words, the `makeAjaxCall` can be reused by different handlers.

But the problem of callback is it becomes difficult to do a series of ajax calls which are dependent one by one.

Promise:

Promise is an object. It can be returned by an async function like ajax.

```Javascript
function makeAjaxCall(url, methodType){
   var promiseObj = new Promise(function(resolve, reject){
      var xhr = new XMLHttpRequest();
      xhr.open(methodType, url, true);
      xhr.send();
      xhr.onreadystatechange = function(){
      if (xhr.readyState === 4){
         if (xhr.status === 200){
            console.log("xhr done successfully");
            var resp = xhr.responseText;
            var respJson = JSON.parse(resp);
            resolve(respJson);
         } else {
            reject(xhr.status);
            console.log("xhr failed");
         }
      } else {
         console.log("xhr processing going on");
      }
   }
   console.log("request sent succesfully");
 });
 return promiseObj;
}

makeAjaxCall(URL, "GET").then(processUserDetailsResponse, errorHandler);

makeAjaxCall(URL, "GET").then(processRepoListResponse, errorHandler);

function processUserDetailsResponse(userData){
 console.log("render user details", userData);
}

function processRepoListResponse(repoList){
 console.log("render repo list", repoList);
}

function errorHandler(statusCode){
 console.log("failed with status", status);
}
```
Above we have defined three callback, two of them are resolve handler: processUserDetailsResponse and processRepoListResponse, one is exception handler: errorHandler.

makeAjaxCall function returns a promise object and doesn’t take any callback.



AJAX with JQuery:

```Javascript
function makeAjaxCall(url, methodType, callback){
 $.ajax({
    url : url,
    method : methodType,
    dataType : "json",
    success : callback,
    error : function (reason, xhr){
     console.log("error in processing your request", reason);
    }
   });
 }
// github url to get HanfordWu details
var URL = "https://api.github.com/users/HanfordWu";
makeAjaxCall(URL, "GET", function(respJson){
 document.getElementById("userid").innerHTML = respJson.login;
 document.getElementById("name").innerHTML = respJson.name;
 document.getElementById("company").innerHTML = respJson.company;
 document.getElementById("blog").innerHTML = respJson.blog;
 document.getElementById("location").innerHTML = respJson.location;
});
```

Because $.ajax returns a promise object, so we can also do this:

```Javascript
function makeAjaxCall(url, methodType, callback){
 return $.ajax({
    url : url,
    method : methodType,
    dataType : "json"
 })
}
// git hub url to get HanfordWu details
var URL = "https://api.github.com/users/HanfordWu";
makeAjaxCall(URL, "GET").then(displayInfo, displayError);

function displayInfo(respJson){
 document.getElementById("userid").innerHTML = respJson.login;
 document.getElementById("name").innerHTML = respJson.name;
 document.getElementById("company").innerHTML = respJson.company;
 document.getElementById("blog").innerHTML = respJson.blog;
 document.getElementById("location").innerHTML = respJson.location;
}

function displayError(reason){
 console.log("error in processing your request", reason);
}

```