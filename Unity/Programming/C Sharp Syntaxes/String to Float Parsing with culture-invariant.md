- Parse with culture-invariant settings for reliable results:

```CS
string s = "123,456.789"; float f = float.Parse(s, CultureInfo.InvariantCulture);
```