---
layout: post
title: Embedding Javascript in Jekyll
---
<script src="http://d3js.org/d3.v3.min.js" charset="utf-8"></script>
<div id="vis"></div>
<script>
var svg = d3.select("#vis").append("svg")
    .attr("width", 500)
    .attr("height", 500);
svg.append("rect").attr("width", 500).attr("height", 500);
</script>
