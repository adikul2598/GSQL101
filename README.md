## DATA LOADING
RUN LOADING JOB load_job_categories USING MyDataSource="../data/categories.csv"  
RUN LOADING JOB load_job_customers USING MyDataSource="../data/customers.csv"  

## LOAD JOB

CREATE LOADING JOB load_job_categories FOR GRAPH Northwind {  
      DEFINE FILENAME MyDataSource;  
      LOAD MyDataSource TO VERTEX Category VALUES($0, $1, $2) USING SEPARATOR="|", HEADER="true", EOL="\n", QUOTE="double";  
    }  

CREATE LOADING JOB load_job_customers FOR GRAPH Northwind {  
      DEFINE FILENAME MyDataSource;  
      LOAD MyDataSource TO VERTEX Customer VALUES($0, $2, $3) USING SEPARATOR="|", HEADER="true", EOL="\n", QUOTE="double";  
      LOAD MyDataSource TO EDGE has_phone VALUES($0 Customer, $9 Phone) USING SEPARATOR="|", HEADER="true", EOL="\n", QUOTE="double";  
      LOAD MyDataSource TO EDGE at_address VALUES($0 Customer, $4 Address) USING SEPARATOR="|", HEADER="true", EOL="\n", QUOTE="double";  
      LOAD MyDataSource TO EDGE in_region VALUES($0 Customer, $6 Region) USING SEPARATOR="|", HEADER="true", EOL="\n", QUOTE="double";  
      LOAD MyDataSource TO EDGE in_country VALUES($0 Customer, $8 Country) USING SEPARATOR="|", HEADER="true", EOL="\n", QUOTE="double";  
      LOAD MyDataSource TO EDGE in_postal_code VALUES($0 Customer, $7 PostalCode) USING SEPARATOR="|", HEADER="true", EOL="\n", QUOTE="double";  
      LOAD MyDataSource TO EDGE in_city VALUES($0 Customer, $5 City) USING SEPARATOR="|", HEADER="true", EOL="\n", QUOTE="double";  
      LOAD MyDataSource TO EDGE has_fax VALUES($0 Customer, $10 Fax) USING SEPARATOR="|", HEADER="true", EOL="\n", QUOTE="double";  
    }  
    
## SCHEMA
    
CREATE VERTEX Shipper(PRIMARY_ID id INT) WITH STATS="OUTDEGREE_BY_EDGETYPE", PRIMARY_ID_AS_ATTRIBUTE="true"  
CREATE VERTEX Employee(PRIMARY_ID id INT, lastName STRING, firstName STRING, birthDate DATETIME, hireDate DATETIME, photoPath STRING, salary FLOAT, notes STRING) WITH STATS="OUTDEGREE_BY_EDGETYPE", PRIMARY_ID_AS_ATTRIBUTE="true"  
CREATE VERTEX City(PRIMARY_ID id STRING) WITH STATS="OUTDEGREE_BY_EDGETYPE", PRIMARY_ID_AS_ATTRIBUTE="true"  
CREATE VERTEX Region(PRIMARY_ID id STRING, description STRING) WITH STATS="OUTDEGREE_BY_EDGETYPE", PRIMARY_ID_AS_ATTRIBUTE="true"  
CREATE VERTEX PostalCode(PRIMARY_ID id STRING) WITH STATS="OUTDEGREE_BY_EDGETYPE", PRIMARY_ID_AS_ATTRIBUTE="true"  
CREATE VERTEX Country(PRIMARY_ID id STRING) WITH STATS="OUTDEGREE_BY_EDGETYPE", PRIMARY_ID_AS_ATTRIBUTE="true"  
CREATE VERTEX Territory(PRIMARY_ID id STRING, description STRING) WITH STATS="OUTDEGREE_BY_EDGETYPE", PRIMARY_ID_AS_ATTRIBUTE="true"  
CREATE VERTEX CustomerOrder(PRIMARY_ID id INT, orderDate DATETIME, requiredDate DATETIME, shippedDate DATETIME, shipVia INT, freight STRING, shipName STRING) WITH STATS="OUTDEGREE_BY_EDGETYPE", PRIMARY_ID_AS_ATTRIBUTE="true"  
CREATE VERTEX Address(PRIMARY_ID id STRING) WITH STATS="OUTDEGREE_BY_EDGETYPE", PRIMARY_ID_AS_ATTRIBUTE="true"  

CREATE DIRECTED EDGE reports_to(FROM Employee, TO Employee) WITH REVERSE_EDGE="reverse_reports_to"  
CREATE UNDIRECTED EDGE in_territory(FROM Employee, TO Territory)  
CREATE UNDIRECTED EDGE to_region(FROM CustomerOrder, TO Region)  
CREATE UNDIRECTED EDGE to_country(FROM CustomerOrder, TO Country)  
CREATE UNDIRECTED EDGE to_postal_code(FROM CustomerOrder, TO PostalCode)  
CREATE UNDIRECTED EDGE to_city(FROM CustomerOrder, TO City)  

CREATE GRAPH Northwind(Shipper, Employee, City, Region, PostalCode, Country, Territory, CustomerOrder, Address, Title, TitleOfCourtesy, Customer, Phone, Fax, Product, Category, Company, CustomerType, Supplier, reports_to, in_territory, to_region, to_country, to_postal_code, to_city, to_address, has_title, has_toc, placed_order, is_category, contains_product)
