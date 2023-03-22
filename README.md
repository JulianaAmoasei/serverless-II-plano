# Curso Serverless II

Esta e a API base para o projeto do curso de Serverless da Alura.

## Requisitos

Os requisitos para executar este projeto em produção são:

- Docker
- Conta ativa na AWS

O restante da stack será instalado a partir do Dockerfile na raiz do próprio repositório.

## Instalação

- Faça o *fork* e o *clone* do projeto para seu computador
- Navegue até a pasta-raiz do projeto

### Criação de perfil e user na AWS

Antes de instalar a interface de linha de comando da AWS (aws-cli) que usaremos no curso, você precisa ter uma conta ativa na AWS. 

Os passos do processo são:

1. Fazer o [cadastro na AWS](https://docs.aws.amazon.com/pt_br/cli/latest/userguide/getting-started-prereqs.html#getting-started-prereqs-signup)
2. Criar uma conta IAM
3. Criar uma chave de acesso e chave secreta (*access key* e *secret key*)

Siga estes passos para criar e acessar chaves ou siga a [documentação](https://docs.aws.amazon.com/pt_br/IAM/latest/UserGuide/id_credentials_access-keys.html):

1. Acesse o console da AWS
2. NA página principal do console, acesse IAM
3. No menu à direita, acesse "Users"
4. Na lista de usuários, adicione um novo no botão "Add users"
5. Preencha o nome do novo usuário e deixe em branco a opção de permitir acesso ao console de gerenciamento IAM
6. Nas permissões, adicione o novo usuário a um grupo
7. Na próxima tela, revise as informações e clique em "Create user"
8. Você voltará para a tela de "Users". Clique no nome do usuário na lista e acesse o submenu "Security credentials"
9. Role a tela até a opção "Access keys". Clique em "Create access key"
10. Na próxima tela, selecione a opção "Command Line Interface (CLI)" e avance.
11. As tags são opcionais, você pode deixar em branco e clicar em "Create Access key".
12. Copie **temporariamente** para um lugar seguro ou faça o download do arquivo .csv até finalizarmos a configuração do CLI.

A documentação da AWS tem mais informações sobre todos os passos. Neste curso utilizaremos as credenciais de longo prazo por questões de praticidade. 

- [cadastro na AWS](https://docs.aws.amazon.com/pt_br/cli/latest/userguide/getting-started-prereqs.html#getting-started-prereqs-signup).
- [criação de conta IAM](https://docs.aws.amazon.com/pt_br/cli/latest/userguide/getting-started-prereqs.html#getting-started-prereqs-iam).
- [documentação sobre chaves de acesso e seus tipos](https://docs.aws.amazon.com/pt_br/cli/latest/userguide/getting-started-prereqs.html#getting-started-prereqs-keys).

### Instalação da aws-cli

Após criar o IAM e gerar as chaves, instale a interface de linha de comando da AWS [`aws-cli`](https://docs.aws.amazon.com/pt_br/cli/latest/userguide/getting-started-install.html) para o seu sistema operacional.

Se não tiver certeza se já tem a CLI instalada, verifique com o comando `aws --version` no terminal. Caso esteja instalada, o terminal exibirá a mensagem `aws-cli/<versao> Python/<versao>` mais as informações sobre o seu sistema operacional. Neste curso utilizamos a versão 2.11.0.

### Configuração da aws-cli com as chaves

No terminal, insira o comanto `aws configure`. Você deverá em seguida adicionar as chaves.
Copie e cole com cuidado a *Access Key ID*:
```
AWS Access Key ID [************************]: SUACHAVEAQUI
```
Em seguida, insira a *Secret Access Key*:
```
AWS Secret Access Key [************************]: SUACHAVESECRETAAQUI
```
Na opção `Default region name:` se você estiver no Brasil, insira `us-east-1`. Caso você não esteja no Brasil, deverá verificar na documentação da AWS em qual região você está.

Na opção `Default output format:` selecione `json`.

> **IMPORTANTE:** Estas configurações ficam armazenadas **localmente** em seu computador. Para saber mais sobre como as credenciais funcionam e como podem ser acessadas e modificadas, acesse a [documentação da AWS](https://docs.aws.amazon.com/pt_br/cli/latest/userguide/cli-configure-files.html). 

## Execução e deploy

### Gerando certificados para o DocumentDB

Este projeto utiliza como banco de dados o DocumentDB da própria AWS. Para autenticar a conexão da API com o banco é necessário gerar um certificado localmente e adicionar o arquivo à pasta-raiz do projeto. Siga os passos:

1. Certifique-se que está em um ambiente logado da AWS;
2. Acesse o [link para gerar o certificado](https://s3.amazonaws.com/rds-downloads/rds-combined-ca-bundle.pem). O navegador deverá baixar automaticamente o arquivo `rds-combined-ca-bundle.pem` na pasta padrão para Downloads de seu sistema operacional. 
3. Acesse esta pasta e transfira o arquivo `rds-combined-ca-bundle.pem` para a pasta raiz do projeto.

Após este processo, a pasta-raiz do projeto deverá ter a seguinte estrutura:

```
.
├── env
│   └── prod
│       ├── Main.tf
│       ├── terraform.tfstate
│       └── terraform.tfstate.backup
├── infra
│   ├── ALB.tf
│   ├── DocDB.tf
│   ├── ECS.tf
│   ├── GrupoSeguranca.tf
│   ├── IAM.tf
│   ├── Provider.tf
│   ├── Variaveis.tf
│   └── VPC.tf
├── Desligar.sh
├── Infraestrutura.sh
├── rds-combined-ca-bundle.pem
└── README.md

3 directories, 15 files
```

### Executando o deploy via Terraform

Para executar e fazer o deploy do projeto na AWS, execute o seguinte comando no terminal:
```
./Infraestrutura.sh
```
Você deverá estar na pasta-raiz do projeto, onde está localizado o arquivo `Infraestrutura.sh`. Este script fará a instalação do Terraform, a execução dos scripts do Terraform e o deploy do projeto na AWS a partir de uma imagem Docker no DockerHub.

Após a finalização do processo, a URL base da API será exibida no terminal:

```
Outputs:

IP_alb = "curso-serverless2-api-<idDaURL>.us-east-1.elb.amazonaws.com"
```
Copie e cole a URL para ser utilizada durante o curso.
Caso seja necessário, você pode consultar a URL no console da AWS. No menu de "Services" no canto superior esquerdo, acesse EC2 > Load balancers > curso-serverless-2 > DNS Name.

> Importante: Para evitar cobranças desnecessárias de serviços da AWS, **mantenha a API no ar apenas enquanto estiver acompanhando o curso.** Para derrubar a API, execute o script `./Desligar.sh` na pasta-raiz do projeto.

## Acessando a URL do projeto

Este projeto expõe os seguintes endpoints:

- GET `/alunos`
- GET `/alunos/:id`
- POST `/alunos`
- PUT `/alunos/:id`
- DELETE `/alunos/:id`
