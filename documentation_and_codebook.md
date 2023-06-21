# OxCGRT technical documentation

The canonical version of OxCGRT documentation is published in our working paper at the Blavatnik School of Government: https://www.bsg.ox.ac.uk/covidtracker

This document contains an extract of the technical appendix from June 2023.

Contents:
- [Getting started with OxCGRT data](#getting-started-with-oxcgrt-data)
- [Codebook and interpretation guidance](#codebook-and-interpretation-guidance)
- [Calculation of policy indices](#calculation-of-policy-indices)

# Getting started with OxCGRT data
--[return to top](#)--

Because of the complexity of the OxCGRT dataset, it is published across 25 CSV files. This appendix should contain all the information users need to navigate and use the data. However, at almost 100 pages long, users may struggle to navigate the appendix itself.

For those who want to dive in straight away, the files are labelled as follows:
- `OxCGRT_compact` is the file we expect most people will use, and is a good starting place. It will reflect the policies that applies to the majority of people in a jurisdiction.
  - It contains 56 variables (including metadata) for 185 national jurisdictions and 210 subnational jurisdictions.
- `OxCGRT_simplfied` is the most basic version of the data.
  - It contains just a single variable for each indicator.
  - In total, this data file contains 50 variables for 185 national jurisdictions and 170 subnational jurisdictions (state level only).
- Files denoted `fullwithnotes` provide maximum detail while still reporting the overall situation in each jurisdiction.
  - All jurisdictions in these files contain the TOTAL designation, meaning they report the overall total policy environment on residents.
  - These files are structured across 147 variables (including metadata).
- Files denoted `raw` provide the raw data that our team collected for subnational jurisdictions.
  - These jurisdictions are generally designated as WIDE or GOV, meaning they report the policies set by a certain level of government (residents may also be subject to policies from other levels of government).
  - These files are structured across 128 variables (including metadata).
- The `vaccines_full` files contain our raw data for the vaccine indicators V1-V4 over 226 variables (where each of the 4 indicators has a separate variable recording the policy for each of the 52 population categories).
- The four policy indices are also published in `timeseries` format, which uses the “average” version of each index from the `OxCGRT_compact` files.
- We have also separately published supplementary files for jurisdictions where we only partially completed the data collection exercise. This includes regions of Italy and second cities of Brazilian states (large non-capital cities). These are published separately, and not part of the main dataset.

If you are troubleshooting or things look wrong in the data, there are some common things to check:
- make sure you are looking at the right ***version of the indicator*** for what you want.
  - `M` versions of indicators (eg. `C1M`) won’t always report the existence of a lockdown or closure if it doesn’t apply to vaccinated people.
  - `E`, `NV`, and `V` versions of indicators (eg. `C1E`,  `C1NV`, and `C1V`) are not continuous and will have gaps.
  - You need to choose the right one for your situation.
  - see the section “*Differentiation of policies by vaccine status (vaccine passports)*” for more information.
- make sure you are looking at the right ***jurisdiction type*** for what you want.
  - you might know that the US state of Oklahoma was not recommending any business closures in August 2020, and yet we report `C2M`=1 (recommended business closure) for `US_OK` in our OxCGRT_compact_subnational_v1.csv file.
  - this is because our main files use the `STATE_TOTAL` jurisdiction type, which reports any policies that apply to residents, *including policies from the federal government*.
  - to find policies enacted by a certain level of government you need to use jurisdictions labelled `_WIDE` or `_GOV`. We only have these for some, not all, of our jurisdictions.
  - see the section “*Subnational data and the `jurisdiction` variable*” for more information.
- make sure you have ***check the flag variables*** if you see a policy that is much stricter than expected.
  - because we record the strictest policy in a jurisdiction, we will sometimes report a strict policy that only applies in a very small geography (eg. a lockdown in one city when the rest of the country is free). Our flag variables will usually tell you if a policy is *targeted* to a specific geography, or *general* across the whole jurisdiction.
  - see the section “*Flag variables for geographic targeting or economic support*” for more information.
- make sure to ***check the notes*** to understand the thinking from our data collector.
  - it is always useful to use the notes column to corroborate the value you see in an indicator – this should explain why a particular value was chosen. If there are no notes on the day you are looking at, scroll back in time until the most recent note.
## Old repositories and prior versions of our data
During the course of the pandemic we published OxCGRT data in several formats – most of which are subtly different to the final dataset at: https://github.com/OxCGRT/covid-policy-dataset

These repositories are still available and can be used to access data published in older data structures. GitHub also allows people to view historical versions of repositories, so it is possible to examine the data as it was at any point in time.

The repositories that may be of interest are:
- https://github.com/OxCGRT/covid-policy-tracker
  - this was the live production repository, updated every hour. Because it was updated in real time the repository is incredibly large, holding the entire history of past edits and changes.
- https://github.com/OxCGRT/covid-policy-tracker-legacy
  - this repository contains up-to-date data published in our “legacy” format. While we made several changes to the codebook and data structure over the course of the pandemic, we always maintained a version that was easily comparable back to the very first dataset we published.
- https://github.com/OxCGRT/Brazil-covid-policy
  - this is where we published raw data for Brazilian subnational jurisdictions (ie. `STATE_GOV` and `CITY_WIDE`)
- https://github.com/OxCGRT/USA-covid-policy
  - this is where we published raw data for the states in the USA (ie. `STATE_WIDE`)
- https://github.com/OxCGRT/Australia-covid-policy
  - this is where we published raw data for Australian subnational jurisdictions (ie. `STATE_GOV` and `CITY_WIDE`)

# Codebook and interpretation guidance
--[return to top](#)--

This is the authoritative codebook for the Oxford Covid-19 Government Response Tracker. The dataset contains 24 indicators and a miscellaneous notes field organised into five groups (`C`, `E`, `H`, `V`, `M`); we also capture how policies vary based on vaccination status and geographic targeting. This codebook is divided into the following sections:
- [C - containment and closure policies](#c---containment-and-closure-policies)
- [E - economic policies](#e---economic-policies)
- [H - health system policies](#h---health-system-policies)
- [V - vaccination policies](#v---vaccination-policies)
- [M - miscellaneous policies](#m---miscellaneous-policies)
- [Additional variables with epidemiological statistics](#additional-variables-with-epidemiological-statistics)
- [General interpretation guidance](#general-interpretation-guidance)
- [Subnational data and the `jurisdiction` variable](#subnational-data-and-the-jurisdiction-variable)
- [Differentiation of policies by vaccine status (vaccine passports)](#differentiation-of-policies-by-vaccine-status-vaccine-passports)
- [Flag variables for geographic targeting or economic support](#flag-variables-for-geographic-targeting-or-economic-support)
- [Common issues](#common-issues)
- [Detailed interpretation guidance for each indicator](#detailed-interpretation-guidance-for-each-indicator)
- [Codebook changelog](#codebook-changelog)

Most indicators are recorded on an ordinal scale that represents the level of strictness of the policy. Four of the indicators (`E3`, `E4`, `H4` and `H5`) are recorded as a US dollar value of fiscal spending. V1 records categorical data and the ranked order of prioritised groups for vaccination in a population.

In August 2021 OxCGRT stopped updating data for the `E3`, `E4` and `H4` fiscal indicators. `H5` (vaccine spending) may also be incomplete. The data for these indicators remains in the CSV files, but they have not been updated beyond August 2021 and they have not been through the same quality checking and review process as the other indicators.

The indicators are of four types:
- **Ordinal**: These indicators measure policies on a simple scale of severity / intensity. These indicators are reported for each day a policy is in place.
  - Many have a further flag to note if they are “targeted”, applying only to a sub-region of a jurisdiction, or a specific sector; or “general”, applying throughout that jurisdiction or across the economy. (Note, the flag for indicators `E1` and `H7` means something different.)
- **Numeric**: These indicators measure a specific number, typically the value in USD. These indicators are only reported on the day they are announced.
- **Text**: This is a “free response” indicator that records other information of interest.
- **Categorical**: These indicators have a range of eligible categories to select, and in some instances, rank (i.e. vaccine prioritisation/eligibility policies).
- **Binary**: This measures the presence (1) or absence (0) of a requirement to be vaccinated for certain groups

All observations also have a “notes” cell that reports sources and comments to justify and substantiate the designation.

Table 1: OxCGRT Indicators:
| ID | Name | Type | Targeted / General? | Differentiation based on vaccination status? |
| --- | --- | --- | --- | --- |
| C1 | School closing | Ordinal | Geographic | Yes |
| C2 | Workplace closing | Ordinal | Geographic | Yes |
| C3 | Cancel public events | Ordinal | Geographic | Yes |
| C4 | Restrictions on gathering size | Ordinal | Geographic | Yes |
| C5 | Close public transport | Ordinal | Geographic | Yes |
| C6 | Stay at home requirements | Ordinal | Geographic | Yes |
| C7 | Restrictions on internal movement | Ordinal | Geographic | Yes |
| C8 | Restrictions on international travel | Ordinal | No | Yes |
| E1 | Income support | Ordinal | Sectoral | No |
| E2 | Debt/contract relief for households | Ordinal | No | No |
| E3 | Fiscal measures | Numeric | No | No |
| E4 | Giving international support | Numeric | No | No |
| H1 | Public information campaign | Ordinal | Geographic | No |
| H2 | Testing policy | Ordinal | No | No |
| H3 | Contact tracing | Ordinal | No | No |
| H4 | Emergency investment in healthcare | Numeric | No | No |
| H5 | Investment in Covid-19 vaccines | Numeric | No | No |
| H6 | Facial coverings | Ordinal | Geographic | Yes |
| H7 | Vaccination Policy | Ordinal | Cost | No |
| H8 | Protection of elderly people | Ordinal | No | Yes |


## C - containment and closure policies
--[return to top](#)-- <br/>
--[return to start of codebook](#codebook-and-interpretation-guidance)--

| ID | Name | Description | Measurement | Coding |
| --- | --- | --- | --- | --- |
| C1 | `C1E_School closing`<br/> `C1NV_School closing`<br/> `C1V_School closing`<br/> `C1M_School closing`| Record closings of schools and universities | Ordinal scale | 0 - no measures <br/>1 - recommend closing or all schools open with alterations resulting in significant differences compared to non-Covid-19 operations <br/>2 - require closing (only some levels or categories, eg just high school, or just public schools) <br/>3 - require closing all levels <br/>Blank - no data |
||  `C1E_Flag` <br/>`C1NV_Flag` <br/>`C1V_Flag`<br/> `C1M_Flag` || Binary flag for geographic scope | 0 - targeted <br/>1- general <br/>Blank - no data |
| C2 | `C2E_Workplace closing`<br/> `C2NV_Workplace closing`<br/> `C2V_Workplace closing`<br/> `C2M_Workplace closing` | Record closings of workplaces | Ordinal scale | 0 - no measures <br/>1 - recommend closing (or recommend work from home) or all businesses open with alterations resulting in significant differences compared to non-Covid-19 operation <br/>2 - require closing (or work from home) for some sectors or categories of workers <br/>3 - require closing (or work from home) for all-but-essential workplaces (eg grocery stores, doctors) <br/>Blank - no data |
| |  `C2E_Flag`<br/> `C2NV_Flag`<br/> `C2V_Flag`<br/> `C2M_Flag` | | Binary flag for geographic scope | 0 - targeted <br/>1- general <br/>Blank - no data |
| C3 | `C3E_Cancel public events` <br/>`C3NV_Cancel public events` <br/>`C3V_Cancel public events`<br/> `C3M_Cancel public events` | Record cancelling public events | Ordinal scale | 0 - no measures <br/>1 - recommend cancelling <br/>2 - require cancelling <br/>Blank - no data |
| |  `C3E_Flag` <br/>`C3NV_Flag`<br/> `C3V_Flag`<br/> `C3M_Flag` | | Binary flag for geographic scope | 0 - targeted <br/>1- general <br/>Blank - no data |
| C4 | `C4E_Restrictions on gatherings`<br/> `C4NV_Restrictions on gatherings`<br/> `C4V_Restrictions on gatherings`<br/> `C4M_Restrictions on gatherings` | Record the cut-off size for limits on gatherings | Ordinal scale | 0 - no restrictions <br/>1 - restrictions on very large gatherings (the limit is above 1000 people) <br/>2 - restrictions on gatherings between 101-1000 people <br/>3 - restrictions on gatherings between 11-100 people <br/>4 - restrictions on gatherings of 10 people or less <br/>Blank - no data |
| |  `C4E_Flag`<br/> `C4NV_Flag` <br/>`C4V_Flag`<br/> `C4M_Flag` | | Binary flag for geographic scope | 0 - targeted <br/>1- general <br/>Blank - no data |
| C5 | `C5E_Close public transport`<br/> `C5NV_Close public transport` <br/>`C5V_Close public transport`<br/> `C5M_Close public transport` | Record closing of public transport | Ordinal scale | 0 - no measures <br/>1 - recommend closing (or significantly reduce volume/route/means of transport available) <br/>2 - require closing (or prohibit most citizens from using it) <br/>Blank - no data |
| |  `C5E_Flag`<br/> `C5NV_Flag`<br/> `C5V_Flag`<br/> `C5M_Flag` | | Binary flag for geographic scope | 0 - targeted <br/>1- general <br/>Blank - no data |
| C6 | `C6E_Stay at home requirements`<br/> `C6NV_Stay at home requirements`<br/> `C6V_Stay at home requirements`<br/> `C6M_Stay at home requirements` | Record orders to "shelter-in-place" and otherwise confine to the home | Ordinal scale | 0 - no measures <br/>1 - recommend not leaving house <br/>2 - require not leaving house with exceptions for daily exercise, grocery shopping, and 'essential' trips <br/>3 - require not leaving house with minimal exceptions (eg allowed to leave once a week, or only one person can leave at a time, etc) <br/>Blank - no data |
| |  `C6E_Flag`<br/> `C6NV_Flag`<br/> `C6V_Flag`<br/> `C6M_Flag` | | Binary flag for geographic scope | 0 - targeted <br/>1- general <br/>Blank - no data |
| C7 | `C7E_Restrictions on internal movement`<br/> `C7NV_Restrictions on internal movement`<br/> `C7V_Restrictions on internal movement` <br/>`C7M_Restrictions on internal movement` | Record restrictions on internal movement between cities/regions | Ordinal scale | 0 - no measures <br/>1 - recommend not to travel between regions/cities <br/>2 - internal movement restrictions in place <br/>Blank - no data |
| |  `C7E_Flag`<br/> `C7NV_Flag`<br/> `C7V_Flag`<br/> `C7M_Flag` | | Binary flag for geographic scope | 0 - targeted <br/>1- general <br/>Blank - no data |
| C8 | `C8E_International travel controls`<br/> `C8NV_International travel controls`<br/> `C8EV_International travel controls` | Record restrictions on international travel <br/><br/>Note: this records policy for foreign travellers, not citizens | Ordinal scale | 0 - no restrictions <br/>1 - screening arrivals <br/>2 - quarantine arrivals from some or all regions <br/>3 - ban arrivals from some regions <br/>4 - ban on all regions or total border closure <br/>Blank - no data |

## E - economic policies
--[return to top](#)-- <br/>
--[return to start of codebook](#codebook-and-interpretation-guidance)--

| ID | Name | Description | Measurement | Coding |
| --- | --- | --- | --- | --- |
| E1 | `E1_Income support` <br/>(for households)<br/> *no differentiated policies reported in this indicator | Record if the government is providing direct cash payments to people who lose their jobs or cannot work. <br/><br/>Note: only includes payments to firms if explicitly linked to payroll/salaries | Ordinal scale | 0 - no income support <br/>1 - government is replacing less than 50% of lost salary (or if a flat sum, it is less than 50% median salary) <br/>2 - government is replacing 50% or more of lost salary (or if a flat sum, it is greater than 50% median salary) <br/>Blank - no data |
| | `E1_Flag` | | Binary flag for sectoral scope | 0 - formal sector workers only or informal sector workers only <br/>1 - all workers
| E2 | `E2_Debt/contract relief` <br/>(for households) <br/>*no differentiated policies reported in this indicator | Record if the government is freezing financial obligations for households (eg stopping loan repayments, preventing services like water from stopping, or banning evictions) | Ordinal scale | 0 - no debt/contract relief <br/>1 - narrow relief, specific to one kind of contract <br/>2 - broad debt/contract relief |
| E3 | `E3_Fiscal measures` <br/>*no differentiated policies reported in this indicator | Announced economic stimulus spending <br/><br/>Note: only record amount additional to previously announced spending | USD | Record monetary value in USD of fiscal stimuli, includes any spending or tax cuts NOT included in E4, H4 or H5 <br/>0 - no new spending that day <br/>Blank - no data |
| E4 | `E4_International support` <br/>*no differentiated policies reported in this indicator | Announced offers of Covid-19 related aid spending to other countries <br/><br/>Note: only record amount additional to previously announced spending | USD | Record monetary value in USD <br/>0 - no new spending that day <br/>Blank - no data |

## H - health system policies
--[return to top](#)-- <br/>
--[return to start of codebook](#codebook-and-interpretation-guidance)--

| ID | Name | Description | Measurement | Coding |
| --- | --- | --- | --- | --- |
| H1 | `H1_Public information campaigns` <br/>*no differentiated policies reported in this indicator | Record presence of public info campaigns | Ordinal scale | 0 - no Covid-19 public information campaign <br/>1 - public officials urging caution about Covid-19 <br/>2- coordinated public information campaign (eg across traditional and social media) <br/>Blank - no data |
| | `H1_Flag` | | Binary flag for geographic scope |  0 - targeted <br/>1- general <br/>Blank - no data |
| H2 | `H2_Testing policy` <br/>*no differentiated policies reported in this indicator | Record government policy on who has access to testing <br/><br/>Note: this records policies about testing for current infection (PCR tests) not testing for immunity (antibody test) | Ordinal scale | 0 - no testing policy <br/>1 - only those who both (a) have symptoms AND (b) meet specific criteria (eg key workers, admitted to hospital, came into contact with a known case, returned from overseas) <br/>2 - testing of anyone showing Covid-19 symptoms <br/>3 - open public testing (eg "drive through" testing available to asymptomatic people) <br/>Blank - no data |
| H3 | `H3_Contact tracing` <br/>*no differentiated policies reported in this indicator | Record government policy on contact tracing after a positive diagnosis | Ordinal scale | 0 - no contact tracing <br/>1 - limited contact tracing; not done for all cases <br/>2 - comprehensive contact tracing; done for all identified cases |
| H4 | `H4_Emergency investment in healthcare` *no differentiated policies reported in this indicator | Announced short term spending on healthcare system, eg hospitals, masks, etc <br/><br/>Note: only record amount additional to previously announced spending | USD | Record monetary value in USD <br/>0 - no new spending that day <br/>Blank - no data |
| H5 | `H5_Investment in vaccines` <br/>*no differentiated policies reported in this indicator | Announced public spending on Covid-19 vaccine development <br/><br/>Note: only record amount additional to previously announced spending | USD | Record monetary value in USD <br/>0 - no new spending that day <br/>Blank - no data |
| H6 | `H6E_Facial coverings`<br/> `H6NV_Facial coverings`<br/> `H6V_Facial coverings` <br/>`H6M_Facial coverings` | Record policies on the use of facial coverings outside the home <br/> | Ordinal scale | 0 - No policy <br/>1 - Recommended <br/>2 - Required in some specified shared/public spaces outside the home with other people present, or some situations when social distancing not possible <br/>3 - Required in all shared/public spaces outside the home with other people present or all situations when social distancing not possible <br/>4 - Required outside the home at all times regardless of location or presence of other people |
| | `H6E_Flag`<br/> `H6NV_Flag`<br/> `H6V_Flag`<br/> `H6M_Flag` | | Binary flag for geographic scope |  0 - targeted <br/>1- general <br/>Blank - no data |
| H7 | `H7_Vaccination policy` <br/>*no differentiated policies reported in this indicator | Record policies for vaccine delivery for different groups <br/> | Ordinal scale | 0 - No availability <br/>1 - Availability for ONE of following: key workers/ clinically vulnerable groups (non elderly) / elderly groups <br/>2 - Availability for TWO of following: key workers/ clinically vulnerable groups (non elderly) / elderly groups <br/>3 - Availability for ALL of following: key workers/ clinically vulnerable groups (non elderly) / elderly groups <br/>4 - Availability for all three plus partial additional availability (select broad groups/ages) <br/>5 - Universal availability |
| | `H7_Flag` | | Binary flag for cost |  0 - At cost to individual (or funded by NGO, insurance, or partially government funded) <br/>1- No or minimal cost to individual (government funded or subsidised) <br/>Blank - no data |
| H8 | `H8E_Protection of elderly people`<br/> `H8NV_Protection of elderly people`<br/> `H8V_Protection of elderly people`<br/> `H8M_Protection of elderly people` | Record policies for protecting elderly people (as defined locally) in Long Term Care Facilities and/or the community and home setting | Ordinal scale | 0 - no measures <br/>1 - Recommended isolation, hygiene, and visitor restriction measures in LTCFs and/or elderly  people to stay at home <br/>2 - Narrow restrictions for isolation, hygiene in LTCFs, some limitations on external visitors and/or restrictions protecting elderly people at home <br/>3 - Extensive restrictions for isolation and hygiene in LTCFs, all non-essential external visitors prohibited, and/or all elderly people required to stay at home and not leave the home with minimal exceptions, and receive no external visitors <br/>Blank - no data |
| | `H8E_Flag`<br/> `H8NV_Flag`<br/> `H8V_Flag` <br/>`H8M_Flag` | | Binary flag for geographic scope | 0 - targeted <br/>1- general <br/>Blank - no data |

## V - vaccination policies
--[return to top](#)-- <br/>
--[return to start of codebook](#codebook-and-interpretation-guidance)--

The four V indicators focus on 53 different population groups – by occupation, risk status, and age group.

***V1 – Vaccine prioritisation*** is a categorical indicator that captures eligible and prioritised groups of people (e.g. profession, age, vulnerability, etc.), and shows the order in which these groups are prioritised for vaccines by their country/region/territory. ***V2 – Vaccine eligibility/availability*** (also a categorical indicator) is linked to V1, and indicates which of the prioritised groups are actually being vaccinated at that time. ***V3 – Vaccine financial support*** captures information on whether vaccines are government funded, or otherwise. ***V4- Mandatory Vaccination*** is a binary indicator which reports the existence of a requirement to be vaccinated for a group of people.

This means that each of the V indicators are actually reported as 53 different variables. We also summarise these into 10 summary indicators: `V1`, `V2A`, `V2B`, `V2C`, `V2D`, `V2E`, `V2F`, `V2G`, `V3`, and `V4`.

Most of the data under our vaccine indicators are directly recorded by OxCGRT data collectors. These variables are:

| ID | Name | Description | Measurement | Coding |
| --- | --- | --- | --- | --- |
| V1 | Vaccine prioritisation <br/> <br/>`V1_0-4 yrs Infants` <br/>`V1_5-15 yrs Young people` <br/>`V1_General 16-19 yrs` <br/>`V1_General 20-24 yrs` <br/>`V1_General 25-29 yrs` <br/>`V1_General 30-34 yrs` <br/>`V1_General 35-39 yrs` <br/>`V1_General 40-44 yrs` <br/>`V1_General 45-49 yrs` <br/>`V1_General 50-54 yrs` <br/>`V1_General 55-59 yrs` <br/>`V1_General 60-64 yrs` <br/>`V1_General 65-69 yrs` <br/>`V1_General 70-74 yrs` <br/>`V1_General 75-79 yrs` <br/>`V1_General 80+ yrs` <br/>`V1_At Risk 16-19 yrs` <br/>`V1_At Risk 20-24 yrs` <br/>`V1_At Risk 25-29 yrs` <br/>`V1_At Risk 30-34 yrs` <br/>`V1_At Risk 35-39 yrs` <br/>`V1_At Risk 40-44 yrs` <br/>`V1_At Risk 45-49 yrs` <br/>`V1_At Risk 50-54 yrs` <br/>`V1_At Risk 55-59 yrs` <br/>`V1_At Risk 60-64 yrs` <br/>`V1_At Risk 65-69 yrs` <br/>`V1_At Risk 70-74 yrs` <br/>`V1_At Risk 75-79 yrs` <br/>`V1_At Risk 80+ yrs` <br/>`V1_Airport/Border/Airline Staff` <br/>`V1_Clinically vulnerable/chronic illness/significant underlying health condition (excluding elderly and disabled)` <br/>`V1_Crowded/communal living conditions (dormitories for migrant workers, temporary accommodations)` <br/>`V1_Disabled People` <br/>`V1_Educators` <br/>`V1_Ethnic minorities` <br/>`V1_Factory workers` <br/>`V1_Frontline/essential workers (when subcategories not specified)` <br/>`V1_Frontline retail workers` <br/>`V1_Healthcare workers/carers (excluding care home staff)` <br/>`V1_Military` <br/>`V1_Other 'high contact' professions/groups (taxi drivers, security guards)` <br/>`V1_People living with a vulnerable/shielding person or other priority group` <br/>`V1_Police/ first responders` <br/>`V1_Pregnant people` <br/>`V1_Primary and secondary school students` <br/>`V1_Religious/Spiritual Leaders` <br/>`V1_Residents in an elderly care home` <br/>`V1_Staff working in an elderly care home` <br/>`V1_Tertiary education students` <br/>`V1_Refugees/migrants`<br/>`V1_Government Officials`| Record the ranked position for different groups within a jurisdiction's prioritisation plan | Rank order | Blank – category not selected for prioritisation<br/> <br/>1, 2, 3, 4... – category has been selected for prioritisation; number represents the rank of prioritisation; equal-ranked categories will share the same number |
| V2 | Vaccine eligibility/availability <br/> <br/>`V2_0-4 yrs Infants` <br/>`V2_5-15 yrs Young people` <br/>`V2_General 16-19 yrs` <br/>`V2_General 20-24 yrs` <br/>`V2_General 25-29 yrs` <br/>`V2_General 30-34 yrs` <br/>`V2_General 35-39 yrs` <br/>`V2_General 40-44 yrs` <br/>`V2_General 45-49 yrs` <br/>`V2_General 50-54 yrs` <br/>`V2_General 55-59 yrs` <br/>`V2_General 60-64 yrs` <br/>`V2_General 65-69 yrs` <br/>`V2_General 70-74 yrs` <br/>`V2_General 75-79 yrs` <br/>`V2_General 80+ yrs` <br/>`V2_At Risk 16-19 yrs` <br/>`V2_At Risk 20-24 yrs` <br/>`V2_At Risk 25-29 yrs` <br/>`V2_At Risk 30-34 yrs` <br/>`V2_At Risk 35-39 yrs` <br/>`V2_At Risk 40-44 yrs` <br/>`V2_At Risk 45-49 yrs` <br/>`V2_At Risk 50-54 yrs` <br/>`V2_At Risk 55-59 yrs` <br/>`V2_At Risk 60-64 yrs` <br/>`V2_At Risk 65-69 yrs` <br/>`V2_At Risk 70-74 yrs` <br/>`V2_At Risk 75-79 yrs` <br/>`V2_At Risk 80+ yrs` <br/>`V2_Airport/Border/Airline Staff` <br/>`V2_Clinically vulnerable/chronic illness/significant underlying health condition (excluding elderly and disabled)` <br/>`V2_Crowded/communal living conditions (dormitories for migrant workers, temporary accommodations)` <br/>`V2_Disabled People` <br/>`V2_Educators` <br/>`V2_Ethnic minorities` <br/>`V2_Factory workers` <br/>`V2_Frontline/essential workers (when subcategories not specified)` <br/>`V2_Frontline retail workers` <br/>`V2_Healthcare workers/carers (excluding care home staff)` <br/>`V2_Military` <br/>`V2_Other 'high contact' professions/groups (taxi drivers, security guards)` <br/>`V2_People living with a vulnerable/shielding person or other priority group` <br/>`V2_Police/ first responders` <br/>`V2_Pregnant people` <br/>`V2_Primary and secondary school students` <br/>`V2_Religious/Spiritual Leaders` <br/>`V2_Residents in an elderly care home` <br/>`V2_Staff working in an elderly care home` <br/>`V2_Tertiary education students` <br/>`V2_Refugees/migrants`<br/>`V2_Government Officials`| Record which categories of people – regardless of their position in a prioritised rollout plan – are currently receiving vaccines | Categorical/ binary | Blank – no data <br/>0 - vaccines are not being made available to this category <br/>1 - vaccines are being made available to this category |
| V3 | Vaccine financial support <br/> <br/>`V3_0-4 yrs Infants` <br/>`V3_5-15 yrs Young people` <br/>`V3_General 16-19 yrs` <br/>`V3_General 20-24 yrs` <br/>`V3_General 25-29 yrs` <br/>`V3_General 30-34 yrs` <br/>`V3_General 35-39 yrs` <br/>`V3_General 40-44 yrs` <br/>`V3_General 45-49 yrs` <br/>`V3_General 50-54 yrs` <br/>`V3_General 55-59 yrs` <br/>`V3_General 60-64 yrs` <br/>`V3_General 65-69 yrs` <br/>`V3_General 70-74 yrs` <br/>`V3_General 75-79 yrs` <br/>`V3_General 80+ yrs` <br/>`V3_At Risk 16-19 yrs` <br/>`V3_At Risk 20-24 yrs` <br/>`V3_At Risk 25-29 yrs` <br/>`V3_At Risk 30-34 yrs` <br/>`V3_At Risk 35-39 yrs` <br/>`V3_At Risk 40-44 yrs` <br/>`V3_At Risk 45-49 yrs` <br/>`V3_At Risk 50-54 yrs` <br/>`V3_At Risk 55-59 yrs` <br/>`V3_At Risk 60-64 yrs` <br/>`V3_At Risk 65-69 yrs` <br/>`V3_At Risk 70-74 yrs` <br/>`V3_At Risk 75-79 yrs` <br/>`V3_At Risk 80+ yrs` <br/>`V3_Airport/Border/Airline Staff` <br/>`V3_Clinically vulnerable/chronic illness/significant underlying health condition (excluding elderly and disabled)` <br/>`V3_Crowded/communal living conditions (dormitories for migrant workers, temporary accommodations)` <br/>`V3_Disabled People` <br/>`V3_Educators` <br/>`V3_Ethnic minorities` <br/>`V3_Factory workers` <br/>`V3_Frontline/essential workers (when subcategories not specified)` <br/>`V3_Frontline retail workers` <br/>`V3_Healthcare workers/carers (excluding care home staff)` <br/>`V3_Military` <br/>`V3_Other 'high contact' professions/groups (taxi drivers, security guards)` <br/>`V3_People living with a vulnerable/shielding person or other priority group` <br/>`V3_Police/ first responders` <br/>`V3_Pregnant people` <br/>`V3_Primary and secondary school students` <br/>`V3_Religious/Spiritual Leaders` <br/>`V3_Residents in an elderly care home` <br/>`V3_Staff working in an elderly care home` <br/>`V3_Tertiary education students` <br/>`V3_Refugees/migrants`<br/>`V3_Government Officials`| Record how vaccines are funded for each category of people identified in V2 as currently receiving vaccines. | Ordinal scale | Blank - no data <br/>0 - full cost borne by the individual (or through private health insurance) or no policy <br/>1 - partially funded by government and individual pays nominal fee <br/>2 -  fully covered by government funding, FREE |
| V4 | V4_Vaccine requirement/mandate <br/> <br/>`V4_0-4 yrs Infants` <br/>`V4_5-15 yrs Young people` <br/>`V4_General 16-19 yrs` <br/>`VV4_General 20-24 yrs` <br/>`V4_General 25-29 yrs` <br/>`V4_General 30-34 yrs` <br/>`V4_General 35-39 yrs` <br/>`V4_General 40-44 yrs` <br/>`V4_General 45-49 yrs` <br/>`V4_General 50-54 yrs` <br/>`V4_General 55-59 yrs` <br/>`V4_General 60-64 yrs` <br/>`V4_General 65-69 yrs` <br/>`V4_General 70-74 yrs` <br/>`V4_General 75-79 yrs` <br/>`V4_General 80+ yrs` <br/>`V4_At Risk 16-19 yrs` <br/>`V4_At Risk 20-24 yrs` <br/>`V4_At Risk 25-29 yrs` <br/>`V4_At Risk 30-34 yrs` <br/>`V4_At Risk 35-39 yrs` <br/>`V4_At Risk 40-44 yrs` <br/>`VV4_At Risk 45-49 yrs` <br/>`V4_At Risk 50-54 yrs` <br/>`V4_At Risk 55-59 yrs` <br/>`V4_At Risk 60-64 yrs` <br/>`V4_At Risk 65-69 yrs` <br/>`V4_At Risk 70-74 yrs` <br/>`V4_At Risk 75-79 yrs` <br/>`V4_At Risk 80+ yrs` <br/>`V4_Airport/Border/Airline Staff` <br/>`V4_Clinically vulnerable/chronic illness/significant underlying health condition (excluding elderly and disabled)` <br/>`V4_Crowded/communal living conditions (dormitories for migrant workers, temporary accommodations)` <br/>`V4_Disabled People` <br/>`V4_Educators` <br/>`V4_Ethnic minorities` <br/>`V4_Factory workers` <br/>`V4_Frontline/essential workers (when subcategories not specified)` <br/>`V4_Frontline retail workers` <br/>`V4_Healthcare workers/carers (excluding care home staff)` <br/>`V4_Military` <br/>`V4_Other 'high contact' professions/groups (taxi drivers, security guards)` <br/>`V4_People living with a vulnerable/shielding person or other priority group` <br/>`V4_Police/ first responders` <br/>`V4_Pregnant people` <br/>`V4_Primary and secondary school students` <br/>`V4_Religious/Spiritual Leaders` <br/>`V4_Residents in an elderly care home` <br/>`V4_Staff working in an elderly care home` <br/>`V4_Tertiary education students` <br/>`V4_Refugees/migrants`<br/>`V4_Government Officials`| Reports the existence of a requirement to be vaccinated  | Binary | Blank - no data <br/>0 - no requirement to be vaccinated <br/>1 - requirement to be vaccinated |

<br/>
Additional summary indicators are automatically generated by our database, based on data entered for variables in the previous table:

| ID | Name | Description | Measurement | Coding |
| --- | --- | --- | --- | --- |
| V1 | `V1_Vaccine prioritisation (summary)` | Reports the existence of a prioritised plan for vaccine rollout | Ordinal scale| Blank – no data <br/>0 - no plan <br/> 1 – a prioritised plan is in place <br/>2 – universal/general eligibility; no prioritisation between groups |
| V2A | `V2_Vaccine eligibility/availability (summary)` | Reports whether any categories of people are receiving vaccines | Ordinal scale | Blank – no data <br/>0 – no categories  are receiving vaccines <br/>1 – vaccines are available to some categories <br/>2 – vaccines are available to anyone over the age of 16 yrs <br/>3 – vaccines are available to anyone over the age of 16 yrs PLUS one or both of 5-15 yrs and 0-4 yrs |
| V2B | `V2B_Vaccine age eligibility/availability age floor(General population summary)` | Reports lowest age range of general population being vaccinated| Numerical | Blank – no data <br/><br/>0 – no categories  are receiving vaccines <br/><br/>numerical range – Lowest age range for ‘General’ category |
| V2C | `V2C_Vaccine age eligibility/availability age floor(At-risk population summary)` | Reports lowest age range of at risk population being vaccinated| Numerical | Blank – no data <br/><br/>0 – no categories  are receiving vaccines <br/><br/>numerical range – Lowest age range from either ‘General’ or ‘At-risk’ categories |
| V2D | `V2D_Medically/ clinically vulnerable (Non-elderly)` | Reports the number of categories selected from thematic group: <br/>`V2_At risk` age ranges below 60 (one or more selected counts as 1 x category) <br/>`V2_Clinically vulnerable/chronic illness/significant underlying health condition (excluding elderly and disabled)` <br/>`V2_Disabled people` <br/>`V2_Pregnant people` <br/>`V2_People living with a vulnerable/shielding person or other priority group` | Ordinal | Blank – no data <br/>0 – no categories  are receiving vaccines <br/>1 – 1 or 2 categories in group selected <br/>2 – 3 or more categories selected or all from `V2_General 16-19 years` up to `V2_General 80+ years`present|
| V2E| `V2E_Education` | Reports the number of categories selected from thematic group: </br>`V2_Educators` <br/>`V2_Primary and secondary school students` <br/>`V2_Tertiary education students` | Ordinal | Blank – no data <br/>0 – no categories  are receiving vaccines <br/>1 – 1 category in group selected <br/>2 - 2 or more categories selected or all from `V2_General 16-19 years` up to `V2_General 80+ years`present |
| V2F| `V2F_Frontline workers  (non healthcare)`| Reports the number of categories selected from thematic group: `V2_Police/first responders` <br/>`V2_Airport/Border/Airline staff` <br/>`V2_Factory workers` <br/>`V2_Frontline retail workers` <br/>`V2_Military` <br/>`V2_Other high contact professions/groups (taxi drivers, security guards)` <br/>`V2_Frontline/essential workers (when subcategories not specified)` (triggers an automatic 2) | Ordinal | Blank – no data <br/>0 – no categories  are receiving vaccines <br/>1 – 1 or 2 categories in group selected <br/>2 - 3 or more categories selected or all from `V2_General 16-19 years` up to `V2_General 80+ years`present |
| V2G| `V2F_Frontline workers  (healthcare)` | Reports the number of categories selected from thematic group: <br/>`V2_Staff working in an elderly care home` <br/>`V2_Healthcare workers/carers (excluding care home staff)` | Ordinal | Blank – no data <br/>0 – no categories  are receiving vaccines <br/>1 – 1 category in group selected <br/>2 - 2 categories selected or all from `V2_General 16-19 years` up to `V2_General 80+ years`present |
| V3 | `V3_Vaccine financial support (summary)` | Reports the overall approach taken to vaccine funding – whether paid by the individual or the government | Ordinal scale | Blank - no data <br/>0 – no availability <br/> 1 – full cost to the individual for all categories identified in V2 <br/> 2 – full cost to the individual for some categories identified in V2, some subsidy for other categories <br/> 3- partial funding by the government for all of the categories identified in V2 <br/> 4 – partial funding by the government for some categories identified in V2, full funding for other categories <br/> 5 – all categories fully funded by the government |
| V4 | `V4_Mandatory Vaccination (summary)` | Reports the existence of a requirement to be vaccinated | Binary | Blank - no data <br/>0 – no requirement to be vaccinated <br/> 1 – requirement to be vaccinated is in place for one or more groups |

## M - miscellaneous policies
--[return to top](#)-- <br/>
--[return to start of codebook](#codebook-and-interpretation-guidance)--

| ID | Name | Description | Measurement | Coding |
| --- | --- | --- | --- | --- |
| M1 | `M1_Notes` | Records policy announcements that do not fit anywhere else | Free text notes field | Note unusual or interesting interventions that our data collectors think are worth flagging  |

## Additional variables with epidemiological statistics
--[return to top](#)-- <br/>
--[return to start of codebook](#codebook-and-interpretation-guidance)--

| ID | Name | Description | Measurement | Coding |
| --- | --- | --- | --- | --- |
|  | `ConfirmedCases` | The cumulative number of reported covid-19 cases since the beginning of the pandemic | Number | Number of covid-19 cases |
|  | `ConfirmedDeaths` | The cumulative number of deaths attributed to covid-19 since the beginning of the pandemic | Number | Number of covid-19 deaths |
|  | `PopulationVaccinated` | The percentage of fully vaccinated population in the jurisdiction, or, depending on data availability, a binary indicator of whether the majority of people are unvaccinated or vaccinated. | Percentage OR binary indicator | Number - the proportion of the population that is reported as vaccinated by that jurisdiction <br/>Under 50%* - the majority of the population is unvaccinated (used in jurisdictions where we do not have regular vaccination rate data) <br/> Over 50%* - the majority of the population is vaccinated (used in jurisdictions where we do not have regular vaccination rate data) <br/> Blank - no data |
|  | `MajorityVaccinated` | Record a binary indicator of majority (non-)vaccinated. <br/> <br/> We primarily source this data from [Our World In Data’s](https://ourworldindata.org/covid-vaccinations)  'fullyvaccinatedperhundred' data series | Binary indicator | NV - the majority of the population is unvaccinated (used in jurisdictions where we do not have regular vaccination rate data) <br/> V - the majority of the population is vaccinated (used in jurisdictions where we do not have regular vaccination rate data) <br/> Blank - no data |

See our full working paper at https://www.bsg.ox.ac.uk/covidtracker for detailed source notes for all of this epidemiological data.

## General interpretation guidance
--[return to top](#)-- <br/>
--[return to start of codebook](#codebook-and-interpretation-guidance)--

There are a few general rules that apply to our data:

 - **We report the most stringent government policy** with the highest ordinal value. If the most stringent policy is only present in a limited geographic area or sector, we use a binary flag variable for most indicators to describe this scope and reflect whether the policy is targeted or general.
- **Implementation not announcement:** We start coding a policy from the day the policy was implemented in practice, not the day it was announced (except for `V1`, where the policy being recorded is the announced prioritisation list, not the actual availability of vaccines).
 - **If coding a country with a contested government or multiple ruling parties**, we try to code the “dominant tendency” in the jurisdiction, which generally means recording the policies of the more formalised government, or the one which governs the larger proportion of the population
 - **Where testing/vaccination exemptions are in place we still report this as a closure**. Some governments implement restrictions where citizens can gain exemption through evidence of testing or vaccination. We deal with this primarily through our differentiated coding (see more below). But the general rule is that – apart from differentiated coding – we still report the more stringent government policy that applies to people who do not obtain an exemption. The only time we would report the more open policy is if anyone can arrive and get tested onsite with a rapid test to gain entry. We would not code this as a required closure, as anyone can ‘test out’ of restrictions easily. Such at-the-door testing must apply to all sectors within the indicator, and be a government policy, not that of a private business.

## Subnational data and the `jurisdiction` variable
--[return to top](#)-- <br/>
--[return to start of codebook](#codebook-and-interpretation-guidance)--

OxCGRT includes data for nearly all national-level countries, territories, and regions in the world. It also includes subnational-level data for Australia, Brazil, Canada, China, India, the United Kingdom, and the United States of America (as well as partial data for Italian regions).

OxCGRT data are used in different ways – for instance some people may want to know the policies that apply to all residents in a state, while others may want to know the policies adopted by that state government (as opposed to the national government). To distinguish between these uses, different published versions of OxCGRT data are tagged in the database using the `jurisdiction` variable.

The `jurisdiction` variable is composed of two parts: the first part specifies the level of government (ie. `NAT`, `STATE`, or `CITY`) and the second part of the variable describes type of data (ie. `TOTAL`, `WIDE`, or `GOV`), which we will describe next.

- The jurisdiction label `TOTAL` simply represents the total package of policies that apply to a resident in that jurisdiction, regardless of which level of government set the policy. This seems to be the most common use case for OxCGRT data. For example, observations labelled `AUS_NSW STATE_TOTAL` describe the policies that apply to people in the state of New South Wales, regardless of whether the policy comes from the state government, the federal Australian government, or even the city of Sydney.
- The jurisdiction label `GOV` indicates that observations include only policies instigated by a particular level of government; higher- or lower-level jurisdictions do not inform the coding of these observations.
- The jurisdiction label `WIDE` refers to policies put in place at a given level of government as well as any policies from lower levels of government. `WIDE` observations therefore do not incorporate general policies from higher levels of government that may supersede local policies. Continuing to examine the case of New South Wales, the data recorded for `AUS_NSW STATE_WIDE` would include any policies made by the state government of New South Wales in Australia plus policies from municipal governments (eg. cities) within New South Wales, but not policies from the Australian federal government. For example, if a country has an international travel restriction (indicator `C8`) set by the national government, we would report this policy in our `STATE_TOTAL` data but not `STATE_WIDE` data.

Note that `CITY_GOV` and `NAT_WIDE` are not used, since these are functionally equivalent to `CITY_WIDE` and `NAT_TOTAL`, given that we do not consider units below city level or above national level.

Table 2: Currently available OxCGRT data across different levels of government and types of observations:
|  | `TOTAL` | `WIDE` | `GOV` |
| --- | --- | --- | --- |
| National | 185 countries | N/A | <ul><li>Australia </li><li>Brazil </li><li>Canada </li><li>China </li><li>United Kingdom </li><li>India </li><li>USA </li></ul> |
| State/province | <ul><li>Australia: 8 states/territories </li><li>Brazil: 26 states & Federal District </li><li>Canada: 13 provinces/territories </li><li>China: 31 province-level divisions </li><li>United Kingdom: 4 devolved nations </li><li>India: 36 states and territories </li><li>USA: 50 states & Washington DC </li></ul> | <ul><li>Canada: 13 provinces/territories </li><li>China: 31 province-level divisions </li><li>United Kingdom: 4 devolved nations </li><li>India: 36 states and territories </li><li>USA: 50 states & Washington DC </li></ul> | <ul><li>Australia: 8 states/territories </li><li>Brazil: 26 states & Federal District </li></ul> |
| City | <ul><li>Australia: 7 state/territory capital cities and 7 “rest of” states and territories </li><li>Brazil: 26 state capital cities plus Brazilia </li></ul> | <ul><li>Australia: 7 state/territory capital cities and 7 “rest of” states and territories </li><li>Brazil: 26 state capital cities plus Brazilia </li></ul> | N/A |

### Imputing subnational data that we have not manually collected

While we *report* all of the data types described in Table 2 above, we do not *collect* them all manually. Because of the logical connections between `TOTAL`, `WIDE`, and `GOV` data, it is possible to impute some of the data types based on existing data.

Specifically, `STATE_TOTAL` and `CITY_TOTAL` data is always imputed from other data. We start with the data collected at that jurisdictional level (ie. `STATE_WIDE`, `STATE_GOV` or `CITY_WIDE`) and we replace these responses with relevant responses from higher levels of government (ie. `NAT_GOV`) when the following two conditions are met:
- The corresponding `NAT_GOV` indicator is general, not targeted, and therefore is applied across the whole country
- The corresponding `NAT_GOV` indicator is equal to or greater than the `STATE_WIDE` or `STATE_GOV` indicator on the ordinal scale for that indicator

In this way, `NAT_TOTAL` and `STATE_TOTAL` measures in the core dataset are comparable, in that they show the totality of policies in effect within a given jurisdiction. We apply the same process in cities with both `STATE_GOV` and `NAT_GOV` policies to ensure that `CITY_TOTAL` also reports the totality of policies that affect people in the city.

For example, this will often occur with border closures C8. There will be a border policy from a federal government (ie. C8=4 reported for `NAT_GOV`). The state and municipal governments will not have a border policy because they don’t control borders (ie. C8=0 for `STATE_WIDE`). But when imputing `STATE_TOTAL`, we will carry over the higher C8=4.

If a state robustly re-implements a national policy under their own rules, for example by repeating national rules in the state’s legislation, this will be reported independently under the subnational jurisdiction coding.

## Differentiation of policies by vaccine status (vaccine passports)
--[return to top](#)-- <br/>
--[return to start of codebook](#codebook-and-interpretation-guidance)--

Many countries used “vaccine passports” or similar policies that apply restrictions only to unvaccinated people. Ten of our indicators are split into separate variables for non-vaccinated (`NV`) people and vaccinated (`V`) people where policies differ between these groups. We refer to this as a ‘differentiated policy’: where vaccinated people can access greater freedoms due to their vaccination status, and are subject to less stringent restrictions. The ten differentiated indicators are: `C1`, `C2`, `C3`, `C4`, `C5`, `C6`, `C7`, `C8`, `H6` and `H8`.

For each these 10 indicators, we publish four different versions. Three versions to capture the three different populations a policy might apply to (those that apply to *everyone*, *non-vaccinated*, or *vaccinated* people), plus a fourth version to summarise this into a single variable.

The three differentiated versions of each indicator:
- **Everyone** (eg. `C1E_School closing`): if the same policy applies to everyone, regardless of vaccination status we report the policy as an ‘Everyone’ value. If this is the case, there will then be no values reported in the ‘Non-vaccinated’ or ‘Vaccinated’ variables, and this value is repeated in the ‘Majority’ variable. These variables will be empty during periods when vaccine-differentiated policies are in place.
  - **Note:** the use of the `E` modifier here to signal that a policy applies to everyone without differentiation is *different* to the use of `E` to denote our E1-E4 indicators about economic response (eg. `E1_Income support`).
- **Non-vaccinated** (eg. `C1NV_School closing`): if there is a requirement to present a negative test, or proof of vaccination to gain entry to optional aspects of public life (e.g., events, businesses), it is recorded as a closure to non-vaccinated people. If this is the case, we will also publish the policy that applies to vaccinated people, but there will be no value published in the ‘Everyone’ variable.
- **Vaccinated** (eg. `C1V_School closing`): if entry can be gained or movement is allowed with just proof of vaccination, we report this as being open for people who are vaccinated. The policy may be a ‘1’ level code if significant operational differences remain, or even a ‘2’ level if vaccinated people are also required to present a negative test, as well as proof of vaccination (this is reported as a closure to reflect this stringency of policy). There will be no value published in the ‘Everyone’ variable if a ‘Vaccinated’ value is published.
  - **Note:** the use of the `V` modifier here to signal that a C or H policy applies to vaccinated people (eg. `C1V_School closing`) is *different* to the use of `V` to denote our V1-V4 indicators that describe specific policies about vaccine rollout (eg. `V1_Vaccine prioritisation (summary)`)

The fourth summary version of each indicator:
- **Majority** (eg. `C1M_School closing`):  the ‘Majority’ value reflects either the policy for everyone (eg. `C1E_School closing`), or the policy applying to the *majority* of people in a country, using vaccination rate data to determine if this is the vaccinated (eg. `C1V_School closing`) or non-vaccinated (eg. `C1NV_School closing`) part of the population.
  - reporting the "majority" policy means that sometimes the value of an `M` indicator will change due to the vaccinated rate crossing the 50% value, meaning our `M` indicator would report the `V` value instead of the `NV` value, even though there is no change to goverenment policy on that day. This is explained with a note when it occurs.
  - **Note:** the use of the `M` modifier here to signify the policy that applies to the majority of people is different to the use of `M` to denote out `M1_Notes` field which is used for miscellaneous notes from our data collectors.
- **Vaccinated or Everyone** (only for `C8EV_International travel controls`):  if there is a border policy that applies to everyone (`E`), we include it. If there are differentiated policies in place, we publish the vaccinated (`V`) value for `C8` in order to report the policies applying to (vaccinated) international arrivals. Because we do not have data on the vaccination rates of prospective travellers (as we do for the domestic population), we cannot publish a ‘majority’ variable. Note that this will not reflect if a country has quarantine requirements for non-vaccinated arrivals (or border closures to non-vaccinated travellers).

To summarise, depending on whether or not a country has policies differentiated by vaccine status, we will publish some combination of the following versions of each indicator.

Table 3. Summary of differentiated policy indicator types:
| Policy setting | E | NV | V | M / EV |
| --- | --- | --- | --- | --- |
| The same policy applies to everyone | yes | x | x | yes |
| There is a differentiated policy | x | yes | yes | yes |

For most indicators with differentiated policies, the `M` variable (majority, eg. `C1M_School closing`) is the only variable that provides an uninterrupted series of data; it is always populated. For C8, there is no `M` version, but we instead publish `C8EV_International travel controls` to create an uninterrupted series.

To calculate majority (`M`) versions of indicators, we use the variable `PopulationVaccinated`. We also include another variable, `MajorityVaccinated` to indicate whether the majority of the population in that country/region/jurisdiction is either vaccinated or non-vaccinated people.

Additional notes on differentiated policies:
- If our data reports a single policy for everyone (E), where there was previously a differentiated policy, this may represent either the end of policy differentiation, or it could represent a targeted lockdown in a single place applying to everyone (we always record the single *strictest* policy for a jurisdiction), while the rest of the country maintains policy differentiation by vaccination status at a lower level of stringency.
- There does not need to be a vaccine “pass” in place for us to record a differentiated policy. Some jurisdictions have vaccine passes of different kinds (eg. paper, digital certificate, sign-in app) but some countries do not actively enforce their vaccine requirements. We simply record the policy settings for vaccinated/non-vaccinated people, regardless of enforcement.
- If countries distinguish between one, two, three or more vaccination doses and there are different freedoms granted based on the number of doses, we report the freedoms given for the highest number of doses under our `vaccinated` policy.
- We accept each jurisdictions’ decision on which vaccine brand, or number of doses makes vaccinated people eligible for less restrictive policies.
- If vaccinated people are also required to present a negative PCR test in order to access less stringent restrictions, we still report this as a closure (2 or 3) to vaccinated people, to reflect the stringency of this measure. We record policies in both the non-vaccinated and vaccinated columns here, to reflect the existence of two different policies for each group.

Table 4. Examples of differentiated policies:
| ID | Name | Non-vaccinated | Explanation | Vaccinated | Explanation |
| --- | --- | --- | --- | --- | --- |
| C3 | `C3_Public events` | 2 | Vaccines required to enter large concerts and sporting events. No exceptions | 0 | Vaccinated people can attend with no restrictions |
| C8 | `C8_International travel controls` | 4 | Non-vaccinated people from all countries cannot enter the country | 3 | Vaccinated people can enter, but some bans on entry to all people remain from specific countries |
| H6 | `H6_Facial coverings` | 4 | Non-vaccinated people must wear masks at all times | 0 | Vaccinated people do not have to wear masks |
| C1 | `C1_School closing` | 2 | Students can enter schools with a COVID-19 pass showing negative PCR test or evidence of prior infection | 0 | Students can enter schools with a COVID-19 pass showing vaccination status. No significant operational changes besides this |
| C2 | `C2_Workplace closures` | 2 | Patrons must present a negative test OR a vaccine to enter nightclubs | 0 | Vaccinated people can enter nightclubs |
| C5 | `C5_Public transport` | 2 | Non-vaccinated need a COVID-19 pass with negative PCR test to travel on buses | 1 | Vaccinated people can get on buses without testing requirements, subject to reduced capacity |


## Flag variables for geographic targeting or economic support
--[return to top](#)-- <br/>
--[return to start of codebook](#codebook-and-interpretation-guidance)--

Commonly a government will enact a policy that only applies to a specific area within the jurisdiction, such as a state or a city – a “geographically targeted” policy.

Government coronavirus policies often vary by region within countries. We code the most stringent government policy that is in place in a country/territory, as represented by the highest ordinal value. Sometimes the most stringent policy in a country/territory will only apply to a fraction of the population. If the most stringent policy is only present in a targeted geographic area or sector (e.g. only some states has implemented policies at a high level), we use a binary flag variable to denote this limited scope. Ten of the indicators (C1-C7, H1, H6 and H8) have a flag variable to record whether they are "targeted" to a specific geographical region (flag=0) or whether they are a "general" policy that is applied across the whole country/territory (flag=1).

As explained in our index methodology documentation below, an indicator with flag=0 is weighted lower than flag=1 when calculating overall index values.

Two other indicators have flags that use similar logic. The E1 variable has a flag to describe whether income support is for just formal sector workers (flag=0) or whether it includes informal workers as well (flag=1). H7 has a flag to describe whether vaccine policy is funded at cost to the individual (flag=0) or by government (flag=1).

## Common issues
--[return to top](#)-- <br/>
--[return to start of codebook](#codebook-and-interpretation-guidance)--

### Delineating between policy levels 0 and 1
In many of our indicators, the line between level 0 and level 1 is blurry – it is often the difference between “no policy” and a “recommendation” to avoid some activity. This can create issues when policies are reduced over time and any residual precautions could reasonably be interpreted as either a 0 or a 1. In practice, this means care should be taken in assuming a strict difference between 0 and 1 policies, as it often comes down to the judgement of our data collectors.

Our general rule is that 0 reflects a state that is comparable to pre-covid times, whereas a 1 would reflect significant differences from pre-covid operational norms. The table below provides examples of residual precautions that would indicate a 0 or a 1.

Table 5. Comparison of policies that would be rated a 0 or a 1:
| **0 – Equivalent/comparable to pre-Covid times** | **1 – Significant differences to pre-Covid times, significant behavioural and/or operational differences**|
| --- | --- |
No social distancing <br /> Full capacity <br /> Regular opening hours <br /> Any recommended change to operations (such as use of facial coverings) that is not a recommendation to close | Regular Lateral Flow Testing <br /> Social distancing <br />  Altered operating times <br /> Reduced capacity <br /> Use of close contact bubbles <br /> Significant cleaning and ventilation <br /> Requirement to check in with track and trace  |


### Identifying groups for vaccine eligibility
The four OxCGRT vaccine indicators (V1-V4) report vaccine policies as they apply to 52 different groups of the population. These groups are not mutually exclusive; instead they try to reflect the different ways that governments prioritised groups for vaccine access. Even with 52 groups, the codebook does not comprehensively cover every possible policy. The following points provide guidance in interpreting the groups:

- Frontline workers – We generally record policies against specific groups (eg. `Police`) and **only** use the category `Frontline/essential workers (when subcategories not specified)` if there has been a vague/ambiguous reference to frontline or essential workers without specifying discrete groups.
- Clinically vulnerable and At risk – `Clinically vulnerable/chronic illness/significant underlying health condition (excluding elderly and disabled)` represents people who live with illnesses and conditions which place them on the prioritisation list. These may include those who have had an organ transplant, people undergoing chemotherapy, or have a severe lung condition. The age-related `At risk` categories are used for groups that are not extremely vulnerable, but still have comorbidities and underlying health conditions that make them eligible for vaccination sooner.
- Missing categories - If there is a missing category (that is, a country specifies a priority group that does not obviously fit into our list of groups), our data collectors have two options:
  - *Option A*: Best Fit - Use the ‘Best fit’ table below to decide which category to use as a proxy for this to ensure standardisation. We will also record this in detail in the notes.
  - *Option B*: Do not record - If the group is not on the list in the portal, and there is not a suitable best fit, we exclude it from V1/V2  but ensure it is mentioned in the free text notes.

Table 5. Vaccine category “best fit” table:
| OxCGRT Category  | Examples of country-designated categories that have resulted in this box being ticked  |
| --- | --- |
| `Police/ first responders` | Occupations important to functioning of society (IRL) <br/> Groups of persons who are of critical importance to the functioning of Singapore (SGP) <br/> Ambulance and paramedic staff (AUS) <br/> Emergency health  staff (FIN) <br/> Firefighters (FRA) <br/> Fire (AUS)
| `Disabled people`  | People with a learning or neurological disability (GBR) <br/> People with Down’s Syndrome (PRT) <br/>  People in communal facilities with an increased risk of infection and outbreaks (for example homes for the handicapped) (CHE)
| `Border staff`  | Key workers in essential jobs who cannot avoid high risk of exposure (IRL) <br/> Maritime and aviation (SGP)
| `Frontline retail workers`   | Restaurant workers
| `Frontline workers (when not otherwise specified)`  | Other people aged 65-69 and key workers essential to the vaccine programme’ (IRL)  <br/> Workers identified as performing a critical function in society [unspecified] (DNK) <br/> Operators essential essential for the country's economic activities (FRA) <br/> Essential professions In this phase, people with essential social and/or economic profession are vaccinated (BEL)
| `Tertiary education students`   | University, college, or technical trade schools
| `Educators`  | University, college, or technical trade schools  <br/> Teachers in any level of school <br/> Instructors/professors in colleges and universities
| `Other high contact professions`   | Disability care staff (AUS), Olympic/professional/international athletes (BRB)
| `Ethnic minorities`  | Aboriginal and Torres Strait Islander people > 55 (AUS) <br/> Indigenous populations (CAN)
| `Factory staff`   | Meat processing staff (AUS)
| `Crowded/communal living conditions (dormitories for migrant workers, temporary accommodations)`  | People in communal facilities with an increased risk of infection and outbreaks (with residents of mixed ages)  (SGP) <br/> People living or working in crowded settings (IRL)  <br/> Prison populations (ISR)  <br/> People who live in socially vulnerable situations, such as the homeless or the undocumented. (SWE) <br/>Homeless (KOR)  <br/> Vulnerable and precarious people (homeless…), living in communities (prisons, psychiatric establishments, homes) (FRA)  <br/> People in communal facilities with an increased risk of infection and outbreaks (with residents of mixed ages) (CHE)


### Locating vaccine-related mandates and requirements in OxCGRT data

In our data we report both vaccine mandates (`V4`) as well as differentiated restrictions that apply to unvaccinated people (`C1`, `C2`, `C3`, `C4`, `C5`, `C6`, `C7`, `C8`, H6 and `H8`). It can sometimes be difficult to know where to find the right policy reflected in the data.

As a general rule: mandates apply to staff/professions, and differentiated restrictions apply to the general public.

For instance, if there is a requirement for teachers, shopkeepers, event staff, bus drivers, or care home nurses to be vaccinated in order to work, this would all be reported in `V4`. However, a requirement for students (`C1`), customers (`C2`), public event attendees (`C3`), public transport passengers (`C5`) or care home visitors (`H8`) to be vaccinated would be reported as a differentiated policy.

## Detailed interpretation guidance for each indicator
--[return to top](#)-- <br/>
--[return to start of codebook](#codebook-and-interpretation-guidance)--

### C1 - School Closures
- `C1` reports closures of both schools and universities. It does not report closures of childcare, nurseries, language courses, and driving schools, which are instead recorded as workplaces under `C2`.
- If in-person teaching is suspended and all  instruction is online, this is reported as closed (physically closed). Some governments use different wording (eg. soft-closing, recommend without restricting civil liberty), but if the situation is that schools are closed, or policies make it impossible for them to open, then we report a full closure even if schools are theoretically allowed to open.
- If schools are closed, and this same closure policy then rolls into school holidays, we keep the code the same, for example ‘all levels of education remain closed’. This coding would only go down only once students actually return, when schools reopen.
- If only children of essential workers are allowed in schools, this is reported as a closure for the general public.
- Some schools only open for exams, but not for classes. In this case, if schools are open for a one-off exam, for example one that is an hour long, or on one day only, this would not change the coding. If exams are running for multiple sessions, on multiple days, or even over multiple weeks, this is a similar situation to classes being open for some groups during that time (`C1`=2).
- If teachers are back in school to prepare for the new school year, but no students are allowed back, this would not count as open.
- If individual school districts have the authority to decide closures/openings, we generally record closures conservatively with a ’targeted’ flag, as recording ‘general’ policies would require a high level of confidence that every single schools in a jurisdiction are closed.
- Summer school counts as school. If schools have been closed (`C1`=3) but then some summer school is allowed, the value would change (likely `C1`=3 and `C1_Flag`=0 if some school districts remain totally closed, or `C1`=2 and `C1_Flag`=1 if summer school has a ‘general’ country/territory wide scope). Summer school or other vacation-based programming includes substantial school-run educational programming such as entrance examination classes, remedial classes, or summer term courses, but does not include more peripheral activities such as recreational summer camps.
- If a narrowly defined list of university courses which rely on essential in-person teaching, for example medical programs, are permitted to operate as an exemption, but all other in-person university teaching is cancelled, we treat this as a closure of universities.

###  C2 - Workplace closing

- When workplaces are meant to be closed but many people are still going to work regardless, we report the official government policy.
- If workplaces can reopen under sanitation and social distancing requirements – e.g. up to 30% of capacity or operating at reduced capacity, and/or use only outdoor seats – this is a `C2`=1 (recommend closing) to reflect significant operational differences compared to pre-covid times.
- Voluntary closures are not the same thing as closures mandated by government policy. If a workplace voluntarily closes their business or makes their staff work from home – even if that employer is the government closing administrative offices – this does not count under `C2`.  
- Businesses usually considered to be essential (and therefore outside the scope of `C2`) are: Healthcare, groceries, take-out food, hardware stores, plumbers/electricians, legal services, education preparations (teachers planning courses), limited business operations support (tiny staff capacity to ensure remote working can continue).
- Some businesses that are not essential: In-person retail, personal grooming (salons, spas, barbers etc), dine-in restaurants, movie theaters, entertainment/theme parks, nightclubs.
- If a government publishes a list of essential business that is very long, particularly expansive or contains unusual inclusions, our data collectors use their best judgement on a case-by-case basis, and this is recorded in detail in the note. We try to be mindful of edge cases where governments may declare every business ‘essential’ or use other terminology that is inconsistent with general practice.
- Airports and schools would not count as "some businesses" in the `C2` indicator here. If these workplaces have been closed by government policy decisions, this will be captured in other indicators (eg `C1_School closing` and `C8_International travel controls`).

### C3 - Cancel Public Events

- When private gatherings of only 10 or less are permitted (i.e. `C4`=4), this restriction would prevent public events from taking place, so we also report `C3`=2 (public events are required to be cancelled), unless there is a specific policy in place permitting public gatherings to go ahead.
- When all public gatherings are cancelled, but people can still go to church, we still record this as `C3`=2 (all gatherings cancelled), but make a note about the exception for religious gatherings. We do the same thing (maintain `C3`=2) also in cases where there is a one-off exception for a large event with stringent social distancing.
- If a venue for public events is able to open (eg. a concert hall), but with a specified percentage of original capacity, this is reported at the `C3`=1 level to reflect an operational departure from pre-covid norms.


### C4- Restrictions on gatherings

- When there are variations in numbers based on the type gathering (e.g. gatherings are capped at 6 people with the exceptions of funerals/weddings where it is 30 people) we report the stricter (in this case 6 people, `C4`=4) policy while including the less strict outdoor/wedding/funeral gathering limit in the notes.
- A ban on all gatherings outside of the house would be reported as `C4`=4, even if no specific gathering size is mentioned.
- If there are restrictions in gatherings based on a proportion of capacity of indoor spaces (e.g. up to 30% of capacity), but there is no mention of any clear gathering size, we record `C4`=0.

### C5- Public Transportation

- If only essential workers (or some other specific category) are allowed to use public transport, and it is otherwise closed to the general public, we still report this as `C5`=2.
- The sorts of significant volume reductions that would constitute `C5`=1 include: closing major routes, reducing the number of services, or discouraging use by members of the public
- In rare instances, governments added a significant number of buses into circulation in order to dilute capacity and enable greater social distancing. We reported this as `C5`=1 as it is a significant operational change.
- If closures of transport seem to be primarily the result of decreasing demand rather than deliberate government policy to prevent spreading, this is judged by our data collectors on a case-by-case basis, but if there are substantial changes to schedules we may report `C5`=1.

### C6- Stay at Home Order

- If the government policy is that people should stay home, but people don’t seem to be actually doing this in reality, we still report the official government policy.
- We record curfews (eg. where people are not allowed out between 6pm and 6am, or people only allowed for 1 hour a day) as a `C6`=2. If people cannot leave the house for multiple days at a time (eg. can only go out on a specific day of the week), this would be reported as a `C6`=3 for total confinement.
  - If during non-curfew hours people can go out of the house for non-essential trips, this is still reported as `C6`=2 to fully represent the limitations during curfew hours.
- If the only policy relates to clinically vulnerable groups being required to shield at home, but not the broader population, we record this as `C6`=1 (recommended) with a ‘general’ flag of `C6_Flag`=1 (if nationwide).

### C7- Restrictions on Internal Movement

- If people are allowed to physically travel within a country, but only on the condition of producing a negative test result or undergoing mandatory quarantine, then this is still reported as `C7`=2 (restrictions in place).
- If a stay-at-home order (`C6`) is accompanied by an explicit geographic restriction (eg. stay-at-home and only allowed within 5km radius for exercise or groceries) then this would also be reported under `C7`.
- **For subnational (state-level) data:** `C7` is used to record state-level border closures where a state restricts entry from other states from another state, as well as recording restrictions on movement within the state.
- Non-intrusive checks at the state border (e.g. asking where you will be quarantining but not following up extensively) and voluntary quarantine measures will generally be reported as `C7`=1.
- If there are restrictions for the circulation of private cars based on certain criteria (e.g. last digit of licence plate) to reduce the number of vehicles on the streets, we also report it as restriction of internal movement (`C7`=2).


### C8-  International Travel Controls

#### General interpretation guidance

- This indicator is to record policies relating to incoming foreign travellers to the jurisdiction being coded. We do not report restrictions on outbound travel, and we do not count citizen repatriation as a case of open borders (if all other incoming travel is restricted).
- If visitors can get a PCR test to avoid quarantine we record this as `C8`=1 (screening) for everyone. If quarantine is mandatory, and visitors cannot do a test to avoid this, we record `C8`=2.
- This indicator does not have a binary flag variable to reflect geographic variation in policies. Therefore, we generally record the level of policy that applies everywhere across the jurisdiction – the highest common value of policy nationwide or statewide.
- If visitors are meant to self quarantine/isolate after travelling to certain areas, but there are no enforcement or tracking measures in place, we record the official policy accordingly.
- If country/territory borders are completely closed, but it is because of a civil war, or other non-covid related reasons, this will still be reported as a `C8`=4.
- If land borders are closed, but international flights are permitted, then still we record `C8` based on whether there is a total ban on entry from some countries. In most cases, residents of the country with the land border could still get on a flight. But if the policy would prevent *most* people from a certain country from entering, then this may be recorded as `C8`=3 (ban on entry from some countries).
- **Constituent countries**: some constituent countries/regions/territories have a `C8` policy that is set by another. For example, Puerto Rico has international travel restrictions set by both the Puerto Rican government and the US federal government. We code the most stringent `C8` policy applying to PRI even if this is from another government (in this case, the USA). Other jurisdictions in our national dataset where this may occur are: Aruba, American Samoa, Bermuda, Greenland, Guam, New Caledonia, Puerto Rico, French Polynesia, and the United States Virgin Islands.

#### Differentiated policy guidance
- If vaccinated people do not have to quarantine, we record this as `C8V`=1 for them (as they are still being ‘screened’ by vaccination status), and if non-vaccinated can ‘test out’ of quarantine, we also record this as a `C8NV`=1. If non-vaccinated people must present evidence of a test to arrive in a country, this is therefore a `C8NV`=1. If they must present a test and also quarantine, this is reported as a `C8NV`=2.
- If the country/region prohibits non-vaccinated people from any country from entering its borders, we record this as a `C8NV`=4 (total closure) for non-vaccinated people.


### E1- Income Support

-  This indicator does not have a binary flag variable to reflect geographic variation in policies. Therefore, we generally record the level of policy that applies everywhere across the jurisdiction - the highest common value of policy nationwide or statewide. We do not report any policies of a higher value that are only available in limited areas.  *For example*  if income support is only being provided by some cities, and not across the whole country/territory or state, we would not report it. It is only recorded when the support recorded applies nationally or statewide.
- Formal sector workers are people who are employed with contracts, and pay taxes. Informal workers may be roadside vendors, work on markets, and do not have a formal contract for their work.
- If benefits in kind are being given (e.g. dry rations, or school meals), this is recorded as a `E1`=0.
- We do not include payments made to people undergoing mandatory quarantine after international travel. It is only recorded if ALL the employees in the formal sector who cannot work are being covered.
- If a government extends or increases existing unemployment benefits (including broadening eligibility to more people) we would report this as a non-zero benefit. If a government simply makes it faster and easier to claim unemployment benefits (without changing the amount or the eligibility criteria) then this would still be reported as `E1`=0.

### E2- Debt/contract relief for households

- Debt relief for business and corporations is not reported here; `E2` only records debt relief to private households.
- This indicator does not have a binary flag variable to reflect geographic variation in policies. Therefore, we generally record the level of policy that applies everywhere across the jurisdiction – the highest common value of policy nationwide or statewide.

### E3- Fiscal Measures

- **We stopped actively updating data for E3 in August 2021.** In general, the OxCGRT’s four monetary indicators (E3, E4, H4, and H5) are incomplete, have not been reviewed for quality, and should be used with caution.
- If the specific monetary value is undisclosed or unclear, we make a note recording the announcement, but don’t record the monetary value.
- Sometimes governments make ambiguous funding announcements (where it is not clear what the money is for), or they announce multiple programmes under a single number (eg, fiscal stimulus, plus hospital funding, plus vaccine investment, plus support for other countries, in one number). In these cases, the spending is recorded in this `E3` category, and not in the other monetary categories (`E4`, `H4`, and `H5`).
- If the state is providing support to specific groups, for example funding children’s school meals, handing out staple goods, or providing grants to single parents, this is recorded under `E3`.

### E4- Providing Support to other countries

- **We stopped actively updating data for E4 in August 2021.** In general, the OxCGRT’s four monetary indicators (E3, E4, H4, and H5) are incomplete, have not been reviewed for quality, and should be used with caution.
- We only record money here that a government DONATES to another government, not money that is received. We do not record in-kind support, for example donations of medical equipment, as a monetary equivalent.
- We record donations to international organisations, such as  WHO, WFP, as long as the country being recorded is the donor and it is to a specific relief fund specifically related to COVID.

### H1- Public Information Campaigns

- Evidence for the beginning of a coordinated campaign (H1=2) includes a website being launched, an official announcement or press release of a campaign, or government and health department social media announcements of a campaign.
- The end of campaigns are rarely announced officially. If evidence is still present of a dormant, or rarely updated COVID-19 information campaign, we may report this as `H1`=1 (public officials urge caution, but no coordinated campaign). If there is no guidance remaining at all, or a campaign over 6 months out of date we may report `H1`=0.

### H2 - Testing Policy

- The main purpose for `H2` is to record PCR testing. For the first year of the pandemic, most countries struggled with limited supplies of PCR tests and a lack of available substitutes. We do not usually report the availability of antibody tests (known as “lateral flow tests” or “rapid antigen tests”) in `H2`. The only rare case where we would report other types of tests was where, say, widespread antibody testing was used to identify individuals who automatically received follow-up PCR tests.
- While government policies are often clear, availability of tests affected implementation, particularly early in the pandemic. This indicator therefore requires judgement from our data collectors – we will sometimes record a lower code than the official announced policy if sources suggest there is not capacity on the ground to meet this testing policy.
- This indicator does not have a binary flag variable to reflect geographic variation in policies. Therefore, we report the level of testing availability that applies everywhere across the jurisdiction – the highest common value of policy nationwide or statewide. We do not report any policies of a higher value that are only available in limited areas.
- In some countries, most testing is coordinated by private companies, with little oversight or coordination from the government (eg. in the USA, particularly in the first months of the pandemic). In this cases, we report as follows:
    - When the state/nation is clearly putting resources towards making testing available, we record this as a `H2`=1 (only symptomatic and eligible)
    - If the government implements a statewide/nationwide plan to fund and procure local testing facilities, with the intention of widespread public accessibility, we report `H2`=2 (all symptomatic eligible)

### H3 - Contact tracing

- For this indicator we are only interested in manual contact tracing that is intended to reach all people known to a newly-diagnosed case. Digital contact tracing apps do not achieve this goal, and the presence of a contact tracing app in a country/territory would not be reported under `H3`. The difference between the levels here is considering whether this top-notch manual contact tracing is done for just some COVID-19 cases (`H3`=1) or for all cases (`H3`=2).
- Countries will often announce when they are successfully implementing universal contact tracing (which is possible for tens of cases per day). But countries will rarely announce when their contact tracing resources have been overwhelmed by cases. We will sometimes downgrade a jurisdiction `H3`=2 to a `H3`=1 if there are high daily case numbers, and credible reports of newly-diagnosed cases whose recent contacts were not traced.

### H4 - Emergency investment in healthcare

- **We stopped actively updating data for H4 in August 2021.** In general, the OxCGRT’s four monetary indicators (E3, E4, H4, and H5) are incomplete, have not been reviewed for quality, and should be used with caution.


### H5 - Investment in vaccines

- **We stopped actively updating data for E5 in August 2021.** In general, the OxCGRT’s four monetary indicators (E3, E4, H4, and H5) are incomplete, have not been reviewed for quality, and should be used with caution.
- This indicator records spending on vaccines procurement as well as vaccine development spending – therefore covering all vaccine related spending

### H6 - Facial coverings
No additional guidance.

### H7 Vaccination policy

- Significant bottlenecks of vaccine supply in 2020 and 2021 meant that we only report vaccine availability under `H7` once three conditions are met: (1) there is an explicit policy to vaccinate a population group, (2) there are enough vaccine doses on order to vaccinate at least approx. 20% of the target population, and (3) there is evidence that vaccines are actually being administered.
- We also only report vaccination policies where a vaccine is delivered across the entire jurisdiction – if there is geographic variation we report the lowest common value.
- “Broad groups” may include a large group of adults (eg. all those aged over 40), or other broadly defined population groups (eg. students)
- A policy recorded as “no cost to individual” may also include a very small nominal cost to the individual, such as the small pharmaceutical co-payments (eg. $5) in some public health systems.
- We do not report any policy under `H7` if countries are using vaccines that have not passed phase 3 clinical trials.
- When there is a vaccine shortage and there are not enough doses to vaccinate the target population, we reduce the value to reflect this reduction.
- We report `H7`=5 (Universal Coverage) when the vaccine is available to all adults (and there is evidence that it is being delivered on the ground).
- After introducing `H7` in late-2020, we subsequently developed indicators `V1` and `V2` which report similar information with much more granularity.

### H8 Protection of elderly people

- This indicator focuses mainly on institutions where elderly people live, but also has scope to record equivalent restrictions in settings where elderly people are cared for in the home or community, especially in cultures where long-term care facilities are not common.
- Policies for other vulnerable groups that live in institutionalised settings (eg. prison population or people in long-term disability care) are not recorded here.
 - If masks are required to be worn as part of a specific hygiene measure in elderly care facilities, this may be reported as `H8`=1, but would not be reported if masks are generally required in public places.

### V1- Vaccine prioritisation

- We report groups under `V1` from when the official priority groups plan is published, not from when potential categories are discussed informally. This is a departure from our usual rule of reporting a policy only once it is implemented, not when it is announced. This is because `V2` reports the implementation of vaccine availability, `V1` merely records the announced prioritisation order at any point in time.
- When there is no longer prioritisation between groups, and the policy is to vaccinate all adults, this is reported as a universal prioritisation list.

### V2- Vaccine eligibility/availability

- We report categories in `V2` when there is evidence from anywhere within the jurisdiction that this group is being vaccinated. This logic **differs** from the logic used to code `H7`, where we only report a new ordinal level when this is the situation across the whole jurisdiction.
- If access to vaccines is universal, we report all general ages above 16 in `V2`. If the vaccine is approved for additional groups, such as `0-4 yrs infants` this is added when the policy comes into effect.
- If there is a policy to restrict vaccine availability to certain groups, but there is evidence that vaccination centres have excess capacity and are offering vaccinations to people who walk in, and there is evidence of this being a widespread phenomenon in multiple locations in a consistent manner, we report the vaccines as available to the walk-in groups.

### V3- Vaccine financial support

- If people receive the vaccine from their private healthcare insurance-run organisations (e.g. Israel’s HMO members) we still report this depending on whether the government is funding the organisations to deliver the vaccine, or if the government requires copayment from the insurer (therefore not fully government funded).

### V4- Mandatory vaccination

- `V4` records a policy requirement to be vaccinated against COVID-19 in order to work in a specific occupation, or for a specific group to be vaccinated. This is a mandatory vaccination as part of occupation or citizenship, and here we do not record voluntary vaccination that people may take in order to access greater freedoms (ie. if people must be vaccinated in order to travel).
- We do not report enforcement, only the presence of the policy.
- We report a vaccine mandate when there is evidence from anywhere within a country/region/territory that this group is subject to mandatory vaccination rules. If this is happening in a subnational region, but not nationally, we still report it under `V4`.
- If there is a vaccine mandate in place for workers of certain occupations, and non-vaccinated people in this occupation have the option of testing regularly to opt out of vaccination, we still record this.

## Codebook changelog
--[return to top](#)-- <br/>
--[return to start of codebook](#codebook-and-interpretation-guidance)--

Over the course of the covid-19 pandemic the OxCGRT constantly adapted by adding new indicators and refining the definitions of existing indicators. The summary below covers the major developments in the OxCGRT data structure.

March 2020: OxCGRT begins data collection with 7 indicators named S1-S7. These were broadly comparable to C1, C2, C3, C5, C7, C8 and H1, and are the indicators on which the stringency index is based.
April 2020: major expansion of indicators to introduce C4, C6, E1-E4, H2-H5.
October 2020: introduction of H6 indicator (but at this point, it does not have a flag to indicate geographic targeting).
November 2020
The meaning of C1=1 is updated. It previously meant “recommend closing schools” but now includes operational changes that are significantly different to pre-covid norms. Existing coding is retrospectively edited to reflect this change.
A flag for geographic targeting is added to H6.
December 2020: introduction of H7 indicator
January 2021:
The word “private” is removed from the definition of C4 which originally referred to private gatherings.
The flag variable for E1 originally differentiated between two levels of income support: “formal sector workers only” or “informal workers too”. This is changed to “formal sector workers only or informal sector workers only” or “all workers”.
March 2021:
Introduction of H8 indicator.
The definition of H7 is revised to specify “non-elderly clinically vulnerable groups” to be distinct from “elderly groups”
May 2021: the meaning of C2 is updated, similar to C1, to reflect that C2=1 can include major operational changes, not just a “recommended closure”.
June 2021: introduction of V1-3 indicators.
March 2022: introduction of V4 indicator.
July 2022: introduction of differentiated coding structure for C1-C8, H6 and H8.


# Calculation of policy indices
--[return to top](#)--

## Policy indices

The OxCGRT calculates several indices to give an overall impression of government activity: the Government Response Index, the Containment and Health Index, the the Stringency Index, the Economic Support Index, and an old (legacy) version of the Stringency Index.
The different indices are comprised as follows:

| Index name | _k_ | C1 | C2 | C3 | C4 | C5 | C6 | C7 | C8 | E1 | E2 | E3 | E4 | H1 | H2 | H3 | H4 | H5 | H6 | H7 | H8 | M1 | V1 | V2 | V3 | V4 |
| --- | ---: | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |--- |--- |--- | --- |--- |--- |--- |
| Government response index | 16 | `x` | `x` | `x` | `x` | `x` | `x` | `x` | `x` | `x` | `x` | | | `x` | `x` | `x` | | | `x` | `x` | `x` | | | | | | | |
| Containment and health index | 14 | `x` | `x` | `x` | `x` | `x` | `x` | `x` | `x` | | | | | `x` | `x` | `x` | | |`x` | `x` | `x` | | | | | | | |
| Stringency index | 9 | `x` | `x` | `x` | `x` | `x` | `x` | `x` | `x` | | | | | `x` | | | | | | | | | | |
| Economic support index | 2 | | | | | | | | | `x` | `x` | | | | | | | | | | | | | |
| Legacy stringency index | 7 | `x` | `x` | `?` | `?` | `x` | `?` | `?` | `x` | | | | | `x` | | | | | | | | | | |

We publish four versions of each index with different treatment of vaccine-differentiated policies. These are denoted as follows:
-  `_Nonvax` – constructs the index using 'non-vaccinated' (NV) policies if present, or otherwise using 'everyone' (E) policies.
- `_Vax` – constructs the index using 'vaccinated' (V) policies if present, or otherwise using 'everyone' (E) policies.
- `_SimpleAverage` – takes the sum of "_Nonvax" and "_Vax" indices and divides them by 2.
- `_WeightedAverage` – takes an average of the "_Nonvax" and "_Vax" indices weighted by the proportion of the population that is vaccinated.
In our ‘compact’ and ‘simplified’ CSVs we only present one version of each index (so 4 indices in total), using an `_Average` version that is just the weighted average for most jurisdictions (but for which we substitute the simple average for jurisdictions where it is not possible to calculate the weighted average.
## Calculating sub-index scores for each indicator
All of the indices use ordinal indicators where policies are ranked on a simple numerical scale. In order to aggregate these indicators into an index, we first must calculate a sub-index score that normalises each of the indicators.

Some indicators – C1-C7, E1, H1, H6, H7, and H8 – have an additional binary flag variable that can be either 0 or 1. The codebook above has details about each indicator and what the different values represent.

Because different indicators (j) have different maximum values (N<sub>j</sub>) in their ordinal scales, and only some have flag variables, each sub-index score must be calculated separately. The different indicators that contribute to the indices are:

| Indicator | Max value (N<sub>j</sub>) | Flag? (F<sub>j</sub>) |
| --- | --- | --- |
| C1 | 3 (0, 1, 2, 3) | Yes=1 |
| C2 | 3 (0, 1, 2, 3) | Yes=1 |
| C3 | 2 (0, 1, 2) | Yes=1 |
| C4 | 4 (0, 1, 2, 3, 4) | Yes=1 |
| C5 | 2 (0, 1, 2) | Yes=1 |
| C6 | 3 (0, 1, 2, 3) | Yes=1 |
| C7 | 2 (0, 1, 2) | Yes=1 |
| C8 | 4 (0, 1, 2, 3, 4) | No=0 |
| E1 | 2 (0, 1, 2) | Yes=1 |
| E2 | 2 (0, 1, 2) | No=0 |
| H1 | 2 (0, 1, 2) | Yes=1 |
| H2 | 3 (0, 1, 2, 3) | No=0 |
| H3 | 2 (0, 1, 2) | No=0 |
| H6 | 4 (0, 1, 2, 3, 4) | Yes=1 |
| H7 | 5 (0, 1, 2, 3, 4, 5) | Yes=1 |
| H8 | 3 (0, 1, 2, 3) | Yes=1 |

Each sub-index score (`I`) for any given indicator (`j`) on any given day (`t`), is calculated by the function described in equation 1 based on the following parameters:
- the maximum value of the indicator (N<sub>j</sub>)
- whether that indicator has a flag (F<sub>j</sub>=1 if the indicator has a flag variable, or 0 if the indicator does not have a flag variable)
- the recorded policy value on the ordinal scale (v<sub>j,t</sub>)
- the recorded binary flag for that indicator, if that indicator has a flag (f<sub>j,t</sub>)

[note: see the working paper on [our website](https://www.bsg.ox.ac.uk/covidtracker) for equation 1]

This normalises the different ordinal scales to produce a sub-index score between 0 and 100 where each full point on the ordinal scale is equally spaced. For indicators that have a flag variable, if this flag is recorded as 0 (i.e. if the policy is geographically targeted or for E1 if the support only applies to informal sector workers) then this is treated as a half-step between ordinal values.

Note that the database only contains flag values if the indicator has a non-zero value. If a government has no policy for a given indicator (i.e. the indicator equals zero) then the corresponding flag is blank/null in the database. For the purposes of calculating the index, this is equivalent to a sub-index score of zero. In other words, I<sub>j,t</sub>=0 if v<sub>j,t</sub>=0.


## Index calculations

In this section, we explore how indices are calculated, taking into account differentiated policies that treat vaccinated and non-vaccinated people differently.

### Non-vaccinated and Vaccinated Indices
For a given jurisdiction, our non-vaccinated and vaccinated indices are simple averages of the individual component indicators for each group. This is described in equation 2 below where k is the number of component indicators in an index and Ij is the sub-index score for an individual indicator. If a component indicator is one of the ten for which we record differentiated policy, then we will use either:
- the `NV` or `V` version of the policy (in the non-vaccinated or vaccinated index respectively) where there is a differentiated policy
- the `E` (everyone) version of the policy for both the non-vaccinated and vaccinated index where there is no differentiated policy

[note: see the working paper on [our website](https://www.bsg.ox.ac.uk/covidtracker) for equation 2]

This results in two versions of each index that report the overall policy settings that apply to, respectively, non-vaccinated people and vaccinated people.


### Simple Average Indices
For a given jurisdiction, our simple average indices are the sum of the vaccinated and the non-vaccinated indices divided by two. This is described in equation 3 below where index<sub>v</sub> is the index for the vaccinated, and index<sub>nv</sub> is the index for the non-vaccinated.

[note: see the working paper on [our website](https://www.bsg.ox.ac.uk/covidtracker) for equation 3]

### Weighted Average Indices
This weights the index value using the non-vaccinated/vaccinated values based on the proportion of the population that are vaccinated. The values used for these calculations are published in our CSVs in a column labelled `Population vaccinated`.

The process is described in equation 4 below. As with the simple average above, we start with index<sub>v</sub> and index<sub>nv</sub> and then weight these by w<sub>v</sub> (the proportion of the population that is vaccinated) and w<sub>nv</sub> (the proportion of the population that is not vaccinated).
Note that for some jurisdictions (such as Australia cities and Indian states) we do not have daily vaccination data. This means we cannot calculate and publish a weighted average, and so for these jurisdictions we only have the simple average index.

[note: see the working paper on [our website](https://www.bsg.ox.ac.uk/covidtracker) for equation 4]

### Legacy stringency index
We also report a legacy stringency index (from pre-April 2020) that approximates the logic of the very first version of the Stringency Index, which only had seven components under our original database structure with the old indicators S1-S7. We generally do not recommend using this legacy index, but it may be useful for continuity purposes for people who have been using our data since March 2020.

The legacy indicator only uses seven indicators, and it chooses a single indicator between C3 and C4, and between C6 and C7, selecting whichever of those pairs provides a higher sub-index score. This is because C3 and C4 aim to measure the information previously measured by S3, and similarly for C6, C7 and the old S6. This method, shown in equation 5, faithfully recreates the logic of the old stringency index.

[note: see the working paper on [our website](https://www.bsg.ox.ac.uk/covidtracker) for equation 5]

The individual sub-index scores for the legacy index are calculated through a slightly different formula to the one described in equation 5 above. This formula is described in equation 6 below (with a separate formula for C8, the only indicator in this index without a flagged variable).

[note: see the working paper on [our website](https://www.bsg.ox.ac.uk/covidtracker) for equation 6]

### Example index calculation
Here is an explicit example of the calculation for a given country on a single day where there is no policy differentiation based on vaccine status (all the policies apply to everyone):

| Indicator | v<sub>j,t</sub> | f<sub>j,t</sub> | | N<sub>j</sub> | F<sub>j</sub> | | Sub-index score (I<sub>j,t</sub>) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| C1 | 2 | 1 | | 3 | yes=1 | | 66.67 |
| C2 | 0 | null | | 3 | yes=1 | | 0.00 |
| C3 | 2 | 0 | | 2 | yes=1 | | 75.00 |
| C4 | 2 | 0 | | 4 | yes=1 | | 37.50  |
| C5 | 0 | null | | 2 | yes=1 | | 0.00   |
| C6 | 1 | 0 | | 3 | yes=1 | | 16.67  |
| C7 | 1 | 1 | | 2 | yes=1 | | 50.00  |
| C8 | 3 | N/A | | 4 | no=0  | | 75.00  |
| E1 | 2 | 0 | | 2 | yes=1 | | 75.00  |
| E2 | 2 | N/A | | 2 | no=0  | | 100.00 |
| H1 | 2 | 0 | | 2 | yes=1 | | 75.00  |
| H2 | 3 | N/A | | 3 | no=0  | | 100.00 |
| H3 | 2 | N/A | | 2 | no=0  | | 100.00 |
| H6 | 2 | 0 | | 4 | yes=1 | | 37.50  |
| H7 | 2 | 1 | | 5 | Yes=1 | | 40.00  |
| H8 | 2 | 1 | | 3 | Yes=1 | | 66.66  |

| Index                        |     |
|------------------------------|----|
| Government response index    | 57.18 |
| Containment and health index | 52.86 |
| Stringency index             | 43.98 |
| Economic support index       | 87.50 |
| Legacy stringency index      | 48.81 |
