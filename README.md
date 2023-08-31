# TransformDictCC
Transforms dict.cc dictionary downloads

- open "Prepare_dict_cc_dict.dib" in Visual Studio Code (requires the extension "Polyglot Notebooks")
- download dictionary "DE->EN (tab-delimited, UTF-8)" from [dict.cc](https://www1.dict.cc/translation_file_request.php?l=e)
- adjust paths to your source/target files
- then just run it.  

---

It's all just this snippet really:

```csharp
var input = File.ReadAllLines(@"C:\tmp\dict.txt");      //source file
var output = new List<string>();
foreach(var line in input)
{
    string[] args = line.Split("\t");
    string german = args[0];
    string englisch = args[1];
    
    if(englisch.Contains("..."))                            // idiom with wildcard is not searchable, so skip it
        continue;
    
    englisch = Regex.Replace(englisch, @"\[.*?\]", "");     //remove [...]
    englisch = Regex.Replace(englisch, @"<.*?>", "");       //remove <...>
    englisch = Regex.Replace(englisch, @"{.*?}", "");       //remove {...}
    englisch = Regex.Replace(englisch, @"\(.*?\)", "");     //remove (...)
    englisch = Regex.Replace(englisch, @"  ", " ");         //replace double-spaces
    englisch = englisch.Trim();                             //trim start/end spaces    
    
    var spellings = englisch.Split(" / ");                  // split alternative spellings
    foreach(var alt in spellings.Where(s => s != ""))
    {
        output.Add(alt + "\t" + german);
    }
}
output.Sort();
File.WriteAllLines(@"C:\tmp\dict_en-de.txt", output);       // target file
```
