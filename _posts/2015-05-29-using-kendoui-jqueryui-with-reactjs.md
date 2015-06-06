---
layout: post
title: Using Kendo UI and jQuery UI with ReactJS
description:
modified: 2015-06-04
tags: [kendoui, jqueryui, reactjs]
image:
  feature: abstract-7.jpg
---

If you want to use Kendo UI or any other UI widget library with ReactJS then you'll need to understand the
<a href="https://facebook.github.io/react/docs/component-specs.html" target="_blank">component
lifecycle</a>. This really somewhat contrived example uses the Kendo date picker.

{% highlight javascript %}
var React = require('react');

var KDatePicker = React.createClass({
    render: function () {
        return (
            <input type="text" id={this.props.id}/>
        );
    },

    componentDidMount: function () {
        $(this.getDOMNode()).kendoDatePicker();
    }
});

module.exports = KDatePicker;

{% endhighlight %}

After the component renders React will call the `componentDidMount` method and this is where you can call Kendo or
jQuery UI's methods to initialize their widgets.