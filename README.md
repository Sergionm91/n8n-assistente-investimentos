# 🤖 Assistente Automatizado de Investimentos com RPA, n8n e IA Generativa

[![n8n Workflow](https://img.shields.io/badge/n8n-Workflow-orange?logo=n8n)](https://n8n.io/)
[![Python](https://img.shields.io/badge/Python-3.x-blue?logo=python)](https://www.python.org/)
[![Google Gemini](https://img.shields.io/badge/AI-Google%20Gemini-4285F4?logo=google)](https://ai.google.dev/)
[![Status](https://img.shields.io/badge/Status-Desafio%20Completo-brightgreen)](#)

Este projeto foi desenvolvido como solução para o desafio de projeto da **DIO (Digital Innovation One)**, aplicando conceitos de **RPA (Robotic Process Automation)**, **Orquestração de Workflows no n8n** e **IA Generativa (LLM)** para criar um assistente automatizado de recomendação de investimentos.

---

## 🎯 Objetivo do Projeto

Construir um pipeline de comunicação financeira automatizado e personalizado de ponta a ponta:
1. **Extração de Dados (RPA):** Coleta automática de dados de clientes simulados via Web Scraping com Python.
2. **Ingestão & Orquestração:** Envio em tempo real dos dados via Webhook para um workflow no n8n.
3. **Enriquecimento & Cruzamento:** Download de opções de investimentos em formato CSV e associação por perfil de investidor (*Conservador*, *Moderado* ou *Arrojado*).
4. **Personalização com IA Generativa:** Utilização da API do **Google Gemini** para redação de mensagens personalizadas e objetivas.
5. **Validação & Entrega:** Verificação do formato do e-mail via Regex e envio da recomendação diretamente para a caixa de entrada do cliente por SMTP.

---

## 🏗️ Arquitetura da Solução

```
 ┌────────────────┐      HTTP / Scraping      ┌──────────────────┐
 │  GitHub Pages  │ ────────────────────────> │   RPA (Python)   │
 │  (docs/index)  │                           │   BeautifulSoup  │
 └────────────────┘                           └────────┬─────────┘
                                                       │
                                                 Webhook (POST)
                                                       │
                                                       ▼
 ┌───────────────────────────────────────────────────────────────┐
 │                        Workflow no n8n                        │
 │                                                               │
 │  ┌─────────────┐       ┌──────────────┐      ┌─────────────┐  │
 │  │   Webhook   │ ────> │ HTTP Request │ ───> │  Nó Code    │  │
 │  │  (Entrada)  │       │  (data.csv)  │      │ (Cruzamento)│  │
 │  └─────────────┘       └──────────────┘      └──────┬──────┘  │
 │                                                     │         │
 │  ┌─────────────┐       ┌──────────────┐             │         │
 │  │ Envio Email │ <──── │   Nó Code    │ <───────────┘         │
 │  │   (SMTP)    │       │ (Limpeza AI) │                       │
 │  └─────────────┘       └──────▲───────┘                       │
 │                               │                               │
 │                       ┌───────┴──────┐                        │
 │                       │ GoogleGemini │                        │
 │                       │   (LLM AI)   │                        │
 │                       └──────────────┘                        │
 └───────────────────────────────────────────────────────────────┘
```

---

## 🛠️ Tecnologias e Ferramentas

- **Linguagem & Web Scraping:** Python 3, Jupyter Notebooks / Google Colab, `requests`, `BeautifulSoup4`.
- **Orquestração de Processos:** [n8n](https://n8n.io/) (Nós: Webhook, HTTP Request, Code JS, Google Gemini, IF/Regex, EmailSend).
- **Inteligência Artificial:** API Google Gemini (`models/gemini-flash-latest`).
- **Hospedagem & Dados estáticos:** GitHub Pages (HTML de clientes e `data.csv` com produtos de investimento).
- **Notificação:** Protocolo SMTP para envio dinâmico de e-mails.

---

## 📂 Estrutura do Repositório

```text
.
├── docs/
│   ├── index.html               # Página HTML simulando cadastro de clientes
│   └── data.csv                 # Tabela com opções e produtos de investimento
├── n8n/
│   └── workflow.json            # Workflow do n8n exportado em JSON (Pronto para importação)
├── src/
│   └── extrair_clientes.ipynb   # Script Python (RPA) para extração e disparo do Webhook
└── README.md                    # Documentação do projeto
```

---

## 🚀 Como Executar o Projeto

### Pré-requisitos
- Instância do **n8n** (Cloud ou Self-hosted via Docker).
- Conta no **Google AI Studio** para obtenção da chave de API do Gemini.
- Servidor de e-mail com suporte a SMTP (ex: Gmail com *App Password* ou Mailtrap).

---

### Passo 1: Configurar e Importar o Workflow no n8n
1. Abra o seu n8n e selecione **Import from File**.
2. Importe o arquivo `n8n/workflow.json` deste repositório.
3. Configure as Credenciais no n8n:
   - **Google PaLM / Gemini API:** Insira sua chave de API do Google AI Studio.
   - **SMTP:** Configure os dados do servidor de envio de e-mails.
4. Salve e ative o nó **Webhook** (copie a URL gerada em modo `Production` ou `Test`).

---

### Passo 2: Executar o Script de RPA
1. Abra o notebook `src/extrair_clientes.ipynb` no **Google Colab** ou no ambiente Python local.
2. Atualize a variável com a URL do seu Webhook do n8n:
   ```python
   WEBHOOK_URL = "https://seu-n8n.com/webhook/Clientes"
   ```
3. Execute todas as células do notebook. O script irá:
   - Fazer scraping dos dados na página de testes.
   - Estruturar a lista de clientes em formato JSON.
   - Enviar a requisição `POST` para o n8n.

---

### Passo 3: Processamento Automatizado e Resultado
- O n8n receberá os dados do cliente e fará o download automatizado do arquivo `docs/data.csv`.
- O nó de código em JavaScript cruza os perfis em lote.
- O **Google Gemini** é acionado individualmente para redigir uma mensagem curta, empática e alinhada às finanças de cada cliente.
- O e-mail do cliente é validado via **Expressão Regular (Regex)** e a mensagem personalizada é entregue via SMTP.

---

## 💡 Recursos Implementados & Diferenciais

- **Processamento em Lote (Batching):** O fluxo é preparado para receber listas de múltiplos clientes e processar interativamente cada recomendação individualmente.
- **Tratamento de Saída da LLM:** Script personalizado em JavaScript para sanitização de markdown e blocos de código (` ```json `) retornados pelo modelo de IA.
- **Validação de Dados:** Nó condicional (`If`) no n8n com verificação Regex para evitar falhas no envio de e-mails sintaticamente incorretos.

---

## 📝 Licença

Este projeto foi construído para fins educacionais como parte do bootcamp/desafio da **Digital Innovation One**. Sinta-se à vontade para utilizar e aprimorar o código!
