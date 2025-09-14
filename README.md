# Automação Conversacional Avançada para Instagram com n8n e IA

Este repositório contém o workflow de uma automação avançada construída em n8n, projetada para gerenciar interações no Instagram de ponta a ponta. O sistema utiliza uma arquitetura de múltiplos agentes de IA para capturar, qualificar e converter leads de forma 100% autônoma e humanizada.

---

## 🎯 Sobre o Projeto

O objetivo deste projeto é solucionar um dos maiores desafios para negócios no Instagram: escalar o atendimento personalizado sem perder a qualidade. A automação foi desenhada para um produto de e-commerce, assumindo o controle total das respostas a comentários e das conversas no Direct, transformando a plataforma em uma máquina de vendas e qualificação de leads que opera 24/7.

## ✨ Principais Funcionalidades

Este não é um simples chatbot. É um ecossistema de automação robusto com funcionalidades de nível profissional:

* **Arquitetura de Múltiplos Agentes de IA:**
    * **Agente de Primeiro Contato ("Batedor"):** Uma IA especialista em enviar a primeira DM, com lógica condicional para tratar leads quentes (foco em conversão direta) e leads mornos/frios (foco em engajamento).
    * **Agente de Conversação ("Negociador"):** Uma IA avançada que assume a conversa após a primeira resposta do lead, utilizando memória, uma base de conhecimento detalhada e um funil de vendas guiado para educar, tratar objeções e conduzir o cliente ao checkout.

* **Webhook Handling Profissional:**
    * Implementação do padrão **Dispatcher/Processor**, com um workflow dedicado a responder `200 OK` imediatamente à API da Meta, eliminando a ocorrência de webhooks duplicados.
    * **Lógica de Idempotência** com checagem no banco de dados para garantir que cada evento de comentário seja processado apenas uma vez.

* **Lógica de Conversa Guiada por Funil:**
    * O agente principal opera com base em um funil de 4 etapas (Diagnóstico > Solução > Objeções > Venda), guiando o lead de forma consultiva e natural.

* **Inteligência e Resiliência:**
    * Filtros inteligentes para descartar interações irrelevantes (ecos de mensagens da própria página, comentários aninhados).
    * Lógica de **debounce** com Redis para agrupar múltiplas mensagens de um usuário, otimizando custos e melhorando o contexto da IA.

## 🏗️ Arquitetura do Sistema

O sistema é dividido em dois workflows principais para garantir eficiência e escalabilidade:

1.  **Workflow 1: Recebedor (Dispatcher)**
    * **Função:** Receber todos os webhooks da Meta, responder `200 OK` imediatamente, filtrar eventos irrelevantes e disparar o workflow de processamento.
    * `[Webhook]` -> `[Filtros Iniciais]` -> `[Respond to Webhook]` -> `[Execute Workflow]`

2.  **Workflow 2: Trabalhador (Processor)**
    * **Função:** Executar toda a lógica de negócio: consultar o banco de dados, chamar as IAs, responder comentários e enviar DMs.
    * `[Start]` -> `[Consulta DB]` -> `[Lógica de IAs]` -> `[Ações no Instagram]` -> `[Update DB]`

## 🛠️ Tecnologias Utilizadas

* **Plataforma de Automação:** [n8n](https://n8n.io/) (Self-Hosted)
* **Inteligência Artificial:** Modelos de Linguagem da [OpenAI](https://openai.com/) (ou qualquer outra LLM compatível)
* **API:** [Meta Graph API (Instagram)](https://developers.facebook.com/docs/instagram-platform)
* **Banco de Dados:** [PostgreSQL](https://www.postgresql.org/) (para persistência de leads e memória de conversa)
* **Cache/Fila:** [Redis](https://redis.io/) (para a lógica de debounce de mensagens)

## ⚙️ Configuração

Para replicar este projeto, a configuração principal envolve a criação de credenciais e a definição de variáveis de ambiente. **Nenhuma chave de API ou token deve ser escrito diretamente no workflow.**

1.  **Configurar Credenciais no n8n:**
    * Crie credenciais para a API da OpenAI, PostgreSQL e Redis.
    * Crie uma credencial do tipo `Header Auth` para armazenar seu `Bearer Token` da API da Meta.

2.  **Configurar App na Meta for Developers:**
    * Crie um aplicativo e configure a API de Mensagens do Instagram.
    * Assine os webhooks para os campos `comments` e `messages`.
    * Aponte o webhook para a URL do seu workflow "Recebedor" no n8n.

3.  **Estrutura do Banco de Dados:**
    * O projeto utiliza duas tabelas principais: uma para gerenciar o estado dos `leads` e outra para o `historico_mensagens`. Os `schemas` podem ser inferidos a partir dos nós do Postgres no workflow.

## 🚀 Conceitos-Chave Demonstrados

Este projeto serve como um case prático dos seguintes conceitos de engenharia e automação:

* **Processamento Assíncrono:** Separação da recepção e do processamento de webhooks.
* **Idempotência:** Capacidade de processar o mesmo evento múltiplas vezes com o mesmo resultado final.
* **Gerenciamento de Estado:** Uso de um banco de dados externo para manter o estado da automação e das conversas.
* **Prompt Engineering Avançado:** Criação de prompts com persona, base de conhecimento, fluxo guiado e exemplos de few-shot para controlar o comportamento da IA.
* **Resiliência de Sistemas:** Implementação de filtros e lógicas para lidar com as inconsistências e casos de borda de APIs externas.
