### Project Workspace

Use this "assignment" as a place to do your work on the project.

You will clone your team's repository here, and proceed to work.  To get started, simply go to Tools->Terminal.

By using this workspace on Codio, every team member will have the same environment, which increases productivity of the team.  Also, by using this workspace, the instructor can much more easily help you with problems you run into.  You are expected to do your project work here.

Below you will find instructions for creating a shared repository in GitHub as well as how to set up your new Rails app. **There are also important instructions about Heroku.**

You are welcome to delete this README file when you no longer need it.

Best wishes!

# Creating a Shared Repository in GitHub for the project

Repository creation is done by one team member only. 

1. Go to https://github.com/UWaterlooMSCI342

1. Be sure you are on the Repositories tab.

1. Click on the green "New" button.

    - Ignore the stuff about "Repository template".

    - Name your repository `W2021-team-NNN` where you replace NNN with your team number, e.g. `W2021-team-1`.

    - You are welcome to write a nice description of the repository or leave it blank.

    - Keep the repository private.  ALL repositories in MSCI 342 should be private.

    - Click the option to "Add a README file" .   Do not select any other options.

     - Click "Create repository"

1. Click on "Settings" (to the right of "Insights")

1. Now we want to give everyone on the team admin access to the repo.  

	- Click on "Manage access"

	- Click on green button "Invite teams or people"

	- Search for your team and select it.

	- Give your team "Admin" access. This gives everyone on the team Admin control of the repo, which is needed to be able to deploy to Heroku as well as control other settings.

	- Then click the big green button to add your team to the repo.

1. Next, we want to protect the `main` branch from people pushing changes onto it without review from the team.  Trust me: we want this to help us develop a good process with source control and GitHub.  You **must** setup your team's project repo with the `main` branch protected.

    - Click on "Branches"

    - Under “Branch protection rules”, click "Add rule"

    - Type in "main" for the "Branch name pattern"

    - Select: "Require pull requests before merging".

    - Select: "Include Administrators". 

    - Double check that you've done this correctly.  Then **click the green "Create" button** at the bottom of the page.  The button may be off the page and not visible without scrolling.

# Creating the Rails app

One person on the team should create the Rails app in the shared repo for the rest of the team to use.

First:

```
git clone git@github.com:UWaterlooMSCI342/W2021-team-NNN.git 
```
and then
```
cd W2021-team-NNN
```
Remember: you must do all work on a branch, so make one:

```
git branch new-rails-app
```

then switch to it:

```
git checkout new-rails-app
```

and then do:

```
rails new . --skip-action-mailbox --skip-action-text --skip-active-storage --skip-action-cable --skip-spring --skip-javascript --skip-turbolinks --database=postgresql --skip-bundle 
```

Then, open up the `Gemfile` and comment out the `jbuilder` and `tzinfo-data` gems:

```
# Build JSON APIs with ease. Read more: https://github.com/rails/jbuilder
#gem 'jbuilder', '~> 2.7'

# Windows does not include zoneinfo files, so bundle the tzinfo-data gem
#gem 'tzinfo-data', platforms: [:mingw, :mswin, :x64_mingw, :jruby]
```

and uncomment the `bcrypt` gem, which is used for encrypting passwords:

```
gem 'bcrypt', '~> 3.1.7'
```

Then:

```
 bundle install
```

Add the following to `config/environments/development.rb` after "`Rails.application.configure do`":

```
config.hosts << /[a-z0-9]+\-[a-z0-9]+\-3000\.codio\.io/
```

For user authentication on the web to be secure, we have to make sure users only connect to our server with SSL.  We do this by editing `config/environments/production.rb` and uncommenting the setting of `config.force_ssl` to `true`:

```ruby
config.force_ssl = true
```

If a user does not use SSL, it is simple for an attacker to listen to the unencrypted communications and steal the session token stored in a user's browser cookie and then be authenticated as that user.

Now, need to adjust some things to make testing work better for us on
Codio in `test/test_helper.rb`.  In this file, comment out:

```
# parallelize(workers: :number_of_processors)
```

By commenting out `parallelize(workers: :number_of_processors)`, this will force our automated tests to run 1 at a time.  This is very handy if we want to use the byebug debugger to debug tests or see how testing works.  If you aren't going to debug while testing, you are welcome to leave parallelization of tests turned on.

In `test/test_helper.rb` also comment out:

```
#fixtures :all
```

For now, the use of fixtures, for testing, complicates our work, and we'll avoid using them.

In `test/application_system_test_case.rb`, comment out: 

```ruby
driven_by :selenium, using: :chrome, screen_size: [1400, 1400]
```

and add:

```ruby
driven_by :rack_test
```

So, you'll end up with:
```
#driven_by :selenium, using: :chrome, screen_size: [1400, 1400]
driven_by :rack_test
```

The `:rack_test` driver cannot process javascript, but it runs very quickly.  The issue with using `:selenium` and Chrome as our system test driver is that I haven't been able to get these components to install correctly on Codio.  Projects in MSCI 342 should not use javascript, and so this will not be an issue.


Then create a file named `Procfile` and put this command in the file as per the [heroku instructions](https://devcenter.heroku.com/articles/getting-started-with-rails6):
```
web: bundle exec puma -C config/puma.rb
```

Then do:

```
rails db:create db:migrate
```

This will create your databases.  

Then run `rails server -b 0.0.0.0` and go to your "Box URL".  Make you get "Yay! You're on Rails!".  This is a good time to commit and push to GitHub and submit your PR, etc.

# Heroku

To date, we've shown you how to deploy your local dev version of an app to Heroku.  This is great, and you'll still want to have that capability to do your own deploy to test it on Heroku.

But, your customer NEVER wants your local dev repo.  Your customer ALWAYS wants to see deployed the GitHub repo's `main` branch.  One person on the team needs to do the following for the team so that you can have deployment of the GitHub repo for the customer.  It would probably be best to create a new Heroku account specifically for this purpose rather than use your personal account.  You could then even give the Heroku account to the customer when the project concludes.

1. Visit https://dashboard.heroku.com/apps and sign in using the account you have already created.

1.	In the top right corner, you should see a "New" button. Click it and choose "Create new app". You can think of an App as a virtual computer / virtual server you're able to access. 

1.	Setup your app by choosing a name. For the project, please name your app using the following format: msci342-w21-team-NNN. Where NNN is your team's number, for example, msci342-w21-team-1. 

1.	Click "Create App". This will create a new Heroku app, accessible by anyone, at:
msci342-w21-team-NNN.herokuapp.com
Check that your app was successfully created by visiting it. When loaded, you should view Heroku's "Welcome to your new app!" screen.  

1.	By default, you should be navigated to the "Deploy" tab. If not, click "Deploy" from the top navigation bar. On the "Deploy" tab, under "Deployment method", you'll see we have different options for how we deploy our app. For MSCI 342, we’ll be deploying using GitHub. Thus, please click the "GitHub" logo.
    a.	Details regarding the GitHub / Heroku integration should be loaded. You'll also see a black "Connect to GitHub" button. Click it.

    b.	Enter your GitHub username and password. In GitHub's "Authorize application" window, scroll down and click the "Authorize application" button.

    c.	In Heroku, you should see a search dialog. Choose "UWaterlooMSCI342" from the dropdown and search for your repository (by entering "W2021-team-NNN" in the text field).
 
    d.	After finding your repository, click the "Connect" button to the right of the listed repository. This will connect your GitHub repository with your Heroku instance, allowing you to deploy to Heroku directly from GitHub.
    
    
6.	With your GitHub repository linked, you should now be able to manually deploy the contents of the repository directly to your Heroku app. By default, Heroku will suggest you deploy the main branch. To execute the deploy, click the "Deploy Branch" button. After clicking the button, Heroku will clone a copy of your repository to your app and build it.

1. After a successful build, you still need to migrate the db.  Go back to the top of the web page, and click on "More" and then "Run Console".  You can then do "rails db:migrate" to run the migrations.

1.	Test your app by clicking "Open App".  Note: if you haven't added anything beyond the "Yay! You're on Rails!" to your app, the production version, which is the one deployed to Heroku, will not work yet.  

1. Give your teammates access to the app via "Access", so that they too can deploy from GitHub to the app.

