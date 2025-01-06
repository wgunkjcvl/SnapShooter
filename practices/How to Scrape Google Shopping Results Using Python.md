
# How to Scrape Google Shopping Results Using Python

## Google Shopping Scraping: A Quick Guide

If you’re short on time, here’s the completed Python script to scrape Google Shopping search results:

```python
import requests
import pandas as pd

keywords = [
   'katana set',
   'katana for kids',
   'katana stand',
]

for keyword in keywords:
   products = []

   payload = {
       'api_key': 'YOUR_API_KEY',
       'query': keyword,
       'country_code': 'it',
       'tld': 'it',
       'num': '100'
   }

   response = requests.get('https://api.scraperapi.com/structured/google/shopping', params=payload)
   data = response.json()

   organic_results = data['shopping_results']
   print(len(organic_results))
   for product in organic_results:
       products.append({
           'Name': product['title'],
           'ID': product['docid'],
           'Price': product['price'],
           'URL': product['link'],
           'Thumbnail': product['thumbnail']
       })

   df = pd.DataFrame(products)
   df.to_csv(f'{keyword}.csv')
```

Before executing the code, make sure to [create a ScraperAPI account](https://bit.ly/Scraperapi) and replace `YOUR_API_KEY` with your unique API key in the `payload`.

---

## Understanding Google Shopping

Google Shopping is a platform where consumers can search, compare, and buy products from numerous vendors in one place. It features filters for price, brands, shipping options, and sales, making it a great source for scraping structured product data. 

Unlike traditional e-commerce sites, Google Shopping acts more like a directory, directing users to seller pages. This makes it an excellent source for gathering detailed product listings for competitive research or market analysis.

### Why Scraping Google Shopping Is Challenging

Google Shopping employs advanced anti-scraping measures like:

- **Rate limiting**
- **CAPTCHAs**
- **JavaScript rendering**

These techniques make scraping resource-intensive. To bypass these obstacles, a tool like ScraperAPI’s [Google Shopping endpoint](https://bit.ly/Scraperapi) is highly effective. It allows you to collect product data in JSON format seamlessly.

---

## Stop Wasting Time on Proxies and CAPTCHAs!
ScraperAPI's simple API handles millions of web scraping requests, so you can focus on the data. Get structured data from Amazon, Google, Walmart, and more.  
👉 [Start your free trial today!](https://bit.ly/Scraperapi)

---

## Scraping Google Shopping Results with Python

### Step 1: Setting Up Your Python Environment

Make sure Python 3+ is installed on your computer. Check this using:

```bash
python --version
```

Next, install the required dependencies:

```bash
pip install requests pandas
```

- **Requests**: For making HTTP requests to the API.
- **Pandas**: For organizing and exporting data into a CSV file.

---

### Step 2: Crafting Your API Payload

The Google Shopping endpoint requires a payload to retrieve data. Here’s a basic example:

```python
payload = {
   'api_key': 'YOUR_API_KEY',
   'query': 'katana set',
   'country_code': 'it',
   'tld': 'it',
   'num': '100'
}
```

The payload parameters include:
- **`api_key`**: Your ScraperAPI key.
- **`query`**: The search term (e.g., "katana set").
- **`country_code`**: The target country (e.g., `it` for Italy).
- **`tld`**: The top-level domain for the Google Shopping page.
- **`num`**: The number of results to retrieve.

---

### Step 3: Sending the API Request

To send a `GET` request to ScraperAPI’s endpoint:

```python
import requests

response = requests.get('https://api.scraperapi.com/structured/google/shopping', params=payload)
print(response.content)
```

This will return the JSON response containing product details.

---

### Step 4: Extracting Specific Data Points

From the JSON response, extract details like product name, ID, price, URL, and thumbnail:

```python
data = response.json()
organic_results = data['shopping_results']

products = []
for product in organic_results:
   products.append({
       'Name': product['title'],
       'ID': product['docid'],
       'Price': product['price'],
       'URL': product['link'],
       'Thumbnail': product['thumbnail']
   })
```

This code loops through the results and collects the desired information.

---

### Step 5: Exporting Data to CSV

Organize your extracted data using Pandas and export it as a CSV:

```python
import pandas as pd

df = pd.DataFrame(products)
df.to_csv('google-shopping-results.csv', index=False)
```

You’ll now find a neatly structured CSV file in your directory containing all the scraped data.

---

## Scraping Multiple Search Terms

To scrape multiple search terms, iterate over a list of keywords:

```python
keywords = [
   'katana set',
   'katana for kids',
   'katana stand'
]

for keyword in keywords:
   products = []
   payload['query'] = keyword

   response = requests.get('https://api.scraperapi.com/structured/google/shopping', params=payload)
   data = response.json()

   organic_results = data['shopping_results']
   for product in organic_results:
       products.append({
           'Name': product['title'],
           'ID': product['docid'],
           'Price': product['price'],
           'URL': product['link'],
           'Thumbnail': product['thumbnail']
       })

   df = pd.DataFrame(products)
   df.to_csv(f'{keyword}.csv', index=False)
```

This script dynamically creates separate CSV files for each keyword.

---

## Scaling Your Scraping Projects

Using ScraperAPI, you can scale this project effortlessly. Add as many search terms as needed and let ScraperAPI handle proxies, CAPTCHAs, and rate limits.

---

Ready to get started?  
👉 [Sign up for a free trial of ScraperAPI today!](https://bit.ly/Scraperapi)  
Use the discount code **SCRAPE9837861** to unlock additional benefits!
