# A common BIML mistake

Working with BIML is a bit like learning to code before stackoverflow.com was available, every little mistake can cause your productivity to froze. Deciphering error messages is a game in itself, an unpleasant one I have to say!

One common BIML mistake to watch out for is the usage of the BimlScript syntax: `<#` vs `<#=`. `<#` defines statements, in .NET code, that provide control flow in a Biml file where as `<#=` evaluates .NET code, converts it to a string, and then embeds it in the Biml file. 

OK, in simple english `<#` **runs** the code whereas `<#=` **returns** a string after running the code. Keep this in mind and take a look at the code snippet below:

```
    <Packages>
    <#
        foreach (XmlNode f in fileList)
        {
            CallBimlScript("boilerplate.biml", f.Attributes["PackageName"].Value);
        }
    #>
    </Packages>
```

If you attempt to generate the packages using the code above, you'll get something like:

> Could not resolve reference to 'xxx' in property 'Package'. '' is invalid. Provide valid scoped name. Property Package.

Not a very helpful error message. What it is trying to is that CallBimlScript returns the package definition and you need to use `<#=` to capture the returned string, otherwise you'll end up with nothing between the `<Packages>` `</Packages>` tags.

A quick fix is shown below, remember to remove the `;` as well:

```
    <Packages>
    <#
        foreach (XmlNode f in fileList)
        {
            #><#=CallBimlScript("boilerplate.biml", f.Attributes["PackageName"].Value)#><#
        }
    #>
    </Packages>
```
