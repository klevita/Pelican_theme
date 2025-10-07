## 1. Установка Python

https://www.python.org/downloads/

## 2. Установка Node

https://nodejs.org/en/download

## 3. Проверка pip и virtualenv

python --version  
pip --version  
virtualenv --version  

или

python3 --version  
pip3 --version  
virtualenv --version  

## 4. Установка virtualenv

pip install virtualenv  
pip3 install virtualenv  

https://virtualenv.pypa.io/en/latest/installation.html

## 5. Создание каталога и виртуального окружения

mkdir pelican  
cd pelican  
virtualenv venv  

Windows:  
venv\Scripts\activate  

macOS/Linux:  
source venv/bin/activate  

## 6. Установка MkDocs

pip install mkdocs  
mkdocs --version

## 7. Установка npm пакетов

npm ci

## 8. Добавляем тему

npm run 

## 9. билд css при помощи PostCSS

npm run 

## 10. Пушим изменения в репозиторий

git init  
git remote add origin <URL репозитория>  
git add .  
git commit -m "Initial commit"  
git push -u origin main

## 11. Настраиваем Actions

На GitHub перейти во вкладку **Pages**  
Ставим Source - **GitHub Actions**

На GitHub перейти во вкладку **Actions**  
Создать новый workflow

## 12. Корректируем yml-файл под GitHub Pages

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
  build-css:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout
      uses: actions/checkout@v4
      with:
        submodules: true
        token: ${{ secrets.GH_TOKEN }}
        
    - name: Setup Node.js
      uses: actions/setup-node@v4
      with:
        node-version: '20'
        cache: 'npm'
        
    - name: Install Node.js dependencies
      run: npm ci
        
    - name: Build CSS with PostCSS
      run: npm run build:css
        
    - name: Upload CSS artifact
      uses: actions/upload-artifact@v4
      with:
        name: built-css
        path: bootstrap2-dark/
        retention-days: 1


  build-python:
    runs-on: ubuntu-latest
    needs: build-css
    steps:
    - name: Checkout
      uses: actions/checkout@v4
      with:
        submodules: true
    
    - name: Download CSS artifact
      uses: actions/download-artifact@v4
      with:
        name: built-css
        path: bootstrap2-dark
        
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
    needs: build-python
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

## 13. Проверяем, что все задеплоилось

В разделе actions открываем последний action 
Переходим по ссылке с последнего этапа https://klevita.github.io/Pelican_theme/

## 14. Отлаживаем

Проверяем логи Actions  
Исправляем ошибки при сборке или деплое  
Повторно пушим изменения и следим за результатом
Повторяем при необходимости
