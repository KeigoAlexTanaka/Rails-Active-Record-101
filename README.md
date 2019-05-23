---
title: Active Record 101
type: Lesson
duration: "2:30"
creator:
    name: Celeste Layne
    city: NY
competencies: Programming
---

# Active Record 101

### Why is this important?
_This workshop is important because:_

We're going to want to interact with the database and ActiveRecord is the machine that manages the Object Relational Mapping system for rails. That means it manages the schemas, models, and the shape of the (relational) database so that we can do all of the CRUD functionality we're used to! As described in the ActiveRecord Basics rails guide:

> In Active Record, objects carry both persistent data and behavior which operates on that data.

### Learning Objectives
_After this lesson, developers will be able to:_

- Create a model that inherits from ActiveRecord class
- CRUD data in the database using our model
- Generate a migration to define a database schema
- Use our model to interact with data in the Rails console

### Where should we be now?
*Before this lesson, developers should already be able to:*

- Describe and build models in a JavaScript application
- Explain the Model, View, Controller architecture

## What _is_ Active Record???

Active Record is what we're going to use to manage our database for our Rails app. It comes built in to Rails. Active Record is an ORM, similar to Sequelize. It allows us to represent all of the information in our database as objects.

### SQL vs ActiveRecord

##### Create
```sql
INSERT INTO animals (name, age, type) VALUES ('Snowball 2', 12, 'Cat');
```
```ruby
Animal.create(name: 'Snowball 2', age: 12, type: 'Cat')
```

##### Read

Getting many:

```sql
SELECT * FROM students;
```
```rb
Student.all
```

Getting one:

```sql
SELECT (*) FROM animals WHERE id = 2;
```
```rb
Animal.find(2)
```

##### Update

Updating many:

```sql
UPDATE students
SET grade = 100
WHERE (students.age BETWEEN 20 AND 30);
```
```ruby
Student.where(age: 20..30).update_all(grade: 100)
```

Updating one:

```sql
UPDATE students
SET name = 'Stacey'
WHERE id = 7;
```
```ruby
Student.find(7).update(name: 'Stacey')
```

##### Delete

```sql
DELETE FROM animals WHERE name = 'Garfield';
```

```ruby
Animal.find_by(name: 'Garfield').delete;
```

## Rails App for Fashion Tech Conference

### Create a new Rails application

To create a new Rails app, we use the `rails new` command. This sets us up with our skeleton Rails app. In the Terminal, in the directory where you want to create a project, generate a new Rails project and cd into it:

```
$ rails new fashion_tech_conf_app -T --database=postgresql
$ cd fashion_tech_conf_app
    
# -T means without Test::Unit (maybe you prefer to use rspec instead)
```

### Setup the database

Set up an empty database:

```
$ rails db:create
```
it will return the following:

```
Created database 'fashion_tech_conf_app_development'
Created database 'fashion_tech_conf_app_test'
```

Open the project in the current directory using your text editor (in this case it's Sublime Text):

```
$ subl .
```
Start the server and open up the project in the browser, `localhost:3000`:

```
$ rails server
```

### Generate Model

Create a Speaker model with `first_name`, `last_name` and `email` attributes as well as the optional string parameters. **Remember:** in Rails, models are singlular and capitalized. Controllers and routes are plural and lowercase.

```
# rails generate model ModelName
$ rails generate model Speaker first_name:string last_name:string email:string
```
In the Terminal, you should now see the following: 

```
  invoke  active_record
  create    db/migrate/20190523032501_create_speakers.rb <-- migration
  create    app/models/speaker.rb <-- model
```
Two files have been created: the ifrst is a migration with a timestamp which has also been named, `create_speakers`. While the model name is singular but the table name is plural. The second is a `speaker.rb` file. Let's look inside at the migration in the `db/migrate` file:

```
class CreateSpeakers < ActiveRecord::Migration[5.2]
  def change
    create_table :speakers do |t|
      t.string :first_name
      t.string :last_name
      t.string :email

      t.timestamps
    end
  end
end
```

Now, let's look inside the `models/speaker.rb`:

```
class Speaker < ApplicationRecord
	// code goes in here
end
```

##### Generate Migrations

Migrations are a set of database instructions which change our database from one state to another. In our migrations we can create a table, add a column, change the name of a column, drop a table, and much more.


To update these changes in the database, we must now run the migration:

```
# run the migration to update the database with this change
$ rails db:migrate
```

The output tells us the timestamp and the name of the migration its running:

```
== 20190523032501 CreateSpeakers: migrating ===================================
-- create_table(:speakers)
   -> 0.0189s
== 20190523032501 CreateSpeakers: migrated (0.0192s) ==========================
```

### Launch the Rails console

The Rails console is a Ruby REPL (like pry or irb) that comes with Rails and knows about your Rails app.

```
rails console
# or
rails c
```

### Confirm that the Speaker model exists

```
Speaker
=> Speaker(id: integer, first_name: string, last_name: string, email: string, created_at: datetime, updated_at: datetime)
```

(You may need to "connect" to your database in the Rails console the first time you do this. Just follow the instructions Rails gives in the console. It will most likely look like the following: 'Speaker.connection' to establish a connection)

> Note that we never told our database to add fields for id, created_at and updated_at. These are added automatically, and we generally won't interact with them directly.

### Create your first Speaker record

We can seed data entry-by-entry if we'd like! Let's do that and create a speaker:

```
# reminder: create = new + save
Speaker.create({first_name: "Hayley", last_name: "Ward", email: "hward@marksandspencer.com"})
Speaker.count
Speaker.all
```

We can also seed multiple data entries at a time.

### We Do :: Record Creation and Query Challenges

Let's practice manipulating our database. Here's some data to play with:

- Create 3 new speakers in the Rails Console.
- Create 4 speakers at once by passing the following array to the create method

```
speakers_data = [
    {:first_name =>"Elise", :last_name =>"Ngobi", :email=>"elise.ngobi@dash-hudson.com"},
    {:first_name=>"Katy", :last_name=>"Blythe", :email=>"kblythe@matchesfashion.com"},
    {:first_name =>"Adam", :last_name=>"Goswell", :email=>"adam@lush.com"},
    {:first_name =>"Sandeep", :last_name=>"Verma", :email=>"sandeep.verma@allbirds.com"}
]
```
- Delete the last speaker you created.
- Find only the first speaker.
- Find only the last two speakers.
- Find a speaker by id (try id 1).
- Find a speaker by first name.
- Sort by last name (alphabetically).
- Update the email of the last speaker you created.
- Delete all the speakers you created.
- **Bonus:** Seed the database, `db/seeds.rb` and run the seed file using `$ rails db:seed`

> Hint: Use the [Rails documentation](https://guides.rubyonrails.org/active_record_querying.html#retrieving-objects-from-the-database) to query the database.

### Create Migration

What if we wanted to add a column to our Speaker table?

```
$ rails generate migration AddJobTitleToSpeakers
```
Now, in the `db/migrate` folder you will see a migration for the added column. Open the file and add the following code:

```
class AddJobTitleToSpeakers < ActiveRecord::Migration[5.0]
	def change
     add_column :speakers, :job_title, :string
	end
end
```

### Modifying Migrations

What if we wanted to make a change to our migration? How would we go about that?  We'd do `rails db:migrate` and then we specify VERSION=0 and VERSION must be in all capitals, it's very important, so VERSION=0 will set this variable equal to 0, and now when we run `rails db:migrate`, it undoes all of those migrations to go back to version 0.

```
$ rails db:migrate VERSION=0
$ rails db:migrate
```

What if we wanted to migrate the database only part of the way? We can run the following commands to see which migrations have been run:

```
$ rails db:migrate:status

# returns the name and status of the migrations
 Status   Migration ID    Migration Name
--------------------------------------------------
   up     20190523032501  Create speakers
```

Then, run:

```
$ rails db:migrate VERSION=<Migration ID>
```

We can also rollback to the last migration:
```
$ rails db:rollback
```

> Migrations often depend on the previous migrations. If you go back in time with your migrations, be very careful what you alter later on in the chain.

To re-run your migrations from the beginning, AND seed the database:

```
$ rails db:reset
```

Destroy a migration file:
```
$ rails destroy migration migration_name
```

Destroy a model:

```
$ rails destroy model model_name
```

### Migration Methods

Migrations allow us to perform mini-database operations. Let's look at some of the options that are available to us:

- Table Migrations Methods
- Column Migration Methods
- Index Migration Methods

#### Turn & Talk

Let's pair up and pick a migration method and report back its use case.

### Talk model

1. Generate a Talk model with attributes: topic (a string), duration (an integer), and start_time (a datetime).

2. Run `rails db:migrate` to update your schema with this new model, then head over to the Rails console and create a Talk instance as a sanity check.

4. Add code to your seed file to create Talks. Here's some sample data you could use:

```ruby
talks_data = [
    {:topic=>"From Waste to Wealth: Moving to a Circular Economy", :duration=>90, :start_time=>DateTime.now-(1.0/24)},
    {:topic=>"Virtual Changes: Will Technology Save Us?", :duration=>45, :start_time=>DateTime.now+(23.0/24)},
    {:topic=>"It’s a Meme World Out There: Keeping up with the Evolution of Communication", :duration=>30, :start_time=>DateTime.now+(2.0/24)},
    {:topic=>"Weaving Tech With Fashion For A Sustainable Future", :duration=>30, :start_time=>DateTime.now+(4.0/24)}
]
```
5. Try a few complex queries:
 - Using `after_initialize` [ActiveRecord Callback](https://guides.rubyonrails.org/active_record_callbacks.html) add default values for a talk's duration (e.g. 30 minutes) and topic (e.g. "Topic Name Here").
 - Select all talks with start_times in the future.
 - Count the number of talks with the default topic ("TBD")
 - Iterate through all the talks with the default topic and puts the start time of each. **Bonus:** format the start time in a more human readable way.

<details>
<summary>Hint for using `after_initialize`:</summary>
<p>Create a `add_default_values` instance method to use with `after_initialize`, and be careful not to overwrite a `duration` or `topic` passed in for the instance.</p>
</details>

### Next Steps

1. Add routes, controllers, and views for `Speaker` and `Talk`.
2. Create an association (relationship) between speakers and talks.

## Resources

* [Active Record Query Interface](http://guides.rubyonrails.org/active_record_querying.html)
* Active Record Class methods
  * [`where` clauses](https://apidock.com/rails/ActiveRecord/QueryMethods/where)
  * [`find_by` clauses](https://apidock.com/rails/v4.0.2/ActiveRecord/FinderMethods/find_by)
  * `find`, `update_all`, `delete_all`, `destroy_all`, etc.
* Active Record Instance methods
  * `save`, `update`, `delete`, `destroy`, etc.
* [Rails Command Line](http://guides.rubyonrails.org/command_line.html)
* [`rails db:` commands](http://jacopretorius.net/2014/02/all-rails-db-rake-tasks-and-what-they-do.html)

