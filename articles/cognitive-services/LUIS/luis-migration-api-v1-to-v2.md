---
title: Migração da API v1 para a v2
titleSuffix: Azure Cognitive Services
description: O ponto de extremidade da versão 1 e a criação de APIs Reconhecimento vocal foram preteridos. Use este guia para entender como migrar para as APIs de criação e o ponto de extremidade versão 2.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: ab42ee29e508bfa62e2dba7a6e6a06baa0da2489
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560311"
---
# <a name="api-v1-to-v2-migration-guide-for-luis-apps"></a>Guia de migração da API v1 para a v2 para aplicativos do LUIS
O [ponto de extremidade](https://aka.ms/v1-endpoint-api-docs) versão 1 e as APIs de [criação](https://aka.ms/v1-authoring-api-docs) foram preteridos. Use este guia para entender como migrar para o [ponto de extremidade](https://go.microsoft.com/fwlink/?linkid=2092356) versão 2 e para as APIs de [criação](https://go.microsoft.com/fwlink/?linkid=2092087). 

## <a name="new-azure-regions"></a>Novas regiões do Azure
O LUIS tem novas [regiões](https://aka.ms/LUIS-regions) fornecidas para as APIs do LUIS. O LUIS fornece um portal diferente para grupos de regiões. O aplicativo deve ser criado na mesma região que você espera consultar. Os aplicativos não migram regiões automaticamente. Exporte o aplicativo de uma região e importe para outra para que ele fique disponível em uma nova região.

## <a name="authoring-route-changes"></a>Criação de alterações de rota
A rota da API de criação mudou de usar a rota **prog** para usar a rota **api**.


| versão | rota |
|--|--|
|1|/luis/v1.0/**prog**/apps|
|2|/luis/**api**/v2.0/apps|


## <a name="endpoint-route-changes"></a>Alterações de rota de ponto de extremidade
A API de ponto de extremidade tem novos parâmetros de cadeia de caracteres de consulta, bem como uma resposta diferente. Se o sinalizador detalhado for true, todas as intenções, independentemente da pontuação, serão retornadas em uma matriz denominada intenções, além de topScoringIntent.

| versão | Rota GET |
|--|--|
|1|/luis/v1/application?ID={appId}&q={q}|
|2|/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]|


Resposta de êxito do ponto de extremidade v1:
```json
{
  "odata.metadata":"https://dialogice.cloudapp.net/odata/$metadata#domain","value":[
    {
      "id":"bccb84ee-4bd6-4460-a340-0595b12db294","q":"turn on the camera","response":"[{\"intent\":\"OpenCamera\",\"score\":0.976928055},{\"intent\":\"None\",\"score\":0.0230718572}]"
    }
  ]
}
```

Resposta de êxito do ponto de extremidade v2:
```json
{
  "query": "forward to frank 30 dollars through HSBC",
  "topScoringIntent": {
    "intent": "give",
    "score": 0.3964121
  },
  "entities": [
    {
      "entity": "30",
      "type": "builtin.number",
      "startIndex": 17,
      "endIndex": 18,
      "resolution": {
        "value": "30"
      }
    },
    {
      "entity": "frank",
      "type": "frank",
      "startIndex": 11,
      "endIndex": 15,
      "score": 0.935219169
    },
    {
      "entity": "30 dollars",
      "type": "builtin.currency",
      "startIndex": 17,
      "endIndex": 26,
      "resolution": {
        "unit": "Dollar",
        "value": "30"
      }
    },
    {
      "entity": "hsbc",
      "type": "Bank",
      "startIndex": 36,
      "endIndex": 39,
      "resolution": {
        "values": [
          "BankeName"
        ]
      }
    }
  ]
}
```

## <a name="key-management-no-longer-in-api"></a>O gerenciamento de chaves não está mais na API
As APIs da chave do ponto de extremidade de assinatura estão preteridas, retornando 410 GONE.

| versão | rota |
|--|--|
|1|/luis/v1.0/prog/subscriptions|
|1|/luis/v1.0/prog/subscriptions/{subscriptionKey}|

As [chaves de ponto de extremidade](luis-how-to-azure-subscription.md) são geradas no portal do Azure. Atribua a chave a um aplicativo LUIS na página **[Publicar](luis-how-to-azure-subscription.md)** . Não é necessário saber o valor real da chave. O LUIS usa o nome da assinatura para fazer a atribuição. 

## <a name="new-versioning-route"></a>Nova rota de controle de versão
Agora o modelo v2 está contido em uma [versão](luis-how-to-manage-versions.md). Um nome de versão tem 10 caracteres na rota. A versão padrão é "0,1".

| versão | rota |
|--|--|
|1|/luis/v1.0/**prog**/apps/{appId}/entities|
|2|/luis/**api**/v2.0/apps/{appId}/**versions**/{versionId}/entities|

## <a name="metadata-renamed"></a>Metadados renomeados
Várias APIs que retornam metadados do LUIS têm novos nomes.

| Nome da rota v1 | Nome da rota v2 |
|--|--|
|PersonalAssistantApps |assistants|
|applicationcultures|cultures|
|applicationdomains|domains|
|applicationusagescenarios|usagescenarios|


## <a name="sample-renamed-to-suggest"></a>"Exemplo" renomeado para "sugestão"
O LUIS sugere declarações com base em [declarações de ponto de extremidade](luis-how-to-review-endpoint-utterances.md) existentes que podem aprimorar o modelo. Na versão anterior, isso era chamado de **exemplo**. Na nova versão, o nome é alterado de exemplo para **sugestão**. Isso é chamado de **[Examinar declarações de ponto de extremidade](luis-how-to-review-endpoint-utterances.md)** no site do LUIS.

| versão | rota |
|--|--|
|1|/luis/v1.0/**prog**/apps/{appId}/entities/{entityId}/**sample**|
|1|/luis/v1.0/**prog**/apps/{appId}/intents/{intentId}/**sample**|
|2|/luis/**api**/v2.0/apps/{appId}/**versions**/{versionId}/entities/{entityId}/**suggest**|
|2|/luis/**api**/v2.0/apps/{appId}/**versions**/{versionId}/intents/{intentId}/**suggest**|


## <a name="create-app-from-prebuilt-domains"></a>Criar aplicativo de domínios predefinidos
[Domínios predefinidos](luis-how-to-use-prebuilt-domains.md) fornecem um modelo de domínio predefinido. Os domínios predefinidos permitem que você desenvolva rapidamente seu aplicativo LUIS para domínios comuns. Essa API permite que você crie um novo aplicativo com base em um domínio predefinido. A resposta é o novo appID.

|Rota v2|verbo|
|--|--|
|/luis/api/v2.0/apps/customprebuiltdomains  |obter, publicar|
|/luis/api/v2.0/apps/customprebuiltdomains/{culture}  |get|

## <a name="importing-1x-app-into-2x"></a>Importando o aplicativo 1.x para 2.x
O JSON do aplicativo 1. x exportado tem algumas áreas que você precisa alterar antes de importar para o [LUIS][LUIS] 2,0. 

### <a name="prebuilt-entities"></a>Entidades predefinidas 
As [entidades predefinidas](luis-prebuilt-entities.md) mudaram. Certifique-se de estar usando as entidades predefinidas V2. Isso inclui usar [datetimeV2](luis-reference-prebuilt-datetimev2.md), em vez de datetime. 

### <a name="actions"></a>Ações
A propriedade de ações não é mais válida. Ela deve ser um vazio 

### <a name="labeled-utterances"></a>Declarações rotuladas
A V1 permitia que declarações rotuladas incluíssem espaços no início ou fim da palavra ou frase. Removeram os espaços. 

## <a name="common-reasons-for-http-response-status-codes"></a>Motivos comuns para códigos de status de resposta HTTP
Confira [LUIS API response codes](luis-reference-response-codes.md) (Códigos de resposta da API do LUIS).

## <a name="next-steps"></a>Próximas etapas

Use a documentação da API v2 atualizar as chamadas REST existentes ao [ponto de extremidade](https://go.microsoft.com/fwlink/?linkid=2092356) do LUIS e às APIs de [criação](https://go.microsoft.com/fwlink/?linkid=2092087). 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
