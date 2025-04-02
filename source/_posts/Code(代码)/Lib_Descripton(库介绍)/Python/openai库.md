---
title: openai库介绍
tags:
  - Python
date: 2025-04-02
---
以下是Python的`openai`库的详细介绍，结合参考资料和实际应用场景整理：

---

### **1. 概述**
OpenAI Python库是OpenAI官方提供的工具包，用于通过Python代码便捷访问OpenAI的各类AI模型（如GPT系列、DALL-E、Whisper等）。它封装了REST API请求，支持文本生成、图像创建、语音处理等任务。

---

### **2. 安装与版本管理**
#### **安装**
```bash
# 使用pip安装最新版（Python 3.7+）
pip install openai
```
- **版本检查**：  
  ```bash
  pip show openai  # 查看版本（示例输出：Version: 1.25.0）
  ```

#### **虚拟环境（推荐）**
```bash
# 创建并激活虚拟环境（Windows）
python -m venv venv
venv\Scripts\activate
pip install openai  # 在虚拟环境中安装
```

---

### **3. 配置API密钥**
#### **方式1：直接设置**
```python
import openai
openai.api_key = "sk-your-api-key"  # 替换为实际密钥
```

#### **方式2：环境变量（更安全）**
```bash
# 命令行设置
export OPENAI_API_KEY="sk-your-api-key"
```
```python
# 代码中自动读取环境变量
import openai
client = openai.OpenAI()  # 无需显式传入密钥
```

#### **自定义API路径**
若需代理或私有部署，需指定`base_url`并添加`/v1`后缀：
```python
from openai import OpenAI
client = OpenAI(base_url="https://your-proxy.com/v1", api_key="sk-xxx")
```

---

### **4. 基础功能示例**
#### **文本生成（ChatGPT）**
```python
response = client.chat.completions.create(
    model="gpt-3.5-turbo",
    messages=[{"role": "user", "content": "用Python写一个快速排序算法"}]
)
print(response.choices.message.content)  # 输出生成的代码
```

#### **图像生成（DALL-E）**
```python
response = client.images.generate(
    model="dall-e-3",
    prompt="赛博朋克风格的城市夜景",
    size="1024x1024"
)
print(response.data.url)  # 输出图像URL
```

---

### **5. 高级功能**
#### **流式响应（逐字输出）**
```python
stream = client.chat.completions.create(
    model="gpt-4",
    messages=[{"role": "user", "content": "写一首关于春天的诗"}],
    stream=True
)
for chunk in stream:
    print(chunk.choices.delta.content, end="")  # 逐字显示
```

#### **函数调用（结构化输出）**
```python
tools = [{
    "type": "function",
    "function": {
        "name": "get_weather",
        "parameters": {"type": "object", "properties": {"location": {"type": "string"}}}
    }
}]
response = client.chat.completions.create(
    model="gpt-3.5-turbo",
    messages=[{"role": "user", "content": "北京今天天气如何？"}],
    tools=tools
)
print(response.choices.message.tool_calls)  # 输出结构化调用参数
```

---

### **6. 应用场景**
- **智能客服**：基于ChatGPT实现自动问答  
- **内容创作**：生成文章、代码、营销文案  
- **多模态处理**：图像生成（DALL-E）、语音转文本（Whisper）  

---

### **7. 注意事项**
1. **费用控制**：API调用按Token计费，需监控用量（可通过`usage`字段查看）  
2. **数据安全**：避免传输敏感数据，遵守OpenAI数据使用政策  
3. **错误处理**：捕获`APIError`等异常：
   ```python
   try:
       response = client.chat.completions.create(...)
   except openai.APIError as e:
       print(f"API调用失败: {e.status_code}")
   ```

---

### **参考资料**
-  API配置与密钥管理  
-  安装与版本检查  
-  核心功能与案例  
-  高级用法与最佳实践