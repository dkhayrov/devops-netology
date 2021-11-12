#Домашнее задание 3.1

8. 'HISTSIZE  Manual page bash(1) line 733'

   Переменная 'HISTCONTROL' с параметром 'ignoreboth' позволяет не записывать историю команд, которые начинались с пробела, либо команд, которые дублируют предыдущие.  

9. 'Brace Expansion Manual page bash(1) line 926'

   '{}' это конструкция, фактически, создает анонимную функцию. Функции, переменные, создаваемые во вложенных блоках кода, доступны в рамках выполняемого сценария,
    что позволяет снизить нагрузку на процессор не создавая подпроцессы для для ее выполнения.

10. 'touch file{1..100000}.txt'

    300000 файлов не получится создать из-за ограничения буфера. В моем случае удалось создать в одном сценарии только 95722 файла.

11. '[[ -d /tmp ]]' конструкция проверяет существование директории '/tmp'

12. 'mkdir /tmp/new_path_directory'
    'cd bin/bash'
    'cp bash /tmp/new_path_directory/'
    'cp bash /usr/local/bin/'
    'cd /'
    'PATH=/tmp/new_path_directory:/usr/local/sbin:/usr/local/bin:/usr/sbin:/sbin:/bin:/usr/bin:/usr/games:/usr/local/games:/snap/bin'
    'vagrant@vagrant:/$ type -a bash'
    'bash is /tmp/new_path_directory/bash'
    'bash is /usr/local/bin/bash'
    'bash is /bin/bash'
    'bash is /usr/bin/bash'

13. 'batch' отличается от 'at' тем, что планировщик выполняет задачу при определенном уровне загрузке системы в то время как 'at' выполняется в определенное время и только один раз.
