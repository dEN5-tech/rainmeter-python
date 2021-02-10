rainmeter-python
================

Первоначально созданный [jblume](https://github.com/jblume), ~~сейчас~~ затем поддерживается [glitchassassin](https://github.com/glitchassassin).

Плагин для Rainmeter, позволяющий писать скрипты Python 3.4 / 3.5

Installation
------------

All Releases: [![Github All Releases](https://img.shields.io/github/downloads/glitchassassin/rainmeter-python/total.svg)](https://github.com/glitchassassin/rainmeter-python/releases)

Разархивируйте DLL для своей версии Python и скопируйте ее в папку «Plugins» вашей установки Rainmeter.

Чтобы этот плагин работал, вам необходимо установить дистрибутив Python, соответствующий архитектуре вашего Rainmeter (Win32 или x64). Если сомневаетесь, используйте Python x64.
Соответствующий «python3x.dll» должен находиться в пути поиска вашей DLL; все стандартные установщики Python 3 автоматически помещают DLL в каталог System32, так что обычно так и должно быть. Если вы видите «Ошибка 126» в журналах Rainmeter при попытке загрузить плагин, вероятно, это ваша проблема.

Пример (простой)
-------
```ini
[Measure]
Measure=Plugin
Plugin=Python
PythonHome=c:\Python35-x64
ScriptPath=default.py
ClassName=Measure
UpdateDivider=1
```

```python
class Measure:
  def Reload(self, rm, maxValue):
    rm.RmLog(rm.LOG_NOTICE, "Reload called")

  def Update(self):
    return 1.0

  def GetString(self):
    return 'Test'

  def ExecuteBang(self, args):
    pass

  def Finalize(self):
    pass
```


Пример (количество непрочитанных писем IMAP)
-------
```ini
[Measure]
Measure=Plugin
Plugin=Python
ScriptPath=IMAP.py
PythonHome=c:\Python35-x64
ClassName=Measure
UpdateDivider=60
Username=username
Password=password
Host=mail.com
```

```python
import imaplib

class Measure:
    def Reload(self, rm, maxValue):
        self.host = rm.RmReadString('Host', 'example.com', False)
        self.username = rm.RmReadString('Username', 'user', False)
        self.password = rm.RmReadString('Password', 'pass', False)

    def Update(self):
        con = imaplib.IMAP4(self.host)
        con.starttls()
        con.login(self.username, self.password)
        con.select('INBOX', True)
        _, msgnums = con.search(None, '(UNSEEN)')
        con.close()
        con.logout()
        return float(len(msgnums[0].split()))
```
