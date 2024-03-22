# Vehicle-Telematics-Clustering
An approach to cluster various trips on Cars based on their trip behaviour(including speed,acceleration,throttle position) experimented in Boston

Exploring Vehicle Telematics Data from Kaggle 🚗💻
This proprietary dataset contains sensor and OBD data for over 4 months and around 30 vehicles. This data was generously made accessible for learning and analysis within the data science community.



Primary Steps Involved
EDA / Data Wrangling and Manipulation
Applying PCA and K-Means Clustering



💭 About
Curious to learn more about vehicle telemetry data and how to work with it, I found this public dataset on Kaggle and decided to dive in.
Seeing as this is an unlabelled dataset in terms of claims frequency/severity, constructing a supervised learning model to predict individual driver risk isn't feasible here. Instead, I applied K-means clustering to group similar "vehicle trips" together. Clustering similar drivers together based off of driving habits seems like the more intuitive and appropriate approach, but this dataset doesn't lend itself very naturally to that task (data is ID'd by trip and device, can't track data by driver).


📔 Resources used
Telematics in Auto Insurance - Towards Data Science
Data Mining for Vehicle Telemetry - University of Warwick Research Paper
Making the Economics of Telematics Work for Insurers - Casualty Actuarial Society


Raw features (18 total)
timeStamp (object)
tripID (integer)
accData (object) - ?
gps_speed (float)
battery (object) - charge?
cTemp (float) - engine coolant temp
dtc (float) - # diagnostic codes
eLoad (float) - ?
iat (float) - ?
imap (float) - ?
kpl (float) - ?
maf (float) - ?
rpm (float) - engine revolutions per minute
speed (float) - speed in km/h
tAdv (float) - ?
tPos (float) - throttle position
deviceID (integer)


Feature engineering
First derivatives with respect to time were calculated for speed (to derive acceleration), throttle position, and engine RPM. These are useful since acceleration metrics tell us a lot about driver habit/behavior (e.g. frequent hard braking).
Various percentile-based statistics were calculated for a basket of suitable features, grouped on tripId. These features roughly capture the distribution of our metrics across an entire trip. Ex: what was the 99.9th percentile observed for acceleration during this outing?
Naturally, for our percentile calculations we must be diligent in excluding values of 0 (e.g. stopped car) since these will skew results undesirably.

Modelling
We then applied PCA, comparing the relationship between number of components and retained variance. 99% of the explained variance could be preserved with only 50 features (originally 162).
K-means clustering was applied to group vehicle trips into groups, based off observed driver patterns (acceleration, speed, braking, sudden opening of the throttle, etc).
To evaluate model performance, the elbow method (with WCSS) and comparison of Silhouette scores was used to select an optimal number of clusters.

Limitations and caveats
Data integrity here is questionable; accelerations of +70km/h/s are impossible, but show up in the dataset. These are filtered out for the purposes of this analysis, but consultation with data engineers and instrumentation documentation would be advised in practice.
What constitutes a "trip"? Some trips in our dataset span 100+ hours, which is perplexing to say the least. Again, these abnormally long "trips" are discarded for this analysis.
This dataset really needs supplementary outcome data (claim frequency/severity) in order to produce significant insight. This clustering approach was only conducted as an academic exercise, since there isn't much else we could do in terms of modelling with this dataset alone.


Ways to amplify this analysis (wish list)
Location data / road type. With this, we could attempt to develop a predictive model that could infer type of road based off of driving patterns.
As said before, claims data or accident data. The is the real key. If we had this, we could concretly prove which driving patterns are the riskiest, and look into incorporating driver behaviour into a more tailored ratemaking model.
