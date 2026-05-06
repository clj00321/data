# librechat_scrapy

Scraper specification for generating `librechat_scrapy.xlsx` from the LibreChat documentation.

## Output

**File:** `librechat_scrapy.xlsx`  
**Sheet:** `env`

## Column Mapping

| Column | Name | XPath | Source |
|--------|------|-------|--------|
| A | `ENV` | `/html/body/main/div/article/h1/text()` | [LibreChat dotenv docs](https://www.librechat.ai/docs/configuration/dotenv) |
| B | `ENV_Heading` | `/html/body/main/div/article/div[2]/h2/a/text()` | [LibreChat dotenv docs](https://www.librechat.ai/docs/configuration/dotenv) |
| C | `ENV_Heading_URL` | `/html/body/main/div/article/div[2]/h2/a/@href` | [LibreChat dotenv docs](https://www.librechat.ai/docs/configuration/dotenv) |
| D | `ENV_Subheading` | `/html/body/main/div/article/div[2]/h3/a/text()` | [LibreChat dotenv docs](https://www.librechat.ai/docs/configuration/dotenv) |
| E | `ENV_Subheading_URL` | `/html/body/main/div/article/div[2]/h3/a/@href` | [LibreChat dotenv docs](https://www.librechat.ai/docs/configuration/dotenv) |
| F | `ENV_Resume` | `/html/body/main/div/article/div[2]/ul/text()` | [LibreChat dotenv docs](https://www.librechat.ai/docs/configuration/dotenv) |
| G | `ENV_Key` | `/html/body/main/div/article/div[2]/div/table/thead/tr/th[1]/text()` | [LibreChat dotenv docs](https://www.librechat.ai/docs/configuration/dotenv) |
| H | `ENV_Type` | `/html/body/main/div/article/div[2]/div/table/thead/tr/th[2]/text()` | [LibreChat dotenv docs](https://www.librechat.ai/docs/configuration/dotenv) |
| I | `ENV_Description` | `/html/body/main/div/article/div[2]/div/table/thead/tr/th[3]/text()` | [LibreChat dotenv docs](https://www.librechat.ai/docs/configuration/dotenv) |
| J | `ENV_Example` | `/html/body/main/div/article/div[2]/div/table/thead/tr/th[4]/text()` | [LibreChat dotenv docs](https://www.librechat.ai/docs/configuration/dotenv) |

## Script

**Generate:** `librechat_scrapy.py`
