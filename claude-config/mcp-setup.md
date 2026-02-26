# Настройка MCP-серверов в Claude Code

## Файл конфигурации

Глобальные MCP-серверы прописываются в файле:

```
C:\Users\<username>\.claude.json
```

Это главный конфиг Claude Code CLI. Помимо служебных полей, он содержит ключ `mcpServers`, в котором перечислены все подключённые MCP-серверы.

## Структура

Внутри `.claude.json` найди (или создай) ключ `mcpServers` на верхнем уровне:

```jsonc
{
  // ...другие поля (numStartups, installMethod и т.д.)...

  "mcpServers": {
    "имя-сервера": {
      // конфигурация сервера
    }
  }
}
```

## Форматы подключения

### stdio (через npx)

Самый распространённый вариант — сервер запускается как дочерний процесс:

```json
"имя-сервера": {
  "command": "cmd",
  "args": ["/c", "npx", "-y", "package-name@latest"]
}
```

С переменными окружения:

```json
"имя-сервера": {
  "type": "stdio",
  "command": "cmd",
  "args": ["/c", "npx", "package-name"],
  "env": {
    "API_KEY": "your-key-here"
  }
}
```

> На Windows используй `"command": "cmd"` и `"args": ["/c", "npx", ...]`.
> На macOS/Linux — `"command": "npx"` и `"args": ["-y", "package-name@latest"]`.

### HTTP (удалённый или локальный сервер)

Если MCP-сервер уже запущен и слушает HTTP:

```json
"имя-сервера": {
  "type": "http",
  "url": "http://127.0.0.1:3846/mcp"
}
```

## Пример полной конфигурации

```jsonc
{
  "mcpServers": {
    "context7": {
      "type": "stdio",
      "command": "cmd",
      "args": ["/c", "npx", "@upstash/context7-mcp"],
      "env": {
        "CONTEXT7_API_KEY": "ctx7sk-..."
      }
    },
    "shadcn": {
      "command": "cmd",
      "args": ["/c", "npx", "-y", "shadcn@latest", "mcp"]
    },
    "chrome-devtools": {
      "command": "cmd",
      "args": ["/c", "npx", "-y", "chrome-devtools-mcp@latest"]
    },
    "next-devtools": {
      "command": "cmd",
      "args": ["/c", "npx", "-y", "next-devtools-mcp@latest"]
    },
    "vibe-annotations": {
      "type": "http",
      "url": "http://127.0.0.1:3846/mcp"
    }
  }
}
```

## Как добавить новый сервер

1. Открой `C:\Users\<username>\.claude.json` в любом редакторе.
2. Найди ключ `"mcpServers"`.
3. Добавь новый объект внутрь, через запятую после последнего сервера.
4. Сохрани файл.
5. Перезапусти Claude Code (`/exit` → запустить заново), чтобы изменения подхватились.

## Как удалить сервер

Удали соответствующий ключ из `mcpServers` и перезапусти Claude Code.

## Проверка

После перезапуска Claude Code подключённые серверы отображаются в статусной строке. Их инструменты становятся доступны через `ToolSearch` с префиксом `mcp__имя-сервера__`.
