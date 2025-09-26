## 1. Установка Python

https://www.python.org/downloads/

## 2. Проверка pip и virtualenv

python --version  
pip --version  
virtualenv --version  

или

python3 --version  
pip3 --version  
virtualenv --version  

## 3. Установка virtualenv

pip install virtualenv  
pip3 install virtualenv  

https://virtualenv.pypa.io/en/latest/installation.html

## 4. Создание каталога и виртуального окружения

mkdir pelican  
cd pelican  
virtualenv venv  

Windows:  
venv\Scripts\activate  

macOS/Linux:  
source venv/bin/activate  

## 5. Установка MkDocs

pip install mkdocs  
mkdocs --version

## 6. Пушим изменения в репозиторий

git init  
git remote add origin <URL репозитория>  
git add .  
git commit -m "Initial commit"  
git push -u origin main

## 7. Настраиваем Actions

На GitHub перейти во вкладку **Pages**  
Ставим Source - **GitHub Actions**

На GitHub перейти во вкладку **Actions**  
Создать новый workflow

## 8. Корректируем yml-файл под GitHub Pages

.github/workflows/pelican.yml:

```
name: Deploy to GitHub Pages

on:
  push:
    branches: ["master"]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout
      uses: actions/checkout@v4
      with:
        submodules: true
        
    - name: Setup Python
      uses: actions/setup-python@v4
      with:
        python-version: '3.11'
        
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install pelican markdown
        
    - name: Build site
      run: pelican content -s publishconf.py -v
        
    - name: Upload artifact
      uses: actions/upload-pages-artifact@v3
      with:
        path: ./output

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

## 9. Проверяем, что все задеплоилось

В разделе actions открываем последний action 
Переходим по ссылке с последнего этапа https://klevita.github.io/Pelican_deploy/

## 10. Отлаживаем

Проверяем логи Actions  
Исправляем ошибки при сборке или деплое  
Повторно пушим изменения и следим за результатом
Повторяем при необходимости
