# librechat_scrapy

Scraper specification for generating `librechat_scrapy.xlsx` from the LibreChat documentation.

## Output

**File:** `librechat_scrapy.xlsx`  
**Sheet:** `env`

## Column Mapping

| Column | Name | XPath | Source |
| --- | --- | --- | --- |
| A | `ENV` | `/html/body/main/div/article/h1/text()` | [LibreChat.ai](https://www.librechat.ai/docs/configuration/dotenv) |
| B | `ENV_Heading` | `/html/body/main/div/article/div[2]/h2/a/text()` | [LibreChat.ai](https://www.librechat.ai/docs/configuration/dotenv) |
| C | `ENV_Heading_URL` | `/html/body/main/div/article/div[2]/h2/a/@href` | [LibreChat.ai](https://www.librechat.ai/docs/configuration/dotenv) |
| D | `ENV_Subheading` | `/html/body/main/div/article/div[2]/h3/a/text()` | [LibreChat.ai](https://www.librechat.ai/docs/configuration/dotenv) |
| E | `ENV_Subheading_URL` | `/html/body/main/div/article/div[2]/h3/a/@href` | [LibreChat.ai](https://www.librechat.ai/docs/configuration/dotenv) |
| F | `ENV_Resume` | `/html/body/main/div/article/div[2]/ul/text()` | [LibreChat.ai](https://www.librechat.ai/docs/configuration/dotenv) |
| G | `ENV_Key` | `/html/body/main/div/article/div[2]/div/table/thead/tr/th[1]/text()` | [LibreChat.ai](https://www.librechat.ai/docs/configuration/dotenv) |
| H | `ENV_Type` | `/html/body/main/div/article/div[2]/div/table/thead/tr/th[2]/text()` | [LibreChat.ai](https://www.librechat.ai/docs/configuration/dotenv) |
| I | `ENV_Description` | `/html/body/main/div/article/div[2]/div/table/thead/tr/th[3]/text()` | [LibreChat.ai](https://www.librechat.ai/docs/configuration/dotenv) |
| J | `ENV_Example` | `/html/body/main/div/article/div[2]/div/table/thead/tr/th[4]/text()` | [LibreChat.ai](https://www.librechat.ai/docs/configuration/dotenv) |

## Script

**Generate:** `librechat_scrapy.py`

```bash
pip install requests lxml openpyxl
python librechat_scrapy.py
```

```python
#!/usr/bin/env python3
"""
librechat_scrapy.py
Scrapes https://www.librechat.ai/docs/configuration/dotenv and writes
librechat_scrapy.xlsx (sheet: env) with columns A–J per README spec.
"""

import sys
import requests
from lxml import html
from openpyxl import Workbook
from openpyxl.styles import Font, PatternFill, Alignment, Border, Side
from openpyxl.utils import get_column_letter

URL = "https://www.librechat.ai/docs/configuration/dotenv"
OUT = "librechat_scrapy.xlsx"

HEADERS = {
    "User-Agent": (
        "Mozilla/5.0 (Windows NT 10.0; Win64; x64) "
        "AppleWebKit/537.36 (KHTML, like Gecko) "
        "Chrome/124.0.0.0 Safari/537.36"
    )
}


def fetch(url: str) -> html.HtmlElement:
    r = requests.get(url, headers=HEADERS, timeout=30)
    r.raise_for_status()
    return html.fromstring(r.content)


def scrape(tree: html.HtmlElement, base_url: str) -> list[dict]:
    # Page title → column A
    env_title = (tree.xpath("/html/body/main/div/article/h1/text()") or ["Environment Variables"])[0].strip()

    article = tree.xpath("/html/body/main/div/article/div[2]")
    if not article:
        # fallback: find article any way
        article = tree.xpath("//article")
    if not article:
        sys.exit("Could not locate article element — page structure may have changed.")
    article = article[0]

    rows = []
    current_h2_text = ""
    current_h2_href = ""
    current_h3_text = ""
    current_h3_href = ""
    current_resume = ""

    def abs_url(href: str) -> str:
        if href.startswith("http"):
            return href
        if href.startswith("#"):
            return base_url + href
        return base_url + "/" + href.lstrip("/")

    for el in article:
        tag = el.tag.lower() if isinstance(el.tag, str) else ""

        if tag == "h2":
            anchors = el.xpath(".//a")
            current_h2_text = "".join(el.itertext()).strip()
            current_h2_href = abs_url(anchors[0].get("href", "")) if anchors else ""
            current_h3_text = ""
            current_h3_href = ""
            current_resume = ""

        elif tag == "h3":
            anchors = el.xpath(".//a")
            current_h3_text = "".join(el.itertext()).strip()
            current_h3_href = abs_url(anchors[0].get("href", "")) if anchors else ""
            current_resume = ""

        elif tag == "ul":
            texts = [li.text_content().strip() for li in el.xpath(".//li") if li.text_content().strip()]
            current_resume = " | ".join(texts)

        elif tag == "p":
            text = el.text_content().strip()
            if text and not current_resume:
                current_resume = text[:200]

        elif tag == "div":
            # Look for tables inside divs
            for table in el.xpath(".//table"):
                thead = table.xpath(".//thead/tr")
                tbody = table.xpath(".//tbody/tr")
                if not tbody:
                    continue
                for tr in tbody:
                    cells = tr.xpath(".//td")
                    if len(cells) < 2:
                        continue
                    env_key  = cells[0].text_content().strip()
                    env_type = cells[1].text_content().strip() if len(cells) > 1 else ""
                    env_desc = cells[2].text_content().strip() if len(cells) > 2 else ""
                    env_ex   = cells[3].text_content().strip() if len(cells) > 3 else ""
                    if not env_key:
                        continue
                    rows.append({
                        "ENV":              env_title,
                        "ENV_Heading":      current_h2_text,
                        "ENV_Heading_URL":  current_h2_href,
                        "ENV_Subheading":   current_h3_text,
                        "ENV_Subheading_URL": current_h3_href,
                        "ENV_Resume":       current_resume,
                        "ENV_Key":          env_key,
                        "ENV_Type":         env_type,
                        "ENV_Description":  env_desc,
                        "ENV_Example":      env_ex,
                    })
    return rows


def write_xlsx(rows: list[dict], path: str) -> None:
    COLS = [
        ("ENV",               5),
        ("ENV_Heading",       25),
        ("ENV_Heading_URL",   50),
        ("ENV_Subheading",    30),
        ("ENV_Subheading_URL",55),
        ("ENV_Resume",        55),
        ("ENV_Key",           35),
        ("ENV_Type",          12),
        ("ENV_Description",   60),
        ("ENV_Example",       45),
    ]

    wb = Workbook()
    ws = wb.active
    ws.title = "env"

    hdr_font  = Font(name="Arial", bold=True, color="FFFFFF", size=10)
    hdr_fill  = PatternFill("solid", start_color="1F4E79")
    hdr_align = Alignment(horizontal="center", vertical="center", wrap_text=True)
    thin      = Side(style="thin")
    border    = Border(left=thin, right=thin, top=thin, bottom=thin)

    for col_idx, (name, width) in enumerate(COLS, 1):
        c = ws.cell(row=1, column=col_idx, value=name)
        c.font = hdr_font
        c.fill = hdr_fill
        c.alignment = hdr_align
        c.border = border
        ws.column_dimensions[get_column_letter(col_idx)].width = width
    ws.row_dimensions[1].height = 22

    even_fill = PatternFill("solid", start_color="DCE6F1")
    odd_fill  = PatternFill("solid", start_color="FFFFFF")
    dat_font  = Font(name="Arial", size=9)
    dat_align = Alignment(vertical="top", wrap_text=True)

    for row_idx, row in enumerate(rows, 2):
        fill = even_fill if row_idx % 2 == 0 else odd_fill
        for col_idx, (name, _) in enumerate(COLS, 1):
            c = ws.cell(row=row_idx, column=col_idx, value=row.get(name, ""))
            c.font = dat_font
            c.fill = fill
            c.alignment = dat_align
            c.border = border

    ws.freeze_panes = "A2"
    ws.auto_filter.ref = f"A1:{get_column_letter(len(COLS))}1"
    wb.save(path)


def main():
    print(f"Fetching {URL} ...")
    tree = fetch(URL)
    rows = scrape(tree, URL)
    if not rows:
        sys.exit("No rows scraped — check XPath selectors against current page structure.")
    print(f"Scraped {len(rows)} rows.")
    write_xlsx(rows, OUT)
    print(f"Written: {OUT}")


if __name__ == "__main__":
    main()
```
