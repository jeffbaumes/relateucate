---
layout: post
title: Educational Coding with D3, Part 2
---
Something

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
    .style("stroke", "black")
    .style("stroke-width", 1);

var earthLocation = svg.append("g")
    .attr("transform", "translate(400,250)");

var earthRotation = earthLocation.append("g")
    .attr("transform", "translate(400,250)");

var observer = earthRotation.append("circle")
    .attr("cx", 15)
    .attr("r", 5)
    .style("fill", "red");

var earth = earthRotation.append("circle")
    .attr("r", 15)
    .style("fill", "steelblue");

var earthDark = earthRotation.append("path")
    .attr("d", "m0,15 a15,15 0 1,0 0,-30")
    .style("fill", "black")
    .style("opacity", 0.5);

var r = 15,
    latitude = 45 * Math.PI / 180,
    tilt = 23.4 * Math.PI / 180,
    offsetX = - r * Math.cos(latitude + tilt),
    radiusY = r * Math.cos(latitude),
    radiusX = radiusY * Math.cos(tilt);
earthLocation.append("ellipse")
    .attr("cx", offsetX + radiusX)
    .attr("rx", radiusX)
    .attr("ry", radiusY)
    .style("fill", "none")
    .style("stroke", "black");

svg.selectAll("text.month")
    .data(months)
    .enter().append("text")
    .attr("class", "month")
    .attr("x", function (d, i) {
        var angle = -2 * Math.PI * (i + 1) / 12;
        return 250 + 200 * Math.cos(angle);
    })
    .attr("y", function (d, i) {
        var angle = -2 * Math.PI * (i + 1) / 12;
        return 250 + 200 * Math.sin(angle);
    })
    .text(function (d) { return d; })
    .style("text-anchor", "middle")
    .style("alignment-baseline", "middle")
    .on("click", function (d, i) {
        data.month = i + 1;
        update();
    });

earthRotation.selectAll("g.hour")
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
        return (d % 12 === 0 ? 12 : d % 12) +
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
    var angle = -2 * Math.PI * (data.month / 12),
        angleDegrees = angle * 180 / Math.PI,
        x = 250 + 150 * Math.cos(angle),
        y = 250 + 150 * Math.sin(angle);
    earthLocation.attr("transform", "translate(" + x + "," + y + ")");
    earthRotation.attr("transform", "rotate(" + angleDegrees + ")");

    // Position observer
    var timeAngle = -2 * Math.PI * (data.hour / 24),
        timeX = 15 * Math.cos(timeAngle),
        timeY = 15 * Math.sin(timeAngle);
    observer
        .attr("cx", timeX)
        .attr("cy", timeY);
    earthLocation.selectAll("g.hour").selectAll("text")
        .attr("transform",
            "rotate(" + -angleDegrees + ")");
}

update();

setInterval(function (d) {
    var hourDelta = 0,
        dayDelta = hourDelta / 24,
        yearDelta = dayDelta / 365.242,
        monthDelta = yearDelta * 12;
    data.hour += hourDelta;
    data.month += monthDelta;
    update();
}, 10);

}());
</script>

