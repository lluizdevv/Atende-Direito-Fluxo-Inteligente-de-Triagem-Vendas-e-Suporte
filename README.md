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
```

## 🔄 Etapas do Fluxo
Recepção e Classificação (n_start → if_else)

* 1 **Filtra mensagens de entrada pela tag de canal.**

* 2 **Verifica se o contato possui a tag novo_contato** (promovendo-o a Recorrente e limpando tags de sessões anteriores) ou se já é Recorrente. Caso contrário, inicia o onboarding solicitando o nome.

* 3 **Menus de Atendimento** (menu_contato_novo / menu_contato_recorrente)
```text
1. Vendas e Produtos → Segue para o catálogo comercial.

2. Suporte Técnico → Segue para triagem de incidentes.

3. Financeiro → Aplica tag financeira e transfere ao departamento responsável.

4. Falar com atendente → Pausa a automação por 2 horas e transfere para atendimento geral.
```

* 4 **Qualificação Comercial & MCP** (produtos → mcp → confirmacao_recebimento)

* 5 **Mapeia o ID do produto escolhido** (flow.id_produto_escolhido), valida o e-mail (flow.emai_cliente), cria o card no CRM e executa a chamada MCP associateProduct vinculando o produto ao negócio recém-criado.

* 6 **Suporte e Encerramento** (suporte → transferencia_suporte → n_end)

* 7 **Registra o relato** (flow.relato_problema), cria card no estágio de suporte do CRM e transfere a conversa.

## 👤 Desenvolvedores
- Rafaela Tavares
- Luiz Vinicius
