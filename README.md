<p align="center">
  <a href="https://www.piloterr.com"><img src="images/banner.png" alt="Zooplus Scraper API by Piloterr – scrape Zooplus data as JSON" width="100%"></a>
</p>

# Zooplus Scraper API

[![Discord](https://img.shields.io/badge/Discord-Join%20the%20community-5865F2?logo=discord&logoColor=white)](https://discord.gg/CFj3ph8aHU) [![Get API key](https://img.shields.io/badge/Piloterr-Get%20a%20free%20API%20key-111827)](https://app.piloterr.com) [![API docs](https://img.shields.io/badge/Docs-OpenAPI-0EA5E9)](https://app.piloterr.com/openapi) [![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

The **Piloterr Zooplus Scraper API** lets you extract public data from Zooplus as **clean, structured JSON** — no proxies, headless browsers or HTML parsing to maintain. Endpoints available in production:

- **Zooplus Product API** — Read one Zooplus product by id or URL, in the region of that shop.
- **Zooplus Search API** — Search a Zooplus country shop by keyword.

Use it for price monitoring, assortment tracking, competitor analysis, product research or catalog enrichment.

| Endpoint | Path | Credits | What you get |
|---|---|---|---|
| [Zooplus Product API](#zooplus-product-api) | `/v2/zooplus/product` | 1 | Read one Zooplus product by id or URL, in the region of that shop. Variants keep their own price and EAN. |
| [Zooplus Search API](#zooplus-search-api) | `/v2/zooplus/search` | 1 | Search a Zooplus country shop by keyword. Region defaults to France. Page through food, litter, and accessory cards with local prices. |

## Table of contents

- [How it works](#how-it-works)
- [Zooplus Product API](#zooplus-product-api)
- [Zooplus Search API](#zooplus-search-api)
- [Code examples in other languages](#code-examples-in-other-languages)
- [Practical use cases](#practical-use-cases)
- [Why Piloterr?](#why-piloterr)
- [FAQ](#faq)
- [Related scrapers](#related-scrapers)
- [Support](#support)

## How it works

1. **Create a free Piloterr account** and copy your API key from the [dashboard](https://app.piloterr.com).
2. **Send a `GET` (or `POST`) request** to `https://api.piloterr.com/v2/...` with the header `x-api-key: YOUR_API_KEY`.
3. **Receive structured JSON** — Piloterr handles rendering, proxies, retries and parsing.

```bash
export PILOTERR_API_KEY="your-api-key"
pip install requests        # Python
# TypeScript: Node.js 18+ (native fetch), run with `npx tsx file.ts`
```

> Every endpoint accepts both `GET` (query string) and `POST` (JSON body with the same fields).

## Zooplus Product API

Read one Zooplus product by id or URL, in the region of that shop. Variants keep their own price and EAN.

`GET | POST https://api.piloterr.com/v2/zooplus/product` · **1 credit per request** · [Full reference](https://app.piloterr.com/openapi)

### Request parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `query` | string | ✅ | A product link, a `/shop/.../{id}` path, or the product number. |
| `region` | string | — | Which country shop when query is a number or a path. `fr` is France (default). `de` is Germany. `uk` is the United Kingdom. |
| `return_page_source` | boolean | — | Set to true to get the raw HTML in result instead of the product. |

### Python example

```python
import json
import os
import requests

response = requests.get(
    "https://api.piloterr.com/v2/zooplus/product",
    headers={"x-api-key": os.environ["PILOTERR_API_KEY"]},
    params={
        "query": "705321",
    },
    timeout=60,
)
response.raise_for_status()
data = response.json()

print(json.dumps(data, indent=2, ensure_ascii=False)[:2000])
```

### TypeScript example

```typescript
type ZooplusProduct = {
  ean?: string;
  url?: string;
  unit?: string;
  brand?: string;
  price?: number;
  title?: string;
  rating?: number;
  region?: string;
  reviews?: {
    body?: string;
    date?: string;
    title?: string;
    author?: string;
    rating?: number;
    variant?: string;
  }[];
  category?: string;
  currency?: string;
  in_stock?: boolean;
  variants?: {
    ean?: string;
    sku?: string;
    unit?: string;
    price?: number;
    currency?: string;
    available?: boolean;
    unit_price?: number;
    variant_id?: string;
    description?: string;
    price_display?: string;
    unit_price_display?: string;
  }[];
  image_url?: string;
  price_max?: number;
  price_min?: number;
  product_id?: string;
  unit_price?: number;
  review_count?: number;
  price_display?: string;
  variant_count?: number;
  is_zooplus_choice?: boolean;
  price_max_display?: string;
  price_min_display?: string;
  unit_price_display?: string;
  [key: string]: unknown;
};

const params = new URLSearchParams({
  "query": "705321",
});

const response = await fetch(`https://api.piloterr.com/v2/zooplus/product?${params}`, {
  headers: { "x-api-key": process.env.PILOTERR_API_KEY! },
});
if (!response.ok) throw new Error(`Piloterr error ${response.status}: ${await response.text()}`);

const data = (await response.json()) as ZooplusProduct;

console.log(JSON.stringify(data, null, 2).slice(0, 2000));
```

<details>
<summary><b>POST variant</b></summary>

```http
POST https://api.piloterr.com/v2/zooplus/product
x-api-key: YOUR_API_KEY
Content-Type: application/json

{"query": "705321"}
```
</details>

### Output example

Example response from the API reference, abridged — real values depend on the page you scrape.

```json
{
  "ean": "8710255150550",
  "url": "https://www.zooplus.fr/shop/chats/croquettes_chat/iams_croquettes_chat/iams_adulte_croquettes_chat/705321",
  "unit": "kg",
  "brand": "IAMS",
  "price": 32.99,
  "title": "IAMS Advanced Nutrition Adult Sterilised poulet pour chat",
  "rating": 4,
  "region": "fr",
  "reviews": [
    {
      "body": "Mon chat aime beaucoup c'est croquettes. Bon rapport qualité prix.",
      "date": "2026-10-12",
      "title": "Mon chat aime beaucoup c'est croquettes",
      "author": "example",
      "rating": 5,
      "variant": "10 kg"
    }
  ],
  "category": "Chat > Croquettes pour chat > IAMS",
  "currency": "EUR",
  "in_stock": true,
  "variants": [
    {
      "ean": "8710255150543",
      "sku": "705321.1",
      "unit": "kg",
      "price": 13.49,
      "currency": "EUR",
      "available": true,
      "unit_price": 4.5,
      "variant_id": "1",
      "description": "3 kg",
      "price_display": "13,49 €",
      "unit_price_display": "4,50 € / kg"
    },
    {
      "ean": "8710255150550",
      "sku": "705321.2",
      "unit": "kg",
      "price": 32.99,
      "currency": "EUR",
      "available": true,
      "unit_price": 3.3,
      "variant_id": "2",
      "description": "10 kg",
      "price_display": "32,99 €",
      "unit_price_display": "3,30 € / kg"
    }
  ],
  "image_url": "https://media.zooplus.com/bilder/9/800/1_8710255150543_1_9.jpg",
  "price_max": 64.49,
  "price_min": 13.49,
  "product_id": "705321",
  "unit_price": 3.3,
  "review_count": 164,
  "price_display": "32,99 €",
  "variant_count": 3,
  "is_zooplus_choice": false,
  "price_max_display": "64,49 €",
  "price_min_display": "13,49 €",
  "unit_price_display": "3,30 € / kg"
}
```

### Response fields

| Field | Type | Description |
|---|---|---|
| `ean` | string |  |
| `url` | string |  |
| `unit` | string |  |
| `brand` | string |  |
| `price` | number |  |
| `title` | string |  |
| `rating` | integer |  |
| `region` | string |  |
| `reviews[]` | object[] |  |
| `reviews[].body` | string |  |
| `reviews[].date` | string |  |
| `reviews[].title` | string |  |
| `reviews[].author` | string |  |
| `reviews[].rating` | integer |  |
| `reviews[].variant` | string |  |
| `category` | string |  |
| `currency` | string |  |
| `in_stock` | boolean |  |
| `variants[]` | object[] |  |
| `variants[].ean` | string |  |
| `variants[].sku` | string |  |
| `variants[].unit` | string |  |
| `variants[].price` | number |  |
| `variants[].currency` | string |  |
| `variants[].available` | boolean |  |

## Zooplus Search API

Search a Zooplus country shop by keyword. Region defaults to France. Page through food, litter, and accessory cards with local prices.

`GET | POST https://api.piloterr.com/v2/zooplus/search` · **1 credit per request** · [Full reference](https://app.piloterr.com/openapi)

### Request parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `query` | string | ✅ | What to look for. A word, or a Zooplus search link. |
| `region` | string | — | Which country shop. `fr` is France (default, www.zooplus.fr). `de` is Germany (www.zooplus.de). `uk` is the United Kingdom (www.zooplus.co.uk). |
| `page` | number | — | Which page of results, starting at 1. Page 2 uses p=2 and returns different products. |
| `return_page_source` | boolean | — | Set to true to get the raw HTML in result instead of the product list. |

### Python example

```python
import json
import os
import requests

response = requests.get(
    "https://api.piloterr.com/v2/zooplus/search",
    headers={"x-api-key": os.environ["PILOTERR_API_KEY"]},
    params={
        "query": "croquettes",
    },
    timeout=60,
)
response.raise_for_status()
data = response.json()

print(json.dumps(data, indent=2, ensure_ascii=False)[:2000])
```

### TypeScript example

```typescript
type ZooplusSearch = {
  results?: {
    sku?: string;
    url?: string;
    unit?: string;
    brand?: string;
    price?: number;
    title?: string;
    region?: string;
    currency?: string;
    in_stock?: boolean;
    image_url?: string;
    product_id?: string;
    unit_price?: number;
    variant_id?: string;
    price_display?: string;
    variant_count?: number;
    discount_label?: string;
    reference_price?: number;
    unit_price_display?: string;
    variant_description?: string;
    reference_price_label?: string;
    reference_price_display?: string;
  }[];
  pagination?: {
    next?: string;
    page?: number;
    per_page?: number;
    total_pages?: number;
    total_results?: number;
  };
};

const params = new URLSearchParams({
  "query": "croquettes",
});

const response = await fetch(`https://api.piloterr.com/v2/zooplus/search?${params}`, {
  headers: { "x-api-key": process.env.PILOTERR_API_KEY! },
});
if (!response.ok) throw new Error(`Piloterr error ${response.status}: ${await response.text()}`);

const data = (await response.json()) as ZooplusSearch;

console.log(JSON.stringify(data, null, 2).slice(0, 2000));
```

<details>
<summary><b>POST variant</b></summary>

```http
POST https://api.piloterr.com/v2/zooplus/search
x-api-key: YOUR_API_KEY
Content-Type: application/json

{"query": "croquettes"}
```
</details>

### Output example

Example response from the API reference, abridged — real values depend on the page you scrape.

```json
{
  "results": [
    {
      "sku": "46220.13",
      "url": "https://www.zooplus.fr/shop/chiens/croquettes_chien/eukanuba_special_breed_croquettes_chien/eukanuba_breed_nutrition_croquettes_chien/462…",
      "unit": "kg",
      "brand": "Eukanuba",
      "price": 42.49,
      "title": "Eukanuba Adult Breed Care Berger Allemand",
      "region": "fr",
      "currency": "EUR",
      "in_stock": true,
      "image_url": "https://media.zooplus.com/bilder/7/400/169996_pla_tetragmbhiams_eukanuba_adult_breedspecific_deutscherschaeferhund_12kg_hs_01_7.jpg",
      "product_id": "46220",
      "unit_price": 3.54,
      "variant_id": "13",
      "price_display": "42,49 €",
      "variant_count": 4,
      "discount_label": "Économisez -10%",
      "reference_price": 62.99,
      "unit_price_display": "3,54 €",
      "variant_description": "12 kg",
      "reference_price_label": "Prix conseillé",
      "reference_price_display": "62,99 €"
    }
  ],
  "pagination": {
    "next": "https://www.zooplus.fr/search/results?q=croquettes&p=2",
    "page": 1,
    "per_page": 48,
    "total_pages": 183,
    "total_results": 8740
  }
}
```

### Response fields

| Field | Type | Description |
|---|---|---|
| `results[]` | object[] |  |
| `results[].sku` | string |  |
| `results[].url` | string |  |
| `results[].unit` | string |  |
| `results[].brand` | string |  |
| `results[].price` | number |  |
| `results[].title` | string |  |
| `results[].region` | string |  |
| `results[].currency` | string |  |
| `results[].in_stock` | boolean |  |
| `results[].image_url` | string |  |
| `results[].product_id` | string |  |
| `results[].unit_price` | number |  |
| `results[].variant_id` | string |  |
| `results[].price_display` | string |  |
| `results[].variant_count` | integer |  |
| `results[].discount_label` | string |  |
| `results[].reference_price` | number |  |
| `results[].unit_price_display` | string |  |
| `results[].variant_description` | string |  |
| `results[].reference_price_label` | string |  |
| `results[].reference_price_display` | string |  |
| `pagination` | object |  |
| `pagination.next` | string |  |
| `pagination.page` | integer |  |

## Code examples in other languages

Ready-to-run examples live in the [`examples/`](examples) folder. Each one reads your key from the `PILOTERR_API_KEY` environment variable.

| Language | Product | Search |
|---|---|---|
| cURL (Bash) | [`product`](examples/curl/product.sh) | [`search`](examples/curl/search.sh) |
| PHP | [`product`](examples/php/product.php) | [`search`](examples/php/search.php) |
| C# | [`product`](examples/csharp/Product.cs) | [`search`](examples/csharp/Search.cs) |
| Go | [`product`](examples/go/product.go) | [`search`](examples/go/search.go) |
| Java | [`product`](examples/java/ProductExample.java) | [`search`](examples/java/SearchExample.java) |
| Ruby | [`product`](examples/ruby/product.rb) | [`search`](examples/ruby/search.rb) |
| Node.js | [`product`](examples/nodejs/product.mjs) | [`search`](examples/nodejs/search.mjs) |

## Practical use cases

- **Price monitoring** — Track prices, promotions and availability over time to react to competitors.
- **Assortment & catalog tracking** — Follow new products, delisted items and category changes.
- **Competitor analysis** — Benchmark ranges, brands and positioning across retailers.
- **Product research** — Spot best-sellers and trends from search results.
- **Catalog enrichment** — Pull titles, images and attributes into your PIM or marketplace listings.

## Why Piloterr?

- **No infrastructure** — no proxies, headless browsers or CAPTCHA solving to run yourself.
- **Structured JSON** — stable, documented fields instead of brittle HTML selectors.
- **Pay per request** — transparent credit cost per endpoint, failed requests are retried for you.
- **One API key, hundreds of sources** — Zooplus plus many other e-commerce, search and company data endpoints in the [Piloterr library](https://www.piloterr.com/library).
- **Any language** — plain HTTPS + JSON, with examples for Python, TypeScript, cURL, PHP, C#, Go, Java, Ruby and Node.js.

## FAQ

### Is it legal to scrape Zooplus?

Collecting publicly available data is generally allowed in many jurisdictions, but rules depend on your country, the data you collect and how you use it. Avoid personal data unless you have a lawful basis, respect the website's terms where they apply, and get legal advice for your specific use case. This is not legal advice.

### Do I need proxies or a headless browser?

No. Piloterr handles rendering, proxy rotation and retries on its side. You only send an HTTPS request with your `x-api-key` header and receive JSON.

### How much does a request cost?

Credits per call: `/v2/zooplus/product` **1**, `/v2/zooplus/search` **1**. See the [Piloterr website](https://www.piloterr.com) for plans.

### What data does the Zooplus API return?

Each endpoint returns structured JSON: **Zooplus Product** — `ean`, `url`, `unit`, `brand`, `price`, `title`; **Zooplus Search** — `results[]`, `results[].sku`, `results[].url`, `results[].unit`, `results[].brand`, `results[].price`.

### How do I paginate Zooplus results?

Use the `page` parameter and keep requesting while the response indicates more results.

### Can I target a specific country or language?

Yes, use `region` — see the parameter tables above for accepted values.

### Can I get the raw HTML instead of JSON?

Yes. Set `return_page_source=true` and the response becomes `{"result": "..."}` with the page source.

### Which languages are supported?

Any language that can make an HTTP request. This README shows Python and TypeScript; the [`examples/`](examples) folder contains cURL, PHP, C#, Go, Java, Ruby and Node.js.

## Related scrapers

- [About You Scraper API](https://github.com/piloterr/aboutyou-scraper)
- [Action Scraper API](https://github.com/piloterr/action-scraper)
- [Aldi Scraper API](https://github.com/piloterr/aldi-scraper)
- [Alibaba Scraper API](https://github.com/piloterr/alibaba-scraper)
- [AliExpress Scraper API](https://github.com/piloterr/aliexpress-scraper)
- [Allegro Scraper API](https://github.com/piloterr/allegro-scraper)
- [All Piloterr scrapers on GitHub](https://github.com/piloterr)

## Support

- 💬 Join the [Piloterr Discord](https://discord.gg/CFj3ph8aHU) to ask questions and share what you build.
- 📚 Browse the [API reference (OpenAPI)](https://app.piloterr.com/openapi) and the [Piloterr library](https://www.piloterr.com/library).
- 🔑 [Get your API key](https://app.piloterr.com).

---

<p align="center">
  <a href="https://www.piloterr.com">
    <picture>
      <source media="(prefers-color-scheme: dark)" srcset="images/logo-dark.svg">
      <img src="images/logo.svg" alt="Piloterr" height="32">
    </picture>
  </a>
  <br>
  <sub>Built by <a href="https://www.piloterr.com">Piloterr</a> — web scraping APIs for e-commerce, search, real estate and more, one API key.</sub>
</p>

<sub>Zooplus is a trademark of its respective owner. This repository is not affiliated with or endorsed by Zooplus. Examples are released under the MIT license.</sub>
