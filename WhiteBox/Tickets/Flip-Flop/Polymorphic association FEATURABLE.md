```rb
class FeatureAssignations < ApplicationRecord 
    belongs_to :feature_assignable, polymorphic: true 
end 

class Wallet < ApplicationRecord 
    has_many :feature_assignations, as: :feature_assignable 
end 


# If in the future we want to use other models as :feature_assignable
class Product < ApplicationRecord 
	has_many :feature_assignations, as: :feature_assignable
end
```




## Why we cannot use `flipflop_features` table?

After reading the documentation we can know:

> When you ask Flipflop if a feature is active (for example, `Flipflop.enabled?(:my_feature))`, it **walks through a list of strategies in order until it finds a strategy that knows how to handle the query**.

```rb
Flipflop.configure do
  # Strategies will be used in the order listed here.
  strategy :cookie
  strategy :active_record # or :sequel, :redis
  strategy :default

  # Basic feature declaration:
  feature :shiny_things

  # Enable features by default:
  feature :world_domination, default: true

  # Group features together:
  group :improved_design do
    feature :improved_navigation
    feature :improved_homepage
  end
end
```

Strategies will be used <span style='color:#eb3b5a'>in the order listed</span> in the `features.rb` file.

Now, we can take a look into the Flipflop's `ActiveRecordStrategy` code:
```rb
module Flipflop
  module Strategies
    class ActiveRecordStrategy < AbstractStrategy
      class << self
        def default_description
          "Stores features in database. Applies to all users."
        end

        def define_feature_class
          return Flipflop::Feature if defined?(Flipflop::Feature)

          model = Class.new(ActiveRecord::Base)
          Flipflop.const_set(:Feature, model)
        end
      end
      # ... #
	 def enabled?(feature)
	     find(feature).first.try(:enabled?)
     end
      ```

As we can see, when Flipflop wants to know the status of a feature, it begins to **iterate over the defined Strategies** and showing which ones respond to the **enabled(feature)** method.

If there is some features in the `flipflop_features` table, they are going to be shown as enabled or disabled in the `ActiveRecordStrategy` column:

![[Pasted image 20230712152646.png]]

![[Pasted image 20230712152702.png]]

this strategy looks into the `Flipflop::Feature` model (`flipflop_features` table) to see the status of the features <span style='color:#eb3b5a'>for this concrete Strategy</span>, meaning:
- There is a record
	- could be enabled
		- ![[Pasted image 20230712154000.png]]
	- could be disabled
		- ![[Pasted image 20230712154008.png]]
- There is not a record
- ![[Pasted image 20230712153947.png]]




