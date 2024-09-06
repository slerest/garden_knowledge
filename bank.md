# DONE Chose country

I simplify the project with choosing only these country:

|                         name                         | country_code |
|------------------------------------------------------|--------------|
| Germany                                              |          276 |
| Italy                                                |          380 |
| France                                               |          250 |
| United Kingdom of Great Britain and Northern Ireland |          826 |
| United States of America                             |          840 |
| Paraguay                                             |          600 |


# DONE generate data Admin merchants

generate admin merchants for every merchants in `data/merchants.csv` like this : `{name_merchants}_{admin}` 
add these admin merchants to the user table

```
bank=> COPY bank.merchant FROM '/hostdata/merchants.csv' DELIMITER ',' CSV HEADER;
ERROR:  missing data for column "admin_id"
CONTEXT:  COPY merchant, line 2: "1,Spinka Inc,2023-07-26 03:59:06,250"
```

# TODO ALTER SEQUENCE of id in SQL tables 

**slerest 17/07/2024**

I insert 1000 users with specific id.
The DB will only update the sequence user_id_seq if a nextval is called.
If you point id=1 manually, the sequence in the DB would still hold its initial value,
since SQLA won't call nextval to determine the next value of the sequence.

So i have to specify the start to 1001 for insert new user without specify id:

```sql
ALTER SEQUENCE user_id_seq RESTART WITH 1001;
```

It could be a problem with other table. **to investigate**

[explanation found here](https://stackoverflow.com/a/67018850)

# generate data product

for every marchants we should generate a random number beetwen 1 and 150 product

**update**: the first dataset we get on kaggle was broken

kaggle is a cool site for download dataset

asin: id of amazon product
price: Current price of the product. If the price is unavailable, it is represented as 0 (type: float, Currency: CAD)
original_price: Original price of the product before any discounts. If no list price is available, it is represented as 0 (type: float)


TODO
1. create table category and fill it -> Ok
2. fill product table

 Schema |       Name       | Type  | Owner
--------+------------------+-------+-------
 bank   | country          | table | bank
 bank   | merchant         | table | bank
 bank   | order            | table | bank
 bank   | order_item       | table | bank
 bank   | product          | table | bank
 bank   | product_category | table | bank
 bank   | user             | table | bank


\copy bank.tmp_product(asin, title, imgurl, producturl, stars, reviews, price, listprice, category_id, isbestseller, boughtinlastmonth) from '/hostdata/product/product_usa.csv' delimiter ',' CSV HEADER;

```sql
UPDATE bank.tmp_product p
SET p.categroy_name = c.name
FROM bank.product_category c
    JOIN table_3 t3 ON t3.id = t2.t3_id
```

```sql
SELECT
    c.id, p.category_name
FROM bank.tmp_product p
    LEFT JOIN product_category c ON CAST(p.category_id AS bigint) = c.id;
```

```sql
UPDATE bank.tmp_product p
SET p.categroy_name = c.name
FROM bank.tmp_product p JOIN  product_category c ON CAST(p.category_id AS bigint) = c.id;
```

cette requete avec plus de CPU donnée au container db
```sql
UPDATE bank.tmp_product
SET category_name = c.name
FROM bank.tmp_product p JOIN  product_category c ON CAST(p.category_id AS bigint) = c.id;
```

cette requete avec plus de CPU donnée au container db
```sql
UPDATE bank.tmp_product p
SET category_name = (
    SELECT name
    FROM product_category c
    WHERE CAST(p.category_id AS bigint) = c.id 
);
```
Ca a marcher en 2sc

On a tout dans une table tmp_product ou il manque les `merchant_id`

|      Column       |            Type             | Collation | Nullable |                 Default |
|-------------------|-----------------------------|-----------|----------|--------------------------|
| id                | bigint                      |           | not null | nextval('tmp_product_id_seq'::regclass) | 
| asin              | text                        |           | not null | | 
| title             | text                        |           | not null | | 
| imgurl            | text                        |           |          | | 
| producturl        | text                        |           |          | | 
| stars             | numeric                     |           |          | | 
| reviews           | integer                     |           |          | | 
| price             | numeric                     |           |          | | 
| listprice         | numeric                     |           |          | | 
| category_id       | text                        |           |          | | 
| isbestseller      | boolean                     |           |          | | 
| boughtinlastmonth | bigint                      |           | not null | | 
| created_at        | timestamp without time zone |           | not null | now() | 
| category_name     | text                        |           |          | | 

et voici `product`


|    Column     |            Type              | Collation | Nullable |               Default   |
|----------------|-----------------------------|-----------|----------|------------------------|
| id             | bigint                      |           | not null | nextval('product_id_seq'::regclass) | 
| asin           | character varying(255)      |           | not null | | 
| name           | character varying(255)      |           | not null | | 
| image_url      | character varying(255)      |           |          | | 
| product_url    | character varying(255)      |           |          | | 
| stars          | numeric                     |           |          | | 
| reviews        | integer                     |           |          | | 
| price          | numeric                     |           |          | | 
| original_price | numeric                     |           |          | | 
| category_name  | character varying(255)      |           |          | | 
| is_best_seller | boolean                     |           |          | | 
| merchant_id    | bigint                      |           | not null | | 
| created_at     | timestamp without time zone |           | not null | now() | 

**05/08/2024**

J'ai remarque que les category_name c'etait mal inserer dans tmp_product pour les donne product du canada donc j'ai separer en 2 table:
- `tmp_usa_product`
- `tmp_canada_product`

`bank=> \d tmp_usa_product`

|      Column       |            Type             | Collation | Nullable |                 Default |
| ------------------|-----------------------------|-----------|----------|----------------------------------------- |
| id                | bigint                      |           | not null | nextval('tmp_product_id_seq'::regclass) |
| asin              | text                        |           | not null | |
| title             | text                        |           | not null | |
| imgurl            | text                        |           |          | |
| producturl        | text                        |           |          | |
| stars             | numeric                     |           |          | |
| reviews           | integer                     |           |          | |
| price             | numeric                     |           |          | |
| listprice         | numeric                     |           |          | |
| category_id       | text                        |           |          | |
| isbestseller      | boolean                     |           |          | |
| boughtinlastmonth | bigint                      |           | not null | |
| created_at        | timestamp without time zone |           | not null | now() |
| category_name     | text                        |           |          | |

`bank=> \d tmp_canada_product`

|      Column       |            Type             | Collation | Nullable |             Default |
| ------------------|-----------------------------|-----------|----------|---------------------------------- |
| id                | bigint                      |           | not null | generated by default as identity |
| asin              | text                        |           | not null | |
| title             | text                        |           | not null | |
| imgurl            | text                        |           |          | |
| producturl        | text                        |           |          | |
| stars             | numeric                     |           |          | |
| reviews           | integer                     |           |          | |
| price             | numeric                     |           |          | |
| listprice         | numeric                     |           |          | |
| categoryname      | text                        |           |          | |
| isbestseller      | boolean                     |           |          | |
| boughtinlastmonth | bigint                      |           | not null | |
| created_at        | timestamp without time zone |           | not null | now() |

**10/08/2024**

On a enfin une table product OK tier avec quelque perte de donnée  depuis les CSV mais osef c'est deja assez concequent pour que que l'on veut avoir comme rsultat 

```
bank=> select count(*) from product;
  count
---------
 3894338
(1 row)
```

# generate data order and order_item

La table `order`

|   Column   |            Type             | Collation | Nullable |              Default |
| -----------|-----------------------------|-----------|----------|----------------------------------- |
| user_id    | bigint                      |           | not null | |
| status     | character varying(50)       |           | not null | |
| created_at | timestamp without time zone |           |          | now() |
| id         | bigint                      |           | not null | nextval('order_id_seq'::regclass) |

La table `order_item`

|  Column   |  Type   | Collation | Nullable |                Default |
|-----------|---------|-----------|----------|---------------------------------------- |
|id         | bigint  |           | not null | nextval('order_item_id_seq'::regclass) |
|order_id   | bigint  |           | not null | |
|product_id | bigint  |           | not null | |
|quantity   | integer |           | not null | |

on a 1100 user ( dont 100 marchants )
3894338 produit au total et 2179 par marchant

on va dire que chaque user non marchant fait un nombre random **x** de commande avec comme date **y** entre `01/01/2011` et `now()`

nombre de commande **x** entre 1 et 2500:
- 60% des utilisateurs entre 1 et 40 commandes.
- 10% des utilisateurs entre 40 et 100 commandes.
- 5% des utilisateurs entre 100 et 200 commandes.
- 3% des utilisateurs entre 200 et 500 commandes.
- 2% des utilisateurs entre 500 et 2000 commandes.
- 20% des utilisateurs entre 2000 et 10000 commandes, ces utilisateurs sont des comptes d'entreprise ( TODO rajouter un champ dans la table user pour ces comptes la ?)

il nous faudrait pour cela une fonction de ce genre:

seulement 5% de chance pour chaque order qu'elle soit en cours

**status order** va etre tres simple pour demarrer, soit en cours soit terminé

quantity de chaque order -> on va init a 0, ens unsuite on va update pour chaquer order: entre 1 et 120 unite
- 80% des order ont 1 unité
- 10% des order ont entre 2 et 10 unité
- 5% des order ont entre 10 et 30 unité
- 5% des order ont entre 30 et 120 unité
