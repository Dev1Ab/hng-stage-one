# HNG-Internship Stage 2
# Intelligence Query Engine API

A backend system built for **Insighta Labs** that provides advanced filtering, sorting, pagination, and natural language querying over demographic profile data.

---

# Features

- Advanced filtering (gender, age, country, probability ranges)
- Sorting (age, created_at, gender_probability)
- Pagination (page & limit support, max 50)
- Natural language query parsing (rule-based, no AI/LLMs)
- External API-based profile generation (Genderize, Agify, Nationalize)
- Standardized API response format

---

## Natural Language Search

`GET /api/profiles/search?q=`

Converts plain English into structured filters using a rule-based parsing system (**NO AI / LLM used**).

---

## Example Requests

**Young males from Nigeria**  
`/api/profiles/search?q=young males from nigeria`

**Females above 30**  
`/api/profiles/search?q=females above 30`

**Adult males from Kenya**  
`/api/profiles/search?q=adult males from kenya`

---

## Parsing Rules

| Phrase        | Mapping              |
|--------------|----------------------|
| young        | age 16–24           |
| above X      | age >= X            |
| below X      | age <= X            |
| male         | gender = male       |
| female       | gender = female     |
| teenager     | age_group = teenager |
| adult        | age_group = adult   |
| child        | age_group = child   |
| senior       | age_group = senior  |
| country name | mapped to ISO code  |

---

## Response Format

```json
{
  "status": "success",
  "page": 1,
  "limit": 10,
  "total": 120,
  "data": []
}
```

Error Response
```json
{
  "status": "error",
  "message": "Unable to interpret query"
}
```

---
## Natural Language Parsing Limitations

The parser is rule-based and has the following limitations:

### 1. Multi-intent queries
Queries like "male and female teenagers above 17" require combining multiple filters. The system handles gender conflicts by not applying gender filters when both are present.

### 2. Ambiguity in wording
Words like "young" are strictly mapped to 16–24 age range and do not adapt based on context.

### 3. Sequential parsing limitation
Filters are applied independently and then combined, meaning complex sentence structures are not deeply parsed.

### 4. Country mapping limitation
Only predefined countries are supported via a static dictionary.

```json
COUNTRY_MAP = {
    "nigeria": "NG",
    "kenya": "KE",
    "angola": "AO",
}
```
      
- Only countries included in the `COUNTRY_MAP` are supported
- Queries outside this list cannot be interpreted