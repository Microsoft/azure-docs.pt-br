---
title: Reagir aos eventos de armazenamento de Blobs do Azure | Microsoft Docs
description: Use a Grade de Eventos do Azure para assinar eventos de Armazenamento de Blobs.
services: storage,event-grid
author: cbrooksmsft
ms.author: cbrooks
ms.date: 01/30/2018
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.openlocfilehash: c0655d02fd5d0d64c22db286236b2a26f9e70619
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444674"
---
# <a name="reacting-to-blob-storage-events"></a>Reagir aos eventos de armazenamento de Blobs

Eventos de armazenamento do Azure permitem que os aplicativos reagir a eventos, como a criação e exclusão de blobs, usando modernas arquiteturas sem servidor. Isso é feito sem a necessidade de código complicado ou serviços de sondagem caros e ineficientes.

Em vez disso, os eventos são enviados por meio [grade de eventos do Azure](https://azure.microsoft.com/services/event-grid/) assinantes como o Azure Functions, aplicativos de lógicos do Azure, ou até mesmo seu próprio custom ouvinte http e você paga apenas pelo que usar.

Eventos de armazenamento de blob confiável são enviados para o serviço de grade de eventos que fornece serviços de entrega confiável para seus aplicativos por meio de políticas de repetição avançadas e entrega de inatividade.

Os cenários comuns de eventos de armazenamento de Blobs incluem processamento de vídeo ou imagem, indexação de pesquisa ou qualquer fluxo de trabalho orientado a arquivos. Os carregamentos de arquivo assíncronos são uma excelente opção para eventos. Quando as alterações não forem frequentes, mas seu cenário exigir uma capacidade de resposta imediata, a arquitetura baseada em eventos pode ser especialmente eficaz.

Se você quiser experimentar isso agora, consulte qualquer um destes artigos de início rápido:

|Se você quiser usar essa ferramenta:    |Consulte este artigo: |
|--|-|
|Portal do Azure    |[Início Rápido: Encaminhar eventos de armazenamento de Blob para o ponto de extremidade da web com o portal do Azure](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|CLI do Azure    |[Início Rápido: Encaminhar eventos de armazenamento para o ponto de extremidade da web com o PowerShell](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Powershell    |[Início Rápido: Encaminhar eventos de armazenamento para o ponto de extremidade da web com a CLI do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="the-event-model"></a>O modelo de evento

Grade de eventos usa [assinaturas de evento](../../event-grid/concepts.md#event-subscriptions) para rotear mensagens de evento para os assinantes. Esta imagem ilustra o relacionamento entre os editores de eventos, assinaturas de eventos e manipuladores de eventos.

![Modelo da Grade de Eventos](./media/storage-blob-event-overview/event-grid-functional-model.png)

Primeiro, inscrever-se um ponto de extremidade a um evento. Em seguida, quando um evento é disparado, o serviço de grade de eventos enviará dados sobre esse evento para o ponto de extremidade.

Consulte a [esquema de eventos do armazenamento de Blob](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) artigo para exibir:

> [!div class="checklist"]
> * Uma lista completa de eventos do armazenamento de Blob e como cada evento é disparado.
> * Um exemplo dos dados da grade de eventos enviaria para cada um desses eventos.
> * A finalidade de cada par chave-valor que aparece nos dados.

## <a name="filtering-events"></a>Filtragem de eventos

As assinaturas de evento de blob podem ser filtradas com base no tipo de evento e pelo nome do contêiner e nome de blob do objeto que foi criado ou excluído.  Filtros podem ser aplicados para assinaturas de evento ou durante a [criação](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) da assinatura do evento ou [em um momento posterior](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest). Os filtros de assunto na Grade de Eventos funcionam com base em correspondências de "começa com" e "termina com", para que os eventos com o assunto correspondente sejam entregues ao assinante.

Para saber mais sobre como aplicar filtros, consulte [filtrar eventos de grade de eventos](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

O assunto de eventos do Armazenamento de Blobs usa o formato:

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

Para corresponder todos os eventos de uma conta de armazenamento, você pode deixar os filtros de assunto vazios.

Para corresponder os eventos de blobs criados em um conjunto de contêineres que compartilham um prefixo, use um filtro `subjectBeginsWith` como:

```
/blobServices/default/containers/containerprefix
```

Para corresponder os eventos de blobs criados em um contêiner específico, use um filtro `subjectBeginsWith` como:

```
/blobServices/default/containers/containername/
```

Para corresponder os eventos de blobs criados em um contêiner específico que compartilha um prefixo de nome de blob, use um filtro `subjectBeginsWith` como:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Para corresponder os eventos de blobs criados em um contêiner específico que compartilha um sufixo de nome de blob, use um filtro `subjectEndsWith` como “.log” ou “.jpg”. Para saber mais, confira [Conceitos da Grade de Eventos](../../event-grid/concepts.md#event-subscriptions).

## <a name="practices-for-consuming-events"></a>Práticas para consumo de eventos

Aplicativos que manipulam eventos de Armazenamento de Blobs devem seguir algumas práticas recomendadas:
> [!div class="checklist"]
> * Como várias assinaturas podem ser configuradas para eventos de rota para o mesmo manipulador de eventos, é importante não supor que os eventos sejam de uma fonte específica, mas para verificar o tópico de mensagem a fim de garantir que ela venha da conta de armazenamento que você está esperando.
> * Da mesma forma, verifique se o eventType é do tipo que você está preparado para processar, e não suponha que todos os eventos recebidos serão os tipos esperados.
> * Como as mensagens podem chegar fora de ordem e após algum atraso, use os campos de etag para entender se suas informações sobre objetos ainda estão atualizadas.  Além disso, use os campos do sequenciador para entender a ordem de eventos em qualquer objeto específico.
> * Use o campo blobType para entender os tipos de operações permitidos no blob, e quais tipos de biblioteca de cliente você deve usar para acessar o blob. Os valores válidos são `BlockBlob` ou `PageBlob`. 
> * Use o campo de url com os construtores `CloudBlockBlob` e `CloudAppendBlob` para acessar o blob.
> * Ignore os campos que você não entende. Essa prática ajudará você a manter-se resiliente a novos recursos que possam ser adicionados no futuro.
> * Se você quiser garantir que o **Microsoft.Storage.BlobCreated** evento é disparado apenas quando um Blob de blocos é completamente confirmado, filtrar o evento para o `CopyBlob`, `PutBlob`, `PutBlockList` ou `FlushWithClose` REST Chamadas à API. Esses disparador de chamadas de API do **Microsoft.Storage.BlobCreated** evento somente depois que dados são totalmente confirmados ao Blob de bloco. Para saber como criar um filtro, consulte [filtrar eventos de grade de eventos](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a Grade de Eventos e experimente os eventos do Armazenamento de Blobs:

- [Sobre a Grade de Eventos](../../event-grid/overview.md)
- [Rotear eventos do Armazenamento de Blobs para um ponto de extremidade da Web personalizado](storage-blob-event-quickstart.md)
