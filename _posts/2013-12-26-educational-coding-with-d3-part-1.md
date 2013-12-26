---
layout: post
title: Educational Coding with D3, Part 1
---
Why not try embedding a little educational material in a blog?
[D3](http://d3js.org) is a great visualization tool that
enables all manner of interactive content on web pages. If you have
the programming bug, or just like hacking through code, you can
learn to make interesting diagrams by looking through the plethora
of examples in the
[D3 gallery](https://github.com/mbostock/d3/wiki/Gallery).
Here I'll attempt to do produce a teaching tool for
a simple topic: the orbit and rotation of the Earth.

Start With Data
---------------

Since the first "D" in D3 stands for "data", it would make sense to
begin there. What is the data in the Earth's orbit and rotation? Well,
the position of the Earth is defined by a date and time, so that might
be a good start. Now for educational purposes do we really need exact
dates and times? Probably not, since we are teaching higher-level daily
and seasonal effects. So let's try using two numbers, one for the month
of the year (1 for January), and one for the hour of the day in 24-hour
format. So here is our initial data representing noon on a January day:

{% highlight javascript %}
var data = {
    month: 1,
    hour: 12
};
{% endhighlight %}

Set the Scene
-------------

Now we need to visually represent the data. Where would we start?
Well, the Sun and Earth are spheres, so let's draw them as circles.
First we set up an SVG scene to draw into:

{% highlight javascript %}
var svg = d3.select("#vis").append("svg")
    .attr("width", 500)
    .attr("height", 500);
{% endhighlight %}

Now let's draw a Sun. It just has to be yellow, and why not put it
in the center? So here we go:

{% highlight javascript %}
var sun = svg.append("circle")
    .attr("cx", 250)
    .attr("cy", 250)
    .attr("r", 20)
    .style("fill", "yellow");
{% endhighlight %}

Now where is the Earth? Let's put it to the right of the Sun
to start:

{% highlight javascript %}
var earth = svg.append("circle")
    .attr("cx", 400)
    .attr("cy", 250)
    .attr("r", 5)
    .style("fill", "steelblue");
{% endhighlight %}

Let's see what we have so far:

<script src="http://d3js.org/d3.v3.min.js" charset="utf-8"></script>
<div id="vis"></div>
<script>
(function () {
var data = {
    month: 1,
    hour: 12
};

var svg = d3.select("#vis").append("svg")
    .attr("width", 500)
    .attr("height", 500);

var sun = svg.append("circle")
    .attr("cx", 250)
    .attr("cy", 250)
    .attr("r", 30)
    .style("fill", "yellow");

var earth = svg.append("circle")
    .attr("cx", 400)
    .attr("cy", 250)
    .attr("r", 15)
    .style("fill", "steelblue");
}());
</script>

Represent the Time of Year
--------------------------

Not bad. However, the Earth's position should not be fixed, it should
instead depend on our data. First we'll put in the orbit track:

{% highlight javascript %}
var orbit = svg.append("circle")
    .attr("cx", 250)
    .attr("cy", 250)
    .attr("r", 200)
    .style("fill", "none")
    .style("stroke", "black");
{% endhighlight %}

We will also write an `update()`
function to put things in the right place based on our data:

{% highlight javascript %}
function update() {
    var angle = 2 * Math.PI * (data.month / 12);
    earth
        .attr("cx", 250 + 200 * Math.cos(angle))
        .attr("cy", 250 - 200 * Math.sin(angle));
}
update();
{% endhighlight %}

Note that we need to subtract the y value because in the SVG coordinate
system the y axis is opposite of what is normal, that is it points down
instead of up. If we run this function we now get:

<div id="vis2"></div>
<script>
(function () {
var data = {
    month: 1,
    hour: 12
};

var svg = d3.select("#vis2").append("svg")
    .attr("width", 500)
    .attr("height", 500);

var sun = svg.append("circle")
    .attr("cx", 250)
    .attr("cy", 250)
    .attr("r", 30)
    .style("fill", "yellow");

var orbit = svg.append("circle")
    .attr("cx", 250)
    .attr("cy", 250)
    .attr("r", 150)
    .style("fill", "none")
    .style("stroke", "black");

var earth = svg.append("circle")
    .attr("cx", 400)
    .attr("cy", 250)
    .attr("r", 15)
    .style("fill", "steelblue");

function update() {
    var angle = 2 * Math.PI * (data.month / 12);
    earth
        .attr("cx", 250 + 150 * Math.cos(angle))
        .attr("cy", 250 - 150 * Math.sin(angle));
}

update();
}());
</script>

Ok, so when the month value is 1, the Earth has travelled 1/12
of the way counter-clockwise around the Sun. This is how things
would look if you were looking down from above the North Pole.
I'm from the northern hemisphere, so I guess I'm biased.

Since we are connecting the scene to the month, we should let the user
change the data and see the effects. There are many ways to do this, including a separate drop-down menu or slider, but why not put the months
directly in the scene at their proper locations and let the student
click on them? Here is the code for that:

{% highlight javascript %}
var months = ["Jan", "Feb", "Mar", "Apr",
              "May", "Jun", "Jul", "Aug",
              "Sep", "Oct", "Nov", "Dec"];

svg.selectAll("text.month")
    .data(months)
    .enter().append("text")
    .attr("class", "month")
    .attr("x", function (d, i) {
        var angle = 2 * Math.PI * (i + 1) / 12;
        return 250 + 200 * Math.cos(angle);
    })
    .attr("y", function (d, i) {
        var angle = 2 * Math.PI * (i + 1) / 12;
        return 250 - 200 * Math.sin(angle);
    })
    .text(function (d) { return d; })
    .style("text-anchor", "middle");
    .attr("dy", ".3em")
    .style("cursor", "pointer")
    .on("click", function (d, i) {
        data.month = i + 1;
        update();
    });
{% endhighlight %}
<div id="vis3"></div>
<script>
(function () {
var data = {
    month: 1,
    hour: 12
};

var svg = d3.select("#vis3").append("svg")
    .attr("width", 500)
    .attr("height", 500);

var sun = svg.append("circle")
    .attr("cx", 250)
    .attr("cy", 250)
    .attr("r", 30)
    .style("fill", "yellow");

var orbit = svg.append("circle")
    .attr("cx", 250)
    .attr("cy", 250)
    .attr("r", 150)
    .style("fill", "none")
    .style("stroke", "black");

var earth = svg.append("circle")
    .attr("cx", 400)
    .attr("cy", 250)
    .attr("r", 15)
    .style("fill", "steelblue");

var months = ["Jan", "Feb", "Mar", "Apr",
              "May", "Jun", "Jul", "Aug",
              "Sep", "Oct", "Nov", "Dec"];

svg.selectAll("text.month")
    .data(months)
    .enter().append("text")
    .attr("class", "month")
    .attr("x", function (d, i) {
        var angle = 2 * Math.PI * (i + 1) / 12;
        return 250 + 200 * Math.cos(angle);
    })
    .attr("y", function (d, i) {
        var angle = 2 * Math.PI * (i + 1) / 12;
        return 250 - 200 * Math.sin(angle);
    })
    .text(function (d) { return d; })
    .style("text-anchor", "middle")
    .attr("dy", ".3em")
    .style("cursor", "pointer")
    .on("click", function (d, i) {
        data.month = i + 1;
        update();
    });

function update() {
    var angle = 2 * Math.PI * (data.month / 12);
    earth
        .attr("cx", 250 + 150 * Math.cos(angle))
        .attr("cy", 250 - 150 * Math.sin(angle));
}

update();
}());
</script>

This seems to be working fine so far, but we want to teach about what
happens during the day and night, and in different seasons. To do this
there needs to be the notion of an observer. This observer should
be represented in the scene at a fixed location on the Earth. As the
Earth rotates, so should the observer. Since we are drawing both the Earth
and the observer, let's make both objects
children of the same group element
so they travel together. Notice how I'm now also rotating the Earth
so that the observer is always pointing away from the Sun.
This is intentional, so the observer time (right now showing midnight)
is always the same, no matter what time of year it is.

{% highlight javascript %}
var earthLocation = svg.append("g");

var earth = earthLocation.append("circle")
    .attr("r", 15)
    .style("fill", "steelblue");

var observer = earthLocation.append("circle")
    .attr("cx", 15)
    .attr("r", 5)
    .style("fill", "red");

function update() {
    var angle = 2 * Math.PI * (data.month / 12),
        angleDegrees = angle * 180 / Math.PI,
        x = 250 + 200 * Math.cos(angle),
        y = 250 - 200 * Math.sin(angle);
    earthLocation
        .attr("transform",
            "translate(" + x + "," + y + ")" +
            "rotate(" + -angleDegrees + ")");
}
{% endhighlight %}
<div id="vis4"></div>
<script>
(function () {
var data = {
    month: 1,
    hour: 12
};

var svg = d3.select("#vis4").append("svg")
    .attr("width", 500)
    .attr("height", 500);

var months = ["Jan", "Feb", "Mar", "Apr",
              "May", "Jun", "Jul", "Aug",
              "Sep", "Oct", "Nov", "Dec"];

var sun = svg.append("circle")
    .attr("cx", 250)
    .attr("cy", 250)
    .attr("r", 30)
    .style("fill", "yellow");

var orbit = svg.append("circle")
    .attr("cx", 250)
    .attr("cy", 250)
    .attr("r", 150)
    .style("fill", "none")
    .style("stroke", "black");

var earthLocation = svg.append("g");

var observer = earthLocation.append("circle")
    .attr("cx", 15)
    .attr("r", 5)
    .style("fill", "red");

var earth = earthLocation.append("circle")
    .attr("r", 15)
    .style("fill", "steelblue");

svg.selectAll("text.month")
    .data(months)
    .enter().append("text")
    .attr("class", "month")
    .attr("x", function (d, i) {
        var angle = 2 * Math.PI * (i + 1) / 12;
        return 250 + 200 * Math.cos(angle);
    })
    .attr("y", function (d, i) {
        var angle = 2 * Math.PI * (i + 1) / 12;
        return 250 - 200 * Math.sin(angle);
    })
    .text(function (d) { return d; })
    .style("text-anchor", "middle")
    .attr("dy", ".3em")
    .style("cursor", "pointer")
    .on("click", function (d, i) {
        data.month = i + 1;
        update();
    });

function update() {
    var angle = 2 * Math.PI * (data.month / 12),
        angleDegrees = angle * 180 / Math.PI,
        x = 250 + 150 * Math.cos(angle),
        y = 250 - 150 * Math.sin(angle);
    earthLocation
        .attr("transform",
            "translate(" + x + "," + y + ")" +
            "rotate(" + -angleDegrees + ")");
}

update();
}());
</script>

Represent Day and Night
-----------------------

One of the crucial takeaways to this interactive graphic is
to show where the Earth is dark and light based on where the
Sun's rays can reach. To do this, we should draw the Earth as
a light half and a dark half. Here I did some [SVG path](http://www.w3.org/TR/SVG/paths.html#PathDataEllipticalArcCommands)
trickery, but if you want more help with arcs check out
[d3.svg.arc](https://github.com/mbostock/d3/wiki/SVG-Shapes#wiki-arc).

{% highlight javascript %}
var earthDark = earthLocation.append("path")
    .attr("d", "m0,15 a15,15 0 1,0 0,-30")
    .style("fill", "black")
    .style("opacity", 0.5);
{% endhighlight %}
<div id="vis5"></div>
<script>
(function () {
var data = {
    month: 1,
    hour: 12
};

var svg = d3.select("#vis5").append("svg")
    .attr("width", 500)
    .attr("height", 500);

var months = ["Jan", "Feb", "Mar", "Apr",
              "May", "Jun", "Jul", "Aug",
              "Sep", "Oct", "Nov", "Dec"];

var sun = svg.append("circle")
    .attr("cx", 250)
    .attr("cy", 250)
    .attr("r", 30)
    .style("fill", "yellow");

var orbit = svg.append("circle")
    .attr("cx", 250)
    .attr("cy", 250)
    .attr("r", 150)
    .style("fill", "none")
    .style("stroke", "black");

var earthLocation = svg.append("g")
    .attr("transform", "translate(400,250)");

var observer = earthLocation.append("circle")
    .attr("cx", 15)
    .attr("r", 5)
    .style("fill", "red");

var earth = earthLocation.append("circle")
    .attr("r", 15)
    .style("fill", "steelblue");

var earthDark = earthLocation.append("path")
    .attr("d", "m0,15 a15,15 0 1,0 0,-30")
    .style("fill", "black")
    .style("opacity", 0.5);

svg.selectAll("text.month")
    .data(months)
    .enter().append("text")
    .attr("class", "month")
    .attr("x", function (d, i) {
        var angle = 2 * Math.PI * (i + 1) / 12;
        return 250 + 200 * Math.cos(angle);
    })
    .attr("y", function (d, i) {
        var angle = 2 * Math.PI * (i + 1) / 12;
        return 250 - 200 * Math.sin(angle);
    })
    .text(function (d) { return d; })
    .style("text-anchor", "middle")
    .attr("dy", ".3em")
    .style("cursor", "pointer")
    .on("click", function (d, i) {
        data.month = i + 1;
        update();
    });

function update() {
    var angle = 2 * Math.PI * (data.month / 12),
        angleDegrees = angle * 180 / Math.PI,
        x = 250 + 150 * Math.cos(angle),
        y = 250 - 150 * Math.sin(angle);
    earthLocation
        .attr("transform",
            "translate(" + x + "," + y + ")" +
            "rotate(" + -angleDegrees + ")");
}

update();
}());
</script>

Now let's rotate the Earth based on the time of day. To show this,
we will position the observer along the appropriate location on the
rim of the Earth. We'll also add some time controls around the
Earth to set the time of day. These were a tad tricky also in order
to get them positioned correctly without making the text flip
upside-down at certain times of year. Our `update()` function
additionally will handle changes in the hour.

{% highlight javascript %}
earthLocation.selectAll("g.hour")
    .data([0, 3, 6, 9, 12, 15, 18, 21])
    .enter().append("g")
    .attr("class", "hour")
    .attr("transform", function (d) {
        var angle = -2 * Math.PI * d / 24;
            x = 30 * Math.cos(angle),
            y = 30 * Math.sin(angle);
        return "translate(" + x + "," + y + ")";
    })
    .append("text")
    .text(function (d) {
        return (d % 12 === 0 ? 12 : d % 12) + "" +
               (d < 12 ? "a" : "p");
    })
    .style("font-size", 8)
    .style("text-anchor", "middle")
    .attr("dy", ".3em")
    .style("cursor", "pointer")
    .on("click", function (d) {
        data.hour = d;
        update();
    });

function update() {
    // ...
    // Position observer
    var timeAngle = 2 * Math.PI * (data.hour / 24),
        timeX = 15 * Math.cos(timeAngle),
        timeY = -15 * Math.sin(timeAngle);
    observer
        .attr("cx", timeX)
        .attr("cy", timeY);
}
{% endhighlight %}
<div id="vis6"></div>
<script>
(function () {
var data = {
    month: 1,
    hour: 12
};

var svg = d3.select("#vis6").append("svg")
    .attr("width", 500)
    .attr("height", 500);

var months = ["Jan", "Feb", "Mar", "Apr",
              "May", "Jun", "Jul", "Aug",
              "Sep", "Oct", "Nov", "Dec"];

var sun = svg.append("circle")
    .attr("cx", 250)
    .attr("cy", 250)
    .attr("r", 30)
    .style("fill", "yellow");

var orbit = svg.append("circle")
    .attr("cx", 250)
    .attr("cy", 250)
    .attr("r", 150)
    .style("fill", "none")
    .style("stroke", "black");

var earthLocation = svg.append("g")
    .attr("transform", "translate(400,250)");

var observer = earthLocation.append("circle")
    .attr("cx", 15)
    .attr("r", 5)
    .style("fill", "red");

var earth = earthLocation.append("circle")
    .attr("r", 15)
    .style("fill", "steelblue");

var earthDark = earthLocation.append("path")
    .attr("d", "m0,15 a15,15 0 1,0 0,-30")
    .style("fill", "black")
    .style("opacity", 0.5);

svg.selectAll("text.month")
    .data(months)
    .enter().append("text")
    .attr("class", "month")
    .attr("x", function (d, i) {
        var angle = 2 * Math.PI * (i + 1) / 12;
        return 250 + 200 * Math.cos(angle);
    })
    .attr("y", function (d, i) {
        var angle = 2 * Math.PI * (i + 1) / 12;
        return 250 - 200 * Math.sin(angle);
    })
    .text(function (d) { return d; })
    .style("text-anchor", "middle")
    .attr("dy", ".3em")
    .style("cursor", "pointer")
    .on("click", function (d, i) {
        data.month = i + 1;
        update();
    });

earthLocation.selectAll("g.hour")
    .data([0, 3, 6, 9, 12, 15, 18, 21])
    .enter().append("g")
    .attr("class", "hour")
    .attr("transform", function (d) {
        var angle = 2 * Math.PI * d / 24;
            x = 30 * Math.cos(angle),
            y = -30 * Math.sin(angle);
        return "translate(" + x + "," + y + ")";
    })
    .append("text")
    .text(function (d) {
        return (d % 12 === 0 ? 12 : d % 12) +
               (d < 12 ? "a" : "p");
    })
    .style("font-size", 8)
    .style("text-anchor", "middle")
    .attr("dy", ".3em")
    .style("cursor", "pointer")
    .on("click", function (d) {
        data.hour = d;
        update();
    });

function update() {
    // Position Earth
    var angle = 2 * Math.PI * (data.month / 12),
        angleDegrees = angle * 180 / Math.PI,
        x = 250 + 150 * Math.cos(angle),
        y = 250 - 150 * Math.sin(angle);
    earthLocation.attr("transform", "translate(" + x + "," + y + ")"
        + "rotate(" + -angleDegrees + ")");

    // Position observer
    var timeAngle = 2 * Math.PI * (data.hour / 24),
        timeX = 15 * Math.cos(timeAngle),
        timeY = -15 * Math.sin(timeAngle);
    observer
        .attr("cx", timeX)
        .attr("cy", timeY);
    earthLocation.selectAll("g.hour").selectAll("text")
        .attr("transform",
            "rotate(" + angleDegrees + ")");
}

update();

}());
</script>

Make it Move!
-------------

Now that we have have our simple update function that places things
correctly given the month and hour, we can start to have fun
by animating it. First, let's add a field to our data that defines
how much faster we should animate the graphic compared to the real-time
speed of one rotation per day. Here we initialize it to one rotation
each second.

{% highlight javascript %}
var data = {
    month: 1,
    hour: 12,
    speedup: 24 * 60 * 60
};
{% endhighlight %}

To animate, we just need to keep track of our last render time, update the
hour and month accordingly, and put the update function in a loop
with `setTimeout()`. Notice that we don't care about adding logic to reset
the month or hour once it rolls past its normal maximum. We'll let
the cyclic nature of sine and cosine handle that for us.

{% highlight javascript %}
function update() {
    if (data.lastTime) {
        var hourDelta = data.speedup * (Date.now() - data.lastTime) / (1000 * 60 * 60),
            dayDelta = hourDelta / 24,
            yearDelta = dayDelta / 365.242,
            monthDelta = yearDelta * 12;
        data.hour += hourDelta;
        data.month += monthDelta;
    }
    data.lastTime = Date.now();

    // ...

    setTimeout(update, 10);
}
{% endhighlight %}

Now to add a couple controls for the time and speed. This first
button will enable you to set the observer to your
current time. I'm adding 0.5 in the month calculation because I'm assuming
the labels represent mid-month rather than the start of the month.

{% highlight javascript %}
svg.append("text")
    .attr("x", 10).attr("y", 120).text("Now")
    .style("cursor", "pointer")
    .on("click", function () {
        var date = new Date();
        data.month = date.getMonth() + date.getDate()/30 + 0.5;
        data.hour = date.getHours();
        update();
    });
{% endhighlight %}

We'll also add other controls to pause and animate in real-time
(about the same as pause unless you're really patient), low,
and high speeds.

{% highlight javascript %}
var speeds = svg.selectAll("text.speed")
    .data([
        {name: "Pause", speed: 0},
        {name: "Real-time (Slow!)", speed: 1},
        {name: "Fast", speed: 24 * 60 * 60},
        {name: "Hyper-speed", speed: 24 * 60 * 60 * 5}
        ])
    .enter().append("text")
    .attr("class", "speed")
    .attr("x", 10)
    .attr("y", function (d, i) { return 20 + 20 * i; })
    .style("fill", function (d, i) { return i === 2 ? "black" : "gray"; })
    .text(function (d) { return d.name; })
    .style("cursor", "pointer")
    .on("click", function (d) {
        data.speedup = d.speed;
        speeds.style("fill", function (dd) { return dd === d ? "black" : "gray"; })
        update();
    });
{% endhighlight %}

And here's the final graphic:

<div id="vis7"></div>
<script>
(function () {
var data = {
    month: 1,
    hour: 12,
    speedup: 24 * 60 * 60 * 1
};

var svg = d3.select("#vis7").append("svg")
    .attr("width", 500)
    .attr("height", 500);

svg.append("text")
    .attr("x", 10).attr("y", 120).text("Now")
    .style("cursor", "pointer")
    .on("click", function () {
        var date = new Date();
        data.month = date.getMonth() + date.getDate()/30 + 0.5;
        data.hour = date.getHours();
        update();
    });

var speeds = svg.selectAll("text.speed")
    .data([
        {name: "Pause", speed: 0},
        {name: "Real-time (Slow!)", speed: 1},
        {name: "Fast", speed: 24 * 60 * 60},
        {name: "Hyper-speed", speed: 24 * 60 * 60 * 5}
        ])
    .enter().append("text")
    .attr("class", "speed")
    .attr("x", 10)
    .attr("y", function (d, i) { return 20 + 20 * i; })
    .style("fill", function (d, i) { return i === 2 ? "black" : "gray"; })
    .text(function (d) { return d.name; })
    .style("cursor", "pointer")
    .on("click", function (d) {
        data.speedup = d.speed;
        speeds.style("fill", function (dd) { return dd === d ? "black" : "gray"; })
        update();
    });

var months = ["Jan", "Feb", "Mar", "Apr",
              "May", "Jun", "Jul", "Aug",
              "Sep", "Oct", "Nov", "Dec"];

var sun = svg.append("circle")
    .attr("cx", 250)
    .attr("cy", 250)
    .attr("r", 30)
    .style("fill", "yellow");

var orbit = svg.append("circle")
    .attr("cx", 250)
    .attr("cy", 250)
    .attr("r", 150)
    .style("fill", "none")
    .style("stroke", "black");

var earthLocation = svg.append("g")
    .attr("transform", "translate(400,250)");

var observer = earthLocation.append("circle")
    .attr("cx", 15)
    .attr("r", 5)
    .style("fill", "red");

var earth = earthLocation.append("circle")
    .attr("r", 15)
    .style("fill", "steelblue");

var earthDark = earthLocation.append("path")
    .attr("d", "m0,15 a15,15 0 1,0 0,-30")
    .style("fill", "black")
    .style("opacity", 0.5);

svg.selectAll("text.month")
    .data(months)
    .enter().append("text")
    .attr("class", "month")
    .attr("x", function (d, i) {
        var angle = 2 * Math.PI * (i + 1) / 12;
        return 250 + 200 * Math.cos(angle);
    })
    .attr("y", function (d, i) {
        var angle = 2 * Math.PI * (i + 1) / 12;
        return 250 - 200 * Math.sin(angle);
    })
    .text(function (d) { return d; })
    .style("text-anchor", "middle")
    .attr("dy", ".3em")
    .style("cursor", "pointer")
    .on("click", function (d, i) {
        data.month = i + 1;
        update();
    });

earthLocation.selectAll("g.hour")
    .data([0, 3, 6, 9, 12, 15, 18, 21])
    .enter().append("g")
    .attr("class", "hour")
    .attr("transform", function (d) {
        var angle = 2 * Math.PI * d / 24;
            x = 30 * Math.cos(angle),
            y = -30 * Math.sin(angle);
        return "translate(" + x + "," + y + ")";
    })
    .append("text")
    .text(function (d) {
        return (d % 12 === 0 ? 12 : d % 12) +
               (d < 12 ? "a" : "p");
    })
    .style("font-size", 8)
    .style("text-anchor", "middle")
    .attr("dy", ".3em")
    .style("cursor", "pointer")
    .on("click", function (d) {
        data.hour = d;
        update();
    });

function update() {
    if (data.lastTime) {
        var hourDelta = data.speedup * (Date.now() - data.lastTime) / (1000 * 60 * 60),
            dayDelta = hourDelta / 24,
            yearDelta = dayDelta / 365.242,
            monthDelta = yearDelta * 12;
        data.hour += hourDelta;
        data.month += monthDelta;
    }
    data.lastTime = Date.now();

    // Position Earth
    var angle = 2 * Math.PI * (data.month / 12),
        angleDegrees = angle * 180 / Math.PI,
        x = 250 + 150 * Math.cos(angle),
        y = 250 - 150 * Math.sin(angle);
    earthLocation.attr("transform", "translate(" + x + "," + y + ")"
        + "rotate(" + -angleDegrees + ")");

    // Position observer
    var timeAngle = 2 * Math.PI * (data.hour / 24),
        timeX = 15 * Math.cos(timeAngle),
        timeY = -15 * Math.sin(timeAngle);
    observer
        .attr("cx", timeX)
        .attr("cy", timeY);
    earthLocation.selectAll("g.hour").selectAll("text")
        .attr("transform",
            "rotate(" + angleDegrees + ")");

    setTimeout(update, 10);
}

update();

}());
</script>

Tilt of the Earth? Seasons?
---------------------------

The interactive graphic we've created covers the basics of
time of day and time of year. But there are several simplifying
assumptions, such as that the Earth has no tilt and the observer
is limited to the equator. In Part 2 we'll tackle making our
visualization a bit more robust. Have any ideas for me?
Comment or create a GitHub issue
if there's something you'd like to see added or improved.
