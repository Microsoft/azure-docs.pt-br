---
title: Mover os dados de armazenamento de Blob com o Gerenciador de Armazenamento do Microsoft Azure – Processo de Ciência de Dados de Equipe
description: Saiba como usar Gerenciador de Armazenamento do Azure para carregar e baixar dados do armazenamento de BLOBs do Azure.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 53cb8cdd1c5f9824b07b16b8b6c70648603b9f38
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98788902"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Mover dados de e para o Armazenamento de Blobs do Azure usando o Gerenciador de Armazenamento do Azure
O Gerenciador de Armazenamento do Azure é um ferramenta gratuita da Microsoft que possibilita o trabalho com dados do Armazenamento do Azure no Windows, MacOS e Linux. Este tópico descreve como usá-lo para carregar e baixar dados do armazenamento de BLOBs do Azure. A ferramenta pode ser baixada em [Gerenciador de Armazenamento do Microsoft Azure](https://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Se você estiver usando a VM configurada com os scripts fornecidos pelas [Máquinas virtuais de ciências de dados no Azure](../data-science-virtual-machine/overview.md), o Gerenciador de armazenamento do Azure já estará instalado na VM.
> 
> [!NOTE]
> Para obter uma introdução completa ao armazenamento de BLOBs do Azure, consulte [noções básicas de blob do Azure](../../storage/blobs/storage-quickstart-blobs-dotnet.md) e [serviço blob do Azure](/rest/api/storageservices/Blob-Service-Concepts).   
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Este documento pressupõe que você tenha uma assinatura, uma conta de armazenamento do Azure e a chave de armazenamento correspondente dessa conta. Antes de carregar/baixar dados, você deve saber o nome da conta de armazenamento do Azure e a chave de conta. 

* Para configurar uma assinatura do Azure, consulte [avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/).
* Para obter instruções sobre como criar uma conta de armazenamento e obter informações de conta e chave, consulte [sobre contas de armazenamento do Azure](../../storage/common/storage-account-create.md). Anote a chave de acesso para sua conta de armazenamento, pois você precisa dessa chave para conectar-se à conta com a ferramenta Gerenciador de Armazenamento do Azure.
* A ferramenta Gerenciador de Armazenamento do Azure pode ser baixada em [Gerenciador de Armazenamento do Microsoft Azure](https://storageexplorer.com/). Aceite os padrões durante a instalação.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Usar o Gerenciador de Armazenamento do Azure
As etapas a seguir mostram como carregar/baixar os dados usando o Gerenciador de Armazenamento do Azure. 

1. Inicie o Gerenciador de Armazenamento do Microsoft Azure.
2. Para abrir o assistente **Entrar em sua conta...**, selecione o ícone **Configurações de conta do Azure**, depois **Adicionar uma conta** e insira suas credenciais. 
![Adicionar uma conta de armazenamento do Azure](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Para exibir o assistente **conectar ao armazenamento do Azure** , selecione o ícone **conectar ao armazenamento do Azure** . ![Clique em "conectar ao armazenamento do Azure"](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Insira a chave de acesso de sua conta de armazenamento do Azure no assistente **conectar ao armazenamento do Azure** e, em seguida, em **Avançar**. ![Inserir a chave de acesso da conta de armazenamento do Azure](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Insira o nome da conta de armazenamento na caixa **Nome da conta** e selecione **Avançar**. ![Anexar um armazenamento externo](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. A conta de armazenamento adicionada deve agora ser exibida. Para criar um contêiner de blob em uma conta de armazenamento, clique com o botão direito no nó **Contêineres de Blob** na conta, selecione **Criar Contêiner de Blob** e insira um nome.
7. Para carregar dados em um contêiner, selecione o contêiner de destino e clique no botão **Carregar**.
![Contas de armazenamento](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Clique em **...** à direita da caixa **Arquivos**, selecione um vários arquivos para carregar no sistema de arquivos e clique em **Carregar** para começar a carregar os arquivos.![Carregar os arquivos](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Para baixar os dados, selecione o blob no contêiner correspondente para baixar e clique em **Download**. ![Baixar arquivos](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)
