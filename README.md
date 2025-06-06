yum/dnf
```
docker exec -u root --workdir / -ti adb-free /bin/sh
whoami

cd /etc/yum/vars
mv ociregion ociregion.sav
touch ociregion

dnf install vi -y
dnf install wget -y
dnf install ncurses -y
dnf install iputils -y
```
Oracle Sample Schema's
```
mkdir downloads
cd downloads/
wget https://github.com/oracle-samples/db-sample-schemas/archive/refs/tags/v23.3.tar.gz
tar -xvzf v23.3.tar.gz
cd db-sample-schemas-23.3/sales_history
sql admin/SysPassword1@localhost:1521/myatp @sh_install
```
Select ai
```
SQL> select ai how many customers in san fransisco are married;

   MARRIED_CUSTOMERS_COUNT 
__________________________ 
                         0 

SQL> select ai showsql how many customers in san fransisco are married;

RESPONSE                                      
_____________________________________________ 
SELECT COUNT(*) AS married_customers_count
FROM "SH"."CUSTOMERS" c
WHERE c."CUST_CITY" = 'San Francisco'
AND c."CUST_MARITAL_STATUS" = 'Married'    

SQL> select cust_marital_status, count(*) from sh.customers group by cust_marital_status;

CUST_MARITAL_STATUS       COUNT(*) 
______________________ ___________ 
divorced                       765 
married                      16287 
never married                 1503 
separated                      134 
single                       19094 
widowed                        211 
                             17506 

7 rows selected. 

SQL> SELECT COUNT(*) AS "Number of Married Customers in San Francisco"
  2  FROM "SH"."CUSTOMERS" c
  3  WHERE c."CUST_CITY" = 'San Francisco'
  4* AND c."CUST_MARITAL_STATUS" = 'married';

   Number of Married Customers in San Francisco 
_______________________________________________ 
                                             46

SQL> select ai find the top 3 baby boomers big spenders;

CUSTOMER_NAME       CUST_YEAR_OF_BIRTH CUST_EMAIL                     TOTAL_AMOUNT_SPENT 
________________ _____________________ ___________________________ _____________________ 
Dora Rice                         1961 Rice@company.example.com                103412.66 
Lolita Katz                       1962 Katz@company.example.com                 99578.09 
Mitchel Polk                      1954 Polk@company.example.com                 97010.48 

SQL> select ai showsql find the top 3 baby boomers big spenders;

RESPONSE                                                                                                                                                                                                                                                                                                                                                                                                                                                        
_______________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________ 
SELECT 
    C.CUST_FIRST_NAME || ' ' || C.CUST_LAST_NAME AS CUSTOMER_NAME,
    C.CUST_YEAR_OF_BIRTH,
    C.CUST_EMAIL,
    SUM(S.AMOUNT_SOLD) AS TOTAL_AMOUNT_SPENT
FROM 
    "SH"."CUSTOMERS" C
JOIN 
    "SH"."SALES" S ON C.CUST_ID = S.CUST_ID
WHERE 
    C.CUST_YEAR_OF_BIRTH BETWEEN 1946 AND 1964
GROUP BY 
    C.CUST_FIRST_NAME, C.CUST_LAST_NAME, C.CUST_YEAR_OF_BIRTH, C.CUST_EMAIL
ORDER BY 
    TOTAL_AMOUNT_SPENT DESC
FETCH FIRST 3 ROWS ONLY    
```
