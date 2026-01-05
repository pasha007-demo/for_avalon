# Open Interpreter: Полное руководство по использованию

## Содержание
- [CLI флаги](#cli-флаги)
- [Программное использование (Python API)](#программное-использование-python-api)
- [Файлы конфигурации](#файлы-конфигурации)
- [Примеры и рецепты](#примеры-и-рецепты)
- [OpenRouter интеграция](#openrouter-интеграция)

---

## CLI флаги

### Основные
```bash
interpreter --help                          # Показать справку
interpreter --version                       # Версия
interpreter --settings                      # Настроить параметры по умолчанию
```

### Модель и API
```bash
interpreter --model openrouter/openai/gpt-4o
interpreter --model deepseek/deepseek-v3.2-20251201
interpreter --model openrouter/anthropic/claude-3-5-haiku

interpreter --temperature 0.8              # Креативность (0.0-2.0)
interpreter --context_window 128000         # Размер контекста
interpreter --max_tokens 1024               # Макс. длина ответа

interpreter --api_base https://openrouter.ai/api/v1
interpreter --api_key YOUR_KEY
```

### Поведение
```bash
interpreter --auto_run                      # Авто-выполнение кода
interpreter --safe_mode ask                 # Безопасный режим: off/ask/auto
interpreter --loop                          # Повторять до завершения задачи
interpreter --multi_line                    # Включить многострочный ввод (```)
interpreter --plain                         # Простой текстовый вывод
```

### Локальные модели
```bash
interpreter --local                         # Локальная модель (shortcut)
interpreter --local_models                  # Открыть папку с моделями
```

### Профили
```bash
interpreter --profile fast.yaml            # Загрузить профиль
interpreter --profiles                       # Открыть папку с профилями
interpreter --reset_profile                 # Сбросить профиль
interpreter --reset_profile myprofile.yaml  # Сбросить конкретный профиль
```

### Горячие клавиши и сокращения
```bash
interpreter -y                              # То же, что --auto_run
interpreter -m gpt-4o                       # То же, что --model gpt-4o
interpreter -t 0.8                          # То же, что --temperature 0.8
interpreter -x 1024                         # То же, что --max_tokens 1024
interpreter -cw 128000                       # То же, что --context_window 128000
interpreter -v                               # То же, что --verbose
interpreter -o                               # То же, что --offline
```

### Специальные режимы
```bash
interpreter --os                            # Управление мышью/клавиатурой
interpreter --vision                         # Включить режим зрения
interpreter --server                        # Запустить как сервер
interpreter --conversations                 # Показать список диалогов
interpreter --stdin                          # Режим stdin
```

### Отладка и ограничения
```bash
interpreter --verbose                       # Подробные логи
interpreter --debug                         # Режим отладки
interpreter --max_budget 10.0               # Макс. бюджет в USD
interpreter --max_output 10000              # Макс. символов в выводе
interpreter --disable_telemetry             # Отключить телеметрию
```

---

## Программное использование (Python API)

### Базовый пример
```python
from interpreter import interpreter

# Простое сообщение
interpreter.chat("Привет! Как дела?")

# Авто-выполнение
interpreter.auto_run = True
interpreter.chat("Напиши hello world на Python и запусти")
```

### Настройка модели
```python
from interpreter import interpreter

interpreter.llm.model = "openrouter/openai/gpt-4o"
interpreter.llm.temperature = 0.8
interpreter.llm.max_tokens = 1024
interpreter.llm.context_window = 128000

interpreter.chat("Объясни квантовые вычисления")
```

### OpenRouter через Python
```python
import os
from interpreter import interpreter

os.environ["OPENROUTER_API_KEY"] = "sk-or-v1-..."
interpreter.llm.model = "openrouter/anthropic/claude-3-5-haiku"
interpreter.chat("Привет")
```

### Потоковые ответы
```python
from interpreter import interpreter

for chunk in interpreter.chat("Напиши стих", stream=True):
    print(chunk, end="", flush=True)
```

### Выполнение кода без LLM
```python
from interpreter import interpreter

interpreter.computer.run("python", code="print('Hello')")
```

### OS режим (управление компьютером)
```python
from interpreter import interpreter

interpreter.os = True
interpreter.chat("Открой браузер и найди погоду")
```

---

## Файлы конфигурации

### 1) Профили (`~/.config/open-interpreter/profiles/`)
Пример `fast.yaml`:
```yaml
model: openrouter/openai/gpt-4o-mini
temperature: 0
max_tokens: 512
auto_run: true
safe_mode: off
```

Пример `local.py`:
```python
interpreter.llm.model = "ollama/llama2"
interpreter.llm.context_window = 4096
interpreter.auto_run = False
```

### 2) Настройки по умолчанию (`~/.config/open-interpreter/settings.json`)
```json
{
  "model": "openrouter/openai/gpt-4o",
  "temperature": 0.8,
  "context_window": 128000,
  "max_tokens": 1024,
  "auto_run": false,
  "safe_mode": "ask"
}
```

Создаются через:
```bash
interpreter --settings
```

### 3) Переменные окружения
```bash
export OPENROUTER_API_KEY="sk-or-v1-..."
export OPENROUTER_API_BASE="https://openrouter.ai/api/v1"
export DISABLE_TELEMETRY="true"
```

---

## Примеры и рецепты

### 1) Быстрый режим (короткие ответы)
```bash
interpreter --profile fast
# или
interpreter --model openrouter/openai/gpt-4o-mini --max_tokens 256 --temperature 0
```

### 2) Креативный режим
```bash
interpreter --temperature 1.3 --max_tokens 2048
```

### 3) Безопасный режим
```bash
interpreter --safe_mode ask
```

### 4) Локальная модель + OpenRouter fallback
```bash
interpreter --local --model ollama/llama2
# Если не сработает, можно переключиться:
interpreter --model openrouter/openai/gpt-4o
```

### 5) Режим программиста
```bash
interpreter --auto_run --safe_mode off --model openrouter/openai/gpt-4o
```

### 6) Работа с файлами
```python
from interpreter import interpreter

interpreter.chat("Прочитай файл README.md и сделай summary")
```

### 7) Визуальный режим
```bash
interpreter --vision --model openrouter/openai/gpt-4o
# Затем можно отправлять изображения
```

### 8) Серверный режим
```bash
interpreter --server
# Запустит сервер, можно работать через API
```

---

## OpenRouter интеграция

### Что работает
- Все онлайн модели автоматически маршрутизируются через OpenRouter
- Поддерживаются `openai/`, `anthropic/`, `deepseek/` и другие провайдеры
- Автоматическая нормализация имён моделей

### Настройка
```bash
export OPENROUTER_API_KEY="sk-or-v1-..."
interpreter --model openrouter/openai/gpt-4o
```

### Примеры моделей
```bash
interpreter --model openrouter/openai/gpt-4o
interpreter --model openrouter/anthropic/claude-3-5-sonnet
interpreter --model openrouter/deepseek/deepseek-v3.2-20251201
interpreter --model openrouter/meta-llama/llama-3.1-70b-instruct
```

### Особенности
- Автоматическое снижение `max_tokens` при 402 ошибках
- Graceful error handling для недостатка кредитов
- Поддержка vision (если модель поддерживает)

---

## Советы

1) **Используйте `--settings` один раз**, чтобы не писать каждый раз флаги
2) **Создавайте профили** под разные задачи (`fast.yaml`, `creative.yaml`, `local.yaml`)
3) **Для OpenRouter** ставьте `--max_tokens 1024` или меньше, чтобы избежать 402
4) **Используйте `--auto_run`** только если доверяете коду
5) **`--safe_mode ask`** — хороший компромисс между безопасностью и удобством

---

## Полный список всех флагов

```
--profile, -p           Профиль
--custom_instructions, -ci  Кастомные инструкции
--system_message, -sm   Системное сообщение
--auto_run, -y          Авто-выполнение кода
--no_highlight_active_line, -nhl  Отключить подсветку активной строки
--verbose, -v           Подробные логи
--model, -m             Модель
--temperature, -t       Температура
--llm_supports_vision, -lsv  Поддержка зрения
--llm_supports_functions, -lsf  Поддержка функций
--context_window, -cw   Контекстное окно
--max_tokens, -x        Макс. токенов
--max_budget, -b        Макс. бюджет
--api_base, -ab         API base URL
--api_key, -ak          API ключ
--api_version, -av      API версия
--max_output, -xo       Макс. вывод
--loop                  Повторять до завершения
--disable_telemetry, -dt Отключить телеметрию
--offline, -o           Офлайн-режим
--speak_messages, -sp   Озвучивать сообщения (Mac)
--safe_mode, safe       Безопасный режим: off/ask/auto
--debug, debug          Режим отладки
--fast, -f              Быстрый профиль
--multi_line, -ml       Многострочный ввод
--local, -l             Локальная модель
--codestral             Профиль Codestral
--assistant             Профиль Assistant
--llama3                Профиль Llama3
--groq                  Профиль Groq
--vision, -vi           Режим зрения
--os, os                Режим управления компьютером
--reset_profile         Сбросить профиль
--profiles              Открыть папку профилей
--local_models          Открыть папку локальных моделей
--conversations         Показать диалоги
--server                Серверный режим
--version               Версия
--contribute_conversation  Поделиться диалогом
--plain, -pl            Простой вывод
--stdin, -s             Режим stdin
--settings              Настроить параметры по умолчанию
```

---

**Готово! Теперь у тебя есть полное руководство по использованию Open Interpreter.**
