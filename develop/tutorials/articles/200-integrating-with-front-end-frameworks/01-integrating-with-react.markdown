# Integrating with React [](id=integrating-with-react)

You can use the 
[npm React portlet template](/develop/reference/-/knowledge_base/7-1/npm-react-portlet-template) 
to automate much of the required configuration for you, or create the module 
manually if you wish. For convenience, all steps are listed if you want to 
proceed with a manual configuration. This tutorial shows how to use React in 
your portlets, whether you're migrating an existing React project or building a 
fresh project. See the 
[npm React portlet template](/develop/reference/-/knowledge_base/7-1/npm-react-portlet-template) 
reference docs for more information on the portlet's anatomy or the 
[react npm portlet sample](/develop/reference/-/knowledge_base/7-1/react-npm-portlet) 
for a React portlet example that you can test and deploy right now. Get started 
by creating your OSGi module and configuring its metadata. 

## Configuring Metadata [](id=configuring-metadata)

Follow these steps to create the module and configure its metadata for React:

1.  [Create an OSGi module](/develop/tutorials/-/knowledge_base/7-1/starting-module-development#creating-a-module). 
    For example, use the 
    [npm React portlet template](/develop/reference/-/knowledge_base/7-1/npm-react-portlet-template). 

2.  Specify the `Web-ContextPath` BND Header in your project's `bnd.bnd` file. 
    Below is the default configuration for the npm React portlet template:

        Web-ContextPath: /my-npm-react-portlet

3.  Create a 
    [`.babelrc` file](/develop/reference/-/knowledge_base/7-1/configuring-liferay-npm-bundler) 
    and add the following presets to it:

        {
        	"presets": ["es2015", "react", "liferay-project"]
        }

4.  Add a 
    [`.npmbundlerrc` file](/develop/reference/-/knowledge_base/7-1/configuring-liferay-npm-bundler) 
    to your project's root folder and add the 
    [`liferay-npm-bundler-preset-react` preset](https://www.npmjs.com/package/liferay-npm-bundler-preset-react). 
    This preset provides the required `.npmbundlerrc` configuration. You can, 
    however, copy the contents of the preset and add additional configuration 
    options to the file instead if needed:

        {
            "preset": "liferay-npm-bundler-preset-react"
        }

5.  Include the following dependency to your `build.gradle` file:

        compileOnly group: "com.liferay", 
        name: "com.liferay.frontend.js.loader.modules.extender.api", 
        version: "2.0.2"

6.  Create a `package.json` in your project if it doesn't already exist and add 
    the configuration shown below to it. Update the `"main"` JS path to reflect 
    your app's main JS file (remember to add the `.es.js` extension if your file 
    uses it). Note that the `liferay-npm-bundler` is added last to the build 
    script. List any additional build processes before this that your project 
    requires:
    
        {
        	"dependencies": {
        		"react": "15.6.2",
        		"react-dom": "15.6.2"
        	},
        	"devDependencies": {
        		"babel-cli": "6.26.0",
        		"babel-preset-es2015": "6.24.1",
        		"babel-preset-liferay-project": "1.6.1",
        		"babel-preset-react": "6.24.1",
        		"liferay-npm-bundler": "1.6.1",
        		"liferay-npm-bundler-preset-react": "1.6.1"
        	},
        	"main": "js/index.js",
        	"name": "my-npm-react-portlet",
        	"scripts": {
        		"build": "babel --source-maps -d 
            build/resources/main/META-INF/resources 
            src/main/resources/META-INF/resources && liferay-npm-bundler"
        	},
        	"version": "1.0.0"
        }    

Next You can configure the portlet.

## Configuring the Portlet [](id=configuring-the-portlet)
 
Follow these steps to configure your portlet:

1.  Create a Component class that implements the `Portlet.class` service:

        @Component(
        	immediate = true,
        	property = {
        		"com.liferay.portlet.display-category=category.sample",
        		"com.liferay.portlet.instanceable=true",
        		"javax.portlet.init-param.template-path=/",
        		"javax.portlet.init-param.view-template=/view.jsp",
        		"javax.portlet.name=" + MyNpmReactPortletKeys.MyNpmReact,
        		"javax.portlet.resource-bundle=content.Language",
        		"javax.portlet.security-role-ref=power-user,user"
        	},
        	service = Portlet.class
        )
        public class MyNpmReactPortlet extends MVCPortlet {
          ...
        }
        
2.  If your React project includes CSS styling as well, add the following 
    additional property to specify the location of the main CSS file:
    
        "com.liferay.portlet.header-portlet-css=/css/main.css"

    Note that this path is relative to the resources path. If using Sass, drop 
    the `.scss` extension in this property and use `.css` instead. For example, 
    if your main CSS file is located in 
    `src/main/resources/META-INF/resources/css/app.scss`, then you would have 
    the following configuration:
    
        "com.liferay.portlet.header-portlet-css=/css/app.css"

3.  To improve code maintenance, 
    [use the NPMResolver APIs to alias your module's package name](/develop/tutorials/-/knowledge_base/7-1/referencing-an-npm-modules-package). 
    The example below exposes the module's name as `bootstrapRequire`:

        @Override
        public void doView(
        		RenderRequest renderRequest, RenderResponse renderResponse)
        	throws IOException, PortletException {

        	JSPackage jsPackage = _npmResolver.getJSPackage();

        	renderRequest.setAttribute(
        		MyNpmReactWebKeys.BOOTSTRAP_REQUIRE,
        		jsPackage.getResolvedId() + " as bootstrapRequire");

        	super.doView(renderRequest, renderResponse);
        }

        @Reference
        private NPMResolver _npmResolver;

4.  Inside your `init.jsp`, add the following Java scriptlet to access the 
    `bootstrapRequire` variable in your portlet's `view.jsp`:

        <%
        String bootstrapRequire = (String)renderRequest.getAttribute(
          MyNpmReactWebKeys.BOOTSTRAP_REQUIRE
        );
        %>

Next you can learn how to render your app's component and transpile your JS 
files. 

## Transpiling your JS and Rendering Your Component [](id=transpiling-your-js-and-rendering-your-component)

Follow these steps to transpile your JS files and render your app component:

1.  To use ES2015+ syntax in your portlet, you must transpile it for the 
    browser. To do this, replace the `.js` extension with `.es.js` for all JS 
    files that need transpiled to ES2015+. For example the main `index.js` file 
    would be renamed `index.es.js`. JS files with this extension are 
    automatically transpiled by Babel before deployment. 
   
2.  Inside your app's main JS file (`index.js` for example), use the function 
    below to render your component. Note the `.es` extension added to the 
    `AppComponent` import in the example below. This is required to import JS 
    files that use the `.es.js` file extension:

        import React from 'react';
        import ReactDOM from 'react-dom';
        import AppComponent from './components/App.es' //parent component

        export default function(elementId) {
          ReactDOM.render(<AppComponent />, document.getElementById(elementId));
        }  

3.  Open your `view.jsp` and add an element container to house your component. 
    Then, add an `<aui:script>` and pass your aliased module name as the 
    `require` attribute's value. Finally, call your module's `default` function 
    that you exported in the previous step, and pass the container element in as 
    the element ID. Adding the `<portlet:namespace />` to the `<div>`'s `id` 
    ensures that it is unique to the portlet and doesn't clash with any existing 
    elements on the page:

        <%@ include file="/init.jsp" %>

        <div id="<portlet:namespace />-root"></div>

        <aui:script require="<%= bootstrapRequire %>">
        	bootstrapRequire.default('<portlet:namespace />-root');
        </aui:script>

## Related Topics [](id=related-topics)

[Integrating with Angular JS](/develop/tutorials/-/knowledge_base/7-1/integrating-with-angular-js)

[Integrating with Vue](/develop/tutorials/-/knowledge_base/7-1/integrating-with-vue)

[Using npm in Your Portlets](/develop/tutorials/-/knowledge_base/7-1/using-npm-in-your-portlets)