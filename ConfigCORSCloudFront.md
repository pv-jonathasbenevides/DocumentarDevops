<div class="container">
  <div class="row">
    <div class="col-6" style="background-color:#C5E0ED;">
      <img src="https://github.com/conexasaude/pv-documentar-devops/blob/master/img/conexa-logo.svg" style="width:300px; padding-top:50px; padding-bottom:30px;">
    </div>
    <div class="col-6" style="background-color:#C5E0ED;">
    </div>
  </div>


<div align="center">
  <h1>🔒 Configuração CORS no CloudFront — Hospital Digital</h1>
  <p>Integração via Module Federation com o Paciente</p>
</div>

---

## :dart: Status
<p align="center">
  <img src="http://img.shields.io/static/v1?label=STATUS&message=%20Validado&color=GREEN&style=for-the-badge"/>
</p>

---

## :book: Por que essa configuração é necessária?

O Hospital Digital (HD) está sendo exposto como **microfrontend (MF)** via **Module Federation**.

Quando o usuário acessa o site do Paciente (`paciente.conexasaude.com.br`), o browser tenta carregar o arquivo `remoteEntry.js` diretamente do domínio do Hospital Digital (`hospital-digital.conexasaude.com.br`). Como são **domínios diferentes**, o browser aplica a política de segurança **Same-Origin Policy** e **bloqueia a requisição** — a menos que o servidor de origem (HD) autorize explicitamente.

Essa autorização é feita através dos **headers CORS** (`Access-Control-Allow-Origin`). Como é o CloudFront do HD que serve o recurso, é ele quem precisa retornar esses headers — não o CloudFront do Paciente.

```
Browser do usuário
       │
       ├── acessa ──► paciente.conexasaude.com.br  (site do Paciente)
       │
       └── carrega ──► hospital-digital.conexasaude.com.br/remoteEntry.js
                              │
                        CloudFront do HD
                        precisa responder com:
                        Access-Control-Allow-Origin: https://paciente.conexasaude.com.br ✅
```

---

## :clipboard: Pré-Requisitos

- Acesso ao console da AWS com permissão no CloudFront
- IDs das distribuições CloudFront do Hospital Digital (HML e PROD)

---

## :hammer_and_wrench: O que será configurado

| Recurso | Nome | Finalidade |
|---|---|---|
| Origin Request Policy | `HD-Forward-Origin-Header` | Encaminha o header `Origin` para a origem |
| Response Headers Policy | `HD-CORS-RemoteEntry` | Retorna `Access-Control-Allow-Origin` refletindo a origem da requisição |
| Cache Behavior `/remoteEntry.js` | — | Aplica as policies no path principal do MF |
| Cache Behavior `/assets/*` | — | Aplica as policies nos assets carregados pelo MF |

> **Por que encaminhar o header `Origin`?**
> Sem isso, o CloudFront faz cache de uma única resposta sem o header CORS. Com o `Origin` na cache key, ele diferencia requisições por origem e retorna o header correto para cada uma.

---

## :footprints: Passo a Passo

### 1. Criar a Origin Request Policy

1. Acesse o console da AWS → **CloudFront**
2. No menu lateral esquerdo, clique em **Policies**
3. Aba **Origin request** → **Create origin request policy**

| Campo | Valor |
|---|---|
| Name | `HD-Forward-Origin-Header` |
| Headers | **Include the following headers** → adicionar: `Origin` |
| Cookies | **None** |
| Query strings | **None** |

Clique em **Create**.

---

### 2. Criar a Response Headers Policy

1. Ainda em **Policies**, vá na aba **Response headers**
2. Clique em **Create response headers policy**

| Campo | Valor |
|---|---|
| Name | `HD-CORS-RemoteEntry` |

Desça até a seção **Cross-origin resource sharing (CORS)** e ative o toggle **Configure CORS**.

| Campo CORS | Valor |
|---|---|
| Access-Control-Allow-Origins | **Customize** → adicionar: `https://*.conexasaude.com.br` e `http://localhost:3000` |
| Access-Control-Allow-Headers | **All headers** |
| Access-Control-Allow-Methods | Marcar **GET** e **HEAD** |
| Access-Control-Allow-Credentials | **Desmarcado** |
| Origin override | **Desmarcado** ⚠️ |

> **Por que `Origin override` deve estar desmarcado?**
> Com ele desmarcado, o CloudFront reflete a origem **exata** da requisição no header de resposta (ex: `https://paciente.conexasaude.com.br`), em vez de retornar o wildcard literal `*.conexasaude.com.br` — que os browsers não aceitam.

Clique em **Create**.

---

### 3. Configurar os Behaviors nas Distribuições

Repita os passos abaixo nas duas distribuições: **HML** e **PROD**.

1. CloudFront → **Distributions** → clique na distribuição
2. Aba **Behaviors** → **Create behavior**

> ⚠️ **Não edite o Default behavior (`*`)**. Crie um novo behavior específico para cada path.

#### Behavior 1 — `/remoteEntry.js`

| Campo | Valor |
|---|---|
| Path pattern | `/remoteEntry.js` |
| Origin | mesma origem do Default behavior |
| Viewer protocol policy | **Redirect HTTP to HTTPS** |
| Allowed HTTP methods | **GET, HEAD** |
| **Cache policy** | `CachingOptimized` |
| **Origin request policy** | `HD-Forward-Origin-Header` |
| **Response headers policy** | `HD-CORS-RemoteEntry` |

> ⚠️ Em **Cache key and origin requests**, selecione **Cache policy and origin request policy (recommended)**. A opção **Legacy cache settings** não exibe o campo de Origin Request Policy.

#### Behavior 2 — `/assets/*`

| Campo | Valor |
|---|---|
| Path pattern | `/assets/*` |
| Origin | mesma origem do Default behavior |
| Viewer protocol policy | **Redirect HTTP to HTTPS** |
| Allowed HTTP methods | **GET, HEAD** |
| **Cache policy** | `CachingOptimized` |
| **Origin request policy** | `HD-Forward-Origin-Header` |
| **Response headers policy** | `HD-CORS-RemoteEntry` |

> **Por que `/assets/*` também precisa?**
> O Module Federation não carrega apenas o `remoteEntry.js`. Após o bootstrap, o browser faz requisições cross-origin para os chunks em `/assets/`. Sem CORS nesse path, o browser bloqueia o carregamento dos módulos e a integração quebra.

Após salvar, aguarde o status da distribuição mudar de **Deploying** para **Enabled** (~5 minutos).

---

## :white_check_mark: Validação

### Windows (PowerShell)

**HML:**
```powershell
$r = Invoke-WebRequest -Uri "https://hml-hospital-digital.conexasaude.com.br/remoteEntry.js" -Headers @{ "Origin" = "https://qa-paciente.conexasaude.com.br" } -Method GET -UseBasicParsing
$r.Headers
```

**PROD:**
```powershell
$r = Invoke-WebRequest -Uri "https://hospital-digital.conexasaude.com.br/remoteEntry.js" -Headers @{ "Origin" = "https://paciente.conexasaude.com.br" } -Method GET -UseBasicParsing
$r.Headers
```

### Linux / Git Bash

**HML:**
```bash
curl -sI -H "Origin: https://qa-paciente.conexasaude.com.br" \
  https://hml-hospital-digital.conexasaude.com.br/remoteEntry.js \
  | grep -i access-control
```

**PROD:**
```bash
curl -sI -H "Origin: https://paciente.conexasaude.com.br" \
  https://hospital-digital.conexasaude.com.br/remoteEntry.js \
  | grep -i access-control
```

### Resultado Esperado

```
Access-Control-Allow-Origin: https://[qa-]paciente.conexasaude.com.br
Vary: Origin
```

---

## :globe_with_meridians: Origens Autorizadas

| Origem | Ambiente |
|---|---|
| `https://*.conexasaude.com.br` | HML e PROD |
| `http://localhost:3000` | Desenvolvimento local |

---

## :warning: Pontos de Atenção

- **Não copiar os arquivos de configuração antigos** — edite manualmente se precisar ajustar policies existentes
- **Origin override desligado** é obrigatório para a reflexão de origem funcionar
- **Legacy cache settings** não deve ser usado — impede a configuração da Origin Request Policy
- Qualquer nova origem que precisar ser autorizada deve ser adicionada na Response Headers Policy `HD-CORS-RemoteEntry`

---

## :memo: Referências

- [AWS CloudFront — Configurar CORS](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/add-origin-response-headers.html)
- [Module Federation — Webpack](https://webpack.js.org/concepts/module-federation/)
- [MDN — Cross-Origin Resource Sharing (CORS)](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/CORS)
