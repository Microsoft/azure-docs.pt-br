---
title: Atividade de Função no Azure Data Factory | Microsoft Docs
description: Saiba como usar a atividade de função do Azure para executar uma função do Azure em um pipeline do Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: dfdfb9e38f16d0077175587933b0800b87cc1931
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144125"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Atividade de função do Azure no Azure Data Factory

Saiba como usar a atividade do [Azure Functions](../azure-functions/functions-overview.md) para executar uma função do Azure em um pipeline do Data Factory. Para executar uma função do Azure, você precisará criar uma conexão de serviço vinculado e uma atividade que especifica a função do Azure que você planeja executar.

Para ver uma introdução de oito minutos e uma demonstração desse recurso, assista ao seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Serviço de função vinculado do Azure

O tipo de retorno da função do Azure deve ser um `JObject` válido. (Lembre-se de que [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) *não* é um `JObject`.) Qualquer tipo de retorno diferente de `JObject` falha e gera o erro de usuário *conteúdo da resposta não é um JObject válido*.

| **Propriedade** | **Descrição** | **Obrigatório** |
| --- | --- | --- |
| type   | A propriedade type deve ser definida como: **AzureFunction** | Sim |
| url do aplicativo de função | URL para o Aplicativo de Funções do Azure. O formato é `https://<accountname>.azurewebsites.net`. Essa URL é o valor na seção **URL** ao exibir o Aplicativo de funções no portal do Azure  | Sim |
| teclas de função | Tecla de acesso para o Azure Function. Clique na seção **Gerenciar** seção para a função respectiva e copie-a na **Tecla de Função** ou na **Tecla Host**. Saiba mais aqui: [Gatilhos e associações HTTP do Azure Functions](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | Sim |
|   |   |   |

## <a name="azure-function-activity"></a>Atividade do Azure Function

| **Propriedade**  | **Descrição** | **Valores permitidos** | **Obrigatório** |
| --- | --- | --- | --- |
| name  | Nome da atividade no pipeline  | Cadeia de caracteres | Sim |
| type  | O tipo de atividade é 'AzureFunctionActivity' | Cadeia de caracteres | Sim |
| serviço vinculado | O serviço de função do Azure vinculado para o Aplicativo de funções correspondente do Azure  | Referência de serviço vinculado | Sim |
| nome da função  | Nome da função no Aplicativo de funções do Azure que essa atividade chama | Cadeia de caracteres | Sim |
| method  | Método da API REST para a chamada de função | Tipos com suporte de cadeia de caracteres: "GET", "POST", "PUT"   | Sim |
| cabeçalho  | Cabeçalhos que são enviados para a solicitação. Por exemplo, para definir o idioma e o tipo em uma solicitação: "cabeçalhos": { "Accept-Language": "en-us", "Content-Type": "application/json" } | Cadeia de caracteres (ou expressão com um resultType de cadeia de caracteres) | Não |
| body  | corpo que é enviado junto com a solicitação para o método de api de função  | Cadeia de caracteres (ou expressão com um resultType de cadeia de caracteres) ou objeto.   | Necessário para os métodos PUT/POST |
|   |   |   | |

Consulte o esquema da carga de solicitação na seção  [Esquema de carga de solicitação](control-flow-web-activity.md#request-payload-schema) .

## <a name="routing-and-queries"></a>Roteamento e consultas

A atividade do Azure Function dá suporte a **roteamento**. Por exemplo, se sua função do Azure tem o ponto de extremidade `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`, em seguida, a `functionName` usar na atividade de função do Azure é `<functionName>/<value>`. Você pode parametrizar essa função para fornecer os detalhes desejados `functionName` em tempo de execução.

A atividade do Azure Function também dá suporte a **consultas**. Uma consulta deve ser incluído como parte do `functionName`. Por exemplo, quando é o nome da função `HttpTriggerCSharp` e é a consulta que você deseja incluir `name=hello`, em seguida, você pode construir a `functionName` na atividade de função do Azure como `HttpTriggerCSharp?name=hello`. Essa função pode ser parametrizada para que o valor pode ser determinado em tempo de execução.

## <a name="timeout-and-long-running-functions"></a>Tempo limite e funções de execução longa

Azure Functions atingirá o tempo limite depois de 230 segundos, independentemente do `functionTimeout` configuração que você configurou nas configurações. Para obter mais informações, consulte [este artigo](../azure-functions/functions-versions.md#timeout). Para contornar esse comportamento, siga um padrão assíncrono ou usar as funções duráveis. O benefício das funções duráveis é que eles oferecem o seu próprio mecanismo de rastreamento de estado, assim você não terá que implementar sua própria.

Saiba mais sobre as funções duráveis [deste artigo](../azure-functions/durable/durable-functions-overview.md). Você pode configurar uma atividade de função do Azure para chamar a função durável, que retornará uma resposta com um URI diferente, como [Este exemplo](../azure-functions/durable/durable-functions-http-api.md#http-api-url-discovery). Porque `statusQueryGetUri` retorna o Status de HTTP 202 enquanto a função está em execução, você pode sondar o status da função usando uma atividade da Web. Basta configurar uma atividade da Web com o `url` campo definido como `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri`. Quando a função durável é concluído, a saída da função será a saída da atividade da Web.


## <a name="sample"></a>Amostra

Você pode encontrar um exemplo de uma fábrica de dados que usa uma função do Azure para extrair o conteúdo de um arquivo tar [aqui](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as atividades no Data Factory no [Pipelines e atividades no Azure Data Factory](concepts-pipelines-activities.md).
