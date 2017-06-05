#Pikes Peak Marathon 

## Udacity Data Visualization With D3.js

Sections:

* <a href = "#WhyPP">Why Pikes Peak? </a>

* <a href = "#DataPrep"> Data Prep </a>

* Explorations and Iterations on Visualization Race Results

	* <a href = "#FirstVis"> First Exploration </a>
	* <a href = "#SecondVis"> Second Exploration </a> 
	* <a href = "#ThirdVis"> Third Visualization </a>
	* <a href = "#FourthVis"> Fourth Visualization </a>
	* <a href = "#FifthVis"> Fifth Visualization - First Iteration </a>
	* <a href = "#SixthVis"> Sixth Visualization </a>
	* <a href = "#SeventhVis"> Seventh Visualization </a>
	* <a href = "#EigthVis"> Eigth and Final Visualization </a>




<h2 id="WhyPP"> Why Pikes Peak? </h2>
The Pikes Peak Marathon is an 26.2 mile race in the front range of Colorado. The race begins in the downtown area of Manitou Springs where runners parade through the streets before shortly getting off the pavement and starting the ascent of Pikes Peak on trails.  Pikes Peak is a rather prominent mountain, reaching altitudes of over 14,000 feet.  Runners of the marathon must both ascend and descend the mountain on the same trail.  Not only battling the oxygen deprivation of altitude, but skirting around racers traveling in the opposite direction, presents its own challenge on sections of narrow trail above the 12,000 foot mark.  The race has a festival feeling as you reach the highest of altitudes on course and cheering spectators line the course to watch.  

I chose to work with data from the Pikes Peak Marathon for several reasons: the length of its history, the moderate size of each years participant pools, the rather selective nature of the race (so that the fitness level of each age would be less spread out), and because I had a few questions that were already interesting to me.  One in particular was whether or not he demographic has shifted over the years and if so, how this would affect average race finish times.  

<h3 id = "DataPrep"> The data that was gathered was scraped from <a href = "http://www.pikespeakmarathon.org"> here </a>  using Python with the packages "urllib" and "BeautifulSoup".  After scraping, there was much cleaning and preprocessing that had to be done to extract only the results table and format it correctly for processing.  The work and code can be found in <a href="./PP_Webscrape.ipynb"> PP_Webscrape.ipynb </a> for scraping and preprocessing and in <a href="./Data_Manipulation.ipynb">Data_Manipulation.ipynb</a> for manipulation of the individual records into variations including the age grouping the is used for the final visualization.   Since there were some exploratory visualizations, the final data set ended up begin different than the initial sets.


<h3 id="FirstVis"> First Exploration </h3>

The initial exploratory plot was just to see if there was any trending in age and/or finish time as the years progressed.  

![image](pic_index_01.png)

Clearly, there is an overall trending in the data for the average age of participants to increase as well as the overall average finish time as one would expect with an increasing age profile in an athletic event such as this. (Live working script: <a href="./index_01.html">First</a>)

<h3 id="SecondVis"> Second Exploration </h3>

For the second visualization, I was trying to see how much variation there was with each year at each age and within each age for the various years.


![image](pic_index_02.png)

Even among the more populated ages of 30-40 years, there was a large variability from year to year in the average finish time.  The more extreme ages showed the highest greatest variability, but as will be shown this is probably due to the small sample size of each age near the extremes.  As a reference the overall average finish time across the 30 years at each age is also shown in green and when a mouse-over is performed on each curve the year it corresponds to is displayed. (live working script: <a href="./index_02.html">Second</a>)

<h3 id="ThirdVis"> Third Visualization </h3>

![image](pic_index_03.png)

<h3 id="FourthVis"> Fourth Visualization - Scatter Plot</h3>


I created this visualization so that I could use the year selector to thumb through the scatterplots of age and finish times for the various years.


![image](pic_index_04.png)

<h3 id="FifthVis"> Fifth Visualization - First Iteration For Final Plot </h3>

![image](pic_index_05.png)

<h3 id="SixthVis"> Sixth Visualization </h3>

![image](pic_index_06.png)

<h3 id="SeventhVis"> Seventh Visualization </h3>

![image](pic_index_07.png)
This visualization was to simplify the moving boxes that complicated the previous animation.  Instead plotting a smoothed distribution curve focuses the reader to notice the changes in the overall shape of the distribution as years increase and not what is happening to each individual age group. (live working script: <a href = "./index_07.html">Seventh</a>)


<h3 id="EigthVis"> Eigth and Final Visualization </h3>

![image](pic_index_08.png)




The previous couple of plots again have too much going on and it is difficult to decipher what the plot is trying to get across to the reader since they must expend some effort to figure out that the animation is a chronological movie.  The diverging colors were chosen to make visual separation of the years easier. (live working script: <a href = "./index_08.html">Eigth</a>)




  <meta charset = "utf-8">
  <script src="./d3.v3.min.js"></script>
  <script src="./d3-timer.v1.min.js"></script>
  <script src="./d3-color.v1.min.js"></script>
  <script src="./d3-interpolate.v1.min.js"></script>
  <script src="./d3-scale-chromatic.v1.min.js"></script>

<style>
.axis path,
.axis line {
  fill: none;
  stroke: black;
  shape-rendering: crispEdges;
}

</style>

<body>
  
</body>
  <script type="text/javascript">
  var summary_data;
  var avg_data;

    var width = 800,
        height = 500,
        top_margin = 75,
        left_margin = 75,
        bottom_margin = 75,
        right_margin = 75,
        canvas_top = top_margin,
        canvas_bottom = height - bottom_margin,
        canvas_left = left_margin,
        canvas_right = width - right_margin;

    var canvas_width = width - right_margin - left_margin;
    var canvas_height = height - top_margin - bottom_margin;
    var vertical_shelf = height - top_margin;

    //x scale axis
    var xScale = d3.scale.linear()
                .domain([10,85])
                .range([canvas_left, canvas_right]);

    var yScale = d3.scale.linear()
                .domain([0,50])
                .range([canvas_bottom, canvas_top]);
    var y_rt_Scale = d3.scale.linear()
                .domain([0,8])
                .range([canvas_bottom, canvas_top]);

    var y_rt_Axis = d3.svg.axis().scale(y_rt_Scale).orient("right");
    var xAxis = d3.svg.axis().scale(xScale).orient("bottom");
    var yAxis = d3.svg.axis().scale(yScale).orient("left");

    var svg = d3.select("body").append("svg").attr("width", width).attr("height", height);

    svg.append("rect")
      .attr("width", "100%")
      .attr("height", "100%")
      .attr("fill", "rgb(224,224,224");

    svg.append("g")
          .attr("transform", "translate(0,"+yScale(0)+")")
          .attr("class", "axis")
          .call(xAxis.ticks(10))//;
          .append("text")
            .attr("x", (left_margin + canvas_width) / 2)
            .attr("y", bottom_margin / 2)
            .text("AGE (years)");
        //y-axis
    svg.append("g")
          .attr("transform", "translate("+ canvas_left +",0)")
          .attr("class", "axis")
          .call(yAxis.ticks(10))//;
          .append("text")
            .attr("x", 0)
            .attr("y", -20)
            .attr("transform", "translate("+-left_margin/4+","+ yScale(16)+")rotate(-90)")
            .text("Percent of Runners");


        //y-axis
        svg.append("g")
          .attr("transform", "translate("+ canvas_right +",0)")
          .attr("class", "axis")
          .call(y_rt_Axis.ticks(10))//;
          .append("text")
          .attr("x", 0)
          .attr("y", -20)
          .attr("transform", "translate(" + 20 + "," + y_rt_Scale(6) + ")rotate(90)")
          .attr("fill", "black")
          .text("Average Time (hours)");

      g = svg.append("g").attr("transform", "translate(0,0)");

  var pdf = svg.append("g");

function update(data, year){

  var transition_duration = 200;

  d3.select("#year"+(year-1))
              .remove();
  d3.select("#year"+2016).remove();

  svg.append("text")
              .attr("id", "year"+year)
              .attr("x", xScale(35))
              .attr("y", yScale(55))
              .attr("font-size", 30)
              .text("Pikes Peak Marathon"); 
  
      filtered_data = data.filter(function(d){ return d.YEAR == year;});
      count_data = filtered_data.map(function(d){ return {AGE_GROUP: d.AGE_GROUP, AGE_GROUP_COUNT: d.AGE_GROUP_COUNT};});
      //debugger;


    //x scale axis
TOTAL_RUNNERS = d3.sum(filtered_data, function(d){return d.AGE_GROUP_COUNT;});

   
    avg_age = d3.sum(filtered_data, function(d){return d.AGE_GROUP * d.AGE_GROUP_COUNT / TOTAL_RUNNERS;});
    avg_time = d3.sum(filtered_data, function(d){return d.AGE_GROUP_COUNT * d.AGE_GROUP_AVG_TIME / TOTAL_RUNNERS;});
    svg.append("circle")
      .attr("cx", xScale(avg_age))
      .attr("cy", y_rt_Scale(avg_time))
      .attr("r", 4)
      .attr("stroke", "black")
      .attr("stroke-width", 1)
      .attr("fill", d3.interpolateBrBG(1 - (2016 - year) / 30))
      .transition();
       // .delay(1500)

      
        
      var line = d3.svg.line()
            .interpolate("basis") //http://www.d3noob.org/2013/01/smoothing-out-lines-in-d3js.html
            .x(function (d){return xScale(d.AGE_GROUP);})
            .y(function (d){return yScale(d.AGE_GROUP_COUNT / TOTAL_RUNNERS * 100);});

      svg.append("path")
        .attr("d", line(filtered_data))
        .attr("stroke", d3.interpolateBrBG(1 - (2016 - year) / 30))
        .attr("stroke-width", 3)
        .attr("fill", "none");






};

function create_legend(){
    var g = svg.append("g").attr("class", ".legend").attr("transform", "translate(" + xScale(55) + "," + yScale(45) + ")");

    g.append("rect")
            .attr("class", "legend")
            .attr("x", -20)
            .attr("y", -35)
            .attr("width", 250)
            .attr("height", 115)
            .attr("stroke-width", 1)
            .attr("stroke", "black")
            .attr("fill", "white");
    for (ii = 1976;ii<=2016;ii++){
          g.append("rect")
            .attr("class", "legend")
            .attr("x", (ii - 1976) * 5)
            .attr("y", 30)
            .attr("width", 5)
            .attr("height", 25)
            .attr("fill", d3.interpolateBrBG(1 - (2016 - ii) / 30));
    };
    g.append("text")
            .attr("x", 50)
            .attr("y", -15)
            .text("LEGEND");

    g.append("text")
            .attr("x", 0)
            .attr("y", 70)
            .text("1976");
    g.append("text")
            .attr("x", 175)
            .attr("y", 70)
            .text("2016");

    g.append("circle")
            .attr("cx", 0)
            .attr("cy", 10)
            .attr("r", 4)
            .attr("fill", d3.interpolateBrBG(1 - 20/30))
            .attr("stroke", "black")
            .attr("stroke-width", 1);
    
    g.append("text")
            .attr("x", 5)
            .attr("y", 15)
            .text("(Avg Age, Avg Time)");
    //g.append("text")
    //    .attr("x", 25)
    //    .attr("y", 15)
    //    .text("(Avg Age, Avg Time)");
    
};

create_legend();

  var year = 1976;

  d3.csv("age_summary.csv", 
    function(d){ 
      d.AGE_GROUP_COUNT = +d.AGE_GROUP_COUNT; 
      d.YEAR = +d.YEAR; 
      d.AGE_GROUP = +d.AGE_GROUP; 
      d.AGE_GROUP_AVG_TIME = + d.AGE_GROUP_AVG_TIME; 
      return d;
    }, 
    staging);



function staging(data){
  for (ii = 1976;ii <= 2016; ii ++){
    update(data, ii);

  };

  //d3.interval(function(){
  //  pre_update(data);
//}, 200)
};


  function pre_update(data){
   
    

    update(data, year);
    console.log(year);
    if (year == 2016){year = 1976;}
    
  };





  </script>



