# direnv-dotenv

The [Twelve-Factor App](https://12factor.net/config) recommends storing your apps configuration in
the environment (normally done so via environment variables).

In production these will be set/injected from your production environment (e.g.
[GitLab CI/CD environment variables](https://docs.gitlab.com/ee/ci/variables/)).

However, when developing locally it is exhausting to set these variables again and again.

## dotenv

For this reason there are multiple projects that support a so called `.env`-File (dotenv-File).
For example:
* ruby: https://github.com/bkeepers/dotenv
* node: https://github.com/motdotla/dotenv
* go: https://github.com/joho/godotenv
* ...

The way this works is that you first create a `.env`-File:
```dotenv
DB_HOST=YOURDBHOST
S3_BUCKET=YOURS3BUCKET
SECRET_KEY=YOURSECRETKEY
```

And then load this file (via a provided library-method) during the bootstrapping of your app.
For example in `node`:
```js
require('dotenv').config()
```

You can then use the variables defined in the .dotenv-File as if they had been defined as environment
variables, for example in `node`:
```js
console.log(process.env.DB_HOST)
```

## direnv

There is another library with a similar purpose but another methodology:
[direnv](https://github.com/direnv/direnv).
`direnv` uses `.envrc` files with the following format (note the `export`):
```shell script
export DB_HOST=YOURDBHOST
export S3_BUCKET=YOURS3BUCKET
export SECRET_KEY=YOURSECRETKEY
```

The way `direnv` works, is that it hooks into your shell and watches for directory-changes.
When you enter a directory, it loads the variables from the `.envrc`-files into your actual shell.
When you leave the directory it unloads them again. This also works for subdirectories, i.e.
the file `~/workspace/project/.envrc` will be loaded when executing
`cd ~/workspace/project/tooling`.

Since the environment variables are exported to the shell, you can then just start your app
via your shell, and the app can directly use the environment variables. You do not have to make
any library-call in the bootstrapping of your app.

This also means that `direnv` is totally
language/framework agnostic and can be used for a broader area of use cases. For example, it is
very helpful when working with Infrastructure-As-Code. Or it can be used when developing/testing
GitLab-pipelines locally that use environment variables defined as
[GitLab CI/CD environment variables](https://docs.gitlab.com/ee/ci/variables/)!

## using direnv and dotenv at the same time

While working with `direnv` (`.envrc`) is very comfortable there are some tools that only accept
the `.env`-Format. Instead of duplicating all variables, we can use the following workaround
(https://github.com/direnv/direnv/issues/284):

Define your variables in a `.env`-File and use this `.envrc`-File:
```shell script
dotenv
```

`dotenv` is in this case a command from the `direnv` standard library
(https://direnv.net/man/direnv-stdlib.1.html) and does exactly what you suspect: it loads
a `.env`-File. We still need two files instead of one, but it works :)


## Integration tips

### direnv with Oh my zsh
* install direnv according to the docs: https://github.com/direnv/direnv/blob/master/docs/installation.md
* just use the plugin `direnv` for the hook: https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/direnv

### IntelliJ
* https://plugins.jetbrains.com/plugin/9525--env-files-support
* https://plugins.jetbrains.com/plugin/7861-envfile
