ZBG API Documentation Project
-----

## Introduction

This is the project for the official API documents of ZBG. This README will introduce how this project is structured and how to work with it.

## Repo Structure

The API documentation is generated with Slate. This project is an fork of https://github.com/lord/slate slate.


ZBG currently supports one versions of API (v1) and two languages of API documents (English, Chinese), so we have a total of four versions of API documents sit in two branches: v1_en, v1_cn, v2_en, v2_cn. 

To access the web page of a specific version you can visit https://zbgapi.github.io/docs/{module}/{version}/{language}, 
e.g. for v1 english version visit ：https://zbgapi.github.io/docs/spot/v1/en

## Change Style(logo, style, layout）

1. Checkout master branch
2. Make the common file changes
3. Commit and push the changes 
4. Checkout each version's branch, merge/master, then run deploy.sh

## To make a change to a specific version of the documentation

1. Checkout the target version's branch, e.g. if you want to update v1 chinese documents, checkout v1_cn
2. Make changes to source/index.html.md in this branch and commit
3. Run deploy.sh to deploy the changes to the website.
4. Confirm the result on the web page, if all look good, push the changes.

## Slate Change

- Change logo: https://github.com/lord/slate/wiki/Changing-the-Logo
- Customize style: https://github.com/lord/slate/wiki/Custom-Slate-Themes
- Change font: https://github.com/slatedocs/slate/wiki/Changing-the-font
- Change content markdown: https://github.com/slatedocs/slate/wiki/Markdown-Syntax

## Build and Deploy the API

- Install slate：https://github.com/slatedocs/slate/wiki/Installing-Slate
- Publish API Documents：https://github.com/slatedocs/slate/wiki/Deploying-Slate
