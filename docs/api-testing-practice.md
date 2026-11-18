# API Testing and Data-Driven Cases — Practical Guide

This is a **new educational guide**. The historical `web2/` files remain unchanged and must not be assumed to be valid against any current external test stand.

## A generic API contract

| Scenario | Request | Expected | What to check |
|---|---|---|---|
| Valid payload | POST fictional case | Contract-defined success | Status, schema, identifier |
| Missing required field | POST without `title` | Contract-defined 4xx | Error structure |
| Incorrect type | Integer where string required | Rejected or normalized per contract | Type handling |
| Unknown identifier | GET fictional missing resource | Contract-defined 404 | No private data leakage |
| Unauthorized call | No demo credentials | Contract-defined 401/403 | Access enforcement |
| Repeated request | Duplicate submission | Contract-defined idempotency behavior | No unintended duplicates |

Never invent a universal HTTP status for a service: confirm its documented API specification. A valid JSON body alone does not prove that the response is correct.

## Data-driven testing (DDT)

Use a small table of **invented** inputs so that one test logic can validate several outcomes.

```python
import pytest

@pytest.mark.parametrize(
    "value, expected",
    [("42", 42), ("0", 0), ("not-a-number", None)],
)
def test_parse_demo_integer(value, expected):
    try:
        actual = int(value)
    except ValueError:
        actual = None
    assert actual == expected
```

This example tests a pure function shape, not a remote API. For integration tests, prefer a local mock server or explicitly permitted sandbox, and separate response status, contract schema and business behavior assertions.

## Test evidence

Record the fixture, endpoint **category** (not a private URL), expected contract, observed response, execution date, environment and failure artifact. Replace credentials with safe placeholders and scrub logs before publication.

**RU:** DDT — это проверка одного поведения на разных синтетических параметрах. API QA проверяет HTTP-контракт, ошибки, авторизацию и идемпотентность; доступ к реальным сервисам не нужен.

Official: [pytest parametrize](https://docs.pytest.org/en/stable/how-to/parametrize.html), [HTTP semantics](https://www.rfc-editor.org/rfc/rfc9110), [Requests](https://requests.readthedocs.io/).
