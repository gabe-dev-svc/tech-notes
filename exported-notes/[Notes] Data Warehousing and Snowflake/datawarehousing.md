# Datewarehousing

- [Datewarehousing](#datewarehousing)
    - [Acronyms](#acronyms)
    - [_What is a data warehouse?_](#what-is-a-data-warehouse)
    - [_What's Dimensional Modeling?_](#whats-dimensional-modeling)

### Acronyms
- __OLTP__: Online Transaction Processing
- __OLAP__: Online Analytics Processing 

### _What is a data warehouse?_
  - A data warehouse is a database used primarily for data analysis which holds read-only copies of data from several OLTP company databases. 
  - Data warehouses are optimized for different kinds of queries from transactional databases given their indexing engines differ from those found in transactional databases.
  - Data warehouse datamodels typically follow something called a _star schema_ or a _snowflake schema_ both of which are forms of _dimensional modeling_

### _What's Dimensional Modeling?_
  - An example of dimensional modeling would be the use of a `fact_sales` table holding all transactions that have taken place. From there, different dimensional tables would exist for customers, products, promotions, stores, etc. 
    - e.g.

    ![Star Schema Example](assets/star-schema.png)
