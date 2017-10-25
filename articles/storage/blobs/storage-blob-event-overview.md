---
title: "Reagir a eventos do Armazenamento de Blobs do Azure (versão prévia) | Microsoft Docs"
description: Use a Grade de Eventos do Azure para assinar eventos de Armazenamento de Blobs.
services: storage,event-grid
keywords: 
author: cbrooksmsft
ms.author: cbrooks
ms.date: 08/25/2017
ms.topic: article
ms.service: storage
ms.openlocfilehash: c760cf5a9bdd4b64a60470fa48cb9b57ec4ab5fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="reacting-to-blob-storage-events-preview"></a>Reagir a eventos do Armazenamento de Blobs (versão prévia)

Os eventos do Armazenamento de Blobs do Azure permitem que os aplicativos reajam à criação e exclusão de blobs usando arquiteturas modernas sem servidor, e sem a necessidade de código complicado ou serviços de sondagem ineficientes e caros.  Em vez disso, os eventos são enviados por push pela [Grade de Eventos do Azure](https://azure.microsoft.com/services/event-grid/) aos assinantes como [Azure Functions](https://azure.microsoft.com/services/functions/), [Aplicativos Lógicos do Azure](https://azure.microsoft.com/services/logic-apps/), ou até mesmo seu próprio ouvinte http personalizado, e você só pague pelo que usa.

Entre os cenários comuns de eventos do Armazenamento de Blobs estão processamento de imagem ou vídeo, indexação de pesquisa ou qualquer fluxo de trabalho orientado a arquivos.  Os carregamentos de arquivo assíncronos são uma excelente opção para eventos.  Quando as alterações não forem frequentes, mas seu cenário exigir uma capacidade de resposta imediata, a arquitetura baseada em eventos pode ser especialmente eficaz.

![Modelo da Grade de Eventos](./media/storage-blob-event-overview/event-grid-functional-model.png)

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="join-the-preview"></a>Ingressar na versão prévia
Os eventos do Armazenamento de Blobs estão disponíveis como uma versão prévia.  Os usuários podem solicitar o ingresso na versão prévia emitindo os seguintes comandos em suas assinaturas:
```azurecli-interactive
az provider register --namespace  Microsoft.EventGrid
az feature register --name storageEventSubscriptions --namespace Microsoft.EventGrid
```
As assinaturas são adicionadas ao Programa de Visualização à medida que a capacidade é disponibilizada.  O status da solicitação pode ser monitorado emitindo o comando a seguir:
```azurecli-interactive
az feature show --name storageEventSubscriptions --namespace Microsoft.EventGrid
```
Após a alteração do estado do registro para "Registrado", você terá sido admitido no programa de versão prévia e poderá assinar os eventos do Armazenamento de Blobs para contas na localização ***Centro-Oeste dos EUA*** ou ***Oeste dos EUA 2***.  Dê uma olhada em [Rotear eventos do Armazenamento de Blobs para um ponto de extremidade da Web personalizado](storage-blob-event-quickstart.md) para obter um exemplo rápido.

## <a name="blob-storage-accounts"></a>Contas de Armazenamento de Blobs
Os eventos do Armazenamento de Blobs estão disponíveis em [contas do Armazenamento de Blobs](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-accounts) (e não em contas de armazenamento de finalidade geral).  Uma conta de Armazenamento de Blobs é uma conta de armazenamento especializada para armazenar dados não estruturados como blobs (objetos) no Armazenamento do Azure. As contas de armazenamento de Blobs são como contas de armazenamento de finalidade geral existentes e compartilham todos os excelentes recursos de durabilidade, disponibilidade, escalabilidade e desempenho que você usa atualmente, incluindo 100% de consistência de API para blobs de bloco e blobs de acréscimo. Para aplicativos que exigem apenas o armazenamento de blobs em bloco ou acréscimo, recomendamos o uso de contas de Armazenamento de Blobs.

## <a name="available-blob-storage-events"></a>Eventos do Armazenamento de Blobs disponíveis
A Grade de eventos usa [assinaturas de evento](../../event-grid/concepts.md#event-subscriptions) para rotear mensagens de evento para os assinantes.  As assinaturas de evento do armazenamento de blobs podem incluir os dois tipos de eventos:  

> |Nome do evento|Descrição|
> |----------|-----------|
> |`Microsoft.Storage.BlobCreated`|Acionado quando um blob é criado ou substituído por meio de operações `PutBlob`, `PutBlockList` ou `CopyBlob`|
> |`Microsoft.Storage.BlobDeleted`|Acionado quando um blob é excluído por meio de uma operação `DeleteBlob`|

## <a name="event-schema"></a>Esquema do evento
Eventos do armazenamento de blobs contêm todas as informações que você precisa para responder às alterações em seus dados.  Você pode identificar um evento do Armazenamento de Blobs porque a propriedade eventType começa com "Microsoft.Storage".  
Encontre informações adicionais sobre o uso de propriedades de evento da Grade de Eventos em [Esquema de eventos da Grade de Eventos](../../event-grid/event-schema.md).  

> |Propriedade|Tipo|Descrição|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |topic|string|Id completa do Azure Resource Manager da conta de armazenamento que emite o evento.|
> |subject|string|O caminho relativo do recurso até o objeto que é o assunto do evento, usando o mesmo formato estendido do Azure Resource Manager que usamos para descrever as contas de armazenamento, serviços e contêineres para o Azure RBAC.  Esse formato inclui um nome de blob que preserva as maiúsculas e minúsculas.|
> |eventTime|string|Data/hora de geração do evento, no formato ISO 8601|
> |eventType|string|"Microsoft.Storage.BlobCreated" ou "Microsoft.Storage.BlobDeleted"|
> |ID|string|Identificador exclusivo deste evento|
> |data|objeto|Coleta de dados de evento específicos do Armazenamento de Blobs|
> |data.contentType|string|O tipo de conteúdo do blob, como seria retornado no cabeçalho Content-Type do blob|
> |data.contentLength|número|O tamanho do blob como um inteiro que representa um número de bytes, como seria retornado no cabeçalho Content-Length do blob.  Enviado com o evento BlobCreated, mas não com BlobDeleted.|
> |data.url|string|A url do objeto que é o assunto do evento|
> |data.eTag|string|A etag do objeto quando esse evento foi acionado.  Não disponível para o evento BlobDeleted.|
> |data.api|string|O nome da operação de api que disparou esse evento.  Para eventos BlobCreated, esse valor será "PutBlob", "PutBlockList" ou "CopyBlob".  Para eventos BlobDeleted, esse valor será "DeleteBlob".  Esses valores são os mesmos nomes de api que estão presentes nos logs de diagnóstico do Armazenamento do Azure.  Confira [Mensagens de operações e status registradas em log](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).|
> |data.sequencer|string|Um valor de cadeia de caracteres opaca que representa a sequência lógica de eventos para qualquer nome específico de blob.  Os usuários podem usar a comparação de cadeia de caracteres padrão para entender a sequência relativa dos dois eventos no mesmo nome de blob.|
> |data.requestId|string|Id da solicitação gerada pelo serviço para a operação da API de armazenamento.  Pode ser usada para correlacionar com os logs de diagnóstico do Armazenamento do Azure usando o campo "request-id-header" nos logs, e retornada pela inicialização da chamada á API no cabeçalho 'x-ms-request-id'. Consulte [Formato de Log](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format).|
> |data.clientRequestId|string|Id da solicitação fornecida pelo cliente para a operação da API de armazenamento.  Pode ser usada para correlacionar com os logs de diagnóstico do Armazenamento do Azure usando o campo "client-request-id" nos logs, e pode ser fornecida em solicitações de cliente usando o cabeçalho "x-ms-client-request-id". Consulte [Formato de Log](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format).|
> |data.storageDiagnostics|objeto|Dados de diagnóstico ocasionalmente incluídos pelo serviço de Armazenamento do Azure.  Quando presente, deve ser ignorado pelos consumidores de evento.|

Veja um exemplo de um evento BlobCreated:
```json
[{
  "topic": "/subscriptions/319a9601-1ec0-0000-aebc-8fe82724c81e/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file1.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T01:57:26.005121Z",
  "id": "602a88ef-0001-00e6-1233-1646070610ea",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "799304a4-bbc5-45b6-9849-ec2c66be800a",
    "requestId": "602a88ef-0001-00e6-1233-164607000000",
    "eTag": "0x8D4E44A24ABE7F1",
    "contentType": "text/plain",
    "contentLength": 447,
    "blobType": "BlockBlob",
    "url": "https://myaccount.blob.core.windows.net/testcontainer/file1.txt",
    "sequencer": "00000000000000EB000000000000C65A",
  }
}]

```

Para saber mais, confira [Esquemas de evento do Armazenamento de Blobs](../../event-grid/event-schema.md#azure-blob-storage).

## <a name="filtering-events"></a>Filtragem de eventos
As assinaturas de evento de blob podem ser filtradas com base no tipo de evento e pelo nome do contêiner e nome de blob do objeto que foi criado ou excluído.  Os filtros de assunto na Grade de Eventos funcionam com base em correspondências de "começa com" e "termina com", para que os eventos com o assunto correspondente sejam entregues ao assinante.
O assunto de eventos do Armazenamento de Blobs usa o formato:
```json
/blobServices/default/containers/<containername>/blobs/<blobname>
```
Para corresponder todos os eventos de uma conta de armazenamento, você pode deixar os filtros de assunto vazios.

Para corresponder os eventos de blobs criados em um conjunto de contêineres que compartilham um prefixo, use um filtro `subjectBeginsWith` como:
```json
/blobServices/default/containers/containerprefix
```
Para corresponder os eventos de blobs criados em um contêiner específico, use um filtro `subjectBeginsWith` como:
```json
/blobServices/default/containers/containername/
```
Para corresponder os eventos de blobs criados em um contêiner específico que compartilha um prefixo de nome de blob, use um filtro `subjectBeginsWith` como:
```json
/blobServices/default/containers/containername/blobs/blobprefix
```

Para corresponder os eventos de blobs criados em um contêiner específico que compartilha um sufixo de nome de blob, use um filtro `subjectEndsWith` como “.log” ou “.jpg”

Para saber mais, confira [Conceitos da Grade de Eventos](../../event-grid/concepts.md#filters).

## <a name="practices-for-consuming-events"></a>Práticas para consumo de eventos
Aplicativos que manipulam eventos de Armazenamento de Blobs devem seguir algumas práticas recomendadas:
> [!div class="checklist"]
> * Como várias assinaturas podem ser configuradas para eventos de rota para o mesmo manipulador de eventos, é importante não supor que os eventos sejam de uma fonte específica, mas para verificar o tópico de mensagem a fim de garantir que ela venha da conta de armazenamento que você está esperando.
> * Da mesma forma, verifique se o eventType é do tipo que você está preparado para processar, e não suponha que todos os eventos recebidos serão os tipos esperados.
> * Como as mensagens podem chegar fora de ordem e após algum atraso, use os campos de etag para entender se suas informações sobre objetos ainda estão atualizadas.  Além disso, use os campos do sequenciador para entender a ordem de eventos em qualquer objeto específico.
> * Use o campo blobType para entender os tipos de operações permitidos no blob, e quais tipos de biblioteca de cliente você deve usar para acessar o blob.
> * Use o campo de url com os construtores `CloudBlockBlob` e `CloudAppendBlob` para acessar o blob.
> * Ignore os campos que você não entende.  Essa prática ajudará você a manter-se resiliente a novos recursos que possam ser adicionados no futuro.


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a Grade de Eventos e experimente os eventos do Armazenamento de Blobs:

- [Sobre a Grade de Eventos](../../event-grid/overview.md)
- [Rotear eventos do Armazenamento de Blobs para um ponto de extremidade da Web personalizado](storage-blob-event-quickstart.md)