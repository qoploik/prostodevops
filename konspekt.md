#как проверить что localhost лежит?
curl localhost
#как ребутнуть сервер?
sudo systemctl restart
#узнать статус сервиса
systemctl status site

# LINUX / основы системы
все команды чаще всего находятся в /usr/bin  -> which whoami
~ == homedirectory
$ == base user
# == root
hostname == machine name
ctrl + l == поднять текст терминала
#команды о состоянии сервера
uptime
date +%F / +%Y-%m-%d_%H-%M / +%s
uname -r / версия ядра
history
! + номер команды из history
echo "Server $(hostname), user $(whoami)"
___________PПОДСКАЗКИ
command --help
man command

ll == ls -la

nproc = количество ядер
uname -a
Linux AntonsASUS 6.6.87.2-microsoft-standard-WSL2 #1 SMP PREEMPT_DYNAMIC Thu Jun  5 18:30:46 UTC 2025 x86_64 GNU/Linux
uname -r
6.6.87.2-microsoft-standard-WSL2

#File system & navigation
etc/ - конфиги/настройки
var/ - изменяющиеся данные -> var/log
home/ - 
opt/ - сторонние приложения

alias = зашивание сокращений
tree -L 2 #проверить 2 уровня вложенности
du -> disk usage | df -> disk free
FIND / DF DU / TREE / ls >>>+флаги

FHS	Стандарт, по которому в Linux разложены папки

команды, работающие благодаря bash функционалу
#МАСКИ: * ? [abc] [a-e] {} [!abc] >> mkdir -p ~/courier/{app,config,logs}/ touch report-{1..5}.txt

rsync - для больший файлов в отличие от cp

Чтобы звёздочка находила и скрытые файлы, в bash включают настройку dotglob. Она действует, пока открыт этот терминал:
shopt -s dotglob

#как выводить в диапазоне: head -10 <file> | tail -2 || sed -n '9,10p'

#как работать с архивированием
tar czf dostavka.tar.gz /opt/dostavka   # создать сжатый архив
tar tzf dostavka.tar.gz                 # показать, что внутри
tar xzf dostavka.tar.gz                 # распаковать в текущую папку

tar xzf dostavka-2.4.0.tar.gz --strip-components=1 -C /opt/dostavka
--strip-components=1 срезает первую часть каждого пути: dostavka-2.4.0/conf/app.conf превращается в conf/app.conf
-C /opt/dostavka переходит в эту папку перед распаковкой, поэтому файл окажется в /opt/dostavka/conf/app.conf
флаг --exclude убирает из архива то, что можно пересоздать и незачем хранить: логи, кэш, временные файлы.

tar czf - -C /opt dostavka | ssh backup "cat > /backup/dostavka.tar.gz" #перенаправление с полного сервера на другой через ssh


