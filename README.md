# Address formats by country

Postcode formats with validation regexes, line-by-line address templates, phone codes and national ID formats for 27 countries, as JSON and CSV.

Human-readable version with notes and sources for every country: **https://addrkit.addrkit.workers.dev/address-formats/**

Version 1.0.0 · updated 2026-10-10 · license [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)

## Files

- `address-formats.json`: metadata (`license`, `source`, `version`…) plus a `countries` array.
- `address-formats.csv`: one row per country; list fields are joined with ` | `.

## Fields (JSON, per country)

| Field | Meaning |
| --- | --- |
| `code` | ISO 3166-1 alpha-2 code |
| `country` | English name |
| `postal.name` | Local name of the postcode (ZIP Code, PLZ, CEP, PIN code, 郵便番号…) |
| `postal.format` | Human format: `N` digit, `A` letter. `null` where postcodes are not used (Hong Kong) |
| `postal.regex` | Regex source string that accepts every valid shape. Match against trimmed, upper-cased input. `null` where postcodes are not used |
| `postal.examples` | Two example postcodes |
| `postal.note` | Optional note, `{ en, zh }` |
| `order` | `small-to-large` (recipient first) or `large-to-small` (region first) |
| `addressTemplate` | Address lines top to bottom in the local convention, with placeholders |
| `latinTemplate`, `latinNote` | Layout when written in Latin letters, where it differs (JP, KR, CN, TW) |
| `phone.code` | Country calling code |
| `phone.mobileLength` | Possible lengths of a mobile national significant number (without trunk prefix) |
| `phone.e164`, `phone.local` | Example number in E.164 and in common local display |
| `regionLevel.name` | What the first-level division is called, `{ en, zh }` |
| `regionLevel.written` | Whether it is normally written in a postal address |
| `idName` | Common national ID or tax number: `name`, `format`, `length` (format and length only) |
| `sources` | Postal operator and UPU addressing references |

Placeholders: `{recipient} {building} {floor} {unit} {number} {street} {district} {city} {province} {region} {postal} {country}`.

## Summary

| Code | Country | Postcode format | Regex | Order | Phone |
| --- | --- | --- | --- | --- | --- |
| US | United States | NNNNN or NNNNN-NNNN | `^\d{5}(-\d{4})?$` | small-to-large | +1 |
| CA | Canada | ANA NAN | `^[ABCEGHJ-NPRSTVXY]\d[ABCEGHJ-NPRSTV-Z] ?\d[ABCEGHJ-NPRSTV-Z]\d$` | small-to-large | +1 |
| AU | Australia | NNNN | `^\d{4}$` | small-to-large | +61 |
| JP | Japan | NNN-NNNN | `^\d{3}-?\d{4}$` | large-to-small | +81 |
| TW | Taiwan | NNN, NNNNN or NNNNNN | `^\d{3}(\d{2,3})?$` | large-to-small | +886 |
| KR | South Korea | NNNNN | `^\d{5}$` | large-to-small | +82 |
| HK | Hong Kong | none | — | small-to-large | +852 |
| GB | United Kingdom | AN NAA, ANN NAA, AAN NAA, AANN NAA, ANA NAA, AANA NAA | `^(GIR ?0AA\|[A-PR-UWYZ](\d{1,2}\|[A-HK-Y]\d{1,2}\|\d[A-HJKPSTUW]\|[A-HK-Y]\d[ABEHMNPRV-Y]) ?\d[ABD-HJLNP-UW-Z]{2})$` | small-to-large | +44 |
| DE | Germany | NNNNN | `^(?!00)\d{5}$` | small-to-large | +49 |
| SG | Singapore | NNNNNN | `^\d{6}$` | small-to-large | +65 |
| FR | France | NNNNN | `^\d{5}$` | small-to-large | +33 |
| IT | Italy | NNNNN | `^\d{5}$` | small-to-large | +39 |
| ES | Spain | NNNNN | `^(0[1-9]\|[1-4]\d\|5[0-2])\d{3}$` | small-to-large | +34 |
| NL | Netherlands | NNNN AA | `^[1-9]\d{3} ?(?!SA\|SD\|SS)[A-Z]{2}$` | small-to-large | +31 |
| MY | Malaysia | NNNNN | `^\d{5}$` | small-to-large | +60 |
| RU | Russia | NNNNNN | `^\d{6}$` | small-to-large | +7 |
| CN | China | NNNNNN | `^\d{6}$` | large-to-small | +86 |
| TH | Thailand | NNNNN | `^[1-9]\d{4}$` | small-to-large | +66 |
| PH | Philippines | NNNN | `^\d{4}$` | small-to-large | +63 |
| AR | Argentina | ANNNNAAA (legacy: NNNN) | `^([A-HJ-NP-Z]\d{4}[A-Z]{3}\|\d{4})$` | small-to-large | +54 |
| TR | Türkiye | NNNNN | `^(0[1-9]\|[1-7]\d\|8[01])\d{3}$` | small-to-large | +90 |
| VN | Vietnam | NNNNN | `^\d{5}$` | small-to-large | +84 |
| BR | Brazil | NNNNN-NNN | `^\d{5}-?\d{3}$` | small-to-large | +55 |
| MX | Mexico | NNNNN | `^\d{5}$` | small-to-large | +52 |
| IN | India | NNNNNN | `^[1-9]\d{2} ?\d{3}$` | small-to-large | +91 |
| ID | Indonesia | NNNNN | `^[1-9]\d{4}$` | small-to-large | +62 |
| NZ | New Zealand | NNNN | `^\d{4}$` | small-to-large | +64 |

## Validate a postcode

JavaScript:

```js
const data = await fetch("https://addrkit.addrkit.workers.dev/address-formats.json").then((r) => r.json());
const byCode = Object.fromEntries(data.countries.map((c) => [c.code, c]));

function isValidPostcode(country, value) {
  const { regex } = byCode[country].postal;
  if (!regex) return value.trim() === ""; // no postcodes, e.g. HK
  return new RegExp(regex).test(value.trim().toUpperCase());
}

isValidPostcode("CA", "h3z 2y7"); // true
isValidPostcode("GB", "SW1A 1CA"); // false: C is never used in the inward code
```

Python:

```python
import json, re

with open("address-formats.json", encoding="utf-8") as f:
    data = json.load(f)
by_code = {c["code"]: c for c in data["countries"]}

def is_valid_postcode(country, value):
    regex = by_code[country]["postal"]["regex"]
    if regex is None:  # no postcodes, e.g. HK
        return value.strip() == ""
    return re.fullmatch(regex, value.strip().upper()) is not None

is_valid_postcode("NL", "1052 MA")  # True
is_valid_postcode("US", "1234")     # False
```

The regexes check the shape of a postcode, not whether it is assigned. Use the postal operator's lookup to confirm that a code exists.

## License and attribution

The data is licensed under [Creative Commons Attribution 4.0 International](https://creativecommons.org/licenses/by/4.0/) (see `LICENSE`). Please credit it as:

> Address formats dataset by AddrKit, https://addrkit.addrkit.workers.dev/address-formats/, CC BY 4.0

Corrections are welcome as issues or pull requests; please include a source from the national postal operator or the UPU.
