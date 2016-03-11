While much of Spring4gwt's power lies in dependency injection, it may be also useful to simply use the provided servlet to expose Spring services to your client while still looking up those services directly using GWT.create(). This is a good first step for those new to GWT RPC and may be sufficient in itself for projects with relatively few services or when another client-side framework like Google Gin is being used to manage dependencies. The following steps show how to accomplish this for a simple greeting service. Full source code and a working war for for this example is contained in the examples/simplerpc project.

<ol>
<li>Add the spring4gwt jar to your server (war) classpath. Note that for simple RPC without DI it is not needed in the client classpath.</li>
<li>Edit your server's web.xml (which should already contain a Spring context listener) to also contain the following servlet:<br>
<pre><code>&lt;!-- SpringGwt remote service servlet --&gt;<br>
&lt;servlet&gt;<br>
	&lt;servlet-name&gt;springGwtRemoteServiceServlet&lt;/servlet-name&gt;<br>
	&lt;servlet-class&gt;org.spring4gwt.server.SpringGwtRemoteServiceServlet&lt;/servlet-class&gt;<br>
&lt;/servlet&gt;<br>
&lt;servlet-mapping&gt;<br>
	&lt;servlet-name&gt;springGwtRemoteServiceServlet&lt;/servlet-name&gt;<br>
	&lt;url-pattern&gt;/springGwtServices/*&lt;/url-pattern&gt;<br>
&lt;/servlet-mapping&gt;<br>
</code></pre>
Note that this is a single servlet that handles all requests matching the URL /springGwtServices/. Individual services are automatically exposed within this path, e.g. /springGwtServices/greetingService, but do not per-service mappings defined in web.xml.<br>
</li>

<li>Identify the Spring service(s) you wish to access from your GWT client. For this example, we assume a service like the following.<br>
<pre><code>@Service("greetingService")<br>
public class GreetingServiceImpl implements GreetingService {<br>
<br>
	public String greet(String input) {<br>
		return "Hello from the server, " + input + "!";<br>
	}<br>
}<br>
</code></pre>

Note that the service is a "pure" Spring service and does not need to extend RemoteServlet or contain any other coupling to GWT. This is the primary difference between using spring4gwt instead of exposing Spring services as servlets directly. The only requirement for spring4gwt is that the service has a meaningful name since all client-side lookups are performed by name.<br>
</li>

<li>For each service, create an interface containing the remote methods (or optionally, move an existing interface to a location that allows it to be shared by the client classpath) that extends RemooteService. Also annotate it with the @RemoteServiceRelativePath annotation using /springGwtServices/<bean name> as the path.<br>
<pre><code>@RemoteServiceRelativePath("springGwtServices/greetingService")<br>
public interface GreetingService extends RemoteService {<br>
<br>
	String greet(String name);<br>
}<br>
</code></pre>
</li>

<li>For each service, also create an async counterpart that is accessible within the client classpath. See the GWT documentation for more on the relationship between primary and async interfaces. Essentially they provide a mechanism for calling the proxied service asynchronously with a callback on success or failure.<br>
<pre><code>public interface GreetingServiceAsync {<br>
<br>
	void greet(String input, AsyncCallback&lt;String&gt; callback);<br>
}<br>
</code></pre>
</li>

<li>In client code, access the service using GWT.create() and invoke methods.<br>
<pre><code>private final GreetingServiceAsync greetingService = GWT.create(GreetingService.class);<br>
<br>
...<br>
<br>
greetingService.greet(nameField.getText(), new AsyncCallback&lt;String&gt;() {<br>
<br>
	public void onFailure(Throwable caught) {<br>
		...<br>
	}<br>
<br>
	public void onSuccess(String result) {<br>
		...<br>
	}<br>
});<br>
</code></pre>
</li>
</ol>
When a method on the service is called, the proxy created by GWT will access the URL gwtSpringServices/greetingService which is handled by the springGwtRemoteServiceServlet. The servlet will perform a bean lookup by name in the current web application context for "greetingService" and then invoke the appropriate method, also returning the result. An IllegalArgumentException will be thrown if the bean does not exist or does not implement the RemoteService interface. The later is a safety check to prevent accessing any bean in the server's Spring context from the client since typically only a subset should be exposed. Message serialization is not affected by sprig4gwt so refer to the GWT documentation for additional information on RPC type constraints and serialization errors that may occur.