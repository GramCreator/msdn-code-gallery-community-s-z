# SOLID Web Parts with SharePoint Framework
## License
- MIT
## Technologies
- Sharepoint Online
## Topics
- Sharepoint Online
- Sharepoint Web Parts
- SOLID Principles
## Updated
- 03/13/2018
## Description

<p>The principles of SOLID are guidelines that can be applied to software development to improve legibility and maintainability of software applications. This article explores best practices and design patterns for developing SharePoint Web Parts in TypeScript,
 using the new client-side SharePoint Framework, and improving compliance to the five SOLID principles.</p>
<h1><br>
Being SOLID</h1>
<p>The term SOLID is a mnemonic acronym for five design principles intended to make software designs more understandable, flexible and maintainable:</p>
<p>&nbsp;</p>
<ol>
<li><strong>Single responsibility</strong>: An object should have only a single responsibility.
</li><li><strong>Open/closed</strong>: An object should be open for extension but closed for modification.
</li><li><strong>Liskov substitution</strong>: An object should be replaceable with instances of its subtype without altering the correct functioning of the software.
</li><li><strong>Interface segregation</strong>: When different behaviours are expected in an object, the object should implement many specific interfaces rather than one general-purpose interface.
</li><li><strong>Dependency inversion</strong>: An object should not have direct dependency on other concrete objects, but only on abstractions.
</li></ol>
<p>&nbsp;</p>
<p><br>
Typically, these software design principles are applied to large software development projects in order to guarantee a higher level of maintainability of the software, and loose object coupling, which encourages a microservice-oriented architecture. But nothing
 prevents us from being SOLID also in relatively smaller applications such as SharePoint web parts.</p>
<h1><br>
A not so SOLID web part</h1>
<p>By following the clearly described <a href="https://docs.microsoft.com/en-us/sharepoint/dev/spfx/web-parts/get-started/build-a-hello-world-web-part" target="_blank">
steps for building your first SharePoint client-side web part</a>, we can easily create a web part with the SharePoint Framework in a few minutes. However, let me tell you, this is just a very simple web part, far from being SOLID-compliant. Let&rsquo;s have
 a look at what the pitfalls are, or &ldquo;code smells&rdquo;, of such simple web part.</p>
<p>Let&rsquo;s assume we want to build a web part that shows some famous quotes, stored in a SharePoint custom list called &ldquo;Quotes&rdquo;, which contains a field for the Author and a field for the Quote itself. The first step, is to create a new folder
 and the run the Yeoman SharePoint Generator. Yeoman will install the required dependencies and it will scaffold the solution files along with the web part. We call this web part &ldquo;MyQuotes&rdquo; and we choose not to use any JavaScript framework for the
 purpose.</p>
<p><img id="192480" src="192480-fig1.png" alt="" width="952" height="318"></p>
<p><br>
A few minutes later, Yeoman is done. We can open the solution with VS Code (or any other TypeScript editor) and add the code for reading the quotes from a SharePoint list.</p>
<p>The <strong>MyQuotesWebPart.ts</strong> file in the src\webparts\myQuotes folder defines the main entry point for the web part. The
<strong>MyQuotesWebPart</strong> class extends the <strong>BaseClientSideWebPart</strong>. Any client-side web part should extend the BaseClientSideWebPart class to be defined as a valid web part, and this base class defines the minimal functionality that is
 required to build a web part with SPFx.</p>
<p>The <strong>render()</strong> method is used to render the web part inside a page, as part of its DOM (and not as an iframe, as it happens for traditional SharePoint add-ins). Here, in the render method, is where we want to make the necessary changes to
 display our famous quotes. We start by changing the title of the web part to &ldquo;Famous Quotes&rdquo;, and then adding a container with id = &ldquo;quotesContainer&rdquo; to use to fill with data coming from a SharePoint list. This &lt;div&gt; is populated
 by the method <strong>renderData()</strong>, invoked immediately after.</p>
<p>&nbsp;</p>
<div class="scriptcode">
<div class="pluginEditHolder" pluginCommand="mceScriptCode">
<div class="title"><span>JavaScript</span></div>
<div class="pluginLinkHolder"><span class="pluginEditHolderLink">Edit</span>|<span class="pluginRemoveHolderLink">Remove</span></div>
<span class="hidden">js</span>

<div class="preview">
<pre class="js">public&nbsp;render():&nbsp;<span class="js__operator">void</span>&nbsp;<span class="js__brace">{</span>&nbsp;
&nbsp;&nbsp;<span class="js__operator">this</span>.domElement.innerHTML&nbsp;=&nbsp;`&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&lt;div&nbsp;class=<span class="js__string">&quot;${styles.myQuotes}&quot;</span>&gt;&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&lt;div&nbsp;class=<span class="js__string">&quot;${styles.container}&quot;</span>&gt;&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&lt;div&nbsp;class=<span class="js__string">&quot;ms-Grid-row&nbsp;ms-bgColor-themeDark&nbsp;ms-fontColor-white&nbsp;${styles.row}&quot;</span>&gt;&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&lt;div&nbsp;class=<span class="js__string">&quot;ms-Grid-col&nbsp;ms-lg10&nbsp;ms-xl8&nbsp;ms-xlPush2&nbsp;ms-lgPush1&quot;</span>&gt;&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&lt;span&nbsp;class=<span class="js__string">&quot;ms-font-xl&nbsp;ms-fontColor-white&quot;</span>&gt;Famous&nbsp;Quotes&lt;/span&gt;&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&lt;div&nbsp;class=<span class="js__string">&quot;ms-font-l&nbsp;ms-fontColor-white&quot;</span>&nbsp;id=<span class="js__string">&quot;quotesContainer&quot;</span>&gt;&lt;/div&gt;&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&lt;/div&gt;&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&lt;/div&gt;&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&lt;/div&gt;&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&lt;/div&gt;`;&nbsp;
&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;<span class="js__operator">this</span>.renderData();&nbsp;
<span class="js__brace">}</span>&nbsp;
</pre>
</div>
</div>
</div>
<div class="endscriptcode"><em>(The code snippet is in TypeScript)</em></div>
<p>&nbsp;</p>
<h1>Local and live environments</h1>
<p><strong>SharePoint Workbench</strong> gives us the flexibility to test web parts in our local environment and from a SharePoint site. SharePoint Framework aids this capability by helping us understand which environment our web part is running from by using
 the <strong>Environment </strong>class. This is what renderData() does to differentiate between a local environment and an online environment. Depending on the environment type, two different methods are invoked:</p>
<ul>
<li><strong>getRealData()</strong> to retrieve list items from the SharePoint &ldquo;Quotes&rdquo; list using the helper class
<strong>spHttpClient </strong>to execute REST API requests against SharePoint. </li><li><strong>getMockData()</strong> to return sample quotes when testing our web part locally.
</li></ul>
<p>A third private method, <strong>renderQuotes()</strong> generates the HTML code for rendering a list of quotes. This is irrespective on the environment type, as data, either mock or real, is obtained before invoking this method.</p>
<p>&nbsp;</p>
<div class="scriptcode">
<div class="pluginEditHolder" pluginCommand="mceScriptCode">
<div class="title"><span>JavaScript</span></div>
<div class="pluginLinkHolder"><span class="pluginEditHolderLink">Edit</span>|<span class="pluginRemoveHolderLink">Remove</span></div>
<span class="hidden">js</span>

<div class="preview">
<pre class="js">private&nbsp;renderData():&nbsp;<span class="js__operator">void</span>&nbsp;<span class="js__brace">{</span>&nbsp;
&nbsp;&nbsp;<span class="js__statement">if</span>&nbsp;(Environment.type&nbsp;===&nbsp;EnvironmentType.SharePoint&nbsp;||&nbsp;Environment.type&nbsp;===&nbsp;EnvironmentType.ClassicSharePoint)&nbsp;<span class="js__brace">{</span>&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;<span class="js__operator">this</span>.getRealData().then((response)&nbsp;=&gt;&nbsp;<span class="js__brace">{</span>&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="js__operator">this</span>.renderQuotes(response.value);&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;<span class="js__brace">}</span>);&nbsp;
&nbsp;&nbsp;<span class="js__brace">}</span>&nbsp;
&nbsp;&nbsp;<span class="js__statement">else</span>&nbsp;<span class="js__statement">if</span>&nbsp;(Environment.type&nbsp;===&nbsp;EnvironmentType.Local)&nbsp;<span class="js__brace">{</span>&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;<span class="js__operator">this</span>.getMockData().then((response)&nbsp;=&gt;&nbsp;<span class="js__brace">{</span>&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="js__operator">this</span>.renderQuotes(response.value);&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;<span class="js__brace">}</span>);&nbsp;
&nbsp;&nbsp;<span class="js__brace">}</span>&nbsp;
<span class="js__brace">}</span></pre>
</div>
</div>
</div>
<div class="endscriptcode"><em>(The code snippet is in TypeScript)</em></div>
<p>&nbsp;</p>
<p>&nbsp;</p>
<h1>Mocking data</h1>
<p>We need to define an interface that models the SharePoint list items that represent a quote. We actually need two interfaces:</p>
<ul>
<li><strong>IQuote</strong>, which defines the <strong>Author </strong>and <strong>
Quote </strong>fields that we want to display in the web part. This represents a single quote.
</li><li><strong>IQuotes</strong>, which represents a collection of quotes. </li></ul>
<p>&nbsp;</p>
<p>&nbsp;</p>
<div class="scriptcode">
<div class="pluginEditHolder" pluginCommand="mceScriptCode">
<div class="title"><span>JavaScript</span></div>
<div class="pluginLinkHolder"><span class="pluginEditHolderLink">Edit</span>|<span class="pluginRemoveHolderLink">Remove</span></div>
<span class="hidden">js</span>

<div class="preview">
<pre class="js">export&nbsp;interface&nbsp;IQuotes&nbsp;<span class="js__brace">{</span>&nbsp;
&nbsp;&nbsp;Quotes:&nbsp;IQuote[];&nbsp;
<span class="js__brace">}</span>&nbsp;
&nbsp;
export&nbsp;interface&nbsp;IQuote&nbsp;<span class="js__brace">{</span>&nbsp;
&nbsp;&nbsp;Author:&nbsp;string;&nbsp;
&nbsp;&nbsp;Quote:&nbsp;string;&nbsp;
<span class="js__brace">}</span></pre>
</div>
</div>
</div>
<div class="endscriptcode"><em>(The code snippet is in TypeScript)</em></div>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>To see sample quotes in the local Workbench, we need an object that returns mock data. We can define this object in a separate TypeScript file, say &ldquo;MockData.ts&rdquo;, and then reference this file in MyQuoteWebPart.ts.
<strong>MockData.ts</strong> itself refences (i.e. import in TypeScript) the IQuote interface defined in MyQuotesWebPart.</p>
<p>&nbsp;</p>
<div class="scriptcode">
<div class="pluginEditHolder" pluginCommand="mceScriptCode">
<div class="title"><span>JavaScript</span></div>
<div class="pluginLinkHolder"><span class="pluginEditHolderLink">Edit</span>|<span class="pluginRemoveHolderLink">Remove</span></div>
<span class="hidden">js</span>

<div class="preview">
<pre class="js">import&nbsp;<span class="js__brace">{</span>&nbsp;IQuote&nbsp;<span class="js__brace">}</span>&nbsp;from&nbsp;<span class="js__string">'./MyQuotesWebPart'</span>;&nbsp;
&nbsp;
export&nbsp;<span class="js__statement">default</span>&nbsp;class&nbsp;MockData&nbsp;<span class="js__brace">{</span>&nbsp;
&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;private&nbsp;static&nbsp;_items:&nbsp;IQuote[]&nbsp;=&nbsp;[&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="js__brace">{</span>&nbsp;Author:&nbsp;<span class="js__string">'Author&nbsp;1'</span>,&nbsp;Quote:&nbsp;<span class="js__string">'Quote&nbsp;1'</span>&nbsp;<span class="js__brace">}</span>,&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="js__brace">{</span>&nbsp;Author:&nbsp;<span class="js__string">'Author&nbsp;2'</span>,&nbsp;Quote:&nbsp;<span class="js__string">'Quote&nbsp;2'</span>&nbsp;<span class="js__brace">}</span>,&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="js__brace">{</span>&nbsp;Author:&nbsp;<span class="js__string">'Author&nbsp;3'</span>,&nbsp;Quote:&nbsp;<span class="js__string">'Quote&nbsp;3'</span>&nbsp;<span class="js__brace">}</span>];&nbsp;
&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;public&nbsp;static&nbsp;get():&nbsp;Promise&lt;IQuote[]&gt;&nbsp;<span class="js__brace">{</span>&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="js__statement">return</span>&nbsp;<span class="js__operator">new</span>&nbsp;Promise&lt;IQuote[]&gt;((resolve)&nbsp;=&gt;&nbsp;<span class="js__brace">{</span>&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;resolve(MockData._items);&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="js__brace">}</span>);&nbsp;
&nbsp;&nbsp;&nbsp;<span class="js__brace">}</span>&nbsp;
<span class="js__brace">}</span></pre>
</div>
</div>
</div>
<div class="endscriptcode"><em>(The code snippet is in TypeScript)</em></div>
<p>&nbsp;</p>
<p><br>
We can now use the <strong>MockData </strong>class in MyQuotesWebPart, after importing the MockData module.</p>
<pre>import MockData from './MockData';<br></pre>
<p>The <strong>getMockData()</strong> method returns a list of sample quotes defined in the MockData object.</p>
<p>&nbsp;</p>
<div class="scriptcode">
<div class="pluginEditHolder" pluginCommand="mceScriptCode">
<div class="title"><span>JavaScript</span></div>
<div class="pluginLinkHolder"><span class="pluginEditHolderLink">Edit</span>|<span class="pluginRemoveHolderLink">Remove</span></div>
<span class="hidden">js</span>

<div class="preview">
<pre class="js">private&nbsp;getMockData():&nbsp;Promise&lt;IQuotes&gt;&nbsp;<span class="js__brace">{</span>&nbsp;
&nbsp;&nbsp;<span class="js__statement">return</span>&nbsp;MockData.get()&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;.then((data:&nbsp;IQuote[])&nbsp;=&gt;&nbsp;<span class="js__brace">{</span>&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="js__statement">var</span>&nbsp;listData:&nbsp;IQuotes&nbsp;=&nbsp;<span class="js__brace">{</span>&nbsp;Quotes:&nbsp;data&nbsp;<span class="js__brace">}</span>;&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="js__statement">return</span>&nbsp;listData;&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;<span class="js__brace">}</span>)&nbsp;as&nbsp;Promise&lt;IQuotes&gt;;&nbsp;
<span class="js__brace">}</span></pre>
</div>
</div>
</div>
<div class="endscriptcode"><em>(The code snippet is in TypeScript)</em></div>
<p>&nbsp;</p>
<p>&nbsp;</p>
<h1>Displaying the web part</h1>
<p>Quotes are rendered in HTML and added to a container in the web part DOM by the
<strong>renderQuotes()</strong> method. This method loops the IQuote array and, for each item, defined the HTML code for showing a quote and its author.</p>
<p>&nbsp;</p>
<div class="scriptcode">
<div class="pluginEditHolder" pluginCommand="mceScriptCode">
<div class="title"><span>JavaScript</span></div>
<div class="pluginLinkHolder"><span class="pluginEditHolderLink">Edit</span>|<span class="pluginRemoveHolderLink">Remove</span></div>
<span class="hidden">js</span>

<div class="preview">
<pre class="js">private&nbsp;renderQuotes(items:&nbsp;IQuote[]):&nbsp;<span class="js__operator">void</span>&nbsp;<span class="js__brace">{</span>&nbsp;
&nbsp;&nbsp;let&nbsp;html:&nbsp;string&nbsp;=&nbsp;<span class="js__string">''</span>;&nbsp;
&nbsp;&nbsp;items.forEach((item:&nbsp;IQuote)&nbsp;=&gt;&nbsp;<span class="js__brace">{</span>&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;html&nbsp;&#43;=&nbsp;`&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&lt;div&gt;$<span class="js__brace">{</span>escape(item.Quote)<span class="js__brace">}</span>&lt;/div&gt;&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&lt;div&nbsp;class=<span class="js__string">&quot;${styles.author}&quot;</span>&gt;$<span class="js__brace">{</span>escape(item.Author)<span class="js__brace">}</span>&lt;/div&gt;&nbsp;&nbsp;&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;`;&nbsp;
&nbsp;&nbsp;<span class="js__brace">}</span>);&nbsp;
&nbsp;
&nbsp;&nbsp;<span class="js__statement">const</span>&nbsp;listContainer:&nbsp;Element&nbsp;=&nbsp;<span class="js__operator">this</span>.domElement.querySelector(<span class="js__string">'#quotesContainer'</span>);&nbsp;
&nbsp;&nbsp;listContainer.innerHTML&nbsp;=&nbsp;html;&nbsp;
<span class="js__brace">}</span></pre>
</div>
</div>
</div>
<div class="endscriptcode"><em>(The code snippet is in TypeScript)</em></div>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>We can now open the local SharePoint Workbench with the &ldquo;gulp serve&rdquo; command and admire our web part in all its beauty!</p>
<p><img id="192481" src="192481-fig2.png" alt="" width="1199" height="379"></p>
<p>After deploying the web part to a SharePoint site, we can open the SharePoint Workbench hosted in SharePoint and see real quotes defined in the &ldquo;Quotes&rdquo; custom list.</p>
<h1><br>
The code smells</h1>
<p>So far so good, the web part works as expected, except it&rsquo;s not so good, really. At least, it&rsquo;s not SOLID good. These are the violations to the SOLID principles that I can identify for this web part:</p>
<ol>
<li><strong>No single responsibility</strong> in the MyQuotesWebPart. This class has methods for retrieving data, from both local and live environments, as well as displaying it. This should be done in two separate objects, one to read quotes from the SharePoint
 list, and the other one to write (display) quotes on screen. </li><li>The web part class is <strong>tightly coupled</strong> to the mock data object and the Quotes custom list. There is a direct dependency on these objects. If they don&rsquo;t exist, the web part won&rsquo;t work.
</li><li>The if condition on the environment type checking for local or SharePoint environment
<strong>prevents abstraction</strong> of the dependency (hard reference to spHttpClient to retrieve live data) and its replacement with a subtype, as a mock providing sample data should be.
</li></ol>
<p>In merit to the third point, basically providing mock data should not be a switch condition but an injection of a subtype of the real data object. So, it&rsquo;s time to refactor the web part to be more SOLID!</p>
<h1><br>
Refactoring to SOLID</h1>
<p>The first change to apply is a simple separation of files. Instead of adding the definition of the IQuote and IQuotes interfaces in the same TypeScript file where the web part object is defined, let&rsquo;s separate them into a
<strong>QuoteContracts.ts</strong> file. We&rsquo;ll also add a <strong>DataReader.ts</strong> file the contains the definition of the
<strong>IDataReader </strong>interface and <strong>DataReaderFactory</strong> class that we are going to use in the web part to abstract the dependency on the actual data store, and then the two implementations for reading quotes from the &ldquo;Quotes&rdquo;
 SharePoint custom list (<strong>SPDataReader.ts</strong>) and its subtype <strong>
MockDataReader </strong>(in <strong>MockDataReader.ts</strong>). The bad code of the web part is in the MyQuotesWebPart.BAD.ts file, just for comparison.</p>
<p><img id="192482" src="192482-fig3.png" alt="" width="385" height="439"></p>
<p>We now want to remove the hard-coded dependency to SharePoint data and mock data in the MyQuotes web part, controlled by the condition on the environment. Ideally, I would introduce an abstract data reader, and inject the dependency in the constructor of
 the web part. This would require an <strong>Inversion of Control container</strong>, but (a) I am not aware of any IoC library for TypeScript, specifically working with SPFx (feedback welcome here), and (b) it&rsquo;s an overkill solution for just a web part
 with one dependency.</p>
<p>I&rsquo;ll refactor the web part to use a more traditional abstract factory pattern, defined in the constructor. The web part defined a private data reader (<strong>IDataReader
</strong>type) and obtains a concrete instance from the data reader factory (<strong>DataReaderFactory
</strong>object) passing the current web part context.</p>
<p></p>
<div class="scriptcode">
<div class="pluginEditHolder" pluginCommand="mceScriptCode">
<div class="title"><span>JavaScript</span></div>
<div class="pluginLinkHolder"><span class="pluginEditHolderLink">Edit</span>|<span class="pluginRemoveHolderLink">Remove</span></div>
<span class="hidden">js</span>

<div class="preview">
<pre class="js">export&nbsp;<span class="js__statement">default</span>&nbsp;class&nbsp;MyQuotesWebPart&nbsp;extends&nbsp;BaseClientSideWebPart&lt;IMyQuotesWebPartProps&gt;&nbsp;<span class="js__brace">{</span>&nbsp;
&nbsp;
&nbsp;&nbsp;constructor()&nbsp;<span class="js__brace">{</span>&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;super();&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;<span class="js__operator">this</span>._dataReader&nbsp;=&nbsp;DataReaderFactory.getReader(<span class="js__operator">this</span>.context);&nbsp;
&nbsp;&nbsp;<span class="js__brace">}</span>&nbsp;
&nbsp;
&nbsp;&nbsp;private&nbsp;_dataReader&nbsp;:&nbsp;IDataReader;</pre>
</div>
</div>
</div>
<div class="endscriptcode"><em>(The code snippet is in TypeScript)</em></div>
<p></p>
<p>The <strong>IDataReader </strong>interface simply defines a method <strong>getData()</strong> that returns
<strong>IQuotes</strong>, that is a collection of quotes. And the <strong>DataReaderFactory
</strong>object decides whether to return real data from SharePoint or mock data depending on the environment type.</p>
<p></p>
<div class="scriptcode">
<div class="pluginEditHolder" pluginCommand="mceScriptCode">
<div class="title"><span>JavaScript</span></div>
<div class="pluginLinkHolder"><span class="pluginEditHolderLink">Edit</span>|<span class="pluginRemoveHolderLink">Remove</span></div>
<span class="hidden">js</span>

<div class="preview">
<pre class="js">export&nbsp;interface&nbsp;IDataReader&nbsp;<span class="js__brace">{</span>&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;getData():&nbsp;Promise&lt;IQuotes&gt;;&nbsp;
<span class="js__brace">}</span>&nbsp;
&nbsp;
export&nbsp;class&nbsp;DataReaderFactory&nbsp;<span class="js__brace">{</span>&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;public&nbsp;static&nbsp;getReader(context:&nbsp;IWebPartContext)&nbsp;<span class="js__brace">{</span>&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="js__statement">if</span>&nbsp;(Environment.type&nbsp;===&nbsp;EnvironmentType.SharePoint&nbsp;||&nbsp;Environment.type&nbsp;===&nbsp;EnvironmentType.ClassicSharePoint)&nbsp;<span class="js__brace">{</span>&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="js__statement">return</span>&nbsp;<span class="js__operator">new</span>&nbsp;SPDataReader(context,&nbsp;<span class="js__string">&quot;Quotes&quot;</span>);&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="js__brace">}</span>&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="js__statement">else</span>&nbsp;<span class="js__statement">if</span>&nbsp;(Environment.type&nbsp;===&nbsp;EnvironmentType.Local)&nbsp;<span class="js__brace">{</span>&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="js__statement">return</span>&nbsp;<span class="js__operator">new</span>&nbsp;MockDataReader();&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="js__brace">}</span>&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;<span class="js__brace">}</span>&nbsp;
<span class="js__brace">}</span></pre>
</div>
</div>
</div>
<div class="endscriptcode"><em>(The code snippet is in TypeScript)</em></div>
<p></p>
<p>This will make the <strong>renderData()</strong> method in MyQuotesWebPart very simple, without the differentiation on data source depending on the environment.</p>
<p></p>
<div class="scriptcode">
<div class="pluginEditHolder" pluginCommand="mceScriptCode">
<div class="title"><span>JavaScript</span></div>
<div class="pluginLinkHolder"><span class="pluginEditHolderLink">Edit</span>|<span class="pluginRemoveHolderLink">Remove</span></div>
<span class="hidden">js</span>

<div class="preview">
<pre class="js">private&nbsp;renderData():&nbsp;<span class="js__operator">void</span>&nbsp;<span class="js__brace">{</span>&nbsp;
&nbsp;&nbsp;<span class="js__operator">this</span>._dataReader.getData().then((response)&nbsp;=&gt;&nbsp;<span class="js__brace">{</span>&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;<span class="js__operator">this</span>.renderQuotes(response.Quotes);&nbsp;
&nbsp;&nbsp;<span class="js__brace">}</span>);&nbsp;
<span class="js__brace">}</span></pre>
</div>
</div>
</div>
<div class="endscriptcode"><em>(The code snippet is in TypeScript)</em></div>
<p></p>
<p>The actual data source is defined in the factory. The <strong>SPDataReader </strong>
class implements the <strong>IDataReader </strong>interface and, in the implemented
<strong>getData()</strong> method, utilizes the <strong>spHttpClient </strong>object, obtained by the web part context, to retrieve a list of items from the &ldquo;Quotes&rdquo; list. The dependency on the web part context and the name of the custom list is
 injected via its constructor.</p>
<p></p>
<div class="scriptcode">
<div class="pluginEditHolder" pluginCommand="mceScriptCode">
<div class="title"><span>JavaScript</span></div>
<div class="pluginLinkHolder"><span class="pluginEditHolderLink">Edit</span>|<span class="pluginRemoveHolderLink">Remove</span></div>
<span class="hidden">js</span>

<div class="preview">
<pre class="js">export&nbsp;class&nbsp;SPDataReader&nbsp;implements&nbsp;IDataReader&nbsp;<span class="js__brace">{</span>&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;constructor(context:&nbsp;IWebPartContext,&nbsp;listName:&nbsp;string)&nbsp;<span class="js__brace">{</span>&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="js__operator">this</span>._context&nbsp;=&nbsp;context;&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="js__operator">this</span>._listName&nbsp;=&nbsp;listName;&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;<span class="js__brace">}</span></pre>
</div>
</div>
</div>
<div class="endscriptcode"><em>(The code snippet is in TypeScript)</em></div>
<p></p>
<p><br>
The <strong>MockDataReader </strong>object extends <strong>SPDataReader</strong>, and because sample data is hard-coded in the class itself, there is no need to specific any web part context or list name. Strictly speaking, there is no need to implement MockDataReader
 as a subtype of SPDataReader. MockDataReader can &ldquo;easily&rdquo; implement the IDataReader interface and be used interchangeably with SPDataReader. However, by subtyping MockDataReader from SPDataReader, we can prove the Liskov principle, which states
 that the functionality of the web part won&rsquo;t change if an object is replaced with an instance of its subtype.</p>
<p></p>
<div class="scriptcode">
<div class="pluginEditHolder" pluginCommand="mceScriptCode">
<div class="title"><span>JavaScript</span></div>
<div class="pluginLinkHolder"><span class="pluginEditHolderLink">Edit</span>|<span class="pluginRemoveHolderLink">Remove</span></div>
<span class="hidden">js</span>

<div class="preview">
<pre class="js">export&nbsp;class&nbsp;MockDataReader&nbsp;extends&nbsp;SPDataReader&nbsp;<span class="js__brace">{</span>&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;constructor()&nbsp;<span class="js__brace">{</span>&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;super(null,&nbsp;null);&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;<span class="js__brace">}</span></pre>
</div>
</div>
</div>
<div class="endscriptcode"><em>(The code snippet is in TypeScript)</em></div>
<p></p>
<p>The entire solution is available on my Github repository for download: <a href="https://github.com/stefanotempesta/SharePoint/myquotes-webpart" target="_blank">
https://github.com/stefanotempesta/SharePoint/myquotes-webpart</a></p>
