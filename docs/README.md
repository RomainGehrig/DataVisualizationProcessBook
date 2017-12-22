# Process book
##Table of Contents:
[TOC]

## Project Proposal

### Overview
For this project we would like to present a visualization that one can use to identify patterns in delayed flights.

### Motivation
We were motivated for this project as we often face flight delays when travelling, and it is very annoying. Having such a tool could be very useful as can show travelers the likelihood of a delay based on regional and temporal trends. This can help you make a choice of which airport to use, which airlines to use and even the best time to take a flight, such that you minimize the likelihood of a delayed flight.

### Target Audience:
Travelers

###Related work and inspiration:
One of the inspirations behind the project is [FlightRadar24](https://www.flightradar24.com/), which is a tool that enables anyone to see the real time position of a plane. Not only does this website have a very appealing and easy to understand visualization, but it has become a very popular tool for people to check where a particular plane has reached on its flight path. We would love it if our visualization could have a similar feel to it, but with a focus on airports instead of airplanes.
The following are a couple of projects we found during our research to find related work:

####Tufts Enigma
![Tufts Enigma Visualization](https://raw.githubusercontent.com/sourabhlal/FlightDelaysViz/master/images/tuft.png)

In this [visualization](http://tuftsenigma.org/airport-flight-delays/), there is a chord diagram that shows the 100 airports with the most traffic in the US. It highlights which routes have the least-most delay with a colour-scale from green to red. Further you can choose what time period you want to see the analysis for.

#### IBM Watson Analytics
![IBM Watson visualization](https://raw.githubusercontent.com/sourabhlal/FlightDelaysViz/master/images/watson.png)

In this [visualization](https://www.ibm.com/communities/analytics/watson-analytics-blog/visualizing-network-data-to-illustrate-airline-delays/), the designer used IBM Watson to create a cluster of bubbles where the bubble size represents the average delay from that airport in minutes.

### Questions: What am I trying to show in this viz?
* Which airports are have the most/least delays?
* Which airlines has the most/least delays?
* Which airports catch-up the most? (reduce delay between departure delay and arrival delay)
* Which airlines catch-up the most? (reduce delay between departure delay and arrival delay)
* Can we see which region or country has the greatest delay?
* When are delays at their most? (Time of day/week/year)
* Can we identify particular events just by looking at the delays provoked (For example when the volcano Eyjafjallajökull erupted in Iceland causing severe disruptions across Europe)


### Dataset: where does it come from, what are you processing steps?
We considered a couple of datasets for this project, but we could not find one that had global data. Eventually we decided to use the TranStats database provided by the United States Department of Transportation. In particular we used a dataset from this database titled Flights On Time performance.

 You can download the data over [here](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

## Data Processing and Exploratory Data Analysis

### Data Processing
{TO BE ADDED}

The raw data were coming as zipped files, each containing a CSV with one month worth of data. The size was quite consequent with 8MB archives expanding as 200MB CSV files. For the processing, we used Python and Pandas (as we are familiar with the tool) to extract the data we wanted because there is way we could have served the raw information over the network and process it in Javascript in real time. The plan was to make an aggregation by month of the data we needed and discard unused columns

CSV to JSON for Sankey, CSV to CSV for the rest

* We also checked the data for unusual values, for example we had NaNs for delay time when there was no delay or no data available.

* We found some airports with incomplete data as well - they had data about their delay but not the cause of the delay - this is something we have to handle when preparing the visualization.

### Exploratory Data Analysis
* Our data source provided us with a list of flights and the time data associated for each. The website was quite well designed and we could choose exactly what fields we wanted to download. We decided to use the following fields:
 1. Flight Origin (FAA code)
 2. Flight Destination (FAA code)
 3. Arrival Delay (in minutes)
 4. Departure Delay (in minutes)
 5. Cause of Delay
	 1. Carrier Delay (in minutes)
	 2. Weather Delay (in minutes)
	 3. NAS Delay (in minutes)
	 4. Security Delay (in minutes)
	 5. Late Aircraft Delay (in minutes)

* We performed our initial Data Exploration using Python, and in particular Pandas to examine the data. We created some simple plots to see how variable the data was for a given airport across time and how different were the values across many airports at a given point in time.

![Example plot of seeing how much the delays varied by destination](https://raw.githubusercontent.com/sourabhlal/FlightDelaysViz/master/images/psp-depdelay.png "Seeing how much the delays varied by destination")

![enter image description here](https://raw.githubusercontent.com/sourabhlal/FlightDelaysViz/master/images/psp-sfo-monthdelay.png "Seeing how much the delays varied by time of year")

* We also discovered that the dataset did not include the longitude and latitude co-ordinates for each airport - something that we need in order to mark them on a map. We therefore decided to augment our dataset by scraping data from the [TravelMath Airport Calculator](https://www.travelmath.com/airport/).

## Did you deviate from your initial proposal?
Yes we made some changes - we dropped questions about airlines as it made visualization too cluttered. we felt that there was too much information to show, and we couldn’t add more visuals to our site. Therefore our focus was only on airports.

We also modified the target audience. Once we had a closer look at the data, we realized we could examine the root cause of delays at particular airports, and how these delays spread through the network. So we believe our visualization in addition to being useful to travelers it would also be useful to airport administrations to identify what they should try to fix in order to reduce delays.

And finally we modified this research question: Which airports are have the most/least delays? By also adding a "And why?" at the end. Since we have access to the Cause of Delay data, we want to know why flights are delayed and not just how much.

## Designs: What are the different visualizations you considered?
We considered several different ways to visualize our data so that we could answer our research questions.

1. **Map Visualization**
* We started off with idea of a choropleth as we felt it would be good way to identify regions with a high average delay. Upon clicking each region we can see the individual airports in it. However we had problem as we don’t get the bigger picture. An example of where this an issue is New York City. John F. Kennedy Airport is within the state, whereas Newark Airport is across the state border. It would be a really bad visualization if someone wanted to compare the two airports but had to switch states each time.

* A second consideration for us was to use a heatmap, with markers on it for each airport, and the background colour would reflect the overall delay. However we had problem when it came to the colour coding. A heatmap would give an inaccurate representation of the data as when several airports are near each other it would greatly exaggerate the delay. Further every airport that was isolated but had a low delay would almost disappear as the colour scheme was dominated by the airports with very high delays.
{include image}

* In the end we decided to show all airports with markers. We colour coded the markers in order to provide information on the mean of the delays experienced by the flights departing from that airport and adjusted the size of the marker according to the volume of traffic passing through the airport. Further we felt that having a cluster of markers with would still provide us with information of regional trends as if such a trend exists the markers in the region should have similar colours.
* *Colour choice*: The colours of our markers vary from a pale yellow to dark red. We chose this colour scheme as it contrasted our background, and so the markers stood out. Further we used red for the most delayed airports as the colour has a negative connotation and stands for danger.
* *Marker size*: Visually highlights the more important airports. Doesn’t make a big difference if a tiny airport with 1 flight has a large delay or not, but bigger ones are more important as they are used by many more people and have a greater effect on people’s lives, and so we want these airports to stand out a bit more than the smaller ones. We classified all the airports into 5 size categories based on the volume of flight traffic passing through them in the given time period.

2. **Cause of Delay visualization**
* Initially we considered a pie chart as a way to visualize what caused the greatest amount of delay. However, as we learnt in class this wasn’t a very good visualization as angles and areas are difficult to perceive accurately when small. Further, we felt that in a pie chart while we were able to show the distribution of delay causes we could not compare this value to other airports. We needed a visualization which could also show us the total delay.
* Next we made a stacked bar graph to illustrate the amount of total delay amount, and we broke this down by cause of delay. This was much better but we felt it was not ideal as it was hard to identify persistent trends.
* We then decided to remove this breakdown from our bar graph and represent it in a colour coded table below our graph. What this did was gave an indication of what is the dominant cause for delays for this airport across the time scale. At the same time the bar graph above it still provides information on the total degree of delays experienced on that day.
*Colour choice*:  As we were using a continuous scale, we decided to a predefined color map from the D3 library. We chose the Magma colorscale as it was aesthetically pleasing with our overall website, and conveyed the crucial information efficiently. Whenever we have a delay it appears brighter than the surrounding cells.

3. **Visualizing Delay Transmission**
* We wanted a third visualization to identify which airports were able to recover from incoming flight delays. This was a lot trickier as there aren’t a lot of visualizations that could be used to represent the incoming flights and outgoing flights and all their delays. We settled on a customized Sankey diagram with the airport being examined in the center, all incoming flights on the left and all outgoing nodes on the right.
* Design choices:
  * *Colour coding of nodes*: The node colour was initially randomly distributed so that neigbouring nodes were not the same colour. However this looked confusing, as it just added noise to the visualization and took away from what we were trying to show.  We considered changing this to a plain fixed colour, but eventually decided to use the colour coding to our advantage and add some data that can lead to a more meaningful visual: the average delay at the airport. Here again we have used the D3 Magma predefined color scale.
  * *Color coding of links*: We initially started with continuous range with flights with least delay coloured green, the most delay coloured red, and in between it was yellow. This led to very visually appealing sankey graphs, but it didn’t highlight our key data.
Therefore we decided to set colour cut-offs - where the filghts regularly arriving early are shaded in shades of green, and delayed flights are in shades of red. In between we have flights that are on time.
4. Timeline Element
We wanted to be able to see how our data changed through time. To do so we liked the idea of having a timeline with a slider. Initially we planned to have it at the top of the webpage, but once we implemented it looked out of place, and so we changed it and put it at the bottom.

**The Layout**
Our idea for the webpage evolved. What was clear though was that our main interface would show the Map layout, and the other visualizations would be in a panel that could be opened and closed when a particular airport was clicked.

{add images}

## Implementation: Describe the intent and functionality of the interactive visualizations you implemented.
1. **Map view**
2. **Cause of Delay visualization**
3. **Visualizing Delay Transmission**

To colour code we had to set a minimum acceptable delay as we do not want to highlight flights that are just 1 minute late. After doing a lot of tests, we concluded that a cut off around 20 minutes lead to the most interesting results.

![](https://raw.githubusercontent.com/sourabhlal/FlightDelaysViz/master/images/graph-sankey.png)


## Evaluation

###What did you learn about the data by using your visualizations?
We were able to get alot of insight into the data just by looking at our visualizations. There were alot of patterns that would have been imperceptible by just looking at the raw data. We were able to see how delays change with time, we were able to identify airports that frequently were able to have flights depart on time despite arriving late, and vice versa see how flights arriving late at certain airports caused a bit of a domino effect on other airports. We were also able to learn about flaws in our data. For example when it came to the cause of the delay we suspect that the cause of the delay is not accurately reported. Even on days with a major storm, we saw that the weather delay was negligible and most of the delays were attributed to carrier delays.

In summary, we felt that we were able to get some interesting insights into the data

###How did you answer your questions?
Out of our original 6 questions, we discarded the 2 about airlines. We were able to answer the remaining 4 of them to some degree.

##### Which airports are have the most/least delays? And why?
We cannot directly answer this question as an overall "winner" or "loser" as we noticed that delays change with time. So what we can answer is which ones have large or tiny delays at any given point in time. This is made clear when we look at our map, and see which airports are the darkest shade of red. We can clarify our suspicion further by hovering over it and seeing the average daily delay in the side panel.

##### Which airports catch-up the most? (reduce delay between departure delay and arrival delay)
We are able to answer this question by looking at the Sankey diagram -  which shows how much of the delay is transmitted from arriving flights to departures. We were able to identify some airports were very efficient, and even though flights were departing with a delay, it was a reduction on the arrival delay.

##### Can we see which region or country has the greatest delay?
Again this is not a question that can be answered outright, however our map visualization showed us that there can be entire regions with a delay at a particular moment in time. For example the entire Eastern seaboard had huge delays during the months of June and July 2016, while the Western seaboard was relatively delay free. At other times other regions also have delays, although none as significant as this one.

#####When are delays at their most? (Time of day/week/year)
As mentioned earlier in the process book, we decided to drop the Time of Day/Week visualization. However our visualization can be used to show how delays evolve though the year by sliding the timescale. We can see that in both years delays shoot up in the summer in many airports on the east coast, and drop in the spring and autumn. We also see that delays increased during December all across the country, in particular on the west coast. We can observe this sort of behavior at multiple granularity levels such as country, region, state or even particular airport simply by zooming in and out on the map.

#####Can we identify particular events just by looking at the delays provoked (For example when the volcano Eyjafjallajökull erupted in Iceland causing severe disruptions across Europe)
Yes we are able to answer this through our visualization, although not in the way we expected. We had imagined we would see massive delays in certain regions at the time of a natural disaster, however we cant see this in our map as the minimum time period was one month. So unless the effect of an event was long lasting, we can't see it on the map, for example after Hurricane Irma in September 2017, all of Puerto Rico's airports were red. For shorter events we can observe this in the Cause of Delay visualization. We can observe certain days have much higher delays than other by hovering over an airport. We also noticed some interesting edge cases this way, for example again after Hurricane Irma, many airports in Florida were closed for a few days, and due to that the total delay on those days was 0. This is something we didn't expect and is part of the reason why it wasn't easy to view these events on the map where we look at the average delays.

###How well does your visualization work, and how could you further improve it?
We feel it works fairly well although there are several glitches that should be fixed and improvements that can be made:

 1. We were not able to tame the Sankey diagram - it drove us crazy. The D3 library sometimes made some weird looking graphs when there were alot of nodes, and we no idea how to control this. We would like to further improve this. Further, we observed that Firefox doesn't correctly render the Sankey diagram, we are unclear about why this is the case and would like to fix this.
 2. We want to increase the granularity of the timescale. Right now we limited it to a minimum length of 1 month, however to better observe trends in the data we ideally want to make it work at a 1 day granularity. The reason we did this was due to the huge amount of data pre-processing we need to do to accommodate for all permutations. Currently all our data is processed in 1 month batches.
 3. We also want to find a way to handle edge cases such as when an airport is closed due to some reason (for example airports in Florida during Hurricane Irma). Currently this closure means that there is a negative effect on our visualization and instead of increasing the average delays for the airport it reduces it. We are not sure at this point how we would fix this, but it is something we would like to work on.
 4. We would like to add a filter on the map where we can select the amount of delay and/or the airport size in order to even more clearly examine without accidentally hovering on a type of airport we don't want to see.
 5. We would love to make this work with data from the whole world, and see even more interesting global trends in aviation delays!

## Peer Assessment

| Assessment Criteria| Romain| Sourabh   | Thierry |
| -------: | :---: | :---: | :---:|
| Preparation| Yes|  Yes    |Yes|
| Contribution | Yes   |  Yes   |Yes|
| Respect for others’ ideas | Yes    |  Yes  |Yes|
| Flexibility | Yes   |  Yes   |Yes|
