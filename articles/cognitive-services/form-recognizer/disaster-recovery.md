---
title: Diretrizes de recuperação de desastre para o reconhecedor do Azure Form
titleSuffix: Azure Cognitive Services
description: Saiba como usar a API de modelo de cópia para fazer backup dos recursos do reconhecedor de formulário.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 05/27/2020
ms.author: pafarley
ms.openlocfilehash: 747ceb0106f437f9e2442c2b8c68c0b73a9107a6
ms.sourcegitcommit: 02ed9acd4390b86c8432cad29075e2204f6b1bc3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/29/2020
ms.locfileid: "97808242"
---
# <a name="back-up-and-recover-your-form-recognizer-models"></a>Fazer backup e recuperar seus modelos de reconhecedor de formulário

Ao criar um recurso de reconhecimento de formulário no portal do Azure, você especifica uma região. A partir de então, o recurso e todas as suas operações permanecem associados a essa região de servidor do Azure específica. É raro, mas não impossível, encontrar um problema de rede que atinge toda uma região. Se sua solução precisa estar sempre disponível, você deve criá-la para fazer failover em outra região ou dividir a carga de trabalho entre duas ou mais regiões. Ambas as abordagens exigem pelo menos dois recursos de reconhecedor de formulário em regiões diferentes e a capacidade de sincronizar modelos personalizados entre regiões.

A API de cópia habilita esse cenário permitindo que você copie modelos personalizados de uma conta de reconhecedor de formulário ou para outros, que podem existir em qualquer região geográfica com suporte. Este guia mostra como usar a API REST de cópia com ondulação. Você também pode usar um serviço de solicitação HTTP como o postmaster para emitir as solicitações.

## <a name="business-scenarios"></a>Cenários de negócios

Se seu aplicativo ou negócio depende do uso de um modelo personalizado do reconhecedor de formulário, recomendamos que você copie seu modelo para outra conta do reconhecedor de formulário em outra região. Se ocorrer uma interrupção regional, você poderá acessar o modelo na região onde ele foi copiado.

##  <a name="prerequisites"></a>Pré-requisitos

1. Dois formulários reconhecedor recursos do Azure em diferentes regiões do Azure. Se você não os tiver, vá para a portal do Azure e <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title=" crie um novo recurso reconhecedor de formulário " target="_blank"> criar um novo recurso reconhecedor de formulário <span class="docon docon-navigate-external x-hidden-focus"></span> </a> .
1. A chave de assinatura, a URL de ponto de extremidade e a ID de assinatura do recurso de reconhecimento de formulário. Você pode encontrar esses valores na guia **visão geral** do recurso na portal do Azure.


## <a name="copy-api-overview"></a>Visão geral da API de cópia

O processo para copiar um modelo personalizado consiste nas seguintes etapas:

1. Primeiro, você emite uma solicitação de cópia de autorização para o recurso de destino &mdash; que é, o recurso que receberá o modelo copiado. Você obtém novamente a URL do modelo de destino recém-criado, que receberá os dados copiados.
1. Em seguida, você envia a solicitação de cópia para o recurso &mdash; de origem o recurso que contém o modelo a ser copiado. Você obterá uma URL que pode consultar para acompanhar o progresso da operação.
1. Você usará suas credenciais de recurso de origem para consultar a URL de progresso até que a operação seja bem-sucedida. Você também pode consultar a nova ID do modelo no recurso de destino para obter o status do novo modelo.

> [!CAUTION]
> Atualmente, a API de cópia não dá suporte a IDs de modelo para [modelos personalizados compostos](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/Compose). A composição de modelo é um recurso de visualização na visualização v 2.1-Preview. 2. 

## <a name="generate-copy-authorization-request"></a>Gerar solicitação de autorização de cópia

A solicitação HTTP a seguir obtém a autorização de cópia do recurso de destino. Você precisará inserir o ponto de extremidade e a chave do recurso de destino como cabeçalhos.

```
POST https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/copyAuthorization HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

Você receberá uma `201\Created` resposta com um `modelId` valor no corpo. Essa cadeia de caracteres é a ID do modelo recém-criado (em branco). O `accessToken` é necessário para a API copiar dados para esse recurso, e o `expirationDateTimeTicks` valor é a expiração do token. Salve todos esses três valores em um local seguro.

```
HTTP/1.1 201 Created
Location: https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d
{"modelId":"<your model ID>","accessToken":"<your access token>","expirationDateTimeTicks":637233481531659440}
```

## <a name="start-copy-operation"></a>Iniciar operação de cópia

A solicitação HTTP a seguir inicia a operação de cópia no recurso de origem. Você precisará inserir o ponto de extremidade e a chave do recurso de origem como cabeçalhos. Observe que a URL da solicitação contém a ID do modelo de origem que você deseja copiar.

```
POST https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/<your model ID>/copy HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

O corpo da sua solicitação precisa ter o formato a seguir. Você precisará inserir a ID do recurso e o nome da região do recurso de destino. Você pode encontrar a ID do recurso na guia **Propriedades** do recurso na portal do Azure, e pode encontrar o nome da região na guia **chaves e ponto de extremidade** . Você também precisará da ID do modelo, do token de acesso e do valor de expiração que você copiou da etapa anterior.

```json
{
   "targetResourceId": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}",  
   "targetResourceRegion": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}",
   "copyAuthorization": {"modelId":"<your model ID>","accessToken":"<your access token>","expirationDateTimeTicks":637233481531659440}
}
```

> [!NOTE]
> A API de cópia dá suporte de forma transparente ao recurso [AEK/CMK](https://msazure.visualstudio.com/Cognitive%20Services/_wiki/wikis/Cognitive%20Services.wiki/52146/Customer-Managed-Keys) . Isso não requer tratamento especial, mas observe que, se você estiver copiando entre um recurso não criptografado para um recurso criptografado, você precisará incluir o cabeçalho da solicitação `x-ms-forms-copy-degrade: true` . Se esse cabeçalho não for incluído, a operação de cópia falhará e retornará um `DataProtectionTransformServiceError` .

Você receberá uma `202\Accepted` resposta com um cabeçalho Operation-Location. Esse valor é a URL que você usará para acompanhar o progresso da operação. Copie-o em um local temporário para a próxima etapa.

```
HTTP/1.1 202 Accepted
Operation-Location: https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1
```

### <a name="common-errors"></a>Erros comuns

|Erro|Resolução|
|:--|:--|
| 400/solicitação inadequada com `"code:" "1002"` | Indica erro de validação ou solicitação de cópia mal formada. Problemas comuns incluem: a) carga inválida ou modificada `copyAuthorization` . b) valor expirado para o `expirationDateTimeTicks` token (a `copyAuhtorization` carga é válida por 24 horas). c) inválido ou sem suporte `targetResourceRegion` . d) cadeia de caracteres inválida ou malformada `targetResourceId` .
|

## <a name="track-copy-progress"></a>Acompanhar o progresso da cópia

Acompanhe seu progresso consultando a API **obter resultado de modelo de obtenção** no ponto de extremidade de recurso de origem.

```
GET https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1 HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

Sua resposta irá variar dependendo do status da operação. Procure o `"status"` campo no corpo JSON. Se você estiver automatizando essa chamada à API em um script, é recomendável consultar a operação uma vez a cada segundo.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"status":"succeeded","createdDateTime":"2020-04-23T18:18:01.0275043Z","lastUpdatedDateTime":"2020-04-23T18:18:01.0275048Z","copyResult":{}}
```

### <a name="common-errors"></a>Erros comuns

|Erro|Resolução|
|:--|:--|
|"Errors": [{"Code": "AuthorizationError",<br>"mensagem": "falha de autorização devido a <br>declarações de autorização ausentes ou inválidas. "}]   | Ocorre quando a `copyAuthorization` carga ou o conteúdo é modificado a partir do que foi retornado pela `copyAuthorization` API. Verifique se a carga tem o mesmo conteúdo exato que foi retornado da chamada anterior `copyAuthorization` .|
|"Errors": [{"Code": "AuthorizationError",<br>"Message": "não foi possível recuperar a autorização <br>los. Se esse problema persistir, use outro <br>modelo de destino para copiar em. "}] | Indica que a `copyAuthorization` carga está sendo reutilizada com uma solicitação de cópia. Uma solicitação de cópia com sucesso não permitirá nenhuma solicitação adicional que use a mesma `copyAuthorization` carga. Se você gerar um erro separado (como aqueles indicados abaixo) e depois tentar novamente a cópia com a mesma carga de autorização, esse erro será gerado. A resolução é gerar uma nova `copyAuthorization` carga e, em seguida, emitir novamente a solicitação de cópia.|
|"Errors": [{"Code": "DataProtectionTransformServiceError",<br>"Message": "a solicitação de transferência de dados não é permitida <br>à medida que ele faz downgrade para um esquema de proteção de dados menos seguro. Consulte a documentação ou contate o administrador do serviço <br>para obter detalhes. "}]    | Ocorre ao copiar entre um `AEK` recurso habilitado para um `AEK` recurso não habilitado. Para permitir a cópia do modelo criptografado para o destino como não criptografado, especifique `x-ms-forms-copy-degrade: true` o cabeçalho com a solicitação de cópia.|
|"Errors": [{"Code": "ResourceResolverError",<br>"Message": "não foi possível buscar informações para o recurso cognitiva com a ID '... '. Verifique se o recurso é válido e se existe na região especificada ' westus2 '.. "}] | Indica que o recurso do Azure indicado pelo `targetResourceId` não é um recurso de cognitiva válido ou não existe. Verifique e emita novamente a solicitação de cópia para resolver esse problema.|


### <a name="optional-track-the-target-model-id"></a>Adicional Acompanhar a ID do modelo de destino 

Você também pode usar a API **obter modelo personalizado** para acompanhar o status da operação consultando o modelo de destino. Chame essa API usando a ID do modelo de destino que você copiou na primeira etapa.

```
GET https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

No corpo da resposta, você verá informações sobre o modelo. Verifique o `"status"` status do modelo no campo.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"modelInfo":{"modelId":"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d","status":"ready","createdDateTime":"2020-02-26T16:59:28Z","lastUpdatedDateTime":"2020-02-26T16:59:34Z"},"trainResult":{"trainingDocuments":[{"documentName":"0.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"1.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"2.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"3.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"4.pdf","pages":1,"errors":[],"status":"succeeded"}],"errors":[]}}
```

## <a name="curl-sample-code"></a>código de exemplo de ondulação

Os trechos de código a seguir usam a rotação para fazer as chamadas à API descritas nas etapas acima. Você ainda precisará preencher as IDs de modelo e as informações de assinatura específicas para seus próprios recursos.

### <a name="generate-copy-authorization-request"></a>Gerar solicitação de autorização de cópia

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/copyAuthorization" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" 
```

### <a name="start-copy-operation"></a>Iniciar operação de cópia

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/copyAuthorization" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" --data-ascii "{ \"targetResourceId\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}\",   \"targetResourceRegion\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}\", \"copyAuthorization\": "{\"modelId\":\"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d\",\"accessToken\":\"1855fe23-5ffc-427b-aab2-e5196641502f\",\"expirationDateTimeTicks\":637233481531659440}"}"
```

### <a name="track-copy-progress"></a>Acompanhar o progresso da cópia

```bash
curl -i GET "https://<SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT>/formrecognizer/v2.0/custom/models/{SOURCE_MODELID}/copyResults/{RESULT_ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}"
```

## <a name="next-steps"></a>Próximas etapas

Neste guia, você aprendeu a usar a API de cópia para fazer backup de seus modelos personalizados em um recurso de reconhecedor de formulário secundário. Em seguida, explore os documentos de referência da API para ver o que mais você pode fazer com o reconhecedor de formulário.
* [Documentação de referência da API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)
