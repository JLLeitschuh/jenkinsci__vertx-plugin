jenkins-vertx-plugin
====================

You got Vert.x in my Jenkins!

The plugin starts a clustered instance and deploys no real verticles on its own.
To build applications based on these events, start another clustered instance
and deploy your verticles there.

Right now, based on my development environment, I need to tweak `cluster.xml` to
set the interface to use.  The plugin uses `0.0.0.0:25000` for the cluster host
and port.  So you just need to make sure the cluster config works for your
environment and that you don't have conflicting ports.

published messages
------------------

### address: `jenkins-vertx`

#### when jenkins starts

    {"action":"started"}

#### when jenkins stops

    {"action":"stopped"}

### address: `jenkins.run`

Notifications of Runs.  A Run is serialized like:

    {
        "id":"2012-09-06_16-41-53",
        "num":12,
        "fullDisplayName":"parameterized #12",
        "externalizableId":"parameterized#12",
        "scheduledTimestamp":1346971313525,
        "parent":{
            "name":"parameterized",
            "fullName":"parameterized",
            "url":"job/parameterized/"
            "hudson.model.FreeStyleProject":{ … },
        },
        "build":{
            "hudson.model.FreeStyleBuild":{
                "actions":[
                    {
                        "parameters":[
                            {"name":"key","value":"vert.x value"},
                            {"name":"foo","value":"bar"}
                        ]
                    },
                    {
                        "causes":[
                            {"type":"vert.x"}
                        ]
                    }
                ],
                "number":12,
                "result":{
                    "name":"SUCCESS",
                    "ordinal":0,
                    "color":"BLUE"
                },
                "duration":61,
                "charset":"UTF-8",
                "keepLog":false,
                "builtOn":"",
                "workspace":"…",
                "hudsonVersion":"1.466",
                "scm":{"@class":"hudson.scm.NullChangeLogParser"},
                "culprits":[]
            }
        },
        "artifacts":[],
        "url":"job/parameterized/12/",
        "previousBuild":{
            "hudson.model.FreeStyleBuild":{ … }
        }
    }


#### when a Run is started

    {
        "action":"started",
        "run": …
    }

#### when a Run is completed

    {
        "action":"started",
        "run": …
    }

#### when a Run is finalized

"finalized" means written to disk

    {
        "action":"finalized",
        "run": …
    }

#### when a Run is deleted

    {
        "action":"deleted",
        "run": …
    }


### address: `jenkins.item`

An Item is serialized like

    {
        "name":"parameterized",
        "fullName":"parameterized",
        "url":"job/parameterized/"
        "hudson.model.FreeStyleProject":{ … },
    }

#### when all items have been loaded

Basically when Jenkins is ready to roll.

    {"action":"allloaded"}


#### when a new item is created

    {
        "action":"created",
        "item": { … }
    }

####  when an item is updated

    {
        "action":"updated",
        "item": { … }
    }

####  when an item is renamed

    {
        "action":"renamed",
        "item": { … },
        "oldName": "…",
        "newName": "…"
    }

####  when an item is deleted

    {
        "action":"deleted",
        "item": { … }
    }


registered handlers
-------------------

### address: `jenkins`

#### schedule a build

    {
       "action":"scheduleBuild",
       "data":{
           "projectName":"someProject"
       }
    }

#### schedule a *parameterized* build

    {
       "action":"scheduleBuild",
       "data":{
           "projectName":"someParameterizedProject",
           "params": {
               "param1":"value1",
               "param2":"value2"
           }
       }
    }
 
All parameters are treated as strings.
