# 1C MCP Toolkit

MCP-серверы для AI-ассистентов разработчика 1С.

[![CI](https://github.com/USERNAME/1c-mcp-toolkit/actions/workflows/ci.yml/badge.svg)](https://github.com/USERNAME/1c-mcp-toolkit/actions)
[![Python](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

## Проблема

AI-ассистенты (Claude, Cursor, GPT) не видят структуру конфигурации 1С и не умеют искать по BSL-коду. Разработчик тратит часы на «где вызывается этот метод» и «какие реквизиты у справочника».

## Решение

MCP-серверы (Model Context Protocol) дают ассистенту инструменты:
- `list_objects` — список объектов метаданных по типу.
- `get_structure` — реквизиты и табличные части.
- `find_usages` — все вызовы метода по конфигурации.
- `list_methods` — методы по паттерну имени.
- `get_errors` — ошибки из журнала регистрации.

Индексирует XML-метаданные и BSL-код в SQLite FTS5.

## Установка

```bash
pip install 1c-mcp-toolkit
```

## Быстрый старт

```bash
# 1. Индексация выгрузки конфигурации
onec-mcp index --config ./config_dump --db ./mcp.db

# 2. Запуск MCP-сервера
onec-mcp serve --db ./mcp.db --transport stdio
```

## Настройка Claude Desktop

`~/Library/Application Support/Claude/claude_desktop_config.json` (macOS):
`%APPDATA%\Claude\claude_desktop_config.json` (Windows):

```json
{
  "mcpServers": {
    "1c": {
      "command": "onec-mcp",
      "args": ["serve", "--db", "/path/to/mcp.db", "--transport", "stdio"]
    }
  }
}
```

После перезапуска Claude сможет отвечать на вопросы вроде:
- «Какие реквизиты у справочника Контрагенты?»
- «Где вызывается метод ПроверитьПрава?»

## Возможности

- Парсинг XML-метаданных 1С (Catalogs, Documents, Registers).
- Индексация BSL-кода в SQLite FTS5 (unicode61).
- 5 MCP-инструментов для AI-агента.
- Транспорт: stdio + HTTP.
- CLI для индексации и запуска.

## Архитектура

```
config_dump/  →  indexer  →  SQLite (FTS5)  →  MCP Server  →  Claude
                  │             │                   │
              XML + BSL      objects, bsl       5 tools
```

## Разработка

```bash
git clone https://github.com/USERNAME/1c-mcp-toolkit
cd 1c-mcp-toolkit
pip install -e ".[dev]"
make test
```

## Лицензия

MIT
