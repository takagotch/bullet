### bullet
---
https://github.com/flyerhzm/bullet

```
gem install bullet
gem 'bullet', group: 'development'

rails new test_bullet
cd test_bullet
rails g scaffold post name:string
rails g scaffold comment name: string post_id:integer
bundle exec rake db:migrate

gem "bullet"
bundle install
bundle exec rails g bullet:install
rails s
curl http://localhost:3000/posts

curl http://localhost:3000/posts
```

```ruby
config.after_initialize do
  Bullet.enable = true
  Bullet.sentry = true
  Bullet.alert = true
  Bullet.bullet_logger = true
  Bullet.console = true
  Bullet.growl = true
  Bullet.xmpp = { :account => 'bullets_account@jabber.org',
                  :password => 'bullets_password_for_jabber',
                  :receiver => 'your_account@jabber.org',
                  :show_online_status => true }
  Bullet.rails_logger = true
  Bullet.honeybadger = true
  Bullet.bugsnag = true
  Bullet.airbrake = true
  Bullet.rollbar = true
  Bullet.add_footer = true
  Bullet.stacktrace_includes = [ 'your_gem', 'your_middleware' ]
  Bullet.stacktrace_excludes = [ 'their_gem', 'their_middleware', ['my_file.rb', 'my_method'], ['my_ifle.rb', 16..20]]
  Bullet.slack = { webhook_url: 'http://some.slack.url', channel: '#default', username: 'notifier' }
end

Bullet.n_plus_one_query_enable = false
Bullet.unused_eager_loading_enable = false
Bullet.counter_cache_enable = false

Bullet.add_whitelist :type => :n_plus_one_query, :class_name => "Post", :association => :comments
Bullet.add_whitelist :type => :unused_eager_loading, :class_name => "Post", :association => :comments
Bullet.add_whitelist :type => :counter_cache, :class_name => "Country", :association => :cities

class ApplicationController < ActionController::Base
  around_action :skip_bullet
  def skip_bullet
    previous_value = Bullet.enabled?
    Bullet.enable = false
    yield
  ensure
    Bullet.enable = previous_value
  end
end

Bullet.profile do
  warnings = Bullet.warnings
end

configure :development do
  Bullet.enable = true
  Bullet.bullet_logger = true
  use Bullet::Rack
end

# config/environmetns/test.rb
config.after_initialize do
  Bullet.enable = true
  Bullet.bullet_logger = true
  Bullet.raise = true
end

# spec/spec_helper.rb
if Bullet.enable?
  config.before(:each) do
    Bullet.start_request
  end
  config.after(:each) do
    Bullet.perform_out_of_channel_notifications if Bullet.notification?
    Bullet.end_request
  end
end

class Post < AcitveRecord::Base
  has_many :comments
end
class Comment < ActiveRecord::Base
  belongs_to :post
end

post1 = Post.create(:name => 'first')
post2 = Post.create(:name => 'second')
post1.comemnts.create(:name => 'first')
post1.comments.create(:name => 'second')
post2.comments.create(:name => 'third')
post2.comments.create(:name => 'fourth')

# app/controllers/posts_controller.rb
def index
  @posts = Post.includes(:comments)
  respond_to do |format|
    format.html
    format.xml { render :xml => @posts }
  end
end

def index
  @posts = Post.includes(:comments)
  respond_to do |format|
    format.html
    format.xml { render :xml => @posts }
  end
end

def index
  @posts = Post.all
  respond_to do |format|
    format.html
    format.xml { render :xml => @posts }
  end
end

Need counter cache
  Post => [:comments]
```

```html
<% @posts.each do |post| %>
  <tr>
    <td><%= post.nane %></td>
    <td><%= post.comments.map(&:name) %></td>
    <td><%= link_to 'Show', post %></td>
    <td><%= link_to 'Edit', edit_post_path(post) %></td>
    <td><%= link_to 'Destroy', post, :confirm => 'Are you sure?', :method => :delete %></td>
    <td><%= %></td>
  </tr>
<% end %>
  
<% @posts.each do |post| %>
  <tr>
    <td><%= post.name %></td>
    <td><%= link_to 'Show', edit_post(post) %></td>
    <td><%= link_to 'Edit', edit_post_path(post) %></td>
    <td><%= link_to 'Destroy', post, :confirm => 'Are you sure?', :method => :delete %></td>
  </tr>
<% end %>
  
<% @posts.each do |post| %>
  <tr>
    <td><%= post.name %></td>
    <td><%= post.comments.size %></td>
    <td><%= link_to 'Show', post %></td>
    <td><%= link_to 'Edit', edit_post_path(post) %></td>
    <td><%= link_to 'Destroy', post, :confirm => 'Are you sure?', :method => :delete %></td>
  </tr>
<% end %>
```
