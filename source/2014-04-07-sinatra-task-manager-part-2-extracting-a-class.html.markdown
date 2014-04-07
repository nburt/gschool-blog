---
title: Sinatra Task Manager Part 2 - Extracting a Class
date: 2014-04-07 01:03 UTC
tags: Sinatra, how-to
---

In this post I’ll be building off my <a href="http://www.nathanaelburt.me/2014/04/02/creating-a-task-manager-with-sinatra.html">previous post</a> on how to create a simple task manger using Sinatra. Here is how to extract a class from the `app.rb` file to make your app more adaptable to future change:

###The set up

Start by creating your directories and files. We’ll need a new directory called `lib` and I’ve named the new file `tasks_helper.rb`. To see the code for this project, check it out on GitHub here: <a href="https://github.com/nburt/sinatra_task_manager">https://github.com/nburt/sinatra\_task\_manager</a>

###Adding and displaying tasks

1) Create a new class called TasksHelper.

2) Initialize the class with an empty array:

```
def initialize
  @task_list = []
end
```

3) Create an `add` method.

```
def add(task)
  @task_list << task
end
```

You’ll notice that I used the same basic principles for the `add` method as I did previously in the `app.rb` file. Before we had this:

```
TASK_LIST << params[:add_task]
```
We’re still shoveling the task into the task list, just in a separate class.

4) Now create a `display_tasks` method that returns the `task_list`.

```
def display_tasks
  @task_list
end
```

###Editing tasks

1) There is just one quick step to add an `edit` method to the file. Again, we’ll be using the same basic principles as before. Here, we’ll take two parameters: the `id` for the task and the `new_task`. Then, we’ll just rename the task at the id that is passed in.

```
def edit(id, new_task)
  @task_list[id] = new_task
end
```

###Deleting tasks

1) Finally we have to add a `delete` method to the class. Here, we’ll delete the task at the id that is passed in.

```
def delete(id)
  @task_list.delete_at(id)
end
```

You can see from the final version, we really didn’t have to do a whole lot of work.

Here’s the `app.rb` file that we started with:

```
class App < Sinatra::Application
  TASK_LIST = []

  get '/' do
    erb :index, locals: {:task_list => TASK_LIST}
  end

  get '/tasks/new' do
    erb :add_task
  end

  post '/' do
    TASK_LIST << params[:add_task]
    redirect '/'
  end

  get '/tasks/:id' do
    erb :show_task, locals: {
                            :id => params[:id],
                            :task => TASK_LIST[params[:id].to_i]
                            }
  end

  get '/tasks/:id/edit' do
    erb :edit_task, locals: {
                            :id => params[:id],
                            :task => TASK_LIST[params[:id].to_i]
                            }
  end

  put '/tasks/:id' do
    TASK_LIST[params[:id].to_i] = params[:new_task_name]
    redirect '/'
  end

  delete '/tasks/:id' do
    TASK_LIST.delete_at(params[:id].to_i)
    redirect '/'
  end
end
```
and here’s the class we just extracted:

```
class TasksHelper

  def initialize
    @task_list = []
  end

  def add(task)
    @task_list << task
  end

  def display_tasks
    @task_list
  end

  def edit(id, new_task)
    @task_list[id] = new_task
  end

  def delete(id)
    @task_list.delete_at(id)
  end
end
```

###Incorporating the class in the app

Now we need to edit our `app.rb` file to incorporate the new class that we just created.

1) Start off by requiring the new class that you just created

```
require './lib/tasks_helper'
```

2) Next, instead of setting our `TASK_LIST` constant to an empty array, we’ll just set it to an instance of our `TaskHelper` class:

```
TASK_LIST = TasksHelper.new
```

3) Now let’s work on inserting the `add` method. Instead of shoveling `params[:add_task]` into `TASK_LIST`, we’re simply going to call the `add` method on `TASK_LIST` and pass it `params[:add_task]`:

```
TASK_LIST.add(params[:add_task])
```

4) To pass everything to our views, we need to call our `display_all` method when we save `TASK_LIST` as a local in all of our `get` routes. They should all look like this now:

```
get '/' do
  erb :index, locals: {:task_list => TASK_LIST.display_tasks}
end

get '/tasks/:id' do
  erb :show_task, locals: {
                          :id => params[:id],
                          :task => TASK_LIST.display_tasks[params[:id].to_i]
                          }
end

get '/tasks/:id/edit' do
  erb :edit_task, locals: {
                          :id => params[:id],
                          :task => TASK_LIST.display_tasks[params[:id].to_i]
                          }
end
```

5) Next, we’ll tackle editing tasks. To incorporate our edit method, we just need to make a couple of changes to our `put` route. Call our `edit` method on `TASK_LIST` and pass it two variables: the id (`params[:id].to_i`) and the new task name (`params[:new_task_name]`). Remember that since params return strings, we need to call the `.to_i` method on `params[:id]` to convert it to an integer.

```
put '/tasks/:id' do
  TASK_LIST.edit(params[:id].to_i, params[:new_task_name])
  redirect '/'
end
```

6. To integrate our final method, `delete`, simply call the `delete` method on `TASK_LIST` and pass it the id.

```
delete '/tasks/:id' do
  TASK_LIST.delete(params[:id].to_i)
  redirect '/'
end
```

Here’s what your final code should look like for your `app.rb` file:

```
require 'sinatra/base'
require './lib/tasks_helper'

class App < Sinatra::Application
  TASK_LIST = TasksHelper.new

  get '/' do
    erb :index, locals: {:task_list => TASK_LIST.display_tasks}
  end

  get '/tasks/new' do
    erb :add_task
  end

  post '/' do
    TASK_LIST.add(params[:add_task])
    redirect '/'
  end

  get '/tasks/:id' do
    erb :show_task, locals: {
                            :id => params[:id],
                            :task => TASK_LIST.display_tasks[params[:id].to_i]
                            }
  end

  get '/tasks/:id/edit' do
    erb :edit_task, locals: {
                            :id => params[:id],
                            :task => TASK_LIST.display_tasks[params[:id].to_i]
                            }
  end

  put '/tasks/:id' do
    TASK_LIST.edit(params[:id].to_i, params[:new_task_name].to_s)
    redirect '/'
  end

  delete '/tasks/:id' do
    TASK_LIST.delete(params[:id].to_i)
    redirect '/'
  end
end
```

###Next steps

Here are some ideas to improve your app, I’ll hash them out in future blog posts.

1) Try styling the app with some CSS

2) Try tying a database to the app so that can save tasks even when the app has closed

3) How can you add a description to each task that you create?

4) Try using layouts to dry up the html

