> Work in progress

<p align="center">
  <a href="https://github.com/AurelienLourot/reframe-on-up">
    <img src="https://rawgit.com/AurelienLourot/reframe-on-up/master/readme_assets/reframe-on-up.jpg"
         width="200" height="96">
  </a>
</p>
<p align="center">
  <b>Reframe on Up</b>
</p>
<p align="center">
  How I deployed for free a Reframe web app on Up in less than an hour
</p>
<p align="right">
  <a href="https://github.com/AurelienLourot">@AurelienLourot</a> - 17 Jun 2018
</p>

[<img src="https://rawgit.com/AurelienLourot/reframe-on-up/master/readme_assets/reframe.jpg" align="left" width="64" height="64">](https://github.com/reframejs/reframe)

# What is Reframe?

[Reframe](https://github.com/reframejs/reframe) is a powerful open-source framework for creating
[Node.js](https://nodejs.org/en/) web apps. It gets you quickly started and doesn't get in the way
when your app becomes more complex. It supports [React](https://reactjs.org/) and
[Vue.js](https://vuejs.org/) for the front-end.

[<img src="https://rawgit.com/AurelienLourot/reframe-on-up/master/readme_assets/up.jpg" align="left" width="64" height="64">](https://github.com/apex/up)

# What is Up?

[Up](https://github.com/apex/up) is an open-source tool that deploys your web app to
[AWS Lambda](https://aws.amazon.com/lambda/) in your own AWS account. Hosting your app on AWS will
remain free as long as it doesn't consume much memory and you have less than a million requests per
month.

# Step-by-step guide

## Creating a Reframe web app

1. Install Reframe:

```bash
$ mkdir ~/myapp/
$ cd ~/myapp/
$ echo {} > package.json
$ npm install @reframe/cli@0.1.13-next.0 --save
```

> **NOTE**: At the time of writing the latest stable Reframe version (0.1.12) has a bug in which
> the build output contains invalid paths in some cases. Thus we need to install this floating
> version which contains
> [a fix](https://github.com/reframejs/reframe/commit/215a3b97316e588020351cb851ab4afafc765e0d).

2. Create a Hello World:

```bash
$ npx reframe init .
```

Unfortunately `reframe init` overwrites our [package.json](package.json), thus removing our
dependency to `@reframe/cli`. This is because Reframe is designed to be installed globally on your
system, but I personally prefer to install it locally in the project as a dependency, which is also
needed in order to deploy it to Up.

Fix this by editing your [package.json](package.json) and making sure it looks like:

```json
{
  "dependencies": {
    "@reframe/cli": "^0.1.13-next.0",
    "@brillout/fetch": "^0.1.0",
    "@reframe/react-kit": "0.1.13-next.0",
    "react": "^16.3.2"
  }
}
```

3. Run your app locally:

```bash
$ npm install
$ npx reframe start
...
 ✔ Server running (for development)
     http://localhost:3000/ -> WelcomePage
     http://localhost:3000/counter -> CounterPage
     http://localhost:3000/game-of-thrones -> GameOfThronesPage
     http://localhost:3000/time -> TimePage
```

![localhost](readme_assets/localhost.png)
