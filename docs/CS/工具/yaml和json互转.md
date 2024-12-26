---
tags:
  - no-tag
date: 2024-12-01
publish: true
---

YAML和JSON互转可以通过编程实现，这里提供一些基本的代码示例，帮助你理解如何进行转换。

```bash
pip install pyyaml
```

## YAML 转 JSON

### Python 示例
```python
import yaml
import json

# 假设这是你的YAML数据
yaml_data = """
person:
  name: John Doe
  age: 30
  married: true
  children:
    - name: Jane Doe
      age: 10
    - name: Baby Doe
      age: 2
"""

# 将YAML转换为Python字典
data = yaml.safe_load(yaml_data)

# 将Python字典转换为JSON字符串
json_data = json.dumps(data, indent=4)
print(json_data)
```

### JavaScript 示例
```javascript
const yaml = require('js-yaml');

// 假设这是你的YAML数据
const yamlData = `
person:
  name: John Doe
  age: 30
  married: true
  children:
    - name: Jane Doe
      age: 10
    - name: Baby Doe
      age: 2
`;

// 将YAML转换为JavaScript对象
const data = yaml.load(yamlData);

// 将JavaScript对象转换为JSON字符串
const jsonData = JSON.stringify(data, null, 2);
console.log(jsonData);
```

## JSON 转 YAML

### Python 示例
```python
import json
import yaml

# 假设这是你的JSON数据
json_data = """
{
  "person": {
    "name": "John Doe",
    "age": 30,
    "married": true,
    "children": [
      {
        "name": "Jane Doe",
        "age": 10
      },
      {
        "name": "Baby Doe",
        "age": 2
      }
    ]
  }
}
"""

# 将JSON字符串转换为Python字典
data = json.loads(json_data)

# 将Python字典转换为YAML字符串
yaml_data = yaml.dump(data, default_flow_style=False)
print(yaml_data)
```

### JavaScript 示例
```javascript
const yaml = require('js-yaml');
const json5 = require('json5');

// 假设这是你的JSON数据
const jsonData = `
{
  "person": {
    "name": "John Doe",
    "age": 30,
    "married": true,
    "children": [
      {
        "name": "Jane Doe",
        "age": 10
      },
      {
        "name": "Baby Doe",
        "age": 2
      }
    ]
  }
}
`;

// 将JSON字符串转换为JavaScript对象
const data = json5.parse(jsonData);

// 将JavaScript对象转换为YAML字符串
const yamlData = yaml.dump(data);
console.log(yamlData);
```

这些代码示例展示了如何在Python和JavaScript中进行YAML和JSON的互转。如果你有具体的数据需要转换，可以直接提供数据，我可以帮你进行转换。
