---
title: Criar um volume do NFS para Azure NetApp Files | Microsoft Docs
description: Este artigo mostra como criar um volume do NFS no Azure NetApp Files. Saiba mais sobre as considerações, como qual versão usar e as práticas recomendadas.
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
ms.date: 09/24/2020
ms.author: b-juche
ms.openlocfilehash: 2cc9d3e0fb711a0662852ce4f2c5a08dc626f246
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96854726"
---
# <a name="create-an-nfs-volume-for-azure-netapp-files"></a>Criar um volume NFS para o Azure NetApp Files

O Azure NetApp Files dá suporte à criação de volumes usando NFS (NFSv3 e NFSv 4.1), SMB3 ou protocolo duplo (NFSv3 e SMB). O consumo de capacidade de um volume conta contra a capacidade provisionada do pool desse volume. Este artigo mostra como criar um volume do NFS. 

## <a name="before-you-begin"></a>Antes de começar 
* Você deve já configurou um pool de capacidade.  
    Consulte [configurar um pool de capacidade](azure-netapp-files-set-up-capacity-pool.md).   
* Uma sub-rede deve ser delegada ao Azure NetApp Files.  
    Consulte [delegar uma sub-rede a Azure NetApp files](azure-netapp-files-delegate-subnet.md).

## <a name="considerations"></a>Considerações 

* Decidindo qual versão do NFS usar  
  O NFSv3 pode lidar com uma ampla variedade de casos de uso e é normalmente implantado na maioria dos aplicativos empresariais. Você deve validar qual versão (NFSv3 ou NFSv 4.1) seu aplicativo requer e criar seu volume usando a versão apropriada. Por exemplo, se você usar o [Apache ActiveMQ](https://activemq.apache.org/shared-file-system-master-slave), o bloqueio de arquivo com nfsv 4.1 será recomendado em relação a NFSv3. 

* Segurança  
  O suporte para bits de modo UNIX (leitura, gravação e execução) está disponível para NFSv3 e NFSv 4.1. O acesso no nível da raiz é necessário no cliente NFS para montar volumes NFS.

* Usuário/grupo local e suporte LDAP para NFSv 4.1  
  Atualmente, o NFSv 4.1 dá suporte ao acesso raiz somente a volumes. Consulte [Configurar o domínio padrão do nfsv 4.1 para Azure NetApp files](azure-netapp-files-configure-nfsv41-domain.md). 

## <a name="best-practice"></a>Melhor prática

* Verifique se você está usando as instruções de montagem adequadas para o volume.  Confira [montar ou desmontar um volume para máquinas virtuais Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).

* O cliente NFS deve estar na mesma VNet ou VNet emparelhada que o volume Azure NetApp Files. Há suporte para a conexão de fora da VNet; no entanto, ele apresentará latência adicional e reduzirá o desempenho geral.

* Verifique se o cliente NFS está atualizado e executando as atualizações mais recentes para o sistema operacional.

## <a name="create-an-nfs-volume"></a>Criar um volume NFS

1.  Clique na folha **Volumes** da folha Pools de Capacidade. Clique em **+ Adicionar volume** para criar um volume. 

    ![Navegar até Volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  Na janela criar um volume, clique em **criar** e forneça informações para os seguintes campos na guia noções básicas:   
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

        A VNET especificada precisa ter uma sub-rede delegada ao Azure NetApp Files. O serviço Azure NetApp Files pode ser acessado somente na mesma VNET ou em uma VNET que esteja na mesma região do volume por meio do emparelhamento VNET. Também é possível acessar o volume na rede local por meio do ExpressRoute.   

    * **Sub-rede**  
        Especifique a sub-rede que você deseja usar para o volume.  
        A sub-rede especificada deve ser delegada ao Azure NetApp Files. 
        
        Se você não tiver delegado uma sub-rede, poderá clicar em **Criar novo** na página Criar um volume. Em seguida, na página Criar sub-rede, especifique as informações da sub-rede e selecione **Microsoft.NetApp/volumes** para delegar a sub-rede para o Azure NetApp Files. Em cada vnet, somente uma sub-rede pode ser delegada para Azure NetApp Files.   
 
        ![Criar um volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Criar sub-rede](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Se você quiser aplicar uma política de instantâneo existente ao volume, clique em **Mostrar seção avançada** para expandi-la, especifique se deseja ocultar o caminho do instantâneo e selecione uma política de instantâneo no menu suspenso. 

        Para obter informações sobre como criar uma política de instantâneo, consulte [gerenciar políticas de instantâneo](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

        ![Mostrar seleção avançada](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. Clique em **Protocolo** e, em seguida, conclua as seguintes ações:  
    * Selecione **NFS** como o tipo de protocolo para o volume.   
    * Especifique o **caminho do arquivo** que será usado para criar o caminho de exportação para o novo volume. O caminho de exportação é usado para montar e acessar o volume.

        O nome do caminho do arquivo pode conter apenas letras, números e hifens ("-"). O nome deve ter entre 16 e 40 caracteres. 

        O caminho do arquivo deve ser exclusivo em cada assinatura e em cada região. 

    * Selecione a versão do NFS (**NFSv3** ou **NFSv4.1**) para o volume.  

    * Se você estiver usando o NFSv 4.1, indique se deseja habilitar a criptografia **Kerberos** para o volume.  

        Configurações adicionais serão necessárias se você usar o Kerberos com o NFSv 4.1. Siga as instruções em [Configurar criptografia Kerberos do nfsv 4.1](configure-kerberos-encryption.md).

    * Opcionalmente, [Configure a política de exportação para o volume do NFS](azure-netapp-files-configure-export-policy.md).

    ![Especificar o protocolo NFS](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

4. Clique em **Examinar + Criar** para examinar os detalhes do volume.  Em seguida, clique em **criar** para criar o volume.

    O volume que você criou aparece na página Volumes. 
 
    Um volume herda a assinatura, grupo de recursos, atributos de localização de seu pool de capacidade. Para monitorar o status de implantação do volume, você pode usar a guia Notificações.


## <a name="next-steps"></a>Próximas etapas  

* [Configurar o domínio padrão do NFSv4.1 para o Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Configurar a criptografia Kerberos do NFSv4.1](configure-kerberos-encryption.md)
* [Montar ou desmontar um volume para máquinas virtuais do Windows ou do Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Configurar a política de exportação para um volume do NFS](azure-netapp-files-configure-export-policy.md)
* [Limites de recursos do Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Saiba mais sobre a integração de rede virtual para os serviços do Azure](../virtual-network/virtual-network-for-azure-services.md)