<div class="container">
  <div class="row">
    <div class="col-6" style="background-color:#C5E0ED;">
      <img src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/conexa-logo.svg" style="width:300px; padding-top:50px; padding-bottom:30px;">
    </div>
    <div class="col-6" style="background-color:#C5E0ED;">
    </div>
  </div>
  
<h1 align="center">:file_cabinet: Comando para deletar um cluster no Rancher</h1>

### :memo: Descrição
 
Comando para deletar um cluster no Rancher.

### :books: Funcionalidades

Ao usar esse comando no Rancher, é como se alguém pressionasse um botão de "excluir" para todo o conjunto de servidores e os aplicativos que estavam neles. Isso é uma ação permanente e tudo que estiver nesse conjunto será perdido para sempre. Por isso, é importante ter cuidado ao usar essa opção e, se necessário, fazer uma cópia de segurança (backup) antes de excluir. Lembre-se de que apenas pessoas autorizadas devem realizar essa ação para evitar problemas acidentais.

### :wrench: Tecnologias utilizadas

Tecnologia: ``RANCHER``

<div style="display: inline_block">
   
  <img align="center" alt="Rancher" src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/rancher.png" width="250px" />
   
</div><br/>   
   
### :rocket: Segue os comandos para utilizar no terminal do Rancher (kubernetes).

---
    kubectl get clusters.management.cattle.io  
---

OU

---
    kubectl get clusters.management.cattle.io --all-namespaces -o wide
---

- Este comando é usado para listar os clusters gerenciados pelo Rancher no Kubernetes.

 Obs: O parâmetro "--all-namespaces" indica que você deseja listar recursos de todos os namespaces, e "-o wide" solicita uma saída detalhada, mostrando informações adicionais sobre os clusters.

Exemplo de saída:

![image](https://github.com/conexasaude/pv-documentar-devops/assets/122643683/e5aede93-2a9b-4425-a2db-c359729f75a7)

---
    export CLUSTERID="xxxxxxxxx"
---

- Este comando é usado para definir uma variável de ambiente chamada CLUSTERID, que deve ser substituída pelo ID do cluster que você deseja manipular em etapas subsequentes.

Exemplo: Se o ID do cluster que você deseja manipular é "abcdefghij", você executaria o seguinte comando:

![image](https://github.com/conexasaude/pv-documentar-devops/assets/122643683/669d191a-5665-4446-9647-5d2e67457afe)


---
    kubectl patch clusters.management.cattle.io $CLUSTERID -p '{"metadata":{"finalizers":[]}}' --type=merge
---

- Este comando é usado para remover os finalizadores (finalizers) de um cluster gerenciado pelo Rancher no Kubernetes, permitindo que ele seja excluído mesmo se houver recursos finais pendentes.


---
    kubectl delete clusters.management.cattle.io $CLUSTERID
---

- Este comando é usado para excluir um cluster gerenciado pelo Rancher no Kubernetes. Ele solicitará uma confirmação antes de prosseguir com a exclusão do cluster.

Nota: Tenha cuidado ao executar este comando, pois a exclusão do cluster é uma ação irreversível e resultará na perda permanente de todos os recursos do cluster.

Exemplo: Substitua "abcdefghij" pelo ID do cluster real que você deseja excluir.

![image](https://github.com/conexasaude/pv-documentar-devops/assets/122643683/c923fd7e-b93e-41d0-b995-3ed948ce9bdb)

 :bulb: Lembre-se de que a manipulação de clusters é uma tarefa sensível e deve ser executada com cuidado para evitar a perda acidental de dados ou recursos importantes. Certifique-se de ter um backup adequado e confirme todas as ações antes de prosseguir.

## :handshake: Colaboradores DevOps / Infra-Soprar

|   [<img src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/1625799222011.jpg" width=115><br><sub>Marco Tulio</sub>](https://www.linkedin.com/in/mtuliog/)  |   [<img src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/john.jpg" width=115><br><sub>Jonathas Benevides</sub>](https://www.linkedin.com/in/jonathas-benevides-124757148/)  |   [<img src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/bruno1.jpg" width=115><br><sub>Bruno Baltazar</sub>](https://www.linkedin.com/in/bruno-baltazar-68a660201/)  |   [<img src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/Leandro.jpg" width=115><br><sub>Leandro Dionizio</sub>](https://www.linkedin.com/in/leandro-dionizio-ramos-it/)  |  
| :---: | :---: | :---: | :---: |

## :dart: Status

<p align="center">
<img src="http://img.shields.io/static/v1?label=STATUS&message=%20Validado&color=GREEN&style=for-the-badge"/>
</p>
