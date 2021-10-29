# devops-netology
##новая строка
В файле `.gitignore` созданы правила для исключения отслеживания контроля версий git.

`**/.terraform/*` - вложенные каталоги, где присутствует файл с расширением `.terraform`.

`*.tfstate` `*.tfstate.*` - любые файлы с расширением `.tfstate` в текущей директории.

`crash.log` - лог файл crash в текущей директории.

`*.tfvars` - любые файлы с расширением `.tfvars` в текущей директории.

`override.tf` `override.tf.json` `*_override.tf` `*_override.tf.json` - файлы, в название которых присутствует override и расширением `.tf` `.tf.json` в текущей директории.

`.terraformrc` `terraform.rc` - файлы с названиес terraform и расширением `.rc` `.terraformrc` в текущей директории.

