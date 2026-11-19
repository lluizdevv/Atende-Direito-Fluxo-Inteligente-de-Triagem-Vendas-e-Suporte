# 🤖 Atende Direito — Fluxo Inteligente de Triagem, Vendas e Suporte

Fluxo conversacional automatizado desenvolvido para a plataforma **Atende Direito** (`adflow` v1), responsável pela recepção, identificação de perfil de contato, qualificação comercial com integração CRM/MCP, abertura de chamados de suporte e transbordo humano inteligente.

---

## 📋 Visão Geral da Arquitetura

O workflow atua como um subfluxo conversacional (`subflow_callable`) acionado por eventos de recebimento de mensagens (`message.received`), aplicando controle de *debounce* (2 segundos / lote máximo de 10 mensagens) para agrupar mensagens picadas enviadas pelo usuário antes de processar a resposta.

### Principais Funcionalidades

* **Segregação Inteligente de Leads:** Identifica automaticamente se o contato é novo ou recorrente com base nas etiquetas (*tags*) vinculadas ao perfil.
* **Menus Interativos & Resiliência:** Apresenta botões de resposta rápida (*Quick Replies*) e possui contador de tentativas inválidas (`flow.tentativas > 2`) para novos contatos, transferindo automaticamente para um atendente caso o usuário não consiga navegar.
* **Funil Comercial Automatizado (Vendas):**
  * Apresenta catálogo de 5 soluções/produtos.
  * Coleta e valida sintaticamente o e-mail profissional do lead (verificação de `@`).
  * Cria o card de oportunidade diretamente no CRM (`create_business`) e associa o produto escolhido via ferramenta **MCP** (`associateProduct`).
* **Triagem de Suporte Técnico:** Categoriza a dificuldade enfrentada (*Acesso*, *Lentidão* ou *Outros*), registra o ticket no pipeline do CRM e encaminha ao departamento de suporte.
* **Roteamento Financeiro e Transbordo Humano:** Direciona solicitações financeiras ou pedidos de atendimento humano aos respectivos departamentos, aplicando tags de controle e pausando a automação por 2 horas (`PT2H`) quando necessário.

---

## 🗂️ Estrutura do Repositório

```text
.
├── workflow_atendedireito.json   # Definição completa do fluxo (36 nós) pronta para importação
└── README.md                  # Documentação técnica do projet
