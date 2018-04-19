# Disabling All or Portions of the Management Bar [](id=disabling-all-or-portions-of-the-management-bar)

When there's no content in the app, you should disable all the Management Bar's 
buttons, except the sidenav toggler button. 

You can disable the Management Bar by adding the `disabled` attribute to the 
`liferay-frontend:management-bar` tag:

    <liferay-frontend:management-bar
            disabled="<%= total == 0 %>"
            includeCheckBox="<%= true %>"
            searchContainerId="<%= searchContainerId %>"
    >

You can also disable individual components by adding the `disabled` attribute to 
the corresponding tag. The example below disables the display buttons when the 
search container displays 0 results, since changing the display style has no 
effect when there aren't any results to view:

    <liferay-frontend:management-bar-display-buttons
            disabled="<%= total == 0 %>"
            displayViews='<%= new String[] {"descriptive", "icon", "list"} %>'
            portletURL="<%= changeDisplayStyleURL %>"
            selectedDisplayStyle="<%= displayStyle( %>"
    />

![Figure 1: You can disable all or portions of the Management Bar.](../../../../images/liferay-frontend-taglib-management-bar-disabled.png)

## Related Topics [](id=related-topics)

[Filtering Search Results with the Management Bar](/develop/tutorials/-/knowledge_base/7-1/filtering-search-results-with-the-management-bar)

[Including Actions in the Management Bar](/develop/tutorials/-/knowledge_base/7-1/including-actions-in-the-management-bar)

[Sorting Search Results with the Management Bar](/develop/tutorials/-/knowledge_base/7-1/sorting-search-results-with-the-management-bar)