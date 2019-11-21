# Blazor Dynamic Script Load

Demonstrates dynamic loading of a JavaScript library in Blazor

As a demo for a [StackOverflow question](https://stackoverflow.com/questions/58976579/blazor-server-load-js-scripts-only-on-certain-pages-not-on-all/58979941)
I wrote this simple demo app that will load JQuery and a local JS interop library dynamically.

Often examples of using JavaScript libraries in Blazor add the library to the `_Host.cshtml` or the `index.html` page. This
is fine if most pages are going to use the library, e.g. Bootstrap, but some libraries might only be used on infrequent occassions or 
by a small percentage of users. Dynamic loading is a better approach in this case.

To achieve this the demo has a single script reference in `index.html`:
```html
    <!-- enables dynamic loading of scripts -->
    <script src="js/scriptLoader.js"></script>
```
When a page or component needs to load a specific library, it injects the `IJSRuntime` and invokes `loadScript`. Note this returns 
a promise since the script loads asynchronously, so we `await` this in the Blazor code.

In the example below, we load JQuery and then a simple interop library that sets the H1 text using JQuery.
```
@page "/"
@inject IJSRuntime jsRuntime

<h1>Blazor Dynamic Script Load</h1>

<p>..content removed...</p>
@code
{
    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        // invoke script loader
        Console.WriteLine("Loading jQuery");
        // note the scripts will only load once
        await jsRuntime.InvokeVoidAsync("loadScript", "https://code.jquery.com/jquery-3.4.1.js");
        await jsRuntime.InvokeVoidAsync("loadScript", "js/myJQueryTest.js");

        Console.WriteLine("Invoking jQuery");

        await jsRuntime.InvokeVoidAsync("setH1", "Hello world!");

        Console.WriteLine("Invoked JQuery");

        await base.OnAfterRenderAsync(firstRender);
    }
}
```
