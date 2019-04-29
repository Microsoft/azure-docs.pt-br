---
title: Gerenciar instantâneos por meio do Azure NetApp Files | Microsoft Docs
description: Descreve como criar um instantâneo para um volume ou restaurar de um instantâneo para um novo volume por meio do Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: b-juche
ms.openlocfilehash: 01387d0c219c86f33762b9c3fbf9f81cf04b4455
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61086783"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Gerenciar instantâneos por meio do Azure NetApp Files

Você pode usar o Azure NetApp Files para criar um instantâneo sob demanda para um volume ou restaurar de um instantâneo para um novo volume.

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Criar um instantâneo sob demanda para um volume

Você pode criar instantâneos somente sob demanda. Políticas de instantâneo não são compatíveis atualmente.

1.  Na folha Volume, clique em **Instantâneos**.

    ![Navegar para instantâneos](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  Clique em **+ Adicionar instantâneo** para criar um instantâneo sob demanda para um volume.

    ![Adicionar instantâneo](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  Na janela Novo Instantâneo, forneça um nome para o novo instantâneo que você está criando.   

    ![Novo instantâneo](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Clique em **OK**. 

## <a name="restore-a-snapshot-to-a-new-volume"></a>Restaurar um instantâneo para um novo volume

No momento, você pode restaurar um instantâneo somente para um novo volume. 
1. Vá para a folha **Gerenciar Instantâneos** da folha do Volume para exibir a lista de instantâneos. 
2. Selecione um instantâneo para restaurar.  
3. Clique com o botão direito do mouse no nome do instantâneo e selecione **Restaurar para o novo volume** da opção de menu.  

    ![Restaurar instantâneo para o novo volume](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

4. Na janela Novo Volume, forneça informações para o novo volume:  
    * **Nome**   
        Especifique o nome para o volume que você está criando.  
        
        O nome precisa ser exclusivo em um grupo de recursos. Ele precisa ter, pelo menos, três caracteres.  Deve usar qualquer caractere alfanumérico.

    * **Caminho do arquivo**     
        Especifique o caminho do arquivo que será usado para criar o caminho de exportação para o novo volume. O caminho de exportação é usado para montar e acessar o volume.   
        
        Um destino de montagem é o ponto de extremidade do endereço IP do serviço NFS. Eles são gerados automaticamente.   
        
        O nome do caminho do arquivo pode conter apenas letras, números e hifens ("-"). O nome deve ter entre 16 e 40 caracteres. 

    * **Cota**  
        Especifique a quantidade de armazenamento lógico que é alocada para o volume.  

        O campo **cota disponível** mostra a quantidade de espaço não utilizado no pool de capacidade escolhido que você pode usar para a criação de um novo volume. O tamanho do novo volume não pode exceder a cota disponível.

    *   **Rede virtual**  
        Especifique a rede virtual (VNet) do Azure da qual você deseja acessar o volume.  
        A VNET especificada precisa ter uma sub-rede delegada ao Azure NetApp Files. Você pode acessar o Azure NetApp Files somente na mesma VNET ou em uma VNET que esteja na mesma região do volume por meio do emparelhamento VNET. Você pode acessar o volume da sua rede local através da rota expressa. 

    * **Sub-rede**  
        Especifique a sub-rede que você deseja usar para o volume.  
        A sub-rede especificada precisa ser delegada ao serviço Azure NetApp Files. Crie uma sub-rede selecionando **Criar nova** no campo Sub-rede.  
   <!--
    ![Restored new volume](../media/azure-netapp-files/azure-netapp-files-snapshot-new-volume.png) 
   -->

5. Clique em **OK**.   
    O novo volume para o qual o instantâneo é restaurado é exibido na folha Volumes.

## <a name="next-steps"></a>Próximas etapas

[Compreender a hierarquia de armazenamento do Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)