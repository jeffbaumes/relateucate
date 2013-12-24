---
layout: post
title: Embedding Javascript in Jekyll
---
<script src="http://d3js.org/d3.v3.min.js" charset="utf-8"></script>

Why not try embedding a little educational material in a blog?

There's this great visualization tool called [D3](http://d3js.org) that
enables all sorts of interactive content on web pages. If you have
the programming bug, or just like hacking through code, you can
learn to make interesting diagrams by looking through the plethora
of examples on the D3 website.

Try this out with me while I walk through making an educational
post about a simple topic: the orbit and rotation of the Earth.

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

```
var data = {
    month: 1,
    hour: 12
};
```

Set the Scene
-------------

Now we need to visually represent the data. Where would we start?
Well, the Sun and Earth are spheres, so let's draw them as circles.
First we set up an SVG scene to draw into:

```
var svg = d3.select("#vis").append("svg")
    .attr("width", 500)
    .attr("height", 500);
```

Now let's draw a Sun. It just has to be yellow, and why not put it
in the center? So here we go:

```
var sun = svg.append("circle")
    .attr("cx", 250)
    .attr("cy", 250)
    .attr("r", 20)
    .style("fill", "yellow");
```

Now where is the Earth? Let's put it to the right of the Sun
to start:

```
var earth = svg.append("circle")
    .attr("cx", 400)
    .attr("cy", 250)
    .attr("r", 5)
    .style("fill", "steelblue");
```

Let's see what we have so far:

<div id="vis"></div>
<script>
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
    .attr("cx", 450)
    .attr("cy", 250)
    .attr("r", 15)
    .style("fill", "steelblue");
</script>

Represent the Time of Year
--------------------------

Not bad. However, the Earth's position should not be fixed, it should
instead depend on our data. We will write an `update()`
function to put things in the right place based on our data:

```
function update() {
    var angle = 2 * Math.PI * (data.month / 12);
    earth
        .attr("cx", 250 + 200 * Math.cos(angle))
        .attr("cy", 250 + 200 * Math.sin(angle));
}
update();
```

If we run this function we now get:

<div id="vis2"></div>
<script>
var svg = d3.select("#vis2").append("svg")
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

function update() {
    var angle = 2 * Math.PI * (data.month / 12);
    earth
        .attr("cx", 250 + 200 * Math.cos(angle))
        .attr("cy", 250 + 200 * Math.sin(angle));
}

update();
</script>

Ok, so when the month value is 1, the Earth has travelled 1/12
of the way around the Sun. Makes sense. But should it travel clockwise
or counterclockwise? Shouldn't matter, since it could appear to
go either way depending whether you are looking at the Earth's orbit
from on top or underneath (But what is the "top" or "underneath" anyway?
The universe does not have an objective "up" direction that I know of).

Since we are connecting the scene to the month, we should let the user
change the data and see the effects. There are many ways to do this, including a separate drop-down menu or slider, but why not put the months
directly in the scene at their proper locations and let the student
click on them? Here is the code for that:

```
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
        return 250 + 200 * Math.sin(angle);
    })
    .text(function (d) { return d; })
    .style("text-anchor", "middle");
    .on("click", function (d, i) {
        data.month = i + 1;
        update();
    });
```
<div id="vis3"></div>
<script>
(function () {
var svg = d3.select("#vis3").append("svg")
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
        return 250 + 200 * Math.sin(angle);
    })
    .text(function (d) { return d; })
    .style("text-anchor", "middle")
    .on("click", function (d, i) {
        data.month = i + 1;
        update();
    });

function update() {
    var angle = 2 * Math.PI * (data.month / 12);
    earth
        .attr("cx", 250 + 150 * Math.cos(angle))
        .attr("cy", 250 + 150 * Math.sin(angle));
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
This is intentional, as we'll soon see.

```
var earthLocation = svg.append("g")
    .attr("transform", "translate(400,250)");

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
        y = 250 + 200 * Math.sin(angle);
    earthLocation
        .attr("transform",
            "translate(" + x + "," + y + ")" +
            "rotate(" + angleDegrees + ")");
}
```
<div id="vis4"></div>
<script>
(function () {
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

var earthLocation = svg.append("g")
    .attr("transform", "translate(400,250)");

var earth = earthLocation.append("circle")
    .attr("r", 15)
    .style("fill", "steelblue");

var observer = earthLocation.append("circle")
    .attr("cx", 15)
    .attr("r", 5)
    .style("fill", "red");

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
        return 250 + 200 * Math.sin(angle);
    })
    .text(function (d) { return d; })
    .style("text-anchor", "middle")
    .on("click", function (d, i) {
        data.month = i + 1;
        update();
    });

function update() {
    var angle = 2 * Math.PI * (data.month / 12),
        angleDegrees = angle * 180 / Math.PI,
        x = 250 + 150 * Math.cos(angle),
        y = 250 + 150 * Math.sin(angle);
    earthLocation
        .attr("transform",
            "translate(" + x + "," + y + ")" +
            "rotate(" + angleDegrees + ")");
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

```
var earthDark = earthLocation.append("path")
    .attr("d", "m0,15 a15,15 0 1,0 0,-30")
    .style("fill", "black");
```
<div id="vis5"></div>
<script>
(function () {
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
    .style("fill", "black");

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
        return 250 + 200 * Math.sin(angle);
    })
    .text(function (d) { return d; })
    .style("text-anchor", "middle")
    .on("click", function (d, i) {
        data.month = i + 1;
        update();
    });

function update() {
    var angle = 2 * Math.PI * (data.month / 12),
        angleDegrees = angle * 180 / Math.PI,
        x = 250 + 150 * Math.cos(angle),
        y = 250 + 150 * Math.sin(angle);
    earthLocation
        .attr("transform",
            "translate(" + x + "," + y + ")" +
            "rotate(" + angleDegrees + ")");
}

update();
}());
</script>

Now let's rotate the Earth based on the time of day. To show this,
we will position the observer along the appropriate location on the
rim of the Earth.

```
function update() {
    // ...
    // Position observer
    var timeAngle = 2 * Math.PI * (data.hour / 24),
        timeX = 15 * Math.cos(timeAngle),
        timeY = 15 * Math.sin(timeAngle);
    observer
        .attr("cx", timeX)
        .attr("cy", timeY);
}
```
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
    .style("fill", "black");

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
        return 250 + 200 * Math.sin(angle);
    })
    .text(function (d) { return d; })
    .style("text-anchor", "middle")
    .style("alignment-baseline", "middle")
    .on("click", function (d, i) {
        data.month = i + 1;
        update();
    });

earthLocation.selectAll("text.hour")
    .data([0, 6, 12, 18])
    .enter().append("text")
    .attr("class", "hour")
    .attr("x", function (d) {
        var angle = 2 * Math.PI * d / 24;
        return 30 * Math.cos(angle);
    })
    .attr("y", function (d) {
        var angle = 2 * Math.PI * d / 24;
        return 30 * Math.sin(angle);
    })
    .text(function (d) {
        return (d % 12 === 0 ? 12 : d % 12) + "" +
               (d < 12 ? "a" : "p");
    })
    .style("font-size", 8)
    .style("text-anchor", "middle")
    .style("alignment-baseline", "middle")
    .on("click", function (d) {
        data.hour = d;
        update();
    });

function update() {
    // Position Earth
    var angle = 2 * Math.PI * (data.month / 12),
        angleDegrees = angle * 180 / Math.PI,
        x = 250 + 150 * Math.cos(angle),
        y = 250 + 150 * Math.sin(angle);
    earthLocation
        .attr("transform",
            "translate(" + x + "," + y + ")" +
            "rotate(" + angleDegrees + ")");

    // Position observer
    var timeAngle = 2 * Math.PI * (data.hour / 24),
        timeX = 15 * Math.cos(timeAngle),
        timeY = 15 * Math.sin(timeAngle);
    observer
        .attr("cx", timeX)
        .attr("cy", timeY);
}

update();
setInterval(function (d) {
    data.hour += 1;
    data.month += 1 / (30 * 24);
    update();
}, 5)
}());
</script>

Seasons?
--------


