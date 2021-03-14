---
title: Gerenciamento de conta de armazenamento do Azure Stack Edge pro GPU | Microsoft Docs
description: Descreve como usar o portal do Azure para gerenciar a conta de armazenamento em seu Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: 2d9520c8f97171dbf2f46aa2c94b9e1e280ed86c
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201276"
---
# <a name="use-the-azure-portal-to-manage-edge-storage-accounts-on-your-azure-stack-edge-pro"></a>Use o portal do Azure para gerenciar contas de armazenamento do Edge em seu Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo descreve como gerenciar contas de armazenamento de borda em seu Azure Stack Edge pro. Você pode gerenciar o Azure Stack Edge pro via portal do Azure ou por meio da interface do usuário da Web local. Use o portal do Azure para adicionar ou excluir contas de armazenamento de borda em seu dispositivo.

## <a name="about-edge-storage-accounts"></a>Sobre contas de armazenamento de borda

Você pode transferir dados de seu dispositivo Azure Stack Edge pro por meio dos protocolos SMB, NFS ou REST. Para transferir dados para o armazenamento de BLOBs usando as APIs REST, você precisa criar contas de armazenamento de borda em seu Azure Stack Edge pro. 

As contas de armazenamento de borda que você adiciona no dispositivo Azure Stack Edge pro são mapeadas para contas de armazenamento do Azure. Todos os dados gravados nas contas de armazenamento de borda são automaticamente enviados para a nuvem.

Um diagrama detalhando os dois tipos de contas e como os dados fluem de cada uma dessas contas para o Azure é mostrado abaixo:

![Diagrama para contas de armazenamento de BLOBs](media/azure-stack-edge-gpu-manage-storage-accounts/ase-blob-storage.svg)

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Adicionar uma conta de armazenamento do Edge
> * Excluir uma conta de armazenamento de borda


## <a name="add-an-edge-storage-account"></a>Adicionar uma conta de armazenamento do Edge

Para criar uma conta de armazenamento do Edge, execute o seguinte procedimento:

[!INCLUDE [Add an Edge storage account](../../includes/azure-stack-edge-gateway-add-storage-account.md)]

## <a name="delete-an-edge-storage-account"></a>Excluir uma conta de armazenamento de borda

Execute as etapas a seguir para excluir uma conta de armazenamento de borda.

1. Vá para **configuração > contas de armazenamento** em seu recurso. Na lista de contas de armazenamento, selecione a conta de armazenamento que você deseja excluir. Na barra de comandos superior, selecione **Excluir conta de armazenamento**.

    ![Ir para a lista de contas de armazenamento](media/azure-stack-edge-gpu-manage-storage-accounts/delete-edge-storage-account-1.png)

2. Na folha **Excluir conta de armazenamento** , confirme a conta de armazenamento a ser excluída e selecione **excluir**.

    ![Confirmar e excluir conta de armazenamento](media/azure-stack-edge-gpu-manage-storage-accounts/delete-edge-storage-account-2.png)

A lista de contas de armazenamento é atualizada para refletir a exclusão.


## <a name="add-delete-a-container"></a>Adicionar, excluir um contêiner

Você também pode adicionar ou excluir os contêineres para essas contas de armazenamento.

Para adicionar um contêiner, execute as seguintes etapas:

1. Selecione a conta de armazenamento que você deseja gerenciar. Na barra de comandos superior, selecione **+ Adicionar contêiner**.

    ![Selecionar conta de armazenamento para adicionar contêiner](media/azure-stack-edge-gpu-manage-storage-accounts/add-container-1.png)

2. Forneça um nome para seu contêiner. Esse contêiner é criado na sua conta de armazenamento de borda, bem como na conta de armazenamento do Azure mapeada para essa conta. 

    ![Adicionar contêiner de borda](media/azure-stack-edge-gpu-manage-storage-accounts/add-container-2.png)

A lista de contêineres é atualizada para refletir o contêiner recém-adicionado.

![Lista atualizada de contêineres](media/azure-stack-edge-gpu-manage-storage-accounts/add-container-4.png)

Agora você pode selecionar um contêiner nessa lista e selecionar **+ excluir contêiner** na barra de comandos superior. 

![Excluir um contêiner](media/azure-stack-edge-gpu-manage-storage-accounts/add-container-3.png)

## <a name="sync-storage-keys"></a>Sincronizar chaves de armazenamento

Você pode sincronizar as chaves de acesso para as contas de armazenamento de borda (local) em seu dispositivo. 

Para sincronizar a chave de acesso da conta de armazenamento, execute as seguintes etapas:

1. Em seu recurso, selecione a conta de armazenamento que você deseja gerenciar. Na barra de comandos superior, selecione **sincronizar chave de armazenamento**.

    ![Selecionar chave de armazenamento de sincronização](media/azure-stack-edge-gpu-manage-storage-accounts/sync-storage-key-1.png)

2. Quando solicitado a confirmar, selecione **Sim**.

    ![Selecione sincronizar chave de armazenamento 2](media/azure-stack-edge-gpu-manage-storage-accounts/sync-storage-key-2.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba como [Gerenciar usuários usando o portal do Azure](azure-stack-edge-gpu-manage-users.md).
