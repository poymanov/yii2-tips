# Шаблон подготовки базы для нового проекта

Данные консольные команды подготовят базу (**mysql**) для работы с новым проектом:

```bash
DROP DATABASE IF EXISTS `demo`;
CREATE DATABASE IF NOT EXISTS `demo` DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
GRANT ALL PRIVILEGES ON demouser.* TO demo@localhost IDENTIFIED BY 'demopass';
```

Эти команда должна быть выполнена через консоль mysql. 

Необходимо предварительно в неё войти:

```bash
mysql -uroot -proot
```

Эта команда удалит базу с именем **```demo```**, если такая уже существует:

```bash
DROP DATABASE IF EXISTS `demo`;
```

Эта команда создаст базу с именем **```demo```** (если она уже не существует) и установит ей кодировку utf-8:
```bash
CREATE DATABASE IF NOT EXISTS `demo` DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
```

Эта команда создаст пользователя **```demouser```** с паролем **```demopass```** и присвоит ему полный набор прав для управления базой **demo**:
```bash
GRANT ALL PRIVILEGES ON demouser.* TO demo@localhost IDENTIFIED BY 'demopass';
```

В итоге получаем данные для подключения к БД:
```
host: localhost
database: demo
user: demouser
password: demopass
```
