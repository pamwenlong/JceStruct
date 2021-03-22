# Jce Struct

Tencent JCE Encode/Decode with fully pydantic support

## Installation

```bash
pip install JceStruct
```

or install from source (using poetry)

```bash
poetry add git+https://github.com/yanyongyu/JceStruct.git
```

or clone and install

```bash
git clone https://github.com/yanyongyu/JceStruct.git
cd JceStruct
poetry install
```

## Usage

### Create Struct

Create your struct by inheriting `JceStruct` and define your fields with `JceField`

```python
from jce import types, JceStruct, JceField


class ExampleStruct(JceStruct):
    # normal definition
    field1: types.INT32 = JceField(jce_id=1)
    # define type in options
    field2: float = JceField(jce_id=2, jce_type=types.DOUBLE)
    # define an optional field with default value
    field3: Optional[types.BOOL] = JceField(None, jce_id=3)
    # nested struct supported
    field4: types.LIST[OtherStruct] = JceField(types.LIST(), jce_id=4)
    # optional other pydantic field
    extra_pydantic_field: str = "extra_pydantic_field"
```

### Encode/Decode

```python
# simple encode decode
example: ExampleStruct = ExampleStruct(
    field1=1, field2=2., field4=types.LIST[OtherStruct()]
)
bytes = example.encode()

example: ExampleStruct = ExampleStruct.decode(bytes, extra_pydantic_field="extra")

# decode list from example struct
others: List[OtherStruct] = OtherStruct.decode_list(bytes, jce_id=3, **extra)
```

### Custom Encoder/Decoder

Just inherit JceEncoder/JceDecoder and add it to your struct configuration

```python
from jce import JceStruct, JceEncoder


class CustomEncoder(JceEncoder):
    pass


class ExampleStruct(JceStruct):

    class Config:
        jce_encoder = CustomEncoder
        # jce_decoder = CustomDecoder
```

### Custom types

Just inherit JceType and implement abstruct methods

```python
from jce import types


class CustomType(types.JceType):
    ...
```

### Change default types

By default, head bytes are treated like this:

```python
{
    0: BYTE,
    1: INT16,
    2: INT32,
    3: INT64,
    4: FLOAT,
    5: DOUBLE,
    6: STRING1,
    7: STRING4,
    8: MAP,
    9: LIST,
    10: STRUCT_START,
    11: STRUCT_END,
    12: ZERO_TAG,
    13: BYTES
}
```

field will be converted to the type defined in struct when validate.

to change it:

```python
class ExampleStruct(JceStruct):

    class Config:
        jce_default_type = {
            # add all types here
        }
```

## Command Line Usage

```bash
python -m jce 1f2e3d4c5b6a79
```
