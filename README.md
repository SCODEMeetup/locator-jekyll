## Introduction
Last year, [Smart Columbus Open Data Enthusiats (SCODE)](https://www.scodemeetup.org/) was issued an [Open Design Challenge](https://github.com/SCODEMeetup/locator-jekyll/blob/release-v2.0/HACKABLE-SNACK.md). The challenge was to help people find and access
Community Services using data from the [Smart Columbus Operating System (SCOS)](https://www.smartcolumbusos.com/). SCODE members could build an interactive map, displaying locations of active service providers where users could navigate the map by location, service provider, and service offerings.

Below are the links to the initial visualization submissions:
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

The **Agency** file uses name, ID number and other values to tell you about a specific agency. The **Service Location** file contains contact information (phone numbers, hours, email address, website, etc...) but did not contain an agency's physical address. That information was found in the **Agency Location** file. The **Agency Service** file describes the services an agency offers. 

![Image of Entity Relationship Diagram](/img/EntityRelationshipDiagram.PNG)

The diagram can be read as follows:
1. An agency (can) have "zero, one, or many" location(s).
2. An agency (can) provide "one or many" service(s).
3. An agency service (can) have "one or many" taxonomies.

### Taxonomy Hierarchy?
The taxonomy file has five different physical levels. Each record in the file has a unique TAXON_ID value. That value relates parent
and child records from one level to the next. While the records are logically related, the data structure is not a hierarchy. Each 
record is an individual endpoint. An agency may offer a level 5 service without offering services at levels 1-4.
```bash
├── BASIC_NEEDS
│   ├── FOOD
│   │   ├── EMERGENCY_FOOD
│   │   │   ├── FOOD_PANTRIES
├── EDUCATION
│   ├── EDUCATIONAL_PROGRAMS
├── HEALTHCARE
│   ├── SPECIALITY_MEDICINE
│   │   ├── PEDIATRICS
```
- [Jesse Mathews](https://www.linkedin.com/in/jesse-mathews-20662450/)

---
## Application Program Interface Team
### REST API
The REST API was created using Node JS and Express JS, and the endpoints were separated by resource type (service category/taxonomy, agency information, location information). All of the endpoints either return a single instance (specified by primary/foreign keys) or a list of objects, with specific query parameters and paging available. The service categories can be queried in a hierarchy using the `/children` endpoint, which returns any categories that are a subcategory of the specified ID.

Any breaking changes to the API were introduced in a new version, with v1 being the raw data from the source and v2 being a prettified and slimmed down version of the data. The API is documented via swagger at `/api-docs`.  All endpoints are tested individually using Chai and Mocha (Javascript testing libraries). Future improvements to the API could include the addition of a GraphQL API to better model the data needed between the entities, and addition of other data sets provided by MOFB.

1. Tools: [NodeJs](https://nodejs.org/en/), [ExpressJS](https://expressjs.com/), [ChaiJS](https://www.chaijs.com/), [MochaJS](https://mochajs.org/), [Swagger](https://swagger.io/).
2. Data set: [MOFB Data](https://ckan.smartcolumbusos.com/dataset/food-pantry-and-user-data).
3. Source code: [Github](https://github.com/SCODEMeetup/mofb-api).
4. API Documentation: [Swagger](https://mofb-api.appspot.com/api-docs/).

- [Eddie Legg](https://www.linkedin.com/in/delbert-eddie-legg-5ba6a583/)

---
## User Interface Team
### PWA using Reactjs, Redux with Google maps
The Community Service locator UI is a progressive web app that creates an interactive map, displaying locations of active service providers where users could navigate the map by location, service provider, and service offerings. It uses Reactjs library and its ecosystem.
1. React Ecosystem: [Reactjs](https://reactjs.org/), [Redux](https://redux.js.org/), [react-google-maps](https://tomchentw.github.io/react-google-maps/), 
2. Testing: [Jest](https://jestjs.io/), [Enzyme](https://airbnb.io/enzyme/)
3. Data set : Rest calls to API project
4. Source Code: [Github](https://github.com/SCODEMeetup/community-services-locator-ui)

- [Mihir Amin](https://www.linkedin.com/in/mihir-amin-46910540/), [Melissa Boyer](https://www.linkedin.com/in/melissa3000/), [Vijay Yadav](https://www.linkedin.com/in/vejay/)

---
### Mapbox
One approach to visualizing these locations on a map is to use React and Mapbox to manage the data as a Vector Tileset. In order for a dataset to be converted to a Vector Tileset, it needs values for Longitude and Latitude, and since the initial dataset did not have these values, they can be run through a Jupyter Notebook in order to produce these values.

From there, Mapbox provides a free tool to upload that data, and convert it into a Vector Tileset. Vector Tilesets are built to work with more front-end mapping frameworks, and reduce work needed to render the data into a map. From this point, a relatively minimal React frontend is used to populate the data on the map. A benefit of this approach is that a query made on the front end can search through the data for these points without the need to talk to the OS since that data is included in the Vector Tileset, although that component of the UI hasn't been implemented yet.

1. Data: Food Bank [Agency Locations](https://ckan.smartcolumbusos.com/dataset/b0390b58-35c9-45e8-8a2d-d2472b20d65f/resource/570a8e02-fb0e-4cee-895b-3b32bd740650)
2. Dataset uploaded through [Jupyter Notebook](https://jupyter.org/) to geocode data, and saved as new csv
3. New csv uploaded to Mapbox as tileset (Map ID: mapbox://smrtcbus.9n1znhht)

![Map](img/Locations.png)

- [Adam Sauer](https://www.linkedin.com/in/adam-sauer-5167a640/)
