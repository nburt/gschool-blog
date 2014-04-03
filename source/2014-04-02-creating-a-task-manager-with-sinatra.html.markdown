---
title: Creating a Task Manager with Sinatra
date: 2014-04-02 00:44 UTC
tags: Sinatra, how-to
---

I’ve been working with Sinatra for a little over a week as I continue my journey at gSchool and have been playing around with a basic task manager. The following is a tutorial to create your own task manager in about an hour. I will not be going through the GitHub workflow during this tutorial, but you can check out my code and commits here: <a href="https://github.com/nburt/sinatra_task_manager">https://github.com/nburt/sinatra\_task\_manager</a>.

###Project Goals:

1) Have a homepage to view all your tasks

2) Can create a new task

3) Can view individual tasks

4) Can edit an existing task

5) Can delete old tasks


The database for this tutorial will be in memory, which means that your tasks will be lost each time you reboot the app locally. In a future blog post I will demonstrate how to tie this app to a database.

###Part 1: The set up

1) First we need to create our directory from the terminal: `mkdir sinatra_task_manager`.

2) Then type `cd sinatra_task_manager` and run `bundle init` to create your Gemfile.

3) I used several gems for this project: Sinatra, Capybara and RSpec (for testing), and Rerun (so that you don’t have to restart your local server every time you make a change in the app).

Here’s what the content of your Gemfile should look like:

```
source "https://rubygems.org"

gem 'sinatra', '~> 1.4.4'

group :development, :test do
  gem 'capybara', '~> 2.2.1'
  gem 'rspec', '~> 2.14.1'
  gem 'rerun', '~> 0.9.0'
end
```
Run `bundle` to install all the gems.

While I did write this application using tests, I will not be going over the test-writing portion for this blog post. If you would like to view the test-writing process, please view my spec directory commits for the project.

4) To enable you to run rackup and start your server locally, you’ll also need to add a `config.ru` file.

Here’s what that should look like:

```
require './app'

run App
```

###Part 2: Creating the homepage

1) The first thing we need is an `app.rb` file, so let’s go ahead and create that in our main project directory. To start the setup of the file, we’re going to require Sinatra/Base and inherit from Sinatra/Application.

```
require 'sinatra/base'

class App < Sinatra::Application

end
```

2) Next, we’ll need a “Get” route so that we can render the homepage.

```
  get ‘/‘ do
    erb :index
  end
```
3) Now that the route is setup, we’ll create a new directory called `views` where we will hold all of our webpages. I’ll be using erb files in this directory so that I can add Ruby to the page so I’ll also need to create a `index.erb` file within that directory. In that file I’ll just put some basic html with “Welcome to your Task Manager” in an `h1` tag and “Task Manager” as the page title.

```
<html>
<head>
  <title>Task Manager</title>
</head>
<body>
<h1>Welcome to your Task Manager</h1>
</body>
</html>
```
4) You can now check out the app in your browser by typing `rerun rackup` in Terminal and pointing your browser to `http://localhost:9292` and you should see this:

![Welcome to task manager homepage](/images/task_manager_part_2.png)

###Part 3: Adding new tasks

So we’ve got our application running locally, but it doesn’t really do anything yet. Let’s fix that.

1) Add a link to a page where you can create new tasks in your `index.erb` file:

```
<a href="/tasks/new">Add Task</a>
```

2) Then set up the route in your `app.rb file`

```
  get '/tasks/new' do
    erb :add_task
  end
```
3) Create a new file called `add_task.erb` in your `views` directory.

4) In it, we’ll need to add a form so that we can capture the name of the task that the user would like to add:

```
<html>
<head>
  <title>Add a Task</title>
</head>
<body>
<form action="/" method="post">
  <input type="text" name="add_task">
  <button type="submit">Add Task</button>
</form>
</body>
</html>
```

If you’re a little shaky on HTML forms and how they work, I’d recommend reading the following article from the Mozilla Developer Network: <a href="https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/Forms">https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/Forms</a>

5) Declare an empty array at the top of your `app.rb` file and then add a post route so that you can create a new task. You should now have the following within your app class:

```
  TASK_LIST = []

  get '/' do
    erb :index
  end

  get '/tasks/new' do
    erb :add_task
  end

  post '/' do
    TASK_LIST << params[:add_task]
    redirect '/'
  end
```

Let’s go over what the `TASK_LIST` constant and the `post` route are doing. The TASK_LIST starts out as an empty array that we’ll use to shovel in tasks as they are added. I decided to use an array because they are easy to add to, and they assign an easy-to-use id number to each task that you add, their index in the array. We’ll use the index to print out the task on the page, to access tasks, and to create individual web pages for each task, so this part is important.

In the `post` route, we’re getting the value from the form input with the name `add_task` in the `add_task.erb` file and shoveling it into our array of tasks. Then, we’re redirecting back to the homepage.

6) Now, we need to populate the homepage (`index.erb`) with the task that was added. So, open up the `index.erb` file and update it with the following:

```
<html>
<head>
  <title>Task Manager</title>
</head>
<body>
<h1>Welcome to your Task Manager</h1>
<a href="/tasks/new">Add Task</a>
<ol>
  <% task_list.each do |task| %>
    <li><%= task %></li>
  <% end %>
</ol>
</body>
</html>
```

Here, we’re iterating through each task in our `TASK_LIST` array, and printing that task to the page.

7) Go to `http://localhost:9292` and add an item! I added “get milk”:

![Added individual task of get milk](/images/task_manager_part_3.png)

###Part 4: Viewing individual tasks

The goal of part 4 will be to be able to click on a link that will take you to an individual page that will list the task. Eventually, this page will also contain links to allow you to edit and delete tasks.

1) First, we need to add a link to the new page that we’ll be creating, let’s add that on the homepage next to the individual task. To get the full URL of the link, we’ll need to update our loop to use the `each_with_index` method. To make these individual pages, we’ll need to use the index of the task in `TASK_LIST` so we need to figure out a way to track the index as we iterate through the `TASK_LIST` and print the task to the page. `each_with_index` will track the index as you iterate through the array, so this will work perfectly:

```
<ol>
  <% task_list.each_with_index do |task, index| %>
    <li><%= task %> - <a href="/tasks/<%= index %>">Show Task</a></li>
  <% end %>
</ol>
```

2) Add in the route to the new page:

```
  get '/tasks/:id' do
    erb :show_task, locals: {:id => params[:id],
                             :task => TASK_LIST[params[:id].to_i]
                             }
  end
```

Here I’ve used locals to save a couple of variables so that I can access them in the `show_task.erb` file that we are about to create. `params[:id]` is equal to whatever `:id` is in the url. For example, if our url was “/tasks/0,” `:id` would equal `0` and I can use it on `show_task.erb` by typing `<%= id %>`.

3) Now we can create our `show_task.erb` file and add in the following html which will list the task in the title and within an h1 tag:

```
<html>
<head>
  <title><%= task %></title>
</head>
<body>
<h1><%= task %></h1>
</body>
</html>
```

We’ll be using the `:id` variable when we get to editing and deleting tasks. When you click on the “Show Task” button, you should see this at the top of the page:

![Can access individual item page](/images/task_manager_part_4.png)

###Part 5: Editing existing tasks

1) Create a link in the `show_task.erb` file to a new page where we’ll have a form to edit the task:

```
<a href="/tasks/<%= id %>/edit">Edit Task</a>
```

2) Add another `get` route to our `app.rb` file for the new page we just linked to:

```
  get '/tasks/:id/edit' do
    erb :edit_task, locals: {:id => params[:id],
                             :task => TASK_LIST[params[:id].to_i]
                             }
  end
```

You’ll notice that I’ve again created a new locals hash that saves the values of the `:id` and `:task`.

3) Create a new file called `edit_task.erb` and then create a form that has a text field and a submit button:

```
<html>
<head>
  <title>Edit <%= task %></title>
</head>
<body>
<form action="/tasks/<%= id %>" method="post">
  <input type="hidden" name="_method" value="put">
  <input type="text" name="new_task_name">
  <button type="submit">Edit Task</button>
</form>
</body>
</html>
```

One thing of note is the first `<input>` line. You’ll notice that this is a hidden form (meaning it won’t appear on the page) and that it has some values that look a little strange such as `name=“_method”` and `value=“put”`> This allows Sinatra to use a `put` request which is for updating things. Here’s a blog about the magic behind this trick: <a href="http://mikeebert.tumblr.com/post/26877173686/quick-tip-using-put-and-delete-in-sinatra">http://mikeebert.tumblr.com/post/26877173686/quick-tip-using-put-and-delete-in-sinatra</a>

4) Similar to the form that we had for creating a new task, with the above form we’re going to be using the field with `new_task_name` to replace a task in the current `TASK_LIST` with the edited task. We’ll do this in our `app.rb` file by adding in the following code:

```
  put '/tasks/:id' do
    TASK_LIST[params[:id].to_i] = params[:new_task_name]
    redirect '/'
  end
```

What am I doing here? I’m basically saying this: “Hey array, find a task at the index of `:id` and replace it with the value that you got from the `new_task_name` field on the form. Then, redirect to the homepage to view the changes.”

Finish all that and you’ll have this:

![Can edit items on the individual item page](/images/task_manager_part_5.png)

###Part 6: Delete old tasks

Finally, we want to be able to delete our tasks from the task manager.

1) Start by adding a delete button on the individual item page. To do so, add the following html to the `show_task.erb` file:

```
<form action="/tasks/<%= id %>" method="post">
  <input type="hidden" name="_method" value="delete">
  <button type="submit">Delete Task</button>
</form>
```

Again, you’ll notice that we have used the hidden input on the form.

2) Now add a `delete` route to the `app.rb` file.

```
  delete '/tasks/:id' do
    TASK_LIST.delete_at(params[:id].to_i)
    redirect '/'
  end
```

In the route we’ll call the `delete_at` method on our `TASK_LIST`. This method takes one parameter, an index, and will delete the task that is at that index. Using the `:id` from the page url (which is the index) we can delete the task.

![Can delete items on the individual item page](/images/task_manager_part_6.png)

###Next Steps

That’s the basic layout of the app but there are obviously many more things that you can do. Here are a couple of ideas to get you started:

1) How can you refactor the add, show, edit, and delete methods in the `app.rb` file into a separate class so that they can be reused in other apps?

2) Try styling the app with some CSS

3) Try tying a database to the app so that can save tasks even when the app has closed

4) How can you add a description to each task that you create?

5) Try using layouts to dry up the html

Stay tuned for future blog posts on the above next steps.
