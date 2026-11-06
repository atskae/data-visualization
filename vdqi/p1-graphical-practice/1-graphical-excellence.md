# Chapter 1: Graphical Excellence
**Excellence** means to communicate complex ideas with clarity, precision, and efficiency
* Encourage the viewer to analyze the data: compare, zoom in/out
* Compact/summarize big datasets

Graphics *reveal* data
* [Anscombe's Quartet](https://en.wikipedia.org/wiki/Anscombe%27s_quartet) (published 1973): all four datasets below have the same statistical features (x-mean, y-mean, variance, correlation, etc.) but visually look very different when visualized:
    <img src="images/anscombes-quartet.png" width="400px" />

    <img src="https://upload.wikimedia.org/wikipedia/commons/e/ec/Anscombe%27s_quartet_3.svg?utm_source=en.wikipedia.org&utm_campaign=imageinfo&utm_content=original" width="1000px" />

**Bivariate scatterplot**: displays the relationship between two quantities (plotted on the x-axis and y-axis)
* We can immediately see outliers A and B when plotted:
    <img src="https://blogs.sas.com/content/iml/files/2019/03/MVOutliers2-1.png" width="400px" />
* **Marginal distribution** - the probability distribution of a variable (x or y), independent of the other variable
    * The marginal distribution of the outliers A and B might not reveal the outlier

The data graphic can only be as good as its dataset
* A visual cannot rescue a small dataset or a bad data model
* Example of a **spurious correlation** - when a dataset only *appears* to be correlated, but only due to coicidence, with no proof of an actual cause-effect
    * Ex) New York stock prices and solar radiation
    <img src="https://figures.semanticscholar.org/53b3b3b2b135b01d9372b3194d3d243575684617/56-Figure22-1.png" width="500px" />
* Website of [spurious correlations](https://www.tylervigen.com/spurious-correlations) 笑

## Data Maps
* [National Cancer Institute (NCI) Cancer Atlas](https://gis.cancer.gov/canceratlas/) - shows the map of cancer rates by state, cancer type, and demographics
    <img src="images/nci-cancer-atlas.png" width="600px" />
* The book originally points to the maps in [Cancer rates by US county (1973)](https://www.science.org/doi/10.1126/science.1220005) (but blocked by a paywall 😢)
    * Can analyze regional hotspots for different types of cancer
    * Cancer and common occupations (ex. highest rate of bladder cancer at the time of publishcation was in Salem County, NJ - there were many workers in the chemical industry)
* Need to be careful with data maps:
    * The viewer assumes geographic size is related to population/importance
    * Our attention goes straight to exploring the data on the map, and not *how* the data was collected
        * Ex) How was the cause of death determined? Differences in how cancers are diagnosed in different geographic area

Data maps, the combination of statistics and cartography, first appeared in the 17th century, 5,000 years after the first maps were drawn on clay tablets
