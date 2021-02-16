---
title: Find duplicate rows in a database using standard SQL
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2006-01-31T00:15:13.000Z'
---
Thanks goes to Xgc in #mysql on freenode for showing me this little one-liner.  We needed to add a unique key to one of our tables, and a duplicate was in our midst.  Enter this handy one-liner:

```
SELECT field1, count(*) FROM tbl1 GROUP BY field1 HAVING count(*) > 1;
```

problem solved, a nice simple list of the duplicated rows in front of you.
