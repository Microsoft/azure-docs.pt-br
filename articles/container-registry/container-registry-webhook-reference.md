---
title: Referência de esquema de webhook do Registro de Contêiner do Azure
description: O webhook solicita a referência da carga JSON para o Registro de Contêiner do Azure.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/05/2019
ms.author: danlep
ms.openlocfilehash: 4c0845b9cf5194ecbd0ab813997e17e070840f44
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61331334"
---
# <a name="azure-container-registry-webhook-reference"></a>Referência de webhook do Registro de Contêiner do Azure

Você pode [configurar webhooks](container-registry-webhook.md) para o registro de contêiner que geram eventos quando certas ações são executadas em relação a ela. Por exemplo, habilite webhooks são disparados quando uma imagem de contêiner ou um gráfico do Helm é enviada por push para um registro ou excluído. Quando um webhook é disparado, o Registro de Contêiner do Azure emite uma solicitação HTTPS ou HTTP contendo informações sobre o evento para um ponto de extremidade que você especificou. Seu ponto de extremidade pode então processar o webhook e agir em conformidade.

As seções a seguir detalham o esquema das solicitações de webhook geradas por eventos com suporte. As seções de evento contêm o esquema de carga para o tipo de evento, um exemplo de carga de solicitação e um ou mais exemplos de comandos que disparam o webhook.

Para obter informações sobre a configuração de webhooks para o seu registro de contêiner do Azure, consulte [Usando Webhooks do Registro de Contêiner do Azure](container-registry-webhook.md).

## <a name="webhook-requests"></a>Solicitações de webhook

### <a name="http-request"></a>Solicitação HTTP

Um webhook disparado faz uma solicitação HTTP `POST` para o ponto de extremidade da URL que você especificou quando configurou o webhook.

### <a name="http-headers"></a>Cabeçalhos HTTP

As solicitações de webhook incluem um `Content-Type` de `application/json` se você não especificou um cabeçalho personalizado `Content-Type` para o seu webhook.

Nenhum outro cabeçalho é adicionado à solicitação além dos cabeçalhos personalizados que podem ter sido especificado para o webhook.

## <a name="push-event"></a>Enviar evento por push

Webhook disparado quando uma imagem de contêiner é enviada por push para um repositório.

### <a name="push-event-payload"></a>Enviar carga do evento por push

|Elemento|Type|DESCRIÇÃO|
|-------------|----------|-----------|
|`id`|Cadeia de caracteres|A ID do evento do webhook.|
|`timestamp`|DateTime|A hora em que o evento do webhook foi disparado.|
|`action`|Cadeia de caracteres|A ação que disparou o evento do webhook.|
|[destino](#target)|Tipo complexo|O destino do evento que desencadeou o evento do webhook.|
|[solicitação](#request)|Tipo complexo|A solicitação que gerou o evento do webhook.|

### <a name="target"></a>target

|Elemento|Type|DESCRIÇÃO|
|------------------|----------|-----------|
|`mediaType`|Cadeia de caracteres|O tipo MIME do objeto referenciado.|
|`size`|Int32|O número de bytes do conteúdo. Mesmo que o campo de Comprimento.|
|`digest`|Cadeia de caracteres|O resumo da mensagem, conforme definido pela Especificação API HTTP do Registry V2.|
|`length`|Int32|O número de bytes do conteúdo. O mesmo que o campo Tamanho.|
|`repository`|Cadeia de caracteres|Nome do repositório.|
|`tag`|Cadeia de caracteres|O nome da marca de imagem.|

### <a name="request"></a>Solicitação

|Elemento|Type|DESCRIÇÃO|
|------------------|----------|-----------|
|`id`|Cadeia de caracteres|A ID da solicitação que iniciou o evento.|
|`host`|Cadeia de caracteres|O nome de host acessível externamente da instância de registro, conforme especificado pelo cabeçalho do host HTTP em solicitações de entrada.|
|`method`|Cadeia de caracteres|O método de solicitação que gerou o evento.|
|`useragent`|Cadeia de caracteres|O cabeçalho do agente de usuário da solicitação.|

### <a name="payload-example-image-push-event"></a>Exemplo de carga: evento de envio por push de imagem

```JSON
{
  "id": "cb8c3971-9adc-488b-xxxx-43cbb4974ff5",
  "timestamp": "2017-11-17T16:52:01.343145347Z",
  "action": "push",
  "target": {
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "size": 524,
    "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
    "length": 524,
    "repository": "hello-world",
    "tag": "v1"
  },
  "request": {
    "id": "3cbb6949-7549-4fa1-xxxx-a6d5451dffc7",
    "host": "myregistry.azurecr.io",
    "method": "PUT",
    "useragent": "docker/17.09.0-ce go/go1.8.3 git-commit/afdb6d4 kernel/4.10.0-27-generic os/linux arch/amd64 UpstreamClient(Docker-Client/17.09.0-ce \\(linux\\))"
  }
}
```

Exemplo [CLI do Docker](https://docs.docker.com/engine/reference/commandline/cli/) comando que dispara a imagem **push** webhook de eventos:

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="chart-push-event"></a>Evento de envio por push do gráfico

Webhook disparado quando um gráfico do Helm é enviado para um repositório.

### <a name="chart-push-event-payload"></a>Carga do evento de envio por push de gráfico

|Elemento|Type|DESCRIÇÃO|
|-------------|----------|-----------|
|`id`|Cadeia de caracteres|A ID do evento do webhook.|
|`timestamp`|DateTime|A hora em que o evento do webhook foi disparado.|
|`action`|Cadeia de caracteres|A ação que disparou o evento do webhook.|
|[destino](#helm_target)|Tipo complexo|O destino do evento que desencadeou o evento do webhook.|

### <a name="helm_target"></a>target

|Elemento|Type|DESCRIÇÃO|
|------------------|----------|-----------|
|`mediaType`|Cadeia de caracteres|O tipo MIME do objeto referenciado.|
|`size`|Int32|O número de bytes do conteúdo.|
|`digest`|Cadeia de caracteres|O resumo da mensagem, conforme definido pela Especificação API HTTP do Registry V2.|
|`repository`|Cadeia de caracteres|Nome do repositório.|
|`tag`|Cadeia de caracteres|O nome de marca do gráfico.|
|`name`|Cadeia de caracteres|O nome do gráfico.|
|`version`|Cadeia de caracteres|A versão do gráfico.|

### <a name="payload-example-chart-push-event"></a>Exemplo de carga: evento de envio por push do gráfico

```JSON
{
  "id": "6356e9e0-627f-4fed-xxxx-d9059b5143ac",
  "timestamp": "2019-03-05T23:45:31.2614267Z",
  "action": "chart_push",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

Exemplo [CLI do Azure](/cli/azure/acr) comando que dispara a **chart_push** webhook de eventos:

```azurecli
az acr helm push wordpress-5.4.0.tgz --name MyRegistry
```

## <a name="delete-event"></a>Excluir evento

Webhook é disparado quando um repositório de imagens ou manifesto é excluído. Não é disparado quando uma marca é excluída.

### <a name="delete-event-payload"></a>Excluir carga do evento

|Elemento|Type|DESCRIÇÃO|
|-------------|----------|-----------|
|`id`|Cadeia de caracteres|A ID do evento do webhook.|
|`timestamp`|DateTime|A hora em que o evento do webhook foi disparado.|
|`action`|Cadeia de caracteres|A ação que disparou o evento do webhook.|
|[destino](#delete_target)|Tipo complexo|O destino do evento que desencadeou o evento do webhook.|
|[solicitação](#delete_request)|Tipo complexo|A solicitação que gerou o evento do webhook.|

### <a name="delete_target"></a> destino

|Elemento|Type|DESCRIÇÃO|
|------------------|----------|-----------|
|`mediaType`|Cadeia de caracteres|O tipo MIME do objeto referenciado.|
|`digest`|Cadeia de caracteres|O resumo da mensagem, conforme definido pela Especificação API HTTP do Registry V2.|
|`repository`|Cadeia de caracteres|Nome do repositório.|

### <a name="delete_request"></a> solicitação

|Elemento|Type|DESCRIÇÃO|
|------------------|----------|-----------|
|`id`|Cadeia de caracteres|A ID da solicitação que iniciou o evento.|
|`host`|Cadeia de caracteres|O nome de host acessível externamente da instância de registro, conforme especificado pelo cabeçalho do host HTTP em solicitações de entrada.|
|`method`|Cadeia de caracteres|O método de solicitação que gerou o evento.|
|`useragent`|Cadeia de caracteres|O cabeçalho do agente de usuário da solicitação.|

### <a name="payload-example-image-delete-event"></a>Exemplo de carga: evento de exclusão de imagem

```JSON
{
    "id": "afc359ce-df7f-4e32-xxxx-1ff8aa80927b",
    "timestamp": "2017-11-17T16:54:53.657764628Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
      "repository": "hello-world"
    },
    "request": {
      "id": "3d78b540-ab61-4f75-xxxx-7ca9ecf559b3",
      "host": "myregistry.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  }
```

Exemplo de comandos da [CLI do Azure](/cli/azure/acr) que disparam um webhook do evento **excluir**:

```azurecli
# Delete repository
az acr repository delete --name MyRegistry --repository MyRepository

# Delete image
az acr repository delete --name MyRegistry --image MyRepository:MyTag
```

## <a name="chart-delete-event"></a>Evento de exclusão do gráfico

Webhook é disparado quando um gráfico do Helm ou repositório é excluído. 

### <a name="chart-delete-event-payload"></a>Carga do evento de exclusão de gráfico

|Elemento|Type|DESCRIÇÃO|
|-------------|----------|-----------|
|`id`|Cadeia de caracteres|A ID do evento do webhook.|
|`timestamp`|DateTime|A hora em que o evento do webhook foi disparado.|
|`action`|Cadeia de caracteres|A ação que disparou o evento do webhook.|
|[destino](#chart_delete_target)|Tipo complexo|O destino do evento que desencadeou o evento do webhook.|

### <a name="chart_delete_target"></a> destino

|Elemento|Type|DESCRIÇÃO|
|------------------|----------|-----------|
|`mediaType`|Cadeia de caracteres|O tipo MIME do objeto referenciado.|
|`size`|Int32|O número de bytes do conteúdo.|
|`digest`|Cadeia de caracteres|O resumo da mensagem, conforme definido pela Especificação API HTTP do Registry V2.|
|`repository`|Cadeia de caracteres|Nome do repositório.|
|`tag`|Cadeia de caracteres|O nome de marca do gráfico.|
|`name`|Cadeia de caracteres|O nome do gráfico.|
|`version`|Cadeia de caracteres|A versão do gráfico.|

### <a name="payload-example-chart-delete-event"></a>Exemplo de carga: evento de exclusão do gráfico

```JSON
{
  "id": "338a3ef7-ad68-4128-xxxx-fdd3af8e8f67",
  "timestamp": "2019-03-06T00:10:48.1270754Z",
  "action": "chart_delete",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

Exemplo [CLI do Azure](/cli/azure/acr) comando que dispara a **chart_delete** webhook de eventos:

```azurecli
az acr helm delete wordpress --version 5.4.0 --name MyRegistry
```

## <a name="next-steps"></a>Próximas etapas

[Como usar webhooks do Registro de Contêiner do Azure](container-registry-webhook.md)