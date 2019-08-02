---
title: Referência para tipos de gatilho e ação na linguagem de definição de fluxo de trabalho – aplicativos lógicos do Azure
description: Guia de referência para tipos de gatilho e ação na linguagem de definição de fluxo de trabalho para aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: reference
ms.date: 06/19/2019
ms.openlocfilehash: 0c7fbe209a20182ac2a17fcdb467d1f2a03183e5
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68370733"
---
# <a name="reference-for-trigger-and-action-types-in-workflow-definition-language-for-azure-logic-apps"></a>Referência para tipos de gatilho e ação na linguagem de definição de fluxo de trabalho para aplicativos lógicos do Azure

Esta referência descreve os tipos gerais usados para identificar gatilhos e ações na definição de fluxo de trabalho subjacente do aplicativo lógico, que é descrita e validada pela [linguagem de definição de fluxo de trabalho](../logic-apps/logic-apps-workflow-definition-language.md).
Para localizar gatilhos e ações de conectores específicos que você pode usar em seus aplicativos lógicos, consulte a lista na [visão geral](https://docs.microsoft.com/connectors/)dos conectores.

<a name="triggers-overview"></a>

## <a name="triggers-overview"></a>Visão geral de gatilhos

Cada fluxo de trabalho inclui um gatilho, que define as chamadas que instanciam e iniciam o fluxo de trabalho. Aqui estão as categorias gerais de gatilho:

* Um acionador de *polling*, que verifica o terminal de um serviço em intervalos regulares

* Um acionador *push*, que cria uma assinatura para um terminal e fornece um *URL de retorno de chamada* para que o terminal possa notificar o acionador quando o evento especificado acontecer ou os dados estiverem disponíveis. O gatilho aguarda a resposta do ponto de extremidade antes de disparar. 

Os gatilhos têm esses elementos de nível superior, embora alguns sejam opcionais:  
  
```json
"<trigger-name>": {
   "type": "<trigger-type>",
   "inputs": { "<trigger-inputs>" },
   "recurrence": { 
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "conditions": [ "<array-with-conditions>" ],
   "runtimeConfiguration": { "<runtime-config-options>" },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
},
```

*Necessária*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*trigger-name*> | Cadeia | O nome do gatilho | 
| <*tipo de gatilho*> | Cadeia | O tipo de gatilho como "Http" ou "ApiConnection" | 
| <*trigger-inputs*> | Objeto JSON | As entradas que definem o comportamento do gatilho | 
| <*time-unit*> | Cadeia | A unidade de tempo que descreve a frequência com que o gatilho é acionado: "Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês" | 
| <*number-of-time-units*> | Inteiro | Um valor que especifica com que frequência o acionador é disparado com base na frequência, que é o número de unidades de tempo a aguardar até que o acionador seja acionado novamente <p>Aqui estão os intervalos mínimos e máximos: <p>- Mês: 1 a 16 meses </br>- Dia: 1 a 500 dias </br>- Hora: 1 a 12.000 horas </br>- Minuto: 1 a 72.000 minutos </br>- Segundo: 1 a 9.999.999 segundos<p>Por exemplo, se o intervalo for 6, e a frequência for "Mês", a recorrência será a cada 6 meses. | 
|||| 

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*matriz com condições*> | Array | Uma matriz que contém uma ou mais [condições](#trigger-conditions) que determinam se o fluxo de trabalho deve ser executado. Disponível apenas para gatilhos. | 
| <*Opções de configuração de tempo de execução*> | Objeto JSON | Você pode alterar o comportamento do tempo de execução do acionador configurando as propriedades `runtimeConfiguration`. Para obter mais informações, consulte [Configurações de tempo de execução](#runtime-config-options). | 
| <*expressão splitOn*> | Cadeia | Para gatilhos que retornam uma matriz, você pode especificar uma expressão que [divide ou *debita*](#split-on-debatch)itens da matriz em várias instâncias de fluxo de trabalho para processamento. | 
| <*opção de operação*> | Cadeia | Você pode alterar o comportamento padrão definindo a `operationOptions` propriedade. Para obter mais informações, consulte [opções de operação](#operation-options). | 
|||| 

## <a name="trigger-types-list"></a>Lista de tipos de gatilho

Cada tipo de gatilho possui uma interface e entradas diferentes que definem o comportamento do gatilho. 

### <a name="built-in-triggers"></a>Gatilhos internos

| Tipo de gatilho | Descrição | 
|--------------|-------------| 
| [**HTTP**](#http-trigger) | Verifica ou *pesquisas* qualquer ponto de extremidade. Esse ponto de extremidade deve estar em conformidade com um contrato de gatilho específico usando um padrão assíncrono "202" ou retornando uma matriz. | 
| [**HTTPWebhook**](#http-webhook-trigger) | Cria um ponto de extremidade que pode ser chamado para seu aplicativo lógico, mas chama o URL especificado para registrar ou cancelar o registro. |
| [**Recurrence**](#recurrence-trigger) | Acionado com base em um agendamento definido. Você pode definir uma data e hora futura para acionar esse gatilho. Com base na frequência, você também pode especificar horários e dias para executar seu fluxo de trabalho. | 
| [**Request**](#request-trigger)  | Cria um ponto de extremidade que pode ser chamado para seu aplicativo lógico e também é conhecido como um acionador "manual". Por exemplo, consulte [Chamar, disparar ou aninhar fluxos de trabalho com pontos de extremidade HTTP](../logic-apps/logic-apps-http-endpoint.md). | 
||| 

### <a name="managed-api-triggers"></a>Gatilhos de API gerenciados

| Tipo de gatilho | Descrição | 
|--------------|-------------| 
| [**ApiConnection**](#apiconnection-trigger) | Verifica ou *pesquisas* por meio de um ponto de extremidade [APIs gerenciadas pela Microsoft](../connectors/apis-list.md). | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-trigger) | Cria um ponto de extremidade que pode ser chamado para seu aplicativo lógico, chamando [APIs gerenciadas pela Microsoft](../connectors/apis-list.md) para assinar e cancelar a assinatura. | 
||| 

## <a name="triggers---detailed-reference"></a>Gatilhos - referência detalhada

<a name="apiconnection-trigger"></a>

### <a name="apiconnection-trigger"></a>Gatilho APIConnection  

Esse acionador verifica ou *pesquisa* um terminal usando [APIs gerenciadas pela Microsoft](../connectors/apis-list.md), de forma que os parâmetros para esse acionador possam diferir com base no terminal. Muitas seções nessa definição de gatilho são opcionais. O comportamento do gatilho depende se as seções estão incluídas ou não.

```json
"<APIConnection_trigger_name>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },
      "method": "<method-type>",
      "path": "/<api-operation>",
      "retryPolicy": { "<retry-behavior>" },
      "queries": { "<query-parameters>" }
   },
   "recurrence": { 
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
}
```

*Necessária*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*APIConnection_trigger_name*> | Cadeia | O nome do gatilho | 
| <*connection-name*> | Cadeia | O nome da conexão com a API gerenciada usada pelo fluxo de trabalho | 
| <*tipo de método*> | Cadeia | O método HTTP para comunicação com a API gerenciada: "GET", "PUT", "POST", "PATCH", "DELETE" | 
| <*operação de API*> | Cadeia | A operação de API para chamar | 
| <*time-unit*> | Cadeia | A unidade de tempo que descreve a frequência com que o gatilho é acionado: "Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês" | 
| <*number-of-time-units*> | Inteiro | Um valor que especifica com que frequência o acionador é disparado com base na frequência, que é o número de unidades de tempo a aguardar até que o acionador seja acionado novamente <p>Aqui estão os intervalos mínimos e máximos: <p>- Mês: 1 a 16 meses </br>- Dia: 1 a 500 dias </br>- Hora: 1 a 12.000 horas </br>- Minuto: 1 a 72.000 minutos </br>- Segundo: 1 a 9.999.999 segundos<p>Por exemplo, se o intervalo for 6, e a frequência for "Mês", a recorrência será a cada 6 meses. | 
|||| 

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*comportamento de repetição*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de status 408, 429 e 5XX e todas as exceções de conectividade. Para mais informações, consulte [Políticas de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*parâmetros de consulta*> | Objeto JSON | Quaisquer parâmetros de consulta para incluir na chamada da API. Por exemplo, o `"queries": { "api-version": "2018-01-01" }` objeto adiciona `?api-version=2018-01-01` para a chamada. | 
| <*execuções de max*> | Inteiro | Por padrão, as instâncias de fluxo de trabalho são executadas ao mesmo tempo ou em paralelo até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar esse limite, definindo um novo <*contagem*> de valor, consulte [simultaneidade do gatilho de alteração](#change-trigger-concurrency). | 
| <*máximo de execuções de fila*> | Inteiro | Quando o fluxo de trabalho já estiver executando o número máximo de instâncias, que podem ser alteradas `runtimeConfiguration.concurrency.runs` com base na propriedade, todas as novas execuções serão colocadas nessa fila até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite padrão, consulte [execuções de espera da alteração limitam](#change-waiting-runs). | 
| <*expressão splitOn*> | Cadeia | Para gatilhos que retornam matrizes, essa expressão referencia a matriz a ser usada para que você possa criar e executar uma instância de fluxo de trabalho para cada item de matriz, em vez de usar um loop "para cada". <p>Por exemplo, essa expressão representa um item na matriz retornada dentro do conteúdo do corpo do acionador: `@triggerbody()?['value']` |
| <*opção de operação*> | Cadeia | Você pode alterar o comportamento padrão definindo a `operationOptions` propriedade. Para obter mais informações, consulte [opções de operação](#operation-options). |
||||

*Saídas*
 
| Elemento | Tipo | Descrição |
|---------|------|-------------|
| headers | Objeto JSON | Os cabeçalhos da resposta |
| corpo | Objeto JSON | O corpo da resposta |
| código de status | Inteiro | O código de status da resposta |
|||| 

*Exemplo*

Essa definição de acionador verifica se há emails todos os dias dentro da caixa de entrada para uma conta do Office 365 Outlook: 

```json
"When_a_new_email_arrives": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "get",
      "path": "/Mail/OnNewEmail",
      "queries": {
          "fetchOnlyWithAttachment": false,
          "folderPath": "Inbox",
          "importance": "Any",
          "includeAttachments": false
      }
   },
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

<a name="apiconnectionwebhook-trigger"></a>

### <a name="apiconnectionwebhook-trigger"></a>Gatilho ApiConnectionWebhook

Esse acionador envia uma solicitação de assinatura para um endpoint usando uma [API gerenciada pela Microsoft](../connectors/apis-list.md), fornece um *URL de retorno de chamada* para o ponto de extremidade enviar uma resposta e espera que o endpoint responda. Para obter mais informações, consulte [assinaturas de ponto de extremidade](#subscribe-unsubscribe).

```json
"<ApiConnectionWebhook_trigger_name>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "body": {
          "NotificationUrl": "@{listCallbackUrl()}"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },
      "retryPolicy": { "<retry-behavior>" },
      "queries": "<query-parameters>"
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-run-queue>
      }
   },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
}
```

*Necessária*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*connection-name*> | Cadeia | O nome da conexão com a API gerenciada usada pelo fluxo de trabalho | 
| <*conteúdo do corpo*> | Objeto JSON | Qualquer conteúdo da mensagem para enviar como carga para a API gerenciada | 
|||| 

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*comportamento de repetição*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de status 408, 429 e 5XX e todas as exceções de conectividade. Para mais informações, consulte [Políticas de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*parâmetros de consulta*> | Objeto JSON | Os parâmetros de consulta para incluir com a chamada à API <p>Por exemplo, o `"queries": { "api-version": "2018-01-01" }` objeto adiciona `?api-version=2018-01-01` para a chamada. | 
| <*execuções de max*> | Inteiro | Por padrão, as instâncias de fluxo de trabalho são executadas ao mesmo tempo ou em paralelo até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar esse limite, definindo um novo <*contagem*> de valor, consulte [simultaneidade do gatilho de alteração](#change-trigger-concurrency). | 
| <*máximo de execuções de fila*> | Inteiro | Quando o fluxo de trabalho já estiver executando o número máximo de instâncias, que podem ser alteradas `runtimeConfiguration.concurrency.runs` com base na propriedade, todas as novas execuções serão colocadas nessa fila até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite padrão, consulte [execuções de espera da alteração limitam](#change-waiting-runs). | 
| <*expressão splitOn*> | Cadeia | Para gatilhos que retornam matrizes, essa expressão referencia a matriz a ser usada para que você possa criar e executar uma instância de fluxo de trabalho para cada item de matriz, em vez de usar um loop "para cada". <p>Por exemplo, essa expressão representa um item na matriz retornada dentro do conteúdo do corpo do acionador: `@triggerbody()?['value']` |
| <*opção de operação*> | Cadeia | Você pode alterar o comportamento padrão definindo a `operationOptions` propriedade. Para obter mais informações, consulte [opções de operação](#operation-options). | 
|||| 

*Exemplo*

Essa definição de gatilho se inscreve na API do Office 365 Outlook, fornece uma URL de retorno de chamada ao ponto de extremidade da API e aguarda o ponto de extremidade responder quando chega um novo e-mail.

```json
"When_a_new_email_arrives_(webhook)": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "body": {
         "NotificationUrl": "@{listCallbackUrl()}" 
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "path": "/MailSubscription/$subscriptions",
      "queries": {
          "folderPath": "Inbox",
          "hasAttachment": "Any",
          "importance": "Any"
      }
   },
   "splitOn": "@triggerBody()?['value']"
}
```

<a name="http-trigger"></a>

### <a name="http-trigger"></a>Gatilho HTTP

Esse acionador verifica ou pesquisa o terminal especificado com base no agendamento de recorrência especificado. A resposta do terminal determina se o fluxo de trabalho é executado.

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<endpoint-URL>",
      "headers": { "<header-content>" },
      "body": "<body-content>",
      "authentication": { "<authentication-method>" },
      "retryPolicy": { "<retry-behavior>" },
      "queries": "<query-parameters>"
   },
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

*Necessária*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*tipo de método*> | Cadeia | O método HTTP a ser usado para sondar o ponto de extremidade especificado: "GET", "PUT", "POST", "PATCH", "DELETE" | 
| <*endpoint-URL*> | Cadeia | A URL HTTP ou HTTPS para o ponto de extremidade para sondar <p>Tamanho máximo da cadeia de caracteres: 2 KB | 
| <*time-unit*> | Cadeia | A unidade de tempo que descreve a frequência com que o gatilho é acionado: "Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês" | 
| <*number-of-time-units*> | Inteiro | Um valor que especifica com que frequência o acionador é disparado com base na frequência, que é o número de unidades de tempo a aguardar até que o acionador seja acionado novamente <p>Aqui estão os intervalos mínimos e máximos: <p>- Mês: 1 a 16 meses </br>- Dia: 1 a 500 dias </br>- Hora: 1 a 12.000 horas </br>- Minuto: 1 a 72.000 minutos </br>- Segundo: 1 a 9.999.999 segundos<p>Por exemplo, se o intervalo for 6, e a frequência for "Mês", a recorrência será a cada 6 meses. | 
|||| 

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*conteúdo do cabeçalho*> | Objeto JSON | Um ou mais cabeçalhos para enviar com uma solicitação <p>Por exemplo, para definir o idioma e o tipo de uma solicitação: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*conteúdo do corpo*> | Cadeia | O conteúdo da mensagem para enviar como carga de solicitação | 
| <*método de autenticação*> | Objeto JSON | O método de solicitação usa para autenticação. Para obter mais informações, consulte [Autenticação de saída do Agendador](../scheduler/scheduler-outbound-authentication.md). Além do Agendador, a propriedade `authority` tem suporte. Quando não especificado, o valor padrão é `https://login.windows.net`, mas é possível usar um valor diferente, como`https://login.windows\-ppe.net`. |
| <*comportamento de repetição*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de status 408, 429 e 5XX e todas as exceções de conectividade. Para mais informações, consulte [Políticas de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies). |  
 <*parâmetros de consulta*> | Objeto JSON | Quaisquer parâmetros de consulta para incluir no pedido <p>Por exemplo, o `"queries": { "api-version": "2018-01-01" }`objeto adiciona`?api-version=2018-01-01` à solicitação. | 
| <*execuções de max*> | Inteiro | Por padrão, as instâncias de fluxo de trabalho são executadas ao mesmo tempo ou em paralelo até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar esse limite, definindo um novo <*contagem*> de valor, consulte [simultaneidade do gatilho de alteração](#change-trigger-concurrency). | 
| <*máximo de execuções de fila*> | Inteiro | Quando o fluxo de trabalho já estiver executando o número máximo de instâncias, que podem ser alteradas `runtimeConfiguration.concurrency.runs` com base na propriedade, todas as novas execuções serão colocadas nessa fila até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite padrão, consulte [execuções de espera da alteração limitam](#change-waiting-runs). | 
| <*opção de operação*> | Cadeia | Você pode alterar o comportamento padrão definindo a `operationOptions` propriedade. Para obter mais informações, consulte [opções de operação](#operation-options). | 
|||| 

*Saídas*

| Elemento | Tipo | Descrição |
|---------|------|-------------| 
| headers | Objeto JSON | Os cabeçalhos da resposta | 
| corpo | Objeto JSON | O corpo da resposta | 
| código de status | Inteiro | O código de status da resposta | 
|||| 

*Requisitos para as solicitações de entrada*

Para funcionar bem com seu aplicativo lógico, o ponto de extremidade deve estar de acordo com um padrão de gatilho específico ou o contrato e reconhecer essas propriedades:  
  
| Resposta | Necessário | Descrição | 
|----------|----------|-------------| 
| Código de status | Sim | O código de status "200 OK" inicia uma execução. Qualquer outro código de status não inicia uma execução. | 
| Cabeçalho Retry-after | Não | O número de segundos até o aplicativo lógico sondar o ponto de extremidade novamente | 
| Cabeçalho do local | Não | A URL a chamar no próximo intervalo de sondagem. Se não for especificada, a URL original será usada. | 
|||| 

*Exemplo de comportamentos para diferentes solicitações*

| Código de status | Tentar novamente após | Comportamento | 
|-------------|-------------|----------|
| 200 | {none} | Execute o fluxo de trabalho e verifique novamente se há mais dados após a recorrência definida. | 
| 200 | 10 segundos | Execute o fluxo de trabalho e verifique novamente se há mais dados após 10 segundos. |  
| 202 | 60 segundos | Não dispara o fluxo de trabalho. A próxima tentativa ocorre em um minuto, sujeito à recorrência definida. Se a recorrência definida for inferior a um minuto, o cabeçalho retry-after terá precedência. Caso contrário, a recorrência definida será usada. | 
| 400 | {none} | Solicitação inválida, não execute o fluxo de trabalho. Se nenhum `retryPolicy` for definido, a política padrão será usada. Após a quantidade de novas tentativas ter sido atingida, o gatilho buscará novamente os dados após a recorrência definida. | 
| 500 | {none}| Erro do servidor, não execute o fluxo de trabalho. Se nenhum `retryPolicy` for definido, a política padrão será usada. Após a quantidade de novas tentativas ter sido atingida, o gatilho buscará novamente os dados após a recorrência definida. | 
|||| 

<a name="http-webhook-trigger"></a>

### <a name="httpwebhook-trigger"></a>Gatilho HTTPWebhook  

Esse acionador permite que seu aplicativo lógico seja chamado criando um terminal que possa registrar uma assinatura chamando o URL do terminal especificado. Quando você cria esse acionador em seu fluxo de trabalho, uma solicitação de saída faz a chamada para registrar a assinatura. Dessa forma, o gatilho pode começar a ouvir eventos. Quando uma operação torna esse acionador inválido, uma solicitação de saída faz automaticamente a chamada para cancelar a assinatura. Para obter mais informações, consulte [assinaturas de ponto de extremidade](#subscribe-unsubscribe).

Você também pode especificar [limites assíncronos](#asynchronous-limits) em um **HTTPWebhook** gatilho.
Comportamento do gatilho depende das seções que você use ou omite. 

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<endpoint-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-method>" },
         "retryPolicy": { "<retry-behavior>" }
         },
      },
      "unsubscribe": {
         "method": "<method-type>",
         "url": "<endpoint-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-method>" }
      }
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

Alguns valores, como <*tipo de método*>, estão disponíveis para ambos os `"subscribe"` e `"unsubscribe"` objetos.

*Necessária*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*tipo de método*> | Cadeia | O método HTTP a ser usado para a solicitação de assinatura: "GET", "PUT", "POST", "PATCH" ou "DELETE" | 
| <*endpoint-subscribe-URL*> | Cadeia | O URL do ponto final para onde enviar o pedido de subscrição | 
|||| 

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*tipo de método*> | Cadeia | O método HTTP a ser usado para a solicitação de cancelamento: "GET", "PUT", "POST", "PATCH" ou "DELETE" | 
| <*endpoint-unsubscribe-URL*> | Cadeia | A URL do ponto de extremidade para onde enviar a solicitação de cancelamento | 
| <*conteúdo do corpo*> | Cadeia | Qualquer conteúdo de mensagem para enviar na solicitação de assinatura ou cancelamento | 
| <*método de autenticação*> | Objeto JSON | O método de solicitação usa para autenticação. Para obter mais informações, consulte [Autenticação de saída do Agendador](../scheduler/scheduler-outbound-authentication.md). |
| <*comportamento de repetição*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de status 408, 429 e 5XX e todas as exceções de conectividade. Para mais informações, consulte [Políticas de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*execuções de max*> | Inteiro | Por padrão, todas as instâncias de fluxo de trabalho são executadas ao mesmo tempo ou em paralelo até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar esse limite, definindo um novo <*contagem*> de valor, consulte [simultaneidade do gatilho de alteração](#change-trigger-concurrency). | 
| <*máximo de execuções de fila*> | Inteiro | Quando o fluxo de trabalho já estiver executando o número máximo de instâncias, que podem ser alteradas `runtimeConfiguration.concurrency.runs` com base na propriedade, todas as novas execuções serão colocadas nessa fila até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite padrão, consulte [execuções de espera da alteração limitam](#change-waiting-runs). | 
| <*opção de operação*> | Cadeia | Você pode alterar o comportamento padrão definindo a `operationOptions` propriedade. Para obter mais informações, consulte [opções de operação](#operation-options). | 
|||| 

*Saídas* 

| Elemento | Tipo | Descrição |
|---------|------|-------------| 
| headers | Objeto JSON | Os cabeçalhos da resposta | 
| corpo | Objeto JSON | O corpo da resposta | 
| código de status | Inteiro | O código de status da resposta | 
|||| 

*Exemplo*

Esse acionador cria uma assinatura para o terminal especificado, fornece um URL de retorno de chamada exclusivo e aguarda por artigos de tecnologia recém-publicados.

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "POST",
         "uri": "https://pubsubhubbub.appspot.com/subscribe",
         "body": {
            "hub.callback": "@{listCallbackUrl()}",
            "hub.mode": "subscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         },
      },
      "unsubscribe": {
         "method": "POST",
         "url": "https://pubsubhubbub.appspot.com/subscribe",
         "body": {
            "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
            "hub.mode": "unsubscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         }
      }
   }
}
```

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>Gatilho de recorrência  

Esse acionador é executado com base no agendamento de recorrência especificado e fornece uma maneira fácil de criar um fluxo de trabalho regularmente em execução. 

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
      "startTime": "<start-date-time-with-format-YYYY-MM-DDThh:mm:ss>",
      "timeZone": "<time-zone>",
      "schedule": {
         // Applies only when frequency is Day or Week. Separate values with commas.
         "hours": [ <one-or-more-hour-marks> ], 
         // Applies only when frequency is Day or Week. Separate values with commas.
         "minutes": [ <one-or-more-minute-marks> ], 
         // Applies only when frequency is Week. Separate values with commas.
         "weekDays": [ "Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday" ] 
      }
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

*Necessária*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*time-unit*> | Cadeia | A unidade de tempo que descreve a frequência com que o gatilho é acionado: "Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês" | 
| <*number-of-time-units*> | Inteiro | Um valor que especifica com que frequência o acionador é disparado com base na frequência, que é o número de unidades de tempo a aguardar até que o acionador seja acionado novamente <p>Aqui estão os intervalos mínimos e máximos: <p>- Mês: 1 a 16 meses </br>- Dia: 1 a 500 dias </br>- Hora: 1 a 12.000 horas </br>- Minuto: 1 a 72.000 minutos </br>- Segundo: 1 a 9.999.999 segundos<p>Por exemplo, se o intervalo for 6, e a frequência for "Mês", a recorrência será a cada 6 meses. | 
|||| 

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*start-date-time-with-format-YYYY-MM-DDThh:mm:ss*> | Cadeia | A data e hora de início neste formato: <p>AAAA-MM-DDThh:mm:ss se você especificar um fuso horário <p>- ou - <p>AAAA-MM-DDThh:mm:ssZ se você não especificar um fuso horário <p>Por exemplo, se você quiser 18 de setembro de 2017 às 14h00, especifique "2017-09-18T14:00:00" e especifique um fuso horário como "Hora Padrão do Pacífico" ou especifique "2017-09-18T14:00:00Z" sem um fuso horário. <p>**Observação:** Essa hora de início deve seguir a [especificação de data e hora ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) no [formato de data e hora UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), mas sem uma [diferença UTC](https://en.wikipedia.org/wiki/UTC_offset). Se você não especificar um fuso horário, será necessário adicionar a letra "Z" no final sem espaços. Essa letra "Z" refere-se ao equivalente em [hora náutica](https://en.wikipedia.org/wiki/Nautical_time). <p>Para agendamentos simples, a hora de início é a primeira ocorrência, enquanto que, para agendamentos complexos, o gatilho não é disparado antes da hora de início. Para obter mais informações sobre datas e horas de início, consulte [Criar e agendar tarefas de execução regularmente](../connectors/connectors-native-recurrence.md). | 
| <*time-zone*> | Cadeia | Aplica-se somente quando você especifica uma hora de início, porque o gatilho não aceita [diferença UTC](https://en.wikipedia.org/wiki/UTC_offset). Especifique o fuso horário que deseja aplicar. | 
| <*um-ou-mais--marcas de hora*> | Inteiro ou matriz de inteiros | Se você selecionar "Dia" ou "Semana" para `frequency`, poderá selecionar um ou mais números inteiros, de 0 a 23, separados por vírgulas, como as horas do dia nas quais você deseja executar o fluxo de trabalho. <p>Por exemplo, se você especificar "10", "12" e "14", você obterá 10h, 12h e 14h como as marcas de hora. | 
| <*uma-ou-mais--marcas de minuto*> | Inteiro ou matriz de inteiros | Se você selecionar "Dia" ou "Semana" para `frequency`, poderá selecionar um ou mais números inteiros, de 0 a 59, separados por vírgulas, como os minutos da hora nos quais você deseja executar o fluxo de trabalho. <p>Por exemplo, você pode especificar "30" como a marca de minutos e, usando o exemplo anterior como as horas do dia, você obtém 10h30, 12h30 e 14h30. | 
| weekDays | Cadeia de caracteres ou matriz de cadeia de caracteres | Se você especificar "Semana" para `frequency`, poderá especificar um ou mais dias, separados por vírgulas, quando quiser executar o fluxo de trabalho: "Segunda-feira", "Terça-feira", "Quarta-feira", "Quinta-feira", "Sexta-feira", "Sábado" e "Domingo" | 
| <*execuções de max*> | Inteiro | Por padrão, todas as instâncias de fluxo de trabalho são executadas ao mesmo tempo ou em paralelo até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar esse limite, definindo um novo <*contagem*> de valor, consulte [simultaneidade do gatilho de alteração](#change-trigger-concurrency). | 
| <*máximo de execuções de fila*> | Inteiro | Quando o fluxo de trabalho já estiver executando o número máximo de instâncias, que podem ser alteradas `runtimeConfiguration.concurrency.runs` com base na propriedade, todas as novas execuções serão colocadas nessa fila até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite padrão, consulte [execuções de espera da alteração limitam](#change-waiting-runs). | 
| <*opção de operação*> | Cadeia | Você pode alterar o comportamento padrão definindo a `operationOptions` propriedade. Para obter mais informações, consulte [opções de operação](#operation-options). | 
|||| 

*Exemplo 1*

Esse gatilho de recorrência básica executa diariamente:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

*Exemplo 2*

É possível especificar uma data e hora de início para acionar o gatilho. Esse gatilho de recorrência inicia na data especificada e, em seguida, é acionado diariamente:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1,
      "startTime": "2017-09-18T00:00:00Z"
   }
}
```

*Exemplo 3*

Esse gatilho de recorrência inicia em 9 de setembro de 2017 às 14h00 e é acionado semanalmente às segundas-feiras às 10h30, 12h30 e 14h30, Hora Padrão do Pacífico:

``` json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "hours": [ 10, 12, 14 ],
         "minutes": [ 30 ],
         "weekDays": [ "Monday" ]
      },
      "startTime": "2017-09-07T14:00:00",
      "timeZone": "Pacific Standard Time"
   }
}
```

Para obter mais informações e exemplos para esse gatilho, consulte [Criar e agendar tarefas de execução regularmente](../connectors/connectors-native-recurrence.md).

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Gatilho de solicitação

Esse acionador faz seu aplicativo lógico ser chamado criando um terminal que pode aceitar solicitações de entrada. Para esse acionador, forneça um esquema JSON que descreva e valide a carga útil ou entradas que o acionador recebe da solicitação de entrada. O esquema também facilita a referência das propriedades do acionador de ações posteriores no fluxo de trabalho. 

Para chamar esse gatilho, você deve usar a `listCallbackUrl`API, descrita na [API REST do Serviço de Fluxo de Trabalho](https://docs.microsoft.com/rest/api/logic/workflows). Para saber como usar esse gatilho como um ponto de extremidade HTTP, consulte [Chamar, disparar ou aninhar fluxos de trabalho com pontos de extremidade HTTP](../logic-apps/logic-apps-http-endpoint.md).

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "<method-type>",
      "relativePath": "<relative-path-for-accepted-parameter>",
      "schema": {
         "type": "object",
         "properties": { 
            "<property-name>": {
               "type": "<property-type>"
            }
         },
         "required": [ "<required-properties>" ]
      }
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-run-queue>
      },
   },
   "operationOptions": "<operation-option>"
}
```

*Necessária*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*nome da propriedade*> | Cadeia | O nome de uma propriedade no esquema JSON, que descreve a carga útil | 
| <*tipo de propriedade*> | Cadeia | O tipo da propriedade | 
|||| 

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*tipo de método*> | Cadeia | O método que as solicitações de entrada devem usar para chamar seu aplicativo lógico: "GET", "PUT", "POST", "PATCH", "DELETE" |
| <*relative-path-for-accepted-parameter*> | Cadeia | O caminho relativo para o parâmetro que o URL do seu endpoint pode aceitar | 
| <*propriedades obrigatórias*> | Array | Uma ou mais propriedades que exigem valores | 
| <*execuções de max*> | Inteiro | Por padrão, todas as instâncias de fluxo de trabalho são executadas ao mesmo tempo ou em paralelo até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar esse limite, definindo um novo <*contagem*> de valor, consulte [simultaneidade do gatilho de alteração](#change-trigger-concurrency). | 
| <*máximo de execuções de fila*> | Inteiro | Quando o fluxo de trabalho já estiver executando o número máximo de instâncias, que podem ser alteradas `runtimeConfiguration.concurrency.runs` com base na propriedade, todas as novas execuções serão colocadas nessa fila até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite padrão, consulte [execuções de espera da alteração limitam](#change-waiting-runs). | 
| <*opção de operação*> | Cadeia | Você pode alterar o comportamento padrão definindo a `operationOptions` propriedade. Para obter mais informações, consulte [opções de operação](#operation-options). | 
|||| 

*Exemplo*

Esse acionador especifica que uma solicitação recebida deve usar o método HTTP POST para chamar o acionador e inclui um esquema que valide a entrada da solicitação recebida: 

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "POST",
      "schema": {
         "type": "object",
         "properties": {
            "customerName": {
               "type": "String"
            },
            "customerAddress": { 
               "type": "Object",
               "properties": {
                  "streetAddress": {
                     "type": "string"
                  },
                  "city": {
                     "type": "string"
                  }
               }
            }
         }
      }
   }
}
```

<a name="trigger-conditions"></a>

## <a name="trigger-conditions"></a>Condições de gatilho

Para qualquer gatilho, e apenas gatilhos, você pode incluir uma matriz que contém uma ou mais expressões para condições que determinam se o fluxo de trabalho deve ser executado. Para adicionar a `conditions` Propriedade a um gatilho em seu fluxo de trabalho, abra seu aplicativo lógico no editor de exibição de código.

Por exemplo, você pode especificar um gatilho é acionado somente quando um site da Web retorna um erro interno do servidor, fazendo referência a código de status do gatilho na `conditions` propriedade:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Hour",
      "interval": 1
   },
   "conditions": [ {
      "expression": "@equals(triggers().code, 'InternalServerError')"
   } ]
}
```

Por padrão, um gatilho dispara somente depois de receber uma resposta "200 OK". Quando uma expressão faz referência ao código de status de um acionador, o comportamento padrão do acionador é substituído. Portanto, se você quiser que o gatilho seja acionado para mais de um código de status, como "200" e o código de status "201", você deve incluir essa expressão como sua condição: 

`@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="trigger-multiple-runs"></a>Disparar várias execuções

Se o gatilho retornar uma matriz para o aplicativo lógico processar, às vezes, um loop "for each" poderá levar muito tempo para processar cada item da matriz. Em vez disso, você pode usar a propriedade **SplitOn** em seu gatilho para *retirar em lote* a matriz. O desenvio em lote divide os itens de matriz e inicia uma nova instância de fluxo de trabalho que é executada para cada item de matriz. Essa abordagem é útil, por exemplo, quando você deseja sondar um ponto de extremidade que pode retornar vários itens novos entre os intervalos de sondagem.
Para o número máximo de itens de matriz que **SplitOn** pode processar em uma execução única do aplicativo lógico, consulte [Limites e configuração](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). 

> [!NOTE]
> Você não pode usar **SplitOn** com um padrão de resposta síncrona. Qualquer fluxo de trabalho que usa **SplitOn** e inclui uma ação de resposta é executado de forma assíncrona e envia imediatamente uma resposta `202 ACCEPTED`.

Se o arquivo Swagger do gatilho descrever uma carga que é uma matriz, a propriedade **SplitOn** será adicionada automaticamente ao seu gatilho. Caso contrário, adicione essa propriedade dentro da carga de resposta que tem a matriz da qual deseja remover o lote. 

*Exemplo*

Suponha que você tenha uma API que retorna a seguinte resposta: 
  
```json
{
   "Status": "Succeeded",
   "Rows": [ 
      { 
         "id": 938109380,
         "name": "customer-name-one"
      },
      {
         "id": 938109381,
         "name": "customer-name-two"
      }
   ]
}
```
 
Seu aplicativo lógico precisa apenas do conteúdo da matriz na `Rows`, portanto, você pode criar um gatilho como neste exemplo:

``` json
"HTTP_Debatch": {
   "type": "Http",
    "inputs": {
        "uri": "https://mydomain.com/myAPI",
        "method": "GET"
    },
   "recurrence": {
      "frequency": "Second",
      "interval": 1
    },
    "splitOn": "@triggerBody()?.Rows"
}
```

> [!NOTE]
> Portanto, se você usar o comando `SplitOn`, não será possível obter as propriedades que estão fora da matriz. Portanto, para este exemplo, não é possível obter a propriedade `status` na resposta retornada da API.
> 
> Para evitar uma falha se a propriedade `Rows` não existir, este exemplo usa o operador `?`.

Sua definição de fluxo de trabalho agora pode usar `@triggerBody().name`para obter os valores`name`, que são `"customer-name-one"`da primeira execução e`"customer-name-two"` da segunda execução. Portanto, as saídas do gatilho ficam como neste exemplo:

```json
{
   "body": {
      "id": 938109380,
      "name": "customer-name-one"
   }
}
```

```json
{
   "body": {
      "id": 938109381,
      "name": "customer-name-two"
   }
}
```

<a name="actions-overview"></a>

## <a name="actions-overview"></a>Visão geral de ações

Os Aplicativos Lógicos do Azure fornecem vários tipos de ação - cada um com entradas diferentes que definem o comportamento exclusivo de uma ação. 

As ações têm esses elementos de alto nível, embora alguns sejam opcionais:

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": { 
      "<input-name>": { "<input-value>" },
      "retryPolicy": "<retry-behavior>" 
   },
   "runAfter": { "<previous-trigger-or-action-status>" },
   "runtimeConfiguration": { "<runtime-config-options>" },
   "operationOptions": "<operation-option>"
},
```

*Necessária*

| Valor | Tipo | Descrição | 
|-------|------|-------------|
| <*nome da ação*> | Cadeia | O nome da ação | 
| <*tipo de ação*> | Cadeia | O tipo de ação, por exemplo, "Http" ou "ApiConnection"| 
| <*nome de entrada*> | Cadeia | O nome de uma entrada que define o comportamento da ação | 
| <*valor de entrada*> | Vários | O valor de entrada, que pode ser uma cadeia de caracteres, inteiro, objeto JSON e assim por diante | 
| <*anterior – gatilho-ou-action-status*> | Objeto JSON | O nome e o status resultante para o gatilho ou ação que deve ser executado imediatamente antes de executar esta ação atual | 
|||| 

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------|
| <*comportamento de repetição*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de status 408, 429 e 5XX e todas as exceções de conectividade. Para saber mais, confira Políticas de repetição. | 
| <*Opções de configuração de tempo de execução*> | Objeto JSON | Para algumas ações, você pode alterar o comportamento da ação em tempo de execução, definindo `runtimeConfiguration` propriedades. Para obter mais informações, consulte [Configurações de tempo de execução](#runtime-config-options). | 
| <*opção de operação*> | Cadeia | Para algumas ações, você pode alterar o comportamento padrão definindo a `operationOptions` propriedade. Para obter mais informações, consulte [opções de operação](#operation-options). | 
|||| 

## <a name="action-types-list"></a>Lista de tipos de ação

Aqui estão alguns tipos de ação comumente usadas: 

* [Tipos de ação integrada](#built-in-actions) como esses exemplos e muito mais: 

  * [**HTTP** ](#http-action) para chamar pontos de extremidade via HTTP ou HTTPS

  * [**Resposta** ](#response-action) para responder às solicitações

  * [**Executar código JavaScript**](#run-javascript-code) para executar trechos de código JavaScript

  * [**Função** ](#function-action) para chamar funções do Azure

  * Ações de operação de dados, como [ **ingressar**](#join-action), [ **Compose**](#compose-action), [ **tabela** ](#table-action), [ **Selecionar**](#select-action)e outras pessoas que criam ou transformar dados de várias entradas

  * [**Fluxo de trabalho** ](#workflow-action) para chamar outro fluxo de aplicativo lógico

* [Tipos de ação de API gerenciados](#managed-api-actions), como [**ApiConnection**](#apiconnection-action) e [**ApiConnectionWebHook**](#apiconnectionwebhook-action) que chame vários conectores e APIs gerenciadas pela Microsoft, por exemplo, o barramento de serviço do Azure, Office 365 Outlook, Power BI, o armazenamento de BLOBs do Azure, OneDrive, GitHub e muito mais

* [Tipos de ação de fluxo de trabalho de controle](#control-workflow-actions), como [**se**](#if-action), [**Foreach**](#foreach-action), [**Switch** ](#switch-action), [**Escopo**](#scope-action), e [**até**](#until-action), que contêm outras ações e ajudá-lo organizar a execução de fluxo de trabalho

<a name="built-in-actions"></a>

### <a name="built-in-actions"></a>Ações internas

| Tipo de ação | Descrição | 
|-------------|-------------| 
| [**Redigir**](#compose-action) | Cria uma única saída de entradas, que podem ter vários tipos. | 
| [**Executar código JavaScript**](#run-javascript-code) | Executar trechos de código JavaScript que se enquadram dentro de critérios específicos. Para obter os requisitos de código e mais informações, consulte [Adicionar e executar trechos de código com código embutido](../logic-apps/logic-apps-add-run-inline-code.md). |
| [**Função**](#function-action) | Chama uma função do Azure. | 
| [**HTTP**](#http-action) | Chama um ponto de extremidade HTTP. | 
| [**Junte-se**](#join-action) | Cria uma string de todos os itens em uma matriz e separa esses itens com um caractere delimitador especificado. | 
| [**Analisar o JSON**](#parse-json-action) | Cria conteúdo fácil de usar tokens de propriedades no JSON. Você pode fazer referência a essas propriedades, incluindo os tokens em seu aplicativo lógico. | 
| [**Consulta**](#query-action) | Cria uma matriz de itens em outra matriz com base em uma condição ou filtro. | 
| [**Resposta**](#response-action) | Cria uma resposta a uma chamada de entrada ou a solicitação. | 
| [**Selecione**](#select-action) | Cria uma matriz com objetos JSON, transformando itens de outra matriz com base no mapa especificado. | 
| [**Tabela**](#table-action) | Cria uma tabela CSV ou HTML de uma matriz. | 
| [**Encerrar**](#terminate-action) | Interrompe um fluxo de trabalho ativo. | 
| [**Aguarde**](#wait-action) | Pausa seu fluxo de trabalho por um período especificado ou até a data e a hora especificadas. | 
| [**Fluxo de trabalho**](#workflow-action) | Aninha um fluxo de trabalho dentro de outro fluxo de trabalho. | 
||| 

<a name="managed-api-actions"></a>

### <a name="managed-api-actions"></a>Ações de API gerenciadas

| Tipo de ação | Descrição | 
|-------------|-------------|  
| [**ApiConnection**](#apiconnection-action) | Chama um ponto de extremidade HTTP usando uma [API gerenciada pela Microsoft](../connectors/apis-list.md). | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-action) | Funciona como o HTTP Webhook, mas usa uma [API gerenciada pela Microsoft](../connectors/apis-list.md). | 
||| 

<a name="control-workflow-actions"></a>

### <a name="control-workflow-actions"></a>Ações de controle de fluxo de trabalho

Essas ações ajudam você a controlar a execução do fluxo de trabalho e incluem outras ações. De fora de uma ação de fluxo de trabalho de controle, você pode referenciar diretamente as ações dentro dessa ação de fluxo de trabalho de controle. Por exemplo, se você tiver uma `Http` ação dentro de um escopo, poderá referenciar a expressão `@body('Http')` de qualquer lugar no fluxo de trabalho. No entanto, as ações que existem dentro de uma ação de fluxo de trabalho de controle podem apenas "correr atrás" outras ações que estão na mesma estrutura de fluxo de trabalho de controle.

| Tipo de ação | Descrição | 
|-------------|-------------| 
| [**ForEach**](#foreach-action) | Execute as mesmas ações em um loop para cada item em uma matriz. | 
| [**If**](#if-action) | Execute ações com base em se a condição especificada é verdadeira ou falsa. | 
| [**Escopo**](#scope-action) | Execute ações com base no status do grupo de um conjunto de ações. | 
| [**Switch**](#switch-action) | Execute ações organizadas em casos em que valores de expressões, objetos ou tokens correspondam aos valores especificados por cada caso. | 
| [**Until**](#until-action) | Execute ações em um loop até que a condição especificada seja verdadeira. | 
|||  

## <a name="actions---detailed-reference"></a>Ações – uma referência detalhada

<a name="apiconnection-action"></a>

### <a name="apiconnection-action"></a>Ação APIConnection

Essa ação envia uma solicitação HTTP para uma [API gerenciada pela Microsoft](../connectors/apis-list.md) e exige informações sobre a API e os parâmetros, além de uma referência a uma conexão válida. 

``` json
"<action-name>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<api-name>']['connectionId']"
         },
         "<other-action-specific-input-properties>"        
      },
      "method": "<method-type>",
      "path": "/<api-operation>",
      "retryPolicy": "<retry-behavior>",
      "queries": { "<query-parameters>" },
      "<other-action-specific-properties>"
    },
    "runAfter": {}
}
```

*Necessária*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*nome da ação*> | Cadeia | O nome da ação fornecida pelo conector | 
| <*nome da API*> | Cadeia | O nome da API gerenciada pela Microsoft usada para a conexão | 
| <*tipo de método*> | Cadeia | O método HTTP para chamar a API: "GET", "PUT", "POST", "PATCH" ou "DELETE" | 
| <*operação de API*> | Cadeia | A operação de API para chamar | 
|||| 

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| < *outras propriedades específicas de entrada de ação*> | Objeto JSON | Quaisquer outras propriedades de entrada que se aplicam a essa ação específica | 
| <*comportamento de repetição*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de status 408, 429 e 5XX e todas as exceções de conectividade. Para mais informações, consulte [Políticas de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*parâmetros de consulta*> | Objeto JSON | Quaisquer parâmetros de consulta para incluir na chamada da API. <p>Por exemplo, o `"queries": { "api-version": "2018-01-01" }` objeto adiciona `?api-version=2018-01-01` para a chamada. | 
| <*propriedades específicas que outras é ação*> | Objeto JSON | Outras propriedades que se aplicam a essa ação específica | 
|||| 

*Exemplo*

Esta definição descreve a ação **Enviar um e-mail** para o conector do Office 365 Outlook, que é uma API gerenciada pela Microsoft: 

```json
"Send_an_email": {
   "type": "ApiConnection",
   "inputs": {
      "body": {
         "Body": "Thank you for your membership!",
         "Subject": "Hello and welcome!",
         "To": "Sophie.Owen@contoso.com"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "POST",
      "path": "/Mail"
    },
    "runAfter": {}
}
```

<a name="apiconnection-webhook-action"></a>

### <a name="apiconnectionwebhook-action"></a>Ação APIConnectionWebhook

Essa ação envia uma solicitação de assinatura por meio de HTTP para um ponto de extremidade usando um [API gerenciada pela Microsoft](../connectors/apis-list.md), fornece uma *URL de retorno de chamada* onde o ponto de extremidade pode enviar uma resposta e aguarda o ponto de extremidade responda. Para obter mais informações, consulte [assinaturas de ponto de extremidade](#subscribe-unsubscribe).

```json
"<action-name>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<api-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-method>" },
         "retryPolicy": "<retry-behavior>",
         "queries": { "<query-parameters>" },
         "<other-action-specific-input-properties>"
      },
      "unsubscribe": {
         "method": "<method-type>",
         "uri": "<api-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-method>" },
         "<other-action-specific-properties>"
      },
   },
   "runAfter": {}
}
```

Alguns valores, como <*tipo de método*>, estão disponíveis para ambos os `"subscribe"` e `"unsubscribe"` objetos.

*Necessária*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*nome da ação*> | Cadeia | O nome da ação fornecida pelo conector | 
| <*tipo de método*> | Cadeia | O método HTTP a ser usado para se inscrever ou cancelar a inscrição em um ponto de extremidade: "GET", "PUT", "POST", "PATCH" ou "DELETE" | 
| <*api-subscribe-URL*> | Cadeia | O URI a ser usado para assinar a API | 
|||| 

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*Cancelar assinatura-URL da API*> | Cadeia | O URI a ser usado para cancelar a inscrição da API | 
| <*conteúdo do cabeçalho*> | Objeto JSON | Quaisquer cabeçalhos para enviar a solicitação <p>Por exemplo, para definir o idioma e o tipo em uma solicitação: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*conteúdo do corpo*> | Objeto JSON | Qualquer conteúdo da mensagem para enviar a solicitação | 
| <*método de autenticação*> | Objeto JSON | O método de solicitação usa para autenticação. Para obter mais informações, consulte [Autenticação de saída do Agendador](../scheduler/scheduler-outbound-authentication.md). |
| <*comportamento de repetição*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de status 408, 429 e 5XX e todas as exceções de conectividade. Para mais informações, consulte [Políticas de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*parâmetros de consulta*> | Objeto JSON | Os parâmetros de consulta para incluir com a chamada à API <p>Por exemplo, o `"queries": { "api-version": "2018-01-01" }` objeto adiciona `?api-version=2018-01-01` para a chamada. | 
| < *outras propriedades específicas de entrada de ação*> | Objeto JSON | Quaisquer outras propriedades de entrada que se aplicam a essa ação específica | 
| <*propriedades específicas que outras é ação*> | Objeto JSON | Outras propriedades que se aplicam a essa ação específica | 
|||| 

Você também pode especificar limites em um **ApiConnectionWebhook** ação da mesma forma como [limites assíncronos do HTTP](#asynchronous-limits).

<a name="compose-action"></a>

### <a name="compose-action"></a>Ação para compor

Esta ação cria uma única saída de várias entradas, incluindo expressões. Tanto a saída quanto as entradas podem ter qualquer tipo compatível com os Aplicativos Lógicos do Azure, como matrizes, objetos JSON, XML e binário.
Em seguida, você pode usar a saída da ação em outras ações. 

```json
"Compose": {
   "type": "Compose",
   "inputs": "<inputs-to-compose>",
   "runAfter": {}
},
```

*Necessária* 

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*entradas para compor*> | Any | As entradas para a criação de uma única saída | 
|||| 

*Exemplo 1*

<!-- markdownlint-disable MD038 -->
Esta definição de ação mescla `abcdefg ` com um espaço à direita e o valor `1234`:
<!-- markdownlint-enable MD038 -->

```json
"Compose": {
   "type": "Compose",
   "inputs": "abcdefg 1234",
   "runAfter": {}
},
```

Aqui está a saída que essa ação cria:

`abcdefg 1234`

*Exemplo 2*

Esta definição de ação mescla uma variável de string que contém `abcdefg` e uma variável inteira que contém `1234`:

```json
"Compose": {
   "type": "Compose",
   "inputs": "@{variables('myString')}@{variables('myInteger')}",
   "runAfter": {}
},
```

Aqui está a saída que essa ação cria:

`"abcdefg1234"`

<a name="run-javascript-code"></a>

### <a name="execute-javascript-code-action"></a>Executar ação de código JavaScript

Essa ação executa um trecho de código JavaScript e retorna os resultados por `Result` meio de um token que as ações posteriores podem referenciar.

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "<JavaScript-code-snippet>",
      "explicitDependencies": {
         "actions": [ <previous-actions> ],
         "includeTrigger": true
      }
   },
   "runAfter": {}
}
```

*Necessária*

| Valor | Tipo | Descrição |
|-------|------|-------------|
| <*JavaScript-code-snippet*> | Varia | O código JavaScript que você deseja executar. Para obter os requisitos de código e mais informações, consulte [Adicionar e executar trechos de código com código embutido](../logic-apps/logic-apps-add-run-inline-code.md). <p>No atributo, seu trecho de código pode usar o objeto somente `workflowContext` leitura como entrada. `code` Esse objeto tem subpropriedades que dão ao seu código acesso aos resultados do gatilho e ações anteriores em seu fluxo de trabalho. Para obter mais informações sobre `workflowContext` o objeto, consulte [gatilho de referência e resultados de ação em seu código](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext). |
||||

*Necessário em alguns casos*

O `explicitDependencies` atributo especifica que você deseja incluir explicitamente os resultados do gatilho, ações anteriores ou ambas como dependências para o trecho de código. Para obter mais informações sobre como adicionar essas dependências, consulte [adicionar parâmetros para código embutido](../logic-apps/logic-apps-add-run-inline-code.md#add-parameters). 

Para o `includeTrigger` atributo, você pode especificar `true` ou `false` valores.

| Valor | Tipo | Descrição |
|-------|------|-------------|
| <*ações anteriores*> | Matriz de cadeia de caracteres | Uma matriz com os nomes de ação especificados. Use os nomes de ação que aparecem na definição de fluxo de trabalho em que os nomes de ação usam sublinhados (_), não espaços (""). |
||||

*Exemplo 1*

Esta ação executa o código que obtém o nome do aplicativo lógico e retorna o texto "Olá, \<mundo de Logic-app-Name >" como resultado. Neste exemplo, o código faz referência ao nome do fluxo de trabalho acessando a `workflowContext.workflow.name` Propriedade por meio do objeto somente `workflowContext` leitura. Para obter mais informações sobre como `workflowContext` usar o objeto, consulte [gatilho de referência e resultados de ação em seu código](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext).

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "var text = \"Hello world from \" + workflowContext.workflow.name;\r\n\r\nreturn text;"
   },
   "runAfter": {}
}
```

*Exemplo 2*

Esta ação executa o código em um aplicativo lógico que dispara quando um novo email chega em uma conta do Outlook do Office 365. O aplicativo lógico também usa uma ação enviar email de aprovação que encaminha o conteúdo do email recebido junto com uma solicitação de aprovação. 

O código extrai endereços de email da Propriedade do `Body` gatilho e retorna esses endereços de email junto com o `SelectedOption` valor da propriedade da ação de aprovação. A ação inclui explicitamente a ação enviar email de aprovação como uma dependência no `explicitDependencies`  >  `actions` atributo.

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "var re = /(([^<>()\\[\\]\\\\.,;:\\s@\"]+(\\.[^<>()\\[\\]\\\\.,;:\\s@\"]+)*)|(\".+\"))@((\\[[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}])|(([a-zA-Z\\-0-9]+\\.)+[a-zA-Z]{2,}))/g;\r\n\r\nvar email = workflowContext.trigger.outputs.body.Body;\r\n\r\nvar reply = workflowContext.actions.Send_approval_email_.outputs.body.SelectedOption;\r\n\r\nreturn email.match(re) + \" - \" + reply;\r\n;",
      "explicitDependencies": {
         "actions": [
            "Send_approval_email_"
         ]
      }
   },
   "runAfter": {}
}
```



<a name="function-action"></a>

### <a name="function-action"></a>Ação de função

Essa ação chama criado anteriormente [função do Azure](../azure-functions/functions-create-first-azure-function.md).

```json
"<Azure-function-name>": {
   "type": "Function",
   "inputs": {
     "function": {
        "id": "<Azure-function-ID>"
      },
      "method": "<method-type>",
      "headers": { "<header-content>" },
      "body": { "<body-content>" },
      "queries": { "<query-parameters>" } 
   },
   "runAfter": {}
}
```

*Necessária*

| Valor | Tipo | Descrição | 
|-------|------|-------------|  
| <*ID da função do Azure*> | Cadeia | O ID do recurso para a função do Azure que você deseja chamar. Aqui está o formato para este valor:<p>"/subscriptions/<*Azure-subscription-ID*>/resourceGroups/<*Azure-resource-group*>/providers/Microsoft.Web/sites/<*Azure-function-app-name*>/functions/<*Azure-function-name*>" | 
| <*tipo de método*> | Cadeia | O método HTTP a ser usado para chamar a função: "GET", "PUT", "POST", "PATCH" ou "DELETE" <p>Se não for especificado, o padrão é o método "POST". | 
||||

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------|  
| <*conteúdo do cabeçalho*> | Objeto JSON | Nenhum cabeçalho a ser enviado com a chamada <p>Por exemplo, para definir o idioma e o tipo em uma solicitação: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*conteúdo do corpo*> | Objeto JSON | Qualquer conteúdo da mensagem para enviar a solicitação | 
| <*parâmetros de consulta*> | Objeto JSON | Os parâmetros de consulta para incluir com a chamada à API <p>Por exemplo, o `"queries": { "api-version": "2018-01-01" }` objeto adiciona `?api-version=2018-01-01` para a chamada. | 
| < *outras propriedades específicas de entrada de ação*> | Objeto JSON | Quaisquer outras propriedades de entrada que se aplicam a essa ação específica | 
| <*propriedades específicas que outras é ação*> | Objeto JSON | Outras propriedades que se aplicam a essa ação específica | 
||||

Quando você salva seu aplicativo lógico, o mecanismo do Logic Apps executa essas verificações na função referenciada:

* Seu fluxo de trabalho deve ter acesso à função.

* Seu fluxo de trabalho pode usar apenas um gatilho HTTP padrão ou gatilho JSON webhook genérico. 

  O mecanismo do Logic Apps obtém e armazena em cache a URL do acionador, que é usada no tempo de execução. No entanto, se alguma operação invalidar a URL em cache, a ação **Função** falhará no tempo de execução. Para corrigir esse problema, salve o aplicativo lógico novamente para que o aplicativo lógico receba e armazene novamente o URL do acionador.

* A função não pode ter nenhuma rota definida.

* Apenas os níveis de autorização "função" e "anônimo" são permitidos. 

*Exemplo*

Essa definição de ação chama a função de "GetProductID" criada anteriormente:

```json
"GetProductID": {
   "type": "Function",
   "inputs": {
     "function": {
        "id": "/subscriptions/<XXXXXXXXXXXXXXXXXXXX>/resourceGroups/myLogicAppResourceGroup/providers/Microsoft.Web/sites/InventoryChecker/functions/GetProductID"
      },
      "method": "POST",
      "headers": { 
          "x-ms-date": "@utcnow()"
       },
      "body": { 
          "Product_ID": "@variables('ProductID')"
      }
   },
   "runAfter": {}
}
```

<a name="http-action"></a>

### <a name="http-action"></a>Ação HTTP

Essa ação envia uma solicitação ao ponto de extremidade especificado e verifica a resposta para determinar se o fluxo de trabalho deve ser executado. 

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<HTTP-or-HTTPS-endpoint-URL>"
   },
   "runAfter": {}
}
```

*Necessária*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*tipo de método*> | Cadeia | O método a ser usado para enviar a solicitação: "GET", "PUT", "POST", "PATCH" ou "DELETE" | 
| <*HTTP-or-HTTPS-endpoint-URL*> | Cadeia | O ponto de extremidade HTTP ou HTTPS para chamar. Tamanho máximo da cadeia de caracteres: 2 KB | 
|||| 

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*conteúdo do cabeçalho*> | Objeto JSON | Nenhum cabeçalho a ser enviado com a solicitação <p>Por exemplo, para definir o idioma e o tipo: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*conteúdo do corpo*> | Objeto JSON | Qualquer conteúdo da mensagem para enviar a solicitação | 
| <*comportamento de repetição*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de status 408, 429 e 5XX e todas as exceções de conectividade. Para mais informações, consulte [Políticas de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*parâmetros de consulta*> | Objeto JSON | Quaisquer parâmetros de consulta para incluir no pedido <p>Por exemplo, o `"queries": { "api-version": "2018-01-01" }` objeto adiciona `?api-version=2018-01-01` para a chamada. | 
| < *outras propriedades específicas de entrada de ação*> | Objeto JSON | Quaisquer outras propriedades de entrada que se aplicam a essa ação específica | 
| <*propriedades específicas que outras é ação*> | Objeto JSON | Outras propriedades que se aplicam a essa ação específica | 
|||| 

*Exemplo*

Esta definição de ação obtém as últimas notícias enviando uma solicitação para o terminal especificado:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://mynews.example.com/latest"
   }
}
```

<a name="join-action"></a>

### <a name="join-action"></a>Ingressar em ação

Essa ação cria uma cadeia de caracteres de todos os itens em uma matriz e separa os itens com o caractere delimitador especificado. 

```json
"Join": {
   "type": "Join",
   "inputs": {
      "from": <array>,
      "joinWith": "<delimiter>"
   },
   "runAfter": {}
}
```

*Necessária*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*array*> | Array | A matriz ou expressão que fornece os itens de origem. Se você especificar uma expressão, coloque essa expressão entre aspas duplas. | 
| <*delimiter*> | Cadeia de caracteres única | O caractere que separa cada item na cadeia de caracteres | 
|||| 

*Exemplo*

Suponha que você tenha uma variável "myIntegerArray" criada anteriormente que contenha essa matriz de inteiros: 

`[1,2,3,4]` 

Esta definição de ação obtém os valores da variável usando a função `variables()`em uma expressão e cria essa sequência com esses valores, que são separados por uma vírgula:`"1,2,3,4"`

```json
"Join": {
   "type": "Join",
   "inputs": {
      "from": "@variables('myIntegerArray')",
      "joinWith": ","
   },
   "runAfter": {}
}
```

<a name="parse-json-action"></a>

### <a name="parse-json-action"></a>Analisar a ação de JSON

Essa ação cria campos amigáveis ou *tokens* das propriedades no conteúdo JSON. Você pode acessar essas propriedades em seu aplicativo lógico usando os tokens. Por exemplo, quando você deseja usar a saída JSON de serviços como o Barramento de Serviço do Azure e o Banco de Dados do Azure Cosmos, é possível incluir essa ação em seu aplicativo lógico para poder referenciar mais facilmente os dados nessa saída. 

```json
"Parse_JSON": {
   "type": "ParseJson",
   "inputs": {
      "content": "<JSON-source>",
         "schema": { "<JSON-schema>" }
      },
      "runAfter": {}
},
```

*Necessária*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*Origem do JSON*> | Objeto JSON | O conteúdo JSON que você deseja analisar | 
| <*JSON-schema*> | Objeto JSON | O esquema JSON que descreve o conteúdo JSON subjacente, que a ação usa para analisar o conteúdo JSON de origem. <p>**Dica**: no Designer de Aplicativos Lógicos, você pode fornecer o esquema ou fornecer uma amostra de conteúdo para que a ação possa gerar o esquema. | 
|||| 

*Exemplo*

Essa definição de ação cria esses tokens que você pode usar em seu fluxo de trabalho, mas somente em ações que são executadas após a ação **analisar JSON** : 

`FirstName`, `LastName`, e `Email`

```json
"Parse_JSON": {
   "type": "ParseJson",
   "inputs": {
      "content": {
         "Member": {
            "Email": "Sophie.Owen@contoso.com",
            "FirstName": "Sophie",
            "LastName": "Owen"
         }
      },
      "schema": {
         "type": "object",
         "properties": {
            "Member": {
               "type": "object",
               "properties": {
                  "Email": {
                     "type": "string"
                  },
                  "FirstName": {
                     "type": "string"
                  },
                  "LastName": {
                     "type": "string"
                  }
               }
            }
         }
      }
   },
   "runAfter": { }
},
```

Neste exemplo, a propriedade "content" especifica o conteúdo JSON da ação a ser analisada. Você também pode fornecer esse conteúdo JSON como a carga útil da amostra para gerar o esquema.

```json
"content": {
   "Member": { 
      "FirstName": "Sophie",
      "LastName": "Owen",
      "Email": "Sophie.Owen@contoso.com"
   }
},
```

A propriedade "esquema" Especifica o esquema JSON usado para descrever o conteúdo JSON:

```json
"schema": {
   "type": "object",
   "properties": {
      "Member": {
         "type": "object",
         "properties": {
            "FirstName": {
               "type": "string"
            },
            "LastName": {
               "type": "string"
            },
            "Email": {
               "type": "string"
            }
         }
      }
   }
}
```

<a name="query-action"></a>

### <a name="query-action"></a>Ação de consulta

Essa ação cria uma matriz de itens na outra matriz com base em uma condição especificada ou o filtro.

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": <array>,
      "where": "<condition-or-filter>"
   },
   "runAfter": {}
}
```

*Necessária*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*array*> | Array | A matriz ou expressão que fornece os itens de origem. Se você especificar uma expressão, coloque essa expressão entre aspas duplas. |
| <*filtro ou condição*> | Cadeia | A condição usada para filtrar itens na matriz de origem <p>**Observação**: se nenhum valor atender à condição, a ação criará uma matriz vazia. |
|||| 

*Exemplo*

Essa definição de ação cria uma matriz que tem valores maiores do que o valor especificado, o que é dois:

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": [ 1, 3, 0, 5, 4, 2 ],
      "where": "@greater(item(), 2)"
   }
}
```

<a name="response-action"></a>

### <a name="response-action"></a>Ação de resposta  

Essa ação cria a carga para a resposta a uma solicitação HTTP. 

```json
"Response" {
    "type": "Response",
    "kind": "http",
    "inputs": {
        "statusCode": 200,
        "headers": { <response-headers> },
        "body": { <response-body> }
    },
    "runAfter": {}
},
```

*Necessária*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*código de status de resposta*> | Inteiro | O código de status HTTP enviado para a solicitação recebida. O código padrão é "200 OK", mas o código pode ser qualquer código de status válido que comece com 2xx, 4xx ou 5xx, mas não com 3xxx. | 
|||| 

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*cabeçalhos de resposta*> | Objeto JSON | Um ou mais cabeçalhos para incluir com a resposta | 
| <*corpo de resposta*> | Vários | O corpo da resposta, que pode ser uma string, um objeto JSON ou até mesmo conteúdo binário de uma ação anterior | 
|||| 

*Exemplo*

Esta definição de ação cria uma resposta a uma solicitação HTTP com o código de status, o corpo da mensagem e os cabeçalhos de mensagens especificados:

```json
"Response": {
   "type": "Response",
   "inputs": {
      "statusCode": 200,
      "body": {
         "ProductID": 0,
         "Description": "Organic Apples"
      },
      "headers": {
         "x-ms-date": "@utcnow()",
         "content-type": "application/json"
      }
   },
   "runAfter": {}
}
```

*Restrições*

Ao contrário de outras ações, a ação **Response** tem restrições especiais: 

* O fluxo de trabalho pode usar o **resposta** ação somente quando o fluxo de trabalho é iniciado com um gatilho de solicitação HTTP, que significa que seu fluxo de trabalho deve ser disparada por uma solicitação HTTP.

* Seu fluxo de trabalho pode usar a ação **Resposta** em qualquer lugar *exceto* dentro de **Foreach** loops, **até** loops, incluindo loops sequenciais e ramificações paralelas. 

* A solicitação HTTP original obtém a resposta do seu fluxo de trabalho somente quando todas as ações exigidas pela ação **Response** são concluídas dentro do limite de tempo [limite da solicitação HTTP](../logic-apps/logic-apps-limits-and-config.md#request-limits).

  No entanto, se o seu fluxo de trabalho chamar outro aplicativo lógico como um fluxo de trabalho aninhado, o fluxo de trabalho pai aguardará até que o fluxo de trabalho aninhado seja concluído, independentemente de quanto tempo passe antes que o fluxo de trabalho aninhado seja concluído.

* Quando seu fluxo de trabalho usa a ação **Response** e um padrão de resposta síncrona, o fluxo de trabalho também não pode usar o comando **splitOn** na definição do acionador porque esse comando cria várias execuções. Verifique este caso quando o método PUT for usado e, se true, retorne uma resposta "solicitação incorreta".

  Caso contrário, se seu fluxo de trabalho usar o comando **splitOn** e uma ação **Response**, o fluxo de trabalho será executado de forma assíncrona e retornará imediatamente uma resposta "202 ACCEPTED".

* Quando a execução do fluxo de trabalho atinge a ação **Resposta**, mas a solicitação recebida já recebeu uma resposta, a ação **Resposta** é marcada como "Falhou" devido ao conflito. E, como resultado, a execução do seu aplicativo lógico também é marcado com status "Falha".

<a name="select-action"></a>

### <a name="select-action"></a>Ação selecionar

Essa ação cria uma matriz com objetos JSON, transformando itens de outra matriz com base no mapa especificado. O array de saída e o array de origem sempre tem o mesmo número de itens. Embora não seja possível alterar o número de objetos na matriz de saída, você pode adicionar ou remover propriedades e seus valores nesses objetos. A propriedade `select` especifica pelo menos um par de valores-chave que define o mapa para transformar itens na matriz de origem. Um par de valores-chave representa uma propriedade e seu valor em todos os objetos na matriz de saída. 

```json
"Select": {
   "type": "Select",
   "inputs": {
      "from": <array>,
      "select": { 
          "<key-name>": "<expression>",
          "<key-name>": "<expression>"        
      }
   },
   "runAfter": {}
},
```

*Necessária* 

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*array*> | Array | A matriz ou expressão que fornece os itens de origem. Certifique-se de que você coloque uma expressão entre aspas duplas. <p>**Observação**: se a matriz de origem estiver vazia, a ação criará uma matriz vazia. | 
| <*key-name*> | Cadeia | O nome da propriedade atribuído ao resultado de <*expressão*> <p>Para adicionar uma nova propriedade a todos os objetos na matriz de saída, forneça um <*nome-chave*> para essa propriedade e uma <*expressão*> para o valor da propriedade. <p>Para remover uma propriedade de todos os objetos na matriz, omita o <*nome-chave*> para essa propriedade. | 
| <*expression*> | Cadeia | A expressão que transforma o item na matriz de origem e atribui o resultado a <*nome-chave* > | 
|||| 

A ação **Select** cria uma matriz como saída, portanto, qualquer ação que queira usar essa saída deve aceitar uma matriz ou converter a matriz no tipo que a ação do consumidor aceita. Por exemplo, para converter a matriz de saída em uma string, você pode passar essa matriz para a ação **Compor** e, em seguida, referenciar a saída da ação **Compor** em suas outras ações.

*Exemplo*

Essa definição de ação cria uma matriz de objetos JSON de uma matriz de inteiros. A ação itera na matriz de origem, obtém a cada valor de inteiro usando o `@item()` expressão e atribui cada valor para o "`number`" propriedade em cada objeto JSON: 

```json
"Select": {
   "type": "Select",
   "inputs": {
      "from": [ 1, 2, 3 ],
      "select": { 
         "number": "@item()" 
      }
   },
   "runAfter": {}
},
```

Aqui está o array que esta ação cria:

`[ { "number": 1 }, { "number": 2 }, { "number": 3 } ]`

Para usar essa matriz de saída em outras ações, passar essa saída em uma **redigir** ação:

```json
"Compose": {
   "type": "Compose",
   "inputs": "@body('Select')",
   "runAfter": {
      "Select": [ "Succeeded" ]
   }
},
```

Em seguida, você pode usar a saída do **redigir** ação em suas outras ações, por exemplo, o **Outlook do Office 365 - enviar um email** ação:

```json
"Send_an_email": {
   "type": "ApiConnection",
   "inputs": {
      "body": {
         "Body": "@{outputs('Compose')}",
         "Subject": "Output array from Select and Compose actions",
         "To": "<your-email@domain>"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "post",
      "path": "/Mail"
   },
   "runAfter": {
      "Compose": [ "Succeeded" ]
   }
},
```

<a name="table-action"></a>

### <a name="table-action"></a>Ação tabela

Esta ação cria uma tabela CSV ou HTML a partir de uma matriz. Para matrizes com objetos JSON, essa ação cria automaticamente os cabeçalhos das colunas a partir dos nomes das propriedades dos objetos. Para matrizes com outros tipos de dados, você deve especificar os cabeçalhos e valores da coluna. Por exemplo, essa matriz inclui as propriedades "ID" e "Product_Name" que essa ação pode usar para os nomes do cabeçalho da coluna:

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]` 

```json
"Create_<CSV | HTML>_table": {
   "type": "Table",
   "inputs": {
      "format": "<CSV | HTML>",
      "from": <array>,
      "columns": [ 
         {
            "header": "<column-name>",
            "value": "<column-value>"
         },
         {
            "header": "<column-name>",
            "value": "<column-value>"
         } 
      ]
   },
   "runAfter": {}
}
```

*Necessária* 

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| \<> CSV *ou* HTML| Cadeia | O formato para a tabela que você deseja criar | 
| <*array*> | Array | A matriz ou expressão que fornece os itens de origem para a tabela <p>**Observação**: se a matriz de origem estiver vazia, a ação criará uma tabela vazia. | 
|||| 

*Opcional*

Para especificar ou customizar cabeçalhos e valores de coluna, use a matriz`columns`. Quando os pares `header-value` tiverem o mesmo nome de cabeçalho, seus valores aparecerão na mesma coluna sob esse nome de cabeçalho. Caso contrário, cada cabeçalho exclusivo define uma coluna exclusiva.

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*nome da coluna*> | Cadeia | O nome do cabeçalho de uma coluna | 
| <*valor da coluna*> | Any | O valor nessa coluna | 
|||| 

*Exemplo 1*

Suponha que você tenha uma variável "myItemArray" criada anteriormente que atualmente contém essa matriz: 

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]`

Esta definição de ação cria uma tabela CSV a partir da variável "myItemArray". A expressão usada pela propriedade `from`obtém a matriz de "myItemArray" usando a função`variables()`: 

```json
"Create_CSV_table": {
   "type": "Table",
   "inputs": {
      "format": "CSV",
      "from": "@variables('myItemArray')"
   },
   "runAfter": {}
}
```

Aqui está a tabela CSV que essa ação cria: 

```
ID,Product_Name 
0,Apples 
1,Oranges 
```

*Exemplo 2*

Esta definição de ação cria uma tabela HTML a partir da variável "myItemArray". A expressão usada pela propriedade `from`obtém a matriz de "myItemArray" usando a função`variables()`: 

```json
"Create_HTML_table": {
   "type": "Table",
   "inputs": {
      "format": "HTML",
      "from": "@variables('myItemArray')"
   },
   "runAfter": {}
}
```

Aqui está a tabela HTML que essa ação cria: 

<table><thead><tr><th>ID</th><th>Product_Name</th></tr></thead><tbody><tr><td>0</td><td>Maçãs</td></tr><tr><td>1</td><td>Laranjas</td></tr></tbody></table>

*Exemplo 3*

Esta definição de ação cria uma tabela HTML a partir da variável "myItemArray". No entanto, esse exemplo substitui os nomes de cabeçalho de coluna padrão por "Stock_ID" e "Description" e adiciona a palavra "Organic" aos valores na coluna "Descrição".

```json
"Create_HTML_table": {
   "type": "Table",
   "inputs": {
      "format": "HTML",
      "from": "@variables('myItemArray')",
      "columns": [ 
         {
            "header": "Stock_ID",
            "value": "@item().ID"
         },
         {
            "header": "Description",
            "value": "@concat('Organic ', item().Product_Name)"
         }
      ]
    },
   "runAfter": {}
},
```

Aqui está a tabela HTML que essa ação cria: 

<table><thead><tr><th>Stock_ID</th><th>Descrição</th></tr></thead><tbody><tr><td>0</td><td>Maçãs orgânicas</td></tr><tr><td>1</td><td>Laranjas orgânicas</td></tr></tbody></table>

<a name="terminate-action"></a>

### <a name="terminate-action"></a>Ação para finalizar

Essa ação interrompe a execução de uma instância de fluxo de trabalho, cancela as ações em andamento, ignora as ações restantes e retorna o status especificado. Por exemplo, você pode usar a ação **Encerrar** quando seu aplicativo lógico precisar sair completamente de um estado de erro. Essa ação não afeta as ações já concluídas e não pode aparecer dentro de loops **Foreach** e **Until**, incluindo loops sequenciais. 

```json
"Terminate": {
   "type": "Terminate",
   "inputs": {
       "runStatus": "<status>",
       "runError": {
            "code": "<error-code-or-name>",
            "message": "<error-message>"
       }
   },
   "runAfter": {}
}
```

*Necessária*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*Status*> | Cadeia | O status a ser retornado para a execução: "Falhou", "Cancelado" ou "Êxito" |
|||| 

*Opcional*

As propriedades para o objeto "runStatus" aplicam-se somente quando a propriedade "runStatus" está configurada para o status "Failed".

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*Erro de código ou nome*> | Cadeia | O código ou o nome do erro |
| <*mensagem de erro*> | Cadeia | A mensagem ou o texto que descreve o erro e as ações do usuário do aplicativo pode levar | 
|||| 

*Exemplo*

Essa definição de ação interrompe uma execução de fluxo de trabalho, define o status de execução como "Falha" e retorna o status, um código de erro e uma mensagem de erro:

```json
"Terminate": {
    "type": "Terminate",
    "inputs": {
        "runStatus": "Failed",
        "runError": {
            "code": "Unexpected response",
            "message": "The service received an unexpected response. Please try again."
        }
   },
   "runAfter": {}
}
```

<a name="wait-action"></a>

### <a name="wait-action"></a>Ação para aguardar  

Esta ação pausa a execução do fluxo de trabalho para o intervalo especificado ou até o horário especificado, mas não ambos. 

*Intervalo especificado*

```json
"Delay": {
   "type": "Wait",
   "inputs": {
      "interval": {
         "count": <number-of-units>,
         "unit": "<interval>"
      }
   },
   "runAfter": {}
},
```

*Tempo especificado*

```json
"Delay_until": {
   "type": "Wait",
   "inputs": {
      "until": {
         "timestamp": "<date-time-stamp>"
      }
   },
   "runAfter": {}
},
```

*Necessária*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*número de unidades*> | Inteiro | Para o **atraso** ação, o número de unidades de espera | 
| <*interval*> | Cadeia | Para a ação **Atrasar**, o intervalo de espera: "Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês" | 
| <*carimbo de data-hora*> | Cadeia | Para o **atraso até que** ação, a data e hora para retomar a execução. Esse valor deve usar o [formato de hora UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time). | 
|||| 

*Exemplo 1*

Essa definição de ação pausa o fluxo de trabalho por 15 minutos:

```json
"Delay": {
   "type": "Wait",
   "inputs": {
      "interval": {
         "count": 15,
         "unit": "Minute"
      }
   },
   "runAfter": {}
},
```

*Exemplo 2*

Essa definição de ação pausa o fluxo de trabalho até o tempo especificado:

```json
"Delay_until": {
   "type": "Wait",
   "inputs": {
      "until": {
         "timestamp": "2017-10-01T00:00:00Z"
      }
   },
   "runAfter": {}
},
```

<a name="workflow-action"></a>

### <a name="workflow-action"></a>Ação do fluxo de trabalho

Essa ação chama outro aplicativo lógico criado anteriormente, o que significa que você pode incluir e reutilizar outros fluxos de trabalho de aplicativos lógicos. Você também pode usar as saídas do aplicativo lógico filho ou *aninhado* em ações que seguem o aplicativo lógico aninhado, desde que o aplicativo de lógica filho retorne uma resposta.

O mecanismo do Logic Apps verifica o acesso ao acionador que você deseja chamar, portanto, verifique se você pode acessar esse acionador. Além disso, o aplicativo lógico aninhado deve atender a esses critérios:

* Um disparador faz com que o aplicativo lógico aninhado pode ser chamado como uma [solicitar](#request-trigger) ou [HTTP](#http-trigger) gatilho

* A mesma assinatura do Azure que seu aplicativo lógico de pai

* Para usar as saídas do aplicativo lógico aninhado no aplicativo lógico pai, o aplicativo lógico aninhado deve ter uma [resposta](#response-action) ação 

```json
"<nested-logic-app-name>": {
   "type": "Workflow",
   "inputs": {
      "body": { "<body-content" },
      "headers": { "<header-content>" },
      "host": {
         "triggerName": "<trigger-name>",
         "workflow": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group>/providers/Microsoft.Logic/<nested-logic-app-name>"
         }
      }
   },
   "runAfter": {}
}
```

*Necessária*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*Nested---nome do aplicativo lógico*> | Cadeia | O nome do aplicativo lógico que você deseja chamar | 
| <*trigger-name*> | Cadeia | O nome do gatilho no aplicativo lógico aninhado que você deseja chamar | 
| <*ID da assinatura do Azure*> | Cadeia | A ID de assinatura do Azure para o aplicativo lógico aninhado |
| <*Azure-resource-group*> | Cadeia | O nome do grupo de recursos do Azure para o aplicativo lógico aninhado |
| <*Nested---nome do aplicativo lógico*> | Cadeia | O nome do aplicativo lógico que você deseja chamar |
||||

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------|  
| <*conteúdo do cabeçalho*> | Objeto JSON | Nenhum cabeçalho a ser enviado com a chamada | 
| <*conteúdo do corpo*> | Objeto JSON | Qualquer conteúdo da mensagem a ser enviado com a chamada | 
||||

*Saídas*

Os resultados desta ação variam com base na ação de resposta do aplicativo lógico aninhado. Se o aplicativo lógico aninhado não incluir uma ação de resposta, as saídas estarão vazias.

*Exemplo*

Depois que a ação "Start_search" for concluída com êxito, essa definição de ação de fluxo de trabalho chama outro aplicativo lógico chamado "Get_product_information", que passa em entradas especificadas: 

```json
"actions": {
   "Start_search": { <action-definition> },
   "Get_product_information": {
      "type": "Workflow",
      "inputs": {
         "body": {
            "ProductID": "24601",
         },
         "host": {
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/InventoryManager-RG/providers/Microsoft.Logic/Get_product_information",
            "triggerName": "Find_product"
         },
         "headers": {
            "content-type": "application/json"
         }
      },
      "runAfter": { 
         "Start_search": [ "Succeeded" ]
      }
   }
},
```

## <a name="control-workflow-action-details"></a>Detalhes de ação de fluxo de trabalho de controle

<a name="foreach-action"></a>

### <a name="foreach-action"></a>Ação Foreach

Essa ação de loop itera através de uma matriz e executa ações em cada item da matriz. Por padrão, o loop "para cada" é executado em paralelo até um número máximo de loops. Para esse máximo, consulte [limites e configurações](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Saiba mais [como criar "for each" loops](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

```json
"For_each": {
   "type": "Foreach",
   "actions": { 
      "<action-1>": { "<action-definition-1>" },
      "<action-2>": { "<action-definition-2>" }
   },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": <count>
      }
    },
    "operationOptions": "<operation-option>"
}
```

*Necessária* 

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*ação-1... n*> | Cadeia | Os nomes das ações que são executados em cada item da matriz | 
| <*ação-definição-1... n*> | Objeto JSON | As definições das ações que são executados | 
| <*para cada expressão*> | Cadeia | A expressão que referencia cada item na matriz especificada | 
|||| 

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*count*> | Inteiro | Por padrão, as iterações do loop "para cada" são executadas ao mesmo tempo ou em paralelo até o limite [padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar esse limite, definindo um novo <*contagem*> de valor, consulte [alterar "loop"for each simultaneidade](#change-for-each-concurrency). | 
| <*opção de operação*> | Cadeia | Para executar um loop "for each" em sequência, em vez de em paralelo, defina <*opção de operação*> para `Sequential` ou <*contagem*> para `1`, mas não ambos. Para obter mais informações, consulte [executar "" for each executa um loop em sequência](#sequential-for-each). | 
|||| 

*Exemplo*

Esse loop "para cada" envia um email para cada item da matriz, que contém anexos de um email recebido. O loop envia um email, incluindo o anexo, para uma pessoa que revisa o anexo.

```json
"For_each": {
   "type": "Foreach",
   "actions": {
      "Send_an_email": {
         "type": "ApiConnection",
         "inputs": {
            "body": {
               "Body": "@base64ToString(items('For_each')?['Content'])",
               "Subject": "Review attachment",
               "To": "Sophie.Owen@contoso.com"
                },
            "host": {
               "connection": {
                  "id": "@parameters('$connections')['office365']['connectionId']"
               }
            },
            "method": "post",
            "path": "/Mail"
         },
         "runAfter": {}
      }
   },
   "foreach": "@triggerBody()?['Attachments']",
   "runAfter": {}
}
```

Para especificar apenas uma matriz que é transmitida como saída do acionador, essa expressão obtém a matriz <*nome da matriz*> do corpo do acionador. Para evitar uma falha se a matriz não existir, a expressão usa o operador `?`:

`@triggerBody()?['<array-name>']` 

<a name="if-action"></a>

### <a name="if-action"></a>Ação If

Essa ação, *que é uma instrução condicional*, avalia uma expressão que representa uma condição e executa uma ramificação diferente com base no fato de a condição ser verdadeira ou falsa. Se a condição for verdadeira, a condição é marcada com o status "Sucedido". Aprenda [como criar instruções condicionais](../logic-apps/logic-apps-control-flow-conditional-statement.md).

``` json
"Condition": {
   "type": "If",
   "expression": { "<condition>" },
   "actions": {
      "<action-1>": { "<action-definition>" }
   },
   "else": {
      "actions": {
        "<action-2>": { "<action-definition" }
      }
   },
   "runAfter": {}
}
```

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*Condição*> | Objeto JSON | A condição, o que pode ser uma expressão, para avaliar | 
| <*ação-1*> | Objeto JSON | A ação a ser executada quando a <*condição*> é avaliada como verdadeira | 
| <*definição de ação*> | Objeto JSON | A definição da ação | 
| <*ação-2*> | Objeto JSON | A ação a ser executada quando <*condição*> for avaliada como falsa | 
|||| 

As ações na `actions` ou `else` objetos obtém esses status:

* "Êxito" quando são executadas e bem-sucedidas
* "Falha" quando são executadas e falham
* "Ignorado" quando a respectiva ramificação não é executada

*Exemplo*

Essa condição especifica que, quando a variável integer tiver um valor maior que zero, o fluxo de trabalho verificará um site. Se a variável for zero ou menos, o fluxo de trabalho verifica um site diferente.

```json
"Condition": {
   "type": "If",
   "expression": {
      "and": [ {
         "greater": [ "@variables('myIntegerVariable')", 0 ] 
      } ]
   },
   "actions": { 
      "HTTP - Check this website": {
         "type": "Http",
         "inputs": {
         "method": "GET",
            "uri": "http://this-url"
         },
         "runAfter": {}
      }
   },
   "else": {
      "actions": {
         "HTTP - Check this other website": {
            "type": "Http",
            "inputs": {
               "method": "GET",
               "uri": "http://this-other-url"
            },
            "runAfter": {}
         }
      }
   },
   "runAfter": {}
}
```  

#### <a name="how-conditions-use-expressions"></a>Como as condições usar expressões

Estes são alguns exemplos que mostram como você pode usar expressões em condições:
  
| JSON | Resultado | 
|------|--------| 
| "expressão": "@parameters('<*hasSpecialAction*>')" | Apenas para expressões booleanas, a condição passa para qualquer valor que seja avaliado como verdadeiro. <p>Para converter outros tipos em Boolean, use estas funções: `empty()` ou `equals()`. | 
| "expressão": "@greater(actions('<*action*>').output.value, parâmetros ('<*limite*>'))" | Para funções de comparação, a ação é executada somente quando a saída do <*ação*> é mais do que <*limite*> valor. | 
| "expression": "@or (maior (actions ('<*action*>'). output.value, parameters ('<*limiar*>')), less (ações ( '<*same-action*>'). Output.value, 100))" | Para funções lógicas e criando aninhados expressões Boolianas, a ação é executada quando a saída do <*ação*> é mais do que <*limite*> valor ou em 100. | 
| "expressão": "@equals(comprimento (actions('<*action*>').outputs.errors), 0))" | Você pode usar funções de matriz para verificar se a matriz possui algum item. A ação é executada quando o `errors` matriz está vazia. | 
||| 

<a name="scope-action"></a>

### <a name="scope-action"></a>Ação de escopo

Esta ação agrupa logicamente as ações em *escopos*, que obtêm seu próprio status depois que as ações nesse escopo terminam a execução. Você pode então usar o status do escopo para determinar se outras ações são executadas. Saiba mais [como criar escopos](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md).

```json
"Scope": {
   "type": "Scope",
   "actions": {
      "<inner-action-1>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      },
      "<inner-action-2>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      }
   }
}
```

*Necessária*

| Valor | Tipo | Descrição | 
|-------|------|-------------|  
| <*interno-ação-1... n*> | Objeto JSON | Uma ou mais ações que são executadas dentro do escopo |
| <*entradas de ação*> | Objeto JSON | As entradas para cada ação |
|||| 

<a name="switch-action"></a>

### <a name="switch-action"></a>Ação Switch

Essa ação, também conhecida como *switch statement*, organiza outras ações em *casos* e atribui um valor a cada caso, exceto no caso padrão, se existir um. Quando seu fluxo de trabalho é executado, a ação **Switch** compara o valor de uma expressão, objeto ou token com os valores especificados para cada caso. Se a ação **Switch** encontrar um caso correspondente, seu fluxo de trabalho executará apenas as ações para esse caso. Toda vez que a ação **Switch** é executada, apenas um caso correspondente existe ou não existem correspondências. Se nenhuma correspondência existir, o **comutador** ação executa as ações padrão. Saiba mais [como criar instruções switch](../logic-apps/logic-apps-control-flow-switch-statement.md).

``` json
"Switch": {
   "type": "Switch",
   "expression": "<expression-object-or-token>",
   "cases": {
      "Case": {
         "actions": {
           "<action-name>": { "<action-definition>" }
         },
         "case": "<matching-value>"
      },
      "Case_2": {
         "actions": {
           "<action-name>": { "<action-definition>" }
         },
         "case": "<matching-value>"
      }
   },
   "default": {
      "actions": {
         "<default-action-name>": { "<default-action-definition>" }
      }
   },
   "runAfter": {}
}
```

*Necessária*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*expression-object-or-token*> | Varia | A expressão, um objeto JSON ou um token para avaliar | 
| <*nome da ação*> | Cadeia | O nome da ação a ser executada para o caso correspondente | 
| <*definição de ação*> | Objeto JSON | A definição da ação a ser executada para o caso correspondente | 
| <*matching-value*> | Varia | O valor para comparar com o resultado avaliado | 
|||| 

*Opcional*

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*default-action-name*> | Cadeia | O nome da ação padrão a ser executada quando nenhum caso correspondente existir | 
| <*definição de ação padrão*> | Objeto JSON | A definição para a ação a ser executada quando não existir nenhum caso correspondente | 
|||| 

*Exemplo*

Esta definição de ação avalia se a pessoa que está respondendo ao email de solicitação de aprovação selecionou a opção "Aprovar" ou a opção "Rejeitar". Com base nessa opção, a ação **Switch** executa as ações do caso correspondente, que é enviar outro e-mail para o respondente, mas com palavras diferentes em cada caso. 

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions": {
            "Send_an_email": { 
               "type": "ApiConnection",
               "inputs": {
                  "Body": "Thank you for your approval.",
                  "Subject": "Response received", 
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}
         },
         "case": "Approve"
      },
      "Case_2": {
         "actions": {
            "Send_an_email_2": { 
               "type": "ApiConnection",
               "inputs": {
                  "Body": "Thank you for your response.",
                  "Subject": "Response received", 
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}     
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": { 
         "Send_an_email_3": { 
            "type": "ApiConnection",
            "inputs": {
               "Body": "Please respond with either 'Approve' or 'Reject'.",
               "Subject": "Please respond", 
               "To": "Sophie.Owen@contoso.com"
            },
            "host": {
               "connection": {
                  "name": "@parameters('$connections')['office365']['connectionId']"
               }
            },
            "method": "post",
            "path": "/Mail"
         },
         "runAfter": {} 
      }
   },
   "runAfter": {
      "Send_approval_email": [ 
         "Succeeded"
      ]
   }
}
```

<a name="until-action"></a>

### <a name="until-action"></a>Ação Until

Esta ação de loop contém ações que são executadas até que a condição especificada seja verdadeira. O loop verifica a condição como a última etapa após todas as outras ações terem sido executadas. Você pode incluir mais de uma ação no `"actions"` objeto e a ação devem definir pelo menos um limite. Saiba mais [como criar "loops until"](../logic-apps/logic-apps-control-flow-loops.md#until-loop). 

```json
 "Until": {
   "type": "Until",
   "actions": {
      "<action-name>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      },
      "<action-name>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      }
   },
   "expression": "<condition>",
   "limit": {
      "count": <loop-count>,
      "timeout": "<loop-timeout>"
   },
   "runAfter": {}
}
```

| Valor | Tipo | Descrição | 
|-------|------|-------------| 
| <*nome da ação*> | Cadeia | O nome para a ação que você deseja executar dentro do loop | 
| <*tipo de ação*> | Cadeia | O tipo de ação que você deseja executar | 
| <*entradas de ação*> | Vários | As entradas para a ação a ser executada | 
| <*Condição*> | Cadeia | A condição ou expressão a ser avaliada depois que todas as ações no loop concluem a execução | 
| <*Contagem de loop*> | Inteiro | O limite no maior número de loops que a ação pode executar. O padrão `count` valor é 60. | 
| <*tempo limite de loop*> | Cadeia | O limite de tempo mais longo que o loop pode ser executados. O padrão `timeout` valor é `PT1H`, que é o necessário [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). |
|||| 

*Exemplo*

Essa definição de ação de loop envia uma solicitação HTTP para a URL especificada até que uma dessas condições seja atendida: 

* A solicitação obtém uma resposta com o código de status "200 OK".
* O loop foi executado 60 vezes.
* O loop foi executado por uma hora.

```json
 "Run_until_loop_succeeds_or_expires": {
    "type": "Until",
    "actions": {
        "Http": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "expression": "@equals(outputs('Http')['statusCode', 200])",
    "limit": {
        "count": 60,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

<a name="subscribe-unsubscribe"></a>

## <a name="webhooks-and-subscriptions"></a>Webhooks e assinaturas

Acionadores e ações com base em Webhook não verificam regularmente os pontos de extremidade, mas aguardam eventos ou dados específicos nesses pontos de extremidade. Esses acionadores e ações *se inscrevem* nos pontos de extremidade fornecendo um *URL de retorno de chamada* no qual o terminal pode enviar respostas.

A chamada `subscribe` ocorre quando o fluxo de trabalho é alterado de alguma forma, por exemplo, quando as credenciais são renovadas ou quando os parâmetros de entrada são alterados para um acionador ou uma ação. Essa chamada usa os mesmos parâmetros que ações HTTP padrão. 

O `unsubscribe` chamada ocorre automaticamente quando uma operação faz o gatilho ou ação inválido, por exemplo:

* Excluir ou desabilitar o gatilho. 
* excluir ou desabilitar o fluxo de trabalho. 
* excluir ou desabilitar a assinatura. 

Para suportar essas chamadas, a expressão `@listCallbackUrl()` retorna um "URL de retorno de chamada" exclusivo para o acionador ou a ação. Essa URL representa um identificador exclusivo para os pontos de extremidade que usam a API REST do serviço. Os parâmetros para essa função são os mesmos do acionador ou ação do webhook.

<a name="asynchronous-limits"></a>

## <a name="change-asynchronous-duration"></a>Alterar a duração assíncrona

Para acionadores e ações, você pode limitar a duração do padrão assíncrono a um intervalo de tempo específico, adicionando a `limit.timeout` propriedade. Dessa forma, se a ação não tiver terminado quando o intervalo decorrer, o status da ação será marcado como `Cancelled`com o`ActionTimedOut` código. O `timeout` usos de propriedade [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). 

``` json
"<trigger-or-action-name>": {
   "type": "Workflow | Webhook | Http | ApiConnectionWebhook | ApiConnection",
   "inputs": {},
   "limit": {
      "timeout": "PT10S"
   },
   "runAfter": {}
}
```

<a name="runtime-config-options"></a>

## <a name="runtime-configuration-settings"></a>Definições de configuração de tempo de execução

Você pode alterar o comportamento de tempo de execução padrão para acionadores e ações com essas propriedades `runtimeConfiguration` na definição de acionador ou ação.

| Propriedade | Tipo | Descrição | Gatilho ou ação | 
|----------|------|-------------|-------------------| 
| `runtimeConfiguration.concurrency.runs` | Inteiro | Altere o [*limite padrão*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) no número de instâncias de fluxo de trabalho que podem ser executadas ao mesmo tempo ou em paralelo. Esse valor pode ajudar a limitar o número de solicitações recebidas pelos sistemas de back-end. <p>A configuração da `runs`propriedade para`1` funciona da mesma maneira que a configuração da propriedade `operationOptions` para `SingleInstance`. Você pode definir a propriedade, mas não ambos. <p>Para alterar o limite padrão, consulte [simultaneidade do gatilho de alteração](#change-trigger-concurrency) ou [disparar instâncias sequencialmente](#sequential-trigger). | Todos os gatilhos | 
| `runtimeConfiguration.concurrency.maximumWaitingRuns` | Inteiro | Altere o [*limite padrão*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) no número de instâncias de fluxo de trabalho que podem esperar para serem executadas quando o fluxo de trabalho já estiver executando o máximo de instâncias simultâneas. Você pode alterar o limite de simultaneidade na propriedade `concurrency.runs`. <p>Para alterar o limite padrão, consulte [execuções de espera da alteração limitam](#change-waiting-runs). | Todos os gatilhos | 
| `runtimeConfiguration.concurrency.repetitions` | Inteiro | Alterar o [*limite padrão*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) no número de "for each" que podem ser executados ao mesmo tempo, ou em paralelo iterações do loop. <p>Definindo o `repetitions` propriedade para `1` funciona da mesma forma que a configuração a `operationOptions` propriedade `SingleInstance`. Você pode definir a propriedade, mas não ambos. <p>Para alterar o limite padrão, consulte [Alterar "para cada" simultaneidade](#change-for-each-concurrency) ou [Executar "para cada" faz um loop sequencialmente](#sequential-for-each). | Ação: <p>[Foreach](#foreach-action) | 
| `runtimeConfiguration.paginationPolicy.minimumItemCount` | Inteiro | Para ações específicas que dão suporte e têm a paginação ativada, esse valor especifica o número *mínimo* de resultados a serem recuperados. <p>Para ativar a paginação, consulte [obter dados em massa, itens ou resultados usando](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md) paginação | Ação: Variado |
| `runtimeConfiguration.secureData.properties` | Array | Em muitos gatilhos e ações, essas configurações ocultam entradas, saídas ou ambos do histórico de execução do aplicativo lógico. <p>Para proteger esses dados, consulte [ocultar entradas e saídas do histórico de execuções](../logic-apps/logic-apps-securing-a-logic-app.md#secure-data-code-view). | Maioria dos gatilhos e ações |
| `runtimeConfiguration.staticResult` | Objeto JSON | Para ações que dão suporte e têm a configuração de [resultado estático](../logic-apps/test-logic-apps-mock-data-static-results.md) ativada `staticResult` , o objeto tem estes atributos: <p>- `name`, que faz referência ao nome de definição de resultado estático da ação atual, que `staticResults` aparece dentro do atributo no atributo do `definition` fluxo de trabalho do aplicativo lógico. Para obter mais informações, consulte [resultados estáticos-referência de esquema para linguagem de definição de fluxo de trabalho](../logic-apps/logic-apps-workflow-definition-language.md#static-results). <p> - `staticResultOptions`, que especifica se os resultados estáticos são `Enabled` ou não para a ação atual. <p>Para ativar os resultados estáticos, consulte [testar aplicativos lógicos com dados fictícios Configurando resultados estáticos](../logic-apps/test-logic-apps-mock-data-static-results.md) | Ação: Variado |
||||| 

<a name="operation-options"></a>

## <a name="operation-options"></a>Opções de operação

Você pode alterar o comportamento padrão de acionadores e ações com a propriedade `operationOptions` na definição de acionador ou ação.

| Opção de operação | Tipo | Descrição | Gatilho ou ação | 
|------------------|------|-------------|-------------------| 
| `DisableAsyncPattern` | Cadeia | Execute ações baseadas em HTTP de forma síncrona, em vez de assíncrona. <p><p>Para definir essa opção, consulte [executar ações de forma síncrona](#asynchronous-patterns). | Ações: <p>[ApiConnection](#apiconnection-action), <br>[HTTP](#http-action), <br>[Resposta](#response-action) | 
| `OptimizedForHighThroughput` | Cadeia | Altere o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) no número de execuções de ação por 5 minutos para o [limite máximo](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). <p><p>Para definir essa opção, consulte [Executar no modo de alto rendimento](#run-high-throughput-mode). | Todas as ações | 
| `Sequential` | Cadeia | Execute "for each" loop iterações um por vez, em vez de todos ao mesmo tempo em paralelo. <p>Esta opção funciona da mesma forma que a configuração da `runtimeConfiguration.concurrency.repetitions` propriedade para `1`. Você pode definir a propriedade, mas não ambos. <p><p>Para definir essa opção, consulte [executar "for each" executa um loop em sequência](#sequential-for-each).| Ação: <p>[Foreach](#foreach-action) | 
| `SingleInstance` | Cadeia | Executar o gatilho para cada instância de aplicativo lógico em sequência e aguarde a execução anteriormente ativa concluir antes de disparar a próxima instância do aplicativo lógico. <p><p>Esta opção funciona da mesma forma que a configuração da `runtimeConfiguration.concurrency.runs` propriedade para `1`. Você pode definir a propriedade, mas não ambos. <p>Para definir essa opção, consulte [disparar instâncias sequencialmente](#sequential-trigger). | Todos os gatilhos | 
||||

<a name="change-trigger-concurrency"></a>

### <a name="change-trigger-concurrency"></a>Simultaneidade do gatilho de alteração

Por padrão, as instâncias do aplicativo lógico são executadas ao mesmo tempo, simultaneamente ou em paralelo até o [ limite padrão ](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Portanto, cada instância de gatilho é acionada antes da conclusão da execução da instância do fluxo de trabalho anterior. Esse limite ajuda a controlar o número de solicitações que os sistemas de back-end recebem. 

Para alterar o limite padrão, você pode usar o editor de visualização de código ou o Logic Apps Designer, pois a alteração da configuração de simultaneidade por meio do designer adiciona ou atualiza a propriedade `runtimeConfiguration.concurrency.runs` na definição do acionador subjacente e vice-versa. Essa propriedade controla o número máximo de instâncias de fluxo de trabalho que podem ser executadas em paralelo. 

> [!NOTE] 
> Se você definir o acionador para ser executado sequencialmente usando o designer ou o editor de visualização de código, não defina a propriedade `operationOptions` da propriedade `SingleInstance` no editor de visualização de código. Caso contrário, você pode obter um erro de validação. Para obter mais informações, consulte [disparar instâncias sequencialmente](#sequential-trigger).

#### <a name="edit-in-code-view"></a>Editar no modo de exibição de código 

Na definição de gatilho subjacente, adicione ou atualize a `runtimeConfiguration.concurrency.runs` propriedade para um valor entre `1` e `50`, inclusive.

Aqui está um exemplo que limita as execuções simultâneas a 10 instâncias:

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": 10
      }
   }
}
```

#### <a name="edit-in-logic-apps-designer"></a>Editar no Designer de aplicativos lógicos

1. No canto superior direito do gatilho, escolha o botão reticências (...) e, em seguida, escolha **Configurações**.

2. Em **Controle de Simultaneidade**, defina **Limite** como **Ligado**. 

3. Arraste o controle deslizante **Grau de Paralelismo** para o valor desejado. Para executar seu aplicativo lógico em sequência, arraste o valor do controle deslizante para **1**.

<a name="change-for-each-concurrency"></a>

### <a name="change-for-each-concurrency"></a>Alterar a simultaneidade "for each"

Por padrão, as iterações de loop "para cada" são executadas ao mesmo tempo ou em paralelo, até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite padrão, você pode usar o editor de visualização de código ou o Logic Apps Designer, pois a alteração da configuração de simultaneidade por meio do designer adiciona ou atualiza a propriedade `runtimeConfiguration.concurrency.repetitions` na definição de ação subjacente "para cada" e vice-versa. Essa propriedade controla o número máximo de iterações que podem ser executadas em paralelo.

> [!NOTE] 
> Se você definir a ação "for each" para executar em sequência usando o designer ou editor de exibição de código, não defina a ação `operationOptions`propriedade para`Sequential` no editor de exibição de código. Caso contrário, você pode obter um erro de validação. Para obter mais informações, consulte [executar "" for each executa um loop em sequência](#sequential-for-each).

#### <a name="edit-in-code-view"></a>Editar no modo de exibição de código 

Na definição subjacente "para cada", adicione ou atualize a `runtimeConfiguration.concurrency.repetitions`propriedade para um valor entre`1` e `50`, inclusive. 

Aqui está um exemplo que limita as execuções simultâneas a 10 iterações:

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": 10
      }
   }
}
```

#### <a name="edit-in-logic-apps-designer"></a>Editar no Designer de aplicativos lógicos

1. Na ação **Para cada**, no canto superior direito, escolha o botão reticências (...) e, em seguida, escolha **Configurações**.

2. Em **Controle de Simultaneidade**, defina **Controle de Simultaneidade** como **Ligado**. 

3. Arraste o controle deslizante **Grau de Paralelismo** para o valor desejado. Para executar seu aplicativo lógico em sequência, arraste o valor do controle deslizante para **1**.

<a name="change-waiting-runs"></a>

### <a name="change-waiting-runs-limit"></a>Aguardando o limite de execuções de alteração

Por padrão, todas as instâncias de fluxo de trabalho do aplicativo lógico são executadas ao mesmo tempo, simultaneamente ou em paralelo até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Cada instância de gatilho é acionada antes da conclusão da execução da instância de fluxo de trabalho ativa anteriormente. Embora você possa [alterar esse limite padrão](#change-trigger-concurrency), quando o número de instâncias de fluxo de trabalho atinge o novo limite de simultaneidade, todas as novas instâncias devem aguardar para serem executadas. 

O número de execuções que podem esperar também tem um [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits), que você pode alterar. No entanto, depois que seu aplicativo lógico atinge o limite de espera de execuções, o mecanismo dos aplicativos lógicos não aceita mais novas execuções. Gatilhos de webhook e a solicitação retornam 429 erros e gatilhos recorrentes iniciar ignorar tentativas de sondagem.

Para alterar o limite padrão em execuções de espera, na definição de gatilho subjacente, adicione a propriedade `runtimeConfiguration.concurency.maximumWaitingRuns` com um valor entre `0` e `100`. 

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "maximumWaitingRuns": 50
      }
   }
}
```

<a name="sequential-trigger"></a>

### <a name="trigger-instances-sequentially"></a>Disparar as instâncias em sequência

Para executar cada instância de fluxo de trabalho do aplicativo lógico somente após a conclusão da execução da instância anterior, defina o gatilho para ser executado em sequência. Você pode usar o editor de visualização de código ou o Logic Apps Designer, pois a alteração da configuração de simultaneidade por meio do designer também adiciona ou atualiza a propriedade `runtimeConfiguration.concurrency.runs` na definição do acionador subjacente e vice-versa. 

> [!NOTE] 
> Quando você configura um acionador para ser executado sequencialmente usando o designer ou o editor de visualização de código, não defina  a `operationOptions` propriedade do acionador como `Sequential`no editor de visualização de código. Caso contrário, você pode obter um erro de validação. 

#### <a name="edit-in-code-view"></a>Editar no modo de exibição de código

Na definição do gatilho, defina a essas propriedades, mas não ambos. 

Defina as `runtimeConfiguration.concurrency.runs` propriedade para `1`:

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": 1
      }
   }
}
```

*- ou -*

Para fazer isso, defina a `operationOptions`propriedade para `SingleInstance`:

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "operationOptions": "SingleInstance"
}
```

#### <a name="edit-in-logic-apps-designer"></a>Editar no Designer de aplicativos lógicos

1. No canto superior direito do gatilho, escolha o botão reticências (...) e, em seguida, escolha **Configurações**.

2. Em **Controle de Simultaneidade**, defina **Limite** como **Ligado**. 

3. Arraste o **grau de paralelismo** controle deslizante para o número `1`. 

<a name="sequential-for-each"></a>

### <a name="run-for-each-loops-sequentially"></a>Execute "for each" executa um loop em sequência

Para executar uma iteração de loop "para cada" somente após a execução da iteração anterior, defina a ação "para cada" para ser executada sequencialmente. Você pode usar o editor de visualização de código ou o Logic Apps Designer, pois a alteração da simultaneidade da ação por meio do designer também adiciona ou atualiza a propriedade `runtimeConfiguration.concurrency.repetitions` na definição de ação subjacente e vice-versa. 

> [!NOTE] 
> Quando você define uma ação "para cada" para ser executada sequencialmente usando o designer ou o editor de visualização de código, não defina a `operationOptions` propriedade da ação como `Sequential` no editor de visualização de código. Caso contrário, você pode obter um erro de validação. 

#### <a name="edit-in-code-view"></a>Editar no modo de exibição de código

Na definição de ação, defina uma dessas propriedades, mas não ambas. 

Defina as `runtimeConfiguration.concurrency.repetitions` propriedade para `1`:

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": 1
      }
   }
}
```

*- ou -*

Para fazer isso, defina a `operationOptions`propriedade para `Sequential`:

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "operationOptions": "Sequential"
}
```

#### <a name="edit-in-logic-apps-designer"></a>Editar no Designer de aplicativos lógicos

1. No **para cada** canto do superior direito da ação, escolha o botão de reticências (...) e, em seguida, escolha **configurações**.

2. Em **Controle de Simultaneidade**, defina **Controle de Simultaneidade** como **Ligado**. 

3. Arraste o **grau de paralelismo** controle deslizante para o número `1`. 

<a name="asynchronous-patterns"></a>

### <a name="run-actions-synchronously"></a>Executar ações de forma síncrona

Por padrão, todas as ações baseadas em HTTP seguem o padrão de operação assíncrona padrão. Esse padrão especifica que, quando uma ação baseada em HTTP envia uma solicitação para o terminal especificado, o servidor remoto retorna uma resposta "202 ACCEPTED". Essa resposta significa que o servidor aceitou a solicitação para processamento. O mecanismo do Logic Apps continua verificando a URL especificada pelo cabeçalho de localização da resposta até que o processamento seja interrompido, o que é qualquer resposta não-202.

No entanto, as solicitações têm um limite de tempo limite, portanto, para ações de longa execução, você pode desativar o comportamento assíncrono incluindo e definindo a `operationOptions` propriedade como `DisableAsyncPattern` nas entradas da ação.
  
```json
"<some-long-running-action>": {
   "type": "Http",
   "inputs": { "<action-inputs>" },
   "operationOptions": "DisableAsyncPattern",
   "runAfter": {}
}
```

<a name="run-high-throughput-mode"></a>

### <a name="run-in-high-throughput-mode"></a>Executar no modo de alta taxa de transferência

Para uma definição de aplicativo lógico único, o número de ações executadas a cada 5 minutos tem um [limite padrão](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Para aumentar esse limite para o [máxima](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) possível, defina o `operationOptions` propriedade `OptimizedForHighThroughput`. Essa configuração coloca seu aplicativo lógico no modo "alto rendimento". 

> [!NOTE]
> Modo de alta taxa de transferência está em visualização. Você também pode distribuir uma carga de trabalho entre mais de um aplicativo lógico conforme necessário.

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": { "<action-inputs>" },
   "operationOptions": "OptimizedForHighThroughput",
   "runAfter": {}
}
```

<a name="connector-authentication"></a>

## <a name="authenticate-http-triggers-and-actions"></a>Autenticar gatilhos e ações HTTP

Pontos de extremidade HTTP são compatíveis com diferentes tipos de autenticação. Você pode configurar a autenticação para essas ações e gatilhos de HTTP:

* [HTTP](../connectors/connectors-native-http.md)
* [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)
* [Webhook HTTP](../connectors/connectors-native-webhook.md)

Aqui estão os tipos de autenticação que você pode configurar:

* [Autenticação básica](#basic-authentication)
* [Autenticação de certificado de cliente](#client-certificate-authentication)
* [Autenticação OAuth do Azure AD (Azure Active Directory)](#azure-active-directory-oauth-authentication)

> [!IMPORTANT]
> Proteja informações confidenciais manipuladas pela definição de fluxo de trabalho do aplicativo lógico. Use parâmetros seguros e codifique os dados, conforme necessário. Para saber mais sobre como usar e proteger parâmetros, confira [Secure your logic app](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters) (Proteger o aplicativo lógico).

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>Autenticação básica

Para [autenticação básica](../active-directory-b2c/active-directory-b2c-custom-rest-api-netfw-secure-basic.md) usando o Azure Active Directory, sua definição de gatilho ou de ação pode incluir um objeto JSON `authentication`, que tem as propriedades especificadas pela tabela a seguir. Para acessar os valores do parâmetro em tempo de execução, é possível usar a expressão `@parameters('parameterName')`, fornecida pela [Linguagem de Definição de Fluxo de Trabalho](https://aka.ms/logicappsdocs). 

| Propriedade | Necessário | Value | Descrição | 
|----------|----------|-------|-------------| 
| **type** | Sim | “Basic” | O tipo de autenticação a ser usado, que é “Basic” aqui | 
| **username** | Sim | "@parameters('userNameParam')" | O nome de usuário para autenticar o acesso ao ponto de extremidade de serviço de destino |
| **password** | Sim | "@parameters('passwordParam')" | A senha para autenticar o acesso ao ponto de extremidade de serviço de destino |
||||| 

Neste exemplo de definição de ação HTTP, a seção `authentication` especifica a autenticação `Basic`. Para saber mais sobre como usar e proteger parâmetros, confira [Secure your logic app](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters) (Proteger o aplicativo lógico).

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://www.microsoft.com",
      "authentication": {
         "type": "Basic",
         "username": "@parameters('userNameParam')",
         "password": "@parameters('passwordParam')"
      }
  },
  "runAfter": {}
}
```

> [!IMPORTANT]
> Proteja informações confidenciais manipuladas pela definição de fluxo de trabalho do aplicativo lógico. Use parâmetros seguros e codifique os dados, conforme necessário. Para saber mais sobre proteger parâmetros, confira [Secure your logic app](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters) (Proteger o aplicativo lógico).

<a name="client-certificate-authentication"></a>

### <a name="client-certificate-authentication"></a>Autenticação de certificado do cliente

Para [autenticação baseada em certificado](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) usando o Azure Active Directory, sua definição de gatilho ou de ação pode incluir um objeto JSON `authentication`, que tem as propriedades especificadas pela tabela a seguir. Para acessar os valores do parâmetro em tempo de execução, é possível usar a expressão `@parameters('parameterName')`, fornecida pela [Linguagem de Definição de Fluxo de Trabalho](https://aka.ms/logicappsdocs). Para limites no número de certificados do cliente que você pode usar, confira [Limits and configuration for Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md) (Limites e configuração para Aplicativos Lógicos do Azure).

| Propriedade | Necessário | Value | Descrição |
|----------|----------|-------|-------------|
| **type** | Sim | "ClientCertificate" | O tipo de autenticação a ser usado para certificados do cliente do protocolo SSL. Embora haja suporte para certificados autoassinados, não há suporte para certificados autoassinados para SSL. |
| **pfx** | Sim | "@parameters('pfxParam') | O conteúdo codificado na base64 do arquivo PFX (Troca de Informações Pessoais) |
| **password** | Sim | "@parameters('passwordParam')" | A senha para acessar o arquivo PFX |
||||| 

Neste exemplo de definição de ação HTTP, a seção `authentication` especifica a autenticação `ClientCertificate`. Para saber mais sobre como usar e proteger parâmetros, confira [Secure your logic app](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters) (Proteger o aplicativo lógico).

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://www.microsoft.com",
      "authentication": {
         "type": "ClientCertificate",
         "pfx": "@parameters('pfxParam')",
         "password": "@parameters('passwordParam')"
      }
   },
   "runAfter": {}
}
```

> [!IMPORTANT]
> Proteja informações confidenciais manipuladas pela definição de fluxo de trabalho do aplicativo lógico. Use parâmetros seguros e codifique os dados, conforme necessário. Para saber mais sobre proteger parâmetros, confira [Secure your logic app](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters) (Proteger o aplicativo lógico).

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-ad-oauth-authentication"></a>Autenticação OAuth do Azure AD (Active Directory)

Para [autenticação OAuth do Azure AD](../active-directory/develop/authentication-scenarios.md), sua definição de gatilho ou de ação pode incluir um objeto JSON `authentication`, que tem as propriedades especificadas pela tabela a seguir. Para acessar os valores do parâmetro em tempo de execução, é possível usar a expressão `@parameters('parameterName')`, fornecida pela [Linguagem de Definição de Fluxo de Trabalho](https://aka.ms/logicappsdocs).

| Propriedade | Necessário | Value | Descrição |
|----------|----------|-------|-------------|
| **type** | Sim | `ActiveDirectoryOAuth` | O tipo de autenticação a ser usado, o que é "ActiveDirectoryOAuth" para o OAuth do Azure AD |
| **authority** | Não | <*URL-for-authority-token-issuer*> | A URL para a autoridade que fornece o token de autenticação |
| **tenant** | Sim | <*tenant-ID*> | A ID do locatário para o locatário do Azure AD |
| **audience** | Sim | <*resource-to-authorize*> | O recurso que você deseja usar para autorização, por exemplo, `https://management.core.windows.net/` |
| **clientId** | Sim | <*client-ID*> | A ID do cliente para o aplicativo solicitando a autorização |
| **credentialType** | Sim | "Certificado" ou "Segredo" | O tipo de credencial que o cliente usa para solicitar a autorização. Essa propriedade e o valor não aparecem em sua definição subjacente, mas determinam os parâmetros necessários para o tipo de credencial. |
| **pfx** | Sim, somente para o tipo de credencial “Certificate” | "@parameters('pfxParam') | O conteúdo codificado na base64 do arquivo PFX (Troca de Informações Pessoais) |
| **password** | Sim, somente para o tipo de credencial “Certificate” | "@parameters('passwordParam')" | A senha para acessar o arquivo PFX |
| **secret** | Sim, somente para o tipo de credencial “Secret” | "@parameters('secretParam')" | O segredo do cliente para solicitar autorização |
|||||

Neste exemplo de definição de ação HTTP, a seção `authentication` especifica a autenticação `ActiveDirectoryOAuth` e o tipo de credencial do “Segredo”. Para saber mais sobre como usar e proteger parâmetros, confira [Secure your logic app](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters) (Proteger o aplicativo lógico).

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://www.microsoft.com",
      "authentication": {
         "type": "ActiveDirectoryOAuth",
         "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47",
         "audience": "https://management.core.windows.net/",
         "clientId": "34750e0b-72d1-4e4f-bbbe-664f6d04d411",
         "secret": "@parameters('secretParam')"
     }
   },
   "runAfter": {}
}
```

> [!IMPORTANT]
> Proteja informações confidenciais manipuladas pela definição de fluxo de trabalho do aplicativo lógico. Use parâmetros seguros e codifique os dados, conforme necessário. Para saber mais sobre proteger parâmetros, confira [Secure your logic app](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters) (Proteger o aplicativo lógico).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [Linguagem de Definição de Fluxo de Dados](../logic-apps/logic-apps-workflow-definition-language.md)
