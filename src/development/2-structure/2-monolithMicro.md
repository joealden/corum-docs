# Monolith vs Micro

## At the Start of Development

At the start of the project, I was pretty certain that I would develop both the
client and the API as one project. This kind of structure is very common when
developing with languages such as PHP. This kind of structure is known as a
'monolith system'.

This structure has the benefit of being conceptually simpler to think about and
easier to actually implement. However, monolith projects do have their
drawbacks. It is harder to test conceptually different components of a system as
they are typically tightly coupled, and it is harder to make large refactors as
the changes might break other parts of the system.

Developing in a 'micro service' mindset lends to code being more testable and
reusable, as components of a system that are conceptually separate are developed
in isolation from each other, meaning that tight coupling of components is
avoided at a management level. It also adheres more to the idea of 'separation
of concerns', as conceptually in the context of Corum, the API doesn't need any
knowledge of the client, it only needs to provide a generic interface to the
data the client needs.

Another benefit of developing micro services are that the services can be
deployed separately, even on different machines. This is a benefit because with
a monolith system, if the machine running it fails for whatever reason, and no
redundancy is in place, the whole system goes down. The way to provide
redundancy to a monolith system is to deploy multiple instances of the whole
system, or to boot an instance when another goes down. This could be less than
ideal because it may be using more system resources than needed, and it could
take longer for the whole system to start up, instead of a single micro service.

Deploying micro services means that different parts of the system can be
provided redundancy independently of each other. This could mean that less
system resources are wasted, and start up times are likely to be faster, meaning
that the system is down for a shorter period of time.

For these reasons above, I decided, during the development of the project, that
I would split the client and the API into separate services.

## Implementing the Micro Service Architecture

As mentioned above, implementing a monolith system is easier to start, but it
tends to be harder to maintain. I found that during the implementation of Corum
in a micro service architecture that testing and documenting it was much easier.
I believe this was because I only had to think about part of the system at a
single moment in time, reducing the cognitive load that was placed on me.

### Configuration is Made Easier

I also found that when I begun separating the two logical parts of Corum,
configuring the tools and libraries that I was using was much simpler. This was
because I didn't have to think as much about if a configuration setting would
cause a conflict. Configuration files were also a lot shorter, so therefore more
understandable at a glance. In the following sub sections I will give concrete
examples of where switching to a micro service architecture made configuring my
tools easier.

#### ESLint (My Linter)

For example, the configuration for ESLint (My linter of choice) that I am using
is different for the client and the API. This is because the two run in
different environments. The client code runs in the browser and the API code
runs on the server. (In the node.js runtime) While browsers and node.js both use
JavaScript, they support a different set of features as well as exposing
different global variables. For example, if I tried to use node's CommonJS
'`require`' syntax (used to import files into the current file scope) in the
browser, an error would be thrown because there is no global called '`require`'.
Likewise, if I tried to reference the '`document`' browser global in a node
environment, an error would also be thrown.

This relates to linting because if I was create Corum as a monolith, the ESLint
config would have to be quite permissive, where syntax and globals valid in both
environments would be allowed. This would have reduced the effectiveness of the
linter because if I did make a mistake like the two mentioned above, it wouldn't
be picked up by the linter. This would mean that I would only find this bug at
runtime, meaning that I would have wasted time that wouldn't have been wasted if
I had a stricter linter configuration.

The problem could be mitigated slightly by having separate directories for both
the client and server, then creating a config file per directory. This would
allow for stricter linting, however it would mean that my editor would not be
able to recognise the configuration file. This means that I wouldn't get the
errors showing in my editor, and I would have to instead rely on running the
linter from the command line. Also, if I wanted to work on both projects at the
same time, I would have to start two instances of the linter for each directory.

This problem wasn't present when I switch over to a micro service architecture.
This was because I could have two separate config files at the root of each
service. This means that my editor correctly picks up both the config files and
I could still have strict configs for the different services.

### `.gitignore` File

I am using version control to manage my project, in particular, Git. The reason
for this is explained in a section later on named 'Version Control'. When using
git you can create a file called '`.gitignore`' at the root of the repository.
This file tells git what files not to parse for changes. This means that the
files listed in this file are not committed to the repo. It also allows you to
list directories so that entire directories can be ignore, as well as the use of
a wildcard character (`*`) for pattern matching file names. For example, if you
didn't want to commit an files with the `.txt` extension, you could list `*.txt`
as an entry in the file.

When developing Corum as a monolith, the issue was that I wanted to ignore
certain types of files in the client but not in the API and vice versa. This
problem was solved when switching to a micro service architecture because the
client and the API are completely separate git repos, so they can have separate
`.gitignore` files.

### Editor Configuration Files

The editor that I am using is Visual Studio Code. (vscode) Vscode allows you to
configure workspace settings for different projects, this includes things such
as recommended editor extensions and their settings. These settings can be found
at the root of the project under a directory called '`.vscode`'. The API and
client use different libraries, so they would benefit from different editor
extensions. However it wouldn't make much sense to recommend an extension that
is not useful for what it being used. For example, it wouldn't make sense to
recommend any Vue related extensions for the API code, as it doesn't use Vue. By
using a micro service architecture I can have separate configurations for each
service.

### `package.json` File

The `package.json` file is standard across JavaScript and used to provide
information about the project. For example, you can list the project's name,
version, author and it's dependencies. This information is used by tools that
will be explained soon.

To automate some of the tasks that I do often, I use something called a task
runner. The task runner I am using for corum is called NPM scripts, and it comes
with my package manager by default. I could have used a different task runner
such as gulp but it would have introduced more complexity to the project for not
much gain. All NPM scripts does is run the commands that you specify with a few
nice features. The way you configure NPM scripts is by adding a `"scripts"`
field to `package.json`.

Here is a simple example:

```json
{
  "scripts": {
    "start": "node .",
    "test": "jest",
    "deploy": "yarn test && yarn start"
  }
}
```

In this contrived example, the usefulness of NPM scripts is not very evident at
first glance. Here is what each of these scripts do and how they show the
usefulness of NPM scripts when building coding in JavaScript:

#### `"start"`

Runs the file named `index.js` in the root of the project. In practice, this
could be used to start something like a web server. Also in practice this
command could be a lot more complicated, for example command line flags could be
passed to alter how the program runs. This shows one of the benefits of using
scripts. The user only has to type a short command such as `yarn start` to run
an arbitrarily long command. This reduces both time taken to execute a task as
well as making it so the developer doesn't have to remember a long command every
time they want to run it.

#### `"test"`

Runs tests using a testing framework called 'jest'. The testing library would be
installed as a local dependency to the project, meaning that if the user just
typed `jest` into their command line, it would return an error because the
command isn't available in the global context. (Unless they explicitly installed
it globally on their system which wouldn't make the project very portable) What
NPM scripts is doing is adding the path '`./node_modules/.bin`' to its PATH
environment variable. (This is how it work on Unix like system at least) If NPM
scripts didn't add this path, then user would have to write
`./node_modules/.bin/jest` instead of just `jest`. This NPM scripts feature
makes it a lot easier to write concise, portable scripts.

#### `"deploy"`

Runs both the `"test"` and `"start"` scripts one after each other. In this case
I am using `yarn` to execute the scripts, but NPM would also work.
(`"deploy": "npm run test && npm run start"`) What this script shows is that you
can compose scripts together. This is useful because it means that individual
scripts can be reused and it can abstract the implementation of the scripts
away. For example, it is quite evident that the `deploy` script tests and starts
the project just from the names of the scripts it is running.

From the sections above, I hope you can understand why NPM scripts are very
useful in any type of JavaScript project. The problem is that when there are a
lot of scripts, it is a lot harder to reason about them when reading
`package.json`. This issue showed itself when developing Corum as a monolith
system as I had scripts for both the client and the API, and sometimes I wanted
to call the scripts by the same name. For example, I wanted a `"start"` script
for both the client and the API. The client one would start the production ready
server and the API one would start a production ready docker cluster. Because
you can't have multiple scripts with the same name for obvious reasons, I had to
start prefixing scripts with what part of the system it started. For example, I
had two scripts called `"client:start"` and `"api:start"`. This was fine for a
few scripts but it got out of hand as more scripts were added.

**START HERE**

* NPM scripts, the task runner of my choice. (located under the `scripts`
  section of `package.json`) When I ran a task such as `test`, I want different
  tests to be ran between projects.
* node_modules bundling issue
  * Javascript handles dependencies in the following way:
    * The file `package.json` contains information about the project
    * An important piece of information is the project dependencies
    * When installing the dependencies, (with the `yarn` command) they are
      placed in the `node_modules` folder
  * The API framework I am using, (graphcool) currently bundles the entire
    contents of the `node_modules` folder with the deployed code. This means
    that when the project was a monolith, all of the dependencies for the client
    were also being bundled with the API code. This was an issue because of the
    following:
    * The API would take up more storage space on the deployed server
    * It would take significantly more time to bundle the API before it was
      deployed. This meant that I was wasting time.
