# README

## 概要
GitHub ActionsでAWS LambdaにDeploy

## 手順

### 1.IAMのポリシー作成

AWSのIAMからポリシーを作成する。
```
{
  "Version": "2012-10-17",
    "Statement": [
      {
        "Effect": "Allow",
        "Action": [
          "s3:PutObject",
          "iam:ListRoles",
          "lambda:UpdateFunctionCode",
          "lambda:CreateFunction",
          "lambda:UpdateFunctionConfiguration"
        ],
        "Resource": "*"
      }
    ]
}
```

### 2.IAMのユーザー作成

AWSのIAMからユーザーを作成する。

### 3.githubのSecrets登録

レポジトリ内のSettings → Secrets → New Repository Secretsをクリックする。
AWS_ACCESS_KEY_IDとAWS_SECRET_ACCESS_KEYを作成し、IAMのユーザーのアクセスキーIDとシークレットアクセスキーを登録する。

### 4.GitHub Actionsの作成
レポジトリ内のActionsからテンプレートを作成し、下記を記載する。
```
name: DeployLambda

on:
    push:
        branches:
            - main
            
jobs:
    deploy:
        runs-on: ubuntu-latest
        steps:
            - uses: actions/checkout@main
            - uses: actions/setup-python@v3
              with:
                python-version: 3.8
            - run: zip -r package.zip ./*
            - run: pip3 install awscli
            - run: aws lambda update-function-code --function-name DeployTest --zip-file fileb://package.zip --publish
              env:
                AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
                AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
                AWS_DEFAULT_REGION: ap-northeast-1

```
### 5.GitHub Actionsの作成
lambda-functionファイルを作成して、プッシュする。