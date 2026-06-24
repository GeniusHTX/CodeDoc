# RepoAgent

## Overview
RepoAgent is an automated documentation generation tool that analyzes code repositories and produces structured Markdown documentation. It supports configurable LLM backends, multi-threading, and hierarchical project parsing.

---

## Quick Start

### 1. Navigate to the main folder
```shell
cd repo_agent
```

### 2. Set your API key as an environment variable (optional, can also be passed via CLI)
```shell
export OPENAI_API_KEY=YOUR_API_KEY
```

### 3. Run RepoAgent
```shell
python main_wh.py --OPENAI_API_KEY your_api_key --model gpt-4o-mini --language Chinese
```

> All configuration parameters can be modified directly in `main_wh.py`. Default settings are shown below.

---

## Default Configuration (in `main_wh.py`)

```python
def run(
    model="gpt-4o-mini",
    temperature=0.2,
    request_timeout=60,
    base_url="https://aigptx.top/v1",
    target_repo_path="/home/david/WH/projects/simdjson",  # target project path
    hierarchy_path=".project_doc_record",
    markdown_docs_path="markdown_docs",
    ignore_list="",
    language="English",
    max_thread_count=4,
    log_level="INFO",
    print_hierarchy=False,
):
```

- **`target_repo_path`**: The absolute or relative path to the target project.
- **`markdown_docs_path`**: Output folder where all generated documentation will be saved (relative to the target repo).

---

## TALE Integration (Token-Aware LLM Estimation)

To enable **zero-shot TALE** (Token-Aware Length Estimation), you need to uncomment a small block in `repo_agent/chat_engine.py` inside the `generate_doc` method.

### Steps:
1. Open `repo_agent/chat_engine.py`
2. Locate the `generate_doc` method
3. Uncomment the following lines:

```python
# Open this block to use zero-shot TALE
# import copy
# messages_copy = copy.deepcopy(messages)
# messages_copy[-1].content = create_zero_shot_context() + messages_copy[-1].content
# token_budget = extract_number(self.llm.chat(messages_copy).message.content)

# messages[-1].content += f'use less than {token_budget}'
```

Once uncommented, the system will:
- Estimate a token budget using a zero-shot prompt
- Append a constraint like `"use less than {token_budget}"` to the final user message

This helps control output length and avoid exceeding context windows.

---

## Output
All documentation will be generated inside:
```
<target_repo_path>/<markdown_docs_path>/
```
The hierarchy and relationships between modules are also recorded in `.project_doc_record` (hidden directory).

---

## Notes
- Make sure your API key is valid and has sufficient quota.
- For large repositories, consider increasing `max_thread_count` and adjusting `request_timeout`.
- The `--language` flag supports both `Chinese` and `English` output.
