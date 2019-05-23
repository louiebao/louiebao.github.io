how-to-connect-to-a-sql-database-using-nodejs-on-windows
# How to connect to a SQL database using node.js on Windows

1. [Download](https://nodejs.org/en/download/) and install node.js
2. Create a project directory.
    ```
    > mkdir sql
    > cd sql
    ```
3. Create a node project.
    ```
    > npm init
    ```
3. Install tedious module.
    ```
    > npm install tedious
    ```
4. Create a new javascript file: sql.js
    ```javascript
    var Connection = require('tedious').Connection;  
    var config = {  
        server: 'servername',  
        authentication: { 
            options: { 
                userName: 'louie', 
                password: 'louie' 
            }, 
            type: 'default' 
        },
        options: { port: 1433, database: 'louie' }
    };  

    var connection = new Connection(config);  

    connection.on('connect', err => {  
        if (err) {
            console.log(err);
        } else {
            console.log("Connected");
            executeStatement();		
        }
    });  

    var Request = require('tedious').Request;

    function executeStatement() {  
        request = new Request("select db_name() as [database_name]", err => {  
            if (err) {  
                console.log(err);
            }  
        });  

        request.on('row', columns => {  
            columns.forEach(column => {  
                console.log("Database: " + column.value);
            });
        });

        connection.execSql(request);  
    }
    ```
5. Test connection.
    ```
    > node sql.js
    ```
    
