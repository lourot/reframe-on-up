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

## Making your app Up-compatible

What you need to know:

* `reframe start` is pretty much like `reframe build && reframe server` and you'll want:
  * to run `reframe build` locally, then
  * to send the build output to AWS, and then
  * to have AWS run `reframe server`.
* Up creates an AWS Lambda function that:
  * runs `npm install`, then
  * runs `PORT=<some number> npm start`, then
  * waits for your app to behave like an HTTP server listening at `PORT`, and then
  * exposes it to the world over HTTPS.

### Making your app listen to `PORT`

4. Make your Reframe server code tweakable:

```
$ npx reframe eject server --skip-git
...
 ✔ Eject done.
```

> **NOTES**:
>
> * In the Reframe terminology "ejecting" a Reframe component means dumping the default code of that
>   component to a file so you can tweak it. Eventually if you eject all Reframe components, you
>   have fully gotten rid of Reframe.
> * Without `--skip-git`, `reframe eject` will fail if your project isn't a Git repository. I anyway
>   don't like the idea of Reframe fiddling with Git.

5. Replace `3000` by `process.env.PORT` in [server/start.js](server/start.js). So it should look
   like:

```js
// ...
    const server = Hapi.Server({
        port: process.env.PORT,
        debug: {request: ['internal']},
    });
// ...
```

6. Create useful npm scripts in your [package.json](package.json):

```json
{
  "scripts": {
    "start": "reframe server",
    "local": "PORT=3000 reframe start"
  },
  "dependencies": {
    "...": "..."
  }
}
```

7. Run your app locally:

```bash
$ npm run local
...
 ✔ Server running (for development)
     http://localhost:3000/ -> WelcomePage
     http://localhost:3000/counter -> CounterPage
     http://localhost:3000/game-of-thrones -> GameOfThronesPage
     http://localhost:3000/time -> TimePage
```

![localhost](readme_assets/localhost.png)

## Setting up your AWS account

8. Create an IAM user `myuser` and an access key for it. We will need it in order to use the AWS
   CLI.

See the
[AWS documentation](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html)
for more details.

9. Attach the `IAMFullAccess` policy to `myuser` so you can run commands like
   `aws iam create-group ...` later.

10. Install and configure the AWS CLI:

```bash
$ sudo pip install awscli
$ aws configure
AWS Access Key ID [None]: ********
AWS Secret Access Key [None]: ********
Default region name [None]: us-east-1
Default output format [None]:
```
