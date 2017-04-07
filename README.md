# Data Engineering Exercise
from your friends at Aerial Intelligence 

## Hello world!
At Aerial Intelligence, we use vast amounts of data to help the agricultural industry understand future crop potential. Part of this data consists of public satellite imagery. As you might imagine, handling large amounts of satellite data requires a lot of data infrastructure.  We've chosen the following problems to give you an idea of some of the data challenges we've tackled at Aerial Intelligence, and to give you an opportunity to showcase your coding ability and engineering experience. As an early member of the engineering team, you can expect to work on similar responsibilities like these and much more.

If you feel stuck at any point during this exercise, please don't hesitate to ask us any questions you might have! We'll try to respond as quickly as we can. We want to help you do your best.

We recommend reading through the entire README before starting.

## Part 1: Downloading public satellite data

This is a software development exercise.

One of our public satellite data sources is the MODIS (**MOD**erate Resolution **I**maging **S**pectroradiometer) family of satellites. Data for MODIS is hosted by the USGS on a [public US government site](https://e4ftl01.cr.usgs.gov). 

We'd like you to develop a utility to download and store MODIS data. 

### Considerations

- You will need to use HTTP Basic authentication to authenticate with the server and download data. For convenience, we can give you credentials for this, or you can create your own account at the [USGS EarthData website](https://urs.earthdata.nasa.gov/users/new).
- Your utility should allow the user to input a range of dates, the country to download data for, and the MODIS product to download data for. Please note down any usage instructions in your README.
- The MODIS data we are interested in is indexed as follows: 

```
https://e4ftl01.cr.usgs.gov/{source}/{product}/{date}/{file}
```

- MODIS data is organized by `source`, which corresponds to a single sensor or a combination of satellite sensors. Each source contains several data products. We've provided `modis_data_products.json`. This file contains specific data sources and products we want to download.
- Each MODIS `product` consists of a product name (e.g. `MYD09GA`) and a version (e.g. `006`) separated by a period. You shouldn't have to worry about splitting this for this challenge.
- Each MODIS `date` is in `YYYY.MM.DD` format.
- Each MODIS `file` is an image of a single [tile](https://modis-land.gsfc.nasa.gov/MODLAND_grid.html). We have provided `tiles_in_country.json`, which contains a list of MODIS tiles for the United States and Canada. 
    - There are several types of files available for each `product` and `date`. We only want to download the `.hdf` and `.hdf.xml` files. 
- You can organize the downloaded data however you want, keeping in mind that the files should be easy to access by downstream uses. 
- You should keep track of:
    - files that are available on the source site
    - files that were successfully downloaded
    - downloads that may have failed
- The USGS server may rate limit you by returning empty directories. You should handle download errors in your solution.
- If you choose to use python, you might want to look into the [`pymodis` package](https://pypi.python.org/pypi/pyModis).

### Submission

Please send us a link to a hosted Git repository like GitHub or GitLab. Include your code, dependencies, instructions on how to build and run your code, and a README detailing your work.

We care about your thought process and your engineering prowess. The better we can understand how you approached the problem, the better we can review your project. Here are a few points we'll consider:

- **Do you present your work well in your README?** Many open-source software projects have great README documents that summarize the project, how to use the project, and how to contribute to the project. Here are [some](https://github.com/microsoft/vscode) [noteworthy](https://github.com/fchollet/keras) [examples](https://github.com/reactjs/redux). 

  Your README doesn't have to be super polished, but it should demonstrate your ideas, summarize your work, briefly summarize how to use what you've built, and suggest ways to improve it in the future.

- **Can we understand the problems you're trying to solve and your method for solving them?** If these problems aren't obvious, do you explain your rationale?

- **Is your project intuitive to use?** Is your code quick and easy to use? Can we build or run your code in a single step? You might find it easier to use something like Docker or similar to help with this.

- **Is it easy for others to contribute?** Is your code clean, consistent, and concise? Is it easy for others to understand and build on top of what you've created?

In a follow-up interview, we'd like to discuss your response to this exercise and chat about extending your solution.

## Part 2: Infrastructure design

This is a design exercise.

As one of the first data engineers at Aerial Intelligence, you will have a large impact on the design and implementation of our data infrastructure. We ingest data from numerous sources, including MODIS from the challenge above, and we want to make this data available to customers and to our own engineers and data scientists.
 
We'd like you to write a design proposal for how you might design and implement data infrastructure for storing and representing the following data sources, given the desired use cases. You do not need to describe how the use cases are implemented - just describe how you might get the data to the consumer in a reasonable way.

### Sources

- two satellite sources
    - one satellite source, `A` delivers low resolution data every day, with a 3 day delay at a tile level
    - one satellite source, `B` delivers high resolution data every 14 days at a tile level
    - both sources deliver data via HTTP or FTP direct download of files (similar to exercise 1 above)
    - both sources use different tile formats and boundaries, so tile notation from `A` cannot be used to denote `B` data
    - both sources cover the entire world
    - both satellite sources produce a single kind of value which we care about, just at different times, resolutions, and for different areas at a time. We'll call this 'prettiness'.
    - satellite `A` prettiness is interchangeable with satellite `B` prettiness, so you don't have to worry about converting between `A` and `B`
    - satellite `A` is available from 2000 onwards
    - satellite `B` is available from 2010 onwards
- one weather source
    - weather source `C` delivers either current / historical weather or 7-day forecast weather
    - this source delivers data via a JSON REST API.
    - the data can be retrieved using two endpoints - one for the forecast and one for current / historical.
        - the forecast endpoint takes two params: latitude and longitude
        - the current / historical endpoint takes three params: latitude, longitude, and optionally a timestamp
- one market data source
    - market data source `D` delivers crop price data
    - this source delivers data via streaming JSON API (e.g. Twitter Streaming API)
    - the data is available real time every 15 seconds
    - the data is broken down into lat-long, where each coordinate represents a 50-mile "market radius".

### Use cases

- using a REST API, I want to get weather, market price, and `prettiness` for a given latitude and longitude
- using a REST API, I want to get average weather, average market price, and average `prettiness` for a region
- as a data scientist, I want to get all daily weather and daily market price for the past 15 years
- as a data scientist, I want to get `prettiness` for a single region for the past 15 years, run analysis on it, and store the results for consumption from the REST API

### Bonus challenge

- as a customer, I want to upload proprietary data `Z`, run custom analysis using `Z` and the prettiness from satellite `A` and `B`, and access it through the REST API

## Submission

Please email us a document detailing your proposal as a `README.md` or a PDF. You might consider looking at some design proposal templates (like [this one from Rust](https://github.com/rust-lang/rfcs/blob/master/0000-template.md)) for ideas on how to structure your proposal.

As in exercise #1 above, we'd like to follow up with a chat about your proposal.
