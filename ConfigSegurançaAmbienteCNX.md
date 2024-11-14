<div class="container">
<div class="row">
<div class="col-6" style=" background-color:#C5E0ED;"><img src="https://www.psicologiaviva.com.br/assets/img/logo/psicologiaviva_fundo_claro.png" style="width:300px; padding-top:50px; padding-bottom:30px;"></div>
<div class="col-6" style=" background-color:#C5E0ED;">
</div>

<h1 align="center">:file_cabinet: ConfigSegurançaAmbienteCNX.md</h1>

### :memo: Descrição.

* Documentação de Configurações de Segurança AWS para Ambiente Conexa 🛡️

### :books: Funcionalidades

* Esta documentação abrangente descreve as configurações de segurança implementadas no ambiente Conexa hospedado na AWS. Cada arquivo de configuração e fluxo de trabalho foi projetado para garantir um ambiente seguro, controlado e protegido contra ameaças.

### :wrench: Tecnologias utilizadas
* Tecnologia: ``Aws`` ``Github`` ``Elastic Beanstalk``
 
 
    <img alt="AWS" src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/AWS.png" width="200px" />
    <img alt="github" src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/github1.png" width="200px" />
    <img alt="ElasticBean" src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/ElasticBean.png" width="200px" />


### :rocket: Introdução

No repositório Devops em ``pv-conexa-sg-cloudformation``, encontram-se arquivos de configuração relacionados à segurança para o ambiente Conexa.

Para acesso ao repositorio [clique aqui](https://github.com/conexasaude/pv-conexa-sg-cloudformation)

* Arquivo ``09_securitygroup_ssh.config`` 🔒

Funcionalidade: Restrição de Acesso SSH

Descrição: O arquivo 09_securitygroup_ssh.config contém a configuração para restringir o acesso SSH ao ambiente Conexa. Essa restrição é baseada em regras de grupo de segurança, 
permitindo a conexão SSH apenas de fontes específicas. Essa medida reduz a superfície de ataque e limita o acesso privilegiado a locais autorizados.

<div align="center">
  <img src="https://github.com/conexasaude/pv-documentar-devops/assets/122643683/bb2f1205-7333-4d45-b7cf-98d9b7b0a641" alt="image" />
</div>

* Arquivo ``08_securitygroup_lb-http-https.config`` 🌐
 
Funcionalidade: Configuração do Grupo de Segurança para o Balanceador de Carga Elástico (ALB)

Descrição: O arquivo 08_securitygroup_lb-http-https.config define as regras de segurança para o Balanceador de Carga Elástico (ALB). Essas regras especificam permissões de entrada para tráfego HTTP e HTTPS,
permitindo somente tráfego autorizado às instâncias de destino. Essa configuração fortalece a proteção do ambiente, garantindo que apenas solicitações legítimas sejam encaminhadas.

Regras Implementadas:

<div align="center">
  <img src="https://github.com/conexasaude/pv-documentar-devops/assets/122643683/96e0e129-d06a-45ee-b690-51c74c90cfe0" alt="image" />
</div>

#### Segue o script do arquivo original com as regras adicionadas.

   ```
   Resources:
  AWSEBLoadBalancerSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: "ALB Security Group Conexa"
      SecurityGroupIngress:
        
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 172.31.0.0/16

        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 73.245.48.0/20
        
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 103.21.244.0/22
          
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 103.22.200.0/22

        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 103.31.4.0/22
          
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 141.101.64.0/18


        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 108.162.192.0/18
          
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 190.93.240.0/20

        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 188.114.96.0/20

        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 197.234.240.0/22


        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 198.41.128.0/17

        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 162.158.0.0/15

        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 104.16.0.0/13  

        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 104.24.0.0/14  

        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 172.64.0.0/13 

        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 131.0.72.0/22 

        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIpv6: 2400:cb00::/32 

        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIpv6: 2606:4700::/32 

        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIpv6: 2803:f800::/32 

        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIpv6: 2405:b500::/32

        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIpv6: 2405:8100::/32


        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIpv6: 2c0f:f248::/32


        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIpv6: 2a06:98c0::/29
 
        - IpProtocol: tcp
          FromPort: 443
          ToPort: 443
          CidrIp: 172.31.0.0/16

        - IpProtocol: tcp
          FromPort: 443
          ToPort: 443
          CidrIp: 73.245.48.0/20
        
        - IpProtocol: tcp
          FromPort: 443
          ToPort: 443
          CidrIp: 103.21.244.0/22
          
        - IpProtocol: tcp
          FromPort: 443
          ToPort: 443
          CidrIp: 103.22.200.0/22

        - IpProtocol: tcp
          FromPort: 443
          ToPort: 443
          CidrIp: 103.31.4.0/22
          
        - IpProtocol: tcp
          FromPort: 443
          ToPort: 443
          CidrIp: 141.101.64.0/18


        - IpProtocol: tcp
          FromPort: 443
          ToPort: 443
          CidrIp: 108.162.192.0/18
          
        - IpProtocol: tcp
          FromPort: 443
          ToPort: 443
          CidrIp: 190.93.240.0/20

        - IpProtocol: tcp
          FromPort: 443
          ToPort: 443
          CidrIp: 188.114.96.0/20

        - IpProtocol: tcp
          FromPort: 443
          ToPort: 443
          CidrIp: 197.234.240.0/22


        - IpProtocol: tcp
          FromPort: 443
          ToPort: 443
          CidrIp: 198.41.128.0/17

        - IpProtocol: tcp
          FromPort: 443
          ToPort: 443
          CidrIp: 162.158.0.0/15

        - IpProtocol: tcp
          FromPort: 443
          ToPort: 443
          CidrIp: 104.16.0.0/13  

        - IpProtocol: tcp
          FromPort: 443
          ToPort: 443
          CidrIp: 104.24.0.0/14  

        - IpProtocol: tcp
          FromPort: 443
          ToPort: 443
          CidrIp: 172.64.0.0/13 

        - IpProtocol: tcp
          FromPort: 443
          ToPort: 443
          CidrIp: 131.0.72.0/22 
        
        - IpProtocol: tcp
          FromPort: 443
          ToPort: 443
          CidrIp: 10.30.11.0/24
        
        - IpProtocol: tcp
          FromPort: 443
          ToPort: 443
          CidrIp: 10.30.10.0/24

        - IpProtocol: tcp
          FromPort: 443
          ToPort: 443
          CidrIp: 10.10.63.0/24

        - IpProtocol: tcp
          FromPort: 443
          ToPort: 443
          CidrIp: 10.212.185.0/25

        - IpProtocol: tcp
          FromPort: 443
          ToPort: 443
          CidrIp: 172.30.10.0/24

        - IpProtocol: tcp
          FromPort: 443
          ToPort: 443
          CidrIpv6: 2400:cb00::/32 

        - IpProtocol: tcp
          FromPort: 443
          ToPort: 443
          CidrIpv6: 2606:4700::/32 

        - IpProtocol: tcp
          FromPort: 443
          ToPort: 443
          CidrIpv6: 2803:f800::/32 

        - IpProtocol: tcp
          FromPort: 443
          ToPort: 443
          CidrIpv6: 2405:b500::/32

        - IpProtocol: tcp
          FromPort: 443
          ToPort: 443
          CidrIpv6: 2405:8100::/32


        - IpProtocol: tcp
          FromPort: 443
          ToPort: 443
          CidrIpv6: 2c0f:f248::/32


        - IpProtocol: tcp
          FromPort: 443
          ToPort: 443
          CidrIpv6: 2a06:98c0::/29

   ```
Obs: Add os IPs da Kainos.

![image](https://github.com/conexasaude/pv-documentar-devops/assets/122643683/61400ff3-a70d-4b54-8ac2-bf3014ef3841)


* Arquivo ``sg_config.yml`` ⚙️

Funcionalidade: Automação de Implantação e Segurança

O arquivo que está no caminho .github/workflows/sg_config.yml representa um fluxo de trabalho automatizado. Ele une as configurações de segurança definidas nos arquivos anteriores ao processo de implantação. Isso garante que as regras de segurança sejam aplicadas de maneira consistente e segura durante todo o ciclo de vida do ambiente. A automação dessa integração otimiza a segurança e a eficiência das operações.

Funcionalidades Implementadas:

- Clone do repositório privado de configurações de segurança.
- Transferência das configurações para os locais pertinentes.
- Integração das configurações de segurança ao fluxo de implantação.


<div align="center">
  <img src="https://github.com/conexasaude/pv-documentar-devops/assets/122643683/c7fbe05d-6c24-4a0b-af45-8f4f0aa783a0" alt="image" />
</div>


:low_brightness:No repositório Dev-Conexa, foi necessário implementar o seguinte script. Para acessar o reposit de exemplo, [clique aqui](https://github.com/conexasaude/ms-migrador).

O seguinte exemplifica o processo de vinculação desta implantação. Este código é responsável por transferir os arquivos de configuração de segurança da pasta "security_aws" para a pasta ".ebextensions" durante a etapa de implantação.
Isso é parte fundamental da automação do procedimento de configuração de segurança no ambiente Elastic Beanstalk, como delineado no fluxo de trabalho.


<div align="center">
  <img src="https://github.com/conexasaude/pv-documentar-devops/assets/122643683/e296dda6-0914-4a93-b676-0bad850d9d1e" alt="image" />
</div>


### :white_check_mark: Conclusão

Ao visualizar as configurações implantadas na AWS (SG&EB), fica evidente que a implantação do ambiente "ms-migrador-hml" foi realizada com a incorporação das novas regras de segurança. Estas regras englobam restrições de acesso, incluindo a adição dos IPs privados da Cloudflare no Security Group, bem como o bloqueio do acesso ao domínio por meio do Elastic Beanstalk. Essas ações fortalecem a segurança e protegem contra potenciais ameaças, o que resulta no ambiente não sendo diretamente acessível via URL. Isso proporciona um ambiente mais gerenciável e seguro.


<div align="center">
  <img src="https://github.com/conexasaude/pv-documentar-devops/assets/122643683/041fcf1d-c48f-4a5d-9860-7252c549ae82" alt="image" />
  <img src="https://github.com/conexasaude/pv-documentar-devops/assets/122643683/59270ccb-a80b-4dd7-a60e-3296ffb9382c" alt="image" />
</div>


### :unlock: Detalhando as Configurações Implementadas no ALB Security Group

Ao efetuar a implantação do ambiente "ms-migrador-hml" na Amazon Web Services (AWS), uma série de configurações é aplicada para garantir tanto a segurança quanto o desempenho otimizado do ambiente. Dentre essas configurações, destaca-se a utilização de grupos de segurança (security groups) para supervisionar tanto o fluxo de entrada quanto de saída das instâncias do ambiente.

<div align="center">
  <img src="https://github.com/conexasaude/pv-documentar-devops/assets/122643683/dd063a63-b7be-41c1-980c-e50538d09a88" alt="image" />
</div>

### :dart: Explorando as Configurações de Segurança

Esta documentação abrangente explora as configurações de segurança implementadas no ambiente Conexa. Cada arquivo e configuração desempenha um papel crucial na garantia da segurança do ambiente hospedado na AWS. Além disso, reflete o comprometimento com a proteção dos recursos, mantendo um ambiente seguro e controlado.


## :handshake: Colaboradores DevOps / Infra-Soprar

|   [<img src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/1625799222011.jpg" width=115><br><sub>Marco Tulio</sub>](https://www.linkedin.com/in/mtuliog/)  |   [<img src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/john.jpg" width=115><br><sub>Jonathas Benevides</sub>](https://www.linkedin.com/in/jonathas-benevides-124757148/)  |   [<img src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/bruno1.jpg" width=115><br><sub>Bruno Baltazar</sub>](https://www.linkedin.com/in/bruno-baltazar-68a660201/)  |   [<img src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/Leandro.jpg" width=115><br><sub>Leandro Dionizio</sub>](https://www.linkedin.com/in/leandro-dionizio-ramos-it/)  |  
| :---: | :---: | :---: | :---: |

## :dart: Status
<p align="center">
  <img src="http://img.shields.io/static/v1?label=STATUS&message=%20Validado&color=GREEN&style=for-the-badge"/>
</p>
