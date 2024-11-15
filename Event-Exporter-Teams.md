<div class="container">
  <div class="row">
    <div class="col-6" style="background-color:#C5E0ED;">
      <img src="https://www.psicologiaviva.com.br/assets/img/logo/psicologiaviva_fundo_claro.png" style="width:300px; padding-top:50px; padding-bottom:30px;">
    </div>
    <div class="col-6" style="background-color:#C5E0ED;"></div>
  </div>
</div>

<h1 align="center">:file_cabinet: EventExporterTeamsNotification.md</h1>

## :memo: Descrição

Integração do `event-exporter` do Kubernetes para enviar notificações de eventos para o Microsoft Teams. Essa configuração permite que a equipe de operações e desenvolvimento receba alertas em tempo real sobre eventos críticos, facilitando a monitoração e a resposta a incidentes

### :rocket: Iniciando a POC

1. **Criação do Webhook no Microsoft Teams** :point_right:
   - Abra o seu canal do Microsoft Teams e clique no ícone de reticências (...) próximo ao nome do canal. Selecione a opção "Conectores".
     
     ![image](https://user-images.githubusercontent.com/122643683/231597870-f7ac4ca0-23fe-4fce-bf5d-b9e9ba1d65e2.png)

   - Na janela que aparece, pesquise por "incoming Webhook". Selecione a opção "Webhook" e clique em "Configurar".
     
     ![image](https://user-images.githubusercontent.com/122643683/231598575-3f2f7058-c725-4b13-a135-f2c2a1f176fb.png)

   - Na próxima janela, dê um nome para o webhook e escolha um ícone para ele. Clique em "Criar".
     
     ![image](https://user-images.githubusercontent.com/122643683/231599576-947153ae-8ebd-462f-9704-a15b447a057e.png)

   - Copie a URL do webhook que aparece na janela e salve-a em algum lugar seguro. Você precisará dela mais adiante. Selecione a opção "Concluído".
     
     ![image](https://user-images.githubusercontent.com/122643683/231600112-dfcdf91f-a717-446e-8f63-562d480fbe6a.png)

2. **Criação do Cluster de Teste** :cloud:

   - Faça as credenciais válidas no CloudGuru. Após criar, no caso, utilize um terminal do VSCode WSL.

   - Execute o comando `aws configure` e insira as credenciais.
     
     ![image](https://github.com/user-attachments/assets/8142d60e-0bae-402b-bfc7-b8c5473e4091)

   - Após configurar as credenciais, você estará vinculado ao CloudGuru simulando AWS.

   - Crie o cluster de teste com o arquivo YAML a seguir:
     ```yaml
     apiVersion: eksctl.io/v1alpha5
     kind: ClusterConfig
     metadata:
       name: eks-hml
       region: us-east-1
       version: "1.28"
       tags:
         karpenter.sh/discovery: eks-hml
     availabilityZones: ["us-east-1a", "us-east-1b", "us-east-1c", "us-east-1d"]

     iam:
       withOIDC: true

     managedNodeGroups:
       - instanceType: t4g.medium
         amiFamily: AmazonLinux2
         name: eks-hml-ng
         desiredCapacity: 3
         minSize: 3
         maxSize: 4
         privateNetworking: true

     addons:
       - name: vpc-cni
         resolveConflicts: overwrite

       - name: coredns
         resolveConflicts: overwrite

       - name: kube-proxy
         resolveConflicts: overwrite
     ```

   - Execute o arquivo `cluster.yml` com o comando:
     ```bash
     eksctl create cluster -f cluster.yml
     ```
     ![image](https://github.com/user-attachments/assets/be018e53-0ba8-45cb-b101-7385653c76e3)
     ![image](https://github.com/user-attachments/assets/2a397b15-4cbd-434a-8781-a8d2914f9f5e)

3. **Configuração do Event Exporter** :package:

   - Aplique o YAML do Event Exporter com o seguinte conteúdo:
   
     Obs: Na linha de endpoint cola o link do webhook que foi criado no teams.
     EX: endpoint: "https://plataformaconexasaude.webhook.office.com/webhookb2/...

    ![image](https://github.com/user-attachments/assets/566cefd7-6091-4cda-8fff-6d9960ff1ad5)

     ```yaml
     apiVersion: v1
     kind: Namespace
     metadata:
       name: monitoring
     ---
     apiVersion: v1
     kind: ServiceAccount
     metadata:
       namespace: monitoring
       name: event-exporter
     ---
     apiVersion: rbac.authorization.k8s.io/v1
     kind: ClusterRoleBinding
     metadata:
       name: event-exporter-view
     roleRef:
       apiGroup: rbac.authorization.k8s.io
       kind: ClusterRole
       name: view
     subjects:
       - kind: ServiceAccount
         namespace: monitoring
         name: event-exporter
     ---
     apiVersion: v1
     kind: ConfigMap
     metadata:
       name: event-exporter-cfg
       namespace: monitoring
     data:
       config.yaml: |
         logLevel: error
         logFormat: json
         route:
           groupBy: ['severity']
           groupWait: 10s
           groupInterval: 10s
           repeatInterval: 1m
           routes:
             - match:
                 - receiver: "dump"
             - match:
                 - receiver: "ms_teams"
         receivers:
           - name: "dump"
             stdout: {}
           - name: "ms_teams"
             teams:
               endpoint: "https://plataformaconexasaude.webhook.office.com/webhookb2/8f4106da-d71f-4395-80bf-dbbc797d3dea@6583c910-aeb0-412d-be99-2a299c89f3ef/IncomingWebhook/21e8f044dd974a39901d330c256836d7/ee1c26d2-ac91-4128-8d62-930b49525743"
               layout: # Optional layout configuration
     ---
     apiVersion: apps/v1
     kind: Deployment
     metadata:
       name: event-exporter
       namespace: monitoring
     spec:
       replicas: 1
       selector:
         matchLabels:
           app: event-exporter
           version: v1
       template:
         metadata:
           labels:
             app: event-exporter
             version: v1
         spec:
           serviceAccountName: event-exporter
           containers:
             - name: event-exporter
               image: ghcr.io/opsgenie/kubernetes-event-exporter:v0.11
               imagePullPolicy: IfNotPresent
               args:
                 - -conf=/data/config.yaml
               volumeMounts:
                 - mountPath: /data
                   name: cfg
           volumes:
             - name: cfg
               configMap:
                 name: event-exporter-cfg
     ```

   - Aplique o arquivo `event-exporter01.yml` com o comando:
     ```bash
     kubectl apply -f event-exporter01.yml
     ```
     ![image](https://github.com/user-attachments/assets/df918154-68f3-4ad2-afc4-2b961072c4f1)

  :white_check_mark: Notificação enviada para o Teams com sucesso.
   
  ![image](https://github.com/user-attachments/assets/8afed01b-01ef-40a9-ad86-83157a2d5ef0)

   - Para verificar se os pods estão funcionando, use os seguintes comandos:
     ```bash
     kubectl get pods -n monitoring
     kubectl logs event-exporter-588664898f-z9kwm -n monitoring
     ```
     ![image](https://github.com/user-attachments/assets/ab826284-d308-4ff0-bab5-c1b421fb558e)

## :gear: Ajustes Adicionais

- **Ajuste de Filtros:** Modifique as regras de `match` no ConfigMap para filtrar os eventos desejados.
- **Personalização de Mensagens:** Customize a estrutura de mensagens enviadas ao Teams no campo `layout`.

### Para um cluster já existente, siga estes passos:

- Crie o canal no Microsoft Teams.
- Atualize o endpoint no arquivo event-exporter01.yaml.
- Aplique o arquivo event-exporter01.yaml no cluster.
- Depois disso, verifique se as notificações estão funcionando e se os pods estão operando corretamente.

### Fonte:

- Este documento é baseado em informações e configurações encontradas no [repositório oficial do Kubernetes Event Exporter](https://github.com/resmoio/kubernetes-event-exporter).

## :handshake: Colaboradores DevOps / Infra-Soprar

|   [<img src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/1625799222011.jpg" width=115><br><sub>Marco Tulio</sub>](https://www.linkedin.com/in/mtuliog/)  |   [<img src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/john.jpg" width=115><br><sub>Jonathas Benevides</sub>](https://www.linkedin.com/in/jonathas-benevides-124757148/)  |   [<img src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/bruno1.jpg" width=115><br><sub>Bruno Baltazar</sub>](https://www.linkedin.com/in/bruno-baltazar-68a660201/)  |   [<img src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/Leandro.jpg" width=115><br><sub>Leandro Dionizio</sub>](https://www.linkedin.com/in/leandro-dionizio-ramos-it/)  |  
| :---: | :---: | :---: | :---: |

## :dart: Status
<p align="center">
  <img src="http://img.shields.io/static/v1?label=STATUS&message=%20Validado&color=GREEN&style=for-the-badge"/>
</p>
