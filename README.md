# Primark dataset: 1,000 products in 17 countries (free sample)

A free sample of **Primark product data**: 1,000 random products from Primark's UK range, each
with its listing in every country that sells it. That is 9,391 product rows and 48,968 size rows,
so you can compare what the same product costs in pounds, euros, dollars, złoty, koruna, forint and lei.

Every row has the local price and previous price, category path, colour, sizes, materials, care instructions,
Click & Collect stock, the number of stores that range it, image URLs and the date it went on sale.

**The full dataset** (150,000+ listings across 18 Primark storefronts in 17 countries, every
product and size, with store-level ranging) and **weekly or daily refreshes**: **[cartpie.com/datasets/primark](https://cartpie.com/datasets/primark)**.

Also on [GitHub](https://github.com/votiakov/primark-dataset), [Hugging Face](https://huggingface.co/datasets/votiakov/primark-products-sample) and [Kaggle](https://www.kaggle.com/datasets/votiakov/primark-products-sample).

## Files

| File | Format | Rows |
|---|---|---|
| [`data/primark_sample_2026-10-27.csv`](data/primark_sample_2026-10-27.csv) | CSV (UTF-8, header; lists separated by `\|`) | 9,391 |
| [`data/primark_sample_2026-10-27.parquet`](data/primark_sample_2026-10-27.parquet) | Parquet (zstd) | 9,391 |
| [`data/primark_sample_sizes_2026-10-27.csv`](data/primark_sample_sizes_2026-10-27.csv) | CSV, one row per size per country | 48,968 |
| [`data/primark_sample_sizes_2026-10-27.parquet`](data/primark_sample_sizes_2026-10-27.parquet) | Parquet (zstd) | 48,968 |
| [`data/schema.json`](data/schema.json) | Field names, types and descriptions | |

## One product in every country

Essential Cargo Joggers, product `991160214528`:

| Country | Title | Price |
|---|---|---|
| GB (en-gb) | Essential Cargo Joggers | £12 |
| AT (de-at) | Cargo-Jogginghose | €16 |
| BE (fr-be) | Bas de jogging cargo Essential | €16 |
| BE (nl-be) | Basic cargo-joggingbroek | €16 |
| CZ (cs-cz) | Základní cargo tepláky | 365 CZK |
| DE (de-de) | Cargo-Jogginghose | €16 |
| ES (es-es) | Joggers cargo básicos | €14 |
| FR (fr-fr) | Bas de jogging cargo Essential | €14 |
| HU (hu-hu) | Alapdarab oldalzsebes szabadidőnadrág | 5,200 HUF |
| IE (en-ie) | Essential Cargo Joggers | €14 |
| IT (it-it) | Pantaloni da tuta cargo basic | €14 |
| NL (nl-nl) | Basic cargo-joggingbroek | €16 |
| PL (pl-pl) | Niezbędne bojówki dresowe | 60 PLN |
| PT (pt-pt) | Calças treino cargo essenciais | €14 |
| RO (ro-ro) | Pantaloni casual cargo de bază | 70 RON |
| SI (sl-si) | Osnovne hlače za prosti čas s stranskimi žepi | €14 |
| SK (sk-sk) | Základné kapsáčové tepláky | €14 |
| US (en-us) | Essential Cargo Joggers | $20 |

## Preview (UK)

| Title | Price | Was | Category | Sizes | C&C stock | Stores |
|---|---|---|---|---|---|---|
| Cotton Blend Cosy Wide Leg Joggers | £6 | £12 | Joggers | 7 | 0 | 88 |
| Disney's Stitch Fluffy Double Strap Slippers | £5 | £10 | Slippers | 3 | 1 | 84 |
| Classic Baseball Cap | £3 |  | Hats, Gloves & Scarves | 1 | 0 | 200 |
| Essential Cargo Joggers | £12 |  | Trousers | 6 | 38 | 200 |
| Snaffle Mule Loafers | £10 |  | Flats | 8 | 0 | 140 |
| 4pk Yungblud Badges | £3 |  | Jewellery | 1 | 0 | 42 |

## Quick start

```python
import pandas as pd
df = pd.read_parquet("data/primark_sample_2026-10-27.parquet")
# one product, every country
print(df[df.product_id == "991160214528"][["country", "locale", "currency", "price", "title"]])
# median price by country and top category
print(df.pivot_table(index="top_category", columns="country", values="price", aggfunc="median"))
```

```sql
-- DuckDB: sizes with Click & Collect stock, by country
SELECT country, count(*) AS sizes, avg((click_collect_stock > 0)::INT) AS share_in_stock
FROM 'data/primark_sample_sizes_2026-10-27.parquet' WHERE active
GROUP BY 1 ORDER BY sizes DESC;
```

## Product fields

| Field | Type | Description |
|---|---|---|
| `product_id` | string | Primark product ID of this colour (12 digits: 9-digit style code + 3-digit colour code). The same ID is the same product in every country. |
| `style_code` | string | Style code shared by all colours of a product. |
| `colour_code` | string | Colour code within the style. |
| `title` | string | Product title as shown to shoppers, in the storefront's language. |
| `description` | string | Short product description, in the storefront's language. |
| `product_url` | string | Product page on the country's Primark website. |
| `country` | string | ISO country code of the storefront. |
| `locale` | string | Storefront locale (language-country), e.g. en-gb, fr-be, nl-be. |
| `language` | string | Language of title, description and category names. |
| `currency` | string | ISO currency of the prices. |
| `price` | number | Current selling price in local currency, as shown to shoppers. |
| `original_price` | number | Previous (crossed-out) price when the product is reduced; empty otherwise. |
| `discount_pct` | number | 1 - price / original_price, rounded to 4 decimals; 0 when not reduced. |
| `clearance` | boolean | True when the product is marked as clearance. |
| `top_category` | string | Top-level category, e.g. Women, Kids, Home, Beauty. |
| `category` | string | Leaf category, e.g. Jeans. |
| `category_path` | string | Main category path from top level to leaf, separated by ' > '. |
| `category_paths` | list of strings | Every category path the product is listed under (in CSV: separated by '|'). |
| `gender` | string | Department: Womens, Mens, Girls, Boys, Unisex (empty for home and some beauty). |
| `product_type` | string | Product type, e.g. Hair Accessories, Pyjama Sets. |
| `colour` | string | Colour name. |
| `colour_count` | integer | Number of colours the style comes in. |
| `sizes` | list of strings | Sizes offered (in CSV: separated by '|'). |
| `size_count` | integer | Number of sizes offered. |
| `materials` | list of strings | Materials, without percentages (in CSV: separated by '|'). |
| `care` | list of strings | Care instructions (in CSV: separated by '|'). |
| `availability` | string | How it can be bought: click_collect_and_store, store_only, click_collect_only or coming_soon. |
| `click_collect_stock` | integer | Units available for Click & Collect, all sizes together. |
| `store_count` | integer | Number of stores that range at least one size of the product. |
| `image_url` | string | Main product image URL. |
| `image_urls` | list of strings | All product image URLs, high resolution (in CSV: separated by '|'). |
| `image_count` | integer | Number of product images. |
| `first_listed` | date | Date the product went on sale in this country; empty when not known. |
| `collected_at` | timestamp | When this record was collected (UTC). |

## Size fields

| Field | Type | Description |
|---|---|---|
| `product_id` | string | Primark product ID of the colour (joins to the products file). |
| `sku` | string | Primark SKU of this size. |
| `country` | string | ISO country code of the storefront. |
| `locale` | string | Storefront locale. |
| `size` | string | Size label as shown, e.g. 'uk 10', 'one size'. |
| `currency` | string | ISO currency of the prices. |
| `price` | number | Current selling price of this size. |
| `original_price` | number | Previous (crossed-out) price when reduced; empty otherwise. |
| `click_collect_stock` | integer | Units of this size available for Click & Collect. |
| `active` | boolean | False when the size is no longer sold. |
| `availability` | string | click_collect_and_store, store_only, click_collect_only or coming_soon. |
| `store_count` | integer | Number of stores that range this size. |
| `collected_at` | timestamp | When this record was collected (UTC). |

## Notes

- `product_id` is the same product in every country, so countries join on it. Belgium has two storefronts (French and Dutch).
- Prices are what shoppers in each country see, in local currency, including VAT where it applies.
- Many Primark products are sold in stores only (`availability` = `store_only`); their price and ranging are still listed.
- `store_count` counts stores that range a product or size. It is not store stock.
- Image URLs link to Primark's image CDN. No image files are included.
- Independent data. Not affiliated with, endorsed by or sponsored by Primark. Primark is a trademark of its owner.

## Terms

Free to use for evaluation, research and journalism, with attribution to [cartpie.com/datasets/primark](https://cartpie.com/datasets/primark).
Not for resale or redistribution as a dataset. For commercial use and the full data, get a licence at
[cartpie.com/datasets/primark](https://cartpie.com/datasets/primark).
