# Creating a first actor

We will go through the steps necessary to create a new actor in a complete clean environment.
The purpose of the actor we will write in this tutorial, is to retrieve the hostname of the
system and send it as a message into the system so other actors can consume it.

For the sake of getting started from zero, we will assume that all things have to be created.

## Terminology

First let's start defining the terminology

### Models
To send messages between actors a model has to be created that describes the format of the
message and acts at the same time as an object to access the data. If you know ORM libraries,
this works pretty similar

### Channels
Channels are used to classify the purpose of a message and every Model has to have an assigned
channel.

### Tags
Tags are used by the framework to be able to query the repository for actors that should be
executed together in a [phase of a workflow](workflows.md). This starts being interesting
when you want to have your actor being included into a workflow in a phase. For keeping
the tutorial a bit more simple about how to write and test the actor we skip this topic.

### Actors
Actors define what messages they want to consume and what they produce by importing the
classes and assigning them to a tuple in the actor class definition.
Tags are defined there as well for the reasons as outlined above.


## Getting started

First go to your project directory. If you did not yet create a project please check the
steps [in the create project tutorial](create-project.md)

We're considering that this is an empty project.

### Creating a tag

As outlined above, we will have to create a tag. Since we are scanning the system, let's
call this tag 'Scan'

```shell
    $ snactor new-tag Scan
```

This will create a subdirectory called tags with a file scan.py and in that file all
necessary code is already defined and it creates the class *ScanTag* which we will use
later on.

#### Screencast
![Create Tag Tutorial Cast](create-tag.gif)

### Creating a channel

Next we will have to create a channel, which we will call *SystemInfo* channel

```shell
    $ snactor new-channel SystemInfo
```

This time the folder channels has been created with a systeminfo.py file that provides
the complete code and definition for the *SystemInfoChannel* class we will use in the model.

#### Screencast
![Create Channel Tutorial Cast](create-channel.gif)

### Creating a model

Now we have to create the model we want to use for sending the message. We will call the
model *Hostname* and have it assigned to the *SystemInfoChannel*

```shell
   $ snactor new-model Hostname
```

The model boiler plate will be now available at models/hostname.py and looks like this:

```python
from leapp.models import Model, fields


class Hostname(Model):
    channel = None #  TODO: import appropriate channel and set it here
```

As the comment says, we will have to import the SystemInfoChannel and assign it to
the channel variable of the Hostname class.

You can import the SystemInfoChannel class like this:
```python
from leapp.channels import SystemInfoChannel
```

After the channel has been assigned we will create a new field for the message
called name, which is supposed to be a string. This can be accomplished by
setting the name field like this:

```python
class Hostname(Model):
    channel = SystemInfoChannel
    name = fields.String()
```

Now let's add a default value of 'localhost.localdomain', in case the name
does not get set. Default values are initializing the values in the
construction of the class object, if there has not been passed any other
value.

```python
class Hostname(Model):
    channel = SystemInfoChannel
    name = fields.String(default='localhost.localdomain')
```

Now we can save the file a go write an actor using the other parts.

#### Screencast
![Create Model Tutorial Cast](create-model.gif)


### Creating an actor
