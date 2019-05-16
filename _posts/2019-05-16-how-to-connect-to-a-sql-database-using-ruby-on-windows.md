# How to connect to a SQL Database using Ruby on Windows

1. [Download](https://rubyinstaller.org/downloads/) and install Ruby.

2. Install TinyTDS gem.

```ruby
gem install tiny_tds
```

3. Test connection.

```ruby
require 'tiny_tds'
client = TinyTds::Client.new username: 'louie', password: 'louie', host: 'servername', port: 1433, database: 'louie'

result = client.execute('select db_name() as [database_name]')

result.each do |row|
    puts row[:database_name]
end
```
