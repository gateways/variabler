---
layout: default
title: variabler - IFF Net
---


<div class="blurb">
	<h1>HRR Variables</h1>
	<p>An evolving view of metadata.</p>
</div>


<style>
    .node circle {
        fill: steelblue;
        stroke: steelblue;
        stroke-width: 1.5px;
    }

    .node rect {
        /*fill: steelblue;
        stroke: steelblue;
        stroke-width: 1.5px;*/
        z-index: -10
    }

    .node {
        font: 10px sans-serif;
    }

    .link {
        fill: none;
        stroke: #ccc;
        stroke-width: 1.5px;
    }

    .branch {
        font-weight: bold;
        font-size: 14px;
    }
    
    #viz {
    	background-color: white;
    }
    
    .compo-wrapper { background: white; }
</style>

<div id="viz">

<script src="http://d3js.org/d3.v3.min.js"></script>
<script>

    var diameter = 2660;

    var tree = d3.layout.tree()
        .size([360, diameter / 2 - 120])
        .separation(function(a, b) { return (a.parent == b.parent ? 1 : 2) / a.depth; });

    var diagonal = d3.svg.diagonal.radial()
        .projection(function(d) { return [d.y, d.x / 180 * Math.PI]; });

    var svg = d3.select("#viz").append("svg")
        .attr("width", diameter)
        .attr("height", diameter + 50)
        .append("g")
        .attr("transform", "translate(" + diameter / 2 + "," + diameter / 2 + ")");

    d3.json("https://rawgit.com/gateways/variabler/gh-pages/viz/data.json", function (error, root) {
        if (error) throw error;

        var nodes = tree.nodes(root),
            links = tree.links(nodes);

        var link = svg.selectAll(".link")
            .data(links)
            .enter().append("path")
            .attr("class", "link")
            .attr("d", diagonal);

        var node = svg.selectAll(".node")
            .data(nodes)
            .enter().append("g")
            .attr("class", "node")
            .attr("transform", function(d) { return "rotate(" + (d.x - 90) + ")translate(" + d.y + ")"; })

        //node.append("circle")
        //    .attr("r", 7.5);

        node.append("rect")
            .attr("x", 0)
            .attr("y", -7)
            .attr("width", function(d) { return d.name.length * 8 })
            .attr("height", 12)
            .style("display", function(d) { return d.Case === "Branch" ? "none" : null; })
            .style("fill", function (d) {
                if (d.Case === "Leaf") {
                    return d.sensitivity.Case === "Restricted" ? "red" :  d.sensitivity.Case === "Guarded" ? "yellow" : "green";
                }
            });

        node.append("text")
            .attr("dy", ".31em")
            .attr("text-anchor", function(d) { return d.x < 180 ? "start" : "end"; })
            .attr("transform", function(d) { return d.x < 180 ? "translate(8)" : "rotate(180)translate(-8)"; })
            .text(function (d) { return d.name; })
            .attr("class", function (d) { return (d.Case === "Branch") ? "branch" : "leaf"; });
    });

    d3.select(self.frameElement).style("height", diameter - 150 + "px");

</script>
</div>
