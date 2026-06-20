# 🌤️ Boletim Climático Automatizado com Imagens de Satélite

Este projeto é uma solução de automação de coleta de dados meteorológicos e notificação em tempo real. Desenvolvido para a disciplina de Análise e Desenvolvimento de Sistemas do Senac, o objetivo é fundir web scraping de dados locais, consumo de APIs globais e manipulação de mapas interativos, entregando um painel climático completo (texto e imagem) diretamente no Telegram.

## 🚀 Funcionalidades

- **Coleta de dados locais:** Raspagem automatizada do painel da Embrapa CPACT usando Selenium.
- **Integração de APIs:** Consulta à API pública Open-Meteo para dados geolocalizados de Morro Redondo/RS.
- **Manipulação de DOM e Captura Visual:** Navegação automatizada no mapa interativo da REDEMET (Leaflet) usando `ActionChains` para arrastar a tela, aplicar zoom, ocultar menus e capturar a imagem de satélite da região sul.
- **Orquestração de fluxo:** Uso do n8n para receber cargas `multipart/form-data` (JSON + binário de imagem) e gerenciar a lógica de envio.
- **Notificação inteligente:** Disparo automático de boletins no Telegram contendo formatação Markdown e a captura de tela do satélite.

## 🎓 Requisitos do Projeto Atendidos

Para fins de avaliação acadêmica, este script implementa:
- **Múltiplas Páginas:** Interação com Embrapa e REDEMET.
- **Seletores Variados:** Uso de `By.ID`, `By.XPATH` e `By.CSS_SELECTOR`.
- **Sincronização Inteligente:** Implementação de `WebDriverWait` (Espera Explícita) para garantir o carregamento do DOM antes da execução, substituindo pausas fixas.
- **Tratamento de Exceções:** Blocos `try/except` na requisição HTTP para evitar quebras em caso de falha de conexão com o Webhook.
- **Modularização:** Código estruturado em funções de responsabilidade única (Extração, API, Navegação e Envio).

## 🛠️ Tecnologias Utilizadas

- **Linguagem:** Python 3.x
- **Automação web:** Selenium WebDriver
- **Integração de dados:** Requests (API REST e envio Multipart)
- **Orquestração:** n8n (container Docker)
- **Comunicação:** Telegram Bot API

## ⚙️ Como Configurar e Executar

### 1. Pré-requisitos
- Ter o Docker instalado para rodar o n8n.
- Ter o Python 3.x instalado no sistema. É recomendado o uso de um ambiente virtual (`venv`):

```bash
# Criação e ativação do ambiente virtual
python3 -m venv venv
source venv/bin/activate

# Instalação das dependências
pip install selenium requests
```

### 2. Configurando o n8n

Inicie o container do n8n:

```bash
docker run -d --name n8n -p 5678:5678 -v n8n_data:/home/node/.n8n docker.n8n.io/n8nio/n8n
```

Acesse http://localhost:5678, crie um Webhook (método POST) e um nó do Telegram configurado para a operação Send Photo. 

Habilite a leitura de arquivos binários com a chave imagem_satelite e adicione a formatação das variáveis no campo Caption. 

Ative o workflow.

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

A arquitetura do projeto opera em um fluxo de quatro etapas contínuas, garantindo a automação de ponta a ponta:

- **1. Extração de Dados:** O script Python executa o robô que acessa o site da Embrapa e extrai as métricas locais de Pelotas via seletores (Web Scraping). Em paralelo, o mesmo script consome as previsões de Morro Redondo via integração com a API JSON (Open-Meteo).
- **2. Captura Visual:** O robô redireciona a navegação para a REDEMET, onde manipula ativamente o mapa interativo (ajustando posição, aplicando zoom e ocultando menus) para capturar e salvar a imagem de satélite atualizada da região.
- **3. Fusão e Transporte:** Os dados textuais (temperaturas, umidade, etc.) são fundidos em um objeto único e empacotados junto com o arquivo da imagem física. Esse pacote completo é enviado para o Webhook do n8n via método POST (`multipart/form-data`).
- **4. Orquestração e Entrega:** O n8n atua como o motor lógico final, processando as variáveis recebidas e disparando uma notificação visual (foto + legenda) com formatação Markdown estruturada diretamente para o canal configurado no Telegram.