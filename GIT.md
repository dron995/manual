### https://githowto.com/ru/setup
```
git config --global user.name "dron99"
git config --global user.email dron995@icloud.com
git config --global core.editor nano
git config --global color.status auto
git config --global color.diff auto
git config --global init.defaultBranch main
```
### Установка отображения unicode
```
git config --global core.quotepath off
```
### Параметры установки окончаний строк
```
git config --global core.autocrlf input
git config --global core.safecrlf warn
git config --list --show-origin
```
### Подпись
```
gpg --full-generate-key --expert
gpg --list-secret-keys --keyid-format LONG
gpg --armor --export {key}
git config --global user.signingkey {key}
git config --global commit.gpgsign true
```
