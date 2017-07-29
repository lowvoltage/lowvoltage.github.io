---
layout: post
title: "Yadisk Direct Download in Python"
date: 2017-07-29
categories: Python
---

A Python snippet for directly downloading Yandex.Disk "Share" links.
Specifying the target filename is optional.

Based on https://github.com/wldhx/yadisk-direct.

```python
import requests

API_ENDPOINT = 'https://cloud-api.yandex.net/v1/disk/public/resources/download?public_key={}'


def _get_real_direct_link(sharing_link):
    pk_request = requests.get(API_ENDPOINT.format(sharing_link))
    
    # Returns None if the link cannot be "converted"
    return pk_request.json().get('href')


def _extract_filename(direct_link):
    for chunk in direct_link.strip().split('&'):
        if chunk.startswith('filename='):
            return chunk.split('=')[1]
    return None


def download_yadisk_link(sharing_link, filename=None):
    direct_link = _get_real_direct_link(sharing_link)
    if direct_link:
        # Try to recover the filename from the link
        filename = filename or _extract_filename(direct_link)
        
        download = requests.get(direct_link)
        with open(filename, 'wb') as out_file:
            out_file.write(download.content)
        print('Downloaded "{}" to "{}"'.format(sharing_link, filename))
    else:
        print('Failed to download "{}"'.format(sharing_link))
```

Example:
```python
download_yadisk_link('https://yadi.sk/i/LKkWupFjr5WzR')
```
```
Downloaded "https://yadi.sk/i/LKkWupFjr5WzR" to "0391_The_Ultimate_Question_of_Programming_ru_18_04_2016.pdf"
```
