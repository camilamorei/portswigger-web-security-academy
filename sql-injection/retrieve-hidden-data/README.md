# SQL injection in WHERE clause allowing retrieval of hidden data (Apprentice)

Platform: PortSwigger Web Security Academy  
Category: SQL Injection  
Difficulty: Apprentice  
Status: Solved

## Summary

In this lab, I exploited a SQL injection vulnerability in the product category filter.

The application used the `category` URL parameter directly in a SQL query without properly separating user input from SQL instructions.

By modifying the parameter with a SQL injection payload, I bypassed the condition that restricted the application to displaying released products. As a result, the application returned all products, including unreleased ones.

## Vulnerability category

SQL Injection - Retrieval of hidden data.

SQL injection occurs when an application incorporates untrusted user input into a SQL query without safely handling it.

In this case, the vulnerable parameter was `category`, which allowed the query logic to be modified.

## Lab objective

Retrieve unreleased products by exploiting the category filter in the online store.

## Step-by-step

1. I opened the online store and selected any product category.

2. I inspected the URL and found the following parameter:

   ```text
   /filter?category=Gifts
   ```

3. I modified the `category` parameter by inserting the following SQL injection payload:

   ```text
   /filter?category=Gifts'+OR+1=1--
   ```

4. I submitted the modified URL.

5. The application returned all products instead of only the products belonging to the selected category.

6. The response included unreleased products, confirming that the SQL injection successfully bypassed the filtering condition and completing the lab.

## Why it works

The application originally used a query equivalent to:

```sql
SELECT * FROM products
WHERE category = 'Gifts' AND released = 1
```

The injected payload modified the query logic, resulting in:

```sql
SELECT * FROM products
WHERE category = 'Gifts' OR 1=1 --' AND released = 1
```

The payload works because:

- `'` closes the original string containing the category value.
- `OR 1=1` introduces a condition that is always true.
- `--` comments out the remaining part of the SQL statement, including the original `AND released = 1` condition.

Since `1=1` is always true, the query returns all matching rows instead of restricting the results to the selected category and released products.

## Root cause

The application concatenated user-controlled input directly into a SQL query.

The `category` parameter was treated as part of the SQL command instead of being handled as data.

This allowed an attacker to modify the structure and logic of the original query.

## Vulnerable implementation

An insecure implementation might look like this:

```python
query = "SELECT * FROM products WHERE category = '" + category + "' AND released = 1"
```

Because the input is concatenated directly into the query, SQL instructions supplied through the parameter can alter the statement.

## Secure implementation

The application should use parameterized queries (prepared statements):

```python
cursor.execute(
    "SELECT * FROM products WHERE category = %s AND released = 1",
    (category,)
)
```

Parameterized queries separate SQL instructions from user-supplied data.

The database therefore treats the category value as data rather than executable SQL code.

## Real-world impact

SQL injection can allow attackers to manipulate database queries and access information that should not be publicly available.

Depending on the application's database permissions and implementation, the impact may include:

- Access to hidden or confidential records.
- Exposure of personal information.
- Retrieval of authentication credentials.
- Unauthorized modification or deletion of database records.
- Authentication bypass.
- Potential compromise of the database.

## Mitigation

- Use parameterized queries or prepared statements for database operations.
- Never concatenate untrusted input directly into SQL statements.
- Validate input according to the expected data type and format.
- Apply the principle of least privilege to database accounts.
- Avoid exposing detailed database errors to users.
- Perform security testing on all endpoints that accept user-controlled input.

## Key takeaway

Every user input that reaches a SQL query must be handled safely.

String concatenation allows user input to become part of the SQL command. Parameterized queries keep instructions and data separate, preventing this class of SQL injection.

## References

- [PortSwigger Web Security Academy - SQL injection](https://portswigger.net/web-security/sql-injection)
- [PortSwigger Web Security Academy - SQL injection in WHERE clause allowing retrieval of hidden data](https://portswigger.net/web-security/sql-injection/lab-retrieve-hidden-data)
