# How to run Python in SQL Server?

### 1. Install Python as part of Machine Learning Services (In-Database)

![python]({{ site.url }}/img/install_machine_learning_services_python.PNG)

### 2. Enable external scripts.

```sql
sp_configure 'external scripts enabled', 1; reconfigure;
```

### 3. Restart SQL Server.

### 4. Run a test in SSMS.

```sql
sp_execute_external_script @language = N'python', @script = N'print("Hello World!")'
```

```
STDOUT message(s) from external script: 
Hello World!
```
