---
layout: post
title: Expanding sliders with Kendo UI.
description:
modified: 2015-06-17
tags: [kendoui, javascript]
image:
  feature: abstract-2.jpg
---

I've starting hitting walls with Kendo UI not supporting some expected functionality. The Angular UI
Slider makes it easy change the configuration options at run time and thereby change the min and max settings. I'm 
hugely disappointed that there isn't a way to do this seamlessly with the Kendo UI slider. I was hoping this was a case
of me needing to RTFM but my [forum post](http://www.telerik.com/forums/changing-slider-configuration-dynamically)
confirmed that I can't "refresh" the slider. 

The current recommended hack is to call the `destroy` function, remove it from the DOM, and then recreate the slider.
That isn't slick but I have a job to do so here we go:

{% highlight html %}
<div kendo-slider="baddassSlider" expanding-slider="[100, 300, 700, 1500]"></div>
{% endhighlight %}

{% highlight javascript %}
(function () {
  angular
    .module('kendoExtensions', [])
    .directive('expandingSlider', expandingSlider);
   
  expandingSlider.$inject = ['$compile'];
    
  function expandingSlider() {
    return {
      restrict: 'A',
      link: function (scope, el, attrs) {
        var increments = scope.$eval(attrs.expandingSlider).slice();
        var $el = $(el);
        
        var unbindWatch = scope.$watch(function () {
          return scope[attrs.kendoSlider];
        }, function(slider) {
          if (angular.isDefined(slider)) {
            unbindWatch();
            bindSliderBehavior(slider);
          }
        });
        
        function bindSliderBehavior(slider) {
          scope.$watch(function () {
            return slider.value();
          }, function (newSliderVal) {
            var max = attrs.kMax;
            
            if (newSliderVal && newSliderVal >= max && increments.length > 0) {
              var newMax = getNextIncrement(newSliderVal);
              $el.attr('k-max', newMax);
              $el.attr('k-small-step', newMax / 100);
              $el.attr('increasing-slider', '[' + increments.toString() + ']');
              var html = $el.get(0).outerHTML;
              
              slider.destroy();
              
              var newSlider = $compile(html)(scope);
              newSlider.attr('style', '');
              delete scope[attrs.kendoSlider];
              $el.parents('.k-slider').replaceWith(newSlider);
            }
          });
        }
        
        function getNextIncrement(currentValue) {
          var nextIncrement;
    
          do {
            nextIncrement = increments.shift();
            if (!nextIncrement) {
                nextIncrement = currentValue;
            }
          } while (nextIncrement < currentValue);
    
           return nextIncrement;
        }
      }
    };
  }
})();
{% endhighlight %}