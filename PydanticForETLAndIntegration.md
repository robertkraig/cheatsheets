# [title:Pydantic for ETL & Integration Work]# ⚙️ Pydantic for ETL & Integration Work

> Pydantic brings strong typing, validation, and transformation to your ETL pipelines. These patterns help ensure data integrity before writing to databases or downstream systems.

---

## 1️⃣ Basic Model — Row Validation
```python
from pydantic import BaseModel, Field
from typing import Optional
from datetime import date

class Customer(BaseModel):
    id: int
    name: str
    email: Optional[str] = None
    join_date: Optional[date] = None
    active: bool = True

# Example usage - with validation
c = Customer(id=1, name="Alice", email="alice@example.com")
print(c.dict())

# ⚡ Skip validation for trusted data (faster for large datasets)
# Pydantic v2:
c_fast = Customer.model_construct(id=1, name="Alice", email="alice@example.com")

# Pydantic v1:
# c_fast = Customer.construct(id=1, name="Alice", email="alice@example.com")
```
🧠 *ETL Use:* Validate incoming records before writing to staging. Use `model_construct()` for pre-validated data to skip validation overhead.

---

## 2️⃣ Type Coercion / Auto Conversion
```python
Customer(id="42", name="Bob", active="false")
```

Control strictness using `Config`:
```python
class Customer(BaseModel):
    id: int
    name: str
    class Config:
        strict = True
```

---

## 3️⃣ Field Validation & Defaults
```python
class Order(BaseModel):
    order_id: str
    amount: float = Field(gt=0, description="must be positive")
    discount: float = 0.0
    region: str = Field(default="US", pattern="^[A-Z]{2}$")
```

```python
Order(order_id="A100", amount=0)  # raises ValidationError
```

---

## 4️⃣ Nested Models (JSON Structures)
```python
class Item(BaseModel):
    sku: str
    qty: int

class Order(BaseModel):
    id: str
    items: list[Item]
    total: float

order = Order(
    id="ORD001",
    items=[{"sku": "ABC", "qty": 5}, {"sku": "XYZ", "qty": 2}],
    total=99.99,
)
```

---

## 5️⃣ Model-Level Validation
```python
from pydantic import root_validator

class Invoice(BaseModel):
    subtotal: float
    tax: float
    total: float

    @root_validator
    def check_total(cls, values):
        if abs(values["subtotal"] + values["tax"] - values["total"]) > 0.01:
            raise ValueError("Total mismatch")
        return values
```

---

## 6️⃣ Custom Field Validators
```python
from pydantic import validator

class Employee(BaseModel):
    name: str
    email: str

    @validator("email")
    def check_email(cls, v):
        if "@" not in v:
            raise ValueError("Invalid email")
        return v.lower()
```

---

## 7️⃣ Transforming Data Before Loading
```python
class CleanProduct(BaseModel):
    name: str
    price: float

    @validator("name", pre=True)
    def strip_name(cls, v):
        return v.strip().title()

product = CleanProduct(name="   hammer  ", price="9.99")
print(product.name)  # Hammer
```

---

## 8️⃣ Pydantic + Pandas Bridge
```python
import pandas as pd
from pydantic import BaseModel, ValidationError

class Record(BaseModel):
    id: int
    name: str
    score: float

df = pd.DataFrame([
    {"id": "1", "name": "Bob", "score": "99.5"},
    {"id": "bad", "name": "Sue", "score": "N/A"}
])

valid, invalid = [], []

for _, row in df.iterrows():
    try:
        valid.append(Record(**row.to_dict()).dict())
    except ValidationError as e:
        invalid.append({"error": e.errors(), "row": row.to_dict()})

clean_df = pd.DataFrame(valid)
print(clean_df)

# ⚡ For already-validated data, skip validation (massive speedup):
# Pydantic v2:
pre_validated = [Record.model_construct(**row) for row in trusted_data]

# Pydantic v1:
# pre_validated = [Record.construct(**row) for row in trusted_data]
```

---

## 9️⃣ Schema Exports / Serialization
```python
Customer.schema_json(indent=2)
Customer.parse_obj({...})
Customer.parse_raw('{"id":1}')
```

---

## 🔟 Environment / Config Parsing
```python
from pydantic import BaseSettings

class Settings(BaseSettings):
    db_url: str
    api_key: str
    batch_size: int = 100

    class Config:
        env_file = ".env"

settings = Settings()
print(settings.db_url)
```

---

## 🧠 Pro-Tier Patterns for ETL Pipelines

| Goal | Use This |
|------|-----------|
| Validate inbound records | `BaseModel` with `Field` constraints |
| Flatten/normalize JSON payloads | Nested models + `.dict()` |
| Transform data inline | `@validator(pre=True)` |
| Enforce cross-field rules | `@root_validator` |
| Manage config | `BaseSettings` |
| Validate rows before DB insert | `try/except ValidationError` |
| Serialize schema definitions | `.schema()` or `.schema_json()` |
