<div class="container">
  <div class="row">
    <div class="col-6" style="background-color:#C5E0ED;">
      <img src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/conexa-logo.svg" style="width:300px; padding-top:50px; padding-bottom:30px;">
    </div>
    <div class="col-6" style="background-color:#C5E0ED;">
    </div>
  </div>

   
<h1 align="center">:file_cabinet: "Arquitetura na nuvem: Fluxo de Serviços AWS para uma infraestrutura Segura e Escalável".</h1>

### :wrench: Tecnologias utilizadas
   
* Tecnologia: ``Aws`` ``CloudFlare`` ``Kubernetes``

<div style="display: inline_block">
  
  <img align="center" alt="github" src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/AWS.png" width="200px" />
  <img align="center" alt="github" src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/cloudflare.png" width="200px" />
  <img align="center" alt="Kubernetes" src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/kubernetes.png" width="200px" />
  
   
</div><br/>   
   
### :rocket: Em nosso contexto de arquitetura [Aws_Cloud_Produção](https://github.com/conexasaude/pv-documentar-devops/blob/master/img/PV-PRD.drawio%20(5).png).

https://github.com/conexasaude/pv-documentar-devops/assets/122643683/f238ce55-f3d4-4d43-b5a7-5505214ed619

###  :low_brightness: É construído em torno da infraestrutura da AWS (Amazon Web Services), com o Cloudflare como serviço de proteção de segurança e otimização de desempenho. Vamos detalhar as principais componentes envolvidas e tecnologias:

``Cloudflare:`` É utilizado como uma camada de segurança e otimização de desempenho. Ele ajuda a proteger sua aplicação contra ataques DDoS, XSS e outros tipos de ataques maliciosos. Além disso, ele também oferece recursos de cache e CDN (Content Delivery Network) para melhorar a velocidade de carregamento da aplicação.

``AWS Shield:`` É um serviço de proteção da AWS contra ataques DDoS. Ele trabalha em conjunto com o Cloudflare para garantir a segurança da sua aplicação, detectando e mitigando automaticamente os ataques de negação de serviço.

``AWS Config:`` É um serviço de gerenciamento de conformidade e configuração da AWS. Ele permite que você avalie, audite e monitore as configurações dos seus recursos da AWS, garantindo que eles estejam em conformidade com as melhores práticas e políticas de segurança.

``Elastic Container Registry (ECR):`` É um serviço de registro de contêineres totalmente gerenciado pela AWS. Ele fornece um repositório privado para armazenar, gerenciar e implantar imagens de contêineres do Docker.

``Trusted Advisor:`` É um serviço da AWS que oferece recomendações e verificações automáticas para otimizar sua infraestrutura em termos de custo, desempenho, segurança e tolerância a falhas. Ele analisa sua arquitetura e fornece sugestões para melhorias, ajudando a otimizar o uso dos recursos da AWS.

``CloudTrail:`` É um serviço de auditoria e monitoramento da AWS que registra todas as ações realizadas nos recursos da sua conta. Ele permite que você tenha visibilidade total sobre as atividades na sua infraestrutura, ajudando na segurança e conformidade.

``AWS Lambda:`` É um serviço de computação serverless da AWS. Ele permite que você execute código sem precisar provisionar ou gerenciar servidores. O Lambda é usado para executar tarefas em resposta a eventos, como processamento de dados, integrações de sistemas e tarefas agendadas.

``S3 Bucket:`` É um serviço de armazenamento de objetos altamente escalável da AWS. Ele é utilizado para armazenar e recuperar arquivos e objetos, como imagens, vídeos, documentos e backups. O S3 é altamente durável, seguro e oferece recursos avançados, como controle de acesso e replicação em várias regiões.

#### - Além desses serviços, temos outras tecnologias integradas.

``VPC (Virtual Private Cloud):`` É um serviço que permite criar uma rede virtual isolada na nuvem da AWS. Ele permite que você crie sub-redes, defina tabelas de roteamento e configure regras de firewall para controlar o tráfego de rede entre os componentes do seu ambiente.

``API Gateway:`` É um serviço da AWS que permite criar, publicar, monitorar e proteger APIs (Interfaces de Programação de Aplicativos) de maneira escalável. Ele atua como um ponto de entrada para suas aplicações, permitindo o gerenciamento de autenticação, autorização, controle de acesso e monitoramento de tráfego.

``Elastic Container Service for Kubernetes (EKS):`` É um serviço da AWS que facilita a execução de aplicativos em contêineres utilizando o Kubernetes. Ele provisiona e gerencia automaticamente a infraestrutura necessária para executar o Kubernetes de forma escalável e de alta disponibilidade.

``Elastic Load Balancer:`` É um serviço da AWS que distribui o tráfego de entrada entre várias instâncias ou contêineres para melhorar a disponibilidade e a escalabilidade da sua aplicação. Ele monitora a disponibilidade das instâncias e redireciona o tráfego de forma inteligente para garantir um balanceamento de carga eficiente.

``CloudFront:`` É um serviço de CDN (Content Delivery Network) da AWS. Ele ajuda a melhorar a velocidade de entrega de conteúdo, armazenando em cache os arquivos estáticos da sua aplicação em servidores distribuídos globalmente, mais próximos dos usuários.

``Kubernetes Nodes em Subnets Privadas:`` O Kubernetes é uma plataforma de orquestração de contêineres amplamente utilizada. Nesse caso, os nós (nodes) do Kubernetes são implantados em sub-redes privadas dentro da sua VPC. Isso oferece maior segurança, já que os nós não são diretamente acessíveis pela internet.

``Amazon MQ:`` É um serviço de mensageria gerenciada pela AWS, compatível com os protocolos Apache ActiveMQ e RabbitMQ. Ele permite que você crie sistemas de mensagens altamente disponíveis e escaláveis para comunicação assíncrona entre componentes da sua aplicação.

``Amazon RDS:`` É um serviço de banco de dados relacional gerenciado pela AWS. Ele oferece opções para diferentes tipos de bancos de dados, como MySQL, PostgreSQL, Oracle, SQL Server, entre outros. O RDS simplifica a administração do banco de dados, lidando com tarefas de backup, recuperação, escalabilidade e atualizações de software.

:bulb: Essa é uma descrição básica do fluxo de arquitetura, É importante lembrar que a arquitetura real pode variar dependendo das necessidades específicas de cada serviço.

### :page_with_curl: Segue o fluxo de arquitetura obsoleto do nosso ambiente.

No entanto, antes disso, gostaríamos de compartilhar o fluxo de arquitetura antigo ou obsoleto para fins de referência e contextualização.
É importante ressaltar que o fluxo de arquitetura antigo não reflete mais a configuração atualizada do ambiente. Portanto, as informações apresentadas nesse fluxo podem estar desatualizadas e não representar o estado atual do sistema.
A inclusão do fluxo de arquitetura antigo tem como objetivo fornecer uma compreensão inicial das implementações anteriores e dos elementos que foram modificados ou substituídos na nova versão.

[- fluxo de arquitetura 3d](https://app.cloudcraft.co/view/1592178c-582a-41b5-ad3d-1f3dd92f56ac?key=fdcbef38-b754-43c0-8115-833efd9495ec)
![image](https://github.com/conexasaude/pv-documentar-devops/assets/122643683/2db83679-5c9d-4698-a1d2-2e6cb8b5adf6)

[- fluxo de arquitetura 2d](https://github.com/conexasaude/pv-documentar-devops/blob/master/img/image%20-%20fluxo.png)
![image](https://github.com/conexasaude/pv-documentar-devops/assets/122643683/43a56005-6978-4fa7-9cb2-690709a3613b)


:bookmark_tabs: Este documento é apenas uma explicação introdutória sobre o fluxo inicial da arquitetura do nosso ambiente. Ele fornecerá uma visão geral dos serviços utilizados, mas também será adicionado um fluxo mais detalhado que abordará cada tecnologia de forma mais aprofundada. O objetivo desse fluxo mais detalhado é oferecer uma compreensão abrangente do ambiente, desde a configuração inicial até a personalização avançada, permitindo que desenvolvedores de todos os níveis de habilidade utilizem as tecnologias de forma eficiente e flexível. Portanto, este documento é apenas o ponto de partida, e uma documentação mais completa e detalhada será desenvolvida para proporcionar uma compreensão completa das tecnologias e sua implementação em projetos no ambiente descrito.
          
## :handshake: Colaboradores DevOps / Infra-Soprar

|   [<img src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/1625799222011.jpg" width=115><br><sub>Marco Tulio</sub>](https://www.linkedin.com/in/mtuliog/)  |   [<img src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/john.jpg" width=115><br><sub>Jonathas Benevides</sub>](https://www.linkedin.com/in/jonathas-benevides-124757148/)  |   [<img src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/bruno1.jpg" width=115><br><sub>Bruno Baltazar</sub>](https://www.linkedin.com/in/bruno-baltazar-68a660201/)  |   [<img src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/Leandro.jpg" width=115><br><sub>Leandro Dionizio</sub>](https://www.linkedin.com/in/leandro-dionizio-ramos-it/)  |  
| :---: | :---: | :---: | :---: |

## :dart: Status
<p align="center">
  
<img src="http://img.shields.io/static/v1?label=STATUS&message=%20Validado&color=GREEN&style=for-the-badge"/>
</p>
