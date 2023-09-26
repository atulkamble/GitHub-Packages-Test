# GitHub-Packages-Test
Publish to GitHub Packages with GitHub Actions.
Clone the repository to your local machine.

git clone https://github.com/YOUR-USERNAME/YOUR-REPOSITORY.git
cd YOUR-REPOSITORY
Create an index.js file and add a basic alert to say "Hello world!"

JavaScript
console.log("Hello, World!");
Initialize an npm package with npm init. In the package initialization wizard, enter your package with the name: @YOUR-USERNAME/YOUR-REPOSITORY, and set the test script to exit 0. This will generate a package.json file with information about your package.

$ npm init
  ...
  package name: @YOUR-USERNAME/YOUR-REPOSITORY
  ...
  test command: exit 0
  ...    
Run npm install to generate the package-lock.json file, then commit and push your changes to GitHub.

npm install
git add index.js package.json package-lock.json
git commit -m "initialize npm package"
git push
Create a .github/workflows directory. In that directory, create a file named release-package.yml.

Copy the following YAML content into the release-package.yml file.

YAML
name: Node.js Package

on:
  release:
    types: [created]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v3
        with:
          node-version: 16
      - run: npm ci
      - run: npm test

  publish-gpr:
    needs: build
    runs-on: ubuntu-latest
    permissions:
      packages: write
      contents: read
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v3
        with:
          node-version: 16
          registry-url: https://npm.pkg.github.com/
      - run: npm ci
      - run: npm publish
        env:
          NODE_AUTH_TOKEN: ${{secrets.GITHUB_TOKEN}}
Tell npm which scope and registry to publish packages to using one of the following methods:

Add an npm configuration file for the repository by creating a .npmrc file in the root directory with the contents:

@YOUR-USERNAME:registry=https://npm.pkg.github.com
Edit the package.json file and specify the publishConfig key:

"publishConfig": {
  "@YOUR-USERNAME:registry": "https://npm.pkg.github.com"
}
Commit and push your changes to GitHub.

$ git add .github/workflows/release-package.yml
# Also add the file you created or edited in the previous step.
$ git add .npmrc or package.json
$ git commit -m "workflow to publish package"
$ git push
The workflow that you created will run whenever a new release is created in your repository. If the tests pass, then the package will be published to GitHub Packages.

To test this out, navigate to the Code tab in your repository and create a new release. For more information, see "Managing releases in a repository."

Viewing your published package
You can view all of the packages you have published.

On GitHub.com, navigate to the main page of the repository.

In the right sidebar of your repository, click Packages.

Screenshot of the sidebar of a repository page. The "Packages" section is outlined in orange.

Search for and then click the name of the package that you want to view.
