# Triagem Inteligente de Atendimentos com n8n

Automação de triagem de mensagens de clientes utilizando **n8n**, **IA generativa**, **Google Sheets** e **Gmail**.

O fluxo recebe mensagens por webhook, interpreta o conteúdo com IA, classifica o atendimento por categoria, urgência e sentimento, registra o histórico automaticamente e dispara alertas por e-mail quando uma solicitação é considerada urgente.

## Objetivo

Reduzir o trabalho manual de triagem de mensagens e permitir que solicitações críticas sejam identificadas e encaminhadas com mais rapidez.

## Arquitetura

Fluxo principal:

```text
Webhook
  ↓
Extrair Mensagem
  ↓
Classificar Atendimento com IA
  ↓
Estruturar Resultado
  ↓
É Urgente?
  ├── Sim → Preparar Alerta → Google Sheets → Gmail
  └── Não → Preparar Registro → Google Sheets
```

## Classificação realizada pela IA

Cada mensagem recebida é transformada em dados estruturados contendo:

* categoria
* urgência
* sentimento
* resumo
* ação recomendada

Exemplo:

```json
{
  "categoria": "financeiro",
  "urgencia": "alta",
  "sentimento": "negativo",
  "resumo": "Cliente relata cobrança duplicada e solicita resolução no mesmo dia.",
  "acao_recomendada": "Encaminhar para o setor financeiro para verificar a cobrança e avaliar o estorno."
}
```

## Tecnologias utilizadas

* n8n
* Webhooks / HTTP POST
* Groq API
* GPT-OSS 20B
* Structured Output Parser
* Google Sheets API
* Gmail API
* OAuth 2.0
* JSON

## Funcionalidades

* Recebimento de mensagens via webhook
* Normalização da entrada
* Classificação automática com IA
* Saída estruturada em JSON
* Identificação de urgência
* Registro automático no Google Sheets
* Histórico de atendimentos
* Envio automático de e-mail para casos urgentes
* Separação entre fluxo urgente e fluxo normal

## Exemplos de testes

### Cobrança duplicada

Entrada:

```text
Cliente com cobrança duplicada, precisa resolver hoje.
```

Resultado:

```text
Categoria: financeiro
Urgência: alta
Sentimento: negativo
```

A solicitação é registrada no histórico e gera um alerta por e-mail.

### Interesse comercial

Entrada:

```text
Gostaria de saber quais planos estão disponíveis e os respectivos preços.
```

Resultado:

```text
Categoria: comercial
Urgência: baixa
Sentimento: neutro
```

A solicitação é registrada normalmente, sem disparo de alerta.

### Indisponibilidade do sistema

Entrada:

```text
O sistema caiu e estamos sem conseguir trabalhar, preciso de ajuda agora.
```

Resultado:

```text
Categoria: suporte
Urgência: alta
Sentimento: negativo
```

O atendimento é registrado e a equipe recebe automaticamente um alerta.

## Decisões de arquitetura

A classificação por IA foi isolada do restante do processo para reduzir o acoplamento com um provedor específico.

Isso permite substituir o modelo de linguagem sem alterar a lógica principal da automação.

Para esta implementação foi utilizado um modelo de menor porte, suficiente para uma tarefa de classificação estruturada, priorizando:

* baixa latência
* menor consumo de recursos
* simplicidade
* possibilidade de substituição do provedor

O fluxo utiliza dados estruturados em vez de respostas livres da IA, permitindo que os nós seguintes tomem decisões deterministicamente.

## Possíveis melhorias

* Tratamento automático de rate limit e indisponibilidade da API
* Retry com backoff
* Fallback para outro modelo de linguagem
* Persistência em banco SQL
* Dashboard de métricas dos atendimentos
* Integração com CRM
* Encaminhamento automático por departamento

## Segurança

Credenciais, tokens OAuth e chaves de API não fazem parte do repositório.

As integrações devem ser configuradas individualmente no ambiente n8n.

## Autor

Projeto desenvolvido como estudo prático de automação de processos, integração de APIs e aplicação de IA em fluxos empresariais.
