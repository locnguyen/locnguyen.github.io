---
layout: post
title: Accessing Kendo UI widgets in AngularJS
description:
modified: 2015-06-04
tags: [kendoui, angularjs]
image:
  feature: abstract-7.jpg
---

If you're already using Kendo UI in your AngularJS project then I don't need to sing praises for how easy Telerik 
made integration. They've made the developer experience simple and I'm happy to  for the Pro version. I've recently
become an independent consultant and it's amazing what I will pay for to get my work done better and faster. That's
another post for another day, though.;

Using any of the Kendo UI widgets is as simple as using a directive.

{% highlight html %}
<input kendo-date-picker name="dob" id="dobPicker" />
{% endhighlight %}

In real life though you often want to call on the widget APIs to programmatically do something. If you were in a plain
ole' jQuery app you could open the above date picker like so

{% highlight javascript %}
$('#dobPicker').data('kendoDatePicker').open();
{% endhighlight %}

But how do you do this in AngularJS without using jQuery? In one of my applications I use an accordion but the specs
changed to say the first two sections have to always expand on page load. I needed a way to access the Panel Bar's APIs
to do this.

{% highlight html %}
<ul kendo-panel-bar="accountAccordion" k-expand-mode="'multiple'" init-expanded="[0,1]" id="accountFormAccordion">
    <li>
        Personal Information
        <div>Some personal info inputs</div>
    </li>
    <li>
        Business Information
        <div>Some business information inputs</div>
    </li>
    <li>
        Permissions
        <div>ome permission inputs</div>
    </li>
</ul>
{% endhighlight %}
Of course I don't want to rely on using `$('#accountFormAccordion').data('kendoPanelBar')` because this is an Angular
application. Instead I created a directive that can be reused with the kendo-panel-bar directive.
 
{% highlight javascript %}
angular
    .module('kendo.extras')
    .directive('initExpanded', initExpanded);

function initExpanded() {
    return {
        restrict: 'A',
        link: function (scope, el, attrs) {
            var sectionIndexes = scope.$eval(attrs.initExpanded);

            scope.$watch('accountAccordion', function (accordion) {
                if (angular.isDefined(accordion)) {
                    sectionIndexes.forEach((idx) => {
                        accordion.expand('li:nth-child(' + idx + ')');
                    });
                }
            });
        }
    };
}
{% endhighlight %}

You can access your Kendo widget inside your Angular directives and controllers by giving it name when you declare your 
Kendo widget in your template. In my example I name it 'accountAccordion' and Kendo puts it on the scope for me to use.