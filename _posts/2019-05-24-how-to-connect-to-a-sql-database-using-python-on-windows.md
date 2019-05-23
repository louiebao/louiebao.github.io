# How to connect a SQL database using Python on Windows

1. [Download](https://www.python.org/downloads/) and install python.

2. Install pyodbc.
    ```python
    > pip install pyodbc
    ```

3. Create a new python file: sql.py
    ```python
    import pyodbc 

    cnxn = pyodbc.connect('DRIVER={SQL Server};Server=louie;Trusted_Connection=yes;')
    cursor = cnxn.cursor()

    cursor.execute("select db_name() as [database_name];") 
    for row in cursor:
        print(row)
    ```
    
4. Test connection.
    ```python
    > python sql.py
    ```
    
5. Check output.    
    ```
    ('master', )
    ```
