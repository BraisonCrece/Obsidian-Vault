Having had some research on the topic, this is the current state of the spike:

OUR OBJECTIVES:
- Extending feature flagging functionality (Flipflop)
	- Create a Rails Engine and extend the flipflop controller:
		- Yes this is possible:
			- I have created a custom engine which has flipflop as a dependency on the gemspec
			- When I add the engine to one application, I can access to the features dashboard
			- Is possible to extend the functionality of flipflop in the engine, but it requires to do a lot of things (and maintain it):
				- Override flipflop controllers 
				- Override flipflop routes (kind of a middle ware)
				- Create our custom methods
				- Create our custom control panel views
				- Configure the engine to be able to use custom feature flagging
				- Configure how the engine works with flipflop
				- ...
				  
	- Define custom Strategies using flipflop (as a dependency inside the gemfile):
		- This approach is more convenient in terms of complexity
		- Build our custom strategies and use the default implementation of flipflop gives us a lot of flexibility
		- You can define your custom strategy that inherit from `Flipflop::Strategies::AbstractStrategy` and use their api to use their api in conjunction with the logic defined in the custom strategy to achieve almost any goal in terms of feature release control.
		  Take a look into the document I've created.


	  
 
	  
STEPS:
- Document current state of investigation
- Dive deeper into the default implementation of flipflop and try to test possibilities by creating custom Strategies





Having previously researched on the suggested topics, and in order to achieve a better understanding of flipflop and feature flagging, I have implemented some techniques using the flipflop gem:

- ### Expanding or modifying default flipflop functionality by defining custom strategies.
        - In this document, I show how I have designed these changes to implement the functionality.
         - You can see that it is possible, and gives us <span style='color:#eb3b5a'>some</span> flexibility
         - It is not proven that all of our requirements will fit  
           
- ### Modifying the gem code to add functionality (allowing n number of users access to a feature) and modifying them via UI.
         - You can see that it is possible and gives us a lot of flexibility.
         - The couple with the original gem can be deleted (updates wont affect)
           
           
- ### Creating a custom rails engine that uses flipflop as a dependency and try to expand its functionality.
        - I have managed to use flipflop through a custom engine, <span style='color:#eb3b5a'>BUT</span> I have not modified or expanded its functionality, since this requires a greater effort and more time.
        - It seems that is possible to do it, just as we can modify the behavior of another rails engine like Devise, overwriting its controllers, models, views, etc...

At first glance, all options are valid, but they have pros and cons.

#### PROS / CONS

| **Option** | **Pros** | **Cons** |
| --- | --- | --- |
| **Expanding/modifying default flipflop functionality by defining custom strategies** | - Keeps the original gem code unmodified, which makes updates easier. <br> - Allows for customization without touching the gem's internal code. <br> - <span style='color:#20bf6b'>Less complex than creating a custom engine</span>. | - <span style='color:#eb3b5a'>Might not be flexible enough for all custom needs</span>. <br> - Some functionalities might require changes in the gem's internal code. |
| **Modifying the gem code to add functionality** | - Full control over the gem's code and functionality. <br> - <span style='color:#20bf6b'>Allows adding any required functionality</span>. | - <span style='color:#eb3b5a'>Makes updates more challenging</span>, as changes might conflict with new versions of the gem. <br> - Requires deeper understanding of the gem. <br> - Responsibility of maintaining and debugging the code falls on us. |
| **Creating a custom Rails engine that uses flipflop as a dependency** | - Provides an abstraction layer between the main application and flipflop functionality, <span style='color:#20bf6b'>allowing changes without directly affecting the application</span>. <br> - <span style='color:#20bf6b'>Allows sharing the custom functionality across multiple applications</span>. | - <span style='color:#eb3b5a'>Requires a greater development effort</span>. <br> - Can introduce <span style='color:#eb3b5a'>additional complexity</span> in managing dependencies and configuration. |

