---
title: Método de status de obtenção de documento de tradução de documentos
titleSuffix: Azure Cognitive Services
description: O método obter status do documento retorna o status de um documento específico.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 79bc3d076c1a7e164cab9c3231b29be84370e04a
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612970"
---
# <a name="document-translation-get-document-status"></a>Tradução de documentos: obter status do documento

O método obter status do documento retorna o status de um documento específico. O método retorna o status da tradução para um documento específico com base na ID da solicitação e na ID do documento.

## <a name="request-url"></a>URL da solicitação

Envie uma solicitação `GET` para:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}/documents/{documentId}
```

Saiba como localizar seu [nome de domínio personalizado](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Todas as solicitações de API para o serviço de tradução de documentos exigem um ponto de extremidade de domínio personalizado**.
> * Você não pode usar o ponto de extremidade encontrado em suas chaves de recurso portal do Azure e na página de _ponto de extremidade_ nem no ponto de extremidade do tradutor global — `api.cognitive.microsofttranslator.com` – para fazer solicitações HTTP para a tradução do documento.

## <a name="request-parameters"></a>Parâmetros da solicitação

Os parâmetros de solicitação passados na cadeia de caracteres de consulta são:

|Parâmetro de consulta|Obrigatório|Descrição|
|--- |--- |--- |
|documentId|True|A ID do documento.|
|id|True|A ID do lote.|
## <a name="request-headers"></a>Cabeçalhos da solicitação

Os cabeçalhos de solicitação são:

|Cabeçalhos|Descrição|
|--- |--- |
|Ocp-Apim-Subscription-Key|Cabeçalho de solicitação necessário|

## <a name="response-status-codes"></a>Códigos de status de resposta

Veja a seguir os possíveis códigos de status HTTP retornados por uma solicitação.

|Código de status|Descrição|
|--- |--- |
|200|OK. Solicitação bem-sucedida e é aceita pelo serviço. Os detalhes da operação são retornados. HeadersRetry-After: integerETag: String|
|401|Não autorizado. Verifique suas credenciais.|
|404|Não encontrado. O recurso não foi encontrado.|
|500|Erro Interno do Servidor.|
|Outros códigos de status|<ul><li>Número excessivo de solicitações</li><li>Servidor temporário não disponível</li></ul>|

## <a name="get-document-status-response"></a>Obter resposta de status do documento

### <a name="successful-get-document-status-response"></a>Êxito ao obter resposta de status do documento

|Nome|Type|Descrição|
|--- |--- |--- |
|caminho|string|Local do documento ou da pasta.|
|createdDateTimeUtc|string|Data e hora da criação da operação.|
|lastActionDateTimeUtc|string|Data e hora em que o status da operação foi atualizado.|
|status|String|Lista de status possíveis para o trabalho ou o documento: <ul><li>Canceled</li><li>Cancelando</li><li>Com falha</li><li>NotStarted</li><li>Executando</li><li>Com sucesso</li><li>ValidationFailed</li></ul>|
|como|string|Código de idioma de duas letras de para idioma. Consulte a lista de idiomas.|
|progress|número|Progresso da tradução, se disponível|
|id|string|ID do documento.|
|characterCharged|inteiro|Caracteres cobrados pela API.|

### <a name="error-response"></a>Resposta de erro

|Nome|Type|Descrição|
|--- |--- |--- |
|code|string|Enums que contêm códigos de erro de alto nível. Valores possíveis:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Não Autorizado</li></ul>|
|message|string|Obtém a mensagem de erro de alto nível.|
|innerError|InnerErrorV2|Novo formato de erro interno, que está em conformidade com as diretrizes de API de serviços cognitivas. Ele contém as propriedades obrigatórias ErrorCode, Message e propriedades opcionais Target, detalhes (par chave-valor), erro interno (pode ser aninhado).|
|innerError. Code|string|Obtém a cadeia de caracteres de erro do código.|
|innerError. Message|string|Obtém a mensagem de erro de alto nível.|

## <a name="examples"></a>Exemplos

### <a name="example-successful-response"></a>Exemplo de resposta bem-sucedida
O objeto JSON a seguir é um exemplo de uma resposta bem-sucedida.

```JSON
{
  "path": "https://myblob.blob.core.windows.net/destinationContainer/fr/mydoc.txt",
  "createdDateTimeUtc": "2020-03-26T00:00:00Z",
  "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
  "status": "Running",
  "to": "fr",
  "progress": 0.1,
  "id": "273622bd-835c-4946-9798-fd8f19f6bbf2",
  "characterCharged": 0
}
```

### <a name="example-error-response"></a>Exemplo de resposta de erro

O objeto JSON a seguir é um exemplo de uma resposta de erro. O esquema para outros códigos de erro é o mesmo.

Código de status: 401

```JSON
{
  "error": {
    "code": "Unauthorized",
    "message": "User is not authorized",
    "target": "Document",
    "innerError": {
      "code": "Unauthorized",
      "message": "Operation is not authorized"
    }
  }
}
```

## <a name="next-steps"></a>Próximas etapas

Siga nosso início rápido para saber mais sobre como usar a tradução de documentos e a biblioteca de clientes.

> [!div class="nextstepaction"]
> [Introdução à tradução do documento](../get-started-with-document-translation.md)
