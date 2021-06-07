---
title: Gerenciar usuários do Azure Data Box Gateway | Microsoft Docs
description: Descreve como usar o portal do Azure para gerenciar usuários em seu Azure Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 9e941007ddc27f809de7d43cd33e44c5b521a6bd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96581428"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-data-box-gateway"></a>Use o portal do Azure para gerenciar usuários em seu Azure Data Box Gateway

Este artigo descreve como gerenciar usuários em seu Azure Data Box Gateway. Você pode gerenciar o Azure Data Box Gateway usando o portal do Azure ou a interface do usuário da Web local. Use o portal do Azure para adicionar, modificar ou excluir usuários. 

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Adicionar um usuário
> * Modificar usuário
> * Excluir um usuário

## <a name="about-users"></a>Sobre os usuários

Os usuários podem ter privilégio completo ou somente leitura. Como o nome indica, usuários somente leitura podem apenas exibir os dados do compartilhamento. Usuários com privilégio completo podem ler dados de compartilhamento, gravar nesses compartilhamentos e modificar ou excluir os dados de compartilhamento.

 - **Usuário com privilégios completo** – um usuário local com acesso completo.
 - **Usuário somente leitura** – um usuário local com acesso somente leitura. Esses usuários estão associados a compartilhamentos que permitem operações somente leitura.

As permissões do usuário são definidas quando o usuário é criado durante a criação do compartilhamento. No momento, não há suporte para a modificação de permissões no nível de compartilhamento.

## <a name="add-a-user"></a>Adicionar um usuário

Para adicionar um usuário, siga estas etapas no portal do Azure.

1. Na portal do Azure, vá para o recurso de Gateway do Data Box e, em seguida, navegue até **visão geral**. Clique em **+ Adicionar usuário** na barra de comandos.

    ![Clique em adicionar usuário](media/data-box-gateway-manage-users/add-user-1.png)

2. Especifique o nome de usuário e senha do usuário que você deseja adicionar. Confirme a senha e clique em **Adicionar**.

    ![Clique em Adicionar usuário 2](media/data-box-gateway-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > Estes usuários são reservados pelo sistema e não devem ser usados: Administrador, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount, Convidado.  

3. Você será notificado quando a criação do usuário for iniciada e concluída. Após o usuário ser criado, clique em **Atualizar** na barra de comandos para exibir a lista de usuários atualizada.


## <a name="modify-user"></a>Modificar usuário

Você pode alterar a senha associada a um usuário após ele ser criado. Selecione e clique na lista de usuários. Forneça e confirme a nova senha. Salve as alterações.
 
![Modificar usuário](media/data-box-gateway-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Excluir um usuário

Para excluir um usuário, siga estas etapas no portal do Azure.

1. Selecione e clique em um usuário na lista de usuários e, em seguida, clique em **excluir**.  

   ![Excluir um usuário](media/data-box-gateway-manage-users/delete-user-1.png)

2. Quando receber a solicitação, confirme a exclusão. 

   ![Excluir um usuário 2](media/data-box-gateway-manage-users/delete-user-2.png)

A lista de usuários é atualizada para refletir o usuário excluído.

![Excluir um usuário 3](media/data-box-gateway-manage-users/delete-user-3.png)


## <a name="next-steps"></a>Próximas etapas

- Saiba como [Gerenciar a largura de banda](data-box-gateway-manage-bandwidth-schedules.md).
