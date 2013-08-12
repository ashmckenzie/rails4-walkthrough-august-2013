!SLIDE

# Rails 4

## Walkthrough

![Rails](/assets/images/ruby_on_rails_logo.jpg)

!SLIDE classes="small-bullets-x3"

# What's in?

* Ruby 1.9.3 minimum
* Strong parameters
* ActiveRecord PostgreSQL love
* Cache digests & Russian doll caching
* Thread safety
* Concerns
* ActionController::Live
* Turbolinks
* New ActiveRecord scopes
* Some core features now plugins

!SLIDE

# Major new features

![Rails 4 - what's new](/assets/images/rails4_features.png)

!SLIDE classes="small-bullets"

# Ruby 1.9.3 minimum

!SLIDE classes="small-bullets"

# Strong parameters

* Mass assignment protection
* Define mass assignable fields in the controller
* No more `attr_accessible` cluttering your models

:metal:

[github.com/rails/strong_parameters](https://github.com/rails/strong_parameters)

!SLIDE classes="small-bullets" language="haml"

%h1 Strong parameters

%h2 Code example

%pre{ 'data-language' => 'ruby' }
  :escaped
    class PeopleController < ActionController::Base
      def update
        person = current_account.people.find(params[:id])
        person.update_attributes!(person_params)
        redirect_to person
      end

      private

        def person_params
          params.require(:person).permit(:name, :age)
        end
    end

!SLIDE classes="small-bullets"

# ActiveRecord PostgreSQL :heart:

* HSTORE support - key/value pair (but JSON superior)
* Array support
* uuid column type

:thumbsup:

[A love affair with PGSQL Rails 4 countdown to 2013](http://blog.remarkablelabs.com/2012/12/a-love-affair-with-postgresql-rails-4-countdown-to-2013)

!SLIDE classes="small-bullets" language="haml"

%h1 ActiveRecord PostgreSQL :heart:

%h2 Array support

%pre{ 'data-language' => 'ruby' }
  :escaped
    class AddTagsToArticles < ActiveRecord::Migration
      def change
        change_table :articles do |t|
          t.string :tags, array: true
        end
      end
    end

    article.tags = ['rails']
    article.save
    article.reload
    article.tags # => ["rails"]

!SLIDE classes="small-bullets"

# ActiveRecord PostgreSQL :heart:

## uuid column type

* Universally Unique IDentifier as primary key
* Need `'uuid-ossp'` extension loaded

[Using UUID as a Primary Key on Postgres and Rails 4](http://www.gingerbeard.me/2013/05/28/rails-using-uuid-as-a-primary-key-on-postgres-and-rails-4/)

!SLIDE classes="small-bullets" language="haml"

%h1 ActiveRecord PostgreSQL :heart:

%h2 uuid column type

%pre{ 'data-language' => 'ruby' }
  :escaped
    class CreateBooks < ActiveRecord::Migration
      def change
        enable_extension 'uuid-ossp'
        create_table :books, id: :uuid do |t|
          t.string :name
        end
       end
    end

    Book.create(name: "What's new in Rails 4")
    <Book id: "5c1be234-b2d5-495a-8954-d9de5b0a3637", name: "What's new in Rails 4">

!SLIDE classes="small-bullets" language="haml"

%h1 Cache digests

%div
  %blockquote
    %p
      With this plugin, all calls to #cache in the view will automatically append a digest of that template and all of its dependencies! So you no longer need to manually increment versions in the specific templates you're working on or care about what other templates are depending on the change you make.

%br
%p
  %a{ href: "https://github.com/rails/cache_digests" } github.com/rails/cache_digests

!SLIDE classes="small-bullets" language="haml"

%h1 Russian doll caching

%h2 (nested fragment caching)

%pre{ 'data-language' => 'ruby' }
  :escaped
    class Milestone < ActiveRecord::Base
      has_many :todos
    end

    class Todo < ActiveRecord::Base
      belongs_to :milestone, :touch => true
    end

!SLIDE classes="small-bullets"

# Thread safety

* `config.threadsafe!` enabled by default
* Rails 3 -> 4 upgrade interesting

:joy:

!SLIDE classes="small-bullets"

# Concerns

* `app/concerns` directory exists OOTB
* Some people not amused

:unamused:

[Exploring concerns for Rails 4](http://blog.andywaite.com/2012/12/23/exploring-concerns-for-rails-4/)

!SLIDE classes="small-bullets"

# ActionController::Live

* Utilises Server Sent Events (SSE) for HTML5
* Support for all browsers (except Internet Exploder)
* Needs a compatible app server - Puma, thin, etc
* Great for real-time events, reduce polling

[Using Server Sent Events](https://developer.mozilla.org/en-US/docs/Server-sent_events/Using_server-sent_events)<br/>
[tenderlovemaking.com/2012/07/30/is-it-live.html](http://tenderlovemaking.com/2012/07/30/is-it-live.html)
[railscasts.com/episodes/401-actioncontroller-live](http://railscasts.com/episodes/401-actioncontroller-live)

!SLIDE classes="small-bullets" language="haml"

%h1 ActionController::Live

%pre{ 'data-language' => 'ruby' }
  :escaped
    class MyController < ActionController::Base
      include ActionController::Live

      def index
        response.headers["Content-Type"] = "text/event-stream"
        $redis.psubscribe('messages.*') do |on|
          on.pmessage do |pattern, event, data|
            response.stream.write("event: \#{event}\n")
            response.stream.write("data: \#{data}\n\n")
          end
        end
      end
    end

!SLIDE classes="small-bullets"

# Turbolinks

* Has been around for while
* Built into Rails 4 now
* 'Speeds' up client side by use JavaScript to catch clicks
* Instead of loading entire page (inc. CSS, JS), just update DOM

[railscasts.com/episodes/390-turbolinks](http://railscasts.com/episodes/390-turbolinks)

!SLIDE classes="small-bullets"

# New ActiveRecord scopes

## `.none`

* Negates all previous scopes

!SLIDE classes="small-bullets" language="haml"

%h1 New ActiveRecord scopes

%h2
  %code .none

%pre{ 'data-language' => 'ruby' }
  :escaped
    Post.all.none
    => #<ActiveRecord::Relation []>

!SLIDE classes="small-bullets"

# New ActiveRecord scopes

## `.not`

* Turns an `=` operation into a `!=`

!SLIDE classes="small-bullets" language="haml"

%h1 New ActiveRecord scopes

%h2
  %code .not

%pre{ 'data-language' => 'ruby' }
  :escaped
    Post.where.not(title: 'rails 4')
    => #<ActiveRecord::Relation [#<Post id: 2, title: "rails 3", body: "this is the body", created_at: "2013-08-05 22:46:38", updated_at: "2013-08-05 22:46:38">]>

!SLIDE classes="small-bullets"

# What didn't make it?

* Rails.queue
* Thor as Rake replacement
* Ruby 2.0 minimum

!SLIDE classes="small-bullets"

# What's out?

* Core features extracted into gems
* Deprecated finders

!SLIDE classes="small-bullets"

## Core features extracted into gems

* Active Resource
* Active Record Observers
* Action Pack page and action caching

!SLIDE classes="small-bullets"

# Deprecated finders

* `find_all_by`
* `find_last_by`
* `scoped_by`
* `find_or_initialize_by`
* `find_or_create_by`
* `find_or_create_by`

!SLIDE classes="small-bullets"

# Anything else?

## Over to you!

!SLIDE classes="small-bullets"

# Links

* [Official Rails 4 announcement](http://weblog.rubyonrails.org/2013/6/25/Rails-4-0-final/)
* [Rails 4 release notes](http://edgeguides.rubyonrails.org/4_0_release_notes.html)
* [RailsCasts: What's new in Rails 4](http://railscasts.com/episodes/400-what-s-new-in-rails-4)
* [Rails 4 countdown to 2013](http://blog.remarkablelabs.com/2012/11/rails-4-countdown-to-2013)
* [Digging into Rails 4](http://net.tutsplus.com/tutorials/ruby/digging-into-rails-4/)

!SLIDE classes="small-bullets"

# Thanks

:smile:
