---
layout: splash
title: Automation Lab Network
permalink: /automationlab-network/
---
## Automation Lab: Guide-student network
A big <span style="color:red">red circle</span> represents the guide, and all the nodes (in same colour) attached to it, represent their students.
<span style="color:#59981a">Path colour</span>: current Ph.D. student;
<span style="color:#59981a">Dotted path</span>: graduated Ph.D. student.

**Fun to do <i class="far fa-grin-beam"></i>:** Try hovering your mouse pointer on the nodes and try dragging the nodes while holding it.
{: .notice--info}

**Note:** The following network only comprises of recent (**Graduating batch: >= 2016**) students. 
{: .notice--warning}

<!-- <!DOCTYPE html> -->
<meta charset="utf-8">
<script src="https://cdnjs.cloudflare.com/ajax/libs/d3/3.5.17/d3.min.js"></script>
<script src="https://d3js.org/d3.v3.min.js"></script>
<style>
 
.svg-container {
  display: inline-block;
  border: 2px dotted #d1d1d1;
  position: relative;
  width: 100%;
  padding-bottom: 85%;
  vertical-align: top;
  overflow: hidden;
}

.svg-content {
  display: inline-block;
  position: absolute;
  top: 0;
  left: 0;
}

path.link {
  fill: none;
  stroke: #31352e;
  stroke-width: 1.5px;
}
 
path.link.phdCurrent {
  stroke: #59981a;
  stroke-width: 2px;
}
 
path.link.phdGraduate {
  stroke: #59981a;
  stroke-dasharray: 4 1;
}

path.link.mtechCurrent {
  stroke: #0066ff;
  stroke-width: 2px;
}

path.link.mtechGraduate {
  stroke: #0066ff;
  stroke-dasharray: 4 1;

}

path.link.ddCurrent {
  stroke: #63747a;
  stroke-width: 2px;
}
 
path.link.ddGraduate {
  stroke: #63747a;
  stroke-dasharray: 4 1;
}

path.link.unknown {
  stroke: #b3b3b3;
}
 
circle {
  fill: #ccc;
  stroke: #292929;
  stroke-width: 1px;
}
 
text {
  fill: #000;
  font: 10px sans-serif;
  pointer-events: none;
}
 
</style>
<body>
  <header>
  </header>
  <!-- container for force layout visualisation  -->
  <div id="container" class="svg-container">
  </div>  
<script>
 
// fetch the data
d3.csv("/automation_net.csv", function(error, links) {

var nodes = {},
  nodeGroup = {};
 
// Compute the distinct nodes from the links.
links.forEach(function(link) {
    link.source = nodes[link.source] || 
        (nodes[link.source] = {nameS: link.source});
    link.target = nodes[link.target] || 
        (nodes[link.target] = {nameT: link.target});
    link.value = +link.value;
    nodeGroup[link.target.nameT] = link.group;});
 
var width = 960,
    height = 800,
    color = d3.scale.category10();
 
var force = d3.layout.force()
    .nodes(d3.values(nodes))
    .links(links)
    .size([width, height])
    //.linkStrength(0.5)//** by default 1
    //.friction(0.9)
    .linkDistance(linkLength)
    .charge(-220) //** -ve value: repulsion
    //.gravity(0.1) //** spring attraction btw nodes.
    //.theta(0.8)
    //.alpha(0.1)
    .on("tick", tick)
    .start();

links.forEach(function(link) {
	if (link.value == 1) {
		link.type = "phdCurrent";
	} else if (link.value == 2) {
		link.type = "phdGraduate";
  } else if (link.value == 3) {
    link.type = "mtechCurrent";
  } else if (link.value == 4) {
    link.type = "mtechGraduate";
  } else if (link.value == 5) {
    link.type = "ddCurrent";
  } else if (link.value == 6) {
    link.type = "ddGraduate";
  } else {
    link.type = "unknown";
  }
});
 
var svg = d3.select("div#container").append("svg")
    .attr("preserveAspectRatio", "xMinYMin meet")
    .attr("viewBox", "10 -20 960 800") //** Manually enter width and height. should be same as earlier defined
    .classed("svg-content", true);
    //.attr("width", width)
    //.attr("height", height);
 
// build the arrow.
svg.append("svg:defs").selectAll("marker")
    .data(["end"])      // Different link/path types can be defined here
  .enter().append("svg:marker")    // This section adds in the arrows
    .attr("id", String)
    .attr("viewBox", "0 -5 10 10")
    .attr("refX", 15)
    .attr("refY", -1.5)
    .attr("markerWidth", 6)
    .attr("markerHeight", 6)
    .attr("orient", "auto")
  .append("svg:path")
    .attr("d", "M0,-5L10,0L0,5");
 
// add the links and the arrows
var path = svg.append("svg:g").selectAll("path")
    .data(force.links())
  .enter().append("svg:path")
    .attr("class", function(d) { return "link " + d.type; })
    .attr("marker-end", "url(#end)");
 
// define the nodes
var node = svg.selectAll(".node")
    .data(force.nodes())
  .enter().append("g")
    .attr("class", "node")
    .attr("cursor", "pointer")
    .on("mouseover", mouseover)
    .on("mouseout", mouseout)
    .call(force.drag);
     
// add the nodes
node.append("circle")
    .attr("r", function(d)
      {if (d.nameS) {
        return 8;} 
      if (d.nameT) {
        return 5;}})
    .style("fill", function(d)
      {if (d.nameS) {
        return "red";} 
      if (d.nameT) {
        return color(nodeGroup[d.nameT]);}
      return "#7a7a7a"; });

// add the text 
node.append("text")
    .attr("x", 12)
    .attr("dy", ".35em")
    .text(function(d) 
    {if (d.nameS) {
        return d.nameS;} 
      if (d.nameT) {
        return d.nameT;}});
 
// add the curvy lines
function tick() {
    path.attr("d", function(d) {
        var dx = d.target.x - d.source.x,
            dy = d.target.y - d.source.y,
            dr = Math.sqrt(dx * dx + dy * dy);
        return "M" + 
            d.source.x + "," + 
            d.source.y + "A" + 
            dr + "," + dr + " 0 0,1 " + 
            d.target.x + "," + 
            d.target.y;
    });
 
    node
        .attr("transform", function(d) { 
		    return "translate(" + d.x + "," + d.y + ")"; });
}
 
// action to take on mouse over
function mouseover() {
    d3.select(this).select("text").transition()
        .duration(750)
        .ease("elastic")
        .attr("x", 22)
        .style("stroke", "#39ff14")
        .style("stroke-width", ".2px")
        .style("font", "20px times");
    d3.select(this).select("circle").transition()
        .duration(750)
        .ease("elastic")
        .attr("r", 16);
}
 
// action to take on mouse double out
function mouseout() {
    d3.select(this).select("circle").transition()
        .duration(200)
        .ease("quad")
        .attr("r", function(d)
          {if (d.nameS) {
            return 8;} 
          if (d.nameT) {
            return 5;}});
    d3.select(this).select("text").transition()
        .duration(200)
        .ease("quad")
        .attr("x", 12)
        .style("stroke", "none")
        .style("fill", "black")
        .style("stroke", "none")
        .style("font", "10px sans-serif");
}

function linkLength(link){
	if (link.value == 1 || link.value == 1 || link.value == 1) {
		return 60;
	} else if (link.value == 1 || link.value == 1 || link.value == 1) {
		return 50;
  } else {
    return 50;
  }
}

});
 
</script>
</body>
<!-- </html> -->

The network is built with [D3.js](https://d3js.org/).
