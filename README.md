
# 🎙️ OpenClaw Talk Analyze

[![Python](https://img.shields.io/badge/python-3.11%2B-blue.svg)](https://www.python.org/)
[![Ollama](https://img.shields.io/badge/ollama-gemma4%3A31b--cloud-black.svg)](https://ollama.com/)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](#лицензия)

**CLI-инструмент для автоматического анализа B2B-транскриптов** разговоров менеджеров-интегратора Saby. Читает `.txt`-диалоги, отправляет их в облачную модель `gemma4:31b-cloud` через Ollama и сохраняет результат в структурированные Markdown-таблицы.

---

## 📑 Содержание

- [Что делает](#-что-делает)
- [Стек](#-стек)
- [Структура проекта](#-структура-проекта)
- [Быстрый старт](#-быстрый-старт)
- [Использование](#-использование)
- [Просмотр результатов](#-просмотр-результатов)
- [Известные подводные камни](#-известные-подводные-камни)
- [Ограничения MVP](#-ограничения-mvp)
- [Возможные доработки](#-возможные-доработки)
- [Лицензия](#-лицензия)

---

## 🎯 Что делает

CLI-скрипт `analyze.py`:

1. Читает транскрипты разговоров менеджеров из папки `conversation/`.
2. Отправляет каждый диалог в облачную модель `gemma4:31b-cloud` (через Ollama).
3. Сохраняет результат в Markdown-таблицы с полями:
   - **Итог разговора**
   - **Следующий согласованный шаг**
   - **Дата следующего действия**
   - **Потребности клиента**
   - **Основные риски**
   - **Возможные ошибки менеджера**
   - **Что требует внимания руководителя**

На выходе — по одному `.md`-файлу на каждый диалог + сводный отчёт `summary_report.md`.

---

## 🛠 Стек

- **Python 3.11+** (проверено на 3.14)
- **`ollama`** — Python-клиент для обращения к локальному/облачному серверу Ollama
- **Модель:** `gemma4:31b-cloud` (облачная, через Ollama)
- **Без БД, без веб-сервера** — CLI + Markdown

---

## 📁 Структура проекта

```
openclaw-talk analyze/conversation/     ← рабочая папка
│
├── analyze.py                          ← скрипт анализа
├── prompt.txt                          ← шаблон промпта с {{TRANSCRIPT}}
│
├── conversation/                       ← ВХОДНАЯ папка с диалогами
│   ├── conversation1.txt
│   ├── conversation2.txt
│   ├── conversation3.txt
│   └── conversation4.txt
│
└── output/                             ← ВЫХОДНАЯ папка
    ├── conversation1.md
    ├── conversation2.md
    ├── conversation3.md
    ├── conversation4.md
    └── summary_report.md
```

> ⚠️ Папка `conversation/` должна быть **вложена** в рабочую папку. Скрипт ищет файлы относительно текущей рабочей папки, а не относительно `analyze.py`.

---

## 🚀 Быстрый старт

### 1. Установка Ollama

```powershell
winget install Ollama.Ollama
ollama signin                       # обязательно для облачных моделей
ollama list                         # должна быть gemma4:31b-cloud
ollama run gemma4:31b-cloud "тест"  # smoke-тест
pip install -r requirements.txt
```

### 2. Установка зависимостей Python

```powershell
& C:\Users\<user>\AppData\Local\Python\pythoncore-3.14-64\python.exe -m pip install ollama
 примечание -  «замените <user> на ваше имя пользователя».
```

### 3. Подготовка `prompt.txt`

Файл должен заканчиваться плейсхолдером `{{TRANSCRIPT}}`:

```
...
Диалог:
{{TRANSCRIPT}}
```

Проверка:

```powershell
Select-String -Path "prompt.txt" -Pattern "TRANSCRIPT"
```

### 4. Запуск

```powershell
cd "C:\Users\<user>\Downloads\openclaw-talk analyze\conversation"
& C:\Users\<user>\AppData\Local\Python\pythoncore-3.14-64\python.exe analyze.py
```

Вывод:

```
Модель: gemma4:31b-cloud
Найдено файлов: 4

[1/4] conversation1.txt... ✓ 1.5s
[2/4] conversation2.txt... ✓ 1.7s
[3/4] conversation3.txt... ✓ 1.7s
[4/4] conversation4.txt... ✓ 1.9s
```

---

## 💻 Использование

### Сборка сводного отчёта

```powershell
& C:\Users\<user>\AppData\Local\Python\pythoncore-3.14-64\python.exe merge_reports.py
```

Результат: `output\summary_report.md`.

### Просмотр результатов

```powershell
notepad "output\summary_report.md"
```

Или с корректной кодировкой в консоли:

```powershell
[Console]::OutputEncoding = [System.Text.Encoding]::UTF8
Get-Content "output\summary_report.md" -Encoding UTF8
```

---

## ⚠️ Известные подводные камни

| Симптом | Причина | Решение |
|---|---|---|
| `ModuleNotFoundError: No module named 'ollama'` | Библиотека не в том Python | Установить тем же `python.exe -m pip install ollama` |
| `module 'ollama' has no attribute '__version__'` | Нет такого атрибута | Проверять через `pip show ollama` |
| `FileNotFoundError: 'prompt.txt'` | Файл не в рабочей папке / `prompt.txt.txt` | Включить показ расширений, переименовать |
| `Нет .txt файлов в conversation/` | Нет вложенной папки `conversation/` | Создать и переместить туда файлы |
| `KeyError` по плейсхолдеру | В `prompt.txt` нет `{{TRANSCRIPT}}` | Дописать плейсхолдер |
| `UnicodeDecodeError` | Файл не в UTF-8 | Пересохранить в UTF-8 |
| `bind: Only one usage of each socket address` | Сервер уже запущен | Ничего не делать |
| «Кракозябры» в консоли | PowerShell 5.1 читает UTF-8 как cp1251 | `-Encoding UTF8` или Блокнот |

---

## 🚧 Ограничения MVP

- Модель облачная — требует интернет и авторизации в Ollama.
- Нет валидации ответа модели.
- Нет ретраев для упавших файлов.
- Один шаблон `prompt.txt` на все диалоги.

## 🔮 Возможные доработки

- Параметр `--model` для быстрой смены модели.
- Экспорт в `.xlsx` через `openpyxl`.
- Автоконвертация `summary_report.md` → `.docx` через Pandoc.
- Логирование в `analyze.log`.

---

## 📜 Лицензия

MIT. Используйте свободно.
