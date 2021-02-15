---
title: Criar gatilhos baseados em evento no Azure Data Factory
description: Saiba como criar um gatilho no Azure Data Factory que executa um pipeline em resposta a um evento.
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 10/18/2018
ms.openlocfilehash: 0364bc46059593a51c3e5cd756bd7be032e69028
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393727"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Criar um gatilho que executa um pipeline em resposta a um evento
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve os gatilhos baseados em evento que você pode criar nos pipelines do Data Factory.

A EDA (arquitetura controlada por evento) é um padrão de integração de dados comum que envolve produção, detecção, consumo e reação a eventos. Normalmente, os cenários de integração de dados exigem que os clientes do Azure Data Factory disparem pipelines com base em eventos, como a chegada ou a exclusão de um arquivo em sua conta do Armazenamento do Microsoft Azure. O Data Factory agora está integrado ao [Azure Event Grid](https://azure.microsoft.com/services/event-grid/), que permite acionar pipelines em um evento.

Para uma introdução de dez minutos e demonstração desse recurso, assista ao vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> A integração descrita neste artigo depende na [Grade de Eventos do Azure](https://azure.microsoft.com/services/event-grid/). Verifique se a assinatura está registrada no provedor de recursos da Grade de Eventos. Para obter mais informações, confira [Provedores e tipos de recursos](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). Você deve ser capaz de fazer a ação *Microsoft. EventGrid/eventSubscriptions/**. Esta ação faz parte da função interna de colaborador do EventGrid EventSubscription.

## <a name="data-factory-ui"></a>IU do Data Factory

Esta seção mostra como criar um gatilho de evento dentro na interface do usuário do Azure Data Factory.

1. Acesse a **Tela de criação**

1. No canto inferior esquerdo, clique no botão **Gatilhos**

1. Clique em **+ Novo**, e isso abrirá a navegação lateral do gatilho criar

1. Selecione o tipo de gatilho **Evento**

    ![Criar novo gatilho de evento](media/how-to-create-event-trigger/event-based-trigger-image1.png)

1. Selecione sua conta de armazenamento na lista suspensa de assinatura do Azure ou manualmente usando a ID do recurso da conta de armazenamento. Escolha em qual contêiner você deseja que os eventos ocorram. A seleção de contêiner é opcional, mas lembre-se de que a seleção de todos os contêineres pode levar a um grande número de eventos.

   > [!NOTE]
   > Atualmente, o gatilho de evento dá suporte apenas ao Azure Data Lake Storage Gen2 e a contas de armazenamento da versão 2 de uso geral. Devido a uma limitação da Grade de Eventos do Azure, o Azure Data Factory dá suporte apenas a no máximo de 500 gatilhos de eventos por conta de armazenamento.

   > [!NOTE]
   > Para criar e modificar um novo gatilho de evento, a conta do Azure usada para fazer logon no Data Factory deve ter pelo menos a permissão de *proprietário* na conta de armazenamento. Nenhuma permissão adicional é necessária: a entidade de serviço para o Azure Data Factory _não precisa de_ permissão especial para a conta de armazenamento ou a grade de eventos.

1. As propriedades **Caminho do blob começa com**  e **Caminho do blob termina com** permitem especificar os contêineres, as pastas e os nomes de blob para os quais você deseja receber eventos. Seu gatilho de evento requer que pelo menos uma dessas propriedades seja definida. É possível usar diversos padrões tanto para o **caminho do Blob que começa com** como para o **caminho do Blob que termina com propriedades**, conforme mostrado nos exemplos mais adiante neste artigo.

    * **O caminho do blob começa com:** O caminho do blob deve começar com um caminho de pasta. Os valores válidos incluem `2018/` e `2018/april/shoes.csv`. Este campo não poderá ser selecionado se um contêiner não estiver selecionado.
    * **O caminho de blob termina com:** O caminho do blob deve terminar com uma extensão ou nome de arquivo. Os valores válidos incluem `shoes.csv` e `.csv`. O nome do contêiner e da pasta são opcionais, mas, quando especificados, devem ser separados por um segmento de `/blobs/`. Por exemplo, um contêiner chamado 'ordens' pode ter um valor de `/orders/blobs/2018/april/shoes.csv`. Para especificar uma pasta em qualquer contêiner, omita o caractere '/' à esquerda. Por exemplo, `april/shoes.csv` disparará um evento em qualquer arquivo chamado `shoes.csv` na pasta a chamada “abril” em qualquer contêiner. 
    * Observação: o caminho do blob **começa com** e **termina com** é o único padrão correspondente permitido no gatilho de evento. Não há suporte para outros tipos de correspondência de curingas para o tipo de gatilho.

1. Selecione se o gatilho responderá a um evento de **Blob criado**, **Blob excluído** ou ambos. No local de armazenamento especificado, cada evento vai disparar os pipelines do Azure Data Factory associados ao gatilho.

    ![Configurar o gatilho de evento](media/how-to-create-event-trigger/event-based-trigger-image2.png)

1. Selecione se o gatilho ignora ou não os blobs com zero byte.

1. Depois de configurar o gatilho, clique em **Avançar: Visualização de dados**. Esta tela mostra os blobs existentes que correspondem à sua configuração de gatilho de evento. Garanta que você tem filtros específicos. A configuração de filtros muito amplos pode corresponder a um grande número de arquivos criados/excluídos e pode impactar significativamente o seu custo. Depois que as condições de filtro tiverem sido verificadas, clique em **Concluir**.

    ![Visualização de dados do gatilho de evento](media/how-to-create-event-trigger/event-based-trigger-image3.png)

1. Para anexar um pipeline a esse gatilho, acesse a tela do pipeline e clique em **Adicionar gatilho** e selecione **Novo/Editar**. Quando a navegação lateral aparecer, clique na lista suspensa **Escolher gatilho...** e selecione o gatilho criado. Clique em **Avançar: Visualização de dados** para confirmar se a configuração está correta. Em seguida, clique em **Avançar** para validar se a visualização de dados está correta.

1. Se o pipeline tiver parâmetros, você poderá especificá-los na navegação lateral do parâmetro de execução do gatilho. O gatilho de evento captura o nome de arquivo e o caminho de pasta do blob para as propriedades `@triggerBody().folderPath` e `@triggerBody().fileName`. Para usar os valores dessas propriedades em um pipeline, é necessário mapear as propriedades para parâmetros de pipeline. Depois de mapear as propriedades para parâmetros, acesse os valores capturados pelo gatilho por meio da expressão `@pipeline().parameters.parameterName` em todo o pipeline. Clique em **Concluir** quando terminar.

    ![Mapeando propriedades para parâmetros de pipeline](media/how-to-create-event-trigger/event-based-trigger-image4.png)

No exemplo anterior, o gatilho é configurado para ser acionado quando um caminho de blob terminando em .csv for criado na pasta event-testing no contêiner sample-data. As propriedades **folderPath** e **fileName** capturam o local do novo blob. Por exemplo, quando MoviesDB.csv é adicionado ao caminho de sample-data/event-testing, `@triggerBody().folderPath` tem um valor de `sample-data/event-testing` e `@triggerBody().fileName` tem um valor de `moviesDB.csv`. Esses valores são mapeados no exemplo para os parâmetros de pipeline `sourceFolder` e `sourceFile`, que podem ser usados em todo o pipeline como `@pipeline().parameters.sourceFolder` e `@pipeline().parameters.sourceFile` respectivamente.

## <a name="json-schema"></a>JSON schema

A tabela a seguir fornece uma visão geral dos elementos do esquema relacionados aos gatilhos baseados em eventos:

| **Elemento JSON** | **Descrição** | **Tipo** | **Valores permitidos** | **Necessário** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **escopo** | A ID do recurso do Azure Resource Manager da Conta de Armazenamento. | String | ID do Azure Resource Manager | Sim |
| **events** | O tipo de eventos que causam o acionamento desse gatilho. | Array    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobDeleted | Sim, qualquer combinação desses valores. |
| **blobPathBeginsWith** | O caminho do blob deve começar com o padrão fornecido para o gatilho ser acionado. Por exemplo, `/records/blobs/december/` só aciona o gatilho para blobs na pasta `december` no contêiner `records`. | String   | | Você precisa fornecer um valor para pelo menos uma dessas propriedades: `blobPathBeginsWith` ou `blobPathEndsWith`. |
| **blobPathEndsWith** | O caminho do blob deve terminar com o padrão fornecido para o gatilho ser acionado. Por exemplo, `december/boxes.csv` só aciona o gatilho de blobs denominado `boxes` em uma pasta `december`. | String   | | Você precisa fornecer um valor para pelo menos uma dessas propriedades: `blobPathBeginsWith` ou `blobPathEndsWith`. |
| **ignoreEmptyBlobs** | Se os blobs de zero byte dispararão ou não uma execução de pipeline. Por padrão, isso é definido como true. | Boolean | true ou false | Não |

## <a name="examples-of-event-based-triggers"></a>Exemplos de gatilhos baseados em eventos

Esta seção fornece exemplos de configurações de gatilho baseado em evento.

> [!IMPORTANT]
> Você precisa incluir o segmento `/blobs/` do caminho, como mostrado nos exemplos a seguir, sempre que especificar contêiner e pasta, contêiner e arquivo ou contêiner, pasta e arquivo. Para **blobPathBeginsWith**, a interface do usuário do Data Factory adicionará automaticamente `/blobs/` entre o nome da pasta e do contêiner no JSON de gatilho.

| Propriedade | Exemplo | Descrição |
|---|---|---|
| **O caminho de blob começa com** | `/containername/` | Recebe eventos para qualquer blob no contêiner. |
| **O caminho de blob começa com** | `/containername/blobs/foldername/` | Recebe eventos para todos os blobs no contêiner `containername` e na pasta `foldername`. |
| **O caminho de blob começa com** | `/containername/blobs/foldername/subfoldername/` | Você também pode fazer referência a uma subpasta. |
| **O caminho de blob começa com** | `/containername/blobs/foldername/file.txt` | Recebe eventos para um blob denominado `file.txt` na pasta `foldername` no contêiner `containername`. |
| **O caminho de blob termina com** | `file.txt` | Recebe eventos para um blob denominado `file.txt` em qualquer caminho. |
| **O caminho de blob termina com** | `/containername/blobs/file.txt` | Recebe eventos para um blob denominado `file.txt` no contêiner `containername`. |
| **O caminho de blob termina com** | `foldername/file.txt` | Recebe eventos para um blob denominado `file.txt` na pasta `foldername` em qualquer contêiner. |

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações detalhadas sobre gatilhos, consulte [Gatilhos e execução de pipeline](concepts-pipeline-execution-triggers.md#trigger-execution).
