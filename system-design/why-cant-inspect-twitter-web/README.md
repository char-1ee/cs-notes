# Why can't inspect Twitter web?

#### Is it technically possible to grab the payload returned from the twitter website with F12?

The F12 inspect mode cannot directly grab the payload returned from the Twitter website.

Under F12 mode, the "Network" tab will display a list of all the requests made by the webpage. Look for requests that have a URL starting with an API endpoint, such as "api.twitter.com" for Twitter's API. These requests are typically labeled as "XHR" (XMLHttpRequest) or "Fetch" in the "Type" column.

It's important to note that not all API requests may be visible in the F12 inspect mode. Some APIs may be called from within JavaScript code and may not be directly visible in the network requests. Additionally, websites may use techniques like obfuscation or encryption to hide API endpoints or data.

If you're specifically interested in exploring and interacting with the APIs provided by a website, it's recommended to refer to the website's official API documentation, if available. The documentation will provide detailed information about the supported endpoints, authentication methods, request parameters, and response formats, allowing you to use the APIs in a more controlled and reliable manner.

#### I saw an API called _https://api.twitter.com/live\_pipeline/events_, which seems I cannot find the response tab in inspect mode but a tab called EventStream, what is that?

1. The "EventStream" tab in the F12 inspect mode is related to Server-Sent Events (SSE) or EventSource API, refer to [this](server-sent-events-sse.md).
2. SSE allows a web page to receive real-time updates or events from a server over a persistent HTTP connection.
3. The Twitter API endpoint "https://api.twitter.com/live\_pipeline/events" likely utilizes SSE to provide real-time updates.
4. The EventStream tab in F12 inspect mode shows a continuous stream of events received from the server.
5. Each event contains a type and data payload, indicating the event type and associated information.
