---
title: Criar um volume para o Azure NetApp Files
description: Descreve como criar um volume para Azure NetApp Files.
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
ms.date: 4/12/2019
ms.author: b-juche
ms.openlocfilehash: fc748ee993855c77f25f9b115ea472df4281acec
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764368"
---
# <a name="create-a-volume-for-azure-netapp-files"></a>Criar um volume para Azure NetApp Files

Cada pool de capacidade pode ter no máximo 500 volumes. O consumo de capacidade de um volume conta contra a capacidade provisionada do pool desse volume. Os arquivos NetApp do Azure dá suporte a volumes NFS e SMBv3. 

## <a name="before-you-begin"></a>Antes de começar 
Você deve já configurou um pool de capacidade.   
[Configurar um pool de capacidade](azure-netapp-files-set-up-capacity-pool.md)   
Uma sub-rede deve ser delegada ao Azure NetApp Files.  
[Delegar uma sub-rede ao Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="create-an-nfs-volume"></a>Criar um volume do NFS

1.  Clique o **Volumes** folha na folha de Pools de capacidade. 

    ![Navegue até os Volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2.  Clique em **+ Adicionar volume** para criar um volume.  
    Criar que uma janela de Volume é exibida.

3.  Em criar uma janela de Volume, clique em **criar** e forneça informações para os seguintes campos:   
    * **Nome do volume**      
        Especifique o nome para o volume que você está criando.   

        O nome precisa ser exclusivo em um grupo de recursos. Ele precisa ter, pelo menos, três caracteres.  Você pode usar qualquer caractere alfanumérico.

    * **Pool de capacidade**  
        Especifica o pool de capacidade, onde você deseja que o volume a ser criado.

    * **Cota**  
        Especifique a quantidade de armazenamento lógico que é alocada para o volume.  

        O campo **cota disponível** mostra a quantidade de espaço não utilizado no pool de capacidade escolhido que você pode usar para a criação de um novo volume. O tamanho do novo volume não pode exceder a cota disponível.  

    * **Rede virtual**  
        Especifique a rede virtual (VNet) do Azure da qual você deseja acessar o volume.  

        A VNET especificada precisa ter uma sub-rede delegada ao Azure NetApp Files. O serviço Azure NetApp Files pode ser acessado somente na mesma VNET ou em uma VNET que esteja na mesma região do volume por meio do emparelhamento VNET. Você também pode acessar o volume da sua rede local através da rota expressa.   

    * **Sub-rede**  
        Especifique a sub-rede que você deseja usar para o volume.  
        A sub-rede especificada deve ser delegada ao Azure NetApp Files. 
        
        Se você não tiver delegado uma sub-rede, poderá clicar em **Criar novo** na página Criar um volume. Em seguida, na página Criar sub-rede, especifique as informações da sub-rede e selecione **Microsoft.NetApp/volumes** para delegar a sub-rede para o Azure NetApp Files. Em cada rede virtual, apenas uma sub-rede pode ser delegada aos arquivos do Azure NetApp.   
 
        ![Criar um volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Criar sub-rede](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Clique em **protocolo**, em seguida, selecione **NFS** como o tipo de protocolo para o volume.   
    * Especifique o **caminho do arquivo** que será usado para criar o caminho de exportação para o novo volume. O caminho de exportação é usado para montar e acessar o volume.

        O nome do caminho do arquivo pode conter apenas letras, números e hifens ("-"). O nome deve ter entre 16 e 40 caracteres.  

    * Opcionalmente, [configurar política de exportação para o volume do NFS](azure-netapp-files-configure-export-policy.md)

    ![Especifique o protocolo NFS](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

5. Clique em **revisar + criar** para examinar os detalhes de volume.  Em seguida, clique em **criar** para criar o volume do NFS.

    O volume que você criou aparece na página de Volumes. 
 
    Um volume herda a assinatura, grupo de recursos, atributos de localização de seu pool de capacidade. Para monitorar o status de implantação do volume, você pode usar a guia Notificações.

## <a name="create-an-smb-volume"></a>Criar um volume de SMB

Os arquivos NetApp do Azure dá suporte a volumes SMBv3. Você precisa criar as conexões do Active Directory antes de adicionar um volume SMB. 

### <a name="create-an-active-directory-connection"></a>Criar uma conexão do Active Directory

1. Em sua conta do NetApp, clique em **conexões do Active Directory**, em seguida, clique em **ingressar**.  

    ![Conexões do Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. Na janela de junção do Active Directory, forneça as seguintes informações:

    * **DNS primário**   
        Isso é o endereço IP do controlador de domínio para o preferencial Active Directory Domain Services para uso com arquivos do Azure NetApp. 
    * **DNS secundário**  
        Isso é o endereço IP do controlador de domínio para o secundário Active Directory Domain Services para uso com arquivos do Azure NetApp. 
    * **Domínio**  
        Isso é o nome de domínio do seu Active Directory Domain Services que você deseja ingressar.
    * **Prefixo do SMB (conta de computador) do servidor**  
        Este é o prefixo de nomenclatura para a conta do computador no Active Directory que os arquivos NetApp Azure usará para a criação de novas contas.

        Por exemplo, se o padrão de nomeação que sua organização usa servidores de arquivos for NAS-01, 02 NAS..., NAS 045, em seguida, você digitaria "NAS" para o prefixo. 

        O serviço criará contas de computador adicionais no Active Directory, conforme necessário.

    * **Caminho de unidade organizacional**  
        Esse é o caminho LDAP para a unidade organizacional (UO) em que as contas de computador do servidor SMB serão criadas. Ou seja, UO = segundo nível, UO = primeiro nível. 
    * As credenciais, incluindo sua **nome de usuário** e **senha**

    ![Associar o Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Clique em **Ingressar**.  

    A conexão do Active Directory que você criou é exibido.

    ![Conexões do Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

### <a name="add-an-smb-volume"></a>Adicionar um volume SMB

1. Clique o **Volumes** folha na folha de Pools de capacidade. 

    ![Navegue até os Volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Clique em **+ Adicionar volume** para criar um volume.  
    Criar que uma janela de Volume é exibida.

3. Em criar uma janela de Volume, clique em **criar** e forneça informações para os seguintes campos:   
    * **Nome do volume**      
        Especifique o nome para o volume que você está criando.   

        O nome precisa ser exclusivo em um grupo de recursos. Ele precisa ter, pelo menos, três caracteres.  Você pode usar qualquer caractere alfanumérico.

    * **Caminho do arquivo**  
        Especifique o caminho do arquivo que será usado para criar o caminho de exportação para o novo volume. O caminho de exportação é usado para montar e acessar o volume.   
     
        O nome do caminho do arquivo pode conter apenas letras, números e hifens ("-"). O nome deve ter entre 16 e 40 caracteres.  

    * **Pool de capacidade**  
        Especifica o pool de capacidade, onde você deseja que o volume a ser criado.

    * **Cota**  
        Especifique a quantidade de armazenamento lógico que é alocada para o volume.  

        O campo **cota disponível** mostra a quantidade de espaço não utilizado no pool de capacidade escolhido que você pode usar para a criação de um novo volume. O tamanho do novo volume não pode exceder a cota disponível.  

    * **Rede virtual**  
        Especifique a rede virtual (VNet) do Azure da qual você deseja acessar o volume.  

        A VNET especificada precisa ter uma sub-rede delegada ao Azure NetApp Files. O serviço Azure NetApp Files pode ser acessado somente na mesma VNET ou em uma VNET que esteja na mesma região do volume por meio do emparelhamento VNET. Você também pode acessar o volume da sua rede local através da rota expressa.   

    * **Sub-rede**  
        Especifique a sub-rede que você deseja usar para o volume.  
        A sub-rede especificada deve ser delegada ao Azure NetApp Files. 
        
        Se você não tiver delegado uma sub-rede, poderá clicar em **Criar novo** na página Criar um volume. Em seguida, na página Criar sub-rede, especifique as informações da sub-rede e selecione **Microsoft.NetApp/volumes** para delegar a sub-rede para o Azure NetApp Files. Em cada rede virtual, apenas uma sub-rede pode ser delegada aos arquivos do Azure NetApp.   
 
        ![Criar um volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Criar sub-rede](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Clique em **protocolo** e conclua as seguintes informações:  
    * Selecione **SMB** como o tipo de protocolo para o volume. 
    * Selecione suas **do Active Directory** conexão na lista suspensa.
    * Especifique o nome do volume compartilhado na **nome do compartilhamento**.

    ![Especifique o protocolo SMB](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Clique em **revisar + criar** para examinar os detalhes de volume.  Em seguida, clique em **criar** para criar o volume SMB.

    O volume que você criou aparece na página de Volumes. 
 
    Um volume herda a assinatura, grupo de recursos, atributos de localização de seu pool de capacidade. Para monitorar o status de implantação do volume, você pode usar a guia Notificações.

## <a name="next-steps"></a>Próximos passos  

* [Montar ou desmontar um volume para máquinas virtuais Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Configurar a política de exportação para um volume do NFS](azure-netapp-files-configure-export-policy.md)
* [Saiba mais sobre a integração de rede virtual para os serviços do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
