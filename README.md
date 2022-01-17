# Investigation of Permit Approval Times for New Construction in San Francisco, CA

### Semester Project for CUSP-GX 5002: Principles of Urban Informatics, Fall 2021

### Rohan Kalyani and Turbold Baatarchuuluu

### Abstract

The state of California, and particularly the City of San Francisco, is experiencing a severe housing shortage. Housing developers must go through a lengthy and costly permitting process to build new housing units. Building permit approval times (BPAT) for new construction permits that add units of housing in San Francisco were analyzed to assess the influence of various factors on BPATs. From 2013 to 2021, the median BPAT for new construction permits was determined to be 340 days. Explanatory multivariate OLS regression models were developed, and the three most statistically significant factors were determined to be the permit approval year, the existence of a corresponding ‘site permit’, and the number of proposed stories in the permit. The permit approval year and the existence of a site permit resulted in increased BPATs, while the number of proposed stories resulted in decreased BPATs. The final model was able to explain 33.4% of the variance in the sample BPATs. Furthermore, it was found that permits in low-density zoned parcels resulted in increased BPATs while permits in high-density zoned parcels resulted in decreased BPATs. These results were consistent with the (admittedly limited) available literature studying the housing development process in San Francisco.

#### Master Notebook - View This to Follow Along!
The notebook with the bulk of the project work (data aggregation, EDA, visualization, modeling) is [PUI Mastersheet.ipynb](https://github.com/rohanskalyani/sfpermittimes/blob/main/PUI%20Mastersheet.ipynb). The other notebooks are mostly for API queries, initial EDA, and data cleaning and pre-processing. 

### Motivation and Overview
<details>
<summary>Show/Hide</summary>
The State of California has among the highest housing prices in the United States for a multitude of reasons, most notably because of a massive housing to jobs imbalance caused by decades of sluggish housing construction that isn’t keeping up with the demand<sup>[1]</sup>. High housing costs have displaced more than a million Californians, forced commuters to live in more auto-dependent areas (thus increasing greenhouse gas emissions), and has left the state with an egregious homelessness problem. These problems are particularly pronounced in the City of San Francisco, at the heart of the San Francisco Bay Area, whose astronomically high housing construction costs have further exacerbated the problem. High housing costs have displaced more than a million Californians, forced commuters to live in more auto-dependent areas (thus increasing greenhouse gas emissions), and has left the state with an egregious homelessness problem<sup>[2]</sup>. These problems are particularly pronounced in the City of San Francisco, at the heart of the San Francisco Bay Area, whose astronomically high housing construction costs have further exacerbated the problem.
  <br>
  <br/>
Many cities in the Bay Area (and especially San Francisco) have strict exclusionary zoning ordinances (i.e. land zoned exclusively for single-family homes) and other onerous building regulations (e.g. minimum parking requirements, maximum floor area ratios, minimum lot sizes) that inflate construction costs and severely restrict the type of housing that can be profitably built<sup>[3]</sup>. Another factor that contributes to San Francisco’s high housing construction costs is building permit approval times (BPATs). Housing developers have to wait for their building plans to be approved by the San Francisco Planning Department (SFPD) and the San Francisco Department of Building Inspections (SFDBI) during the plan check process before they can start construction. This is of course a necessary step in ensuring the safety of a proposed building, but many permits end up having unnecessarily long approval times. As of 2021, the median wait time for new construction permits from SFDBI is **797** days. This causes severe delays and huge cost-overruns in development projects, resulting from increased financial uncertainty, as well as wasted land, materials, and labor costs. Delays in building permit approvals are so widespread that many developers have resorted to hiring specialized private permit expeditors to move projects through the review process in a more timely manner<sup>[3]</sup>, which could have serious equity implications.
  <br>
  <br/>
This project seeks to quantify the statistical effects of a variety of factors that could explain BPATs for SFDBI through linear regression analysis. BPATs have changed significantly over time, so to get a better understanding of the current situation, the project will focus on permits issued from 2013-present. Also, since this project’s primary purpose is to examine an aspect of California’s housing crisis (a supply-side problem), the permitting data that will ultimately be collected will refer only to permits that add units of housing to the overall supply (i.e. new construction permits). Permits that refer to non-residential construction, as well as demolition and remodels will be excluded from the analysis.
</details>

### Literature Review
<details>
<summary>Show/Hide</Summary>
One of the only in-depth investigations of the permitting approval process in San Francisco that could be found was conducted by former UC Berkeley Terner Center for Housing Innovation Master’s student Brian Goggin in his report, *Measuring the Length of the Housing Development Review Process* in San Francisco<sup>[6]</sup>, published in 2018. Goggin goes into great detail about the myriad of data quality issues in San Francisco planning and permitting data. In fact, one of his main conclusions is that data quality on the permitting review process needs to be improved. Outside of that, he also found that low-density neighborhoods outside of the dense downtown core had some of the longest permitting review times, and that smaller (>10 units) and mid-size developments (10-50 units) took longer for approval than larger developments (<50 units)
</details>

### Datasets
  <details>
    <summary>Show/Hide</summary>
    
The datasets used for this analysis are as follows:
* SFDBI Historical Building Permits Data (2013-Present)<sup>[4]</sup>
* SFPD Permitting Data (1920-Present)<sup>[5]</sup>
*	SF Assessor Historical Secured Property Tax Rolls (2007-2020)<sup>[7]</sup>
*	SF Active and Retired Land Parcels (2021)<sup>[8]</sup>
*	US Census Bureau → American Community Survey for San Francisco County (2019)<sup>[9]</sup>

All data related to San Francisco was obtained from SF's Open Data portal, [datasf.org](https://datasf.org/opendata/ "SF Open Data")
  </details>
  
### Methodology
<details>
<summary>Show/Hide</Summary>
The first step in this project was to synthesize data sets from the various chosen sources into one master data set that will be used for regression analysis. After cleaning the datasets individually, and filtering for permits that are specific to new housing construction in the DBI dataset (e.g. removing permits for ‘over-the-counter alterations’ which don’t add units of housing), the number of viable permits with permissible data quality went from ~300,000 permits to ~1000 permits. Furthermore, only a handful of features were remaining after the cleaning process, as many of the other features of interest, especially from the SFPD Permitting set (e.g. number of affordable units, presence of accessory dwelling units), had very poor data quality. 
  <br>
  <br/>
Due to issues with the way parcel numbers were recorded in the DBI dataset (i.e. expired parcels that had been split into different parcels in the official SF parcels dataset), merging the DBI data with the tax assessor’s dataset and the parcel shapefile resulted in the loss of another ~300 permits. The final master dataset ultimately only had 693 data points and 10 features:  
  
* Property Area (sq. ft) [Tax Assessor]
* Total Taxable Value of Parcel ($) [Tax Assessor]
* Estimated Cost of Permit Plan ($) [DBI]
* Revised Cost of Permit Plan ($) [DBI]
* Existence of Site Permit (Boolean) [DBI]
* Proposed Number of Units in Permit Plan [DBI]
* Proposed Number of Stories in Permit Plan [DBI]
* Year of Permit Approval [DBI]
* Median Household Income ($) [ACS]
* Parcel Zoning Code Assignment (dummy variable) [SF Parcels]  
  
Before regressions were performed using this dataset, a final exploratory analysis was conducted. A wait time distribution plot was constructed, correlation tables to assess feature multicollinearity were generated, and the BPATs were plotted against every feature to assess spread and correlation. Significant numbers of outliers were detected (e.g. permits with property area greater than 50,000 sqft, which is highly unusual for a small city such as San Francisco), so a final filter step was done to remove the worst outliers, bringing the number of new construction permits to 656. This number seemed rather low, so to ensure the data was grounded in truth, the total number of proposed units for every year in the dataset was cross-verified to be within the same order of magnitude with housing unit production numbers from a housing development pipeline report by the San Francisco Planning Department<sup>[11]</sup>. 

#### Technology
Since the purpose of this project is to investigate possible reasons for long wait times (i.e. create an explanatory model), the data was not split into a training and testing set. Instead, cross-validation in the parameter estimator function was relied upon to reduce bias. For model selection, a lasso cross-validation parameter estimator was chosen in tandem with a forward-searching sequential feature selector, all under the sci-kit learn Python package (known as linear_model.LassoCV and SequentialFeatureSelector, respectively). This combination was selected for its computational speed and statistical robustness. The selector was set to iterate through the full range of possible features (i.e. from 1 to 10 features) and report relevant statistics about the model’s performance. A final model was chosen from the candidate models based on its R2, as well as the statistical significance and the assessed multicollinearity of the component features.
</details>
  
### Results
To see the results of the analysis, view the master notebook, [PUI Mastersheet.ipynb](https://github.com/rohanskalyani/sfpermittimes/blob/main/PUI%20Mastersheet.ipynb).
  
### Discussion and Recommendations
 <details>
   <summary>Show/Hide</summary>
The final regression model identified the existence of site permits, the permit issuance year and the proposed number of stories as the most statistically significant features. The R<sup>2</sup> of the final model indicates that the model’s features were able to explain 33.4% of the variance in the sample BPATs. The permit issuance year was the most impactful feature. This reflects general time-series trends for BPATs in San Francisco. The statistical significance and degree to which the approval year has affected BPATs is a clear sign that the building permit approval process in San Francisco is highly dysfunctional and only getting worse.
   <br>
   <br/>
The existence of site permits is the next most significant regressor for increased BPATs. A site permit is typically required for all new buildings and alterations to existing structures as defined by the San Francisco Building Code<sup>[10]</sup>. However, for unclear reasons, a significant number of permits for new construction do not have a ‘Site Permit’ flag in DBI dataset, enough to have a statistically significant effect on BPATs. Within the original, uncleaned DBI dataset, 33% of all new construction permits have no Site Permit flag, which is interesting considering that site permits are supposedly mandatory for all new construction. The reason for this discrepancy is unknown, though it is plausible that DBI staff, under corrupting influence, waived site permit requirements for preferential developers, as an official San Francisco Public Integrity Review recently found<sup>[14]</sup>. Further investigation is definitely required and DBI’s culture of corruption should definitely be addressed, as San Francisco Mayor London Breed is attempting to do after a slew of high-profile corruption scandals at the DBI<sup>[13]</sup>.
   <br>
   <br/>
The only statistically significant regressor that had a negative impact on BPATs was the proposed number of stories. This contradicts our original hypothesis that taller, denser construction would lead to longer BPATs. This result is consistent with Goggin’s Terner Center report, where mid to large-sized developments were approved faster than smaller developments. A plausible reason for this is the permit expediting industry, in which well-capitalized developers seek to speed up the review process to build taller, higher-margin apartment buildings. 
   <br>
   <br/>
As for the effect that zoning code assignments played on BPATs, permits that were zoned for residential low-density tended to have longer BPATs (i.e. positive coefficient) than permits zoned for high-density (i.e. negative coefficient), which is consistent with Goggin’s findings that lower-density neighborhoods had longer approval times. It is also consistent with this report’s own findings that permits with more proposed stories (i.e. higher density) have shorter BPATs. A caveat is that the zoning code dummy variables were not statistically significant at 95% confidence, possibly as a result of poor data quality, so further research and better data is needed to increase confidence in this result.
   <br>
   <br/> 
Municipal governments collect vast amounts of data regarding each new permit, yet the reporting quality on this data leaves a lot to be desired. A standardized open data reporting format should be developed for housing permits to promote better analysis and accountability in housing development. On this front, California is moving in the right direction; just this year, the California State Legislature passed SB 477, the Housing Data Act, written by State Senator Scott Wiener, which will improve housing data standards across the entire state<sup>[12]</sup>.
   </details>
  
### Bibliography
  <details>
    <summary>Show/Hide</summary>
[1]Californians: Here’s why your housing costs are so high: CalMatters (Sept. 2021)
https://calmatters.org/explainers/housing-costs-high-california/

[2]California’s High Housing Costs - Causes and Consequences: California Legislative Analyst’s Office (2015)
https://lao.ca.gov/reports/2015/finance/housing-costs/housing-costs.pdf

[3]The Hard Costs of Construction: Recent Trends in Labor and Materials Costs for Apartment Buildings in California: Terner Center for Housing Innovation, University of California, Berkeley (Mar. 2020)
https://ternercenter.berkeley.edu/wp-content/uploads/2020/08/Hard_Construction_Costs_March_2020.pdf

[4]San Francisco Department of Building Inspections Historical Building Permits
https://data.sfgov.org/Housing-and-Buildings/Building-Permits/i98e-djp9

[5]San Francisco Department of Planning Historical Permit Data
https://data.sfgov.org/Housing-and-Buildings/SF-Planning-Permitting-Data/kncr-c6jw

[6]Brian Goggin.Measuring the Length of the Housing Development Review Process in San Francisco (2017)
https://ternercenter.berkeley.edu/wp-content/uploads/2018/05/Goggin_Permitting_Timelines_July_2018.pdf

[7]Assessor Historical Secured Property Tax Rolls
https://data.sfgov.org/Housing-and-Buildings/Assessor-Historical-Secured-Property-Tax-Rolls/wv5m-vpq2

[8]Parcels – Active and Retired
https://data.sfgov.org/Geographic-Locations-and-Boundaries/Parcels-Active-and-Retired/acdm-wktn

[9]US Census Bureau → American Community Survey for San Francisco County (2019)
https://www.census.gov/quickfacts/sanfranciscocountycalifornia 

[10]SAN FRANCISCO BUILDING INSPECTION COMMISSION CODES
https://codelibrary.amlegal.com/codes/san_francisco/latest/sf_planning/0-0-0-19797

[11]2020 San Francisco Housing Inventory (Apr. 2021)
https://sfplanning.org/sites/default/files/documents/reports/2020_Housing_Inventory.pdf

[12]Senator Scott Wiener (D-San Francisco)’s Housing Data Act, Senate Bill 477
https://leginfo.legislature.ca.gov/faces/billTextClient.xhtml?bill_id=202120220SB477 (Sep. 2021)

[13]Bay City News: Mayor cracks down on ‘corrupt’ Department of Building Inspection (Sep. 2021)
https://www.sfexaminer.com/news/mayor-issues-executive-directive-to-increase-transparency-in-dbi-amid-misconduct-allegations/

[14]Controller’s Office of the City & County of San Francisco. Public Integrity Review Preliminary Assessment:Department of Building Inspection’s Permitting and Inspections Processes (Sep. 2021)
https://sfcontroller.org/sites/default/files/Documents/Auditing/Public%20Integrity%20Deliverable%20%20DBI%20Permitting%20%20Inspections%20-%2009-16-21.pdf
</details>
