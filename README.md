# 🌤️ Boletim Climático Automatizado

Este projeto é uma solução de automação de coleta de dados e notificação, desenvolvida como parte da disciplina de Análise e Desenvolvimento de Sistemas. O objetivo é integrar dados locais, via web scraping, com dados meteorológicos globais, via API, para fornecer boletins climáticos em tempo real.

## 🚀 Funcionalidades

- Coleta de dados: raspagem automatizada em tempo real do painel da Embrapa CPACT usando Selenium.
- Integração de APIs: consulta à API pública Open-Meteo para dados geolocalizados de Morro Redondo/RS.
- Orquestração de fluxo: uso do n8n para processar os dados e gerenciar o envio das notificações.
- Notificação inteligente: disparo automático de boletins formatados no Telegram para equipes de monitoramento.

## 🛠️ Tecnologias Utilizadas

- Linguagem: Python 3.x
- Automação web: Selenium WebDriver
- Integração de dados: Requests (API REST)
- Orquestração: n8n (container Docker)
- Comunicação: Telegram Bot API

## ⚙️ Como Configurar e Executar

### 1. Pré-requisitos

- Ter o Docker instalado para rodar o n8n.
- Ter o Python 3.x e as bibliotecas necessárias:

```bash
pip install selenium requests
```

### 2. Configurando o n8n

Inicie o container do n8n:

```bash
docker run -d --name n8n -p 5678:5678 -v n8n_data:/home/node/.n8n docker.n8n.io/n8nio/n8n
```

Acesse http://localhost:5678, crie seu workflow, adicione um Webhook com método POST e um nó do Telegram configurado com seu token e Chat ID.

Ative o workflow no botão Publish.

### 3. Executando o Robô

Clone este repositório:

```bash
git clone https://github.com/Wagner-V1eira/Boletim-Climatico.git
cd boletim-climatico-bot
```

Certifique-se de que a URL no script Python aponta para o endereço do seu Webhook:

```text
http://localhost:5678/webhook/boletim-clima
```

Execute o script ou abra o notebook no VS Code e rode as células sequencialmente.

## 🏗️ Arquitetura do Sistema

- O script Python executa o robô que acessa o site da Embrapa e extrai os dados via seletores.
- O mesmo script consome os dados de Morro Redondo via JSON/API.
- Os dados são fundidos em um objeto único e enviados para o Webhook do n8n via método POST.
- O n8n processa as informações e dispara uma mensagem estruturada com formatação Markdown para o canal configurado no Telegram.