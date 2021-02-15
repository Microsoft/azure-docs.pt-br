---
title: Azure Stack Edge pro GPU gerenciar usuários | Microsoft Docs
description: Descreve como usar o portal do Azure para gerenciar usuários em sua GPU do Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/05/2021
ms.author: alkohli
ms.openlocfilehash: 0bef344414a9ba27d5808fcd17ed664b7f51bddc
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97915925"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-stack-edge-pro"></a>Use o portal do Azure para gerenciar usuários em seu Azure Stack Edge pro

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Este artigo descreve como gerenciar usuários em seu Azure Stack Edge pro. Você pode gerenciar o Azure Stack Edge pro via portal do Azure ou por meio da interface do usuário da Web local. Use o portal do Azure para adicionar, modificar ou excluir usuários.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Adicionar um usuário
> * Modificar usuário
> * Excluir um usuário

## <a name="about-users"></a>Sobre os usuários

Os usuários podem ter privilégio completo ou somente leitura. Os usuários somente leitura podem exibir apenas os dados de compartilhamento. Os usuários com privilégios totais podem ler dados de compartilhamento, gravar nesses compartilhamentos e modificar ou excluir os dados de compartilhamento.

 - **Usuário com privilégios completo** – um usuário local com acesso completo.
 - **Usuário somente leitura** – um usuário local com acesso somente leitura. Esses usuários estão associados a compartilhamentos que permitem operações somente leitura.

As permissões do usuário são definidas quando o usuário é criado durante a criação do compartilhamento. Eles podem ser modificados usando o explorador de arquivos.


## <a name="add-a-user"></a>Adicionar um usuário

Para adicionar um usuário, siga estas etapas no portal do Azure.

1. Na portal do Azure, vá para o recurso de borda do Azure Stack e vá para **usuários**. Selecione **+ Adicionar usuário** na barra de comandos.

    ![Selecionar Adicionar usuário](media/azure-stack-edge-j-series-manage-users/add-user-1.png)

2. Especifique o nome de usuário e senha do usuário que você deseja adicionar. Confirme a senha e selecione **Adicionar**.

    ![Especificar nome de usuário e senha](media/azure-stack-edge-j-series-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > Estes usuários são reservados pelo sistema e não devem ser usados: Administrador, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount, Convidado.  

3. Uma notificação é mostrada quando a criação de usuário é iniciada e concluída. Após a criação do usuário, na barra de comandos, selecione **Atualizar** para exibir a lista atualizada de usuários.


## <a name="modify-user"></a>Modificar usuário

Você pode alterar a senha associada a um usuário após ele ser criado. Selecione na lista de usuários. Insira e confirme a nova senha. Salve as alterações.

![Modificar usuário](media/azure-stack-edge-j-series-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Excluir um usuário

Para excluir um usuário, siga estas etapas no portal do Azure.


1. Na portal do Azure, vá para o recurso de borda do Azure Stack e vá para **usuários**.

    ![Selecionar o usuário a ser excluído](media/azure-stack-edge-j-series-manage-users/delete-user-1.png)

2. Selecione um usuário na lista de usuários e, em seguida, selecione **Excluir**. Quando receber a solicitação, confirme a exclusão.

    ![Selecione o usuário a ser excluído 2](media/azure-stack-edge-j-series-manage-users/delete-user-2.png)

A lista de usuários é atualizada para refletir o usuário excluído.

![Lista atualizada de usuários](media/azure-stack-edge-j-series-manage-users/delete-user-4.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba como [Gerenciar a largura de banda](azure-stack-edge-j-series-manage-bandwidth-schedules.md).
