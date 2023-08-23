---
sticker: emoji//1f4a1
---
- <span style='color:#eb3b5a'>Get rid</span> of default Flipflop Strategies
- Only use <span style='color:#3867d6'>custom strategies</span>

- Extend the definition of <span style='color:#fa8231'>FlipflopAbstractStrategy</span> adding (at least) one more method
- The strategy is going to tell the status of a feature **depending on tho methods**:

	- **is_active?** ( possible states --> ON / OF )
	- **enabled?(feature)** is the method that flipflop uses.
		- **feature_assignation_status(feature, assignable)** ( return true or false depending on the logic defined.
		  
```rb
	def enabled?(feature, assignable: nil)
		# we can disable the feature globally if we want
		return false unless is_active?
		# feature_assignation_status returns true/false 
		feature_assignation_status(feature, assignable)
	end
```

![[Feature Flagging.jpg]]