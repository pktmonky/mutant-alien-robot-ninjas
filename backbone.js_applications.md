#Book Review
This is both a review of Backbone.js and the book *Backbone.js Applications*. Having read only one Backbone.js book, can my opinion really be accepted. This is an excellent book for learning Backbone.js: smoothly going from basic topics like creating a Model and Collection for your data and tying a View and events to that Model to advanced topics like unit testing, automation with Grunt, and Modular development with RequireJS. And the real value to this book is the examples: three separate example applications to help solidfy the topics being presented.

#Library Review
Backbone.js is an available option for Single Page Applications (SPA) where the data being presented might change but the basic application logic is the same for every page. It is a client-side MV* library which also includes Routes for client-side routing and Collections for grouping Models. Instead of a Controller, Backbone.js uses an event-driven communication between Views and Models. The eventing system is also quite extensive, including pub/sub out of the box. Backbone.js even provides a RESTful interface central to it's design, which gives the Model an easy interface to the back-end database. One final feature to note is its agnostic nature towards Templating. Although it supports Underscore... Underscore.js is its only dependency.

##Components
* Models define data and logic about the data. A portion of the traditional Controller logic exists within the Backbone.Model. 
* Backbone.View is not a traditional MVC View; it contains more than the tranditional HTML markup templates for an application. Instead it defines the logic behind the presentation of the Model's data combined with the Javascript templating markup.
* Collections are sets of Models allowing events to be tied to a Collection instead of a single Model

##Detail
A View instance uses the render() function to construct an HTML element from the defined template and the Model's data and attributes. By binding the render() function to a Model's change events we create dynamic pages. Because an SPA has events instead of traditional requests, the Controller is no longer relevant and the logic is better suited in the View.

RESTful persistence is provided out of the box. Collections.fetch() retreives a set of Models from the server in JSON by sending an HTTP GET to the collections URL property and then executing a set() on the %%%. The .save() function sends an HTTP PUT to save the data in the back-end database. If save() is called on a new instance or Collections.create() is called then an HTTP POST is sent instead. The .destroy() function rounds out the CRUD model and sends an HTTP DELETE to remove the data from the back-end database.
