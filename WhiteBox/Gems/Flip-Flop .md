---
sticker: 23ef-fe0f
---
Flipflop allows us to define strategies for <span style='color:#20bf6b'>enabling</span> or <span style='color:#eb3b5a'>disabling</span> features. **A strategy is a piece of code that decides whether a feature should be enabled or disabled for a particular request**. Flipflop comes with several built-in strategies, and we can also define **our own custom strategies**.

Characteristics:

- ## Simple configuration: 
	- configured in a single file `config/features.rb`. This is where all the features and strategies are declared. Simple example:
```rb
Flipflop.configure do
  strategy :cookie
  strategy :active_record
  feature :world_domination, default: true
end
```

- ## Simple API to check if a feature is enabled:
```rb
Flipflop.enabled?(:some_feature)  
Flipflop.some_feature?            
```
This methods are **globally available** in <span style='color:#3867d6'>views</span>, <span style='color:#3867d6'>controllers</span> and <span style='color:#3867d6'>models</span>

- ## Dashboard: 
	- It comes with a built-in dashboard that allows reviewing and changing the status of all features in real time.
![[Pasted image 20230612105947.png]]

- ## Feature management through the console (using rake tasks): 
	- It provides rake tasks that can be used to <span style='color:#20bf6b'>enable</span>, <span style='color:#fa8231'>disable</span> or <span style='color:#eb3b5a'>delete</span> features from the command line. 
	- Example of use:
```bash
rake flipflop:turn_on[some_feature]
```

- ## Thread safety : 
	- Flipflop is [[thread safe]], which means you can use it in multithreaded applications without worrying about race conditions.
	- **Thread safety** is important because **Rails is a multi-threaded environment**, which means that there can be multiple threads running simultaneously. Each thread may be checking or changing the state of the Flipflop features. 
	  If Flipflop were not thread-safe, you could end up with <span style='color:#eb3b5a'>race conditions</span>, where **the outcome depends on the relative order in which the threads execute their code**. 

- ## Better database performance:
	- It caches feature configurations per request, which means it only makes one database query per request, regardless of how many times you check a feature. It is cached on each feature toggle.

- ## Features status can be stored on different databases/strategies:
```rb
Flipflop.configure do 
	# Different strategies
	strategy :cookie
	strategy :active_record
	strategy :redis
	strategy :session
	strategy :query_string
	strategy :default 
end
```

> - <mark style='background:#ABF7F7A6'>cookie</mark>:
> 	- This strategy stores feature settings in browser cookies for the current user. This can be useful to be specific to each user and persist between browser sessions.
> 	- Options:
> 			- `prefix`: Allows to set a string prefix for all cookie names. This can be useful if you want <span style='color:#8854d0'>to avoid conflicts with other cookies</span> that may have the same names. By default, there is no prefix.
> 			- `path`: Allows to set the path for which cookies are applied. **By default, it is applied to the root of the application**. This means that cookies <span style='color:#8854d0'>will be accessible on all pages of your application</span>. If you only want cookies to be accessible in a certain part of your application, you can set this option to the path of that part of your application.
> 			- `domain`: Allows to set the cookie domain. **By default, it is null**, which means that cookies will only be accessible on the exact domain that was used to set them. If you want cookies to be accessible on all subdomains of your domain, you can set this option to <span style='color:#8854d0'>:all</span>. You can also set this option to an array of domains if you want cookies to be accessible on multiple domains.
> 			- `secure`: Allows to set whether cookies should only be set if the connection is secured with TLS (Transport Layer Security). **By default, it is false**, which means that <span style='color:#8854d0'>cookies will be set regardless of whether the connection is secured or not</span>. If you set this option to true, cookies will only be set if the connection is secured. This can be useful if you are handling sensitive information and want to ensure that cookies are not transmitted over unsecured connections.
> 			- `httponly`: **Allows to set whether cookies are accessible only via HTTP or also via scripting**. **By default, it is false**, which means that <span style='color:#8854d0'>cookies are accessible both through HTTP and through scripting</span>. If you set this option to true, cookies will only be accessible via HTTP and not via scripting. This can help protect against certain types of cross-site scripting (XSS) attacks.
> - <mark style='background:#ABF7F7A6'>active_record/:sequel</mark>: 
> 	- These are the strategies that tell Flipflop to save feature settings to the database using Active Record or Sequel.
> 	- Options:
> 			- `class`: Allows you to provide the feature model to use. **By default, Flipflop uses Flipflop::Feature**, which is defined automatically and <span style='color:#8854d0'>uses the flipflop_features table in your database</span>. This model represents the feature configurations in the database.
> 			- It is possible to use a custom model to render feature configurations. For example, if you have an Active Record model named `MyFeature` that uses the `my_features` table in the database, you could configure the Active Record strategy as follows:
> 			![[Pasted image 20230612113951.png]]
>- <mark style='background:#ABF7F7A6'>query_string</mark>: 
>	- Allows Flipflop to interpret the query string parameters in the request URL as features. This can be useful if we want to change the behavior of the app based on parameters in the URL.
>	  For example, we could have a URL like http://myapp.com?feature_new_ui=true **that would enable the new_ui feature**. When Flipflop sees this URL, it will interpret the feature_new_ui parameter in the query string as a feature and set its state to true.
>	  It is important to note that this strategy is only used to resolve the state of features. It does not allow to change the state of the characteristics. 
>	  - Options:
>			  - `prefix`: Allows to set a string prefix for all query string parameters. This can be useful <span style='color:#8854d0'>if we want to avoid conflicts with other parameters in the query string</span> that may have the same names. **By default, there is no prefix**
>			    For example, if you set :prefix to <span style='color:#20bf6b'>"feature_"</span>, then Flipflop will only interpret query string parameters beginning with "feature_" as features. So if you have a URL like http://myapp.com?feature_new_ui=true&other_param=false, Flipflop will interpret feature_new_ui as a feature and ignore other_param.
>- <mark style='background:#ABF7F7A6'>redis</mark>:
>	- Save feature settings to Redis.
>	- Options:
>			- `client`: This option allows you to use the specified Redis.				![[Pasted image 20230612115202.png]]- `prefix`: Allows to set a string prefix for all Redis keys. This can be useful to avoid conflicts with other keys in Redis that may have the same names. By default, there is no prefix.
>- <mark style='background:#ABF7F7A6'>session</mark>:
>	- Save feature settings in the current user's application session.
>		- Options:
>				- `:prefix` : String prefix for all session variables. Defaults to no prefix.





## Architecture Planning Outcome 

* **What should the persisting strategy be?** 
	* The persistence strategy we choose depends on the specific needs. For example, if we want feature configurations to persist between different sessions and users, we could use the Active Record or Redis strategy. If we only need persistence at the session or user level, you could use the cookie or session strategy.

* **How can we fork Flipflop into a Whitebox organization?**
	* ??? GitHub Fork inside the gem

* **Can we add customization per user or percentage of users to enable/disable the feature?**
	* Flipflop does not support this natively, but we can add this functionality by creating a <span style='color:#3867d6'>custom strategy</span>.
	* I have adapted the example from the documentation about how to define a custom strategy class, where I defined an example about how the allowing some percentage requirement can be implemented (the first idea that came to my mind, a little tricky 😅)
	* I have overwritten the `enabled?` method to return true if the user ID is less than the defined percentage, and false otherwise.:
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

* Structure this around a Rails engine.



Similar GEM [Flipper](https://github.com/jnunemaker/flipper)

