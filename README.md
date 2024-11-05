# Shortcut API Client using Goal

This repository contains [Goal](https://codeberg.org/anaseto/goal) source code for a [Shortcut REST API v3](https://developer.shortcut.com/api/rest/v3) client, using extensions to Goal provided by [Ari](https://github.com/semperos/ari).

## Setup

First, install the `ari` command:

```shell
go install github.com/semperos/ari/cmd/ari@latest
```

Make a directory to keep your Goal source code and set it as your `GOALLIB`, for example:

```
mkdir -p $HOME/Development/goal
export GOALLIB=$HOME/Development/goal
```

Now check out this repository in your `GOALLIB` folder:

```
cd $GOALLIB
git clone https://github.com/semperos/sc-client-goal sc-client
```

Finally, ensure your Shortcut API token is set to the `SHORTCUT_API_TOKEN` environment variable. I personally use [direnv](https://github.com/direnv/direnv) + [sops](https://github.com/direnv/direnv) to accomplish this with a local `.envrc` file that looks like:

```
export SHORCUT_API_TOKEN=$(sops -d --extract '["shortcut"]["token"]' $HOME/.config/credentials.yaml)
```

To start interacting with the Shortcut API via Goal, run the following command which will fetch entities from your Shortcut workspace (e.g., workflows, teams & members) and provide a `sc>` prompt when ready:

```shell
ari -l sc.goal
```

For one-shot scripts, use the following (requires you to fetch any entities that you need, see [scdata.goal](./scdata.goal))

```shell
ari -l scapi.goal your-script.goal
```

## License

Copyright 2024 Daniel Gregoire

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the “Software”), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED “AS IS”, WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

