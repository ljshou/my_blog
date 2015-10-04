title: parse date in python
date: 2015-10-02 21:00:54
tags: python
categories: python
---
This post introduce a powerful date-time parse module called **dateutil**, which is powerful extensions to the standard datetime module.
<!--more-->
### Brief
Parsing Time is a commonly task in our daily work, such as mining Logs.
As variously date formats exist, say "2015/08/10" or "08-10-2015", a date parse module would be quite usefull. 
The **dateutil** module provides powerful extensions to the standard datetime module, available in Python 2.3+.

### Installation
**dateutil** is not a standard module in Python. So you need to install it first.  
[Installation](https://labix.org/python-dateutil#head-2f49784d6b27bae60cde1cff6a535663cf87497b)

### Example
```py
from dateutil.parser import parse

print parse("2015-08-10")
print parse("08-11-2015")
print parse("08-11-2015 13:53:00")
print parse("2015-08-10  13:53:00")

# time difference in seconds
print int( (parse("2015-08-10")-parse("08-11-2015")).total_seconds() )
# time difference in mininutes
print int( (parse("2015-08-10") - parse("08-11-2015")).total_seconds() / 60 )
```

### Reference 
https://labix.org/python-dateutil#head-42a94eedcff96da7fb1f77096b5a3b519c859ba9
