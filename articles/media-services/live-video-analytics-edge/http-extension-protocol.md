---
title: Protocolo de extensão HTTP – Azure
description: Neste artigo, você aprenderá a usar o protocolo de extensão HTTP para enviar mensagens entre o módulo da Análise Dinâmica de Vídeo e o seu módulo de IA ou CV.
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: 54b25894c60a39de9c0ec00cdc4982f691bf1ee3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105565734"
---
# <a name="http-extension-protocol"></a>Protocolo de extensão HTTP

A Análise Dinâmica de Vídeo do IoT Edge permite que você estenda as funcionalidades de processamento do grafo de mídia por meio de um [nó de extensão de grafo](./media-graph-extension-concept.md). Se você usar o processador de extensão HTTP como o nó de extensão, a comunicação entre o módulo da Análise Dinâmica de Vídeo e o módulo de IA ou CV será feita por HTTP

Neste artigo, você aprenderá a usar o protocolo de extensão HTTP para enviar mensagens entre o módulo da Análise Dinâmica de Vídeo e o seu módulo de IA ou CV. 

O contrato HTTP é definido entre os dois seguintes componentes:

* Servidor HTTP
* A Análise Dinâmica de Vídeo no módulo do IoT Edge funciona como o cliente HTTP

## <a name="request"></a>Solicitação

As solicitações do módulo da Análise Dinâmica de Vídeo para o servidor HTTP serão as seguintes:

|Chave|Valor|
|---|---|
|POST|`https://hostname/optional-path?optional-query`|
|Aceitar|application/json, */*|
|Autorização|Básico, Resumo, Portador (por meio do suporte ao cabeçalho personalizado)|
|Tipo de conteúdo|image/jpeg<br/>image/png<br/>image/bmp<br/>image/x-raw|
|Tamanho do corpo de Content-Length, em bytes|
|User-Agent|Serviços de Mídia do Azure|
|Corpo|Bytes de imagem, codificados como binário em um dos tipos de conteúdo compatíveis.|

### <a name="example"></a>Exemplo

```
POST http://localhost:8080/inference HTTP/1.1
Host: localhost:8080
x-ms-client-request-id: d6050cd4-c9f2-42d3-9adc-53ba7e440f17
Content-Type: image/bmp
Content-Length: 519222

(Image Binary Content)
```

## <a name="response"></a>Resposta

As respostas do módulo para o módulo da Análise Dinâmica de Vídeo devem ser as seguintes:

|Chave|Valor|
|---|---|
|Códigos de status|200 OK – Resultados da inferência encontrados<br/>204 Sem Conteúdo – Nenhum conteúdo encontrado pela IA<br/>400 Solicitação Inválida – Não esperado<br/>500 Erro Interno do Servidor – Não esperado<br/>503 Servidor Ocupado – o AMS fará uma retirada com base no cabeçalho "Retry-After" ou em um período padrão caso o cabeçalho não esteja predefinido.|
|Tipo de conteúdo|aplicativo/json|
|Content-Length|Tamanho do corpo, em bytes|
|Corpo|Objeto JSON com uma só propriedade "inferências".|

### <a name="example"></a>Exemplo

```
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 468
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 17 Apr 2020 04:44:01 GMT

{
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.9048132 },
        "box": { "l": 0.42681578, "t": 0.47660735, "w": 0.019501392, "h": 0.020954132 }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.8953932 },
        "box": { "l": 0.55083525, "t": 0.4843858, "w": 0.046550274, "h": 0.046502113 }
      }
    }    
  ]
}
```

Recomendamos expressamente que as respostas sejam retornadas por meio de documentos JSON válidos seguindo o esquema preestabelecido definido de acordo com o [modelo de objeto de esquema de metadados de inferência](./inference-metadata-schema.md). Isso garantirá melhor a interoperabilidade com outros componentes e possíveis funcionalidades futuras adicionadas ao módulo da Análise Dinâmica de Vídeo.

Se o módulo retornar uma resposta em que o tipo de conteúdo não é “application/json”, a Análise Dinâmica de Vídeo codificará a mensagem como um conteúdo de Base 64 e a serializará como um conteúdo JSON opaco.

Se o módulo retornar uma resposta com o tipo de conteúdo como “application/json”, mas o esquema JSON não seguir o esquema de metadados de inferência descrito abaixo, o conteúdo da mensagem será encaminhado pelo pipeline, mas a interoperabilidade será reduzida. Acesse [aqui](./inference-metadata-schema.md) para obter informações detalhadas e atualizadas sobre o esquema de metadados de inferência.

> [!NOTE]
> Se o módulo não produzir nenhum resultado, ele deverá retornar o código de status HTTP 204 (Sem Conteúdo) com um corpo de resposta vazio. A Análise Dinâmica de Vídeo entenderá isso como um resultado vazio e não encaminhará o evento pelo pipeline.


## <a name="next-steps"></a>Próximas etapas

[Protocolo de extensão gRPC](./grpc-extension-protocol.md)