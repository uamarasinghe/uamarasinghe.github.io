<!DOCTYPE html>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<link rel="stylesheet" href="https://www.w3schools.com/w3css/3/w3.css">
<style>

  .names {
  fill: none;
  stroke: #fff;
  stroke-linejoin: round;
  }

    /* Tooltip CSS */
    .d3-tip {
    line-height: 1.5;
    font-weight: 400;
    font-family:"avenir next", Arial, sans-serif;
    padding: 6px;
    background: rgba(0, 0, 0, 0.6);
    color: #FFA500;
    border-radius: 1px;
    pointer-events: none;
    }

    /* Creates a small triangle extender for the tooltip */
    .d3-tip:after {      
      box-sizing: border-box;
      display: inline;
      font-size: 8px;
      width: 100%;
      line-height: 1.5;
      color: rgba(0, 0, 0, 0.6);
      position: absolute;
      pointer-events: none;
      
    }

    /* Northward tooltips */
    .d3-tip.n:after {
      content: "\25BC";
      margin: -1px 0 0 0;
      top: 100%;
      left: 0;
      text-align: center;
    }

    /* Eastward tooltips */
    .d3-tip.e:after {
      content: "\25C0";
      margin: -4px 0 0 0;
      top: 50%;
      left: -8px;
    }

    /* Southward tooltips */
    .d3-tip.s:after {
      content: "\25B2";
      margin: 0 0 1px 0;
      top: -8px;
      left: 0;
      text-align: center;
    }

    /* Westward tooltips */
    .d3-tip.w:after {
      content: "\25B6";
      margin: -4px 0 0 -1px;
      top: 50%;
      left: 100%;
    }

/*    text{
      pointer-events:none;
    }*/

    .details{
      color:white;
    }

   /* The switch - the box around the slider */
.switch {
  position: relative;
  display: inline-block;
  width: 60px;
  height: 34px;
}

/* Hide default HTML checkbox */
.switch input {display:none;}

/* The slider */
.slider {
  position: absolute;
  cursor: pointer;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: #ccc;
  -webkit-transition: .4s;
  transition: .4s;
}

.slider:before {
  position: absolute;
  content: "";
  height: 26px;
  width: 26px;
  left: 4px;
  bottom: 4px;
  background-color: white;
  -webkit-transition: .4s;
  transition: .4s;
}

input:checked + .slider {
  background-color: #900;
}

input:focus + .slider {
  box-shadow: 0 0 1px #900;
}

input:checked + .slider:before {
  -webkit-transform: translateX(26px);
  -ms-transform: translateX(26px);
  transform: translateX(26px);
}

/* Rounded sliders */
.slider.round {
  border-radius: 34px;
}

.slider.round:before {
  border-radius: 50%;
}

</style>
<body>
  


<script src="https://d3js.org/d3.v4.min.js"></script>
<script src="https://d3js.org/queue.v1.min.js"></script>
<script src="https://d3js.org/topojson.v1.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/d3-tip/0.7.1/d3-tip.min.js"></script>

<h1 style="font-family:avenir next;">Demand & Supply: Financial Inclusion</h1>
    <h2></h2>
    <div id="toggleContainer">
        <table>
            <tr>
                <td><label class="switch">
                    <input id="applicant_toggle" type="checkbox">
                    <div class="slider round"></div>
                </label></td>
                <td style="font-family:avenir next;">IIC Particiants</td>
            </tr>
            <tr>
                <td><label class="switch">
                    <input id="crunchbase_toggle" type="checkbox">
                    <div class="slider round"></div>
                </label></td>
                <td style="font-family:avenir next;">Global Supply</td>
            </tr>
        </table>
    </div>

<script>



var format = d3.format(",");

// Set tooltips
var tip = d3.tip()
            .attr('class', 'd3-tip')
            .offset([100, 0])
            .html(function(d) {
              return "<strong>Country: </strong><span class='details'>" + d.properties.name + "<br></span>" + 
              "<strong>Financial Inclusion Index: </strong><span class='details'>" + format(d.FinancialInclusionIndex) +"<br></span>";
              "<strong>Access: </strong><span class='details'>" + format(d.Access) +"<br></span>"
              "<strong>Usage: </strong><span class='details'>" + format(d.Usage) +"<br></span>"
              "<strong>quality: </strong><span class='details'>" + format(d.Quality) +"</span>";
            })

var bubbleTip = d3.tip()
            .attr('class', 'd3-tip')
            .attr('id', 'd3-tip-bubble')
            .offset([100, 0])
            .html(function(d) {
              return "<strong>City: </strong><span class='details'>" + d.city + "<br></span>" + "<strong>No. of IIC Applicants: </strong><span class='details'>" + format(d.applicants) +"</span>";
            })

// var legend = d3.select('svg')
//     .append("g")
//     .selectAll("g")
//     .data(color.domain())
//     .enter()
//     .append('g')
//       .attr('class', 'legend')
//       .attr('id', 'map-legend')
//       .attr('transform', function(d, i) {
//         var height = legendRectSize;
//         var x = 0;
//         var y = i * height;
//         return 'translate(' + x + ',' + y + ')';
//     });

var margin = {top: 0, right: 0, bottom: 0, left: 0},
            width = 960 - margin.left - margin.right,
            height = 860 - margin.top - margin.bottom;

var color = d3.scaleThreshold()
    // .domain([10000,100000,500000,1000000,5000000,10000000,50000000,100000000,500000000,1500000000])
    .domain([0, 10, 20, 30, 40, 50, 60, 70, 80, 90, 100])
    .range(["rgb(247,251,255)", "rgb(222,235,247)", "rgb(198,219,239)", "rgb(158,202,225)", "rgb(107,174,214)", "rgb(66,146,198)","rgb(33,113,181)","rgb(8,81,156)","rgb(8,48,107)","rgb(3,19,43)","rgb(1,10,20)"]);

var path = d3.geoPath();

var svg = d3.select("body")
            .append("svg")
            .attr("width", width)
            .attr("height", height)
            .append('g')
            .call(d3.zoom().on("zoom", function () {
              svg.attr("transform", d3.event.transform)
              }))
            .attr('class', 'map');

  //           var svg = d3.select("div#container")
  // .append("svg")
  // .attr("preserveAspectRatio", "xMinYMin meet")
  // .attr("viewBox", "0 0 300 300")
  // .classed("svg-content", true);


var projection = d3.geoMercator()
                   .scale(130)
                  .translate( [width / 2, height / 1.5]);

var path = d3.geoPath().projection(projection);

svg.call(tip);
svg.call(bubbleTip);

queue()
    .defer(d3.json, "world_countries.json")
    .defer(d3.csv, "fin_inclusion.csv")
    .defer(d3.csv, "fin_inc_applicants.csv")
    .defer(d3.csv, "tech_access_non_applicants.csv")
    .await(ready);

    console.log('hello')    

function ready(error, data, tech_access, tech_access_applicants, tech_access_non_applicants) {
  var techAccessById = {};

  tech_access.forEach(function(d) { techAccessById[d.id] = +d.FinancialInclusionIndex; });
  data.features.forEach(function(d) { d.FinancialInclusionIndex = techAccessById[d.id] });

  svg.append("g")
      .attr("class", "countries")
    .selectAll("path")
      .data(data.features)
    .enter().append("path")
      .attr("d", path)
      .attr("fill", "#b8b8b8")
      .style("fill", function(d) { return color(techAccessById[d.id]); })
      .style('stroke', 'white')
      .style('stroke-width', 1.5)
      .style("opacity",0.8)
      // tooltips
        .style("stroke","white")
        .style('stroke-width', 0.3)
        .on('mouseover',function(d){
          tip.show(d);

          d3.select(this)
            .style("opacity", 1)
            .style("stroke","white")
            .style("stroke-width",3);
        })
        .on('mouseout', function(d){
          tip.hide(d);

          d3.select(this)
            .style("opacity", 0.8)
            .style("stroke","white")
            .style("stroke-width",0.3);
        });

  svg.append("path")
      .datum(topojson.mesh(data.features, function(a, b) { return a.id !== b.id; }))
       // .datum(topojson.mesh(data.features, function(a, b) { return a !== b; }))
      .attr("class", "names")
      .attr("d", path);

// Add a scale for bubble size
var valueExtent = d3.extent(tech_access_applicants, function(d) { return +d.applicants; })
var size = d3.scaleSqrt()
    .domain(valueExtent)  // What's in the data
    .range([ 3, 10])  // Size in pixel    


  svg
    .selectAll("myCircles")
    .data(tech_access_applicants.sort(function(a,b) { return +b.applicants - +a.applicants }))//.filter(function(d,i){ return i<3 }))
    .enter()
    .append("circle")
      .attr("cx", function(d){ return projection([+d.lng, +d.lat])[0] })
      .attr("cy", function(d){ return projection([+d.lng, +d.lat])[1] })
      .attr("r", function(d){ return size(+d.applicants) })
      .attr("fill", "#ff0000")
      // .style("fill", function(d){ return color(d.homecontinent) })
      .attr("stroke", function(d){ if(d.applicants>0){return "black"}else{return "none"}  })
      .attr("stroke-width", 1)
      .attr("fill-opacity", .4)
      .attr("class", "applicants")
      .attr("visibility", "visible")
      // tooltips
        .style("stroke","white")
        .style('stroke-width', 0.3)
        .on('mouseover',function(d){
          bubbleTip.show(d, document.getElementById('d3-tip-bubble'));

          // svg.call(bubbleTip);

          d3.select(this)
            .style("opacity", 1)
            .style("stroke","white")
            .style("stroke-width",3);
        })
        .on('mouseout', function(d){
          bubbleTip.hide(d);

          d3.select(this)
            .style("opacity", 0.8)
            .style("stroke","white")
            .style("stroke-width",0.3);
        });
  
    // non-applicants
  // Add a scale for bubble size
  var valueExtentCB = d3.extent(tech_access_non_applicants, function(d) { return +d.non_applicants; })
  var sizeCB = d3.scaleSqrt()
    .domain(valueExtentCB)  // What's in the data
    .range([ 1, 30])  // Size in pixel    

    svg
    .selectAll("myCircles2")
    .data(tech_access_non_applicants.sort(function(a,b) { return +b.non_applicants - +a.non_applicants }))//.filter(function(d,i){ return i<3 }))
    .enter()
    .append("circle")
      .attr("cx", function(d){ return projection([+d.lng, +d.lat])[0] })
      .attr("cy", function(d){ return projection([+d.lng, +d.lat])[1] })
      .attr("r", function(d){ return sizeCB(+d.non_applicants) })
      .attr("fill", "#9370DB")
      // .style("fill", function(d){ return color(d.homecontinent) })
      .attr("stroke", function(d){ if(d.non_applicants>100){return "black"}else{return "none"}  })
      .attr("stroke-width", 1)
      .attr("fill-opacity", .4)
      .attr("class", "crunchbase")
      .attr("visibility", "hidden")
      // tooltips
        .style("stroke","white")
        .style('stroke-width', 0.3)
        .on('mouseover',function(d){
          // bubbleTip.show(d);
          // console.log(d);
          // svg.call(bubbleTip);

          d3.select(this)
            .style("opacity", 1)
            .style("stroke","white")
            .style("stroke-width",3);
        })
        .on('mouseout', function(d){
          bubbleTip.hide(d);

          d3.select(this)
            .style("opacity", 0.8)
            .style("stroke","white")
            .style("stroke-width",0.3);
        });


    }

var crunchbaseCheckbox = document.querySelector('input[id="crunchbase_toggle"]');
var applicantCheckbox = document.querySelector('input[id="applicant_toggle"]');
crunchbaseCheckbox.onchange = function() {
  if(this.checked) {
    d3.selectAll(".crunchbase").attr("visibility", "visible");
  } else {
    d3.selectAll(".crunchbase").attr("visibility", "hidden");
  }
};
applicantCheckbox.onchange = function() {
  if(this.checked) {
    d3.selectAll(".applicants").attr("visibility", "visible");
  } else {
    d3.selectAll(".applicants").attr("visibility", "hidden");
  }    
}; 
</script>
</body>
</html>
