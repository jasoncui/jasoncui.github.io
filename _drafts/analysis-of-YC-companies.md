---
layout: post
title: An Analysis of YC Companies 
comments: True
permalink: analysis-of-YC-companies
category: [tech]
---

*[Y Combinator](http://www.ycombinator.com/) (YC) is perhaps the most well-known startup seed accelerator in the world. YC makes $120k seed investments in small groups of startups twice a year in Silicon Valley. The following is a hopefully interesting analysis of YC companies.*

{% highlight css %}
svg {
  margin: 2em 0;
  font: 10px sans-serif;
}

.foreground {
  fill: #2D6A99;
}

.background {
  fill: #eee;
}
{% endhighlight %}

And of course we need to include the d3.js library:

{% highlight html %}
<script src="http://d3js.org/d3.v3.min.js" charset="utf-8"></script>
{% endhighlight %}

Now, let's get some basic moving parts. We need to setup what a bar is and establish initial variables:

{% highlight javascript %}
var n = 10, // how many bars?
    random = function() { return Math.floor(Math.random() * 100); }, // randomize some numbers for data
    data = d3.range(n).map(random); // an array of randomized datapoints

var barChart = {
  init: function() {
    this.height = 100;
    this.width = 220;
    this.padding = 12;
    this.el = "article header"; // where we'll put our svg 

    // calculate the bar width from the total chart width
    barWidth = Math.floor((this.width - (this.padding * (data.length - 1))) / data.length);
    barHeight = this.height - 20;


    this.svg = d3.select(this.el).insert('svg', ':first-child')
      .attr('width', this.width)
      .attr("height", this.height);

  }
}
{% endhighlight %}

Now, we have to actually render it:

{% highlight javascript %}
var barChart = {

  init: function() { /***/ },

  // draw our svg container
  draw: function() {
    this.meters = this.svg
      .append("g")
        .attr("class", "meter")
        .selectAll("rect")
          .data(data)
          .enter()
          .append('g')
            .attr("class", "bar");

    this.drawBar().attr("class", "background").attr("y", 0).attr("height", barHeight);
    this.drawBar().attr("class", "foreground").attr("y", barHeight).attr("height", 0);
  },

  // this actually draws a bar
  drawBar: function () {
    var self = this;

    return this.meters.append("rect")
      .attr("x", function (d, i) {
        return i * (barWidth + self.padding);
      })
      .attr("width", barWidth);
  }
}

// initialize the bar chart!
barChart.init();
{% endhighlight %}

Ok. So this is what we've got so far:

<pre class="codepen" data-height="300" data-type="result" data-href="wakhK" data-user="mshwery" data-safe="true"><code></code><a href="http://codepen.io/mshwery/pen/wakhK">Check out this Pen!</a></pre>
<script async src="http://codepen.io/assets/embed/ei.js"></script>

Alright, now let's animate this shiz.

{% highlight javascript %}
var barChart = {

  init:     function() { /***/ },
  draw:     function() { /***/ },
  drawBar:  function() { /***/ },

  update: function () {
      var self = this;
      // select the foreground bars and call the animate method on them
      d3.selectAll("rect.foreground").each(self.animate);
  },

  animate: function (d, i) {
    var total = data[i];
    var bar = d3.select(this);
    if (barHeight - total != bar.attr("y")) {
      bar.transition().duration(1500).attr("height", total).attr("y", barHeight - total);
    }
  }
}

// In this example, every couple seconds update the bar chart with new numbers!
setInterval(function() {
  data = d3.range(n).map(random); // get new random numbers
  barChart.update();
}, 2000);
{% endhighlight %}

<div id="container" style="min-width: 310px; height: 400px; margin: 0 auto"></div>

<script>
	$(function () {
    $('#container').highcharts({
        chart: {
            type: 'column'
        },
        title: {
            text: 'Monthly Average Rainfall'
        },
        subtitle: {
            text: 'Source: WorldClimate.com'
        },
        xAxis: {
            categories: [
                'Jan',
                'Feb',
                'Mar',
                'Apr',
                'May',
                'Jun',
                'Jul',
                'Aug',
                'Sep',
                'Oct',
                'Nov',
                'Dec'
            ],
            crosshair: true
        },
        yAxis: {
            min: 0,
            title: {
                text: 'Rainfall (mm)'
            }
        },
        tooltip: {
            headerFormat: '<span style="font-size:10px">{point.key}</span><table>',
            pointFormat: '<tr><td style="color:{series.color};padding:0">{series.name}: </td>' +
                '<td style="padding:0"><b>{point.y:.1f} mm</b></td></tr>',
            footerFormat: '</table>',
            shared: true,
            useHTML: true
        },
        plotOptions: {
            column: {
                pointPadding: 0.2,
                borderWidth: 0
            }
        },
        series: [{
            name: 'Tokyo',
            data: [49.9, 71.5, 106.4, 129.2, 144.0, 176.0, 135.6, 148.5, 216.4, 194.1, 95.6, 54.4]

        }, {
            name: 'New York',
            data: [83.6, 78.8, 98.5, 93.4, 106.0, 84.5, 105.0, 104.3, 91.2, 83.5, 106.6, 92.3]

        }, {
            name: 'London',
            data: [48.9, 38.8, 39.3, 41.4, 47.0, 48.3, 59.0, 59.6, 52.4, 65.2, 59.3, 51.2]

        }, {
            name: 'Berlin',
            data: [42.4, 33.2, 34.5, 39.7, 52.6, 75.5, 57.4, 60.4, 47.6, 39.1, 46.8, 51.1]

        }]
    });
});
</script>

<script src="https://code.highcharts.com/highcharts.js"></script>
<script src="https://code.highcharts.com/modules/exporting.js"></script>