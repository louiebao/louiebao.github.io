### How to start many applications with one script

Many of us have a suite of development tools that we use on a daily basis and it can be a pain to start them one by one. It can be a little time saver if you place all the frequently accessed tools inside a powershell script and start them up all at once.

For example

```
$programs = @(
      @{ app = "C:\Program Files (x86)\Microsoft Visual Studio\2017\Pro\Common7\IDE\devenv.exe"; }
    , @{ app = "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\Ssms.exe"; args = @("/nosplash", "-S", "louie_server", "-d", "louie_db") }
)

foreach ($program in $programs) {
    $app = $program.app
    $args = $program.args

    & $app $args
}
```
