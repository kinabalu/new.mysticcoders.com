---
layout: post
title:  "Python script to convert jekyll to Next.JS"
date: '2021-02-15T12:00:00.0000Z'
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
comments: true
---

With ...
If you're building any application using web technology destined for a browser it will likely need to interact with a third party API. Web development has taken a lot of the wonderful patterns from backend development we've all enjoyed and moved it to the frontend. With a server-based backend we would be looking at access to a persistence infrastructure possibly directly (though that isn't the best practice), or through a first-party API with a pattern of controls based on REST or GraphQL. With front-end JavaScript frameworks in order to move beyond mostly a toy, you'll be accessing data through a first-party or a third-party API.

```python
import os
import re
from pathlib import Path, PosixPath

def process_markdown(content):
    parsed_data = []
    begin_header = False
    end_header = False
    for line in content:
        if line.startswith('---') and not begin_header:
            begin_header = True
        elif line.startswith('---') and begin_header:
            end_header = True
            begin_header = False

        if not end_header:
            if line.startswith('date_gmt') or \
                    line.startswith('wordpress_id') or \
                    line.startswith('wordpress_url') or \
                    line.startswith('author_login') or \
                    line.startswith('author_email') or \
                    line.startswith('author_url'):
                continue
            if line.startswith('date'):
                # change line so that it follows the ISO format
                parsed_data.append(re.sub(r'\'([^\s]*)\s([^\s]*)\s\+([^\s]*)\'', r"'\1T\2.\3Z'", line))
            else:
                parsed_data.append(line)
        else:
            parsed_data.append(line)
    return parsed_data

markdown_archive = Path('.').rglob('*.m*')
files = [x for x in markdown_archive]

for name in files:
    f = open(name, 'r')
    content = f.readlines()

    parsed_data = process_markdown(content)

    the_name = f.name
    prefix, suffix = the_name.split('.')

    write_file = open('%s.md' % prefix, 'w')
    for line in parsed_data:
        write_file.write(line)
    write_file.close()

    f.close()
```