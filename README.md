# Oxford Covid-19 Government Response Tracker (OxCGRT)

This repository contains the final version of the Oxford Covid-19 Government Response Tracker (OxCGRT).

The Oxford Covid-19 Government Response Tracker (OxCGRT) collected information on which pandemic response measures were enacted by governments, and when. During the pandemic this helped decision-makers and citizens understand governmental responses in a consistent way, aiding efforts to fight the pandemic. Now that covid-19 is no longer designated a public health emergency of international concern, the data can be used for research purposes and to prepare for future pandemics.

The OxCGRT systematically collected information on several different common policy responses governments took over 2020, 2021, and 2022, recorded these policies on a scale to reflect the extent of government action, and aggregates these scores into a suite of policy indices. We also collected differentiated policies data where different policies apply to people who were vaccinated and non-vaccinated.

The OxCGRT was a project from the [Blavatnik School of Government](www.bsg.ox.ac.uk) at Oxford University. More information on the OxCGRT is available on the school's website: https://www.bsg.ox.ac.uk/covidtracker


## Citing the OxCGRT

Our data is made available free to use for any purpose under a Creative Commons CC BY 4.0 license (see: [our license](LICENSE.txt), and a [summary of CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) at Creative Commons), this means you must give appropriate credit and link back to our original work. Here are two suggested ways to cite our work:

- _Recommended reference for academic publications_: Thomas Hale, Noam Angrist, Rafael Goldszmidt, Beatriz Kira, Anna Petherick, Toby Phillips, Samuel Webster, Emily Cameron-Blake, Laura Hallas, Saptarshi Majumdar, and Helen Tatlow. (2021). “A global panel database of pandemic policies (Oxford COVID-19 Government Response Tracker).” Nature Human Behaviour. https://doi.org/10.1038/s41562-021-01079-8
- _Short credit for media use (CC BY 4.0 License)_: Oxford COVID-19 Government Response Tracker, Blavatnik School of Government, University of Oxford.


## The dataset

The OxCGRT reports publicly available information on ***24 policy indicators*** and a miscellaneous notes field of government response organised into four groups:
- C - containment and closure policies
- E - economic policies
- H - health system policies
- V - vaccination policies

To help make sense of the data, we have produced ***four indices*** that aggregate the data into a single number. For more details about how the indices are comprised, see the section '*[Calculation of policy indices](documentation_and_codebook.md#calculation-of-policy-indices)*' in our documentation. Each of these indices reports a number between 0 to 100 that reflects the level of the government’s response along certain dimensions:
- overall government response index (all indicators)
- containment and health index (all C and H indicators)
- stringency index (all C indicators, plus H1 which records public information campaigns)
- economic support index (all E indicators)

These indices are a measure of how many of the relevant policy types a government has acted upon, and to what degree. The index cannot say whether a government's policy has been implemented effectively.

Because of the complexity of the dataset, it is published across 27 CSV files. Our [technical documentation](documentation_and_codebook.md) contains all the information users need to navigate and use the data. However, it is a long document. For those who want to dive in straight away, the files are labelled as follows:
- `OxCGRT_compact` is the file type we expect most people will use.
  - It contains 56 variables (including metadata) for 185 national jurisdictions and 210 subnational jurisdictions.
- `OxCGRT_simplfied` is the most basic version of the data.
  - It contains just a single variable for each indicator and a single version of each index.
  - In total, this data file contains 50 variables for 185 national jurisdictions and 170 subnational jurisdictions (state level only; no cities).
- Files denoted `fullwithnotes` provide maximum detail while still reporting the overall situation in each jurisdiction.
  - All jurisdictions in these files contain the TOTAL designation, meaning they report the overall total policy environment on residents.
  - These files are structured across 147 variables (including metadata).
- Files denoted `raw` provide the raw data that our team collected for subnational jurisdictions.
  - These jurisdictions are generally designated as WIDE or GOV, meaning they report the policies set by a certain level of government (residents may also be subject to policies from other levels of government).
  - These files are structured across 128 variables (including metadata).
- The `vaccines_full` files contain our raw data for the vaccine indicators V1-V4 over 226 variables (where each of the 4 indicators has a separate variable recording the policy for each of the 52 population categories).
- The four policy indices are also published in `timeseries` format, which uses the “average” version of each index from the `OxCGRT_compact` files.
- We have also separately published supplementary files for jurisdictions where we only partially completed the data collection exercise. This includes regions of Italy and second cities of Brazilian states (large non-capital cities). These are published separately, and not part of the main dataset in this repository.


## Technical documentation and guidance

The canonical version of our documentation exists as a technical appendix to our working paper, published on the Blavatnik School of Government website: https://www.bsg.ox.ac.uk/covidtracker

The main parts of our documentation is also duplicated here in this repository in the [documentation_and_codebook.md](documentation_and_codebook.md) file.


## Quick tips and troubleshooting

If you are troubleshooting or things look wrong in the data, there are some common things to check:
- make sure you are looking at the right **version of the indicator** for what you want.
  - `M` versions of indicators (eg. `C1M`) won’t always report the existence of a lockdown or closure if it doesn’t apply to vaccinated people.
  - `E`, `NV`, and `V` versions of indicators (eg. `C1E`, `C1N`, and `C1V`) are not continuous and will have gaps.
  - You need to choose the right one for your situation.
  - see the section '*[Differentiation of policies by vaccine status (vaccine passports)](documentation_and_codebook.md#differentiation-of-policies-by-vaccine-status-vaccine-passports)*' in our documentation for more information.
- make sure you are looking at the **correct jurisdiction type** for what you want.
  - you might know that the US state of Oklahoma was not recommending any business closures in August 2020, and yet we report `C2M`=1 (recommended business closure) for `US_OK` in our `OxCGRT_compact_subnational_v1.csv` file.
  - this is because our main files use the `STATE_TOTAL` jurisdiction type, which reports any policies that apply to residents, *including policies from the federal government*.
  - to find policies enacted by a certain level of government you need to use jurisdictions labelled `_WIDE` or `_GOV`, eg. `US_OK STATE_WIDE`. We only have these for some, not all, of our jurisdictions.
  - see the section '*[Subnational data and the jurisdiction variable](documentation_and_codebook.md#subnational-data-and-the-jurisdiction-variable)*' in our documentation for more information.
- make sure you have **checked the flag variables** if you see a policy that is much stricter than expected.
  - because we record the strictest policy in a jurisdiction, we will sometimes report a strict policy that only applies in a very small geographic area (eg. a lockdown in one city when the rest of the country is free). Our flag variables will usually tell you if a policy is *targeted* to a specific geography, or *general* across the whole jurisdiction.
  - see the section '*[Flag variables for geographic targeting or economic support](documentation_and_codebook.md#flag-variables-for-geographic-targeting-or-economic-support)*' in our documentation for more information.
- make sure you have checked the **notes** to understand the thinking from our data collector.
  - it is always useful to use the notes column to corroborate the value you see in an indicator – this should explain why a particular value was chosen. If there are no notes on the day you are looking at, scroll back in time until the most recent note.


## Our other repositories

The OxCGRT was a live project during the course of the pandemic, with data being updated and published in real time as it was recorded by our team. There are several repositories we used during the pandemic to publish and disseminate this data. It is possible to retrieve historical versions of the OxCGRT from these repositories (for instance, if you want to see what the OxCGRT dataset looked like on a certain date).

- https://github.com/OxCGRT/covid-policy-tracker
  - this was the live production repository, updated every hour. Because it was updated in real time the repository is incredibly large, holding the entire history of past edits and changes.
- https://github.com/OxCGRT/covid-policy-tracker-legacy
  - this repository contains up-to-date data published in our “legacy” format. While we made several changes to the codebook and data structure over the course of the pandemic, we always maintained a version that was easily comparable back to the very first dataset we published.
- https://github.com/OxCGRT/Brazil-covid-policy
  - this is where we published raw data for Brazilian subnational jurisdictions (ie. STATE_GOV and CITY_WIDE)
- https://github.com/OxCGRT/USA-covid-policy
  - this is where we published raw data for the states in the USA (ie. STATE_WIDE)
- https://github.com/OxCGRT/Australia-covid-policy
  - this is where we published raw data for Australian subnational jurisdictions (ie. STATE_GOV and CITY_WIDE)


## Version history

- June 2023 - the complete OxCGRT dataset is compiled and published as v1
- March 2020 - covid policy data is collected and published in real time at https://github.com/OxCGRT/covid-policy-tracker
