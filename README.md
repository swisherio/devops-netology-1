1.	Есть скрипт:
#!/usr/bin/env python3
a = 1
b = '2'
c = a + b
•	Какое значение будет присвоено переменной c? 
```
Будет ошибка
Traceback (most recent call last):
File "<stdin>", line 1, in <module>
TypeError: unsupported operand type(s) for +: 'int' and 'str'
```

•	Как получить для переменной c значение 12? 
```
        >>> c = str(a) + b
		>>> c
		'12'
```
•	Как получить для переменной c значение 3? 
```
        >>> c = a + int(b)
		>>> c
		3
```

2.	Мы устроились на работу в компанию, где раньше уже был DevOps Engineer. Он написал скрипт, позволяющий узнать, какие файлы модифицированы в репозитории, относительно локальных изменений. Этим скриптом недовольно начальство, потому что в его выводе есть не все изменённые файлы, а также непонятен полный путь к директории, где они находятся. Как можно доработать скрипт ниже, чтобы он исполнял требования вашего руководителя?
#!/usr/bin/env python3

import os

bash_command = ["cd ~/netology/sysadm-homeworks", "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
is_change = False
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print(prepare_result)
        break
```
#!/usr/bin/env python3
import os
from os.path import expanduser
home = expanduser("~")
bash_command = ["cd ~/devops-netology/", "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print(f'{home}/devops-netology/{prepare_result}')
```

3.	Доработать скрипт выше так, чтобы он мог проверять не только локальный репозиторий в текущей директории, а также умел воспринимать путь к репозиторию, который мы передаём как входной параметр. Мы точно знаем, что начальство коварное и будет проверять работу этого скрипта в директориях, которые не являются локальными репозиториями.
```
#!/usr/bin/env python3
import os
import sys
import subprocess

git_dir = sys.argv[1]
bash_command = ["cd " + git_dir, "git status"]
os.chdir(git_dir)
with subprocess.Popen(['git', 'status'], stdout=subprocess.PIPE) as proc:
    result = proc.stdout.read().decode("utf-8")
if result.find('not') == -1:
    print('Данная директория не содержит репозитория!')
else:
  result_os =  os.popen(' && '.join(bash_command)).read()
  for result in result_os.split('\n'):
      if result.find('modified') != -1:
          prepare_result = result.replace('\tmodified:   ', '')
          print(f'{git_dir}{prepare_result}')
```
4.	Наша команда разрабатывает несколько веб-сервисов, доступных по http. Мы точно знаем, что на их стенде нет никакой балансировки, кластеризации, за DNS прячется конкретный IP сервера, где установлен сервис. Проблема в том, что отдел, занимающийся нашей инфраструктурой очень часто меняет нам сервера, поэтому IP меняются примерно раз в неделю, при этом сервисы сохраняют за собой DNS имена. Это бы совсем никого не беспокоило, если бы несколько раз сервера не уезжали в такой сегмент сети нашей компании, который недоступен для разработчиков. Мы хотим написать скрипт, который опрашивает веб-сервисы, получает их IP, выводит информацию в стандартный вывод в виде: <URL сервиса> - <его IP>. Также, должна быть реализована возможность проверки текущего IP сервиса c его IP из предыдущей проверки. Если проверка будет провалена - оповестить об этом в стандартный вывод сообщением: [ERROR] <URL сервиса> IP mismatch: <старый IP> <Новый IP>. Будем считать, что наша разработка реализовала сервисы: drive.google.com, mail.google.com, google.com.
```
#!/usr/bin/env python3

import socket
log_list = {}
host_list = ('drive.google.com', 'mail.google.com', 'google.com')
with open('2.log', 'r') as log:
    data = log.read()
for line in data.splitlines():
    (key, val) = line.split(' - ')
    log_list[key] = val
open('2.log', 'w').close()
for host in host_list:
    host_ip = socket.gethostbyname(host)
    log_host = log_list.get('<' + host + '>')
    if log_host.find(host_ip) == -1:
        print(f'[ERROR] <{host}> IP mismatch: {log_host} <{host_ip}>')
    result = '<' + host + '>' + ' - ' + '<' + host_ip + '>'
    print(result)
    with open('2.log', 'a') as log:
        log.write(result + '\n')

```
```
log_list = {}

не очень хорошо в названии использовать list
в названии сказано что это список, а на деле словарь :)
if log_host.find(host_ip) == -1: - лучше сохранять ip и проверять на равенство
```
