---
layout: post
title: The Earth Spins and Moves
subject: Earth and Space
topic: Days and Seasons
---
The Earth spins around once a day. This spinning
motion is called its rotation.
As the Earth rotates, you are sometimes in the
half of the Earth toward the Sun (day) and other times
you are in the Earth's shadow, pointing away from the Sun
(night). This motion is exactly like a spinning top.
But unlike a top, which eventually slows down by rubbing
against the air and the surface it's spinning on,
there is nothing in space to slow the Earth down, so it
just keeps going on and on. The moon is in fact tugging
on the Earth, slowing it down a tiny bit each day, but the Earth
will be spinning at about the same speed for many many years.

The Earth also moves around the Sun in what is called
an orbit.
It takes one year for the Earth to go all the way around
the Sun.
The Sun's gravity is always wanting to pull
the Earth to it, but because the Earth is moving foward
through space so
fast, it always misses hitting the Sun and keeps going around.
This movement is just like water spinning around a drain,
except again in space there is nothing to slow down the Earth's
movement through space. So the Earth never reaches the Sun -
it never "goes down the drain".

In the picture below, you are looking down on the Earth 
and Sun from far above the Earth's North Pole.
The red dot on the Earth represents you. Even though you
don't feel it, you are moving around with the Earth as it
rotates and moves through space.
The Earth's rotation has been sped up so that one day passes
every second. You can change this by clicking on the speed
controls, or press "Real-time" to see where you are right now.
When in real-time, it will look stopped, but the Earth and
red dot are actually moving at the correct real speed.
If you come back a few hours later, you will see the red
dot in a different location, and the Earth will have moved
slightly.

The month names mark where the Earth is at different times
of year. Press a month to jump the Earth to that location.
There are also times of day around the Earth that you can
press to rotate yourself to day or night.

Questions to think about:

1. Can you count about how many days are in a month? a year?
2. What time of day are you farthest from the Sun? At what time are you closest?
3. In what month will the Earth be the farthest away from where it is right now?
4. What time of day are you moving fastest through space? Hint: When is
   the Earth rotating and orbiting in the same direction?
5. If you were in a hot air baloon floating 1/2 mile above the same
   spot on Earth for a whole day, would the sun be up more, less,
   or the same amount of time compared to someone directly below
   you on the ground?

<script src="http://d3js.org/d3.v3.min.js" charset="utf-8"></script>
<div id="vis"></div>
<script>
(function () {
var data = {
    month: 1,
    hour: 12,
    speedup: 24 * 60 * 60 * 1
};

var svg = d3.select("#vis").append("svg")
    .attr("width", 500)
    .attr("height", 500);

var speeds = svg.selectAll("text.speed")
    .data([
        {name: "Stop", speed: 0},
        {name: "Real-time (now)", speed: 1},
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
        if (d.speed === 1) {
            var date = new Date();
            data.month = date.getMonth() + date.getDate()/30 + 0.5;
            data.hour = date.getHours();
        }
        speeds.style("fill", function (dd) {
            return dd === d ? "black" : "gray";
        });
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


Answers:

1. About 30 days in a month, 365 in a year.
2. Farthest at midnight, closest at noon.
3. Six months from now.
4. Midnight, since the orbit and rotation directions are the same.
5. More. You will be out of the Earth's shadow a bit sooner
   in the morning and enter its shadow a bit later at night.

If you want to see how this animation was made, see [Educational Coding with D3, Part 1](http://jeffbaumes.github.io/relateucate/2013/12/26/educational-coding-with-d3-part-1/).
