## Introduction
Last year, [Smart Columbus Open Data Enthusiats (SCODE)](https://www.scodemeetup.org/) was issued an [Open Design Challenge](https://github.com/SCODEMeetup/locator-jekyll/blob/release-v2.0/HACKABLE-SNACK.md). The challenge was to help people find and access
Community Services using data from the [Smart Columbus Operating System (SCOS)](https://www.smartcolumbusos.com/). SCODE members could build an interactive map, displaying locations of active service providers where users could navigate the map by location, service provider, and service offerings.

Below are the links are to the initial visualization submissions:
- [Heroku Locator](https://cbus-community-service-locator.herokuapp.com/)
- [Jupyter Notebook Locator](https://github.com/SCODEMeetup/community-services-locator-ui-2)

What follows are a collection of lessons learned from the SCODE teams and their members.

---
## Data Analysis Team
### Entity Relationship Diagram
The initial analysis started with looking at the files listed on the Open Design Challenge and understanding how they related to one another. The 4 comma-separated values (CSV) files are part of the [Food Pantry and User Data](https://ckan.smartcolumbusos.com/dataset/food-pantry-and-user-data) dataset on the SCOS. That dataset is provided by [HandsOn Central Ohio](https://handsoncentralohio.org/), and is comprised of 34 CSVs in total. The files used in the analysis all have an ACTIVE_FLAG (Y/N) field, we are only displaying active records in our visualizations.

| Listed            | Actual            |
|:-:                |:-:                |
| Agency            | Agency            |
|                   | Agency Location   |
|                   | Agency Service    |
| Service Location  |                   |
| Service Taxonomy  | Service Taxonomy  |
| Taxonomy          | Taxonomy          |

The Agency file links all the other files together, it is where the AGENCY_ID primary key (PK) resides. The Service Location file contains contact information and has a foreign key (FK) reference to Agency Location. Agency Location lists the physical address of said agency. The Agency Service file describes the services the agency offers. The Service Taxonomy file has a composite key that lets the Agency Service and Taxonomy files uniquely identify a record. Lastly, the Taxonomy file provides TAXON_ID (PK) and TAXONOMY LEVEL. 

![Image of Entity Relationship Diagram](/img/EntityRelationshipDiagram.PNG)

The diagram can be read as follows:
1. An agency (can) have "zero, one, or many" location(s).
2. An agency (can) provide "one or many" service(s).
3. An agency service (can) have "one or many" taxonomies.

- [Jesse Mathews](https://www.linkedin.com/in/jesse-mathews-20662450/)

---
## Application Program Interface Team
- Vijay's comments
- Mihir's comments
- Eddie's comments - The REST API was created using Node JS and Express JS, and the endpoints were separated by resource type (service category/taxonomy, agency information, location information). All of the endpoints either return a single instance (specified by primary/foreign keys) or a list of objects, with specific query parameters and paging available. The service categories can be queried in a hierarchy using the `/children` endpoint, which returns any categories that are a subcategory of the specified ID. Any breaking changes to the API were introduced in a new version, with v1 being the raw data from the source and v2 being a prettified and slimmed down version of the data. The API is documented via swagger at `/api-docs`.  All endpoints are tested individually using Chai and Mocha (Javascript testing libraries). Future improvements to the API could include the addition of a GraphQL API to better model the data needed between the entities, and addition of other data sets provided by MOFB. Tools: [NodeJs](https://nodejs.org/en/), [ExpressJS](https://expressjs.com/), [ChaiJS](https://www.chaijs.com/), [MochaJS](https://mochajs.org/), [Swagger](https://swagger.io/). Data set: [MOFB Data](https://ckan.smartcolumbusos.com/dataset/food-pantry-and-user-data). Source code: [Github](https://github.com/SCODEMeetup/mofb-api). Api Documentation: [Swagger](https://mofb-api.appspot.com/api-docs/).

---
## User Interface Team
- Mihir's comments
- Adam's comments
