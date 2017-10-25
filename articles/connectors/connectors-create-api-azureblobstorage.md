---
title: "Adicionar o Conector do armazenamento de blobs do Azure a seus Aplicativos Lógicos | Microsoft Docs"
description: "Introdução e configuração do conector do armazenamento de blobs do Azure em um aplicativo lógico"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b5dc3f75-6bea-420b-b250-183668d2848d
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/02/2017
ms.author: mandia; ladocs
ms.openlocfilehash: bc7908868828bd1628633cf9e57f8c44f8000827
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-azure-blob-storage-connector-in-a-logic-app"></a>Usar o conector do armazenamento de blobs do Azure em um aplicativo lógico
Use o conector do armazenamento de Blobs do Azure para carregar, atualizar, obter e excluir blobs de sua conta de armazenamento, tudo em um aplicativo lógico.  

Com o armazenamento de blobs do Azure, você:

* Crie seu fluxo de trabalho carregando novos projetos ou obtendo arquivos que foram atualizados recentemente.
* Use as ações para obter metadados do arquivo, excluir um arquivo, copiar arquivos e muito mais. Por exemplo, quando uma ferramenta é atualizada em um site do Azure (um gatilho), em seguida, um arquivo é atualizado no armazenamento de blobs (uma ação). 

Este tópico mostra como usar o conector do armazenamento de blobs em um aplicativo lógico.

Para saber mais sobre os Aplicativos Lógicos, consulte [O que são aplicativos lógicos](../logic-apps/logic-apps-what-are-logic-apps.md) e [Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md).

Para saber mais sobre os Aplicativos Lógicos, consulte [O que são aplicativos lógicos](../logic-apps/logic-apps-what-are-logic-apps.md) e [Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-azure-blob-storage"></a>Conectar o armazenamento de blobs do Azure
Antes do aplicativo lógico poder acessar qualquer serviço, primeiro crie uma *conexão* com o serviço. Uma conexão fornece uma conectividade entre um aplicativo lógico e outro serviço. Por exemplo, para se conectar a uma conta de armazenamento, você primeiro cria uma *conexão* de armazenamento de blobs. Para criar uma conexão, insira as credenciais que você normalmente usa para acessar o serviço ao qual está se conectando. Assim, com o armazenamento do Azure, insira as credenciais de sua conta de armazenamento para criar a conexão. 

#### <a name="create-the-connection"></a>Criar a conexão
> [!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="use-a-trigger"></a>Usar um gatilho
Esse conector não tem gatilhos. Use outros gatilhos para iniciar o aplicativo lógico, como um gatilho de Recorrência, um gatilho de Webhook HTTP, gatilhos disponíveis com outros conectores e muito mais. [Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md) fornece um exemplo.

## <a name="use-an-action"></a>Usar uma ação
Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico.

1. Selecione o sinal de mais. Você tem várias opções: **adicionar uma ação**, **adicionar uma condição** ou uma das opções **Mais**.
   
    ![](./media/connectors-create-api-azureblobstorage/add-action.png)
2. Escolha **Adicionar uma ação**.
3. Na caixa de texto, digite "blob" para obter uma lista de todas as ações disponíveis.
   
    ![](./media/connectors-create-api-azureblobstorage/actions.png) 
4. Em nosso exemplo, escolha **AzureBlob – Obter metadados do arquivo usando o caminho**. Se já existir uma conexão, selecione **...** Botão (Mostrar Seletor) para selecionar um arquivo.
   
    ![](./media/connectors-create-api-azureblobstorage/sample-file.png)
   
    Se as informações de conexão forem solicitadas, insira os detalhes para criar a conexão. [Criar a conexão](connectors-create-api-azureblobstorage.md#create-the-connection) neste tópico descreve estas propriedades. 
   
   > [!NOTE]
   > Neste exemplo, obtemos os metadados de um arquivo. Para ver os metadados, adicione outra ação que cria um novo arquivo usando outro conector. Por exemplo, adicione uma ação do OneDrive que cria um novo arquivo de "teste" com base nos metadados. 


5. **Salve** as alterações (canto superior esquerdo da barra de ferramentas). Seu aplicativo lógico é salvo e pode ser habilitado automaticamente.

> [!TIP]
> O [Gerenciador de Armazenamento](http://storageexplorer.com/) é uma excelente ferramenta para gerenciar várias contas de armazenamento.

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Exiba os gatilhos e ações definidos no swagger e também os limites nos [detalhes do conector](/connectors/azureblobconnector/). 

## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md). Explore os outros conectores disponíveis nos Aplicativos Lógicos em nossa [lista de APIs](apis-list.md).

