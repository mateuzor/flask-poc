# CI/CD com Jenkins e GitHub (Projeto Flask)

Este repositório demonstra uma integração completa entre GitHub, Jenkins e Docker, incluindo:

- Execução de testes com Pytest
- Build de imagem Docker
- Pipeline automatizado no Jenkins
- Gatilho via Webhook do GitHub após push/merge

---

## 🔧 Requisitos

Certifique-se de ter instalado no seu sistema:

- [Docker](https://www.docker.com/)
- [Docker Compose](https://docs.docker.com/compose/)
- [Ngrok](https://ngrok.com/)
- Conta no [GitHub](https://github.com/)
- Projeto Jenkins (local) já iniciado

---

## 🧱 Estrutura dos Repositórios

- [`jenkins-poc`](https://github.com/mateuzor/jenkins-poc): contém o Jenkins e o `docker-compose.yml`
- [`flask-poc`](https://github.com/mateuzor/flask-poc): aplicação Flask e pipeline (`Jenkinsfile`)

---

## 🚀 Passo a passo para rodar o pipeline

### 1. Clone os repositórios

```bash
git clone https://github.com/mateuzor/jenkins-poc.git
git clone https://github.com/mateuzor/flask-poc.git
```

---

### 2. Suba o Jenkins com Docker

📁 No diretório do projeto `jenkins-poc`:

```bash
cd jenkins-poc
docker compose up
```

---

### 3. Configure o Jenkins pela interface web

1. Acesse `http://localhost:8080`
2. Finalize o setup inicial com o token gerado
3. Instale os plugins sugeridos
4. Crie um pipeline com:
   - Nome: `flask-pipeline`
   - Tipo: _Pipeline_
   - Marque: _GitHub project_ e insira o link do `flask-poc`
   - Em _Pipeline script from SCM_:
     - SCM: Git
     - URL: `https://github.com/mateuzor/flask-poc.git`
     - Branch: `*/main`
     - **Desmarque** _Lightweight checkout_

---

### 4. Instale os plugins caso necessários

Acesse `http://localhost:8080/pluginManager/installed` e verifique:

- Git plugin
- GitHub plugin
- GitHub API plugin
- GitHub Branch Source
- Pipeline
- Docker Pipeline

---

### 5. Configure acesso ao Docker host

📁 No `docker-compose.yml` do `jenkins-poc`, inclua:

```yaml
volumes:
  - /var/run/docker.sock:/var/run/docker.sock
```

---

### 6. Exponha o Jenkins com Ngrok

📁 No terminal, execute:

```bash
ngrok http 8080
```

Copie a URL gerada (`https://<subdomínio>.ngrok-free.app`)

---

### 7. Configure o Webhook no GitHub

1. Vá até o repositório `flask-poc` > Settings > Webhooks > Add webhook
2. Insira:
   - **Payload URL**: `https://<seu-ngrok>.ngrok-free.app/github-webhook/`
   - **Content type**: `application/json`
   - Marque apenas o evento: **Let me select individual events.**
   - Selecione **Pushes** & **Pull Requests**
   - Salve

---

### 8. Teste o gatilho automático

📁 No diretório `flask-poc`, faça um commit:

```bash
touch test-webhook.txt
git add .
git commit -m "test webhook"
git push origin main
```

Verifique no Jenkins se o pipeline foi executado automaticamente.

https://github.com/user-attachments/assets/3f820077-da79-413e-9273-ec883348e740

---

## ✅ O que o Pipeline faz

O `Jenkinsfile` no repositório `flask-poc` define:

1. **Install & Test**

   - Usa imagem `python:3.11`
   - Instala dependências
   - Executa testes com Pytest
   - Gera relatório `test-results.xml`

2. **Build Docker Image**
   - Usa `docker build`
   - Tag: `flask-app:<build_number>`

---

## 🧪 Rodando Local

📁 No `flask-poc`, rode:

```bash
python3 -m venv .venv
pip install -r requirements.txt

python app.py -> executa aplicação
pytest -> roda os testes
```
