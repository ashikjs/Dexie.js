Dexie.js
========

[![NPM Version][npm-image]][npm-url]

#### What is Dexie.js?
Dexie.js is a wrapper library for indexedDB - the standard database in the browser.

#### Why is Dexie.js needed?
Dexie solves three main issues with the native IndexedDB API:

 1. Ambivalent error handling
 2. Poor queries
 3. Code complexity

Dexie.js solves these limitations and provides a neat database API. Dexie.js aims to be the first-hand choice of a IDB Wrapper Library due to its well thought-through API design, robust error handling, extendability, change tracking awareness and its extended KeyRange support (case insensitive search, set matches and OR operations).

#### Hello World

```html
<html>
 <head>
  <script src="https://npmcdn.com/dexie/dist/dexie.js"></script>
  <script>
   //
   // Declare Database
   //
   var db = new Dexie("FriendDatabase");
   db.version(1).stores({ friends: "++id,name,age" });
   
   //
   // Manipulate and Query Database
   //
   db.friends.add({name: "Josephine", age: 21}).then(function() {
       return db.friends.where("age").below(25).toArray();
   }).then(function (youngFriends) {
       alert ("My young friends: " + JSON.stringify(youngFriends));
   }).catch(function (e) {
       alert ("Error: " + e.stack || e);
   });
  </script>
 </head>
</html>
```

#### Hello World (ES2015 / ES6)

This sample shows how to use Dexie with ES6 compliant environments and npm module resolution. With ES6, the `yield` keyword can be  used instead of calling `.then()` on every database operation. The `yield` keyword and generator functions are already supported today (March 2016) in Chrome, Firefox, Edge and Opera without a transpiler (though this example also uses import statements which still needs transpilation).

See also [Simplify with yield](https://github.com/dfahlander/Dexie.js/wiki/Simplify-with-yield).

```js
import Dexie from 'dexie';

//
// Declare Database
//
let db = new Dexie("FriendDatabase");
db.version(1).stores({ friends: "++id,name,age" });

//
// Have Fun
//
Dexie.spawn(function*() {

    // Make sure we have something in DB:
    if ((yield db.friends.where('name').equals('Josephine').count()) === 0) {
        let id = yield db.friends.add({name: "Josephine", age: 21});
        alert (`Addded friend with id ${id}`);
    }
    
    // Query:
    let youngFriends = yield db.friends.where("age").below(25).toArray();
        
    // Show result:
    alert ("My young friends: " + JSON.stringify(youngFriends));
    
}).catch(e => {
    alert(e);
});
```

#### Hello World (ES2016 / ES7)
```js
import Dexie from 'dexie';
let Promise = Dexie.Promise; // KEEP! (*1)

//
// Declare Database
//
var db = new Dexie("FriendDatabase");
db.version(1).stores({ friends: "++id,name,age" });

async function haveFun () {

    // Make sure we have something in DB:
    if ((await db.friends.where('name').equals('Josephine').count()) === 0) {
        let id = await db.friends.add({name: "Josephine", age: 21});
        alert (`Addded friend with id ${id}`);
    }
    
    // Query:
    let youngFriends = await db.friends.where("age").below(25).toArray();
        
    // Show result:
    alert ("My young friends: " + JSON.stringify(youngFriends));
}

haveFun().catch(e => {
    alert(e);
});

```
_*1: Makes it safe to use async / await within transactions. ES7 async keyword will take the Promise implementation of the current scope. Dexie.Promise can track transaction scopes, which is not possible with the standard Promise. This declaration needs only to be local to the scope where your async functions reside. If working with different promise implementations in the same module, declare your async functions in a block and put the declaration there `{ let Promise = Dexie.Promise; async function (){...} }` ._

#### Hello World (Typescript)

```js
import Dexie from 'dexie';
let Promise = Dexie.Promise; // KEEP! (See *1 above)

interface IFriend {
    id?: number;
    name?: string;
    age?: number;
}

//
// Declare Database
//
class FriendDatabase extends Dexie {
    friends: Dexie.Table<IFriend,number>;
    
    constructor() {
        super("FriendsDatabase");
        this.version(1).stores({
            friends: "++id,name,age"
        });
    }
}

var db = new FriendDatabase();

async function haveFun () {

    // Make sure we have something in DB:
    if ((await db.friends.where('name').equals('Josephine').count()) === 0) {
        let id = await db.friends.add({name: "Josephine", age: 21});
        alert (`Addded friend with id ${id}`);
    }
    
    // Query:
    let youngFriends = await db.friends.where("age").below(25).toArray();
        
    // Show result:
    alert ("My young friends: " + JSON.stringify(youngFriends));
}

haveFun().catch(e => {
    alert(e);
});
```

Documentation
-------------
[https://github.com/dfahlander/Dexie.js/wiki/Dexie.js](https://github.com/dfahlander/Dexie.js/wiki/Dexie.js)

Samples
-------
https://github.com/dfahlander/Dexie.js/wiki/Samples

https://github.com/dfahlander/Dexie.js/tree/master/samples

Forum
-----
[https://groups.google.com/forum/#!forum/dexiejs](https://groups.google.com/forum/#!forum/dexiejs)

Website
-------
[http://dexie.org](http://dexie.org)

Install over npm
----------------
```
npm install dexie
```

Download
--------
For those who don't like package managers, here's the download links:

https://npmcdn.com/dexie/dist/dexie.min.js

https://npmcdn.com/dexie/dist/dexie.min.js.map

https://npmcdn.com/dexie/dist/dexie.d.ts


Contributing
============
Here is a little cheat-sheet for how to symlink your app's `node_modules/dexie` to a place where you can edit the source, version control your changes and create pull requests back to Dexie. Assuming you've already ran `npm install dexie --save` for the app your are developing.

1. Fork Dexie.js from the web gui on github
2. Clone your fork locally by launching a shell/command window and cd to a neutral place (like `~repos/`, `c:\repos` or whatever) and type:

    ```
    git clone https://github.com/YOUR-USERNAME/Dexie.js.git
    cd Dexie.js
    npm install
    npm run build
    npm link
    ```
3. cd to your app directory and write:
    ```
    npm link dexie
    ```

Your app's `node_modules/dexie/` is now sym-linked to the Dexie.js clone on your hard drive so any change you do there will propagate to your app. Build dexie.js using `npm run build` or `npm run watch`. The latter will react on any source file change and rebuild the dist files.

That's it. Now you're up and running to test and commit changes to Dexie.js that will instantly affect the app you are developing.

Pull requests are more than welcome. Some advices are:

* Run npm test before making a pull request.
* If you find an issue, a unit test that reproduces it is lovely ;). If you don't know where to put it, put it in `test/tests-misc.js`. We use qunit. Just look at existing tests in `tests-misc.js` to see how they should be written. Tests are transpiled in the build script so you can use ES6 if you like.

Build
-----
```
npm run build
```

Test
----
```
npm test
```

Watch
-----
```
npm run watch
```


[npm-image]: https://img.shields.io/npm/v/dexie.svg?style=flat
[npm-url]: https://npmjs.org/package/dexie
