---
sticker: 1f440
---
[TICKET](https://www.notion.so/Spike-WB-Feature-Flag-Architecture-bdf2e860dcf14cbc8b7870dfae70ea1b?pvs=5) 

## What kind of Strategies can bring us Flip-Flop by default?


<table style="width: 100%; border-collapse: collapse"> 
	<tr> 
		<th>Strategy</th> 
		<th><mark style="background: #BBFABBA6;">Pros</mark></th> 
		<th><mark style="background: #FF5582A6;">Cons</mark></th> 
	</tr> 
	<tr style="background-color: rgba(39, 245, 166, 0.04); color: black"> 
		<td>Active Record</td> 
		<td>
			- Native integration with relational databases
			<br>- Data persistence between sessions
		</td> 
		<td>
			- Requires changes to the database structure
			<br>- Can increase code complexity
			<br>- Increased database load
		</td> 
		</tr> 
		<tr  style="background-color: rgba(247, 64, 18, 0.04); color: black"> 
			<td>Redis</td> 
			<td>- Fast and scalable performance
				<br>- Data persistence
				<br>- Integration with other parts of the application
			</td> 
			<td>- Requires additional setup and management of Redis
			<br>- Requires a running Redis instance
			<br>- Increased configuration and management complexity</td> 
		</tr> 
		<tr style="background-color: rgba(247, 245, 18, 0.04); color: black"> 
			<td>Sessions</td> 
			<td>- Does not require changes to the database
				<br>- Easy to implement
			</td> 
			<td>- Not persistent between sessions
				<br>- Not shareable across different devices or browsers
				<br>- Requires additional handling for session assignment
			</td> 
		</tr> 
		<tr style="background-color: rgba(18, 49, 247, 0.04); color: black"> 
			<td>Cookies</td> 
			<td>- Does not require changes to the database
				<br>- Persistence between sessions
				<br>- Easy to implement
			</td> 
			<td>- Limited in size and storage capacity
				<br>- Not shareable across different devices or browsers
				<br>- Users can delete or block cookies, affecting functionality
			</td>
		</tr>
	</table>


# Customizing Feature Access with Flipflop EXAMPLES

Here are some examples of <span style='color:#3867d6'>strategies we could use</span> to enable a feature for a certain percentage of users:

### 1. User ID Strategy

This strategy involves using the user's <span style='color:#8854d0'>ID</span> to determine whether they should have access to a feature. For example, we could enable the feature for all users whose ID is less than a certain value.

```ruby
Flipflop.configure do
  strategy :user_id do |feature|
    if feature == :new_feature && current_user.present?
      current_user.id % 100 < 10  # Enable for 10% of users
    else
      false
    end
  end
  feature :new_feature
end
```

This kind of strategy doesn't seem to be flexible or uniform, but I want to show that is possible to define custom strategies easily.

### 2. Session Strategy

This strategy involves using the user's <span style='color:#8854d0'>session</span> to determine whether they should have access to a feature. We could generate a random number when the user starts a session and store it in the session, then use this number to decide whether to enable the feature.

```ruby
Flipflop.configure do
  strategy :session do |feature|
    if feature == :new_feature && session[:random_number].present?
      session[:random_number] < 10  # Enable for 10% of sessions
    else
      false
    end
  end
  feature :new_feature
end
```

### 3. Custom Strategy

We can also define our own custom strategies. For example, we could define a strategy that enables a feature for a certain percentage of users based on some custom logic.

```rb
class UserPreferenceStrategy < Flipflop::Strategies::AbstractStrategy
  class << self
	attr_accessor :allowed_percentage
	DEFAULT_PERCENTAGE = 10
    def default_description
      "Allows configuration of features to a % of users."
    end
  end
	# ...
	 
	def enabled?(feature) 
		# Can only check features if we have the user's session. 
		return unless request? 
		# Enable the feature received for params to the defined
		# percetage, based on the ID of the user
		percentage = self.class.allowed_percentage || DEFAULT_PERCENTAGE
		(current_user.id % 100) < percentage
	end 
	
	# ...
end 
```

---
---

# <span style='color:#eb3b5a'>How to implement feature access control with default flip flop?</span>

I'm going to show an example in which I have managed to establish control over the users who are shown or not a certain feature.

As I mention before, there are many different strategies to control the user access to features. I have tested one possibility:

1. I have created a model associated with a table to store the id's of the allowed users (naming is not refined as you can see 😅)
```rb
class FeatureUser < ApplicationRecord
end
```

where we have this table associated with:
```rb
create_table "feature_users", force: :cascade do |t|
	t.integer "user_id"
	t.datetime "created_at", null: false
	t.datetime "updated_at", null: false
end
```

Ok... So the idea is to modify the content of the table in order to know which users are allowed to see or use the features.

This can be stored via ActiveRecord or Redis (The logic could be the same IMO)

2. Then, I defined the features in the `features.rb`
```rb
Flipflop.configure do
	# Strategies will be used in the order listed here.
	# Here is my custom Strategy
	strategy UserFeatureStrategy
	
	feature :filtering_bar,
		default: false,
		description: "Filter products"
end
```

3. And create the `UserFeatureStrategy`
```rb
class UserFeatureStrategy < Flipflop::Strategies::AbstractStrategy
	class << self
		def default_description
			"Allows configuration of features per user."
		end
	end
	# ...
	# Some other methods that are going to be explained later
	# ...
	def enabled?(feature)
		request.session[:allowed] 
			||= FeatureUser.exists?(user_id: request.session[:user_id])
	end
end
```

The only method that I need to define is the `enabled?` method. Which is the one that is going to be called whenever the `Flipflop.filtering_bar?` method was called.

The method is setting a `:allowed` key in the user's session with the value of true if the user_id is in the `feature_users` table, and false if not.

To improve a little bit the performance, the query to the database is made only one time per session (using the memoization technique).

4. Now to test (manually) the functionality of our new feature control system, we can hide some 'functionality' anywhere we need (models, controllers, views). In this occasion I'm hiding a beautiful filtering bar from my index page:
```php
# FILTER PRODUCTS BAR
<% if Flipflop.filtering_bar? %>
	<%= form_with url: products_path, method: :get do |form| %>
		<div> 
			# SEARCHBAR CODE \
		</div>
	<% end %>
<% end %>
# END FILTER PRODUCTS BAR
<div class="body">
	# THE REST OF THE INDEX CODE
</div>
```

5. Once we have all this done, we need to specify which percentage of users are going to have access to the new feature. In order to achieve this goal, I make a rake task:
```rb
namespace :features do
	desc "Enable feature for a percentage of users"
	task :enable_for_percentage, [:percentage] => :environment do |_, args|
		percentage = args.percentage.to_f
		if percentage < 0 || percentage > 100
			puts "Percentage must be between 0 and 100."
			return
		end
		total_users = User.count
		num_users_to_enable = (total_users * (percentage / 100.0)).round
		FeatureUser.destroy_all if num_users_to_enable < FeatureUser.count
	
		users_to_enable = User.order(
			created_at: :desc
		).limit(num_users_to_enable)
		
		users_to_enable.each do |user|
			FeatureUser.find_or_create_by(user_id: user.id)
		end
		puts "#{num_users_to_enable} users have been added to the feature_users table."
	end
end
```

Now we can run the following command, and set the percentage we need:

```bash
rake "features:enable_for_percentage[30]"
```

In this case, the percentage was 30. 
![[Pasted image 20230614180513.png]]

![[Pasted image 20230614180437.png]]

6. Having done that, if we open two different browsers with two different users logged in (one who has permission to view the feature and one who doesn't) 
![[Pasted image 20230614180227.png]]

---
---

# <span style='color:#eb3b5a'>How can we modify the code base of the flip-flop gem?</span>

I have tried to change the functionality by adding a new action to the control panel view. For testing purposes I have modified the code of the flip-flop gem that was already bundled into my application.

1. First, we need to know where the gem code is located. To do that, we cam use one of the rails magic commands:
```bash
bundle show --paths
```

No we can see where the code is located:
![[Pasted image 20230614181146.png]]

2. Now, we can start upgrading the functionality, or adapting the functionality to our requirements.
In the `UserFeatureStrategy` Model (inside the main application), I have declared two new methods:
```rb
def percentage_setable?
	true
end

def set_percentage(percentage)
	percentage = percentage.to_f
	total_users = User.count
	num_users_to_enable = (total_users * (percentage / 100.0)).round
	  
	FeatureUser.destroy_all if num_users_to_enable < FeatureUser.count
	  
	users_to_enable = User.order(
		created_at: :desc
	).limit(num_users_to_enable)
	  
	users_to_enable.each do |user|
		FeatureUser.find_or_create_by(user_id: user.id)
	end
end
```

**percentage_setable?** --> it always returns true, indicating that this strategy allows us to set a percentage of users who will have a feature enabled. 

**set_percentage** --> is the method that actually performs this operation. It takes a percentage as an argument, calculates the number of users that should have the feature enabled based on this percentage, and then updates the `FeatureUser` table accordingly.

**To be able to use the new CustomStrategy methods, we need to modify the following files in the gem directory**:
- `app/controllers/flipflop/features_controller.rb`
```rb
def set_percentage_url(strategy, feature)
	set_percentage_feature_strategy_path(feature.key, strategy.key)
end
```

This method **generates the URL** for the set_percentage action of a given strategy. This URL **is used in the view** for the form that allows the user to set the percentage. The **set_percentage_feature_strategy_path** method is an automatically generated path helper method by Rails based on the paths you defined in your paths file.

- `app/controllers/flipflop/strategies_controller.rb`
```rb
def set_percentage
	strategy = FeatureSet.current.strategy(strategy_key)
	strategy.set_percentage(params[:percentage])
	redirect_to features_path
end
```

This action is called when the user submits the form to set the percentage. **Gets the corresponding strategy** (in this case the custom strategy that we have defined), calls the **set_percentage** method on the strategy with the supplied percentage, and then redirects the user back to the features page.

- `config/routes.rb`
```rb
Flipflop::Engine.routes.draw do
	resources :features, path: "", only: [:index] do
		resources :strategies, only: [:update, :destroy] do
			put :set_percentage, on: :member
		end
	end
end
```

Added a new route in the gem routes file to map PUT requests to the **set_percentage** action in the **StrategiesController**. This route includes the **set_percentage** :feature_id and :id as dynamic parameters in the URL, which correspond to the feature key and the strategy key, respectively.

- `app/controllers/flipflop/features/index.html.erb`
```php
<% if strategy.percentage_setable? -%>
	<div class="group">
		<%= form_tag(@feature_set.set_percentage_url(strategy, feature),
			 method: :put) do -%>
		    <%= label_tag :percentage, 'Set percentage:' %>
	        <%= number_field_tag :percentage, nil, in: 0..100, step: 1 %>
	        <%= submit_tag 'Set' %>
	    <% end -%>
    </div>
<% end -%>
```

Finally, we added a new in-view form that allows the user to set the percentage for a strategy. This form is only shown **if the strategy is "percentage setable"**, that is, if the method **percentage_setable?** of the strategy returns true. When the user submits this form, a PUT request is sent to the URL generated by **set_percentage_url**, which is handled by the **set_percentage** action on the **StrategiesController**.

And there it is:
![[Pasted image 20230614183301.png]]

If we click on `Set`, the 80% of the users (the last created at) now are allowed to see the `navbar`.

![[Pasted image 20230614183517.png]]

# Conclusion
It is quite possible to modify the gem to suit our needs. It is necessary to measure the impact on the databases and try to find the proper implementation, but I think we can continue to dive into this as well.
