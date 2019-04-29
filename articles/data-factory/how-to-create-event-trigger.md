---
title: Criar gatilhos baseados em evento no Azure Data Factory | Microsoft Docs
description: Saiba como criar um gatilho no Azure Data Factory que executa um pipeline em resposta a um evento.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/18/2018
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: 94c9c3f997143d72262c1ba3d8dbfea90d6f920c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61347648"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Criar um gatilho que executa um pipeline em resposta a um evento

Este artigo descreve os gatilhos baseados em evento que você pode criar nos pipelines do Data Factory.

A EDA (arquitetura controlada por evento) é um padrão de integração de dados comum que envolve produção, detecção, consumo e reação a eventos. Os cenários de integração de dados geralmente exigem que os clientes do Data Factory disparem pipelines baseados em eventos. O Data Factory agora está integrado ao [Azure Event Grid](https://azure.microsoft.com/services/event-grid/), que permite acionar pipelines em um evento.

Para uma introdução de dez minutos e demonstração desse recurso, assista ao vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> A integração descrita neste artigo depende na [Grade de Eventos do Azure](https://azure.microsoft.com/services/event-grid/). Verifique se a assinatura está registrada no provedor de recursos da Grade de Eventos. Para obter mais informações, confira [Provedores e tipos de recursos](../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

## <a name="data-factory-ui"></a>IU do Data Factory

### <a name="create-a-new-event-trigger"></a>Criar um novo gatilho de evento

Um evento típico é a chegada de um arquivo ou a exclusão de um arquivo na conta de Armazenamento do Microsoft Azure. É possível criar um gatilho que responda a esse evento no pipeline do Data Factory.

> [!NOTE]
> Essa integração suporta apenas as contas de armazenamento da versão 2 (finalidade geral).

![Criar novo gatilho de evento](media/how-to-create-event-trigger/event-based-trigger-image1.png)

### <a name="configure-the-event-trigger"></a>Configurar o gatilho de evento

Com o **caminho do Blob começa com**  e o **caminho do Blob termina com propriedades** , é possível especificar os contêineres, pastas e nomes de blob para os quais você deseja receber eventos. É possível usar diversos padrões tanto para o **caminho do Blob que começa com** como para o **caminho do Blob que termina com propriedades**, conforme mostrado nos exemplos mais adiante neste artigo. Pelo menos uma dessas propriedades é necessária.

![Configurar o gatilho de evento](media/how-to-create-event-trigger/event-based-trigger-image2.png)

### <a name="select-the-event-trigger-type"></a>Selecione o tipo de gatilho de evento

Assim que o arquivo chega ao local de armazenamento e o blob correspondente é criado, esse evento dispara e executa o pipeline do Data Factory. É possível criar um gatilho que responda a um evento de criação de blob, um evento de exclusão de blob ou ambos os eventos nos pipelines do Data Factory.

![Selecione o tipo de gatilho como evento](media/how-to-create-event-trigger/event-based-trigger-image3.png)

### <a name="map-trigger-properties-to-pipeline-parameters"></a>Mapear propriedades de gatilho para parâmetros de pipeline

Quando um gatilho de evento é acionado para um blob específico, o evento captura o nome de arquivo e o caminho de pasta do blob para as propriedades `@triggerBody().folderPath` e `@triggerBody().fileName`. Para usar os valores dessas propriedades em um pipeline, é necessário mapear as propriedades para parâmetros de pipeline. Depois de mapear as propriedades para parâmetros, acesse os valores capturados pelo gatilho por meio da expressão `@pipeline().parameters.parameterName` em todo o pipeline.

![Mapeando propriedades para parâmetros de pipeline](media/how-to-create-event-trigger/event-based-trigger-image4.png)

Por exemplo, na captura de tela anterior, o gatilho está configurado para ser acionado quando um caminho de blob que termina com `.csv` for criado na Conta de Armazenamento. Como resultado, quando um blob com a extensão `.csv` é criada em qualquer lugar da Conta de Armazenamento, as propriedades `folderPath` e `fileName` capturam o local do novo blob. Por exemplo, `@triggerBody().folderPath` tem um valor como `/containername/foldername/nestedfoldername` e `@triggerBody().fileName` tem um valor como `filename.csv`. Esses valores são mapeados no exemplo para os parâmetros de pipeline `sourceFolder` e `sourceFile`. Você pode usá-los em todo o pipeline como `@pipeline().parameters.sourceFolder` e `@pipeline().parameters.sourceFile`, respectivamente.

## <a name="json-schema"></a>JSON schema

A tabela a seguir fornece uma visão geral dos elementos do esquema relacionados aos gatilhos baseados em eventos:

| **Elemento JSON** | **Descrição** | **Tipo** | **Valores permitidos** | **Obrigatório** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **scope** | A ID do recurso do Azure Resource Manager da Conta de Armazenamento. | Cadeia de caracteres | ID do Azure Resource Manager | Sim |
| **events** | O tipo de eventos que causam o acionamento desse gatilho. | Matriz    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobDeleted | Sim, qualquer combinação desses valores. |
| **blobPathBeginsWith** | O caminho do blob deve começar com o padrão fornecido para o gatilho ser acionado. Por exemplo, `/records/blobs/december/` só aciona o gatilho para blobs na pasta `december` no contêiner `records`. | Cadeia de caracteres   | | Você precisa fornecer um valor para pelo menos uma dessas propriedades: `blobPathBeginsWith` ou `blobPathEndsWith`. |
| **blobPathEndsWith** | O caminho do blob deve terminar com o padrão fornecido para o gatilho ser acionado. Por exemplo, `december/boxes.csv` só aciona o gatilho de blobs denominado `boxes` em uma pasta `december`. | Cadeia de caracteres   | | Você precisa fornecer um valor para pelo menos uma dessas propriedades: `blobPathBeginsWith` ou `blobPathEndsWith`. |

## <a name="examples-of-event-based-triggers"></a>Exemplos de gatilhos baseados em eventos

Esta seção fornece exemplos de configurações de gatilho baseado em evento.

> [!IMPORTANT]
> Você precisa incluir o segmento `/blobs/` do caminho, como mostrado nos exemplos a seguir, sempre que especificar contêiner e pasta, contêiner e arquivo ou contêiner, pasta e arquivo.

| Propriedade | Exemplo | DESCRIÇÃO |
|---|---|---|
| **O caminho de blob começa com** | `/containername/` | Recebe eventos para qualquer blob no contêiner. |
| **O caminho de blob começa com** | `/containername/blobs/foldername/` | Recebe eventos para todos os blobs no contêiner `containername` e na pasta `foldername`. |
| **O caminho de blob começa com** | `/containername/blobs/foldername/subfoldername/` | Você também pode fazer referência a uma subpasta. |
| **O caminho de blob começa com** | `/containername/blobs/foldername/file.txt` | Recebe eventos para um blob denominado `file.txt` na pasta `foldername` no contêiner `containername`. |
| **O caminho de blob termina com** | `file.txt` | Recebe eventos para um blob denominado `file.txt` em qualquer caminho. |
| **O caminho de blob termina com** | `/containername/blobs/file.txt` | Recebe eventos para um blob denominado `file.txt` no contêiner `containername`. |
| **O caminho de blob termina com** | `foldername/file.txt` | Recebe eventos para um blob denominado `file.txt` na pasta `foldername` em qualquer contêiner. |

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações detalhadas sobre gatilhos, consulte [Gatilhos e execução de pipeline](concepts-pipeline-execution-triggers.md#triggers).
