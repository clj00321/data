# XPath Documentation

**Source:** https://www.librechat.ai/docs/configuration/dotenv

## Column Mapping

| Tab | Column | ENV | XPath |
|-----|--------|-----|-------|
| XPATH | A | ENV | `/html/body/main/div/article/h1/text()` |
| XPATH | B | Section | `/html/body/main/div/article/div[2]/h2/a/text()` |
| XPATH | C | Section-URL | `/html/body/main/div/article/div[2]/h2/a/@href` |
| XPATH | D | Subsection | `/html/body/main/div/article/div[2]/h3/a/text()` |
| XPATH | E | Subsection_URL | `/html/body/main/div/article/div[2]/h3/a/@href` |
| XPATH | F | Resume | `/html/body/main/div/article/div[2]/ul/text()` |
| XPATH | G | Variable | `/html/body/main/div/article/div[2]/div/table/thead/tr/th[1]/text()` |
| XPATH | H | Type | `/html/body/main/div/article/div[2]/div/table/thead/tr/th[2]/text()` |
| XPATH | I | Description | `/html/body/main/div/article/div[2]/div/table/thead/tr/th[3]/text()` |
| XPATH | J | Example | `/html/body/main/div/article/div[2]/div/table/thead/tr/th[4]/text()` |

## Schema

| Tab | Column | Field | Description |
|-----|--------|-------|-------------|
| `ENV` | `Column A` | [ENV](/html/body/main/div/article/h1/text()) | Top-level page title |
| `ENV` | `Column B` | [Category](/html/body/main/div/article/div[2]/h2/a/text()) | Section heading |
| `ENV` | `Column C` | [Category_URL](/html/body/main/div/article/div[2]/h2/a/@href) | Section anchor URL |
| `ENV` | `Column D` | [Subheading](/html/body/main/div/article/div[2]/h3/a/text()) | Subsection heading |
| `ENV` | `Column E` | [Subheading_URL](/html/body/main/div/article/div[2]/h3/a/@href) | Subsection anchor URL |
| `ENV` | `Column F` | [Resume](/html/body/main/div/article/div[2]/ul/text()) | List item / summary text |
| `ENV` | `Column G` | [Key](/html/body/main/div/article/div[2]/div/table/thead/tr/th[1]/text()) | Table header: variable name |
| `ENV` | `Column H` | [Type](/html/body/main/div/article/div[2]/div/table/thead/tr/th[2]/text()) | Table header: type |
| `ENV` | `Column I` | [Description](/html/body/main/div/article/div[2]/div/table/thead/tr/th[3]/text()) | Table header: description |
| `ENV` | `Column J` | [Example](/html/body/main/div/article/div[2]/div/table/thead/tr/th[4]/text()) | Table header: example value |

