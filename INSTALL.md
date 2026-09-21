powershell

# Установить библиотеку ollama ТЕМ ЖЕ интерпретатором, что будет запускать скрипт:
& C:\Users\<user>\AppData\Local\Python\pythoncore-3.14-64\python.exe -m pip install ollama

Проверка что библиотека видна:
& C:\Users\<user>\AppData\Local\Python\pythoncore-3.14-64\python.exe -c "import ollama; print('ok')"

⚠️ Не проверяйте версию через ollama.__version__ — этого атрибута может не быть.
Используйте pip show ollama или просто импорт

