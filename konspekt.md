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

w < команда о текущих пользователях
lsmod < модули текущей Operating System

tree -d -L 2 / < делать при заходе в корень 

ls -d */ <для просмотра всех папок в текущей директории

Чек-лист
Доступ к серверу выдан аккуратно, когда:

#у каждого человека свой пользователь, а не общий на команду;
группы добавлены через usermod -aG и проверены командой id;
права root выданы через sudo точечным правилом в /etc/sudoers.d/, файл сохранён через visudo;
программы работают от своих системных пользователей с оболочкой nologin.

setuid: запуск с правами владельца файла
В Ubuntu таких программ около десятка – passwd, sudo, su, mount и несколько других. Найти все:
find / -perm -4000 -type f 2>/dev/null

#Права на сервере настроены аккуратно, когда:
секреты – пароли, ключи, .env – имеют права 600 или 640 и принадлежат программе, которая их читает;
нет файлов и папок с правами 777;
в общих папках команды стоит setgid, а права выданы группе;
список find / -perm -4000 не содержит незнакомых программ.

find / -perm -4000 -type f 2>/dev/null
/usr/bin/mount
/usr/bin/sudo.ws
/usr/bin/chfn
/usr/bin/umount
/usr/bin/gpasswd
/usr/bin/fusermount3
/usr/bin/chsh
/usr/bin/su
/usr/bin/passwd
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/openssh/ssh-keysign
/usr/lib/landscape/apt-update
/usr/lib/cargo/bin/sudo
/usr/lib/cargo/bin/s

!проверить

Чек-лист
Сервис настроен аккуратно, когда:

юнит лежит в /etc/systemd/system/, команда запуска – полным путём;
сервис работает от своего пользователя, а не от root, и перезапускается при падении;
после правки юнита выполнен daemon-reload, сервис включён через enable --now;
systemctl status показывает active (running), а в journalctl -u нет ошибок;
после перезагрузки сервера сервис поднялся сам.

Временно подключает mount, постоянно — запись в /etc/fstab
Диск указывают по UUID, уникальному номеру файловой системы, его показывает blkid. Имена вроде /dev/sdb1 могут поменяться после перезагрузки или добавления диска, а UUID остается тем же.

КОНСПЕКТ 30.10.26 ФИНАЛ МОДУЛЯ: РАЗВЕРТЫВАНИЕ ЕДЫ
1. sudo useradd -m appuser #создать юзера с домашней директорией, без -s (пароля)
1.1. sudo usermod -s /usr/sbin/nologin appuser #исправил, тк требовалось чтобы никто не мог зайти под этим пользователем -s /usr/sbin/nologin/
[2-3] easy
4. sudo chown -R appuser:appuser /srv/dostavka-eda/ #директория принадлежит группе и пользователю
5. chmod 600 #*.yaml*
6. #самое сложное#
[Unit]
Description=
After=
[Service]
User=<username>
ExecStart= dependency *.sh*
Restart=on-failure #(wake up when down)
RestartSec=5

[Install]
WantedBy=<other service>
7. 
sudo systemctl daemon-reload
sudo systemctl start dostavka
sudo systemctl enable dostavka
## проверка
systemctl is-active dostavka #active
systemctl is-enabled dostavka #enabled
8...
sudo dd if=/dev/zero of=/swapfile bs=1M count=256 status=progress #Создай файл нужного размера
sudo chmod 600 /swapfile
sudo mkswap /swapfile #Размети файл как swap-область (это создаёт на файле специальную swap-сигнатуру, похоже на mkfs, но для свопа — не файловая система, а формат подкачки)
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab #чтобы swap подключался автоматически при каждой загрузке


### Подыч · зачтено · +20 XP
### Swap-файл готов и записан в fstab. На обычном сервере его включили бы командой swapon и проверили в free -h.


kvizzez
Заведите служебного пользователя dostavka для бэкенда: с домашней папкой, но так, чтобы войти под ним было нельзя. Одна команда.
Объяснение
sudo useradd -m -s /usr/sbin/nologin dostavka: -m создаёт домашнюю папку, оболочка nologin не даёт войти. Сервису вход и не нужен – systemd запустит его от этого пользователя сам.
