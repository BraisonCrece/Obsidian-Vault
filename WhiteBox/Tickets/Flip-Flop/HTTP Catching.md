---
sticker: 1f310
---
<span style='color:#eb3b5a'>it might not be part of the essential complexity and might involve accidental complexity.
The best thing would be, in my opinion, to be able to test the impact of the default strategy (Active Record / Redis + Sessions), before exploring other areas of performance improvement.</span>

# HTTP Caching and Feature Flag Management

## 1. What is HTTP Caching?

What basically HTTP caching does is remember information and reuse it the next time it needs it. That makes things go faster.

But how does the browser know what information to remember and for how long? Through HTTP headers. Here are some of the most important ones:

`Cache-Control`: It tells the browser how long it can remember the information (max-age), if it can share it with other browsers (public) or if it should keep it only for itself (private), and if it needs to ask the server for permission before reusing the information (must-revalidate).

`Expires`: Tells the browser when the information has become old and needs to be updated. It is not as flexible as Cache-Control, but it does the job.

`Last-Modified` and `ETag`: They help the browser and server find out if the information has changed since the last time it was cached. If the information has not changed, the server tells the browser that it can reuse the information in the cache with a 304 (Not Modified) status. The logic in the server side must be design and implemented.


## 2. Incorporating HTTP Caching in Feature Flag Management

In the context of feature flag management, HTTP caching can be used to store and quickly retrieve information about which features are enabled for a particular user. This can reduce the number of database queries and improve the performance of the application.

Here's a brief outline of how this could be implemented:

**1. Generate an HTTP response with feature access information:**
**2. Set HTTP response headers for caching:**
**3. Use browser cache to check feature access:**
**4. Implement cache validation:**


![[HTTP_catching_featureFlag_diagram 1.svg]]

> *In this flow, when the user requests a page (e.g. index.html), the server queries the database for access to the user's features. Depending on whether the user has access to certain features, the server generates the appropriate HTML (e.g., including or excluding the product filter bar) and returns it in the HTTP response. This response also includes Cache-Control headers that instruct the browser to store the feature access information in the HTTP cache.
> 
> When the user requests the same page at a later time, the browser sends another HTTP request to the server. If the information in the cache has expired, the server queries the database again and generates a new HTTP response with the appropriate HTML. Otherwise, the server can generate an HTTP response from the cached information.*


## HTTP CATCHING VS SESSION STORAGE OR COOKIES

Link of interest, [HOW RAILS MANAGE SESSIONS](https://www.akshaykhot.com/rails-sessions/) 


<table>
	<tr>
		<th>Feature</th>
		<th >HTTP Caching</th>
		<th >Session Storage/Cookies</th>
	</tr>
	<tr>
		<td>Control</td>
		<td style="background-color: #FFFBD6">
			Handled automatically by the browser and the server.
		</td>
		<td style="background-color: #FFFBD6">
			Handled automatically by the browser and the server.
		</td>
	</tr>
	<tr>
		<td>Visibility</td>
		<td style="background-color: #ccffcc">
			Invisible to JavaScript code. The browser decides when to use the cached data.
		</td>
		<td style="background-color: #ffcccc ">
			Visible to JavaScript code. Scripts can directly manipulate the stored
			data. 😰
		</td>
	</tr>
	<tr>
		<td>Scope</td>
		<td style="background-color: #ccffcc">
			Shared between all tabs and windows of the same browser.
		</td>
		<td style="background-color: #ffcccc">
			Specific to each tab or window. Data is not shared between different tabs
			or windows. (Not in Rails that uses Cookies, but ok)
		</td>
	</tr>
	<tr>
		<td>Persistence</td>
		<td style="background-color: #ccffcc">
			Can persist between different browsing sessions, depending on cache
			configuration.
		</td>
		<td style="background-color: #ffcccc">
			Does not persist between browsing sessions. Data is deleted when the tab
			or window is closed. (But again, in Rails is different)
		</td>
	</tr>
	<tr>
		<td>Implementation Complexity</td>
		<td style="background-color: #ffcccc">
			Can be complex to implement correctly due to the need to manage cache
			headers and understand caching behavior.
		</td>
		<td style="background-color: #ccffcc">Generally simpler to implement.</td>
	</tr>
</table>




