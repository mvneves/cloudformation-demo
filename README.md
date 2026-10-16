# AWS CloudFormation Demo

Pilha com um bucket S3, uma API no API Gateway, três funções Lambda e uma tabela do DynamoDB para enviar arquivos
e listar os envios mais recentes.

O *template* foi feito para o AWS Academy Learner Lab: as funções Lambda usam a role **LabRole**, que já existe na
conta (não é possível criar *roles* no Learner Lab). Use a região `us-east-1` ou `us-west-2`.

## Parâmetros

| Parâmetro | Padrão | Descrição |
|---|---|---|
| `BucketName` | (vazio) | Nome do bucket S3. Nomes de bucket são únicos no mundo inteiro: escolha um nome próprio (ex.: `aula10-uploads-seunome`) ou deixe em branco para o CloudFormation gerar um nome único |
| `DynamoDBTableName` | `FileUploads` | Nome da tabela do DynamoDB |

O bucket é privado. A página inicial gera URLs pré-assinadas (válidas por 1 hora) para baixar os arquivos.

## Validar template

    aws cloudformation validate-template --template-body file://template.yaml

## Criar uma nova pilha

Com nome de bucket gerado automaticamente:

    aws cloudformation create-stack --stack-name MinhaPilha --template-body file://template.yaml

Com um nome de bucket escolhido por você:

    aws cloudformation create-stack --stack-name MinhaPilha --template-body file://template.yaml \
        --parameters ParameterKey=BucketName,ParameterValue=aula10-uploads-seunome

Acompanhe a criação e veja os endereços da aplicação (*outputs*):

    aws cloudformation wait stack-create-complete --stack-name MinhaPilha
    aws cloudformation describe-stacks --stack-name MinhaPilha --query "Stacks[0].Outputs"

Abra o endereço `HomePageEndpoint` no navegador.

### Pelo console

1. Acesse **CloudFormation > Stacks > Create stack > With new resources (standard)**.
2. Em **Specify template**, escolha **Upload a template file** e envie o `template.yaml`.
3. Dê um nome à pilha e preencha os parâmetros (ou deixe `BucketName` em branco).
4. Avance até o fim e clique em **Submit**. Os endereços aparecem na aba **Outputs** quando a pilha chegar a
   `CREATE_COMPLETE`.

## Atualizar pilha

    aws cloudformation update-stack --stack-name MinhaPilha --template-body file://template.yaml

Se a pilha foi criada com um `BucketName`, repita o parâmetro (ou use
`ParameterKey=BucketName,UsePreviousValue=true`).

## Deletar pilha

O CloudFormation não apaga um bucket que tem arquivos: esvazie o bucket antes, senão o `delete-stack` falha.

    BUCKET=$(aws cloudformation describe-stacks --stack-name MinhaPilha \
        --query "Stacks[0].Outputs[?OutputKey=='BucketName'].OutputValue" --output text)
    aws s3 rm s3://$BUCKET --recursive
    aws cloudformation delete-stack --stack-name MinhaPilha
