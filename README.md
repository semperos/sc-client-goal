# Shortcut API Client using Ari

This repository contains [Ari](https://github.com/semperos/ari) source code for a [Shortcut REST API v3](https://developer.shortcut.com/api/rest/v3) client.

## Usage

First, install the `ari` command:

```shell
go install github.com/semperos/ari/cmd/ari@latest
```

To start a REPL with functions for calling the Shortcut API:

```shell
ari -l scapi.ari
```

You can also load `scdata.ari` to have your Shortcut workspace's members, groups/teams, workflows, etc. downloaded up front (increases start-up time dramatically):

```shell
ari -l scapi.ari -l scdata.ari
```

For one-shot scripts that shouldn't start the REPL, pass your script as a positional argument to the `ari` command:

```shell
ari -l scapi.ari your-script.ari
```

## License

Copyright 2024 Daniel Gregoire

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the “Software”), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED “AS IS”, WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

