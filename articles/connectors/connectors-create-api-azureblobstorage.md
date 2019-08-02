---
title: Conectar-se ao armazenamento de BLOBs do Azure – aplicativos lógicos do Azure
description: Criar e gerenciar blobs no armazenamento do Azure com os Aplicativos Lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 06/20/2019
tags: connectors
ms.openlocfilehash: d9c29837e99d327112e6a9d648a5c56cc35e8555
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296644"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-with-azure-logic-apps"></a>Criar e gerenciar blobs no armazenamento de blobs do Azure com os Aplicativos Lógicos do Azure

Este artigo mostra como você pode acessar e gerenciar arquivos armazenados como blobs em sua conta de armazenamento do Azure de dentro de um aplicativo lógico com o conector do Armazenamento de Blobs do Azure. Dessa forma, você pode criar aplicativos lógicos que automatizam tarefas e fluxos de trabalho para gerenciar seus arquivos. Por exemplo, você pode criar aplicativos lógicos que criam, obtêm, atualizam e excluem arquivos na sua conta de armazenamento.

Suponha que você tem uma ferramenta que é atualizada em um site do Azure. que atua como o gatilho do aplicativo lógico. Quando esse evento ocorre, você pode fazer com que o aplicativo lógico atualize um arquivo em seu contêiner de armazenamento de blobs, o que é uma ação no aplicativo lógico.

> [!NOTE]
> Os Aplicativos Lógicos não permitem a conexão direta com a contas de Armazenamento do Azure por meio de firewalls. Para acessar essas contas de armazenamento, use uma das opções aqui:
>
> * Criar um [Ambiente de Serviço de Integração](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), que pode se conectar aos recursos em uma rede virtual do Azure.
>
> * Se você já usa o Gerenciamento de API, você pode usar esse serviço nesse cenário. Para obter mais informações, confira [Arquitetura Enterprise Integration simples](https://aka.ms/aisarch).

Se ainda não estiver familiarizado com aplicativo lógicos, consulte [O que são os Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) e [Início Rápido: criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para obter informações técnicas específicas do conector, confira a [Referência do conector do Armazenamento de Blobs do Azure](/connectors/azureblobconnector/).

## <a name="limits"></a>limites

* Por padrão, as ações de armazenamento de BLOBs do Azure podem ler ou gravar arquivos que são *50 MB ou menos*. Para lidar com arquivos maiores que 50 MB, mas até 1024 MB, dar suporte a ações de armazenamento de BLOBs do Azure [agrupamento de mensagem](../logic-apps/logic-apps-handle-large-messages.md). O **conteúdo do blob Get** ação usa implicitamente o agrupamento.

* Gatilhos de armazenamento de BLOBs do Azure não dão suporte a agrupamento. Ao solicitar o conteúdo do arquivo, disparadores selecione somente os arquivos que são 50 MB ou menos. Para obter arquivos maiores que 50 MB, siga este padrão:

  * Usar um gatilho de armazenamento de BLOBs do Azure que retorna propriedades de arquivo, como **quando um blob é adicionado ou modificado (somente propriedades)** .

  * Siga o gatilho com o armazenamento de BLOBs do Azure **conteúdo do blob Get** ação, que lê o arquivo completo e usa implicitamente o agrupamento.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Um [contêiner de armazenamento padrão e uma conta de armazenamento](../storage/blobs/storage-quickstart-blobs-portal.md)

* O aplicativo lógico onde você precisa do acesso à conta de armazenamento de blobs do Azure. Para iniciar seu aplicativo lógico com um gatilho do Armazenamento de Blobs do Azure, você precisará de um [aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Adicionar gatilho de armazenamento de blobs

Nos Aplicativos Lógicos do Azure, cada aplicativo lógico deve começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que é disparado quando um evento específico ocorre ou quando uma condição específica é atendida. Cada vez que o gatilho é acionado, o mecanismo de Aplicativos Lógicos cria uma instância de aplicativo lógico e inicia a execução do fluxo de trabalho do aplicativo.

Este exemplo mostra como você pode iniciar um fluxo de trabalho de aplicativo lógico com o **quando um blob é adicionado ou modificado (somente propriedades)** gatilho quando propriedades de um blob é adicionado ou atualizado no contêiner de armazenamento.

1. No [portal do Azure](https://portal.azure.com) ou o Visual Studio, crie um aplicativo lógico em branco, que abre o Designer de aplicativo lógico. Este exemplo usa o portal do Azure.

2. Na caixa de pesquisa, digite "blob do azure" como filtro. Na lista de gatilhos, selecione o gatilho desejado.

   Este exemplo usa esse gatilho: **Quando um blob é adicionado ou modificado (somente propriedades)**

   ![Selecionar gatilho](./media/connectors-create-api-azureblobstorage/azure-blob-trigger.png)

3. Se forem solicitados os detalhes da conexão, [crie sua conexão de armazenamento de blobs agora](#create-connection). Ou, se a conexão já existir, forneça as informações necessárias para o gatilho.

   Neste exemplo, selecione o contêiner e a pasta que você deseja monitorar.

   1. Na caixa **Contêiner**, selecione o ícone de pasta.

   2. Na lista de pastas, escolha o colchete direito ( **>** ) e navegue até localizar e selecionar a pasta desejada.

      ![Selecionar pasta](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Selecione o intervalo e a frequência com a qual o gatilho deve verificar alterações na pasta.

4. Quando terminar, selecione **Salvar** na barra de ferramentas do designer.

5. Agora, continue a adicionar uma ou mais ações ao aplicativo lógico para as tarefas que você deseja executar com os resultados do gatilho.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Adicionar ação de armazenamento de blobs

Em Aplicativos Lógicos do Azure, uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é uma etapa do fluxo de trabalho que segue um gatilho ou outra ação. Neste exemplo, o aplicativo lógico começa com o [gatilho de recorrência](../connectors/connectors-native-recurrence.md).

1. No [Portal do Azure](https://portal.azure.com) ou no Visual Studio, abra seu aplicativo lógico no Designer do Aplicativo Lógico. Este exemplo usa o portal do Azure.

2. No Designer do aplicativo lógico, sob o gatilho ou ação, escolha **nova etapa**.

   ![Adicionar uma ação](./media/connectors-create-api-azureblobstorage/add-action.png) 

   Para adicionar uma ação entre etapas existentes, mova o mouse sobre a seta de conexão. Escolha o sinal de adição ( **+** ) que aparece e selecione **adicionar uma ação**.

3. Na caixa de pesquisa, digite "blob do azure" como filtro. Na lista de ações, selecione a ação desejada.

   Este exemplo usa essa ação: **Obter conteúdo do blob**

   ![Ação selecionar](./media/connectors-create-api-azureblobstorage/azure-blob-action.png)

4. Se forem solicitados os detalhes da conexão, [crie sua conexão do Armazenamento de Blobs do Azure agora](#create-connection).
Ou, se a conexão já existir, forneça as informações necessárias para a ação.

   Neste exemplo, selecione o arquivo desejado.

   1. Na caixa **Blob**, selecione o ícone de pasta.
  
      ![Selecionar pasta](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Localize e selecione o arquivo desejado com base no número de **ID** do blob. Você pode encontrar esse número de **ID** nos metadados do blob retornados pelo gatilho de Armazenamento de Blobs descrito anteriormente.

5. Quando terminar, selecione **Salvar** na barra de ferramentas do designer.
Para testar seu aplicativo lógico, verifique se a pasta selecionada contém um blob.

Este exemplo obtém apenas o conteúdo de um blob. Para exibir o conteúdo, adicione outra ação que cria um arquivo com o blob usando outro conector. Por exemplo, adicione uma ação do OneDrive que cria um arquivo com base no conteúdo do blob.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Conectar à conta de armazenamento

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos, como gatilhos, ações e limites, conforme descrito de API do conector aberta (anteriormente conhecido como Swagger) de arquivos, consulte o [página de referência do conector](/connectors/azureblobconnector/).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
