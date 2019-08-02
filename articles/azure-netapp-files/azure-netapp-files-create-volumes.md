---
title: Criar um volume do NFS para Azure NetApp Files | Microsoft Docs
description: Descreve como criar um volume do NFS para Azure NetApp Files.
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
ms.date: 7/9/2019
ms.author: b-juche
ms.openlocfilehash: 06733103980086fad0975514ae3489c3652e428a
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846736"
---
# <a name="create-an-nfs-volume-for-azure-netapp-files"></a>Criar um volume NFS para o Azure NetApp Files

O Azure NetApp Files dá suporte a volumes NFS e SMBv3. O consumo de capacidade de um volume conta contra a capacidade provisionada do pool desse volume. Este artigo mostra como criar um volume do NFS. Se você quiser criar um volume SMB, consulte [criar um volume SMB para Azure NetApp files](azure-netapp-files-create-volumes-smb.md). 

## <a name="before-you-begin"></a>Antes de começar 
Você deve já configurou um pool de capacidade.   
[Configurar um pool de capacidade](azure-netapp-files-set-up-capacity-pool.md)   
Uma sub-rede deve ser delegada ao Azure NetApp Files.  
[Delegar uma sub-rede ao Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="create-an-nfs-volume"></a>Criar um volume do NFS

1.  Clique na folha **volumes** da folha pools de capacidade. 

    ![Navegar até volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2.  Clique em **+ Adicionar volume** para criar um volume.  
    A janela criar um volume é exibida.

3.  Na janela criar um volume, clique em **criar** e forneça informações para os seguintes campos:   
    * **Nome do volume**      
        Especifique o nome para o volume que você está criando.   

        Um nome de volume deve ser exclusivo em cada pool de capacidade. Ele precisa ter, pelo menos, três caracteres. Você pode usar qualquer caractere alfanumérico.

    * **Pool de capacidade**  
        Especifique o pool de capacidade no qual você deseja que o volume seja criado.

    * **Cota**  
        Especifique a quantidade de armazenamento lógico que é alocada para o volume.  

        O campo **cota disponível** mostra a quantidade de espaço não utilizado no pool de capacidade escolhido que você pode usar para a criação de um novo volume. O tamanho do novo volume não pode exceder a cota disponível.  

    * **Rede virtual**  
        Especifique a rede virtual (VNet) do Azure da qual você deseja acessar o volume.  

        A VNET especificada precisa ter uma sub-rede delegada ao Azure NetApp Files. O serviço Azure NetApp Files pode ser acessado somente na mesma VNET ou em uma VNET que esteja na mesma região do volume por meio do emparelhamento VNET. Você também pode acessar o volume de sua rede local por meio da rota expressa.   

    * **Sub-rede**  
        Especifique a sub-rede que você deseja usar para o volume.  
        A sub-rede especificada deve ser delegada ao Azure NetApp Files. 
        
        Se você não tiver delegado uma sub-rede, poderá clicar em **Criar novo** na página Criar um volume. Em seguida, na página Criar sub-rede, especifique as informações da sub-rede e selecione **Microsoft.NetApp/volumes** para delegar a sub-rede para o Azure NetApp Files. Em cada vnet, somente uma sub-rede pode ser delegada para Azure NetApp Files.   
 
        ![Criar um volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Criar sub-rede](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Clique em **Protocolo** e selecione **NFS** como o tipo de protocolo para o volume.   
    * Especifique o **caminho do arquivo** que será usado para criar o caminho de exportação para o novo volume. O caminho de exportação é usado para montar e acessar o volume.

        O nome do caminho do arquivo pode conter apenas letras, números e hifens ("-"). O nome deve ter entre 16 e 40 caracteres. 

        O caminho do arquivo deve ser exclusivo em cada assinatura e em cada região. 

    * Opcionalmente, [Configure a política de exportação para o volume do NFS](azure-netapp-files-configure-export-policy.md)

    ![Especificar o protocolo NFS](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

5. Clique em **examinar + criar** para examinar os detalhes do volume.  Em seguida, clique em **criar** para criar o volume do NFS.

    O volume que você criou aparece na página volumes. 
 
    Um volume herda a assinatura, grupo de recursos, atributos de localização de seu pool de capacidade. Para monitorar o status de implantação do volume, você pode usar a guia Notificações.


## <a name="next-steps"></a>Próximas etapas  

* [Montar ou desmontar um volume para máquinas virtuais Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Configurar a política de exportação para um volume do NFS](azure-netapp-files-configure-export-policy.md)
* [Limites de recursos do Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Saiba mais sobre a integração de rede virtual para os serviços do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
