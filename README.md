### Script for Recording a YouTube Tutorial: Building an Advanced To-Do Application in Rails with Tailwind CSS and PostgreSQL

---

**[Intro: 0:00 - 1:30]**

*Hello everyone! Welcome back to my channel. Today, we're going to build a full-featured to-do application using Ruby on Rails, PostgreSQL, and Tailwind CSS.*

Rails is a powerful framework known for its simplicity and productivity. It's often praised for its ability to get an application up and running fast, thanks to its convention-over-configuration philosophy. Combined with Tailwind CSS for styling and PostgreSQL for data management, we're setting ourselves up with a robust tech stack that’s ideal for building scalable web applications.

Our to-do application will allow users to create, edit, delete, and mark tasks as complete, with advanced features like due dates and filtering. Let’s dive in!

---

**[Setting Up the Project: 1:30 - 3:00]**

1. **Create a New Rails Application with PostgreSQL and Tailwind CSS**  
   First, let's open up our terminal and initialize a new Rails project:

   ```bash
   rails new todo_app -d postgresql -c tailwind
   ```

   - Here, we’re passing `-d postgresql` to set up PostgreSQL as our database.
   - The `-css=tailwind` flag tells Rails to install Tailwind CSS for styling. Rails 7 has amazing support for Tailwind, which will make our styling smooth and modern.

   *[Pause to explain the importance of PostgreSQL and Tailwind]*

2. **Navigate to the Project Directory**

   ```bash
   cd todo_app
   ```

3. **Set Up the Database**  
   Since we’re using PostgreSQL, we need to ensure our database is ready. First, let’s create it:

   ```bash
   rails db:create
   ```

   This will set up our development and test databases. If you encounter any errors, double-check your PostgreSQL username and password in `config/database.yml`.

---

4. **Add hotwire live reload**
    Add hotwire-livereload to your Gemfile:
     ```bash
    bundle add hotwire-livereload --group development
     ```
    Run installer:
    
    ```bash
      rails livereload:install
    ```

**[Generating the To-Do Model and Controller: 3:00 - 5:00]**

1. **Scaffold the Task Model with Rails Generator**  
   Rails has a great tool called the `scaffold` generator that allows us to generate a full set of views, controllers, routes, and database migrations in one command. Let’s use it to create our Task model:

   ```bash
   rails generate scaffold Task title:string description:text completed:boolean due_date:datetime
   ```

   Here’s a quick breakdown:
   - `title` is a `string` that holds the name of our task.
   - `description` is a `text` field for additional details.
   - `completed` is a `boolean` to track whether the task is done.
   - `due_date` is a `datetime` field that we’ll use to set deadlines.

2. **Migrate the Database**

   Now that our model is generated, we need to apply the changes to the database:

   ```bash
   rails db:migrate
   ```

   This command updates the database with our new `tasks` table.

3. **Start the Server and Verify the Scaffold**  
   Now, let’s start our Rails server to see the scaffolded views in action:

   ```bash
   rails server
   ```

   Open your browser and go to `http://localhost:3000/tasks`. You should see a list of tasks with options to add a new task, edit, and delete. Rails has already set up a full CRUD interface for us with the `scaffold` command!

---

**[Customizing the Application with Tailwind CSS: 5:00 - 10:00]**

1. **Open the Task Views and Add Tailwind Classes**  
   Rails has created views for us, but they’re plain HTML. Let’s make them look better using Tailwind.

   Open the `index.html.erb` file for tasks at `app/views/tasks/index.html.erb`. Here, we’ll add Tailwind classes to make it look more appealing.

   Here’s a quick example:

   ```erb
   <h1 class="text-3xl font-bold mb-4">Tasks</h1>
   <%= link_to 'New Task', new_task_path, class: "bg-blue-500 text-white px-4 py-2 rounded" %>

   <div class="mt-4 space-y-4">
     <% @tasks.each do |task| %>
       <div class="p-4 border rounded-lg shadow-md">
         <h2 class="text-xl font-semibold"><%= task.title %></h2>
         <p><%= task.description %></p>
         <p>Due: <%= task.due_date %></p>
         <p>Completed: <%= task.completed ? 'Yes' : 'No' %></p>
         <%= link_to 'Edit', edit_task_path(task), class: "text-blue-500" %> |
         <%= link_to 'Delete', task_path(task), method: :delete, data: { confirm: 'Are you sure?' }, class: "text-red-500" %>
       </div>
     <% end %>
   </div>
   ```

   *[Demonstrate how Tailwind classes transform the look and feel of the UI]*

2. **Add Tailwind Classes to the Form**  
   Open the `new.html.erb` and `edit.html.erb` views under `app/views/tasks/`. Let’s add Tailwind classes to make the form look better.

   Here’s an example of adding Tailwind to our form fields:

   ```erb
   <div class="mb-4">
     <%= form_with model: @task, local: true do |form| %>
       <div class="mb-4">
         <%= form.label :title, class: "block text-sm font-medium text-gray-700" %>
         <%= form.text_field :title, class: "mt-1 p-2 border rounded-md w-full" %>
       </div>

       <div class="mb-4">
         <%= form.label :description, class: "block text-sm font-medium text-gray-700" %>
         <%= form.text_area :description, class: "mt-1 p-2 border rounded-md w-full" %>
       </div>

       <div class="mb-4">
         <%= form.label :completed, class: "inline-flex items-center" %>
         <%= form.check_box :completed, class: "mr-2" %> Completed
       </div>

       <div class="mb-4">
         <%= form.label :due_date, class: "block text-sm font-medium text-gray-700" %>
         <%= form.datetime_select :due_date, class: "mt-1 p-2 border rounded-md w-full" %>
       </div>

       <%= form.submit class: "bg-green-500 text-white px-4 py-2 rounded" %>
     <% end %>
   </div>
   ```

---

**[Implementing Advanced Features: 10:00 - 15:00]**

1. **Adding a Task Completion Toggle**  
   To add a way to mark tasks as complete from the index, go to `app/controllers/tasks_controller.rb`, and add a new action called `toggle_complete`. This action will flip the `completed` status.

   ```ruby
   def toggle_complete
     @task = Task.find(params[:id])
     @task.update(completed: !@task.completed)
     redirect_to tasks_path
   end
   ```

   In `config/routes.rb`, add this route:

   ```ruby
   resources :tasks do
     member do
       patch :toggle_complete
     end
   end
   ```

2. **Filter Tasks by Status**  
   Add links at the top of `index.html.erb` to filter tasks by their completion status.

   ```erb
   <%= link_to 'All Tasks', tasks_path, class: "mr-4" %>
   <%= link_to 'Completed', tasks_path(completed: true), class: "mr-4" %>
   <%= link_to 'Incomplete', tasks_path(completed: false), class: "mr-4" %>
   ```

3. **Due Date Notifications**  
   Finally, add code in the `index` action to highlight tasks that are nearing or past their due date.

   ```ruby
   @tasks = Task.all
   ```

---

**[Outro: 15:00 - 16:00]**

*And there you have it! We’ve created a fully functional to-do application with advanced features using Rails, Tailwind CSS, and PostgreSQL. This app has real-world functionalities and looks great, thanks to Tailwind.*

If you enjoyed this video, don’t forget to like, subscribe, and hit the bell icon for more tutorials. Thanks for watching, and happy coding!



Great idea! Using Hotwire will make our to-do application more interactive and responsive. Hotwire combines Turbo and Stimulus to enable real-time updates without writing much JavaScript, allowing us to create a dynamic and reactive app that feels modern and fast. Here’s how to integrate Hotwire into our project:

---

### Script for Enhancing the Rails To-Do Application with Hotwire

**[Intro Recap: 0:00 - 1:00]**

*Welcome back! In this part of our advanced to-do app tutorial, we’ll use Hotwire to add real-time, reactive features to our Rails application. Hotwire, which is built into Rails 7, includes Turbo and Stimulus, giving us the ability to create a highly interactive UI without needing a lot of JavaScript.*

---

### Step 1: Setting Up Turbo and Stimulus with Rails

1. **Install Hotwire (if not installed by default)**

   If you’re using Rails 7, Hotwire is already included. To ensure Turbo and Stimulus are set up, let’s add it:

   ```bash
   rails turbo:install stimulus:install
   ```

   This command sets up both Turbo and Stimulus in your Rails app.

---

### Step 2: Making Task Actions Reactive with Turbo

1. **Enable Turbo for Task Creation and Updates**

   Let’s start by updating our task views to make them responsive to changes. 

   In the `app/views/tasks/index.html.erb`, replace our task list with a Turbo frame, which will allow us to update tasks dynamically:

   ```erb
   <h1 class="text-3xl font-bold mb-4">Tasks</h1>
   <%= link_to 'New Task', new_task_path, class: "bg-blue-500 text-white px-4 py-2 rounded" %>

   <%= turbo_frame_tag "tasks" do %>
     <div class="mt-4 space-y-4">
       <% @tasks.each do |task| %>
         <%= render task %>
       <% end %>
     </div>
   <% end %>
   ```

2. **Create a Partial for Tasks**

   To make each task easily re-renderable, let’s move the task HTML into a partial. Create a `_task.html.erb` file in `app/views/tasks/`:

   ```erb
   <div id="<%= dom_id(task) %>" class="p-4 border rounded-lg shadow-md">
     <h2 class="text-xl font-semibold"><%= task.title %></h2>
     <p><%= task.description %></p>
     <p>Due: <%= task.due_date %></p>
     <p>Completed: <%= task.completed ? 'Yes' : 'No' %></p>
     <%= link_to 'Edit', edit_task_path(task), class: "text-blue-500" %> |
     <%= link_to 'Delete', task_path(task), method: :delete, data: { turbo_frame: "tasks" }, class: "text-red-500" %>
     <%= link_to 'Toggle Complete', toggle_complete_task_path(task), method: :patch, data: { turbo_frame: dom_id(task) }, class: "text-green-500" %>
   </div>
   ```

3. **Update the `TasksController` to Use Turbo Frames**

   In `TasksController`, update the `create` and `update` actions to respond to Turbo requests:

   ```ruby
   def create
     @task = Task.new(task_params)
     if @task.save
       respond_to do |format|
         format.html { redirect_to tasks_path, notice: "Task was successfully created." }
         format.turbo_stream
       end
     else
       render :new
     end
   end

   def update
     @task = Task.find(params[:id])
     if @task.update(task_params)
       respond_to do |format|
         format.html { redirect_to tasks_path, notice: "Task was successfully updated." }
         format.turbo_stream
       end
     else
       render :edit
     end
   end
   ```

4. **Create Turbo Stream Views for Real-Time Updates**

   In `app/views/tasks/create.turbo_stream.erb`, add:

   ```erb
   <%= turbo_stream.append "tasks", partial: "tasks/task", locals: { task: @task } %>
   ```

   In `app/views/tasks/update.turbo_stream.erb`, add:

   ```erb
   <%= turbo_stream.replace dom_id(@task), partial: "tasks/task", locals: { task: @task } %>
   ```

5. **Make Delete Reactive**

   When deleting a task, we want it to disappear from the list without a full page reload. In `TasksController#destroy`, use Turbo to respond with a Turbo stream:

   ```ruby
   def destroy
     @task = Task.find(params[:id])
     @task.destroy

     respond_to do |format|
       format.html { redirect_to tasks_path, notice: "Task was successfully deleted." }
       format.turbo_stream
     end
   end
   ```

   Then, create a Turbo stream view in `app/views/tasks/destroy.turbo_stream.erb`:

   ```erb
   <%= turbo_stream.remove dom_id(@task) %>
   ```

---

### Step 3: Enhancing Interactivity with Stimulus

1. **Create a Stimulus Controller**

   To add more interactivity, let’s create a Stimulus controller for tasks. Run the following command:

   ```bash
   rails generate stimulus task
   ```

   This will create `app/javascript/controllers/task_controller.js`.

2. **Add Toggle Logic to Mark Tasks as Complete**

   Open `task_controller.js` and add logic to handle task toggling or additional interactivity:

   ```javascript
   import { Controller } from "stimulus"

   export default class extends Controller {
     static targets = ["checkbox"]

     toggle(event) {
       const completed = this.checkboxTarget.checked
       this.element.classList.toggle("line-through", completed)
     }
   }
   ```

3. **Use the Stimulus Controller in Views**

   In `_task.html.erb`, attach the Stimulus controller:

   ```erb
   <div data-controller="task" data-task-target="checkbox" id="<%= dom_id(task) %>" class="p-4 border rounded-lg shadow-md <%= task.completed ? 'line-through' : '' %>">
     <h2 class="text-xl font-semibold"><%= task.title %></h2>
     <input type="checkbox" data-action="task#toggle" <%= task.completed ? 'checked' : '' %>>
     <p><%= task.description %></p>
     <p>Due: <%= task.due_date %></p>
     <p>Completed: <%= task.completed ? 'Yes' : 'No' %></p>
     <%= link_to 'Edit', edit_task_path(task), class: "text-blue-500" %> |
     <%= link_to 'Delete', task_path(task), method: :delete, data: { turbo_frame: "tasks" }, class: "text-red-500" %>
     <%= link_to 'Toggle Complete', toggle_complete_task_path(task), method: :patch, data: { turbo_frame: dom_id(task) }, class: "text-green-500" %>
   </div>
   ```

4. **Test the Reactive Features**

   Save your changes, restart the Rails server if needed, and head to `http://localhost:3000/tasks` in your browser. Test creating, updating, toggling completion, and deleting tasks, all with smooth, real-time updates!

---

**[Outro Recap: 12:00 - 13:00]**

*And that’s it! We’ve successfully enhanced our Rails to-do application with Hotwire, making it reactive and more interactive. We covered Turbo for real-time updates and Stimulus for custom interactivity. With these tools, our app feels snappy and responsive without relying heavily on JavaScript frameworks.*

*Thanks for tuning in! Don’t forget to like, subscribe, and comment if you found this helpful, and let me know what features you’d like to add next. Happy coding!*
