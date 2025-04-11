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

Alternatively, you can `""import"sc-client/sc"` from your Goal program (run via `ari`).

## Gotchas

- Goal deserializes JSON `null` as `0n` which is its NaN value.
- Goal deserializes JSON `true` as `0w` which is its positive infinity value.
- Goal deserializes JSON `false` as `-0w` which is its negative infinity value.

## Examples

**Iteration workstreams**, defined as number of epics + number of stories not in an epic for the last 10 iterations:

```
import"sc-client/sc"              / Located based on GOALLIB env var
epid:..x["epic_id"]               / Helper to grab epic id
repnan:{y[&nan y]:x}              / Helper to replace a NaN value with x
nanepic:repnan[-1]                / Helper to replace a NaN epic ID with -1
ittbl:mktbl its:iteration.list""  / Table of all iterations in workspace
groupits:{[group]                 / Table filtered to iterations for given group (Team)
 (1;..1~'*'x["group_ids"]=p.group"id";..status="done")#ittbl}
itnumws:{[its]                    / Num epics + stories wout epic in last 10 iterations
 its:its[(..>x["end_date"])its]
 itsdone:its[!10]"id"
 itss:iteration.stories'itsdone
 epicfreqs:k.freq'nanepic'epid''itss
 numepics:-1+#'epicfreqs
 numnonepic:@[;-1]'epicfreqs
 numepics + numnonepic
}
ac"gr.*"                          / See which teams are defined
itnumws@groupits gr.yourteamhere  / Report
```

**Produce a CSV of labels** that are no longer in use for in-progress work:

```
import"sc-client/sc"
say"PLEASE WAIT. Fetching all labels with their stories and/or epics."
lbs:label""; lbsstories:label.stories'lbs; lbsepics:label.epics'lbs
nosts:0=#'lbsstories; noeps:0=#'lbsepics
ALLNONE:lbs@&nosts&noeps

SNONE:lbs[&nosts] / labels with no stories
ENONE:lbs[&noeps] / labels with no epics

/ TODO can use "completed" field instead
SDONE:lbs@&1~'{&/x}'story.isdone''lbsstories / labels with stories, all stories done
EDONE:lbs@&1~'{&/x}'epic.isdone''lbsepics    / labels with epics,   all epics done

ids:@[;"id"]'; seti:{x[&~(#y)=(ids y)?(ids x)]} / set intersection
ALLDONE:seti[SDONE;EDONE]
ALLSDONE:seti[SDONE;ENONE]
ALLEDONE:seti[EDONE;SNONE]

ARCHIVE:,/(ALLNONE;ALLDONE;ALLSDONE;ALLEDONE)
cols:{A:x[("id";"name";"app_url")];@[A;0;"i"$]}'ARCHIVE
hd:!"id name app_url"
t:hd!+cols; t:@[t;"id";"i"$]; t:@[t;"name";rx/\t/ sub " "] / int id; name w/out csv sep
f:"labels.csv"; f print"\t"csv t; say "Wrote archive-able labels to $f"
```

Cancel stories owned by former members of your Shortcut workspace that are in progress (never closed out). This assumes that there's either a workflow state with the word "cancel" in it, or that the very last workflow state in your workflows is the best fit to convey that something was cancelled. It also relies on the `cache.` bindings that are made in `scdata.goal`, so make sure you load `sc.goal` (which loads `scdata.goal`) when running this:

```
cancelWorkflowStates:cache.workflowstates[&{rx/(?i)cancel/@x}'{x["name"]}'cache.workflowstates]
workflowToCancelState:(@[;"_workflow_id"]'cancelWorkflowStates)!(cancelWorkflowStates)
lastWorkflowState:{[workflowId] *|cache.workflows[workflowId;"states"]}
cancelWorkflowState:{[workflowId]
 state:workflowToCancelState[workflowId]
 ?[0<#state;state;lastWorkflowState[workflowId]]}
formerMembers:cache.members@&0w={x["disabled"]}'cache.members
ss:story.search@..["owner_ids":@[;"id"]'formerMembers;"archived":-0w;"workflow_state_types":!"started"]
count:#ss; say"Cancelling $count stories..."
cancelStory:{[s]
 workflowId:s"workflow_id";newWorkflowState:cancelWorkflowState[workflowId]
 story.update[s"id";..["workflow_state_id":newWorkflowState"id"]]}
cancelStory'ss
```

## License

Copyright 2024 Daniel Gregoire

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the “Software”), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED “AS IS”, WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
