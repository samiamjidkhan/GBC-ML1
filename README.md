# Article Scraper

This Python script scrapes text from a Medium article URL and saves it to a file.

## Prerequisites

- Python 3.x
- `requests` and `beautifulsoup4` libraries

Install the required libraries:

```sh
pip install requests beautifulsoup4
```

## Usage

1. Clone the repository and navigate to the directory:

```sh
git clone https://github.com/yourusername/article-scraper.git
cd article-scraper
```

2. Run the script:

```sh
python scraper.py
```

3. Enter the URL of the Medium article when prompted.

4. The extracted text will be saved in the `scraped_articles` directory.

## Example URL

Use the following Medium URL as an example:

```
https://medium.com/@subashgandyer/papa-what-is-a-neural-network-c5e5cc427c7
```

## Code Overview

```python
import os
import requests
import re
import sys
from bs4 import BeautifulSoup

def get_page():
    global url
    url = input("Enter the URL of a Medium article: ")
    if not re.match(r'https?://medium.com/', url):
        print('Please enter a valid Medium article URL')
        sys.exit(1)
    res = requests.get(url)
    res.raise_for_status()
    return BeautifulSoup(res.text, 'html.parser')

def clean(text):
    rep = {"<br>": "\n", "<br/>": "\n", "<li>": "\n"}
    rep = dict((re.escape(k), v) for k, v in rep.items())
    pattern = re.compile("|".join(rep.keys()))
    text = pattern.sub(lambda m: rep[re.escape(m.group(0))], text)
    text = re.sub('\<(.*?)\>', '', text)
    return text

def collect_text(soup):
    text = f'url: {url}\n\n'
    for para in soup.find_all('p'):
        text += f"{para.text}\n\n"
    return text

def save_file(text):
    if not os.path.exists('./scraped_articles'):
        os.mkdir('./scraped_articles')
    fname = f'scraped_articles/{url.split("/")[-1]}.txt'
    with open(fname, 'w', encoding='utf-8') as file:
        file.write(text)
    print(f'File saved in {fname}')

if __name__ == '__main__':
    text = collect_text(get_page())
    save_file(text)
```
