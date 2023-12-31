# **Введение**

Все ниже описанные действия производятся на компьютере с установленным `Ubuntu 20.04`. Если у вас установлена другая ОС, то необходимо самостоятельно внести изменения на некоторых этапах.

Для выполнения работы потребуются следующие инструменты (все установщики лежат в общей папке - HW_1.1):

- **VirtualBox** - среда виртуализации, позволяет создавать и выполнять виртуальные машины;
- **Vagrant** - ПО для создания и конфигурирования виртуальной среды. В данном случае в качестве среды виртуализации используется *VMware*;
- **Git** - система контроля версий
- **Python 3.8** - ЯП для компиляции Mech (https://www.python.org/ftp/python/3.8.1/python-3.8.1-amd64.exe)
- **PyPi** - скрипт для установки Mech https://pythonru.com/baza-znanij/ustanovka-pip-dlja-python-i-bazovye-komandy



---
# **Установка ПО**

### **Vagrant**
Переходим на https://www.vagrantup.com/downloads.html выбираем соответствующую версию. 

# **Kernel update**

### **Клонирование и запуск**

Для запуска рабочего виртуального окружения необходимо зайти через браузер в GitHub под своей учетной записью и выполнить `fork` данного репозитория: git@github.com:Zagir2000/AdministratorLinux.git

После этого данный репозиторий необходимо склонировать к себе на рабочую машину. 
```
git clone git@github.com:Zagir2000/AdministratorLinux.git
```
В текущей директории появится папка с именем репозитория. В данном случае `hw1`. Ознакомимся с содержимым:
```
cd hw1
```
Запустим виртуальную машину и залогинимся:
```
vagrant up
...
==> kernel-update: Importing base box 'centos/7'...
...
==> kernel-update: Booting VM...
...
==> kernel-update: Setting hostname...

vagrant ssh
[vagrant@locale ~]$ uname -msr
Linux 3.10.0-1127.el7.x86_64 x86_64
```
Теперь приступим к обновлению ядра.

### **kernel update**


Подключаем репозиторий, откуда возьмем необходимую версию ядра.

```
sudo yum install -y http://www.elrepo.org/elrepo-release-7.0-3.el7.elrepo.noarch.rpm
```

В репозитории есть две версии ядер **kernel-ml** и **kernel-lt**. Первая является наиболее свежей стабильной версией, вторая это стабильная версия с длительной поддержкой, но менее свежая, чем первая. В данном случае ядро 5й версии будет в  **kernel-ml**.

Поскольку мы ставим ядро из репозитория, то установка ядра похожа на установку любого другого пакета, но потребует явного включения репозитория при помощи ключа ```--enablerepo```.

Ставим последнее ядро:

```
sudo yum --enablerepo elrepo-kernel install kernel-ml -y
```

### **grub update**
После успешной установки нам необходимо сказать системе, что при загрузке нужно использовать новое ядро. В случае обновления ядра на рабочих серверах необходимо перезагрузиться с новым ядром, выбрав его при загрузке. И только при успешно прошедших загрузке нового ядра и тестах сервера переходить к загрузке с новым ядром по-умолчанию. В тестовой среде можно обойти данный этап и сразу назначить новое ядро по-умолчанию. 

Обновляем конфигурацию загрузчика:
```
sudo grub2-mkconfig -o /boot/grub2/grub.cfg
```
Выбираем загрузку с новым ядром по-умолчанию:
```
sudo grub2-set-default 0
```

Перезагружаем виртуальную машину:
```
sudo reboot
```

После перезагрузки виртуальной машинызаходим в нее и выполняем:

```
uname -msr
Linux 6.5.1-1.el7.elrepo.x86_64 x86_64
```

---
