# Summary of Introduction to Node.js
Summary of Introduction to Node.js by Scott Moss. The goal of this class to build a example or two of some of the best uses cases of Node js. Think of it as building mini apps for each topic.

# What is Node js
Node.js is a runtime for built on top of Chrome's V8. It allows you to develop apps in JavaScript outside of the browser. It's single threaded non blocking and asynchronous. This is acheived by the use of an event loop at the core of Node.js.

Because Node.js can run outside of the browser, it can be used for pretty much anything!
- API's and servers
- Databases (yes some DB's are built in Node)
- CLI's
- Build Tools
- Automations
- Basic Scripting
- GPU shopping bots

# Installing Node.js
- For Windows : recommended to use the official instlaller from the [Node.js site](https://nodejs.org/en/).
- For everything else : recommended to install with [NVM](https://github.com/nvm-sh/nvm).NVM allows you to install many versions of Node.js at once and switch when you need. Also, NVM installs Node.js in a folder that will not have permission errors that you would otherwise run into with the official installer.

Once you have nvm installed, you need to install a Node version. You can download the latest LTS version with this command.
`nvm install --lts`

# Executing Node
### Node REPL
Before we get into creating programs and apps with Node.js, let's get a feet wet with the Node.js REPL. Just type `node` in your terminal. This will create a Node.js environment where we can write and execute JavaScript. You'll see that all that JS knowledge you have learned carries over to Node.js!. Although, some things, specifically browser based API's probably won't work. Go ahead, try an `alert('hello world')` and see what happens. You'll get an error. Although the Node.js runtime uses JS as it's languge, none of the Browser based globals that you are familiar with actually exist in Node.js or they are pollyfilled to prevent runtime errors.

### File Execution
The Node REPL it nice, but you can't build an application with that. We need to be able write our code to a file and tell Node.js to execute that.
> Create a file called:`index.js`

In that file, write some JS. Try out `console.log('hello there')`. To execute this .js file in the Node.js runtime, we can use the `node` command with the file name as an argument.
```
> node index.js
```
# Globals
Like the Browser, Node.js comes with some practical globals for us to use in our applications.
### Common
- `global` Think of this as like `window` but for Node.js.
- `__dirname` This global is a `String` value that points the the directory name of the file it's used in.
- `__filename` Like `__dirname`, it too is relative to the file it's written in. A `String` value that points the the file name.
- `process` A swiss army knife global. An `Object` that contains all the context you need about the current program being executed. Things from env vars, to what machine you're on.
- `exports` `module` `require` These globals are used for creating and exposing modules throughout your app. We'll get to modules in a second
### The rest
Depending on what version on Node.js you're running, there are so many more globals. Not as many as the Browser, but enough that you'll probably never use many of them. Check them out [here](https://nodejs.org/api/globals.html).

# Modules
There is no GUI in Node.js, no HTML or CSS. This also means there aren't any scipt tags to include JS files into our application. Node.js uses modules to to share your JavaScript with other JavaScript in your apps. No window or globals needed.

A module is a reusable chunk of code that has its own context. That way modules can't interfere with or polute the global scope. You can think of them like lego blocks that you can create, import, and share.

By default, Node.js uses common js modules. With newer versions of Node.js we can now take advantage of ES6 modules. To opt into using this syntax, you can use the `.mjs` extension instead of `.js`. We'll be using the ES6 module syntax going forward as they are the standard now with browsers adding support now.

### Module syntax
Now, let's create our first module. The only thing we have to do is expose some code in one for our JavaScript files. We can do that with the export keyword.
```
// utils.js
export const action = () => {

}

export const run = () => {

}
```
```
// app.js

import { action, run } from './utils'
```

Usually if you only have to expose one bit of code, you should use the default keyword. This allows you to import the module with whatever name you want.
```
// utils.js
default export function () {
  console.log('did action')
}
```
```
// app.js
import whateverIWant from './utils'
```
### Internal Modules
Node.js comes with some great internal modules. You can think of them as like the phenonminal global APIs in the browser. Here are some of the most useful ones:
- `fs` - useful for interacting with the file system.
- `path` - lib to assit with manipulating file paths and all their nuiances.
- `child_process` - spawn subprocesses in the background.
- `http` - interact with OS level networking. Useful for creating servers.

# File System
### Reading a file
Node.js ships with a powerful module, `fs` short for file system. There are many methods on the [fs module](https://nodejs.org/api/fs.html). To read a file, we'll use the `readFile` method.

Create a simple html file `template.html`.
```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
</head>
<body>
  <h1>{title}</h1>
  <p>{body}</p>
</body>
</html>
```
To read the file:
```
import { readFile } from 'fs/promises'

let template = await readFile(new URL('./template.html', import.meta.url), 'utf-8')
```
### Write a file
```
import { readFile, writeFile } from 'fs/promises'

let template = await readFile(new URL('./test.html', import.meta.url), 'utf-8')

const data = {
  title: 'My new file',
  body: 'I wrote this file to disk using node'
}

for (const [key, val] of Object.entries(data)) {
  template = template.replace(`{${key}}`, val)
}

await writeFile(new URL('./index.html', import.meta.url), template)
```
# Error Handling
### Process exiting
When a exception is thrown in Node.js, the current process will exit with a code of `1`. This effectively errors out and stops your programing completely. You can manually do this with:

> process.exit(1)

### Async Errors
When dealing with callbacks that are used for async operations, the standard pattern is:
```
fs.readFile(filePath, (error, result) => {
  if (error) {
    // do something
  } else {
    // yaaay
  }
})
```
- Callbacks accept the `(error, result)` argument signature where error could be `null` if there is no error.
- For `promises`, you can continue to use the `.catch()` pattern. Nothing new to see here. 
- For `async / await` you should use `try / catch`.

```
try {
  const result = await asyncAction()
} catch (e) {
  // handle error
}
```
### Sync Errors
```
try {
  const result = syncAction()
} catch (e) {
  // handle error
}
```
### Catch All
```
process.on('uncaughtException', cb)
```
# Packages
### NPM - Init
To consume a package, we must first turn our app into a package. We can do this with a simple file called `package.json` on the root of our app. Writing it by hand is cool, but using a CLI called `npm` is better. NPM was already installed when you installed Node.js. In a new folder, run: `npm init`

This will initialze a new package by walking you through a few prompts. Once you're finished, you'll have a `package.json` file that looks like this:
```
{
  "name": "app",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC"
}
```
Now are soon to be app is a package. We'll get into how to distribute and deploy different types of Node.js apps, but for now, this package is staying local. Let's take a look at some of these fields:
- `"name"` - is the name of your package. Can be anything since we're local
- `"version"` - is the [Semantic Version Number](https://semver.org/) or semver
- `"main"` - the main entry point into your package
- `"scripts"` - object of custom scripts to be excuted with `npm` cli

### NPM - Commands
NPM has [several commands](https://docs.npmjs.com/cli/v6/commands) at its disposal that you don't need to know really. There are some important ones that you will use repeatedly.
- `npm install` - installs given module(s) from remote registries or local sources
- `npm test` - runs the `test` script in your package.json
- `npm uninstall` - will uninstall a give package

# CLIs
A CLI, or command line interface is a program desgined to start and complete one off tasks. Like git or npm. Node.js is a perfect runtime to create a CLI that will run on any machine that has Node.js isntalled.
### Creating a CLI
Creating a CLI in Node.js just takes a extra step or two because they are really just an ordinary Node.js app wrapped behind a bin command. For this exercise, we'll create a CLI that opens a random reddit post in our browser. To start, we'll create a new folder and make it a package with npm init.

Once inside that folder, create a file reddit.mjs:
```
// reddit.mjs
#! /usr/bin/env node

console.log('hello from your CLI')
```
Next we need to tell Node.js what the name of our CLI is so when can actually use it in our terminal. Just have to add a section to our package.json:
```
"bin": {
  "reddit": "./reddit.mjs"
}
```
Once installed, this package will have it's bin command installed into your machine's bin folder allowing us to use the `reddit` command. Lastly, we must install our own package locally so we can test out the CLI. We could just execute the file with the node runtime, but we want to see the CLI actually work.

> npm install -g

We can simply instll with no args which tells npm to install the current director. The `-g` flag means we want to globally install this package vs in a local node_modules. You should now be able to run `reddit` and see your log print.

### Packages in our Pacakge
> npm install open node-fetch yargs --save

We'll install just these three packages.
- `open` - will open our browser with a URL
- `node-fetch` - is a fetch client that we can use to hit the reddit API
- `yargs` - will allow us to process any flags or arguments passed to the CLI

So to put it all together
```
#! /usr/bin/env node
// import our packages
import open from 'open'
import fetch from 'node-fetch'
import yargs from 'yargs'

// parse env vars
const { argv } = yargs(process.argv)
// init fetch to reddit api
const res = await fetch('https://www.reddit.com/.json')
const data = await res.json()
const randomIndex = Math.floor(Math.random() * data.data.children.length)
// get radom post from reddit api response of all posts on front page
const post = data.data.children[randomIndex]

// log if --print flag is passed
if (argv.print) {
  console.log(`
    Title: ${post.data.title}\n
    Link: ${post.data.permalink}
  `)
} else {
  // open in browser if not
  open(`https://reddit.com${post.data.permalink}`)
}
```
# Servers
Node.js has access to OS level functionality, like networking tools. This allows us to build very capable servers. Mixed with the fact that Node.js is single threaded and runs an even loop for async tasks, Node.js is widely used for API's that need to respond fast and don't require heavy CPU intensive work.

### The hard way
Node.js ships with the `http` module. This module is an abstraction around OS level networking tools. For Node.js, the http module would be considered "low level". Let's create a simple server.
```
import http from 'http'

const host = 'localhost'
const port = 8000

const server = http.createServer((req, res) => {
  if (req.method === 'POST') {
    let body = ''

    req.on('data', chunk => {
      body += chunk.toString()
    })

    req.on('end', () => {
      if (req.headers['content-type'] === 'application/json') {
        body = JSON.parse(body)
      }

      console.log(body)
      res.writeHead(201)
      res.end('ok')
    })
  } else {
    res.writeHead(200)
    res.end('hello from my server')
  }

})

server.listen(port, host, () => {
  console.log(`Server is running on http://${host}:${port}`)
})
```
### Express
There is an awesome packaged, `express`, that makes creating servers in Node.js a breeze. We're going to use it now.

> npm install express body-parser morgan

- `express` - a framework for building servers
- `body-parser` - a middleware that parses incoming requests
- `morgan` - a middleware for logging incoming requests

With everything installed, we'll create a simple API for a todo app using express.
```
import express from 'express'
import morgan from 'morgan'
import bp from 'body-parser'

const { urlencoded, json } = bp

const db = {
  todos: [],
}

const app = express()

app.use(urlencoded({ extended: true }))
app.use(json())
app.use(morgan('dev'))

app.get('/todo', (req, res) => {
  res.json({ data: db.todos })
})

app.post('/todo', (req, res) => {
  const newTodo = { complete: false, id: Date.now(), text: req.body.text }
  db.todos.push(newTodo)

  res.json({ data: newTodo })
})

app.listen(8000, () => {
  console.log('Server on http://localhost:8000')
})
```
Our todo API has two routes:
- `GET /todo` - get all todos
- `POST /todo` - create a new todo
