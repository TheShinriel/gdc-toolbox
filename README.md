# gdc-server, an arma 3 community toolbox

## Target users

This application is intended for players, mission creators and administrators of Arma 3 community servers.

Arma 3 is a military simulation video game from Bohema Studios.

## Features

### Mission publication

Allows mission makers to :

* Check the pbo file :
  * File size
  * File extension : must bo ```pbo```
  * pbo integrity
  * file naming convention
  * a briefing.sqf must exist in the pbo
  * a mission.sqm file must exist in the pbo

* If the pbo conformity check is successful, allow the mision maker to :
  * publish his mission to a directory, as defined by server admin
  * save key informations in a database

### List missions

Allows players to access the list of missions. Mission briefing can be accessed from this list.

### Automatic briefing generation

The briefing is rendered as an html page, using the view ```showBriefing.hbs```.

Briefing is automatically generated from a ```briefing.sqf``` file, by reading his content and computing it with some regex :

```player createDiaryRecord ["Diary", ["Mission", "Notre commando a réussi à s'infiltrer près de l'aérodrome..."]];```

* Where the string ```"Mission"``` is considered as a title
* and ```"Notre commando..."``` as a content. This content is rendered as pure html.

All other strings are ignored.

If an ```onLoadImage``` is found in the mission .pbo, it is used as an illustrative image for the briefing.

## Installation

### First installation

#### Download and install Mikero tools on your server

[Mikero tools](https://mikero.bytex.digital/Downloads).

At least :

* ExtractPbo
* DeOgg
* (maybe) DePbo

#### Download and install node

[node](https://nodejs.org/)

#### Clone git repository

git clone <https://github.com/Tanin69/gdc-server>

#### Install modules

In the server directory, run ```npm install```

#### Define environment variables

* Copy ```.sample-env``` to ```.env```
* Edit path with your configuration

## Tech notes

### General architecture

This webserver is a **[node.js](https://nodejs.org/) + [Express JS](https://expressjs.com)** app. Express JS greatly simplifies node http server operations.

This app is structured as a Model-View-Controller (MVC) architecture, based on the tutorial found at <https://developer.mozilla.org/en-US/docs/Learn/Server-side/Express_Nodejs/Tutorial_local_library_website>, (although I didn't use this tutorial myself). Many many portions of the code snipets are directly taken from the github repo underlying this tutorial (<https://github.com/mdn/express-locallibrary-tutorial/tree/auth>).

Here is a visual representation of the app architecture (from the same source) :

![general application architecture](https://media.prod.mdn.mozit.cloud/attachments/2016/12/06/14456/6a97461a03a5329243b994347c47f12b/MVC%20Express.png "Express MVC architecture")

### Directory structure

    /gdc-server (app root)
        app.js
        .env
        package.json
        package-lock.json
        README.md
        CHANGELOG.md
        /controllers
        /models
        /node_modules
            [many modules]
        /public
            /css
            /img
            /javascript
        /routes
            index.js
        /views
            /layouts
            /partials
            ...

### Models and database

Models and database use [mongoDB](https://www.mongodb.com/) through [Mongoose](https://mongoosejs.com/) node module.

Models are a representation of database objects, called ```Schema``` in the mongoose universe. For example, the mission model looks like :

```javascript
const mongoose = require('mongoose');

const Schema = mongoose.Schema;

const MissionSchema = new Schema({
    missionTitle: {type: String, required: true},
    missionVersion: {type: String},
    missionMap: {type: String},
    missionPbo: {type: String},
    pboFileSize: {type: Number},
    pboFileDateM: {type: String},
    author: {type: String},
    onLoadName: {type: String},
    onLoadMission: {type: String},
    gameType: {type: String},
    minPlayers: {type: Number},
    maxPlayers: {type: Number}
});

module.exports = mongoose.model("Mission", MissionSchema);
```

### Views

Views are generated with [handlebars](https://handlebarsjs.com/) template engine (express flavor).

### Controllers

Coontrollers are portions of code that manage business logic.

### Client side components

Much of the logic is delegated to  javascript client components

* List management : [tabulator](http://tabulator.info/). Tabulator is an open source (MIT licence) javascript library. It is very well maintained, documented and very very powerfull. You should definitly take a look at tabulator !
* File uploads : [dropzoneJS](https://www.dropzonejs.com/)
