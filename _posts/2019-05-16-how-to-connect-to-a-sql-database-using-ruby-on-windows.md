# How to connect to a SQL Database using Ruby on Windows

1. [Download](https://rubyinstaller.org/downloads/) and install Ruby.

2. Install TinyTDS gem.

    ```ruby
    > gem install tiny_tds
    ```

3. Create a new ruby file: sql.rb

    ```ruby
    require 'tiny_tds'
    client = TinyTds::Client.new username: 'louie', password: 'louie', host: 'louie'

    result = client.execute('select db_name() as [database_name]')

    result.each do |row|
        puts row['database_name']
    end
    ```

4. Run.
    ```
    > ruby sql.rb
    ```
    
5. Check output.
    ```
    master
    ```
