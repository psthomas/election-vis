## Visualizing Voter Turnout and Party Margins by County, 2004 to 2016

This is an interactive visualization built with d3.js for viewing the turnout and two party margin by county.  It covers the presidential elections for 2004, 2008, 2012 and 2016.  A blog post and demo of the project are available [here](https://pstblog.com/2017/06/05/national-election-vis).  The goal is to improve the visualization and underlying data over time, so pull requests and suggestions are welcome. 

![Example Image](example_small.jpg) 

## Features 

* Click through years to see the "dynamics" of the electorate. 
* Search by state or county to view a subset of counties. 
* Bubble areas can be set to be proportional to the fraction of national votes, fraction of electoral votes, or the Voter Power Index (VPI) for each county.  
* Electoral votes and national vote percentages are tallied in the bottom left.
* Tooltips show both the county level and state level data on mouseover.
* Click and drag counties to update the vote percentages and electoral counts if the party threshold for the state is crossed.  I find this is a good way to consider "what if" scenarios for the elections.   

## A Few Notes

* The drop in turnout happened in 2012 -- 2016 was largely about a left-right sorting of counties by size (although crucial counties like Milwaukee still saw a drop).  

* The left-right sorting is especially apparent in Midwestern swing states that gave the election to Trump.  Try searching for WI, PA, MI, IA, MN and clicking through the years to see this sorting in action. 

* The value of an additional voter is much higher in some states than others in most elections (except 2012).  To see this, weight the circles by the Voter Power Index (VPI) and click through the years.  New Hampshire, Pennsylvania, Wisconsin and Michigan dominate the voting power calculation for 2016.  Clinton would have won the 2016 election if turnout was a few points higher in just three counties: Milwaukee County WI, Wayne County MI, and Philadelphia County PA.  

* A good approach to flipping an election is to weight the circles by VPI, then click and drag the largest circles to increase the turnout or margin of the county until the electoral count changes.

## Assumptions

* I assume increases in turnout are apportioned based on the fraction of each county that voted for each party initially.  This probably underestimates the impact of increased turnout for democrats because the electorate often leans left as turnout increases. 

* Changes in margin are zero sum. Any increase in the Democrat’s vote total comes from Republican voters switching sides, not from third party candidates.


## Calculations

**Voter Power Index**:  This index an estimate of the value of additional voters in each state based on [given] [that the election turned out the way that it did] the candidate margin of victory [in each state].  Groups like [538](link) predict how likely a state is to switch between the candidates in order to calculate the VPI, but this takes a simpler approach as outlined at [DailyKos](http://www.dailykos.com/story/2016/12/19/1612252/-Voter-Power-Index-Just-How-Much-Does-the-Electoral-College-Distort-the-Value-of-Your-Vote).  This equation calculates the VPI and apportions it to each county based on the fraction of the state's votes:

* `VPI = (county_number/state_number) * (state_electoral_votes/(Math.abs(num_state_dem-num_state_rep)))`

**Electoral Weighting**:  This weighting splits the electoral college points among the counties based on their fraction of the state vote: 

* `electoral_weighting = (county_number/state_number)*state_electoral_votes`

**Vote Weighting**: This approach sizes the circle area in proportion to the total votes in the county.

**Dragging Circles**: When the user drags a circle, these equations are used to recalculate the county level data.  These updates and others happen in the `dragged()` function in the code:

* `new_county_number = new_turnout*county_voting_age_population`

* `new_dem_number = (old_dem_fraction + dem_margin_change/2)*new_county_number`

* `new_rep_number = (old_rep_fraction - dem_margin_change/2)*new_county_number`
  

## Data Issues

I'm fairly confident that the aggregate data are accurate because vote counts and electoral outcomes are similar to those of David Leip's [Election Atlas](http://uselectionatlas.org/).  But even if the aggregates are accurate, it's still possible that there are problems at the individual county level.   

The turnout exceeded 100% in 16 counties, which I made note of and filtered out in the Jupyter notebook.  This issue is either caused by bad county level vote tallies or bad voting age population data.  I think the latter is most likely, as I had to use the 2005-2009 American Community Survey average estimates for the 2004 and 2008 elections.  It's possible that the individual year estimates exist somewhere, I just couldn't find them.  I relied on kyaroch's [GitHub repo](https://github.com/kyaroch/2012_and_2016_presidential_election_results_by_county) for the 2012 and 2016 data.  The author uses the annual voting age population data and voting data from The Guardian and the Census Bureau. 

One final thing to mention is the [distinction](http://www.electproject.org/home/voter-turnout/faq/denominator) between Voting Age Population (VAP) and Voting Eligible Population (VEP).  VEP estimates remove non-citizens, felons (depending on state law), and other groups that are ineligible to vote.  This means that using the VAP data could underestimate turnout in counties with e.g. high felony convictions.  The Sentencing Project [estimates](http://www.pewtrusts.org/en/research-and-analysis/blogs/stateline/2016/10/10/more-than-six-million-felons-cant-vote-in-2016) that 6 million felons were ineligible to vote in 2016, so the effect on estimated turnout could be substantial.  Unfortunately, VEP data isn't available at the county level so I used VAP data instead.  This might be preferable in some ways though because it highlights a problem -- close to 2.5 percent of the US Population isn't being represented by their government.      

## Sources

2004-2008 County Voting data: https://github.com/helloworlddata/us-presidential-election-county-results

2005-2009 County VAP data: https://www.census.gov/rdo/data/voting_age_population_by_citizenship_and_race_cvap.html

2012-2016 County Voting and VAP data: https://github.com/kyaroch/2012_and_2016_presidential_election_results_by_county