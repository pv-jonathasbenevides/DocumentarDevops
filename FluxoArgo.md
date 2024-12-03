## 📁 **Pipeline Automatizado de Desenvolvimento e Deploy no AWS EKS**

<div align="center">
  <img src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/Argogiteks.gif" alt="Fluxo GitOps" style="width: 80%; border-radius: 10px; margin-bottom: 20px;">
</div>


 Clique neste [link](https://plataformaconexasaude-my.sharepoint.com/:v:/g/personal/jonathas_benevides_conexasaude_com_br/EWPhy8VL37ZNlShGk89b-S8BL8ArpyoSVdORqqv02WIhYQ?e=JDqbdK&nav=eyJwbGF5YmFja09wdGlvbnMiOnt9LCJyZWZlcnJhbEluZm8iOnsicmVmZXJyYWxBcHAiOiJUZWFtcyIsInJlZmVycmFsTW9kZSI6InZpZXciLCJyZWZlcnJhbFZpZXciOiJwb3N0cm9sbC1jb3B5bGluayIsInJlZmVycmFsUGxheWJhY2tTZXNzaW9uSWQiOiIzOWY2MGI4Yy1kMzE2LTRkOWUtOGJlOS03ZmIwMDE2NDRmNWY%3D) para acessar a apresentação.


### 🌟 **O fluxo de dados ocorre da seguinte forma:**

1. **🖥️ Desenvolvimento de Código:**  
   O código do aplicativo é desenvolvido utilizando qualquer IDE de sua escolha (ex.: IntelliJ, Visual Studio Code), proporcionando flexibilidade para os desenvolvedores.

2. **📂 Commit no Repositório GitHub:**  
   Após o desenvolvimento, o código do aplicativo é confirmado (*commit*) em um repositório GitHub, que serve como a fonte de verdade para o código-fonte e a configuração da infraestrutura.

3. **⚙️ GitHub Actions:**  
   Utilizamos o GitHub Actions para automatizar o processo de *build* do código. O pipeline cria uma imagem de contêiner do aplicativo, que é enviada para o Amazon Elastic Container Registry (ECR).

4. **📜 Atualização do Manifesto Kubernetes:**  
   O GitHub Actions atualiza automaticamente o arquivo de manifesto do Kubernetes, incluindo a nova versão da imagem do contêiner no ECR.

5. **🔄 Argo CD:**  
   O Argo CD, como operador GitOps, extrai as configurações do repositório Git e sincroniza o estado desejado com o cluster Kubernetes.

6. **🚀 Implantação no AWS EKS:**  
   O Argo CD realiza a implantação no Amazon Elastic Kubernetes Service (EKS), garantindo que o cluster esteja no estado desejado conforme descrito nos arquivos de configuração no GitHub.

---

### ➡️ **Fluxo GitOps Detalhado**

O GitOps segue os princípios de automação, controle de versão e consistência na gestão da infraestrutura. Aqui está o fluxo detalhado das tecnologias envolvidas:

- **🔗 GitHub:** Repositório central para o código-fonte e configuração do Kubernetes.  
- **🔧 GitHub Actions:** Ferramenta de CI/CD para automação do *build* e *deploy*.  
- **📦 Amazon ECR:** Repositório de contêineres para armazenar as imagens Docker.  
- **📋 Argo CD:** Ferramenta GitOps que realiza a sincronização entre o repositório Git e o cluster Kubernetes, fazendo *deploy* automático.  
- **☸️ AWS EKS:** Serviço de Kubernetes gerenciado pela AWS para orquestração de contêineres.  


### 📜 **Este Documento**

Este documento fornece uma visão geral do fluxo de GitOps utilizando GitHub, Argo CD e AWS EKS. Ele visa proporcionar uma compreensão básica da arquitetura do sistema e das tecnologias envolvidas. Para mais detalhes, será fornecido um fluxo mais detalhado abordando cada tecnologia e sua implementação em projetos específicos no ambiente AWS EKS.

📌 O objetivo é criar uma documentação completa e detalhada para que desenvolvedores, engenheiros e outras partes interessadas possam compreender o ambiente de forma clara e utilizar as tecnologias de maneira eficiente.

### :handshake: Colaboradores DevOps / Infra-Soprar

|   [<img src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/1625799222011.jpg" width=100><br><sub>Marco Tulio</sub>](https://conexasaude.atlassian.net/jira/people/712020:cb88c7b9-9833-4a28-baba-d5a9eafa9e99)  |   [<img src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/john.jpg" width=100><br><sub>Jonathas Benevides</sub>](https://conexasaude.atlassian.net/jira/people/712020:4c1d3cab-0e35-4f78-af3d-fee37e663fcb)  |   [<img src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/bruno1.jpg" width=100><br><sub>Bruno Baltazar</sub>](https://conexasaude.atlassian.net/jira/people/712020:28422e38-1ecf-4e77-9a1f-f407b75b58d4)  |   [<img src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/Leandro.jpg" width=100><br><sub>Leandro Dionizio</sub>](https://conexasaude.atlassian.net/jira/people/712020:9722f6f3-64fe-4539-86db-1ffbff1c9103/)  |   [<img src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/joao.jpeg" width=100><br><sub>João Henrique</sub>](https://conexasaude.atlassian.net/jira/people/712020:30b26422-52fa-41bd-941d-3ef0ad1774f7)  |   [<img src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/erick.jpeg" width=100><br><sub>Erick Lima</sub>](https://conexasaude.atlassian.net/jira/people/712020:ebb2e4a1-ab0a-4735-88d0-aaac6d0b871a)  |   [<img src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/tiago.jpeg" width=100><br><sub>Tiago Guglielmelli</sub>](https://conexasaude.atlassian.net/jira/people/712020:fe94dc12-ee56-4bd0-8e9e-1dd3abe4312f)  |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |

## :dart: Status
<p align="center">
  <img src="http://img.shields.io/static/v1?label=STATUS&message=%20Validado&color=GREEN&style=for-the-badge"/>
</p>

