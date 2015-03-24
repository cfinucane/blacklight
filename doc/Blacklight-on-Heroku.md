> As of March 2014, the directions from the Heroku [Getting Started with Rails](https://devcenter.heroku.com/articles/getting-started-with-rails4) guide can be followed as-is with an existing Blacklight application.

<hr />

Blacklight can be deployed on [Heroku](http://heroku.com/), a Platform-as-a-Service (PaaS) provider that supports Ruby. This walkthrough assumes some familiarity with Heroku, that you have a Solr server running somewhere, and that you already have a Heroku account. For more information on working with Heroku, please see [Getting Started with Heroku](https://devcenter.heroku.com/articles/quickstart).

## Create a new Blacklight app

* Use the Blacklight [[Quickstart]] to create a new Rails application:

    ```bash
    $ rails new blheroku      
    #      create  
    #      create  README
    #      create  Rakefile
    #      create  config.ru
    #      create  .gitignore
    #      create  Gemfile
    #      [...]
    
    $ cd blheroku
    $ rm public/index.html

* Append this line to your application's `Gemfile`:

    ```ruby
    gem 'blacklight'
    ```

* Install the gems using Bundler:

   ```bash
$ bundle install
   ```

* Call the Blacklight generator (in this case, without Devise for authentication):

   ```bash
$ rails generate blacklight
   ```

## Change Gem dependencies for Heroku deployment

* Change the database-related gems in your `Gemfile` from:

    ```ruby
    gem 'sqlite3'
    ```

    * to the following:

    ```ruby
gem 'sqlite3', :groups => [:development, :test]
gem 'pg', :group => :production
    ```

* Move all gems in the asset block of `Gemfile` to the main block [see here for more info](http://stackoverflow.com/questions/9629620) (still needed? - 2/1/2014).

## Configure Solr for your application

* While untested, you may want to look at [Websolr](http://websolr.com/), which provides Solr as a Service.
* Add a line `config/solr.yml` to point to your production instance of Solr:

   ```yaml
production:
  url: http://my.solr.host:8983/path/to/solr
   ```

## Index some test records

   ```bash
$ RAILS_ENV=production rake solr:marc:index_test_data
   ```
## Set up Heroku

   ```bash
$ git init
$ heroku create --stack cedar
Creating vivid-winter-2427... done, stack is cedar
http://vivid-winter-2427.herokuapp.com/ | git@heroku.com:vivid-winter-2427.git
Git remote heroku added
$ git add .
$ git commit -m"Initial import"
   ```

* Take note of the URL reported back by Heroku (in this case, http://vivid-winter-2427.herokuapp.com/)

## Deploy to Heroku

   ```bash
$ git push heroku master
[...]
$ heroku run rake db:migrate
[...]
   ```

## Try opening up your app

* Go to URL reported by Heroku above (in this example, http://vivid-winter-2427.herokuapp.com/).
* Does it work? If it doesn't, try running `heroku logs` to see where problems are cropping up.