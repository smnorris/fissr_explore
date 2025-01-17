Big picture here is to build evidence based approach to the parameters used to model the best habitat for individual fish species to help make decisions around where to invest in aquatic restoration activities (along with lots of others factors of course).  [`bcfishpass`](https://github.com/smnorris/bcfishpass/tree/main/02_model/parameters_newgraph) will except habitat parameters that allow us to model the quantity and quality of potential spawning and rearing habitat upstream of culvert locations. How do we come up with those parameters? 

<br>

Using the electrofishing data from British Columbia databases (supplied by [Robin Munro](https://dir.gov.bc.ca/gtds.cgi?esearch=&updateRequest=&view=detailed&sortBy=name&for=people&attribute=display+name&matchMethod=is&searchString=Robin+Munro&objectId=125664) from the BC Ministry of Environment) this work intends to tie fish density to habitat characteristics. Gradient is easy in the freshwater atlas, and discharge data is available for some watersheds from [PCIC](https://www.pacificclimate.org/data/gridded-hydrologic-model-output).  We are developing methods to estimate [channel width](https://github.com/smnorris/bcfishpass/tree/main/01_prep/habitat/02_channel_width) using [Bayesian modelling](https://github.com/NewGraphEnvironment/fish_passage_bulkley_2020_reporting/blob/master/docs/channel-width-21.pdf) of watershed size, precipitation and potentially other factors. We hope to step back to using parameters that have fed the PCIC discharge data to build discharge estimates raw when time and funding allows. 

<br>

[Joe Thorley](https://github.com/poissonconsulting) had a great point when he said that though we are starting with BC data we will keep our minds open to ways of bringing in datasets from elsewhere in the world in the long term. 

<br>

Through this work and numerous other initiatives [Simon Norris](https://github.com/smnorris) has been evolving [fwapg](https://github.com/smnorris/fwapg). fwapg is leveraged with [fwapgr](https://github.com/poissonconsulting/fwapgr) to provide an R Client to the database and expand the tool's functionality. To gather stream segment and [watershed characteristics](https://github.com/smnorris/fissr_explore/tree/master/scripts) related to the electrofishing density points that were shared with us by the province these tools are expanding the information in the dataset. The resulting outputs are constantly evolving with initial versions undergoing defensible magic in [fissr-explore-21](https://github.com/poissonconsulting/fissr-explore-21).
