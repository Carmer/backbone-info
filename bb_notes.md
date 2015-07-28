# Backbone.js
==========================
### Models

Create Models:
* var TodoItem = Backbone.Model.extend({});

Create Model instance:
* var todoItem = new TodoItem({
  description: 'Buy new pants',
  status: 'incomplete',
  id: 1
  });

### Getting and Setting data:
Get a data attribute:
```
 todoItem.get('description');
 ```

To set an attribute:
```
 todoItem.set({status: 'complete'});
```

Sync to the server:
 ```
 todoItem.save();
 ```

### Displayting Data
Create a view class
```
TodoView = Backbone.View.extend({
  render: function(){
    var html = '<h3>' + this.model.get('description') + '<h3>';
    $(this.el).html(html); // this.el is the view (el)ement associated with the html. The element is a div my default but can be anything (p, li, header, section ...)

  }
  });
```

Render view by calling ```render(); ```
```
 var todoView = new TodoView({ model: todoItem });  //notice the instance variable name of the model
 ```
 ```
 todoView.render();
 console.log(todoView.el);
 ```
 or:
 ```
 $('#idName').html(todoView.el);
 ```

 which will render:
 ```
 <div>
  <h3>Buy new pants</h3>
</div>
```

### Fetching data from server

```
var todoItem = new TodoItem();
```

URL to get JSON for the model
```
todoItem.url = '/todo'
```

Populate your model from the server:
```
todoItem.fetch();
```
the above fetch function gets the whole model instance
```
{id: 1, description: 'Buy new pants', status: 'incomplete'};

todoItem.get('description')
