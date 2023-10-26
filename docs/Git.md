---
sidebar_position: 4
---

# Git

```sh title="Посмотреть версию гита"
git –version
```

```sh title="Настройка пользователя гита"
git config [--global] user.name dev-ewp
git config [--global] user.email dev-ewp@yandex.ru
```

(должны совпадать с данными на гитхабе/лабе и т.д.), а то не будет зеленых квадратиков в contributions

```sh title="Клонирование репозитория"
git clone git@github.com:dev-ewp/ewp.sitemap.git [folder]
```

`folder` - название папки с проектом. Если не указывать, то возьмется из названия репозитория

```sh title="Инициализация подмодулей"
git submodule init
```

```sh title="Обновление подмодулей"
git submodule update
```

```sh title="Клонирование подмодуля"
git submodule add git@github.com:dev-ewp/ewp.sitemap.git [assets]
```

`assets` - название папки с проектом. Если не указывать, то возьмется из названия репозитория
