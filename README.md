# Rails 2 Walkthrough Lecture

## Timeline
- 9:00 quick stretch
- 9:02 start lecture - Part I
- 9:55 break
- 10:00 resume lecture - Part II
- 10:55 break
- 11:00 resume lecture - Part III
- 11:30 - 12:00 end lecture

## Part I
### README.md
- As you're reading through the README, point out how much time people have, and what the ideal timeframe is that they should be spending in total (~40 min to and hour), this is also 'note taking space' where they can write out the FIGVAPOR/CELLL acronym for future reference. 
- Creating DB:
  - don't forget to `bundle install`  
  - run `bundle exec rspec` and see what happens when you don't create the db (might have to run `bundle exec rails db:drop` to see this error)
    - you end up with an `ActiveRecord::NoDatabaseError: FATAL:  database "user_goal_app_test" does not exist` error
  - after creating db with `bundle exec rails db:create` run rspec:
    - errors should now say `NameError: uninitialized constant ___` for models and controllers
- Migrations:
  - must create migrations using `rails g migration Users col_name:datatype ...`
  - stress that migration needs to be saved with the command `bundle exec rails db:migrate db:test:load`
  - what happens if you don't run `db:test:load`? Your "test" db schema isn't updated to mirror the "development" db schema.
- Resources:
  - **!! stress that they should READ THE WHOLE README before writing any code. !!**
  - pay close attention b/c the actual exam could use different column names and datatypes
  - keep in mind the boolean datatype also needs a default of false
- User Authentication Tips:
  - just mention that the bcrypt gem is already commented in
- Specs:
  - Models > Controllers > Views > Features
  - demonstrate how to use save_and_open_page inside of capybara specs later
  - follow the order
- Submission:
  - after renaming the directory, please remove the `tmp` and `vendor` folders before submitting, if you have any submission issues, please raise a question on zoom immediately
  
### Migrations and Models
- when running rspec, without making migrations: 
  - error: `NameError: uninitialized constant User`
- after making migration but not migrating:
  - error: `ActiveRecord:PendingMigrationError: Migrations are pending. To resolve this issue, run: bin/rails db:migrate RAILS_ENV=test`
  - error: when you don't run `db:test:load` you get a migrations are pending error
- after migrating:
  - error: `NameError: uninitialized constant User`
- create the model:
  - we get all of our failing specs
  - DON'T FORGET TO INHERIT FROM APPLICATIONRECORD
- FIGVAPER acronym:
  - F: find_by_credentials
  - I: is_password?
  - G: generate_session_token
  - V: validations
  - A: after_initialize, attr_reader
  - P: password=
  - E: ensure_session_token
  - R: reset_session_token!
- some bugs you might come across:
  - not setting @password = password
  - don't forget that reset_session_token! is not using ||=
  - User model: make sure you have ensure_session_token and password= and after initialize and attr_reader in order for validation specs to pass

## Part II
### Controllers
- Application Controller spec is like a free spec BUT:
  - fill it out so you have access to these methods in your UsersController
  - C: current_user
  - E: ensure_logged_in
  - L: login!
  - L: logout!
  - L: logged_in?
- all the other controllers need to inherit from ApplicationController
- if you run the spec without making a controller: (or spelling error)
  - `NameError: unititialized constant UsersController`
- after making controller: 
  - errors could say: `ActionController::UrlGenerationError: No route matches {:action=>"new", :controller=>"users"}`
  - means you have to make the routes
- even after defining the methods, you'll run into an error:
  - `UsersController#new is missing a template for this request format and variant.`
- create the views for views/users
  - **make sure not to nest views in the layouts**
- understanding that you'll need to implement some methods from application controller to pass user controller specs
- having the whole user controller filled out will still fail some specs which are dependent on having a login page
- Error you might run into:
  - `@user.save` vs `@user.save!`: if you use save! you'll actually return errors and fail the specs
- for SessionsController, routes need to have `resource` singular otherwise specs will show:
  - `No route matches {:action=>"create", :controller=>"sessions", :user=>{:username=>"jill_bruce", :password=>"abcdef"}}`
- errors for sessions_controller:
  - check specs for specific message rather than calling @user.errors.full_messages
  - destroy action: don't forget that we want to have a `before_action :ensure_logged_in` for the destory action
- errors for goals_controller:
  - if routing is not nested:
    - `No route matches "/users/1/goals"`
  - for destroy action:
    - make sure to use find_by on current_user.goals

## Part III
### Views
- add `save_and_open_page` in the specs for login (doesn't work on windows for some reason)
- users index
  - make sure that erb tags are the correct type for logic vs rendering
  - include debuggers here at anytime if you want to see what variables you have access to
- users/new
  - make sure labels and inputs are nested inside of the form tag
  - if you use the wrong erb tag in a form, it will not recognize the route on submit
  - must include `<input type="hidden" name="authenticity_token" value="<%= form_authenticity_token %>">` for the exam
- sessions/new
  - if you copy over the users/new form, make sure to remove the `@user.username` and `@user.password`
  - include `Sign In` somehwere on the page
- users/show
  - form action: look it up in routes, make sure that it takes in the argument `@user`
  - adding a goal: make sure the radio button values are true/false and that there's a label with True and False
  - button name matters
  - make sure your top level key is SINGULAR: `goal[]` not `goals[]`
  - delete goal, make sure the goal_url has argument (goal), don't add the form outside of the do block
