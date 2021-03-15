**LOCATION, LOCATION, LOCATION**\
**General Assembly Data Science Immersive**\
**Final Capstone Project (completed February 2021)**
\
\
\
**PROBLEM STATEMENT**\
It’s often said that location is the single most important factor in determining the value of a London property. But to what extent can the price be predicted simply from its location, without access to metrics such as square footage, overall quality, and the number of bedrooms? 

Another way to view the same problem:  if a property were to move from one postcode to another, how much would its value change? The most expensive flat currently advertised on Rightmove is located in Mayfair, with an asking price of £11,000,000. The cheapest is in Eltham, selling for £110,000. If the Mayfair flat were to be dismantled brick by brick, and reassembled in Eltham, how much of its value would have been destroyed? 

The goal at the end of this project is to write a function that will ‘move’ any property from one London postcode to another, and calculate by how much its value would have increased or decreased.

<img width="1195" alt="two_flats" src="https://user-images.githubusercontent.com/64950454/110815054-f9e72800-8281-11eb-929b-903cd058d2dd.png">



\
**BACKGROUND AND COMMERCIAL RELEVANCE**\
Anyone buying or selling a property needs to estimate its correct market value. Estate agents do this by looking at similar properties that have sold in the area, using them as  benchmarks.  

The problem is that a neighbouring house may be quite different, and an identical house that sold a few hundred meters away might have better access to shops, tubes, schools and so on. Among all the properties in a neighbourhood, the few that have sold recently invariably give rise to a sparse matrix. Estate agents try to plug the gaps with their ‘expertise’, a process which is much less scientific than they tend to imply.

If I can determine the relative values of all London postcodes, then this sparse matrix problem can be solved simply by finding an equivalent property that has sold in a different postcode, and then adjusting its price by the correct amount. Creating an ‘exchange rate’ for postcodes will help buyers and sellers to know where they stand when pricing or bidding on a property.

To do this, I will need to quantify the desirability of all of London’s 200,000 unique postcodes. Each postcode will need to be assessed against a range of measures that drive the decisions of buyers and sellers.


\
**PROCESSING THE DATA AND ENGINEERING FEATURES**\
This repository contains twenty numbered notebooks, which can be followed in order or viewed individually. Some of them will run as they are, others need large files which I would be very happy to provide - if required, please contact me [directly](https://www.linkedin.com/in/david-rhode/).

I began by sourcing the records for over 300,000 transactions from the Land Registry, spanning a period of five years to March 2020. These included sale price and postcode, but nothing on square footage, quality and so on. The basic cleaning of this data can be found in the [first notebook](https://github.com/david-rhode/DSI15-capstone-project/blob/main/1_property_transactions.ipynb).


I chose this five-year period for several reasons.

- It provides enough data to draw conclusions confidently, while remaining relatively easy to work with.
- The final cut-off of March 2020 means that I don’t have to account for any market disruption caused by COVID-19.
- London property prices were relatively stable during this period (given the number of exogenous variables I didn’t want to have to build a time-series model to deal with any underlying trends).

My initial assumption was that property prices would correlate with two types of factors. First, the economic status of its residents (wealthy buyers can afford high prices). Second, the features that define objectively how desirable a location is (transport links, good schools etc). I assumed a degree of interaction between these two groups (better amenities drive prices higher, attracting wealthier residents and so on). My plan was to engineer features derived from postcodes that fall into five categories: economic issues, crime, education, transport, and centrality. Please see the [data dictionary](https://github.com/david-rhode/DSI15-capstone-project/blob/main/21_data_dictionary.ipynb) for a full list.



**Economy**\
The Ministry of Housing, Communities and Local Government supplies data on the many ways that people can experience deprivation as a result of economic factors - health, education, employment, living environment, and financial. This information is available for each London LSOA (an administrative unit of about 1500 people).  It’s released every few years, the 2019 figures falling within the relevant time period. Comparisons with previous years don’t suggest any obvious anomalies (ie no neighbourhoods making sudden progress relative to others).

These official statistics broadly captured the social and economic status of specific areas, although it’s possible that as neighbourhoods gentrify, the official figures may lag behind and not truly reflect how desirable a location has become. In the absence of any official measure of gentrification, I looked for other indicators - concentrations of [florists](https://github.com/david-rhode/DSI15-capstone-project/blob/main/4_scraping_florists.ipynb), [delicatessens](https://github.com/david-rhode/DSI15-capstone-project/blob/main/5_scraping_delicatessens.ipynb), [estate agents](https://github.com/david-rhode/DSI15-capstone-project/blob/main/3_scraping_estate_agents.ipynb), and smart [restaurants](https://github.com/david-rhode/DSI15-capstone-project/blob/main/2_scraping_restaurants.ipynb). I scraped their postcodes from All In London and The Good Food Guide, and aggregated them by ward (an administrative area of about 10,000 people). The processing of the economic data can be viewed [here](https://github.com/david-rhode/DSI15-capstone-project/blob/main/11_deprivation_data.ipynb).

**Education**\
The best schools in London are all heavily oversubscribed, and living within the general catchment area is no guarantee of getting a place (in spite of what estate agents might suggest when pushing for a sale).  Statistics on the average distance travelled to school suggest that living within 500m should almost always guarantee a place. Ofsted provides data on which schools are rated ‘Outstanding’, including their postcodes. GeoPandas allowed me to draw a circle of radius 500m around those schools, to create a feature that determines whether any specific property entitles its occupants to a place at an ‘Outstanding’ school. Processing of the schools data can be viewed [here](https://github.com/david-rhode/DSI15-capstone-project/blob/main/6_processing_schools.ipynb).

**Transport**\
Proximity to transport links and easy access to the centre of town is almost always cited in surveys looking at what makes a property desirable. GeoPandas let me draw circles of varying radius around each London tube station. It’s possible to check if a property is within 50m of a tube (bad news presumably), 200m, 400m and so on. I modelled features up to a radius of 2400m, on the basis that a 1.5 mile walk (over 20 minutes) is about the most than anyone will consider walking to the tube. Processing of the tube data can be viewed [here](https://github.com/david-rhode/DSI15-capstone-project/blob/main/9_processing_tubes.ipynb).

**Centrality**\
Aside from its transport links, a property is likely to be more expensive if it boasts a central location. Trafalgar Square is generally cited as the most central point in London, GeoPandas made it simple to calculate how far any given property was from this location. The processing of this feature can be viewed [here](https://github.com/david-rhode/DSI15-capstone-project/blob/main/12_centrality.ipynb).

**Crime**\
The police provide monthly crime statistics for each LSOA going back to the start of 2018. It seemed obvious that more crime would make an area less desirable. However, I found that aggregating the [police data](https://github.com/david-rhode/DSI15-capstone-project/blob/main/7_processing_crime.ipynb) showed a positive correlation between crime levels and property prices (i.e. more crime was associated with higher prices). A closer examination revealed that this was due to high levels of crime in the West End, an area with high property prices - much of it presumably directed against tourists. I looked at a few different approaches to get round this problem.

The first was to look at whether specific categories of police data would work better (for example one of the categories that they segment is ‘Violence and Sexual Offences’). However, this was met with the same problem, high volumes of offences in the West End. Unfortunately, the police don’t divide their data into categories that match the concerns of house buyers (who most likely are focused on local gang-related problems rather than the wellbeing of tourists). 

Another approach was to look at public attitudes towards crime, rather than crime statistics themselves. The London Mayor’s Office for Policing and Crime conducts [quarterly surveys](https://github.com/david-rhode/DSI15-capstone-project/blob/main/8_processing_perception_crime.ipynb) asking people how safe they feel locally, whether gangs are a problem in their area and so on. The limitation to this data is that it is less granular than I would have liked, collected across boroughs rather than LSOAs or wards. However, it did appear to have some predictive value.

A final option was to source data from the London Assembly that deals specifically with [knife and gun crime](https://github.com/david-rhode/DSI15-capstone-project/blob/main/10_knife_gun.ipynb), segmented by ward. This was only available as a rolling twelve-month average. However, there was no reason to think this was invalid, as I couldn’t find any evidence to suggest that specific areas have improved relative to others. The knife crime data suffers from the same problem as the police crime statistics (lots of activity in the West End), but gun crime doesn’t (it’s most likely associated with local turf wars over drugs). The gun crime data emerged as the best option, along with the attitudes to crime surveys. 

\
**ASSEMBLING THE DATA AND EDA**\
After engineering the features it was time to assemble them all into one dataframe and to produce visualisations. The code for constructing the dataframe is [here](https://github.com/david-rhode/DSI15-capstone-project/blob/main/13_constructing_main_dataframe.ipynb).

The first part of the EDA (histograms of features, correlation barchart, heatmap) can be viewed [here](https://github.com/david-rhode/DSI15-capstone-project/blob/main/14_EDA_part_1.ipynb).

The second part of the EDA (geographic visualisations of the feature variables) can be viewed [here](https://github.com/david-rhode/DSI15-capstone-project/blob/main/15_EDA_part_2.ipynb).

\
**PREDICTIVE MODELLING**\
The next stage was to build predictive models. First I had to filter the dataframe to include only the features engineered from the postcode (distance to schools, health scores, economic measures, and so on). Excluded were property type, transaction type, estate type etc.

Based on experience with the Ames housing dataset, I started by looking at linear models (Linear Regression, Ridge, Lasso, ElasticNet). These achieved r2 scores around 0.21.

This was slightly disappointing. I’d already checked that key variables (predictor and target) were largely static over time. However, when I looked at the information that had been deliberately excluded it became clear that an uneven spatial distribution was causing a problem. The issue was that the variable ‘property type’ was not independent of postcode - for example, in Tower Hamlets 94% of sold properties are flats, but in Kingston upon Thames the comparable figure is only 26%.

As a result of this discovery, I had to include ‘property type’ as a variable within the model. This meant that there would be no single answer to the question ‘how much will a property’s value change when it moves from postcode A to postcode B?’ Instead, there would be four answers, one for each property type (flats, terraced houses, semi-detached houses, and detached houses). The code for this part of the project can be viewed [here](https://github.com/david-rhode/DSI15-capstone-project/blob/main/16_models_part_1.ipynb).

This change allowed the linear models to improve their scores to about 0.26. This wasn’t a surprise, for the first time they were being given some limited information about size (generally a detached house is bigger than a terrace, though a flat might refer to a tiny studio or a big three bed property in a mansion block). 

Other models performed much better, with KNN scoring 0.50, and Random Forest achieving a score over 0.52. With a highly skewed target variable, it was worth trying a Power Transformation, but this didn’t help the scores.

It was interesting that the linear models had performed relatively poorly, even with regularisation. This seemed to imply that there were non-linear relationships in the data that weren’t being captured by the linear models. I tried applying Polynomial Features (to a maximum power of 2) and this improved the scores of the linear models to around 0.30 (no improvement was seen in Random Forest). Trying interaction terms only didn’t lead to any further improvements. The modelling with Polynomial Features can be seen [here](https://github.com/david-rhode/DSI15-capstone-project/blob/main/17_models_part_2.ipynb).

Finally I experimented with a multi-layer perceptron [neural network](https://github.com/david-rhode/DSI15-capstone-project/blob/main/19_models_part3.ipynb), but failed to beat the Random Forest’s best score of slightly over 0.52. Looking at the model’s residuals, it’s clear that it has a tendency to overvalue cheaper properties, and undervalue the more expensive ones (please see conclusion for further discussion). The model’s feature importances and residuals are visualised [here](https://github.com/david-rhode/DSI15-capstone-project/blob/main/18_visualise_residuals.ipynb).

\
**ADDRESSING THE PROBLEM STATEMENT**\
Simply knowing the property type (flat, terraced house etc) and the postcode allowed me to explain away more than half the variance in the dataset (r2 score over 0.52). So how effectively is it possible to ‘move’ a property from one postcode to another?

To do this, I needed to ascribe a relative value to each of London’s 200k unique postcodes (less than 70k out of these appeared in the transaction data). I addressed this by creating a new dataframe of about 800k rows. This comprised just under 200k unique postcodes, multiplied by 4 property types (flat, terraced house etc) - essentially creating 800k fictional properties. The Random Forest model, trained on real properties, was then used to predict prices for all of them. The final step was then to write a function to ‘move’ a property, evaluate the loss or gain in value, and plot the move. The code to assemble the final dataframe and to construct the function is [here](https://github.com/david-rhode/DSI15-capstone-project/blob/main/20_final_dataframe.ipynb). This is how the function handles the two flats we started with, in Mayfair and Eltham:
\
\
<img width="568" alt="final_function" src="https://user-images.githubusercontent.com/64950454/110828692-366d5080-828f-11eb-95bb-c9cfc8c286d0.png">







\
**EVALUATION AND NEXT STEPS**\
So does this provide us with a solution to the problem of the sparse matrix, and the need to rely on the intuition/guesswork of estate agents?

The model suggests that moving the Mayfair flat to Eltham would reduce its value by 96%, from £11,000,000 to £440,000. How realistic is that? It would surely beat the ceiling price for a flat in Eltham, but probably not by much - gold taps lose much of their value if they are taken out of a postcode that billionaires want to live in. The most expensive flats in Eltham are currently advertised for over £500,000 (asking prices, not confirmed sales) so it looks like the model slightly overstates how much value was destroyed (94-95% is probably more accurate). It’s a good start, but it wouldn’t be wise yet to trust it with real money. What needs to be done to reach that stage? A clue lies in the model’s residuals.

<img width="447" alt="residuals vs sale price" src="https://user-images.githubusercontent.com/64950454/110828995-8c41f880-828f-11eb-9cf9-63fb80cf6257.png">

<img width="431" alt="Screenshot 2021-03-15 at 12 39 22" src="https://user-images.githubusercontent.com/64950454/111154892-a16aa000-858b-11eb-8e0b-9f5a86a87ec0.png">



\
It’s telling that the model tends to overvalue the cheaper properties, and undervalue the more expensive ones. This tendency remains following a Power Transformation on the target variable. It suggests that there are interactions occurring between the information in the dataset, and data that is unavailable to the model. Most obviously, it’s improbable that the overall quality of a property doesn’t correlate at all with its postcode - it makes sense that properties in more desirable postcodes tend to be done up to a higher standard.

Earlier, it wasn’t possible to simply answer the question ‘How much will a property’s value change when it moves postcodes?’ - the question needed four answers, one for each property type. It’s possible that something similar applies here with regards to overall quality.

The features that were engineered from the postcodes work well on their own terms. To get real commercial value from them though, the next step would be to layer them on top of the more traditional metrics that the model didn’t have access to (overall quality, size etc). Once the model is trained on this extended dataset, it could effectively ‘move’ any property between postcodes as long as we know that property’s size, quality and so on. 

In addition, there are a couple of minor improvements that would be easy to make. Overground train stops could be added to the data on tubes. Location data for parks and the river would also be helpful. 




