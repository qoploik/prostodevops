### SSH ключи, конфиги, безопасность

ssh-keygen -t ed25519 -C "andrey@laptop" # t=type C=comment

ssh-copy-id whoami@hostname # askes for pw transport pk into ~/.ssh servers folder

###vim /.ssh/config #make aliases for access
without config every entry would be: ssh -p [port] -i [~/.ssh/work-key] whoami@hostname
Host <name> #name for a server
    HostName hostname
    User whoami
    Port [port]
    IdentityFile [~/.ssh/work-key/] #pk to access

Host * #apply for all others
    ServerAliveInterval 60

###transport and copy files
scp <filename> <name>:<path>
scp -r #folder

rsync -av --dry-run <foldername> <name>:<path>

###bastion
ssh -J #JUMP - for connection
    ProxyJump <server_name> #~/.ssh/config

###ssh tunnels
-L #достучаться до базы, закрытой снаружи
-R #показать коллеге сайт, запущенный на ноутбуке
-D #открыть в браузере внутренние страницы компании

ssh -fN -L 5432:localhost:5432 db-01 #туннель, без командной строки на сервере -N, а чтобы ssh в фоне -f

    ControlMaster auto #leave 1-st connection opened with next one no need of repeat entry_command
    ControlPath ~/.ssh/sockets/%r@%h:%p #r - user; h - server; p - port
    ControlPersist 10m

### masks
. <- any symbol
* <- any symbol, any quantity
+ <- previous symbol, any quantity
? <- previous symbol, one or none
^ # <- begiinng / end of line
[abc] [0-9] #one symbol from a range
[^abc] #one symbol NOT from a range
(error|fatal) #group with options
{6} <- 6 repeats
{2,4} <- from 2 to 4 repeats
\. <- for a dot symbol

###sed | grep | awk ###ПОВТОРИТЬ
-o <-full line
-u <-removes repeates
for grep: grep --colour
for find: -o=or !=no

### BASH - скриптинг
$1, $2, $3	первый, второй, третий аргумент
$0	имя самого скрипта
$#	сколько аргументов передано
$@	все аргументы

Проверка	Когда верна
[ -f "$path" ]	есть обычный файл
[ -d "$path" ]	есть папка
[ -s "$path" ]	файл есть и не пустой
[ ! -d "$path" ]	знак ! переворачивает проверку: папки нет
[ -z "$s" ], [ -n "$s" ]	строка пустая, строка не пустая
[ "$a" = "$b" ], [ "$a" != "$b" ]	строки равны, не равны
[ "$a" -eq "$b" ], -ne	числа равны, не равны
[ "$a" -lt "$b" ], -gt, -le, -ge	меньше, больше, не больше, не меньше

в Bash есть две записи. 
[[ … ]] – расширенная проверка: в ней работают &&, || и шаблоны вроде [[ $file == *.log ]] #* 
(( … )) – арифметика
