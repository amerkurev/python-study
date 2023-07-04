---
title: Download large file in Python with beautiful progress bar
description: "In this article, we will explore the use of the tqdm package to create beautiful progress bars in the console while downloading large files from the internet."
slug: download-large-file-in-python-with-progress-bar
date: 2023-07-04 00:00:00+0000
image: cover.jpeg
categories:
    - Packages
tags:
    - requests
    - httpx
    - tqdm
links:
  - title: Posted by amerkurev
    description: https://github.com/amerkurev
    website: https://github.com/amerkurev
    image: https://avatars.githubusercontent.com/u/28217522?v=4
---

To begin, let's look at an example of downloading a large file using the [requests](https://requests.readthedocs.io/en/latest/) package.
The **requests** package is one of the most popular packages in Python, with millions of downloads every day.
You can verify this on the website that publishes statistics on [the most downloaded PyPI packages](https://pypistats.org/top).

To run the example, you need to install the **requests** package. Here is the code:
```python
import requests

def download(url: str, filename: str):
    with open(filename, 'wb') as f:
        with requests.get(url, stream=True) as r:
            r.raise_for_status()
            for chunk in r.iter_content(chunk_size=8192):
                f.write(chunk)

download('https://speed.hetzner.de/100MB.bin', '100MB.bin')
```

By setting `stream=True` in the request, we avoid reading the entire content into memory for large responses.
The `chunk_size` parameter determines the number of bytes to read into memory at a time.

If you execute this code, you will notice that it appears to freeze in the terminal while downloading the large file.
Let's fix that!

To do this, we will install the [tqdm](https://github.com/tqdm/tqdm) package.
**tqdm** is a powerful package for creating fast, customizable progress bars in Python.
It can be used in scripts or on the [command line](https://github.com/tqdm/tqdm#module).

Let's rewrite our example using **tqdm**:
```python
import requests
import tqdm

def download(url: str, filename: str):
    with open(filename, 'wb') as f:
        with requests.get(url, stream=True) as r:
            r.raise_for_status()
            total = int(r.headers.get('content-length', 0))

            # tqdm has many interesting parameters. Feel free to experiment!
            tqdm_params = {
                'desc': url,
                'total': total,
                'miniters': 1,
                'unit': 'B',
                'unit_scale': True,
                'unit_divisor': 1024,
            }
            with tqdm.tqdm(**tqdm_params) as pb:
                for chunk in r.iter_content(chunk_size=8192):
                    pb.update(len(chunk))
                    f.write(chunk)

download('https://speed.hetzner.de/100MB.bin', '100MB.bin')
```

Now, in the terminal, you will see a progress bar indicating the download progress:
```console
https://speed.hetzner.de/100MB.bin:   5%|█████▍       | 5.47M/105M [00:03<01:01, 1.61MB/s]
```

You can customize the progress bar using the parameters provided by **tqdm**.

**tqdm** can be used not only for network-related tasks but also in many other cases.
Here is a simple example from the documentation:
```python
import tqdm

for i in tqdm.tqdm(range(int(100e6))):
    pass

# 56%|██████████▏        | 55815969/100000000 [00:05<00:03, 11312880.86it/s]
```

In conclusion, let's rewrite our example using an asynchronous approach with **asyncio**.
Instead of **requests**, we will use the [httpx](https://github.com/encode/httpx/) package, which provides an API similar to **requests** but with additional asynchronous methods.

Here is the code:
```python
import asyncio
import httpx
from tqdm import tqdm

async def download(url: str, filename: str):
    with open(filename, 'wb') as f:
        async with httpx.AsyncClient() as client:
            async with client.stream('GET', url) as r:
                r.raise_for_status()
                total = int(r.headers.get('content-length', 0))

                tqdm_params = {
                    'desc': url,
                    'total': total,
                    'miniters': 1,
                    'unit': 'B',
                    'unit_scale': True,
                    'unit_divisor': 1024,
                }
                with tqdm(**tqdm_params) as pb:
                    downloaded = r.num_bytes_downloaded
                    async for chunk in r.aiter_bytes():
                        pb.update(r.num_bytes_downloaded - downloaded)
                        f.write(chunk)
                        downloaded = r.num_bytes_downloaded

async def main():
    """
    Downloading three large files simultaneously.
    Each file has its own progress bar.
    """
    loop = asyncio.get_running_loop()
    urls = [
        ('https://speed.hetzner.de/100MB.bin', '100MB.bin'),
        ('https://speed.hetzner.de/1GB.bin', '1GB.bin'),
        ('http://ipv4.download.thinkbroadband.com/50MB.zip', '50MB.zip'),
    ]
    tasks = [loop.create_task(download(url, name)) for url, name in urls]
    await asyncio.gather(*tasks, return_exceptions=True)


asyncio.run(main())
```

When you run this code, you will see three progress bars in the terminal, each representing the progress of downloading a specific file.
```console
http://ipv4.download.thinkbroadband.com/50MB.zip:  21%|██████████████████▋    | 10.5M/50.0M [00:20<01:24, 488kB/s]
https://speed.hetzner.de/1GB.bin:   1%|▊                                      | 7.77M/0.98G [00:20<43:02, 403kB/s]
https://speed.hetzner.de/100MB.bin:   8%|████████                             | 7.75M/100M [00:20<04:00, 402kB/s]
```

The purpose of this article was to introduce the **tqdm** package and demonstrate its usage with several useful examples.
For more information, refer to the official documentation for the [requests](https://requests.readthedocs.io/en/latest/), [httpx](https://www.python-httpx.org), and [tqdm](https://tqdm.github.io) packages.
