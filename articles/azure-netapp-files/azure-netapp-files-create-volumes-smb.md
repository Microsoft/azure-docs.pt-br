---
title: Criar um volume SMB para o Azure NetApp Files | Microsoft Docs
description: Este artigo mostra como criar um volume SMB3 no Azure NetApp Files. Saiba mais sobre os requisitos para conexões de Active Directory e serviços de domínio.
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
ms.topic: how-to
ms.date: 02/16/2021
ms.author: b-juche
ms.openlocfilehash: 91f4f90658281282cdcb01b091bd9c9647d8d702
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100635478"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Criar um volume SMB para o Azure NetApp Files

O Azure NetApp Files dá suporte à criação de volumes usando NFS (NFSv3 e NFSv 4.1), SMB3 ou protocolo duplo (NFSv3 e SMB). O consumo de capacidade de um volume conta contra a capacidade provisionada do pool desse volume. Este artigo mostra como criar um volume SMB3.

## <a name="before-you-begin"></a>Antes de começar 

* Você deve já configurou um pool de capacidade. Consulte [configurar um pool de capacidade](azure-netapp-files-set-up-capacity-pool.md).     
* Uma sub-rede deve ser delegada ao Azure NetApp Files. Consulte [delegar uma sub-rede a Azure NetApp files](azure-netapp-files-delegate-subnet.md).

## <a name="configure-active-directory-connections"></a>Configurar conexões de Active Directory 

Antes de criar um volume SMB, você precisa criar uma conexão Active Directory. Se você não tiver configurado Active Directory conexões para arquivos do Azure NetApp, siga as instruções descritas em [criar e gerenciar conexões de Active Directory](create-active-directory-connections.md).

## <a name="add-an-smb-volume"></a>Adicionar um volume SMB

1. Clique na folha **Volumes** da folha Pools de Capacidade. 

    ![Navegar até Volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Clique em **+ Adicionar volume** para criar um volume.  
    A janela Criar um Volume é exibida.

3. Na janela criar um volume, clique em **criar** e forneça informações para os seguintes campos na guia noções básicas:   
    * **Nome do volume**      
        Especifique o nome para o volume que você está criando.   

        O nome do volume precisa ser exclusivo em cada pool de capacidade. Ele precisa ter, pelo menos, três caracteres. Você pode usar qualquer caractere alfanumérico.   

        Você não pode usar `default` ou `bin` como o nome do volume.

    * **Pool de capacidade**  
        Especifique o pool de capacidade no qual você deseja que o volume seja criado.

    * **Cota**  
        Especifique a quantidade de armazenamento lógico que é alocada para o volume.  

        O campo **cota disponível** mostra a quantidade de espaço não utilizado no pool de capacidade escolhido que você pode usar para a criação de um novo volume. O tamanho do novo volume não pode exceder a cota disponível.  

    * **Taxa de transferência (MiB/S)**   
        Se o volume for criado em um pool de capacidade de QoS manual, especifique a taxa de transferência desejada para o volume.   

        Se o volume for criado em um pool de capacidade de QoS automático, o valor exibido nesse campo será (taxa de transferência de nível de serviço de cota x).   

    * **Rede virtual**  
        Especifique a VNet (rede virtual) do Azure da qual você deseja acessar o volume.  

        A VNET especificada precisa ter uma sub-rede delegada ao Azure NetApp Files. O serviço Azure NetApp Files somente pode ser acessado na mesma VNet ou em uma VNet que esteja na mesma região do volume por meio do emparelhamento VNET. Também é possível acessar o volume na rede local por meio do ExpressRoute.   

    * **Sub-rede**  
        Especifique a sub-rede que você deseja usar para o volume.  
        A sub-rede especificada deve ser delegada ao Azure NetApp Files. 
        
        Se você não tiver delegado uma sub-rede, poderá clicar em **Criar novo** na página Criar um volume. Em seguida, na página Criar sub-rede, especifique as informações da sub-rede e selecione **Microsoft.NetApp/volumes** para delegar a sub-rede para o Azure NetApp Files. Em cada VNet, apenas uma sub-rede pode ser delegada ao Azure NetApp Files.   
 
        ![Criar um volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Criar sub-rede](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Se você quiser aplicar uma política de instantâneo existente ao volume, clique em **Mostrar seção avançada** para expandi-la, especifique se deseja ocultar o caminho do instantâneo e selecione uma política de instantâneo no menu suspenso. 

        Para obter informações sobre como criar uma política de instantâneo, consulte [gerenciar políticas de instantâneo](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

        ![Mostrar seleção avançada](../media/azure-netapp-files/volume-create-advanced-selection.png)

4. Clique em **Protocolo** e preencha as seguintes informações:  
    * Selecione **SMB** como o tipo de protocolo para o volume. 
    * Selecione a conexão **Active Directory** na lista suspensa.
    * Especifique o nome do volume compartilhado em **Nome do compartilhamento**.

    ![Especificar protocolo SMB](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Clique em **Examinar + Criar** para examinar os detalhes do volume.  Clique em **Criar** para criar o volume SMB.

    O volume que você criou aparece na página Volumes. 
 
    Um volume herda a assinatura, grupo de recursos, atributos de localização de seu pool de capacidade. Para monitorar o status de implantação do volume, você pode usar a guia Notificações.

## <a name="control-access-to-an-smb-volume"></a>Controlar o acesso a um volume SMB  

O acesso a um volume SMB é gerenciado por meio de permissões.  

### <a name="share-permissions"></a>Permissões de compartilhamento  

Por padrão, um novo volume tem as permissões de compartilhamento **Todos/Controle Total**. Os membros do grupo Administradores do Domínio podem alterar as permissões de compartilhamento por meio do Gerenciamento do Computador na conta do computador que é usada com o volume do Azure NetApp Files.

![Caminho de montagem do SMB](../media/azure-netapp-files/smb-mount-path.png) 
![Definir permissões de compartilhamento](../media/azure-netapp-files/set-share-permissions.png) 

### <a name="ntfs-file-and-folder-permissions"></a>Permissões de arquivo e pasta NTFS  

Você pode definir permissões para um arquivo ou uma pasta na guia **Segurança** das propriedades do objeto no cliente SMB do Windows.
 
![Definir permissões de arquivo e pasta](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>Próximas etapas  

* [Montar ou desmontar um volume para máquinas virtuais do Windows ou do Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Limites de recursos do Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Perguntas frequentes sobre o SMB](./azure-netapp-files-faqs.md#smb-faqs)
* [Solucionar problemas de volumes de protocolo duplo ou SMB](troubleshoot-dual-protocol-volumes.md)
* [Saiba mais sobre a integração de rede virtual para os serviços do Azure](../virtual-network/virtual-network-for-azure-services.md)
* [Instalar uma nova floresta do Active Directory usando a CLI do Azure](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
