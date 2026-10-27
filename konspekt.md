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
