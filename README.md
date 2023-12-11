# AWS CloudFormation Demo

## Validar template

    aws cloudformation validate-template --template-body file://template.yaml

## Criar um nova pilha

    aws cloudformation create-stack --stack-name MinhaPilha --template-body file://template.yaml

## Atualizar pilha

    aws cloudformation update-stack --stack-name MinhaPilha --template-body file://template.yaml

## Deletar pilha

    aws cloudformation delete-stack --stack-name MinhaPilha
