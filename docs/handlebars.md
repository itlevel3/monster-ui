# Handlebars in Monster-UI
In order to add dynamic data in our views, we needed a templating engine. And Handlebars is the templating engine we chose. In this document, we'll explain how to use it, and how to use some helpers that can be very helpful for certain tasks.

## How to use it
Let's start with an easy example. We want to display a banner displaying `Hello Georges!` whenever the User georges logs in. The HTML to do that could be something like this:

hello.html

	<div class="banner-hello">
		<span>Hello Georges!</span>
	</div>

This hello.html file is a template. In order to add it somewhere in your UI, you would do `container.append(monster.template(self, 'hello'))` and it would add this HTML inside the container.

Now let's add some dynamic data, since that's why we need to use Handlebars in the first place! The problem with our template above, is that we need to say Hello to the user, and we don't want to display Hello Georges! but Hello followed by the first name of the user. In order to do so, we need to have a variable inside this template. We know that in order to give a variable to our template we need to do:

app.js

	var dataTemplate = {
			firstName: 'Pedro'
		},
		template = monster.template(self, 'hello', dataTemplate);


hello.html

	<div class="banner-hello">
		<span>Hello {{firstName}}!</span>
	</div>

It's almost exactly the same template as above, except that we're now using the firstName variable, and we can now dislpay it properly for all the users.

## Going further
First of all, we require all the templates to be internationalizable. We don't force you to create more than the en-US.json file, but it's important to have the capability to add more languages later on. If you want a better understanding on how to use the i18n, please go visit [this link](https://github.com/2600hz/monster-ui/tree/master/docs/internationalization.md)

### Listing of different helpers
Handlebars comes with a lot of helpers already built, you can see the list [here](http://handlebarsjs.com/). It includes basic helpers such as each, if, unless, etc...

Now we'll check which helpers we created to help us specifically with some common problems we encountered.

##### debug
The debug helper is really helpful to debug your templates. Sometimes you lose track of the current context in the template, and dropping a `{{debug}}` will output the current context in the console.
You can also add an argument after it to output a specific object. Here's an example:

example.html

	{{debug}} <!-- Would output the entire context, containing users and anything else -->
	{{debug users}} <!-- Would output the entire users object}} -->
	{{each users}}
		{{debug}} <!-- Would output the object representing the current user -->
		<div class="user-div">
			<span class="user-first-name">{{firstName}}</span>
			<span class="user-last-name">{{lastName}}</span>
		</div>
	{{/each}}

##### formatPhoneNumber
The formatPhoneNumber helper can display a phone-number with proper formatting automatically. It's useful because a lot of numbers are stored in the database with a format such as `+14151993821` and we want to display them to the user like `+1 (415) 199-3821`.

example.html

	<div class="user-div">
		<span class="user-first-name">{{firstName}}</span>
		<span class="user-last-name">{{lastName}}</span>

		<div class="user-phone-number">{{formatPhoneNumber phoneNumber}}</div>
	</div>

##### toLowerCase
This helper is pretty simple, and is basically executing the toLowerCase() JS function, to a string in a template. For example it would transform `Giorgio` in `giorgio`.

example.html

	<div class="user-div">
		<span class="small-user-first-name">{{toLowerCase firstName}}</span>
	</div>

##### compare
There is no way natively in Handlebars to compare if a string is equals to "test2" for example. Although it makes sense to try and avoid doing logic in the views, there are some places where we need this logic in the view instead of adding tons of code in the JS. The compare helper is here to help us with it.

In order to use it, you need to give 3 arguments:
* the left value,
* the operator, needs to be in this list: ['==', '===', '!=', '!==', '<', '>', '<=', '>=', 'typeof'],
* the right value

Let's see an example:

example.html

	{{compare role "===" "admin"}}
		<div class="admin-welcome">You're an admin! You must be so cool!</div>
	{{else}}
		<div class="peon-welcome">You're a user! That's cool.</div>
	{{/compare}}

In the above example, we check if the role of a user is `"admin"` and if it is we display a special div, otherwise we display another one. We can use it the same way to compare values, for example `{{compare moneyLeft ">=" 10000}}You're so rich...{{/compare}}`. You get the idea!

##### replaceVar
This helper allows you to replace a variable inside another variable with Handlebars. It's useful for i18n keys.
You can see a very good example about this helper [here](https://github.com/2600hz/monster-ui/blob/master/docs/internationalization.md#in-html-templates).

##### formatTimestamp
This helper allows you to display time in customizable format. It take a mandatory JavaScript or Gregorian timestamp in parameter and returns a time:

```handlebars
{{formatTimestamp timestamp}}
```
Output: 12/1/14 10:00:00

If you want to display a time in another format, you can specify an optional parameter:

```handlebars
{{formatTimestamp timestamp 'DD-MM-YYYY hh:mm'}}
```
Output: 1-10-2014 15:52

This helper will search for the following strings and replace them by the corresponding values:
* YYYY: full year
* YY: last 2 digits of the year
* MM: month as a 2 digits number
* DD: date of the day
* hh: hours
* mm: minutes
* ss: seconds