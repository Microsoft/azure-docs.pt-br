---
title: Referência de esquema para tipos de ação e gatilho
description: Guia de referência de esquema para tipos de ação e gatilho de linguagem de definição de fluxo de trabalho em aplicativos lógicos
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: devx-track-js
ms.openlocfilehash: ea4a4a47e91e88c00ca8a4e886d0372a24482907
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98784301"
---
# <a name="schema-reference-guide-for-trigger-and-action-types-in-azure-logic-apps"></a>Guia de referência de esquema para tipos de ação e gatilho em aplicativos lógicos do Azure

Esta referência descreve os tipos gerais usados para identificar gatilhos e ações na definição de fluxo de trabalho subjacente do aplicativo lógico, que é descrita e validada pela [linguagem de definição de fluxo de trabalho](../logic-apps/logic-apps-workflow-definition-language.md). Para localizar gatilhos e ações de conectores específicos que você pode usar em seus aplicativos lógicos, consulte a lista na [visão geral dos conectores](/connectors/).

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

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*nome do gatilho*> | String | O nome do gatilho | 
| <*tipo de gatilho*> | String | O tipo de gatilho como "Http" ou "ApiConnection" | 
| <*gatilho-entradas*> | Objeto JSON | As entradas que definem o comportamento do gatilho | 
| <*unidade de tempo*> | String | A unidade de tempo que descreve com que frequência o acionador dispara: "Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês" | 
| <*número de unidades de tempo*> | Integer | Um valor que especifica com que frequência o acionador é disparado com base na frequência, que é o número de unidades de tempo a aguardar até que o acionador seja acionado novamente <p>Aqui estão os intervalos mínimos e máximos: <p>– Mês: 1 a 16 meses </br>–Dia: 1 a 500 dias </br>– Hora: 1 a 12.000 horas </br>– Minuto: 1 a 72.000 minutos </br>– Segundo: 1 a 9.999.999 segundos<p>Por exemplo, se o intervalo for 6, e a frequência for "Mês", a recorrência será a cada 6 meses. | 
|||| 

*Opcional*

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*matriz com condições*> | Array | Uma matriz que contém uma ou mais [condições](#trigger-conditions) que determinam se o fluxo de trabalho deve ser executado. Disponível apenas para gatilhos. | 
| <*tempo de execução-config-Options*> | Objeto JSON | Você pode alterar o comportamento do runtime do acionador configurando as propriedades `runtimeConfiguration`. Para obter mais informações, consulte [Configurações de runtime](#runtime-config-options). | 
| <*dividir-expressão*> | String | Para gatilhos que retornam uma matriz, você pode especificar uma expressão que [divide ou *debita*](#split-on-debatch)itens da matriz em várias instâncias de fluxo de trabalho para processamento. | 
| <*operação-opção*> | String | Você pode alterar o comportamento padrão definindo a `operationOptions` propriedade. Para obter mais informações, consulte [opções de operação](#operation-options). | 
|||| 

## <a name="trigger-types-list"></a>Lista de tipos de gatilho

Cada tipo de gatilho possui uma interface e entradas diferentes que definem o comportamento do gatilho. 

### <a name="built-in-triggers"></a>Gatilhos internos

| Tipo de gatilho | Descrição | 
|--------------|-------------| 
| [**HTTP**](#http-trigger) | Verifica ou *pesquisas* qualquer ponto de extremidade. Esse ponto de extremidade deve estar em conformidade com um contrato de gatilho específico usando um `202` padrão assíncrono ou retornando uma matriz. | 
| [**HTTPWebhook**](#http-webhook-trigger) | Cria um ponto de extremidade que pode ser chamado para seu aplicativo lógico, mas chama o URL especificado para registrar ou cancelar o registro. |
| [**Recorrência**](#recurrence-trigger) | Acionado com base em um agendamento definido. Você pode definir uma data e hora futura para acionar esse gatilho. Com base na frequência, você também pode especificar horários e dias para executar seu fluxo de trabalho. | 
| [**Solicitação**](#request-trigger)  | Cria um ponto de extremidade que pode ser chamado para seu aplicativo lógico e também é conhecido como um acionador "manual". Por exemplo, consulte [Chamar, disparar ou aninhar fluxos de trabalho com pontos de extremidade HTTP](../logic-apps/logic-apps-http-endpoint.md). | 
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

| Valor | Type | Descrição |
|-------|------|-------------|
| <*APIConnection_trigger_name*> | String | O nome do gatilho |
| <*nome da conexão*> | String | O nome da conexão com a API gerenciada usada pelo fluxo de trabalho |
| <*tipo de método*> | String | O método HTTP para se comunicar com a API gerenciada: "GET", "PUT", "POST", "PATCH", "Excluir" |
| <*operação de API*> | String | A operação de API para chamar |
| <*unidade de tempo*> | String | A unidade de tempo que descreve com que frequência o acionador dispara: "Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês" |
| <*número de unidades de tempo*> | Integer | Um valor que especifica com que frequência o acionador é disparado com base na frequência, que é o número de unidades de tempo a aguardar até que o acionador seja acionado novamente <p>Aqui estão os intervalos mínimos e máximos: <p>– Mês: 1 a 16 meses </br>–Dia: 1 a 500 dias </br>– Hora: 1 a 12.000 horas </br>– Minuto: 1 a 72.000 minutos </br>– Segundo: 1 a 9.999.999 segundos<p>Por exemplo, se o intervalo for 6, e a frequência for "Mês", a recorrência será a cada 6 meses. |
||||

*Opcional*

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*comportamento de repetição*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de status 408, 429 e 5XX e todas as exceções de conectividade. Para mais informações, consulte [Políticas de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*parâmetros de consulta*> | Objeto JSON | Quaisquer parâmetros de consulta para incluir na chamada da API. Por exemplo, o `"queries": { "api-version": "2018-01-01" }` objeto adiciona `?api-version=2018-01-01` para a chamada. | 
| <*máx. de execuções*> | Integer | Por padrão, as instâncias de fluxo de trabalho são executadas ao mesmo tempo (simultaneamente ou em paralelo) até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar esse limite, definindo um novo <*contagem*> de valor, consulte [simultaneidade do gatilho de alteração](#change-trigger-concurrency). | 
| <*Max-execuções-fila*> | Integer | Quando o fluxo de trabalho já estiver executando o número máximo de instâncias, que podem ser alteradas com base na `runtimeConfiguration.concurrency.runs` propriedade, todas as novas execuções serão colocadas nessa fila até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite padrão, consulte [execuções de espera da alteração limitam](#change-waiting-runs). | 
| <*dividir-expressão*> | String | Para gatilhos que retornam matrizes, essa expressão referencia a matriz a ser usada para que você possa criar e executar uma instância de fluxo de trabalho para cada item de matriz, em vez de usar um loop "para cada". <p>Por exemplo, essa expressão representa um item na matriz retornada dentro do conteúdo do corpo do acionador: `@triggerbody()?['value']` |
| <*operação-opção*> | String | Você pode alterar o comportamento padrão definindo a `operationOptions` propriedade. Para obter mais informações, consulte [opções de operação](#operation-options). |
||||

*Saídas*
 
| Elemento | Type | Descrição |
|---------|------|-------------|
| headers | Objeto JSON | Os cabeçalhos da resposta |
| body | Objeto JSON | O corpo da resposta |
| código de status | Integer | O código de status da resposta |
|||| 

*Exemplo*

Essa definição de gatilho verifica o email todos os dias dentro da caixa de entrada para uma conta corporativa ou de estudante:

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

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*nome da conexão*> | String | O nome da conexão com a API gerenciada usada pelo fluxo de trabalho | 
| <*corpo-conteúdo*> | Objeto JSON | Qualquer conteúdo da mensagem para enviar como carga para a API gerenciada | 
||||

*Opcional*

| Valor | Type | Descrição |
|-------|------|-------------|
| <*comportamento de repetição*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de status 408, 429 e 5XX e todas as exceções de conectividade. Para mais informações, consulte [Políticas de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies). |
| <*parâmetros de consulta*> | Objeto JSON | Os parâmetros de consulta para incluir com a chamada à API <p>Por exemplo, o `"queries": { "api-version": "2018-01-01" }` objeto adiciona `?api-version=2018-01-01` para a chamada. |
| <*máx. de execuções*> | Integer | Por padrão, as instâncias de fluxo de trabalho são executadas ao mesmo tempo (simultaneamente ou em paralelo) até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar esse limite, definindo um novo <*contagem*> de valor, consulte [simultaneidade do gatilho de alteração](#change-trigger-concurrency). |
| <*Max-execuções-fila*> | Integer | Quando o fluxo de trabalho já estiver executando o número máximo de instâncias, que podem ser alteradas com base na `runtimeConfiguration.concurrency.runs` propriedade, todas as novas execuções serão colocadas nessa fila até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite padrão, consulte [execuções de espera da alteração limitam](#change-waiting-runs). | 
| <*dividir-expressão*> | String | Para gatilhos que retornam matrizes, essa expressão referencia a matriz a ser usada para que você possa criar e executar uma instância de fluxo de trabalho para cada item de matriz, em vez de usar um loop "para cada". <p>Por exemplo, essa expressão representa um item na matriz retornada dentro do conteúdo do corpo do acionador: `@triggerbody()?['value']` |
| <*operação-opção*> | String | Você pode alterar o comportamento padrão definindo a `operationOptions` propriedade. Para obter mais informações, consulte [opções de operação](#operation-options). | 
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

Esse gatilho envia uma solicitação para o ponto de extremidade HTTP ou HTTPS especificado com base na agenda de recorrência especificada. Em seguida, o gatilho verifica a resposta para determinar se o fluxo de trabalho é executado. Para obter mais informações, consulte [chamar pontos de extremidade de serviço por http ou HTTPS de aplicativos lógicos do Azure](../connectors/connectors-native-http.md).

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<HTTP-or-HTTPS-endpoint-URL>",
      "headers": { "<header-content>" },
      "queries": "<query-parameters>",
      "body": "<body-content>",
      "authentication": { "<authentication-type-and-property-values>" },
      "retryPolicy": {
         "type": "<retry-behavior>"
      }
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

| Propriedade | Valor | Type | Descrição |
|----------|-------|------|-------------|
| `method` | <*tipo de método*> | String | O método a ser usado para enviar a solicitação de saída: "GET", "PUT", "POST", "PATCH" ou "DELETE" |
| `uri` | <*HTTP-ou-HTTPS-Endpoint-URL*> | String | A URL de ponto de extremidade HTTP ou HTTPS para a qual você deseja enviar a solicitação de saída. Tamanho máximo da cadeia de caracteres: 2 KB <p>Para um serviço ou recurso do Azure, essa sintaxe de URI inclui a ID de recurso e o caminho para o recurso que você deseja acessar. |
| `frequency` | <*unidade de tempo*> | String | A unidade de tempo que descreve com que frequência o acionador dispara: "Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês" |
| `interval` | <*número de unidades de tempo*> | Integer | Um valor que especifica com que frequência o acionador é disparado com base na frequência, que é o número de unidades de tempo a aguardar até que o acionador seja acionado novamente <p>Aqui estão os intervalos mínimos e máximos: <p>– Mês: 1 a 16 meses </br>–Dia: 1 a 500 dias </br>– Hora: 1 a 12.000 horas </br>– Minuto: 1 a 72.000 minutos </br>– Segundo: 1 a 9.999.999 segundos<p>Por exemplo, se o intervalo for 6, e a frequência for "Mês", a recorrência será a cada 6 meses. |
|||||

*Opcional*

| Propriedade | Valor | Type | Descrição |
|----------|-------|------|-------------|
| `headers` | <*cabeçalho-Conteúdo*> | Objeto JSON | Todos os cabeçalhos que você precisa incluir com a solicitação <p>Por exemplo, para definir o idioma e o tipo: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| `queries` | <*parâmetros de consulta*> | Objeto JSON | Todos os parâmetros de consulta que você precisa usar na solicitação <p>Por exemplo, o `"queries": { "api-version": "2018-01-01" }`objeto adiciona`?api-version=2018-01-01` à solicitação. |
| `body` | <*corpo-conteúdo*> | Objeto JSON | O conteúdo da mensagem para enviar como carga de solicitação |
| `authentication` | <*autenticação-tipo-e-propriedade-valores*> | Objeto JSON | O modelo de autenticação que a solicitação usa para autenticar solicitações de saída. Para obter mais informações, confira [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). Além do Agendador, a propriedade `authority` tem suporte. Quando não especificado, o valor padrão é `https://management.azure.com/` , mas você pode usar um valor diferente. |
| `retryPolicy` > `type` | <*comportamento de repetição*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de status 408, 429 e 5XX e todas as exceções de conectividade. Para mais informações, consulte [Políticas de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies). |
| `runs` | <*máx. de execuções*> | Integer | Por padrão, as instâncias de fluxo de trabalho são executadas ao mesmo tempo (simultaneamente ou em paralelo) até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar esse limite, definindo um novo <*contagem*> de valor, consulte [simultaneidade do gatilho de alteração](#change-trigger-concurrency). |
| `maximumWaitingRuns` | <*Max-execuções-fila*> | Integer | Quando o fluxo de trabalho já estiver executando o número máximo de instâncias, que podem ser alteradas com base na `runtimeConfiguration.concurrency.runs` propriedade, todas as novas execuções serão colocadas nessa fila até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite padrão, consulte [execuções de espera da alteração limitam](#change-waiting-runs). |
| `operationOptions` | <*operação-opção*> | String | Você pode alterar o comportamento padrão definindo a `operationOptions` propriedade. Para obter mais informações, consulte [opções de operação](#operation-options). |
|||||

*Saídas*

| Elemento | Type | Descrição |
|---------|------|-------------|
| `headers` | Objeto JSON | Os cabeçalhos da resposta |
| `body` | Objeto JSON | O corpo da resposta |
| `status code` | Integer | O código de status da resposta |
||||

*Requisitos para as solicitações de entrada*

Para funcionar bem com seu aplicativo lógico, o ponto de extremidade deve estar em conformidade com um padrão ou contrato de gatilho específico e reconhecer essas propriedades de resposta:

| Propriedade | Obrigatório | Descrição |
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

Você também pode especificar [limites assíncronos](#asynchronous-limits) em um **HTTPWebhook** gatilho. Comportamento do gatilho depende das seções que você use ou omite.

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<endpoint-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "retryPolicy": { "<retry-behavior>" }
         },
      },
      "unsubscribe": {
         "method": "<method-type>",
         "url": "<endpoint-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" }
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

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*tipo de método*> | String | O método HTTP a ser usado para a solicitação de inscrição: "GET", "PUT", "POST", "PATCH" ou "DELETE" | 
| <*ponto de extremidade-assinatura-URL*> | String | O URL do ponto final para onde enviar o pedido de subscrição | 
|||| 

*Opcional*

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*tipo de método*> | String | O método HTTP a ser usado para a solicitação de cancelamento: "GET", "PUT", "POST", "PATCH" ou "DELETE" | 
| <*ponto de extremidade-cancelar assinatura-URL*> | String | A URL do ponto de extremidade para onde enviar a solicitação de cancelamento | 
| <*corpo-conteúdo*> | String | Qualquer conteúdo de mensagem para enviar na solicitação de assinatura ou cancelamento | 
| <*tipo de autenticação*> | Objeto JSON | O modelo de autenticação que a solicitação usa para autenticar solicitações de saída. Para obter mais informações, confira [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). |
| <*comportamento de repetição*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de status 408, 429 e 5XX e todas as exceções de conectividade. Para mais informações, consulte [Políticas de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*máx. de execuções*> | Integer | Por padrão, todas as instâncias de fluxo de trabalho são executadas ao mesmo tempo (simultaneamente ou em paralelo) até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar esse limite, definindo um novo <*contagem*> de valor, consulte [simultaneidade do gatilho de alteração](#change-trigger-concurrency). | 
| <*Max-execuções-fila*> | Integer | Quando o fluxo de trabalho já estiver executando o número máximo de instâncias, que podem ser alteradas com base na `runtimeConfiguration.concurrency.runs` propriedade, todas as novas execuções serão colocadas nessa fila até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite padrão, consulte [execuções de espera da alteração limitam](#change-waiting-runs). | 
| <*operação-opção*> | String | Você pode alterar o comportamento padrão definindo a `operationOptions` propriedade. Para obter mais informações, consulte [opções de operação](#operation-options). | 
|||| 

*Saídas* 

| Elemento | Type | Descrição |
|---------|------|-------------| 
| headers | Objeto JSON | Os cabeçalhos da resposta | 
| body | Objeto JSON | O corpo da resposta | 
| código de status | Integer | O código de status da resposta | 
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

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*unidade de tempo*> | String | A unidade de tempo que descreve com que frequência o acionador dispara: "Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês" | 
| <*número de unidades de tempo*> | Integer | Um valor que especifica com que frequência o acionador é disparado com base na frequência, que é o número de unidades de tempo a aguardar até que o acionador seja acionado novamente <p>Aqui estão os intervalos mínimos e máximos: <p>– Mês: 1 a 16 meses </br>–Dia: 1 a 500 dias </br>– Hora: 1 a 12.000 horas </br>– Minuto: 1 a 72.000 minutos </br>– Segundo: 1 a 9.999.999 segundos<p>Por exemplo, se o intervalo for 6, e a frequência for "Mês", a recorrência será a cada 6 meses. | 
|||| 

*Opcional*

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*Data de início-hora-com-formato-AAAA-MM-DDThh: mm: SS*> | String | A data e hora de início neste formato: <p>AAAA-MM-DDThh:mm:ss se você especificar um fuso horário <p>-ou- <p>AAAA-MM-DDThh:mm:ssZ se você não especificar um fuso horário <p>Por exemplo, se você quiser 18 de setembro de 2017 às 14h00, especifique "2017-09-18T14:00:00" e especifique um fuso horário como "Hora Padrão do Pacífico" ou especifique "2017-09-18T14:00:00Z" sem um fuso horário. <p>**Observação:** Essa hora de início tem um máximo de 49 anos no futuro e deve seguir a [especificação de data e hora ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) no [formato de data e hora UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), mas sem um [deslocamento UTC](https://en.wikipedia.org/wiki/UTC_offset). Se você não especificar um fuso horário, será necessário adicionar a letra "Z" no final sem espaços. Essa letra "Z" refere-se ao equivalente em [hora náutica](https://en.wikipedia.org/wiki/Nautical_time). <p>Para agendamentos simples, a hora de início é a primeira ocorrência, enquanto que, para agendamentos complexos, o gatilho não é disparado antes da hora de início. Para obter mais informações sobre datas e horas de início, consulte [Criar e agendar tarefas de execução regularmente](../connectors/connectors-native-recurrence.md). | 
| <*fuso horário*> | String | Aplica-se somente quando você especifica uma hora de início, porque o gatilho não aceita [diferença UTC](https://en.wikipedia.org/wiki/UTC_offset). Especifique o fuso horário que deseja aplicar. | 
| <*uma ou mais marcas de hora*> | Inteiro ou matriz de inteiros | Se você selecionar "Dia" ou "Semana" para `frequency`, poderá selecionar um ou mais números inteiros, de 0 a 23, separados por vírgulas, como as horas do dia nas quais você deseja executar o fluxo de trabalho. <p>Por exemplo, se você especificar "10", "12" e "14", você obterá 10h, 12h e 14h como as marcas de hora. | 
| <*aspas com um ou mais minutos*> | Inteiro ou matriz de inteiros | Se você selecionar "Dia" ou "Semana" para `frequency`, poderá selecionar um ou mais números inteiros, de 0 a 59, separados por vírgulas, como os minutos da hora nos quais você deseja executar o fluxo de trabalho. <p>Por exemplo, você pode especificar "30" como a marca de minutos e, usando o exemplo anterior como as horas do dia, você obtém 10h30, 12h30 e 14h30. | 
| weekDays | Cadeia de caracteres ou matriz de cadeia de caracteres | Se você selecionar "Semana" para `frequency`, poderá selecionar um ou mais dias, separados por vírgulas, nos quais deseja executar o fluxo de trabalho: “segunda-feira”, “terça-feira”, “quarta-feira”, “quinta-feira”, “Sexta-feira”, “sábado” e “domingo” | 
| <*máx. de execuções*> | Integer | Por padrão, todas as instâncias de fluxo de trabalho são executadas ao mesmo tempo (simultaneamente ou em paralelo) até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar esse limite, definindo um novo <*contagem*> de valor, consulte [simultaneidade do gatilho de alteração](#change-trigger-concurrency). | 
| <*Max-execuções-fila*> | Integer | Quando o fluxo de trabalho já estiver executando o número máximo de instâncias, que podem ser alteradas com base na `runtimeConfiguration.concurrency.runs` propriedade, todas as novas execuções serão colocadas nessa fila até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite padrão, consulte [execuções de espera da alteração limitam](#change-waiting-runs). | 
| <*operação-opção*> | String | Você pode alterar o comportamento padrão definindo a `operationOptions` propriedade. Para obter mais informações, consulte [opções de operação](#operation-options). | 
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

Para chamar esse gatilho, você deve usar a `listCallbackUrl`API, descrita na [API REST do Serviço de Fluxo de Trabalho](/rest/api/logic/workflows). Para saber como usar esse gatilho como um ponto de extremidade HTTP, consulte [Chamar, disparar ou aninhar fluxos de trabalho com pontos de extremidade HTTP](../logic-apps/logic-apps-http-endpoint.md).

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

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*nome da propriedade*> | String | O nome de uma propriedade no esquema JSON, que descreve a carga útil | 
| <*tipo de propriedade*> | String | O tipo da propriedade | 
|||| 

*Opcional*

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*tipo de método*> | String | O método que as solicitações recebidas devem usar para chamar seu aplicativo lógico: "GET", "PUT", "POST", "PATCH", "DELETE" |
| <*caminho relativo-para-parâmetro-aceito*> | String | O caminho relativo para o parâmetro que o URL do seu endpoint pode aceitar | 
| <*obrigatório-Propriedades*> | Array | Uma ou mais propriedades que exigem valores | 
| <*máx. de execuções*> | Integer | Por padrão, todas as instâncias de fluxo de trabalho são executadas ao mesmo tempo (simultaneamente ou em paralelo) até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar esse limite, definindo um novo <*contagem*> de valor, consulte [simultaneidade do gatilho de alteração](#change-trigger-concurrency). | 
| <*Max-execuções-fila*> | Integer | Quando o fluxo de trabalho já estiver executando o número máximo de instâncias, que podem ser alteradas com base na `runtimeConfiguration.concurrency.runs` propriedade, todas as novas execuções serão colocadas nessa fila até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar o limite padrão, consulte [execuções de espera da alteração limitam](#change-waiting-runs). | 
| <*operação-opção*> | String | Você pode alterar o comportamento padrão definindo a `operationOptions` propriedade. Para obter mais informações, consulte [opções de operação](#operation-options). | 
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

Se o gatilho retornar uma matriz para o aplicativo lógico processar, às vezes, um loop "for each" poderá levar muito tempo para processar cada item da matriz. Em vez disso, você pode usar a propriedade **SplitOn** em seu gatilho para *retirar em lote* a matriz. O desenvio em lote divide os itens de matriz e inicia uma nova instância de fluxo de trabalho que é executada para cada item de matriz. Essa abordagem é útil, por exemplo, quando você deseja sondar um ponto de extremidade que pode retornar vários itens novos entre os intervalos de sondagem. Para o número máximo de itens de matriz que **SplitOn** pode processar em uma execução única do aplicativo lógico, consulte [Limites e configuração](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). 

> [!NOTE]
> Você não pode usar **SplitOn** com um padrão de resposta síncrona. Qualquer fluxo de trabalho que usa **SplitOn** e inclui uma ação de resposta é executado de forma assíncrona e envia imediatamente uma resposta `202 ACCEPTED`.
>
> Quando a simultaneidade do gatilho está habilitada, o [limite de divisão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) é reduzido significativamente. Se o número de itens exceder esse limite, o recurso de divisão será desabilitado.
 
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

## <a name="actions-overview"></a>Visão geral das ações

Os Aplicativos Lógicos do Azure fornecem vários tipos de ação - cada um com entradas diferentes que definem o comportamento exclusivo de uma ação. As ações têm esses elementos de alto nível, embora alguns sejam opcionais:

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

| Valor | Type | Descrição | 
|-------|------|-------------|
| <*nome da ação*> | String | O nome da ação | 
| <*tipo de ação*> | String | O tipo de ação, por exemplo, "Http" ou "ApiConnection"| 
| <*nome de entrada*> | String | O nome de uma entrada que define o comportamento da ação | 
| <*entrada-valor*> | Vários | O valor de entrada, que pode ser uma cadeia de caracteres, inteiro, objeto JSON e assim por diante | 
| <*estado anterior-gatilho-ou-Action-status*> | Objeto JSON | O nome e o status resultante para o gatilho ou ação que deve ser executado imediatamente antes de executar esta ação atual | 
|||| 

*Opcional*

| Valor | Type | Descrição | 
|-------|------|-------------|
| <*comportamento de repetição*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de status 408, 429 e 5XX e todas as exceções de conectividade. Para saber mais, confira Políticas de repetição. | 
| <*tempo de execução-config-Options*> | Objeto JSON | Para algumas ações, você pode alterar o comportamento da ação em tempo de execução, definindo `runtimeConfiguration` propriedades. Para obter mais informações, consulte [Configurações de runtime](#runtime-config-options). | 
| <*operação-opção*> | String | Para algumas ações, você pode alterar o comportamento padrão definindo a `operationOptions` propriedade. Para obter mais informações, consulte [opções de operação](#operation-options). | 
|||| 

## <a name="action-types-list"></a>Lista de tipos de ação

Aqui estão alguns tipos de ação comumente usadas: 

* [Tipos de ação integrada](#built-in-actions) como esses exemplos e muito mais: 

  * [**HTTP**](#http-action) para chamar pontos de extremidade via HTTP ou HTTPS

  * [**Resposta**](#response-action) para responder às solicitações

  * [**Executar código JavaScript**](#run-javascript-code) para executar trechos de código JavaScript

  * [**Função**](#function-action) para chamar funções do Azure

  * Ações de operação de dados, como [**ingressar**](#join-action), [**Compose**](#compose-action), [**tabela**](#table-action), [**Selecionar**](#select-action)e outras pessoas que criam ou transformar dados de várias entradas

  * [**Fluxo de trabalho**](#workflow-action) para chamar outro fluxo de aplicativo lógico

* [Tipos de ação de API gerenciados](#managed-api-actions), como [**ApiConnection**](#apiconnection-action) e [**ApiConnectionWebHook**](#apiconnectionwebhook-action) que chame vários conectores e APIs gerenciadas pela Microsoft, por exemplo, o barramento de serviço do Azure, Office 365 Outlook, Power BI, o armazenamento de BLOBs do Azure, OneDrive, GitHub e muito mais

* [Tipos de ação de fluxo de trabalho de controle](#control-workflow-actions), como [**se**](#if-action), [**Foreach**](#foreach-action), [**Switch**](#switch-action), [**Escopo**](#scope-action), e [**até**](#until-action), que contêm outras ações e ajudá-lo organizar a execução de fluxo de trabalho

<a name="built-in-actions"></a>

### <a name="built-in-actions"></a>Ações internas

| Tipo de ação | Descrição | 
|-------------|-------------| 
| [**Redigir**](#compose-action) | Cria uma única saída de entradas, que podem ter vários tipos. | 
| [**Executar código JavaScript**](#run-javascript-code) | Executar trechos de código JavaScript que se enquadram dentro de critérios específicos. Para obter os requisitos de código e mais informações, consulte [Adicionar e executar trechos de código com código embutido](../logic-apps/logic-apps-add-run-inline-code.md). |
| [**Função**](#function-action) | Chama uma função do Azure. | 
| [**HTTP**](#http-action) | Chama um ponto de extremidade HTTP. | 
| [**Em**](#join-action) | Cria uma string de todos os itens em uma matriz e separa esses itens com um caractere delimitador especificado. | 
| [**Analisar JSON**](#parse-json-action) | Cria conteúdo fácil de usar tokens de propriedades no JSON. Você pode fazer referência a essas propriedades, incluindo os tokens em seu aplicativo lógico. | 
| [**Consultá**](#query-action) | Cria uma matriz de itens em outra matriz com base em uma condição ou filtro. | 
| [**Resposta**](#response-action) | Cria uma resposta a uma chamada de entrada ou a solicitação. | 
| [**Não**](#select-action) | Cria uma matriz com objetos JSON, transformando itens de outra matriz com base no mapa especificado. | 
| [**Tabela**](#table-action) | Cria uma tabela CSV ou HTML de uma matriz. | 
| [**Encerrar**](#terminate-action) | Interrompe um fluxo de trabalho ativo. | 
| [**Aguardar**](#wait-action) | Pausa seu fluxo de trabalho por um período especificado ou até a data e a hora especificadas. | 
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
| [**Que**](#if-action) | Execute ações com base em se a condição especificada é verdadeira ou falsa. | 
| [**Com**](#scope-action) | Execute ações com base no status do grupo de um conjunto de ações. | 
| [**Comutador**](#switch-action) | Execute ações organizadas em casos em que valores de expressões, objetos ou tokens correspondam aos valores especificados por cada caso. | 
| [**Haja**](#until-action) | Execute ações em um loop até que a condição especificada seja verdadeira. | 
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

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*nome da ação*> | String | O nome da ação fornecida pelo conector | 
| <*nome da API*> | String | O nome da API gerenciada pela Microsoft usada para a conexão | 
| <*tipo de método*> | String | O método HTTP para chamar a API: "GET", "PUT", "POST", "PATCH" ou "DELETE" | 
| <*operação de API*> | String | A operação de API para chamar | 
|||| 

*Opcional*

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*outra ação específica-de-entrada – Propriedades*> | Objeto JSON | Quaisquer outras propriedades de entrada que se aplicam a essa ação específica | 
| <*comportamento de repetição*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de status 408, 429 e 5XX e todas as exceções de conectividade. Para mais informações, consulte [Políticas de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*parâmetros de consulta*> | Objeto JSON | Quaisquer parâmetros de consulta para incluir na chamada da API. <p>Por exemplo, o `"queries": { "api-version": "2018-01-01" }` objeto adiciona `?api-version=2018-01-01` para a chamada. | 
| <*outras propriedades de ação específica*> | Objeto JSON | Outras propriedades que se aplicam a essa ação específica | 
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
         "authentication": { "<authentication-type>" },
         "retryPolicy": "<retry-behavior>",
         "queries": { "<query-parameters>" },
         "<other-action-specific-input-properties>"
      },
      "unsubscribe": {
         "method": "<method-type>",
         "uri": "<api-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "<other-action-specific-properties>"
      },
   },
   "runAfter": {}
}
```

Alguns valores, como <*tipo de método*>, estão disponíveis para ambos os `"subscribe"` e `"unsubscribe"` objetos.

*Necessária*

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*nome da ação*> | String | O nome da ação fornecida pelo conector | 
| <*tipo de método*> | String | O método HTTP a ser usado para assinar ou cancelar a assinatura de um terminal: "GET", "PUT", "POST", "PATCH" ou "DELETE" | 
| <*API-Subscribe-URL*> | String | O URI a ser usado para assinar a API | 
|||| 

*Opcional*

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*API-cancelar assinatura-URL*> | String | O URI a ser usado para cancelar a inscrição da API | 
| <*cabeçalho-Conteúdo*> | Objeto JSON | Quaisquer cabeçalhos para enviar a solicitação <p>Por exemplo, para definir o idioma e o tipo em uma solicitação: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*corpo-conteúdo*> | Objeto JSON | Qualquer conteúdo da mensagem para enviar a solicitação | 
| <*tipo de autenticação*> | Objeto JSON | O modelo de autenticação que a solicitação usa para autenticar solicitações de saída. Para obter mais informações, confira [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). |
| <*comportamento de repetição*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de status 408, 429 e 5XX e todas as exceções de conectividade. Para mais informações, consulte [Políticas de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*parâmetros de consulta*> | Objeto JSON | Os parâmetros de consulta para incluir com a chamada à API <p>Por exemplo, o `"queries": { "api-version": "2018-01-01" }` objeto adiciona `?api-version=2018-01-01` para a chamada. | 
| <*outra ação específica-de-entrada – Propriedades*> | Objeto JSON | Quaisquer outras propriedades de entrada que se aplicam a essa ação específica | 
| <*outras propriedades de ação específica*> | Objeto JSON | Outras propriedades que se aplicam a essa ação específica | 
|||| 

Você também pode especificar limites em um **ApiConnectionWebhook** ação da mesma forma como [limites assíncronos do HTTP](#asynchronous-limits).

<a name="compose-action"></a>

### <a name="compose-action"></a>Ação para compor

Esta ação cria uma única saída de várias entradas, incluindo expressões. Tanto a saída quanto as entradas podem ter qualquer tipo compatível com os Aplicativos Lógicos do Azure, como matrizes, objetos JSON, XML e binário. Em seguida, você pode usar a saída da ação em outras ações. 

```json
"Compose": {
   "type": "Compose",
   "inputs": "<inputs-to-compose>",
   "runAfter": {}
},
```

*Necessária* 

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*entradas para compor*> | Qualquer | As entradas para a criação de uma única saída | 
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

Essa ação executa um trecho de código JavaScript e retorna os resultados por meio de um `Result` token que as ações posteriores podem referenciar.

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

| Valor | Type | Descrição |
|-------|------|-------------|
| <*Trecho de código JavaScript*> | Varia | O código JavaScript que você deseja executar. Para obter os requisitos de código e mais informações, consulte [Adicionar e executar trechos de código com código embutido](../logic-apps/logic-apps-add-run-inline-code.md). <p>No `code` atributo, seu trecho de código pode usar o objeto somente leitura `workflowContext` como entrada. Esse objeto tem subpropriedades que dão ao seu código acesso aos resultados do gatilho e ações anteriores em seu fluxo de trabalho. Para obter mais informações sobre o `workflowContext` objeto, consulte [gatilho de referência e resultados de ação em seu código](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext). |
||||

*Necessário em alguns casos*

O `explicitDependencies` atributo especifica que você deseja incluir explicitamente os resultados do gatilho, ações anteriores ou ambas como dependências para o trecho de código. Para obter mais informações sobre como adicionar essas dependências, consulte [adicionar parâmetros para código embutido](../logic-apps/logic-apps-add-run-inline-code.md#add-parameters). 

Para o `includeTrigger` atributo, você pode especificar `true` ou `false` valores.

| Valor | Type | Descrição |
|-------|------|-------------|
| <*ações anteriores*> | Matriz de cadeia de caracteres | Uma matriz com os nomes de ação especificados. Use os nomes de ação que aparecem na definição de fluxo de trabalho em que os nomes de ação usam sublinhados (_), não espaços (""). |
||||

*Exemplo 1*

Esta ação executa o código que obtém o nome do aplicativo lógico e retorna o texto "Olá \<logic-app-name> , mundo" de "como resultado. Neste exemplo, o código faz referência ao nome do fluxo de trabalho acessando a `workflowContext.workflow.name` Propriedade por meio do objeto somente leitura `workflowContext` . Para obter mais informações sobre como usar o `workflowContext` objeto, consulte [gatilho de referência e resultados de ação em seu código](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext).

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

Essa ação executa o código em um aplicativo lógico que dispara quando um novo email chega em uma conta corporativa ou de estudante. O aplicativo lógico também usa uma ação enviar email de aprovação que encaminha o conteúdo do email recebido junto com uma solicitação de aprovação.

O código extrai os endereços de email da Propriedade do gatilho `Body` e retorna os endereços junto com o `SelectedOption` valor da propriedade da ação de aprovação. A ação inclui explicitamente a ação enviar email de aprovação como uma dependência no `explicitDependencies`  >  `actions` atributo.

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

Essa ação chama criado anteriormente [função do Azure](../azure-functions/functions-get-started.md).

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

| Valor | Type | Descrição | 
|-------|------|-------------|  
| <*Azure-function-ID*> | String | O ID do recurso para a função do Azure que você deseja chamar. Aqui está o formato para este valor:<p>"/subscriptions/<*Azure-subscription-ID*>/resourceGroups/<*Azure-resource-group*>/providers/Microsoft.Web/sites/<*Azure-function-app-name*>/functions/<*Azure-function-name*>" | 
| <*tipo de método*> | String | O método HTTP a ser usado para chamar a função: "GET", "PUT", "POST", "PATCH" ou "DELETE" <p>Se não for especificado, o padrão é o método "POST". | 
||||

*Opcional*

| Valor | Type | Descrição | 
|-------|------|-------------|  
| <*cabeçalho-Conteúdo*> | Objeto JSON | Nenhum cabeçalho a ser enviado com a chamada <p>Por exemplo, para definir o idioma e o tipo em uma solicitação: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*corpo-conteúdo*> | Objeto JSON | Qualquer conteúdo da mensagem para enviar a solicitação | 
| <*parâmetros de consulta*> | Objeto JSON | Os parâmetros de consulta para incluir com a chamada à API <p>Por exemplo, o `"queries": { "api-version": "2018-01-01" }` objeto adiciona `?api-version=2018-01-01` para a chamada. | 
| <*outra ação específica-de-entrada – Propriedades*> | Objeto JSON | Quaisquer outras propriedades de entrada que se aplicam a essa ação específica | 
| <*outras propriedades de ação específica*> | Objeto JSON | Outras propriedades que se aplicam a essa ação específica | 
||||

Quando você salva seu aplicativo lógico, o mecanismo do Logic Apps executa essas verificações na função referenciada:

* Seu fluxo de trabalho deve ter acesso à função.

* Seu fluxo de trabalho pode usar apenas um gatilho HTTP padrão ou gatilho JSON webhook genérico. 

  O mecanismo do Logic Apps obtém e armazena em cache a URL do acionador, que é usada no runtime. No entanto, se alguma operação invalidar a URL em cache, a ação **Função** falhará no runtime. Para corrigir esse problema, salve o aplicativo lógico novamente para que o aplicativo lógico receba e armazene novamente o URL do acionador.

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

Essa ação envia uma solicitação para o ponto de extremidade HTTP ou HTTPS especificado e verifica a resposta para determinar se o fluxo de trabalho é executado. Para obter mais informações, consulte [chamar pontos de extremidade de serviço por http ou HTTPS de aplicativos lógicos do Azure](../connectors/connectors-native-http.md).

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<HTTP-or-HTTPS-endpoint-URL>",
      "headers": { "<header-content>" },
      "queries": { "<query-parameters>" },
      "body": "<body-content>",
      "authentication": { "<authentication-type-and-property-values>" },
      "retryPolicy": {
         "type": "<retry-behavior>"
      },
   },
   "runAfter": {}
}
```

*Necessária*

| Propriedade | Valor | Type | Descrição |
|----------|-------|------|-------------|
| `method` | <*tipo de método*> | String | O método a ser usado para enviar a solicitação de saída: "GET", "PUT", "POST", "PATCH" ou "DELETE" |
| `uri` | <*HTTP-ou-HTTPS-Endpoint-URL*> | String | A URL de ponto de extremidade HTTP ou HTTPS para a qual você deseja enviar a solicitação de saída. Tamanho máximo da cadeia de caracteres: 2 KB <p>Para um serviço ou recurso do Azure, essa sintaxe de URI inclui a ID de recurso e o caminho para o recurso que você deseja acessar. |
|||||

*Opcional*

| Propriedade | Valor | Type | Descrição |
|----------|-------|------|-------------|
| `headers` | <*cabeçalho-Conteúdo*> | Objeto JSON | Todos os cabeçalhos que você precisa incluir com a solicitação <p>Por exemplo, para definir o idioma e o tipo: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| `queries` | <*parâmetros de consulta*> | Objeto JSON | Todos os parâmetros de consulta que você precisa usar na solicitação <p>Por exemplo, o `"queries": { "api-version": "2018-01-01" }` objeto adiciona `?api-version=2018-01-01` para a chamada. |
| `body` | <*corpo-conteúdo*> | Objeto JSON | O conteúdo da mensagem para enviar como carga de solicitação |
| `authentication` | <*autenticação-tipo-e-propriedade-valores*> | Objeto JSON | O modelo de autenticação que a solicitação usa para autenticar solicitações de saída. Para obter mais informações, confira [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). Além do Agendador, a propriedade `authority` tem suporte. Quando não especificado, o valor padrão é `https://management.azure.com/` , mas você pode usar um valor diferente. |
| `retryPolicy` > `type` | <*comportamento de repetição*> | Objeto JSON | Personaliza o comportamento de repetição para falhas intermitentes, que têm o código de status 408, 429 e 5XX e todas as exceções de conectividade. Para mais informações, consulte [Políticas de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies). |
| <*outra ação específica-de-entrada – Propriedades*> | <*entrada-Propriedade*> | Objeto JSON | Quaisquer outras propriedades de entrada que se aplicam a essa ação específica |
| <*outras propriedades de ação específica*> | <*Propriedade-valor*> | Objeto JSON | Outras propriedades que se aplicam a essa ação específica |
|||||

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

### <a name="join-action"></a>Ação unir

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

| Valor | Type | Descrição | 
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

### <a name="parse-json-action"></a>Ação analisar JSON

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

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*Origem JSON*> | Objeto JSON | O conteúdo JSON que você deseja analisar | 
| <*JSON-esquema*> | Objeto JSON | O esquema JSON que descreve o conteúdo JSON subjacente, que a ação usa para analisar o conteúdo JSON de origem. <p>**Dica**: no Logic Apps Designer, você pode fornecer o esquema ou fornecer uma amostra de carga para que a ação possa gerar o esquema. | 
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

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*array*> | Array | A matriz ou expressão que fornece os itens de origem. Se você especificar uma expressão, coloque essa expressão entre aspas duplas. |
| <*condição ou filtro*> | String | A condição usada para filtrar itens na matriz de origem <p>**Observação**: se nenhum valor atender à condição, a ação criará uma matriz vazia. |
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

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*resposta-status-código*> | Integer | O código de status HTTP enviado para a solicitação recebida. O código padrão é "200 OK", mas o código pode ser qualquer código de status válido que comece com 2xx, 4xx ou 5xx, mas não com 3xxx. | 
|||| 

*Opcional*

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*cabeçalhos de resposta*> | Objeto JSON | Um ou mais cabeçalhos para incluir com a resposta | 
| <*corpo da resposta*> | Vários | O corpo da resposta, que pode ser uma string, um objeto JSON ou até mesmo conteúdo binário de uma ação anterior | 
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

* A solicitação original Obtém a resposta do fluxo de trabalho somente quando todas as ações exigidas pela ação de **resposta** são concluídas dentro do [limite de tempo limite de http](../logic-apps/logic-apps-limits-and-config.md#http-limits).

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

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*array*> | Array | A matriz ou expressão que fornece os itens de origem. Certifique-se de que você coloque uma expressão entre aspas duplas. <p>**Observação**: se a matriz de origem estiver vazia, a ação cria uma matriz vazia. | 
| <*nome da chave*> | String | O nome da propriedade atribuído ao resultado da *expressão* <> <p>Para adicionar uma nova propriedade a todos os objetos na matriz de saída, forneça um <*nome-chave*> para essa propriedade e uma <*expressão*> para o valor da propriedade. <p>Para remover uma propriedade de todos os objetos na matriz, omita o <*nome-chave*> para essa propriedade. | 
| <*expression*> | String | A expressão que transforma o item na matriz de origem e atribui o resultado a <*nome-chave*> | 
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

| Valor | Type | Descrição | 
|-------|------|-------------| 
| \<CSV *or* HTML>| String | O formato para a tabela que você deseja criar | 
| <*array*> | Array | A matriz ou expressão que fornece os itens de origem para a tabela <p>**Observação**: se a matriz de origem estiver vazia, a ação cria uma tabela vazia. | 
|||| 

*Opcional*

Para especificar ou customizar cabeçalhos e valores de coluna, use a matriz`columns`. Quando os pares `header-value` tiverem o mesmo nome de cabeçalho, seus valores aparecerão na mesma coluna sob esse nome de cabeçalho. Caso contrário, cada cabeçalho exclusivo define uma coluna exclusiva.

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*nome da coluna*> | String | O nome do cabeçalho de uma coluna | 
| <*coluna-valor*> | Qualquer | O valor nessa coluna | 
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

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*Estado*> | String | O status para retornar para a execução: "Falha", "Cancelado" ou "Êxito" |
|||| 

*Opcional*

As propriedades para o objeto "runStatus" aplicam-se somente quando a propriedade "runStatus" está configurada para o status "Failed".

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*Erro-código-ou-nome*> | String | O código ou o nome do erro |
| <*mensagem de erro*> | String | A mensagem ou o texto que descreve o erro e as ações do usuário do aplicativo pode levar | 
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

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*número de unidades*> | Integer | Para o **atraso** ação, o número de unidades de espera | 
| <*interval*> | String | Para o **atraso** ação, o intervalo de espera: "Second", "Minuto", "Hour", "Day", "Week", "Mês" | 
| <*carimbo de data/hora*> | String | Para o **atraso até que** ação, a data e hora para retomar a execução. Esse valor deve usar o [formato de hora UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time). | 
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

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*Nested-Logic-app-Name*> | String | O nome do aplicativo lógico que você deseja chamar | 
| <*nome do gatilho*> | String | O nome do gatilho no aplicativo lógico aninhado que você deseja chamar | 
| <*Azure-Subscription-ID*> | String | A ID de assinatura do Azure para o aplicativo lógico aninhado |
| <*Azure-resource-group*> | String | O nome do grupo de recursos do Azure para o aplicativo lógico aninhado |
| <*Nested-Logic-app-Name*> | String | O nome do aplicativo lógico que você deseja chamar |
||||

*Opcional*

| Valor | Type | Descrição | 
|-------|------|-------------|  
| <*cabeçalho-Conteúdo*> | Objeto JSON | Nenhum cabeçalho a ser enviado com a chamada | 
| <*corpo-conteúdo*> | Objeto JSON | Qualquer conteúdo da mensagem a ser enviado com a chamada | 
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

Essa ação de loop itera através de uma matriz e executa ações em cada item da matriz. Por padrão, o loop "para cada" é executado em paralelo até um número máximo de loops. Para esse máximo, consulte [limites e configuração](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Saiba [como criar loops "para cada"](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

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

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*ação-1... p*> | String | Os nomes das ações que são executados em cada item da matriz | 
| <*ação-definição-1... p*> | Objeto JSON | As definições das ações que são executados | 
| <*expressão for-each*> | String | A expressão que referencia cada item na matriz especificada | 
|||| 

*Opcional*

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*count*> | Integer | Por padrão, as iterações de loop "for each" são executadas ao mesmo tempo (simultaneamente ou em paralelo) até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar esse limite, definindo um novo <*contagem*> de valor, consulte [alterar "loop"for each simultaneidade](#change-for-each-concurrency). | 
| <*operação-opção*> | String | Para executar um loop "for each" em sequência, em vez de em paralelo, defina <*opção de operação*> para `Sequential` ou <*contagem*> para `1`, mas não ambos. Para obter mais informações, consulte [executar "" for each executa um loop em sequência](#sequential-for-each). | 
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

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*problema*> | Objeto JSON | A condição, o que pode ser uma expressão, para avaliar | 
| <*ação-1*> | Objeto JSON | A ação a ser executada quando a <*condição*> é avaliada como verdadeira | 
| <*ação-definição*> | Objeto JSON | A definição da ação | 
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
  
| JSON | Result | 
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

| Valor | Type | Descrição | 
|-------|------|-------------|  
| <*ação interna-1... p*> | Objeto JSON | Uma ou mais ações que são executadas dentro do escopo |
| <*ação-entradas*> | Objeto JSON | As entradas para cada ação |
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

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*expressão-objeto-ou-token*> | Varia | A expressão, um objeto JSON ou um token para avaliar | 
| <*nome da ação*> | String | O nome da ação a ser executada para o caso correspondente | 
| <*ação-definição*> | Objeto JSON | A definição da ação a ser executada para o caso correspondente | 
| <*correspondência-valor*> | Varia | O valor para comparar com o resultado avaliado | 
|||| 

*Opcional*

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*nome-da-ação padrão*> | String | O nome da ação padrão a ser executada quando nenhum caso correspondente existir | 
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

| Valor | Type | Descrição | 
|-------|------|-------------| 
| <*nome da ação*> | String | O nome para a ação que você deseja executar dentro do loop | 
| <*tipo de ação*> | String | O tipo de ação que você deseja executar | 
| <*ação-entradas*> | Vários | As entradas para a ação a ser executada | 
| <*problema*> | String | A condição ou expressão a ser avaliada depois que todas as ações no loop concluem a execução | 
| <*contagem de loops*> | Integer | O limite no maior número de loops que a ação pode executar. Para obter mais informações sobre o limite padrão e máximo, consulte [limites e configuração para aplicativos lógicos do Azure](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). | 
| <*tempo limite do loop*> | String | O limite de tempo mais longo que o loop pode ser executados. O padrão `timeout` valor é `PT1H`, que é o necessário [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). |
|||| 

> [!NOTE]
> Se a expressão depender da saída de qualquer ação dentro do loop Until, certifique-se de que você conta com qualquer falha resultante dessa ação.

*Exemplo*

Essa definição de ação de loop envia uma solicitação HTTP para a URL especificada até que uma dessas condições seja atendida:

* A solicitação obtém uma resposta com o código de status "200 OK".
* O loop foi executado 60 vezes.
* O loop foi executado por uma hora.

```json
 "Run_until_loop_succeeds_or_expires": {
    "type": "Until",
    "actions": {
        "HTTP": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "expression": "@equals(outputs('HTTP')['statusCode'], 200)",
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

## <a name="runtime-configuration-settings"></a>Definições de configuração de runtime

Você pode alterar o comportamento de tempo de execução padrão para gatilhos e ações adicionando essas `runtimeConfiguration` Propriedades à definição do gatilho ou da ação.

| Propriedade | Type | Descrição | Gatilho ou ação | 
|----------|------|-------------|-------------------| 
| `runtimeConfiguration.concurrency.runs` | Integer | Altere o [*limite padrão*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) no número de instâncias de fluxo de trabalho que podem ser executadas ao mesmo tempo (simultaneamente ou em paralelo). O ajuste desse valor pode ajudar a limitar o número de solicitações que os sistemas de back-end recebem. <p>Definindo o `runs` propriedade para `1` funciona da mesma forma que a configuração a `operationOptions` propriedade `SingleInstance`. Você pode definir a propriedade, mas não ambos. <p>Para alterar o limite padrão, consulte [simultaneidade do gatilho de alteração](#change-trigger-concurrency) ou [disparar instâncias sequencialmente](#sequential-trigger). | Todos os gatilhos | 
| `runtimeConfiguration.concurrency.maximumWaitingRuns` | Integer | Altere o [*limite padrão*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) no número de instâncias de fluxo de trabalho que devem aguardar para serem executadas quando seu aplicativo lógico já estiver executando o máximo de instâncias simultâneas. <p>Para alterar o limite padrão, consulte [execuções de espera da alteração limitam](#change-waiting-runs). | Todos os gatilhos | 
| `runtimeConfiguration.concurrency.repetitions` | Integer | Altere o [*limite padrão*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) no número de iterações de loop "for each" que podem ser executadas ao mesmo tempo (simultaneamente ou em paralelo). <p>Definindo o `repetitions` propriedade para `1` funciona da mesma forma que a configuração a `operationOptions` propriedade `SingleInstance`. Você pode definir a propriedade, mas não ambos. <p>Para alterar o limite padrão, consulte [Alterar "para cada" simultaneidade](#change-for-each-concurrency) ou [Executar "para cada" faz um loop sequencialmente](#sequential-for-each). | Ação: <p>[Foreach](#foreach-action) | 
| `runtimeConfiguration.paginationPolicy.minimumItemCount` | Integer | Para ações específicas que dão suporte e têm a paginação ativada, esse valor especifica o número *mínimo* de resultados a serem recuperados. <p>Para ativar a paginação, consulte [obter dados em massa, itens ou resultados usando paginação](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md) | Ação: variadas |
| `runtimeConfiguration.secureData.properties` | Array | Em muitos gatilhos e ações, essas configurações ocultam entradas, saídas ou ambos do histórico de execução do aplicativo lógico. <p>Para saber mais sobre como proteger esses dados, consulte [ocultar entradas e saídas do histórico de execuções](../logic-apps/logic-apps-securing-a-logic-app.md#secure-data-code-view). | Maioria dos gatilhos e ações |
| `runtimeConfiguration.staticResult` | Objeto JSON | Para ações que dão suporte e têm a configuração de [resultado estático](../logic-apps/test-logic-apps-mock-data-static-results.md) ativada, o `staticResult` objeto tem estes atributos: <p>- `name`, que faz referência ao nome de definição de resultado estático da ação atual, que aparece dentro do `staticResults` atributo no atributo do fluxo de trabalho do aplicativo lógico `definition` . Para obter mais informações, consulte [resultados estáticos-referência de esquema para linguagem de definição de fluxo de trabalho](../logic-apps/logic-apps-workflow-definition-language.md#static-results). <p> - `staticResultOptions`, que especifica se os resultados estáticos são `Enabled` ou não para a ação atual. <p>Para ativar os resultados estáticos, consulte [testar aplicativos lógicos com dados fictícios Configurando resultados estáticos](../logic-apps/test-logic-apps-mock-data-static-results.md) | Ação: variadas |
||||| 

<a name="operation-options"></a>

## <a name="operation-options"></a>Opções de operação

Você pode alterar o comportamento padrão de acionadores e ações com a propriedade `operationOptions` na definição de acionador ou ação.

| Opção de operação | Type | Descrição | Gatilho ou ação | 
|------------------|------|-------------|-------------------| 
| `DisableAsyncPattern` | String | Execute ações baseadas em HTTP de forma síncrona, em vez de assíncrona. <p><p>Para definir essa opção, consulte [executar ações de forma síncrona](#disable-asynchronous-pattern). | Ações: <p>[ApiConnection](#apiconnection-action), <br>[Http](#http-action), <br>[Resposta](#response-action) | 
| `IncludeAuthorizationHeadersInOutputs` | String | Para aplicativos lógicos que [habilitam Azure Active Directory autenticação aberta (Azure ad OAuth)](../logic-apps/logic-apps-securing-a-logic-app.md#enable-oauth) para autorizar o acesso para chamadas de entrada a um ponto de extremidade de gatilho baseado em solicitação, inclua o `Authorization` cabeçalho do token de acesso OAuth nas saídas do gatilho. Para obter mais informações, consulte [incluir o cabeçalho ' Authorization ' nas saídas do gatilho de solicitação](../logic-apps/logic-apps-securing-a-logic-app.md#include-auth-header). | Gatilhos: <p>[Solicitação](#request-trigger), <br>[Webhook HTTP](#http-webhook-trigger) | 
| `Sequential` | String | Execute "for each" loop iterações um por vez, em vez de todos ao mesmo tempo em paralelo. <p>Esta opção funciona da mesma forma que a configuração da `runtimeConfiguration.concurrency.repetitions` propriedade para `1`. Você pode definir a propriedade, mas não ambos. <p><p>Para definir essa opção, consulte [executar "for each" executa um loop em sequência](#sequential-for-each).| Ação: <p>[Foreach](#foreach-action) | 
| `SingleInstance` | String | Executar o gatilho para cada instância de aplicativo lógico em sequência e aguarde a execução anteriormente ativa concluir antes de disparar a próxima instância do aplicativo lógico. <p><p>Esta opção funciona da mesma forma que a configuração da `runtimeConfiguration.concurrency.runs` propriedade para `1`. Você pode definir a propriedade, mas não ambos. <p>Para definir essa opção, consulte [disparar instâncias sequencialmente](#sequential-trigger). | Todos os gatilhos | 
||||

<a name="change-trigger-concurrency"></a>

### <a name="change-trigger-concurrency"></a>Simultaneidade do gatilho de alteração

Por padrão, todas as instâncias de fluxo de trabalho do aplicativo lógico são executadas ao mesmo tempo (simultaneamente ou em paralelo). Esse comportamento significa que cada instância de gatilho é acionada antes da execução da instância de fluxo de trabalho ativa anteriormente. No entanto, o número de instâncias em execução simultânea tem um [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Quando o número de instâncias de fluxo de trabalho em execução simultânea atinge esse limite, todas as outras novas instâncias devem aguardar para serem executadas. Esse limite ajuda a controlar o número de solicitações que os sistemas de back-end recebem.

Quando você ativa o controle de simultaneidade do gatilho, as instâncias do gatilho são executadas em paralelo até o [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Para alterar esse limite de simultaneidade padrão, você pode usar o editor de exibição de código ou o designer de aplicativos lógicos porque alterar a configuração de simultaneidade por meio do designer adiciona ou atualiza a `runtimeConfiguration.concurrency.runs` Propriedade na definição de gatilho subjacente e vice-versa. Essa propriedade controla o número máximo de novas instâncias de fluxo de trabalho que podem ser executadas em paralelo.

Aqui estão algumas considerações sobre quando você deseja habilitar a simultaneidade em um gatilho:

* Quando a simultaneidade é habilitada, o [limite de divisão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) é reduzido significativamente para as [matrizes de reenvio em lote](#split-on-debatch). Se o número de itens exceder esse limite, o recurso de divisão será desabilitado.

* Você não pode desabilitar a simultaneidade depois de habilitar o controle de simultaneidade.

* Quando a simultaneidade está habilitada, uma instância de aplicativo lógico de execução longa pode fazer com que novas instâncias do aplicativo lógico entrem em um estado de espera. Esse estado impede que os aplicativos lógicos do Azure criem novas instâncias e ocorram mesmo quando o número de execuções simultâneas for menor do que o número máximo especificado de execuções simultâneas.

  * Para interromper esse Estado, cancele as instâncias mais antigas que *ainda estão em execução*.

    1. No menu do aplicativo lógico, selecione **visão geral**.

    1. Na seção **histórico de execuções** , selecione a instância mais antiga que ainda está em execução, por exemplo:

       ![Selecionar instância em execução mais antiga](./media/logic-apps-workflow-actions-triggers/waiting-runs.png)

       > [!TIP]
       > Para exibir apenas as instâncias que ainda estão em execução, abra a lista **todos** e selecione **executando**.

    1. Em **execução do aplicativo lógico**, selecione **Cancelar executar**.

       ![Localizar instância em execução mais antiga](./media/logic-apps-workflow-actions-triggers/cancel-run.png)

  * Para contornar essa possibilidade, adicione um tempo limite a qualquer ação que possa manter essas execuções. Se você estiver trabalhando no editor de código, consulte [alterar duração assíncrona](#asynchronous-limits). Caso contrário, se você estiver usando o designer, siga estas etapas:

    1. Em seu aplicativo lógico, na ação em que você deseja adicionar um tempo limite, no canto superior direito, selecione o botão de reticências (**...**) e, em seguida, selecione **configurações**.

       ![Abrir configurações da ação](./media/logic-apps-workflow-actions-triggers/action-settings.png)

    1. Em **tempo limite**, especifique a duração do tempo limite no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations).

       ![Especificar duração do tempo limite](./media/logic-apps-workflow-actions-triggers/timeout.png)

* Para executar seu aplicativo lógico sequencialmente, defina a simultaneidade do gatilho como `1` usando o editor de exibição de código ou o designer. Verifique se você também não definiu a propriedade do gatilho `operationOptions` como `SingleInstance` no editor de exibição de código. Caso contrário, você pode obter um erro de validação. Para obter mais informações, consulte [disparar instâncias sequencialmente](#sequential-trigger).

#### <a name="edit-in-code-view"></a>Editar no modo de exibição de código 

Na definição de gatilho subjacente, adicione a `runtimeConfiguration.concurrency.runs` propriedade, que pode ter um valor que varia de `1` para `50` .

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

Para obter mais informações, consulte [Configurações de runtime](#runtime-config-options).

#### <a name="edit-in-logic-apps-designer"></a>Editar no Designer de aplicativos lógicos

1. No canto superior direito do gatilho, selecione o botão de reticências (**...**) e, em seguida, selecione **configurações**.

1. Em **Controle de Simultaneidade**, defina **Limite** como **Ligado**. 

1. Arraste o controle deslizante **Grau de Paralelismo** para o valor desejado. Para executar seu aplicativo lógico em sequência, arraste o valor do controle deslizante para **1**.

<a name="change-for-each-concurrency"></a>

### <a name="change-for-each-concurrency"></a>Alterar a simultaneidade "for each"

Por padrão, todas as iterações de loop "for each" são executadas ao mesmo tempo (simultaneamente ou em paralelo). Esse comportamento significa que cada iteração começa a ser executada antes que a iteração anterior termine a execução. No entanto, o número de iterações em execução simultâneas tem um [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Quando o número de iterações em execução simultânea atinge esse limite, todas as outras iterações devem aguardar para serem executadas.

Para alterar o limite padrão, você pode usar o editor de visualização de código ou o Logic Apps Designer, pois a alteração da configuração de simultaneidade por meio do designer adiciona ou atualiza a propriedade `runtimeConfiguration.concurrency.repetitions` na definição de ação subjacente "para cada" e vice-versa. Essa propriedade controla o número máximo de iterações que podem ser executadas em paralelo.

> [!NOTE] 
> Se você definir a ação "for each" para executar em sequência usando o designer ou editor de exibição de código, não defina a ação `operationOptions`propriedade para`Sequential` no editor de exibição de código. Caso contrário, você pode obter um erro de validação. Para obter mais informações, consulte [executar "" for each executa um loop em sequência](#sequential-for-each).

#### <a name="edit-in-code-view"></a>Editar no modo de exibição de código 

Na definição de "para cada" subjacente, adicione ou atualize a `runtimeConfiguration.concurrency.repetitions` propriedade, que pode ter um valor que varia de `1` e `50` .

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

Para obter mais informações, consulte [Configurações de runtime](#runtime-config-options).

#### <a name="edit-in-logic-apps-designer"></a>Editar no Designer de aplicativos lógicos

1. Na ação **para cada** , no canto superior direito, selecione o botão de reticências (**...**) e, em seguida, selecione **configurações**.

1. Em **Controle de Simultaneidade**, defina **Controle de Simultaneidade** como **Ligado**.

1. Arraste o controle deslizante **Grau de Paralelismo** para o valor desejado. Para executar seu aplicativo lógico em sequência, arraste o valor do controle deslizante para **1**.

<a name="change-waiting-runs"></a>

### <a name="change-waiting-runs-limit"></a>Aguardando o limite de execuções de alteração

Por padrão, todas as instâncias de fluxo de trabalho do aplicativo lógico são executadas ao mesmo tempo (simultaneamente ou em paralelo). Esse comportamento significa que cada instância de gatilho é acionada antes da execução da instância de fluxo de trabalho ativa anteriormente. No entanto, o número de instâncias em execução simultânea tem um [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Quando o número de instâncias de fluxo de trabalho em execução simultânea atinge esse limite, todas as outras novas instâncias devem aguardar para serem executadas.

O número de execuções em espera também tem um [limite padrão](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Quando o número de execuções em espera atinge esse limite, o mecanismo de aplicativos lógicos não aceita mais novas execuções. Gatilhos de webhook e a solicitação retornam 429 erros e gatilhos recorrentes iniciar ignorar tentativas de sondagem.

Você não só pode [alterar o limite padrão na simultaneidade do gatilho](#change-trigger-concurrency), mas também pode alterar o limite padrão em espera de execuções. Na definição de gatilho subjacente, adicione a `runtimeConfiguration.concurrency.maximumWaitingRuns` propriedade, que pode ter um valor que varia de `1` para `100` .

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

Para obter mais informações, consulte [Configurações de runtime](#runtime-config-options).

<a name="sequential-trigger"></a>

### <a name="trigger-instances-sequentially"></a>Disparar as instâncias em sequência

Para executar cada instância de fluxo de trabalho do aplicativo lógico somente após a conclusão da execução da instância anterior, defina o gatilho para ser executado em sequência. Você pode usar o editor de visualização de código ou o Logic Apps Designer, pois a alteração da configuração de simultaneidade por meio do designer também adiciona ou atualiza a propriedade `runtimeConfiguration.concurrency.runs` na definição do acionador subjacente e vice-versa.

> [!NOTE] 
> Quando você configura um acionador para ser executado sequencialmente usando o designer ou o editor de visualização de código, não defina  a `operationOptions` propriedade do acionador como `Sequential`no editor de visualização de código. Caso contrário, você pode obter um erro de validação. 

#### <a name="edit-in-code-view"></a>Editar no modo de exibição de código

Na definição do gatilho, defina a essas propriedades, mas não ambos. 

Para fazer isso, defina a `runtimeConfiguration.concurrency.runs`propriedade para `1`:

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

*- ou -*

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

Para obter mais informações, consulte [definições de configuração de tempo de execução](#runtime-config-options) e opções de [operação](#operation-options).

#### <a name="edit-in-logic-apps-designer"></a>Editar no Designer de aplicativos lógicos

1. No canto superior direito do gatilho, selecione o botão de reticências (**...**) e, em seguida, selecione **configurações**.

1. Em **Controle de Simultaneidade**, defina **Limite** como **Ligado**. 

1. Arraste o **grau de paralelismo** controle deslizante para o número `1`. 

<a name="sequential-for-each"></a>

### <a name="run-for-each-loops-sequentially"></a>Execute "for each" executa um loop em sequência

Para executar uma iteração de loop "para cada" somente após a execução da iteração anterior, defina a ação "para cada" para ser executada sequencialmente. Você pode usar o editor de visualização de código ou o Logic Apps Designer, pois a alteração da simultaneidade da ação por meio do designer também adiciona ou atualiza a propriedade `runtimeConfiguration.concurrency.repetitions` na definição de ação subjacente e vice-versa.

> [!NOTE] 
> Quando você define uma ação "para cada" para ser executada sequencialmente usando o designer ou o editor de visualização de código, não defina a `operationOptions` propriedade da ação como `Sequential` no editor de visualização de código. Caso contrário, você pode obter um erro de validação. 

#### <a name="edit-in-code-view"></a>Editar no modo de exibição de código

Na definição de ação, defina uma dessas propriedades, mas não ambas. 

Para fazer isso, defina a `runtimeConfiguration.concurrency.repetitions`propriedade para `1`:

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

*- ou -*

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

Para obter mais informações, consulte [definições de configuração de tempo de execução](#runtime-config-options) e opções de [operação](#operation-options).

#### <a name="edit-in-logic-apps-designer"></a>Editar no Designer de aplicativos lógicos

1. No canto superior direito de **cada** ação, selecione o botão de reticências (**...**) e, em seguida, selecione **configurações**.

1. Em **Controle de Simultaneidade**, defina **Controle de Simultaneidade** como **Ligado**.

1. Arraste o **grau de paralelismo** controle deslizante para o número `1`.

<a name="disable-asynchronous-pattern"></a>

### <a name="run-actions-in-a-synchronous-operation-pattern"></a>Executar ações em um padrão de operação síncrona

Por padrão, as ações HTTP Action e APIConnection nos aplicativos lógicos do Azure seguem o [*padrão de operação assíncrona*](/azure/architecture/patterns/async-request-reply)padrão, enquanto a ação de resposta segue o *padrão de operação síncrona*. O padrão assíncrono especifica que depois que uma ação chama ou envia uma solicitação para o ponto de extremidade, serviço, sistema ou API especificado, o receptor retorna imediatamente uma resposta ["202 aceito"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.3) . Esse código confirma que o receptor aceitou a solicitação, mas não concluiu o processamento. A resposta pode incluir um `location` cabeçalho que especifica a URL e uma ID de atualização que o chamador pode usar para sondar continuamente ou verificar o status da solicitação assíncrona até que o receptor pare de processar e retorne uma resposta de êxito ["200 OK"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.1) ou outra resposta diferente de 202. Para obter mais informações, consulte [integração assíncrona de microserviço impõe autonomia de microserviço](/azure/architecture/microservices/design/interservice-communication#synchronous-versus-asynchronous-messaging).

* No designer do aplicativo lógico, a ação HTTP, as ações APIConnection e a ação de resposta têm a configuração **padrão assíncrona** . Quando habilitada, essa configuração especifica que o chamador não aguarda o processamento ser concluído e pode passar para a próxima ação, mas continua verificando o status até que o processamento seja interrompido. Se desabilitada, essa configuração especifica que o chamador aguarda o processamento ser concluído antes de passar para a próxima ação. Para localizar essa configuração, siga estas etapas:

  1. Na barra de título da ação HTTP, selecione o botão de reticências (**...**), que abre as configurações da ação.

  1. Localize a configuração **padrão assíncrono** .

     ![Configuração de "padrão assíncrono"](./media/logic-apps-workflow-actions-triggers/asynchronous-pattern-setting.png)

* Na definição de JavaScript Object Notation (JSON) subjacente da ação, a ação HTTP e as ações APIConnection seguem implicitamente o padrão de operação assíncrona.

Em alguns cenários, talvez você queira uma ação para seguir o padrão síncrono. Por exemplo, ao usar a ação HTTP, talvez você queira:

* [Evitar tempos limite de HTTP para tarefas de execução longa](../connectors/connectors-native-http.md#avoid-http-timeouts)
* [Desabilitar verificação de cabeçalhos de local](../connectors/connectors-native-http.md#disable-location-header-check)

Nesses casos, você pode fazer uma ação ser executada de forma síncrona usando estas opções:

* Substitua a versão de sondagem dessa ação por uma versão do webhook, se disponível.

* Desabilite o comportamento assíncrono da ação seguindo uma das opções a seguir:

  * No designer do aplicativo lógico, [desative a configuração **padrão assíncrono**](#turn-off-asynchronous-pattern-setting).

  * Na definição de JSON subjacente da ação, [adicione a `"DisableAsyncPattern"` opção de operação](#add-disable-async-pattern-option).

<a name="turn-off-asynchronous-pattern-setting"></a>

#### <a name="turn-off-asynchronous-pattern-setting"></a>Desativar a configuração **padrão assíncrona**

1. No designer do aplicativo lógico, na barra de título da ação, selecione o botão de reticências (**...**), que abre as configurações da ação.

1. Localize a configuração **padrão assíncrona** , desative a configuração para **desativado** , se estiver habilitado, e selecione **concluído**.

   ![Desativar a configuração "padrão assíncrono"](./media/logic-apps-workflow-actions-triggers/disable-asynchronous-pattern-setting.png)

<a name="add-disable-async-pattern-option"></a>

#### <a name="disable-asynchronous-pattern-in-actions-json-definition"></a>Desabilitar o padrão assíncrono na definição de JSON da ação

Na definição de JSON subjacente da ação, adicione e defina a [Propriedade "operationoptions"](#operation-options) como `"DisableAsyncPattern"` sob a seção da ação `"inputs"` , por exemplo:

```json
"<some-long-running-action>": {
   "type": "Http",
   "inputs": { "<action-inputs>" },
   "operationOptions": "DisableAsyncPattern",
   "runAfter": {}
}
```

<a name="authenticate-triggers-actions"></a>

## <a name="authenticate-triggers-and-actions"></a>Autenticar gatilhos e ações

Os pontos de extremidade HTTP e HTTPS dão suporte a diferentes tipos de autenticação. Com base no gatilho ou na ação que você usa para fazer chamadas ou solicitações de saída para acessar esses pontos de extremidade, você pode selecionar entre diferentes intervalos de tipos de autenticação. Para obter mais informações, confira [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [Linguagem de Definição de Fluxo de Dados](../logic-apps/logic-apps-workflow-definition-language.md)