## TrackerReact

This mixin is an upgrade to what `ReactMeteorData` offers. Using `TrackerReact` instead you are no longer required to "freeze" alll your reactivity in a single method. Every one of your methods which uses reactive data sources (e.g: `collection.find()` or `Session.get('foo')`) *automatically* registers its dependencies and is *automatically* tracked. In doing so it replicates the standard helper experience from Meteor/Blaze. Enjoy!

Note: There are a few ways to prevent methods from being reactive, as seen below in the comments.

## EXAMPLE:

```
App = React.createClass({
    mixins: [TrackerReact],

    //automatically reactive helpers, no need for `getMeteorData`!
    title() {
        return Session.get('title') ? ' (' + Session.get('title') + ')' : '';
    },
    getCurrenTask() {
        return Session.get('currentTask') || 'n/a';
    },
    getTasks() {
        return Tasks.find({}).fetch();
    },

    //underscore prevents method from being reactive
	_renderTasks() {
		return this.getTasks().map((task) => {
			return <Task key={task._id} task={task} />;
		});
	},

    //events are also blocked from being reactive; for now we put them in a map return from a method named `events` 
    events() {
        return {
            editTitle: function() {
                let title = prompt('Enter a title for this to do list');
                Session.set('title', title);
            },
            addTask: function() {
                Tasks.insert({text: prompt('Enter the title of a task buddy')});
            }
        }
    },

	
	render() {
		return (
			<div className="container">
				<header>
					<h1>
						Todo List {this.title()}
						<span style={{color: 'blue', fontStyle: 'italic', cursor: 'pointer'}} onClick={this.editTitle}> edit title</span>
					</h1> 

					<h2>current task: <i>{this.getCurrenTask()}</i></h2>

					<button onClick={this.addTask}>ADD TASK</button>
				</header>

				<ul>
				  	  {this._renderTasks()}
				</ul>
			</div>
		);
	}
});


Tasks = new Mongo.Collection('tasks');
```

----
###An example app has been prepared to quickly test it. 
Give it a try: https://github.com/ultimatejs/tracker-react-todos-example