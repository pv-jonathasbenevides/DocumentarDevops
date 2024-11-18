<div class="container">
  <div class="row">
    <div class="col-6" style="background-color:#C5E0ED;">
      <img src="https://www.psicologiaviva.com.br/assets/img/logo/psicologiaviva_fundo_claro.png" style="width:300px; padding-top:50px; padding-bottom:30px;">
    </div>
    <div class="col-6" style="background-color:#C5E0ED;">
    </div>
  </div>


<h1 align="center">📦 Guia como criar um Bucket S3 e CloudFront. 🌐</h1>

<p align="center">Este guia fornece instruções passo a passo para criar e configurar um bucket S3 na AWS, criar uma URL pública usando CloudFront e garantir que o acesso direto ao bucket seja bloqueado, permitindo apenas o acesso através do CloudFront.</p>

### :wrench: Tecnologias utilizadas.
* Tecnologia: ``Aws`` ``Bucket S3`` ``Cloudfront``
 
    <img alt="AWS" src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/s3.png" width="200px" />
    <img alt="AWS" src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/cloudfront.png" width="196px" />
  
## Passo 1: Criar um Bucket S3

1. Faça login no [Console de Gerenciamento da AWS](https://aws.amazon.com/console/).
2. Navegue até o serviço S3
3. Clique em "Criar bucket"

![image](https://github.com/conexasaude/pv-documentar-devops/assets/122643683/9839b70d-040b-4fb3-be7d-659cbbd4d048)
   
4. Escolha um nome único para o bucket e clique em "Criar".

![image](https://github.com/conexasaude/pv-documentar-devops/assets/122643683/46954fa5-4eea-4c0f-8823-e4a3463c8ed2)

![image](https://github.com/conexasaude/pv-documentar-devops/assets/122643683/73c58855-7f6e-44d9-9120-92c09e94c488)

5. Adicionar um arquivo no bucket criado. No caso como exemplo foi feito um upload de um aquivo com o nome de index.html

![image](https://github.com/conexasaude/pv-documentar-devops/assets/122643683/536bdd46-7678-455c-ac24-441fe77f6148)

#### Este bucket servirá como o local de armazenamento para os arquivos que você deseja distribuir usando o CloudFront.

## Passo 2: Configurar o CloudFront

1. Navegue até o serviço CloudFront.
   
2. Na seção Acesso de Origem, crie o OAC.
 
  ![image](https://github.com/conexasaude/pv-documentar-devops/assets/122643683/c1f51669-6a1c-4460-95ef-1300286e6197)

  ![image](https://github.com/conexasaude/pv-documentar-devops/assets/122643683/d1fd6fe5-4fa8-43bc-a474-53df5573c346)
  
  ![image](https://github.com/conexasaude/pv-documentar-devops/assets/122643683/5f0cc1d5-2fa7-4ff5-87da-c3de96911abf)

3. Clique em "Criar distribuição".

   ![image](https://github.com/conexasaude/pv-documentar-devops/assets/122643683/bf2a63f0-90b2-4456-bfde-ec7033ed6667)

4. Selecione o Dominio de origem, criado no s3.

   ![image](https://github.com/conexasaude/pv-documentar-devops/assets/122643683/f6904f21-d60d-45bb-9ae6-a5f289d56278)

5. Ajuste o Origin Access control (OAC)

   ![image](https://github.com/conexasaude/pv-documentar-devops/assets/122643683/fdca90f4-3c62-40ac-af71-a8110db6e60f)

6. configura o waf, para não habilitar a proteção de segurança.

   ![image](https://github.com/conexasaude/pv-documentar-devops/assets/122643683/c301aff4-a961-41cf-84d3-22dbcaece8b2)

7. Em "default root object" adicionar o nome do arquivo do bucket neste exemplo é o "index.html"

   ![image](https://github.com/conexasaude/pv-documentar-devops/assets/122643683/b7479058-ba95-4f5d-a556-a13ceb869364)

8. Após salvar a ditribuição, copia as "politicas" para adidionar no s3.

    ![image](https://github.com/conexasaude/pv-documentar-devops/assets/122643683/20f2da26-a28d-417a-bf06-fec1aaa97fe2)


Obs: O CloudFront é um serviço de entrega de conteúdo global que permite distribuir seu conteúdo de maneira eficiente em todo o mundo, reduzindo a latência.

## Passo 3: Adicionar Política de Acesso ao Bucket S3.

1. Volte para o Console S3.
2. Selecione o bucket criado.
3. Vá para a guia "Permissões" e clique em "Política de bucket".

  ![image](https://github.com/conexasaude/pv-documentar-devops/assets/122643683/e63500e3-9376-402e-ad6f-0ba40dc722af)

  ![image](https://github.com/conexasaude/pv-documentar-devops/assets/122643683/aeec095e-84fd-47b0-a0e9-fafe766b7fd7)

4. Adicione a seguinte política para negar o acesso direto ao bucket, permitindo apenas solicitações originadas do CloudFront:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::mybucketteste02/*",
      "Condition": {
        "StringNotEquals": {
          "aws:Referer": "https://d3k9rnmjbbqbgu.cloudfront.net/*"
        }
      }
    }
  ]
}

### Esta política nega explicitamente as solicitações de acesso direto ao bucket S3, exceto aquelas que são originadas da URL do CloudFront que configuramos na distribuição.
````

#### Após seguir os passos acima, você pode acessar seu site usando o domínio da distribuição CloudFront:

![image](https://github.com/conexasaude/pv-documentar-devops/assets/122643683/45cd8172-11ee-475c-badc-ccd551287f8a)
#####  [Acesso url direto do cloudfront - Ativo](https://d3k9rnmjbbqbgu.cloudfront.net/)

![image](https://github.com/conexasaude/pv-documentar-devops/assets/122643683/a5bad096-cd3c-4eb4-9e61-de07b74ea700)
  
#####  [Acesso url direto do s3 - bloqueado](https://mybucketteste02.s3.amazonaws.com/index.html)

#### Agora, o acesso direto ao bucket está bloqueado, enquanto o acesso via CloudFront está permitido. Isso ajuda a garantir a segurança dos seus arquivos e permite uma distribuição eficiente do conteúdo.

#### Segue a documentação oficial: [amazon-cloudfront-introduces-origin-access-control-oac](https://aws.amazon.com/pt/blogs/networking-and-content-delivery/amazon-cloudfront-introduces-origin-access-control-oac/)

### :handshake: Colaboradores DevOps / Infra-Soprar

|   [<img src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/1625799222011.jpg" width=115><br><sub>Marco Tulio</sub>](https://www.linkedin.com/in/mtuliog/)  |   [<img src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/john.jpg" width=115><br><sub>Jonathas Benevides</sub>](https://www.linkedin.com/in/jonathas-benevides-124757148/)  |   [<img src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/bruno1.jpg" width=115><br><sub>Bruno Baltazar</sub>](https://www.linkedin.com/in/bruno-baltazar-68a660201/)  |   [<img src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/Leandro.jpg" width=115><br><sub>Leandro Dionizio</sub>](https://www.linkedin.com/in/leandro-dionizio-ramos-it/)  |  
| :---: | :---: | :---: | :---: |

## :dart: Status
<p align="center">
  <img src="http://img.shields.io/static/v1?label=STATUS&message=%20Validado&color=GREEN&style=for-the-badge"/>
</p>
