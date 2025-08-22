Docker
```
docker run -d -p 1521:1521 -p 1522:1522 -p 8443:8443 -p 27017:27017 -e WORKLOAD_TYPE=ATP -e WALLET_PASSWORD=<Password> -e ADMIN_PASSWORD=<Password> --cap-add SYS_ADMIN --device /dev/fuse --name adb-free --hostname adb-free ghcr.io/oracle/adb-free:latest-23ai
```
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
Oracle alert location
```
cd /u01/app/oracle/diag/rdbms/pod1/POD1/trace
```
Oracle Sample Schema's
```
mkdir downloads
cd downloads/
wget https://github.com/oracle-samples/db-sample-schemas/archive/refs/tags/v23.3.tar.gz
tar -xvzf v23.3.tar.gz
cd db-sample-schemas-23.3/sales_history
sql admin/<password>@localhost:1521/myatp @sh_install
```
Create ai profile
```
sh-4.4$ sql admin/<Password>@localhost:1521/myatp


SQLcl: Release 24.4 Production on Fri Jun 06 08:12:11 2025

Copyright (c) 1982, 2025, Oracle.  All rights reserved.

Last Successful login time: Fri Jun 06 2025 08:12:12 +00:00

Connected to:
Oracle Database 23ai Enterprise Edition Release 23.0.0.0.0 - for Oracle Cloud and Engineered Systems
Version 23.8.0.25.05

SQL> BEGIN
  2       DBMS_NETWORK_ACL_ADMIN.APPEND_HOST_ACE(
  3           host => 'api.openai.com',
  4           ace  => xs$ace_type(privilege_list => xs$name_list('http'),
  5                               principal_name => 'ADMIN',
  6                               principal_type => xs_acl.ptype_db)
  7       );
  8      END;
  9*     /

PL/SQL procedure successfully completed.

SQL> SELECT * FROM dba_network_acls;

HOST                 LOWER_PORT    UPPER_PORT ACL                                             ACLID               ACL_OWNER    
_________________ _____________ _____________ _______________________________________________ ___________________ ____________ 
*                                             NETWORK_ACL_339116DCD2B817B6E06336CD5E64DFED    0000000080002724    SYS          
api.openai.com                                NETWORK_ACL_36E3CB76038908DAE063020011AC9C88    000000008000274C    SYS          

SQL> SELECT * FROM dba_network_acl_privileges;

ACL                                             ACLID               PRINCIPAL            PRIVILEGE    IS_GRANT    INVERT    START_DATE    END_DATE    ACL_OWNER    
_______________________________________________ ___________________ ____________________ ____________ ___________ _________ _____________ ___________ ____________ 
NETWORK_ACL_339116DCD2B817B6E06336CD5E64DFED    0000000080002724    GSMADMIN_INTERNAL    resolve      true        false                               SYS          
NETWORK_ACL_36E3CB76038908DAE063020011AC9C88    000000008000274C    ADMIN                http         true        false                               SYS          

SQL> BEGIN
  2    DBMS_CLOUD.CREATE_CREDENTIAL(
  3      credential_name => 'AI_CRED',
  4      username => '<email>',
  5      password => '<API-key>'
  6    );
  7  END;
  8* /

PL/SQL procedure successfully completed.

SQL> SELECT * FROM user_credentials;

CREDENTIAL_NAME    USERNAME                   WINDOWS_DOMAIN    COMMENTS                                                   ENABLED    
__________________ __________________________ _________________ __________________________________________________________ __________ 
AI_CRED            <email>                                      {"comments":"Created via DBMS_CLOUD.create_credential"}    TRUE       

SQL> BEGIN
  2    DBMS_CLOUD_AI.create_profile(
  3        profile_name => 'SH_OPENAI',
  4        attributes => '{"provider": "openai",
  5                        "credential_name": "AI_CRED",
  6                        "object_list": [{"owner": "SH", "name": "channels"},  
  7                                        {"owner": "SH", "name": "costs"},
  8                                        {"owner": "SH", "name": "countries"},
  9                                        {"owner": "SH", "name": "customers"},  
 10                                        {"owner": "SH", "name": "products"},
 11                                        {"owner": "SH", "name": "promotions"},
 12                                        {"owner": "SH", "name": "sales"},
 13                                        {"owner": "SH", "name": "times"},
 14                                        {"owner": "SH", "name": "supplementary_demographics"}]
 15         }');
 16  END;
 17* /

PL/SQL procedure successfully completed.

SQL> BEGIN
  2    DBMS_CLOUD_AI.SET_PROFILE(
  3       profile_name => 'SH_OPENAI'
  4    );
  5  END;
  6* /

PL/SQL procedure successfully completed.
```
Select ai demo
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

SQL> select ai what are woman and men spending;

   TOTAL_AMOUNT_SOLD    TOTAL_QUANTITY_SOLD CUST_GENDER    
____________________ ______________________ ______________ 
          34772567.7                 324140 F              
         63433263.51                 594703 M              

SQL> select ai who are spending more men or woman;

Error starting at line : 1 in command -
select ai who are spending more men or woman
Error at Command Line : 1 Column : 15
Error report -
SQL Error: ORA-00900: invalid SQL statement

https://docs.oracle.com/error-help/db/ora-00900/00900. 00000 -  "invalid SQL statement"
*Cause:    The statement was not recognized as a valid SQL
           statement. This error can occurred if the Procedural Option was
           not installed and a SQL statement was issued that requires this
           option (for example, a CREATE PROCEDURE statement). You can
           determine if the Procedural Option was installed by starting
           SQL*Plus. If the PL/SQL banner was not displayed, then the
           option was not installed.
*Action:   Correct the syntax or install the Procedural Option.

More Details :
https://docs.oracle.com/error-help/db/ora-00900/

SQL> select ai showsql who are spending more men or woman;

RESPONSE                                                                                                                                                      
_____________________________________________________________________________________________________________________________________________________________ 
Sorry, unfortunately a valid SELECT statement could not be generated for your natural language prompt. Here is some more information to help you further: 

```sql
SELECT gender,
       SUM(amount_sold) AS total_spent
FROM "SH"."SALES" s
JOIN "SH"."CUSTOMERS" c ON s.cust_id = c.cust_id
WHERE c.cust_gender = 'M' OR c.cust_gender = 'F'
GROUP BY gender
ORDER BY total_spent DESC    
```
