<div class="container">
  <div class="row">
    <div class="col-6" style="background-color:#C5E0ED;">
      <img src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/conexa-logo.svg" style="width:300px; padding-top:50px; padding-bottom:30px;">
    </div>
    <div class="col-6" style="background-color:#C5E0ED;">
    </div>
  </div>

  

# Documentação de Implantação: ms-surveys

Esta documentação descreve o processo de build, deploy e a arquitetura de infraestrutura do microserviço **ms-surveys**, desenvolvido em [NestJS](https://docs.nestjs.com/), implantado em [Amazon EKS](https://docs.aws.amazon.com/eks/), com entrega contínua baseada em [GitHub Actions](https://docs.github.com/actions) e [Argo CD](https://argo-cd.readthedocs.io/). A gestão de segredos utiliza [AWS Secrets Manager](https://docs.aws.amazon.com/secretsmanager/) com sincronização para o cluster por meio do [External Secrets Operator](https://external-secrets.io/), e a mensageria é suportada por [RabbitMQ](https://www.rabbitmq.com/docs).

---

## 1. Visão Geral da Arquitetura

A aplicação **ms-surveys** segue uma arquitetura moderna orientada a containers, executada em Kubernetes no EKS e operada sob o modelo **GitOps**. O fluxo de entrega parte do repositório da aplicação, passa pelo pipeline de CI/CD para geração da imagem Docker e atualização dos manifestos Kubernetes, e é reconciliado no cluster pelo Argo CD. No ambiente analisado, a aplicação aparece como **Healthy** e **Synced**, com recursos como deployment, service, pods e segredo sincronizado via External Secrets. [Source](https://www.genspark.ai/api/files/s/BzIAEvts)

### Stack principal

- **Runtime:** [Node.js / NestJS](https://docs.nestjs.com/)
- **Orquestração:** [AWS EKS](https://docs.aws.amazon.com/eks/)
- **CI/CD:** [GitHub Actions](https://docs.github.com/actions) + [Argo CD](https://argo-cd.readthedocs.io/)
- **Mensageria:** [RabbitMQ](https://www.rabbitmq.com/docs)
- **Segredos:** [AWS Secrets Manager](https://docs.aws.amazon.com/secretsmanager/) via [External Secrets Operator](https://external-secrets.io/)

### Diagrama da aplicação no Argo CD

<img width="1492" height="803" alt="image" src="https://github.com/user-attachments/assets/8d7c027c-51c5-496d-837d-46181c72f0d3" />

> **Figura 1.** Visão gráfica da aplicação **ms-surveys** no Argo CD, indicando estado **Healthy** e **Synced**, além dos recursos Kubernetes relacionados.

---

## 2. Fluxo de CI/CD

O pipeline está definido no workflow **`build-deploy-nest.js`** e é disparado por **push** nas branches **develop**, **staging** e **main**.

### Etapas do pipeline

1. **Autenticação**
   - Configura as credenciais da AWS.
   - Realiza o login no **Amazon ECR**.

2. **Infra como Código (Docker)**
   - Faz o checkout do repositório de templates de containers da organização.

3. **Build e Push da imagem**
   - Gera uma imagem Docker **multi-plataforma** com alvo `linux/amd64`.
   - Publica a imagem no **Amazon ECR** do ambiente correspondente (**HML/STG** ou **PRD**).

4. **Tag da imagem**
   - A imagem é versionada no padrão:

   ```text
   ${BRANCH}.${GITHUB_SHA}
   ```

5. **Atualização de manifesto**
   - O pipeline clona o repositório **cnx_kubernetes**.
   - Atualiza a tag da imagem no arquivo **`deployment.yaml`** correspondente ao serviço.
   - Realiza o commit da alteração para que o repositório Git permaneça como fonte da verdade.

6. **Argo CD Sync**
   - Caso a aplicação ainda não exista no Argo CD, o pipeline pode criá-la automaticamente com base em um template.
   - Após a atualização do repositório de manifestos, o Argo CD detecta a mudança e sincroniza o estado desejado com o cluster.

### Resumo do fluxo

```text
Push em develop/staging/main
        ↓
GitHub Actions
        ↓
Build da imagem Docker
        ↓
Push para Amazon ECR
        ↓
Atualização do deployment.yaml no repositório cnx_kubernetes
        ↓
Argo CD detecta alteração
        ↓
Sync no EKS
        ↓
Nova versão do ms-surveys em execução
```

---

## 3. Infraestrutura Kubernetes

A aplicação é implantada no namespace **`conexa`** e utiliza recursos padrão de Kubernetes para execução, exposição de tráfego, escalabilidade e observabilidade.

### 3.1 Deployment e escalabilidade

O `Deployment` da aplicação utiliza estratégia de atualização do tipo **RollingUpdate**, permitindo implantações graduais com menor impacto durante releases. Além disso, a aplicação conta com **HPA** para escalar horizontalmente conforme carga de CPU e memória.

| Componente | Configuração |
|---|---|
| Estratégia de atualização | `RollingUpdate` |
| `maxSurge` | `50%` |
| `maxUnavailable` | `25%` |
| Réplicas via HPA | mínimo `2`, máximo `3` |
| CPU alvo | `80%` |
| Memória alvo | `85%` |

#### Regras de agendamento

- **Affinity:** preferência por instâncias **Spot**.
- **Anti-Affinity:** distribuição dos pods entre nodes distintos para aumentar resiliência.
- **Karpenter:** suporte ao provisionamento eficiente de capacidade no cluster. [Source](https://karpenter.sh/docs/)

### 3.2 Health checks

A aplicação expõe verificações de saúde para garantir inicialização correta e monitoramento contínuo do serviço.

| Tipo de probe | Objetivo | Configuração |
|---|---|---|
| `startupProbe` | Aguarda a inicialização da aplicação | endpoint `/surveys/health` |
| `livenessProbe` | Verifica se o container continua saudável | porta `3000` |
| `readinessProbe` | Verifica se o pod está pronto para receber tráfego | porta `3000` |

### 3.3 Networking

| Recurso | Configuração |
|---|---|
| Service | `ClusterIP` |
| Porta da aplicação | `3000` |
| Controlador de ingress | `cnx-nginx-internet` |
| Host HML | `hml-ms-surveys.conexasaude.com.br` |

---

## 4. Gestão de Configurações e Segredos

A aplicação depende de segredos para conexão com serviços externos, como banco de dados e broker de mensageria. Esses segredos são armazenados no **AWS Secrets Manager** e sincronizados para o cluster Kubernetes por meio do **External Secrets Operator**.

### 4.1 AWS Secrets Manager

Na captura fornecida, o segredo identificado para o ambiente de homologação é:

```text
secret/ms-surveys-hml
```

Esse segredo aparece no console do AWS Secrets Manager com criptografia gerenciada por `aws/secretsmanager`.

<img width="731" height="252" alt="image" src="https://github.com/user-attachments/assets/815629e7-6cfc-4c1b-a991-aa6d3809f1af" />


> **Figura 2.** Tela do AWS Secrets Manager exibindo o segredo **`secret/ms-surveys-hml`**. [Source](https://www.genspark.ai/api/files/s/YnIRSHlL)

### 4.2 External Secrets Operator

O cluster utiliza um recurso **ExternalSecret** para sincronizar automaticamente os dados do AWS Secrets Manager para um **Secret** nativo do Kubernetes.

| Recurso | Valor |
|---|---|
| Nome do ExternalSecret | `ms-surveys-env-hml` |
| Origem dos dados | AWS Secrets Manager |
| Segredo remoto | `secret/ms-surveys-hml` |
| Refresh interval | `30 segundos` |

Na visão do Argo CD, é possível observar a relação entre o recurso de **ExternalSecret** e o **Secret** materializado no cluster.

---

## 5. Dependências Externas

### 5.1 RabbitMQ

A aplicação **ms-surveys** depende de um cluster **RabbitMQ** para processamento assíncrono. O painel de gerenciamento do RabbitMQ permite acompanhar o estado operacional do broker, incluindo conexões, canais, exchanges, filas e consumidores.

Na captura fornecida, o painel mostra:

- **Connections:** `3`
- **Channels:** `3`
- **Exchanges:** `15`
- **Queues:** `2`
- **Consumers:** `6`

Também é possível observar que, no momento da captura, não havia mensagens pendentes (`Ready 0`, `Unacked 0`, `Total 0`).

<img width="720" height="452" alt="image" src="https://github.com/user-attachments/assets/855be13d-5e0b-4b58-b4e9-5d074c546d73" />


> **Figura 3.** Visão geral do RabbitMQ Management UI, com métricas de conexões, exchanges, filas e consumidores. [Source](https://www.genspark.ai/api/files/s/QVkOlscD)

### 5.2 Integração com segredos

As credenciais de acesso ao RabbitMQ devem ser injetadas no ambiente da aplicação por meio dos segredos sincronizados a partir do AWS Secrets Manager, evitando credenciais estáticas em manifesto.

---

## 6. Resumo operacional de implantação

O fluxo de implantação do **ms-surveys** pode ser resumido da seguinte forma:

1. O desenvolvedor realiza push em `develop`, `staging` ou `main`.
2. O workflow **`build-deploy-nest.js`** é disparado no GitHub Actions.
3. A imagem Docker da aplicação é construída e publicada no Amazon ECR.
4. O pipeline atualiza a tag da imagem no repositório **cnx_kubernetes**.
5. O Argo CD detecta a alteração e sincroniza os recursos no EKS.
6. O Kubernetes aplica a estratégia de rolling update.
7. O HPA mantém a aplicação entre 2 e 3 réplicas conforme carga.
8. Os segredos são consumidos via AWS Secrets Manager + External Secrets.
9. A aplicação se integra ao RabbitMQ para processamento assíncrono.

---

## 7. Checklist pós-deploy

```text
[ ] Imagem publicada corretamente no Amazon ECR
[ ] deployment.yaml atualizado no repositório cnx_kubernetes
[ ] Aplicação criada/sincronizada no Argo CD
[ ] Status da aplicação como Healthy e Synced
[ ] Pods distribuídos corretamente e probes saudáveis
[ ] Secret sincronizado via ExternalSecret
[ ] Conectividade com RabbitMQ validada
[ ] Host HML respondendo corretamente
```

---

## 8. Referências

- [NestJS Documentation](https://docs.nestjs.com/)
- [Amazon EKS Documentation](https://docs.aws.amazon.com/eks/)
- [GitHub Actions Documentation](https://docs.github.com/actions)
- [Argo CD Documentation](https://argo-cd.readthedocs.io/)
- [AWS Secrets Manager Documentation](https://docs.aws.amazon.com/secretsmanager/)
- [External Secrets Operator Documentation](https://external-secrets.io/)
- [RabbitMQ Documentation](https://www.rabbitmq.com/docs)
- [RabbitMQ Management Plugin](https://www.rabbitmq.com/docs/management)
