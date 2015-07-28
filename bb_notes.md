# Backbone.js
==========================
## An introduction to Backbone.js - IdeaBox style
### Models

Create Models: Idea class
```
var Idea = Backbone.Model.extend({});
```

Create Model instance: instance of Idea class
* var idea = new Idea({
  title: 'Coffee cookies'
  description: 'Caffeinated cookies that taste like chocolate. Mmm. cookies.',
  ,
  id: 1
  });

### Getting and Setting Data:
Get a data attribute:
```
 idea.get('description');
 ```

To set an attribute:
```
 idea.set({title: "Cafe Biscuits"});
```

Sync to the server:
 ```
 idea.save();
 ```

### Displayting Data
Create a view class
```
IdeaView = Backbone.View.extend({
  render: function(){
    var html = '<h3>' + this.model.get('title') + ': ' + this.model.get('description') + '<h3>';
    $(this.el).html(html);
  }
  });
```
this.el is the view (el)ement associated with the html. The element is a div my default but can be anything (p, li, header, section ...)

Render view by calling ```render(); ```
```
 var ideaView = new IdeaView({ model: idea });  //notice the instance variable name of the model
 ```
 ```
 ideaView.render();
 console.log(ideaView.el);
 ```
 or:
 ```
 $('#idName').html(ideaView.el);
 ```

 which will render:
 ```
 <div>
  <h3>Caffeinated cookies: Caffeinated cookies that taste like chocolate. Mmm cookies.</h3>
</div>
```

### Fetching Data From Server:

```
var idea = new Idea();
```

URL to get JSON for the model - basic example below
```
idea.url = '/idea';
```

Populate your model from the server:
```
idea.fetch();
```
the above fetch function gets the whole model instance:
* {id: 1, title: 'Caffeinated cookies', description: 'Caffeinated cookies that taste like chocolate. Mmm. cookies.', };

Where as:
```
idea.get('title');
idea.get('description');
```
will get you just the description for the model instance:
* 'Coffee cookies.'
* 'Caffeinated cookies that taste like chocolate. Mmm. cookies.'

The URL above is not very RESTful so we can create a new model and set a urlRoot:
```
var Idea = Backbone.Model.extend({urlRoot: '/ideas'});
```
Then when we instantiate a new instance of the Idea model we set the id with will give us the route: 'ideas/:id'
 ```
var newIdea = new Idea({id: 1});
```
This will give us the route: 'ideas/1' and to get the information for our newIdea instance we need to use the fetch() function.
```
newIdea.fetch(); // => GET /ideas/1
```
Updating the idea is as easy as using the .set() and .save() functions:
```
newIdea.set({description: 'decaffeinated cookies will be tasty, and ordinary.'});
newIdea.save(); // => PUT /ideas/1 with JSON params
```

### Creating a New Idea: CRUD

```
var newestIdea = new Idea();

newestIdea.set({
  title: "Wireless laptop charger",
  description: "A laptop charger that doesn't need to physically connect to your laptop."
  id: 2
  });

  newestIdea.save(); // => POST /ideas with JSON params

```

We know it works because we can call
```
newestIdea.get('id'); // => which will return us: 2
```
And to delete an idea we can just call the destroy function.
```
newestIdea.destroy(); // DELETE => /ideas/2
```
If we want to get the JSON object out of our model, to say... i don't know, render it in the view later, we can call toJSON
```
newestIdea.toJSON(); //=> {id: 2, title: "Wireless laptop charger", description: "A laptop charger that doesn't need to physically connect to your laptop."}
```
Again update is just the set() function
```
newestIdea.set({title: "Air Charger"});
newestIdea.save(); // => PUT /ideas/2
```

### Set Default Values for Model:

We can implement default values for Models too!

```
var evenNewerIdea = Backbone.Model.Extend({
  defaults: {
    title: "Title currently blank",
    description: "Description left blank"
    }
  });
```
so if you instantiate an instance of a Model and you don't set any attributes you still get something back:
```
var bestIdea = new Idea();
bestIdea.get('description'); // => "Description left blank"
bestIdea.get('title') // => "Title currently blank"
```
==============================================================================================
## It's a Party!! Models Can Have Events!!

### Backbone Models Come With Some Default Events:

Change event: listens to changes on the model and fires a function on the event.
```
bestIdea.on('change', doThing);
```

So, the event would fire on:
```
bestIdea.set();
```

You can set  an attribute without triggering the event by passing in the silent flag:
```
bestIdea.set({description: 'Lawn mowing robots'}, {silent: true});
```
or you can even plain turn special event listeners off:
```
bestIdea.off('change', doThing);
```

there are other special, built-in events in backbone.js

Special events:
* change -> When attribute is modified
* change:<attr> -> When a specific attribute, <attr>, is modified
* destroy: -> When a model is destroyed
* sync: -> When successfully synced
* error: -> When model save or validation fails
* all: -> Any triggered evernt


### You Can Add Your Own Events Too.

```
bestIdea.on('event-name', function(){
  alert('event-name happened');
  });
```

...this is much like jQuery.

========================================================================================

## Views


First let's start with looking at a basic view.

```
var IdeaView = Backbone.View.extend({});

var ideaView = new IdeaView();

console.log(idesView.el); // => <div></div>
```

First we create a new class or model of the Idea View.
Then, we instantiate an instance of the new view class.
Finally we console.log the (el)ement of the view. By default it is a <div>, but we can set it to whatever HTML element our heart desires. For example:

```
var IdeaView = Backbone.View.extend({tagName: 'ul'})

var ideaView = new IdeaView();

console.log(idesView.el); // => <ul></ul>
```
or
```
var IdeaView = Backbone.View.extend({tagName: 'p'})

var ideaView = new IdeaView();

console.log(idesView.el); // => <p></p>
```

Actually, we can specify more than the tag of the view. We can specify id's and classes too.

```
var IdeaView = Backbone.View.extend({
  tagName: 'li',
  id: 'idea-view',
  class: 'idea'
  });
```

which would give us:

```
var ideaView = new IdeaView();

console.log(ideaView.el); // => <li id="idea-view" class="idea"></li>
```

we can use a backbone along with jQuery to get the DOM element:

```
ideaView.$el.html();
```


### Templates with underscore

Luckily for us we can manage our views a little better because Backbone.js ships with the underscore library.
We can use the _.template() function from the underscore library to help us with our views.

Fore example, if we look back at the view we wrote earlier, we can now make it dynamic.

earlier we had:

```
IdeaView = Backbone.View.extend({
  render: function(){
    var html = '<h3>' + this.model.get('title') + ': ' + this.model.get('description') + '<h3>';
    $(this.el).html(html);
    }
  });
```

And now we can tie together some of the concepts we have learned to change this to:

```
var IdeaView = Backbone.View.extend({
  template: _.template('<h3><%= title %> : <%= description %></h3>'),
  render: function() {
    var attributes = this.model.toJSON();
    this.$el.html(this.template(attributes));
    }
  });
```
The purpose of this is to clean things up and make it more DRY.

Now, when we instantiate a new instance of the IdeaView, and call render on it, we can see we get our templated view.

```
var ideaView = new IdeaView({ model: idea });

ideaView.render();
console.log(ideaView.el);  // => <h3>Caffeinated cookies: Caffeinated cookies that taste like chocolate. Mmm. cookies.</h3>
```

### View Events

Backbone allows us to specify events that are scoped within View Models. We can specify these events like this:
```
var SampleView = Backbone.View.Extend({
  events: {
    "<event> <html-selector>": "<function>"
  },
  ...
  })
```

a simple example of this would be:

```
var IdeaView = Backbone.View.extend({
  events: {
    "click .idea": "eventExampleFunction"
  },
  eventExampleFunction: function() {
    alert("You clicked on an idea!")
  };
  })
```
We can use jQuery and get all the jQuery events.


Now, tie this all together with what you already know about OOP and JavaScript and you're ready to start boxing up your ideas. Good luck.
