# Oregon Bicycle-Motor Vehicle Collisions


## Project Description
Bicyclists and would-be bicyclists are often concerned with the risk of injury. Many assume that the main danger is being hit from behind by a motor vehicle. But we should base safety policies on data rather than popular beliefs about crash circumstances. To that end, for this article I took a deep dive into 10 years of bicyclist crash data from Oregon.

As I discussed in a [previous article based on 20 years of data from Pennsylvania](https://towardsdatascience.com/pennsylvania-bicycle-motor-vehicle-collisions-cdfcf176aa0f), official data with regard to bicycle crashes and injuries is incomplete. Most bicycling injuries are excluded from state and national databases in the U.S., because crashes are only reportable if they involve a motor vehicle in transport. Collisions between a bicyclist and a pedestrian, animal, or other bicyclist, or a single-bicycle crash (a fall or a collision with a fixed object) are excluded, even though hospital data shows that they account for the majority of emergency department visits by bicyclists.

We would like to know is the <i>crash risk</i> associated with different locations, road designs, or operator behaviors. But risk is outcome divided by exposure, and there are very few sources providing estimates of the quantity of bicycling under different circumstances.

Despite these limitations, we can learn a good deal about bicycle-motor vehicle collisions (BMVCs) from crash reports. We can look at crash prevalence: where and under what circumstances do BMVCs occur? In general we can assume that the distribution of crashes is primarily a function of the distribution of bicycle distance traveled: roads with many bicycle crashes likely have many bicyclists. Even if prevalence is not informative about individual crash risk, it does give us information about the location and circumstances of the societal problem. We can also look at the variation in the severity of injuries by modeling injury severity, given that a crash has occurred. This is not the same thing as modeling injury crash occurrence, but it can help us understand which factors make crashes more severe.

## Data
The larger project makes use of several available crash databases for U.S. states and from U.S. DOT/NHTSA. This second portion of the project uses data from Oregon, which contains rich details about crash circumstances including location, actions, direction of travel, errors, and crash causes of all crash participants, including bicyclists.

The data for this analysis comes from ODOT, which by request provided 10 years of crash data for 2009 to 2018 in Access database files for each year. I wrote a query in Access to extract the relevant bicycle-crash data and fields. Bicyclists are included in the Participant (PARTIC) table, but not in the Vehicle (VHCL) table. However, all of the relevant fields in the Vehicle table are in the Participant table. Manner of Collision (angle, rear-end, sideswipe, backing, etc.) is coded at the crash level, including for bicyclist crashes. The final dataset contains data on 9,266 bicyclists and 9,239 motorists involved in 9,180 crashes. There were 577 bicyclists with serious injuries and 87 with fatal injuries.

## Crash Prevalence

#### Vehicle and Operator Characteristics

What types of motor vehicles are involved in BMVCs? Unlike other states, Oregon does not separately code light trucks, but includes pickups, SUVs, and minivans in the category of "passenger car," which account for 97% of all motor vehicles involved in Oregon BMVCs. Less than 2% were large trucks and less than 1% were buses. When we look at fatal crashes only, the large truck share is 14%, but no buses were involved in fatal BMVCs.

Only 0.9% of drivers involved in BMVCs were known to be impaired by alcohol and only 0.4% by drugs. When we look at fatal injury crashes, this rises to 9% using alcohol and 15% using drugs. Only 1.5% of drivers were speeding overall, but 12% were for fatal crashes.


#### Location Characteristics

About 72% of Oregon bicycle-motor vehicle collisions (BMVCs) occur on city streets, 17% on what ODOT classifies as "urban highways," 7% in suburban areas, and 4% in rural areas.

**Crash Prevalence by Urban, Suburban, or Rural Character: All Crashes**
![Crash Prevalence by Urban/Rural](/images/RoadClass.png)

Considering only crashes that resulted in serious or fatal injuries the rural share is dramatically higher, 13% instead of 4%. The suburban share is modestly higher, and the city share is lower, but still dominant. However, considering fatalities only, as shown in the figure below, the rural share is the largest at 40%, which is almost the same as what I found in the Pennsylvania data, even though rural areas accounted for a much greater share of all BMVCs in that state.

**Crash Prevalence by Urban, Suburban, or Rural Character: Fatal Crashes**
![Crash Prevalence by Urban/Rural, Serious and Fatal Crashes Only](/images/RoadClassFatal.png)

The posted speed limit was coded for only 48% of crashes. Therefore the following statistics are based on only about half the data. One-third of BMVCs in Oregon occur in locations where the posted speed limit is 25 mph or less, compared to two-thirds in Pennsylvania. About 43% occur on 30-35 mph roads (compared to a quarter in Pennsylvania). In Oregon, 15% of BMVCs occur where the posted speed limit is 40-45 mph, and 8% occur on roads with 50 mph or greater speed limits.

**Crash Prevalence by Posted Speed Limit: All Crashes**
![Crash Prevalence by Urban/Rural](/images/PSL.png)

The posted speed limit is 25 mph or less in 10% of *fatal* BMVCs, 30-35 mph in 32% of fatal crashes, 40-45 mph in 25%, and 50+ mph in 34% of fatal BMVCs. Clearly higher speed limit roads are hugely overrepresented among fatal crashes, and many of these high-speed roads are in rural areas.

**Crash Prevalence by Posted Speed Limit: Fatal Crashes**
![Crash Prevalence by Urban/Rural](/images/PSL_Fatal.png)

#### Manner of Collision
The ODOT data contains "Manner of Collision" for BMVCs. In the Oregon coding, turning collisions are a separate category whereas in Pennsylvania and most other states they are included as "angle" collisions. As shown in the figure below, 60% of BMVCs involve turning movements and 29% are angle collisions that do not involve turning (e.g., participants approaching on crossing paths from different legs of an intersection). Together these account for 89% of crashes, even higher than the 70% share in the Pennsylvania data. As in Pennsylvania, the next most common types are sideswipe - same direction and rear-end collisions, together accounting for about 10%.

**Manner of Collision: All Crashes**
![Collision Type](/images/CollisionType.png)

Turning movement and angle collisions together account for 50% of fatal BMVCs, making them the most common type if we consider them in combination, as most other states do. Rear-end collisions account for 30%, and another 6% are sideswipe - same direction. Head-on and sideswipe - opposite direction collisions, both involving one party on the wrong side of the road, together account for 12% of fatalities. (These figures are based on the database coding. I did a closer examination of fatal collisions, described below, which revealed that there were two collisions, one angle and one fixed object, that could be better classified as rear-end.)

**Manner of Collision: Fatal Crashes**
![Collision Type](/images/CollisionType_Fatal.png)

#### Intersection Collisions: Crossing or Parallel Paths
Intersections account for 67% of BMVCs, and driveways for another 19%, for a combined total of 86% -- an even higher share than the 72% I found in the Pennsylvania data. Although only 14% of crashes occurred between intersections, 59% of *fatal* crashes were at midblock locations, a bit higher than in Pennsylvania, where 50% were midblock.

Let's take a deeper look at intersection crash circumstances. A first question is whether the participants were initially traveling along crossing or parallel paths. The ODOT dataset gives the "from" and "to" compass direction of crash participants, including bicyclists. I used this information to classify BMVCs as either parallel path (crash participants traveling in the same or opposite direction) or crossing path (all other crashes). I found that crossing paths account for 60% of intersection and driveway BMVCs (52% of all); and parallel paths (in the same or opposite direction) account for 40% (34% of all).

**Crossing Paths at Intersections and Driveways**

When motorists and bicyclists are on intersecting paths, there are right-of-way rules that control who may proceed and who has to yield. In 43% of the 4,803 crossing paths collisions, the bicyclist failed to obey a traffic control device or failed to yield when entering the roadway. Bicyclists can prevent these crashes by obeying traffic rules. In 47% of these crashes, motorists were coded as failing to yield.

There are factors that help explain why motorists fail to yield. Sometimes bicyclists are approaching in an unexpected location, such as from the right instead of the left. There is no single database field that specifically gives bicyclist direction, so I created a flag for bicyclist facing traffic which was true if either the Participant Action was "traveling facing traffic" or Participant Error was facing traffic or driving, passing, or traveling on the wrong side. These fields have other options, such as failure to yield or just "crossing at intersection," so it is likely that we are failing to flag some bicyclists who were facing traffic and *also* engaged in some other error or action.

Drivers turning right from a driveway, stop sign, or red light may look only to the left for traffic, without noticing bicyclists coming from the right, opposite the flow of traffic. Those going straight or turning left need to make sure both traffic directions are clear, and are more likely to notice bicyclists coming from the right. In crossing paths situations, the bicyclist was facing traffic in 8% of crashes when the motorist was going straight, 22% when turning left, 55% when turning right, and 27% overall (see figure below).

The true share of wrong-way bicycling in crossing path crashes may be even larger. I have analyzed Florida and North Carolina bicycle crash data, which each include a field that asks only about bicyclist direction. When the motorist was entering the road from a commercial driveway or stop sign, or turning right on red, the bicyclist was facing traffic in 64% of the collisions in North Carolina and in 78% in Florida.    

**Crossing Path Collisions at Intersections and Driveways By Motorist Movement and Bicyclist Direction**
![Crossing Paths at Intersections: Bicyclist Direction by Motorist Movement](/images/BicyclistDirectionByMotoristMovement.png)

Motorist yielding behavior may also be affected by the position of the bicyclist: roadway, bike lane, or sidewalk. Non-Motorist Location provides bicyclist position *at the time of the collision*. Sidewalk bicyclists might collide with motorists at driveways that cross the sidewalk, or at intersections where the sidewalk becomes a crosswalk. Prior to 2015, bicyclists using a bike lane who collide with a motorist within an intersection were coded as using the roadway; subsequently, a "bike lane at intersection" category was added. For this analysis we restrict the data to 2015 and later in order to more accurately measure bicyclist position. The following figure shows that 60% of bicyclists who failed to yield were approaching on the roadway, 32% in a sidewalk or crosswalk, and 7% in a bike lane. However, when the motorist failed to yield ("No bicyclist cause"), only 31% of bicyclists were using the roadway.  

What explains this difference? Wrong-way bicycling is more common for bicyclists using sidewalks and bike lanes in this data. But the disparity in bicyclist position is apparent even when we filter the data to include only bicyclists operating in the direction of traffic: 66% were using the roadway when the bicyclist failed to yield vs. 36% when there was no bicyclist cause. The explanation may be that when motorists are approaching a red signal  seeing no pedestrians *in* the crosswalk, they enter the crosswalk before stopping, ending up in the path of sidewalk bicyclists *about to enter* the crosswalk, but not in the way of bicyclists approaching on the roadway. When turning right on red or at a stop sign or driveway, motorists need to move far enough into the road to see approaching traffic, typically beyond the crosswalk and possibly encroaching into the bike lane. These factors may explain why bicyclists using bike lanes and sidewalks are overrepresented in motorist-caused crashes compared to bicyclist-caused crashes.

**Crossing Path Collisions at Intersections and Driveways 2015-2018 by Bicyclist Position and Cause**
![Crossing Paths at Intersections: Bicyclist Cause by Position](/images/BicyclistPositionByCause.png)


**Parallel Paths at Intersections and Driveways**

There were 3,119 parallel path crashes at intersections or driveways. Most (2,683, or 86%) involved motorists turning right or left. Motorists were going straight ahead in only 11% (352), and half (164) of those involved bicyclists turning left. These are the rules governing turns:

**Right Turn** Drivers of vehicles turning right must make both the approach and the turn from the right-most part of the roadway, which insures that right-turners will not conflict with vehicles going straight. In Oregon, unlike every other U.S. state, a bike lane is not part of the roadway, so motorists are not required to approach a right turn in a bike lane. A separate rule governs when motorists may enter a bike lane: they are allowed to do so when "making a turn" but not when making the approach. Motorists are required to yield to bicyclists using a bike lane, sidewalk, or crosswalk. However, bicyclists using a sidewalk may not "suddenly leave a curb or other place of safety and move into the path of a vehicle that is so close as to constitute an immediate hazard" and may not operate "at a speed greater than an ordinary walk when approaching or entering a crosswalk, approaching or crossing a driveway or crossing a curb cut or pedestrian ramp and a motor vehicle is approaching the crosswalk, driveway, curb cut or pedestrian ramp.

**Left Turn** Left-turning drivers are required to yield to opposite-direction traffic, including bicyclists. The rules that sidewalk bicyclists may not suddenly move into the path of a vehicle or enter the crosswalk at faster than walking speed applies in this situation too.

As shown in the following figure, in parallel path intersection crashes during 2015-2018, when motorists were turning right, 19% of bicyclists were in the roadway, 27% in the sidewalk or crosswalk in 27%, and 54% were in the bike lane. When motorist were turning left, bicyclist roadway use was higher and bike lane use lower. For both types of turns, bicyclists were far more likely to be in a bike lane compared to bicyclists in crossing path collisions who failed to yield (only 7% in a bike lane). This difference shows that bike lanes increase the risk that motorists will not yield when turning.

There are reasons why it would be harder to yield. When motorists are turning right, they have to look behind to yield to bicyclists approaching in the bike lane, possibly in a blind spot, while also looking ahead for pedestrians in the crosswalk. When motorists are turning left, they don't have to look behind, but their main focus is the travel lane (or lanes). Bike lanes may also make it more likely that bicyclists pass on the right of cars that have stopped to allow opposite direction traffic to turn left, making the bicyclists invisible to turning motorists until close to the moment of impact.

A lack of night-time visibility is a bigger factor in motorist left turn crashes, 28% of which happened in non-daylight hours, compared to only 16% of motorist right turn crashes (these figures are based on the full dataset). This difference suggests that when motorists are turning left, their field of vision is more likely to include bicyclists (since they don't have to look behind), but they may not be able to *see* bicyclists who are approaching at night without the legally required headlight.

**Parallel Path Crashes at Intersections and Driveways 2015-2018 by Bicyclist Position and Motorist Turn**
![Parallel Paths at Intersections](/images/ParallelPaths.png)

#### Non-Intersection Collisions

Non-intersection or "midblock" collisions account for about 14% of BMVCs. We can distinguish three major types:

**Crossing Path**

Non-intersection crossing-path collisions account for 4.3% of BMVCs. In 16% of these collisions, the bicyclist was in a midblock crosswalk. In 86%, including some of the midblock crosswalk crashes, the bicyclist's action was coded as "crossing between intersections" or the bicyclist error involved failing to yield the right of way.

**Parallel Path Opposite Direction**

*Opposite-direction* parallel-path crashes account for 1.7% of BMVCs. The collision type was coded as "head on" or "sideswipe - meeting" for 56% of these 152 crashes. In 39% the bicyclist was coded as facing traffic, but this is likely to be understated for reasons previously described. The motorist error was coded as driving on the wrong side or improperly passing or running off the road in 13 cases (9%), and was coded as having made "no error" in 72%.

**Parallel Path Same Direction**

This is the crash type that most often comes to mind when we hear that a bicyclist has been "hit by a car." Nevertheless, they account for only 7.6% of all BMVCs, a total of 699 over a decade.

The scenario that bicyclists fear most, probably because it feels unavoidable, is being rear-ended by a faster-moving vehicle. However, only 183 of the same-direction, parallel path non-intersection collisions meet that description. Of those, 36 involved a bicyclist striking a slow or stopped motor vehicle and 16 involved a bicyclist changing lanes or attempting to turn. Of the remaining 131 collisions, 60 (46%) occurred in non-daylight hours when better lighting on the bicycle might have been the determining factor; in 25 of these 60 cases, the bicyclist was coded as "not visible" or having "improper lighting." In 46% of the 131 cases, the motorist was either impaired by drugs or alcohol (17 cases), distracted (13), blinded by sun glare (12), driving recklessly, carelessly or speeding (11), or ill or drowsy (7). Only 31 occurred in daylight and with no motorist factor indicated, and of these only 18 occurred in urban areas, only two of which led to suspected serious injuries.

The largest number of parallel path non-intersection same direction crashes are same-direction sideswipes, accounting for 309 of 699 or 44%. In 107 of these cases the bicyclist was the striking party, often in the process of attempting a lane change or a turn. Of the remaining 204, 44 (22%) occurred in non daylight hours. These are the case counts for motorist factors among these 204 sideswipe collisions where the motorist was striking: impaired by drugs or alcohol (10 cases), distracted (13), blinded by sun glare (12), driving recklessly, carelessly or speeding (10), or ill or drowsy (3). Only 31 of the 204 caused serious or fatal bicyclist injuries, and only 17 of these occurred in urban areas.

In sum, rear-end and same direction sideswipe collisions where the bicyclist was not making a lane change or otherwise the striking party are a small share of collisions. Still there is concern that they represent a disproportionate share of serious and fatal injuries. To better understand the interplay of factors that affect crash severity, it is helpful to build a model.

## Injury Severity Model
The severity of injuries for the 9,266 bicyclists in this dataset is shown in the chart below. Nearly one third were coded as "possibly injury." Just under 1% had a fatal injury and 6% had a suspected serious, but non-fatal, injury. Fortunately, more than 93% of BMVCs do not result in a serious or fatal injury. What factors determine if a crash produces major injuries?

![Bicyclists Involved by Injury Severity](/images/InjurySeverity.png)

I decided to do a simple binary model, comparing an outcome of serious or fatal injury with all other outcomes instead of a multi-class model, for the following reasons:
* a binary model is easier to interpret
* it would be useful to have separate classes for fatal and serious but non-fatal injuries, but there were only 87 fatalities, making it harder to find statistically significant results
* among those cases that are not serious or fatal, there are many that were "possible injury," adding uncertainty into the categories.
* the binary model is comparable to the one created for the [National Transportation Safety Board bicycling report](https://aashtojournal.org/2019/12/13/ntsb-releases-bike-safety-recommendations-report/) released in December 2019.

I created a logistic regression model using the Statsmodels Python library, with a dependent variable of Serious or Fatal injury (coded as 1) or any other injury status (coded as 0). Exponentiating each coefficient provides the odds associated with that feature. All of the features were categorical, with one category left out of the model, so the odds represents the odds ratio *compared to the left-out or "reference" category*. An odds ratio of 1 means that there is no difference between the modeled category and the reference category. An odds ratio greater than 1 means that the modeled category has a positive effect compared to the reference, and an odds ratio less than 1 means that it has a negative effect. I calculated the 95% confidence interval associated with each estimated odds ratio: if the interval includes 1, there is not enough information to determine if there is a positive or negative effect.

Although the direction of effect (positive, negative, or none) is easy to understand, the magnitude of an odds ratio has no intuitive interpretation. However, for factors that are uncommon, the odds ratio is a reasonable approximation of the *relative risk*, which does have an easy interpretation: a relative risk of 2, for example, means that the factor is twice as risky as the reference. Injury severity models help us understand the risk of a serious or fatal injury, *given that a crash has occurred*, but provide no information about the risk of a crash occurring. The odds ratios and their confidence intervals are plotted below.

**Injury Severity Model: Odds Ratios**
![Injury Severity Model](/images/ORs_all.png)

Location Characteristics
* Rural areas have an odds ratio (OR) of 2.2 compared to urban and suburban areas, meaning that the risk of serious or fatal injury is higher than in urban areas, even controlling for all the other features in the model.
* The risk of serious injury increases with higher posted speed limits. Crashes that occur on roads with a speed limit of 40-45 mph have an OR of 1.7 compared to the reference category of 25 mph or less. The highest category, 50+ mph, was not statistically significant, possibly because of the high correlation with area type: 71% of serious or fatal injuries on roads with a speed limit of 50 mph or more were in rural areas.
* Midblock locations have an OR of 1.9 compared to intersections.

Vehicle Type
* Bus involvement did not affect injury severity significantly.
* The involvement of a large truck at an intersection had an OR of 4.1, the highest effect of any feature in the model.

Lighting Conditions

The reference category was daylight. In comparison:
* dark with street lights had an OR of 1.3
* dark without street lights had an OR of 1.6
* there was no evidence that crashes at dusk or daylight were more serious than those in daylight.

Collision Type

Compared to angle collisions, *which in this data exclude turning movement collisions*:
* turning movement collisions were less likely to be serious, OR of 0.7
* head-on collisions were more likely to be serious, with an OR of 2.0
The other collision types did not show a statistically significant difference.

Whether the bicyclist was struck (rather than striking) had no significant effect.

Operator Behavior
* Crashes that were alcohol-related were more likely to be serious but without enough data to know with statistical significance.
* Speeding-related crashes had an OR of 2.5.
* Driver drug use had an OR of 3.1, although it was just short of meeting the test of significance at the conventional 95% level.
* Bicyclists using a helmet were somewhat more likely to have serious injuries (OR of 1.5). Paradoxically, so were bicyclists who were not wearing a helmet (OR of 1.3). The reference group was bicyclists with unknown helmet use.
* Hit and run crashes had no significant relationship with injury severity.

Although not shown in the OR plots, the models also include bicyclist age and gender. Gender had no significant effect on injury severity. Only three age groups were statistically different from 11 to 14 year old bicyclists (the reference group). Those under 10 years had a lower likelihood of serious injury, whereas bicyclists 50-69 years (OR 1.5) and 70 and up (OR 2.0) were more likely to have a serious or fatal injury.

### Discussion of Model Results
The injury severity model lets us estimate the independent effect of various features, controlling for the others in the model, on the odds of a serious or fatal injury, given that a crash has occurred. Rear-end and sideswipe collisions were not more likely to result in serious or fatal injuries compared to angle collisions after controlling for the other factors in the model. On the other hand, head-on collisions were the most likely to be serious or fatal. The odds increase in rural areas compared to urban or suburban ones, with higher speed limits, in midblock areas compared to intersections or driveways, and in darkness compared to daylight, particularly on roads without street lighting. Driver speeding increases the odds of a serious or fatal injury; driver alcohol and drug use have that tendency as well but without enough data to meet conventional tests of statistical significance. The involvement of a truck at an intersection was the strongest predictor of a serious or fatal BMVC. Note that all of these factors except midblock and night crashes were uncommon, meaning that the odds ratios can be interpreted as relative risk.

I compared these Oregon results with the injury severity in the National Transportation Safety Board bicycle report and my earlier injury severity model using Pennsylvania data, as shown in the table below. The NTSB model only uses the factors listed, but the other estimates are taken from more complex models. I separately estimated comparable simple models using only the listed factors. The midblock effect was 2.2 in the Oregon simple model--virtually identical to the NTSB model (but in Pennsylvania it was 1.3, in the simple model, even lower than the 1.5 estimate in the more complete model). The rural effect was highest in Oregon, and was even higher (2.5) in the simple model. The effect of higher speed limits in Oregon is very similar to Pennsylvania, except for the 50+ mph category. However, the effect of higher speed limits was much more pronounced in the NTSB data, which is due to the over-representation of Texas in their dataset, and the high share of bicyclist collisions on high-speed urban and rural roads in that state.

**Comparison with NTSB Model**
![Comparison with NTSB model odds ratios](/images/NTSB_comparison.png)

## Analysis of Fatal Collisions ##
To supplement the injury severity model and the review of BMVC prevalence, I reviewed all of the 87 fatalities over the 10 year period, which are almost evenly divided between urban (44) and suburban or rural (43). I used media reports to supplement the crash data to provide the following summary by type of collision. I reclassified three of the urban fatalities as "Motorist Lost Control" and two of the Turning collisions as angle (crossing paths)

#### Urban Fatalities (44) ####

* **Motorist Turning Left (7)**: In all of these cases, a left-turning motorist failed to yield to a bicyclist coming from the opposite direction. In three cases the bicyclist was in a crosswalk or bike lane at a traffic signal; in one of these (on the State Street bike path in Salem), the driver was coded as distracted, but in the other two there were no explanatory factors. In a fourth collision,  the bicyclist was coming down hill at possibly 30 mph and passing on the right of traffic stopped to permit an opposite-direction motorist turn left. In a fifth case the driver was drunk and the bicyclist was coming down hill at speed. In the final two cases, the collision occurred after dark and the bicyclist was not using a headlight.
* **Motorist Turning Right (6)**: Every case involved a right-turning truck and a bicyclist using the bike lane or sidewalk. In one case a sidewalk bicyclist failed to wait for the pedestrian signal. In another case, a bicyclist was on the wrong side of the road either in the bike lane or sidewalk and collided with a right-turning truck coming from the cross street.
* **Angle (15)**: In 12 cases, the bicyclist failed to yield at traffic signal or stop sign or was crossing between intersections (this includes a 5/14/2009 fatality in Portland that was erroneously categorized as a Turning crash in the data and a 10/15/2018 fatality in Portland that was categorized as Turning because the bicyclist was turning left, but was hit by cross traffic after pulling out from a stop sign). In two of these cases the bicyclist was unable to stop for a stop sign on a down slope. There were three cases where a motorist failed to yield. In one of these, a driver using drugs ran a stop sign. In another, a Corvette driver traveling 80 mph in a 40 mph zone hit a bicyclist using a bike path crosswalk at 3:30 AM; the driver was sentenced to 2 years in prison. The third occurred when an "inattentive" driver hit a bicyclist using [a midblock crosswalk in Corvallis in daylight in 2018](https://www.gazettetimes.com/news/local/corvallis-crosswalk-protests-continue/article_b4135b3b-b865-579b-8a08-cc92154f0bf5.html); a second bicyclist was killed in the same crosswalk in 2019 (but data from that year were not available for this study).
* **Motorist Lost Control (3)**: One case involved a motorist knowingly driving a vehicle with bald tires who lost control on a metal bridge. Another was caused by a speeding driver using cruise control who failed to negotiate a curve and hit a bicyclist on the opposite shoulder. In the third case, a texting motorist did not notice that the car in front of him had stopped to let two bicyclists cross the street, and when he hit the brakes hard, his vehicle spun and pinned one of the bicyclists against a rock wall.
* **Head-on (1)**: The one example involved a bicyclist traveling in the wrong direction in a travel lane of a one-way street.
* **Rear-end or Sideswipe - Same Direction (12)**: Eight occurred at night, when a primary factor is visibility. In four cases the crash was coded as bicyclist "not visible" or "improper or no lights". In five cases at night, the motorist was drunk or drugged. (Two cases involved **both** motorist impairment and bicyclist failure to have lights.) One case involved a distracted driver who drifted into the bike lane and hit a bicyclist who was using lights, per [media reports](https://www.sthelensoregon.gov/police/page/bicyclist-identified-thursday-morning-crash-highway-30-st-helens). Four occurred in daylight: two of these involved drunk and speeding motorists. A third involved a driver aggressively changing lanes approaching an intersection. [According to witnesses](https://eugenebicyclist.com/2011/08/30/more-shitty-news/), the bicyclist had already merged into the center turn lane and was waiting to turn left into a commercial driveway. The motorist [pled guilty to negligent homicide and was sentenced to 6 months](https://www.registerguard.com/article/20121006/NEWS/310069950). In the final case, the bicyclist "was riding in the center turn lane of the highway with another cyclist when she suddenly fell over into the inside southbound lane of the highway, where she was hit." According to police, the bicyclist "may have fallen when she was unable to get one of the cleats to dislodge from the pedal."

#### Rural and Suburban Fatalities (43) ####

* **Motorist Turning Right (1)**: There was a single case at a traffic signal in an urbanized area just outside the Portland city limits, and as in the urban cases, it involved a right-turning truck and a bicyclist going straight from the bike lane to the right of the large vehicle.
* **Bicyclist Turning Left (4)**: In three cases the bicyclist turned left from the right side of the road or from the shoulder. In one case the bicyclist failed to yield to opposite direction traffic when turning left.
* **Angle (8)**: In seven cases the bicyclist failed to yield: four between intersections and three at stop signs, one of which was at the Row River Rail Trail at Row River Road (which now has an underpass due to multiple prior crashes). In one case an "inattentive" motorist failed to yield at a stop sign at an intersection of two residential streets.
* **Head-on or Sideswipe - Opposite Direction (7)**: In two cases the bicyclist was traveling on the wrong side of the road. In two other cases the bicyclist lost control and crossed the center line on a downhill curve. In two cases a motorist on drugs or alcohol was traveling on the wrong side of the road. The final case involved a speeding motorist who sideswiped a car while trying to pass it, lost control and hit a bicyclist on the shoulder of the other side of the road ([the driver was sentenced to 30 days in jail and three years probation](https://www.registerguard.com/article/20100604/NEWS/306049995)).
* **Rear-end or Sideswipe - Same Direction (23)**: In 10 cases, the crash occurred in the dark and the bicyclist was not sufficiently visible (this includes one erroneously coded as an Angle crash). In five cases, the motorist was impaired by drugs or alcohol; in a sixth case the speeding motorist who hit a bicyclist in a bike lane at 1:00 AM had three prior DUIs but fled the scene and could not be tested. A motorist driving into the blinding sun was the cause of one crash that killed two bicyclists and the likely cause of another. In one case, the motorist was drowsy (this crash was coded by ODOT as Fixed Object). In three cases, the bicyclist moved left into overtaking traffic; one was an 81-year old bicyclist who struck the side of a log truck.

The cause of these fatalities was in many cases bicyclist error (failing to yield when required, not using lights, operating facing traffic, or failing to control the bicycle). In every case where the bicyclist made no error, the motorist was impaired, distracted, speeding, blinded by sun glare, or driving an unsafe vehicle, with only 6 exceptions: two motorist left turns and four motorist right turns, all of which involved bicyclists using a bike lane or crosswalk. Although motorists in Oregon are required to yield to bicyclists in bike lanes, it can be difficult to yield to bicyclists approaching from unexpected places. Notably, [a judge ruled](https://www.oregonlive.com/pacific-northwest-news/2018/10/judge_rules_cyclist_hit_by_fed.html) that a truck driver was not to blame for turning across the path of a bicyclist overtaking on the right in a bike lane. As a result, in 2019 Oregon changed the definition of a bike lane in state law to specify that a "bicycle lane exists in an intersection if the bicycle lane is marked on opposite sides of the intersection in the same direction of travel." However this statutory change may is unlikely to eliminate this crash circumstance.

### Conclusions
The vast majority (86%) of BMVCs in Oregon are angle and turning collisions, and most of these occur at intersections or driveways -- where most turning and crossing movements happen. Addressing the factors that cause angle collisions would have by far the biggest impact on reducing BMVCs.

In half of BMVCs, the bicyclist and motorist were on crossing paths, and in 43% of these, the bicyclist failed to yield. Crossing path (angle) collisions were also the most common circumstance of urban bicyclist fatalities, and in 80% the bicyclist failed to yield. Clearly, improving bicyclist compliance with right-of-way rules needs to be part of the solution.

When the motorist failed to yield in a crossing path collision, often the bicyclist was coming from an unexpected direction (facing traffic) and/or not on the roadway (on the sidewalk or in a bike lane). Right-turning motorists, who typically look only left for vehicles, are far more likely to collide with a wrong-way cyclist compared to motorists going straight or turning left. When bicyclists are not the cause of crossing collisions, they are far more likely to be in a crosswalks or bike lane. The most reasonable explanation for these two findings is that operating against the flow of traffic and on a sidewalk or bike lane increases the risk of intersection collisions.

Parallel path collisions are the next most common. Most involved right or left turning motorists. Far more bicyclists were in bike lanes (54% for motorist right turns and 40% for left turns) than what we would expect based on the share in crossing path collisions where the bicyclist failed to yield (only 7% were using a bike lane). The best explanation is that bike lanes increase the risk of parallel path collisions with turning motorists. With right turns, unsuspecting bicyclists may position themselves to the right of right-turning cars, in the driver's blind spot. Not only are motorists permitted to turn right from that position, in Oregon they are not allowed to first merge into the bike lane to prevent overtaking on the right. The mechanism of increased risk is less obvious when motorists are turning left. It may be that bicyclists using bike lanes are more likely to pass on the right of slow traffic that screens them from opposite direction left-turners. Or perhaps left-turning motorists focus on yielding to traffic in the travel lanes, paying less attention to bicyclists approaching in bike lanes.

The NTSB bicycle safety report emphasizes that midblock crashes tend to be more serious and therefore recommends separated bike lanes as a primary method to improve bicyclist safety, arguing that they can "all but eliminate hit from behind, overtaking, and sideswipe crashes." My injury severity model confirmed the NTSB's finding that midblock collisions are about twice as likely to result in serious or fatal injuries compared to intersection BMVCs. However, only just over half of midblock collisions were rear-end or sideswipe collisions, and of those, and in about half of those the bicyclist either hit a car in front or was attempting to turn or changing lanes. Therefore only 27% of midblock collisions, and 4% of all collisions, were actually "hit from behind" collisions, the type targeted by these recommended facilities.

Although rear-end and sideswipe same-direction collisions account for 35 of the 87 (40%) fatalities in this dataset, the model shows that these types are not inherently likely to produce serious or fatal injuries compared to angle collisions. These fatalities often occur to occur in darkness and rural areas, on high-speed roads, and when drivers are speeding or impaired--all factors that increase the odds of serious injuries. In fact, two-thirds of the Oregon rear-end and sideswipe fatalities were in rural areas (23 of 35). Separated bike lanes do not address these rural crashes: they are installed on city streets. [As of 2020](https://docs.google.com/spreadsheets/d/11H0gArHxo6kMop1I18yMcq7ArbNrwaGBLmIXgqI1Gjk/edit#gid=3), all of the existing or planned separated bike lanes in Oregon are in the cities of Beaverton, Bend, Eugene, Hillsboro, and Portland, with the vast majority in the latter.

The injury severity model showed that motorist speeding, and with less certainty motorist impairment, were significant predictors of more severe bicyclist injuries. All rear-end and sideswipe fatalities involved either bicyclist error (not using lights at night or loss of control) or criminally negligent motorists. The recommended separation is often provided with flexible delineators that are designed to fold without damaging a motor vehicle or with parked cars, which are not always present. These devices are by no means certain to prevent injury from impaired or out-of-control drivers.

Moreover, separated bike lanes provide no protection at intersections. The model showed that the presence of a truck at an intersection was the strongest predictor of a serious or fatal injury. In fact, there is a risk that separate bike lanes will lead to an increase in intersection collisions. Bike lane and sidewalk bicycling was associated with a higher frequency of collisions with turning and entering motorists at intersections and driveways. Two-way bike facilities that put bicyclists in an unexpected position facing traffic may increase the risk even more. The potential for more intersection crashes is reinforced by the finding that all of the fatalities where the bicyclist was following the rules of the road and the motorist was in control of the vehicle and not impaired, blinded, distracted, or speeding happened when the bicyclist was entering an intersection from a bike lane or sidewalk.

Fortunately, there are other tools that can reduce bicyclist fatalities. Getting more bicyclists to use lights at night (for example by changing Oregon law to require the use of a rear light, not just a reflector) would not only address at least four and possibly six of the 12 urban rear-end and same direction fatalities, but also 12 others. A statewide program to reduce the number of drunk and drugged drivers on the road would address 14 fatalities, half of them urban rear-end collisions. Most of the remaining fatalities could be addressed by reducing distracted and dangerous driving, enforcement directed at bicyclists who fail to yield at intersections or operate facing traffic, and making both bicyclists and motorists aware of the problem of sun glare.
