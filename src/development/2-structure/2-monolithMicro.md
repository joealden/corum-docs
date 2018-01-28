# Monolith vs Micro

* Talk about how at the start, the API and the client were going to be built as
  a monolith project
* When I got further into development, I decided to separate the API and the
  client because:
  * Separation of concerns
  * As the API and client wouldn't be deployed together, it doesn't make sense
    to package them together
  * It meant that documenting and testing the projects was a simpler process
  * node_modules bundling issue
    * Javascript handles dependencies in the following way:
      * The file `package.json` contains information about the project
      * An important piece of information is the project dependencies
      * When installing the dependencies, (with the `yarn` command) they are
        placed in the `node_modules` folder
    * The API framework I am using, (graphcool) currently bundles the entire
      contents of the `node_modules` folder with the deployed code. This means
      that when the project was a monolith, all of the dependencies for the
      client were also being bundled with the API code. This was an issue
      because of the following:
      * The API would take up more storage space on the deployed server
      * It would take significantly more time to bundle the API before it was
        deployed. This meant that I was wasting time.
  * As the API code and the client code use different libraries and frameworks,
    it made sense for them to have different configuration and settings for the
    following:
    * ESLint, The code linter of my choice (The config file is called
      `.eslintrc.js`) The client code has to be browser compatible, whereas the
      API code had to be node.js compatible.
    * git ignores Git ignores tell git (the version control system of my choice)
      what it shouldn't allow to be committed to the repository. (config file is
      `.gitignore`) I wanted to ignore different files between the projects.
    * NPM scripts, the task runner of my choice. (located under the `scripts`
      section of `package.json`) When I ran a task such as `test`, I want
      different tests to be ran between projects.
    * Code editor settings for Visual Studio Code (vscode), my editor of choice.
      (Config files can be found inside of the `.vscode` directories in both
      projects) Vscode allows you to configure workspace settings for different
      projects, this includes things such as recommend editor extensions and
      their settings. For example, it wouldn't make sense to recommend any Vue
      related extensions for the API, as it doesn't use Vue.
  * This was made difficult when I had a monolith project
