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
ms.date: 12/01/2020
ms.author: b-juche
ms.openlocfilehash: 7c5f18d33efa1838fccab3935054ea19227bc06c
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102845"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Criar um volume SMB para o Azure NetApp Files

O Azure NetApp Files dá suporte à criação de volumes usando NFS (NFSv3 e NFSv 4.1), SMB3 ou protocolo duplo (NFSv3 e SMB). O consumo de capacidade de um volume conta contra a capacidade provisionada do pool desse volume. Este artigo mostra como criar um volume SMB3.

## <a name="before-you-begin"></a>Antes de começar 
Você deve já configurou um pool de capacidade.   
[Configurar um pool de capacidade](azure-netapp-files-set-up-capacity-pool.md)   
Uma sub-rede deve ser delegada ao Azure NetApp Files.  
[Delegar uma sub-rede ao Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Requisitos para conexões do Active Directory

 Você precisa criar conexões do Active Directory antes de criar um volume SMB. Os requisitos de conexões do Active Directory são os seguintes: 

* A conta de administrador usada deve ter a capacidade de criar contas de computador no caminho da UO (unidade organizacional) que você especificará.  

* As portas adequadas devem estar abertas no servidor do Microsoft AD (Active Directory) aplicável.  
    As portas obrigatórias são as seguintes: 

    |     Serviço           |     Porta     |     Protocolo     |
    |-----------------------|--------------|------------------|
    |    Serviços Web do AD    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    N/D       |    Resposta de Eco    |
    |    Kerberos           |    464       |    TCP           |
    |    Kerberos           |    464       |    UDP           |
    |    Kerberos           |    88        |    TCP           |
    |    Kerberos           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    Nome NetBIOS       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    UDP           |
    |    w32time            |    123       |    UDP           |

* A topologia do site para o Active Directory Domain Services de destino precisa atender às diretrizes, em particular a VNet do Azure na qual o Azure NetApp Files foi implantado.  

    O espaço de endereço para a rede virtual em que Azure NetApp Files está implantado deve ser adicionado a um site do Active Directory novo ou existente (onde houver um controlador de domínio que possa ser acessado pelo Azure NetApp Files). 

* Os servidores DNS especificados precisam estar acessíveis na [sub-rede delegada](./azure-netapp-files-delegate-subnet.md) do Azure NetApp Files.  

    Confira as [Diretrizes para planejamento de rede do Azure NetApp Files](./azure-netapp-files-network-topologies.md) para ver as topologias de rede com suporte.

    Os NSGs (grupos de segurança de rede) e os firewalls precisam ter regras configuradas adequadamente para permitir solicitações de tráfego do Active Directory e de DNS. 

* A sub-rede delegada do Azure NetApp Files precisa ser capaz de alcançar todos os controladores de domínio do ADDS (Active Directory Domain Services) no domínio, incluindo todos os controladores de domínio locais e remotos. Caso contrário, poderá ocorrer uma interrupção do serviço.  

    Se você tiver controladores de domínio que não podem ser acessados pela sub-rede delegada do Azure NetApp Files, poderá especificar um site do Active Directory durante a criação da conexão do Active Directory.  O Azure NetApp Files precisa se comunicar somente com controladores de domínio no site em que está o espaço de endereço da sub-rede delegada do Azure NetApp Files.

    Confira [Projetando a topologia do site](/windows-server/identity/ad-ds/plan/designing-the-site-topology) sobre serviços e sites do AD. 
    
* Você pode habilitar a criptografia AES para autenticação do AD marcando a caixa **criptografia AES** na janela [ingressar Active Directory](#create-an-active-directory-connection) . O Azure NetApp Files dá suporte aos tipos de criptografia DES, Kerberos AES 128 e Kerberos AES 256 (do menos seguro para o mais seguro). Se você habilitar a criptografia AES, as credenciais de usuário usadas para ingressar Active Directory devem ter a opção de conta correspondente mais alta habilitada que corresponde aos recursos habilitados para seu Active Directory.    

    Por exemplo, se o Active Directory tiver apenas o recurso AES-128, você deverá habilitar a opção de conta AES-128 para as credenciais do usuário. Se o Active Directory tiver o recurso AES-256, você deverá habilitar a opção de conta AES-256 (que também dá suporte a AES-128). Se o seu Active Directory não tiver nenhum recurso de criptografia Kerberos, o Azure NetApp Files usará DES por padrão.  

    Você pode habilitar as opções de conta nas propriedades do Active Directory usuários e computadores console de gerenciamento Microsoft (MMC):   

    ![Active Directory os usuários e computadores MMC](../media/azure-netapp-files/ad-users-computers-mmc.png)

* O Azure NetApp Files dá suporte à [assinatura LDAP](/troubleshoot/windows-server/identity/enable-ldap-signing-in-windows-server), que permite a transmissão segura de tráfego LDAP entre o serviço de Azure NetApp Files e os [controladores de domínio de Active Directory](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)de destino. Se você estiver seguindo as diretrizes do Microsoft Advisory [ADV190023](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023) para autenticação LDAP, habilite o recurso de assinatura ldap no Azure NetApp files marcando a caixa **assinatura LDAP** na janela [ingressar Active Directory](#create-an-active-directory-connection) . 

    A configuração de [Associação de canal LDAP](https://support.microsoft.com/help/4034879/how-to-add-the-ldapenforcechannelbinding-registry-entry) sozinha não tem nenhum efeito no serviço de Azure NetApp files. No entanto, se você usar a associação de canal LDAP e o LDAP seguro (por exemplo, LDAPs ou `start_tls` ), a criação do volume SMB falhará.

Confira as [Perguntas frequentes sobre SMB](./azure-netapp-files-faqs.md#smb-faqs) do Azure NetApp Files para obter informações adicionais sobre o AD. 

## <a name="decide-which-domain-services-to-use"></a>Decidir quais Serviços de Domínio usar 

O Azure NetApp Files dá suporte ao [ADDS](/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) (Active Directory Domain Services) e ao AADDS (Azure Active Directory Domain Services) em conexões do AD.  Antes de criar uma conexão do AD, você precisa decidir se deseja usar o ADDS ou o AADDS.  

Para obter mais informações, confira [Comparar o Active Directory Domain Services autogerenciado, o Azure Active Directory e o Azure Active Directory Domain Services gerenciado](../active-directory-domain-services/compare-identity-solutions.md). 

### <a name="active-directory-domain-services"></a>Active Directory Domain Services

Você pode usar seu escopo preferencial de [Sites e Serviços do Active Directory](/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) para o Azure NetApp Files. Essa opção permite leituras e gravações em controladores de domínio do ADDS (Active Directory Domain Services) [que podem ser acessados pelo Azure NetApp Files](azure-netapp-files-network-topologies.md). Ele também impede que o serviço se comunique com controladores de domínio que não estão no site especificado de Sites e Serviços do Active Directory. 

Para localizar o nome do seu site ao usar o ADDS, entre em contato com o grupo administrativo em sua organização responsável pelo Active Directory Domain Services. O exemplo a seguir mostra o plug-in Sites e Serviços do Active Directory com o nome do site exibido: 

![Sites e Serviços do Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-services.png)

Ao configurar uma conexão do AD para o Azure NetApp Files, especifique o nome do site no escopo do campo **Nome do Site do AD**.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 

Para obter diretrizes e configurações do AADDS (Azure Active Directory Domain Services), confira a [Documentação do Azure AD Domain Services](../active-directory-domain-services/index.yml).

Outras considerações de AADDS que se aplicam ao Azure NetApp Files: 

* Verifique se a VNet ou a sub-rede em que o AADDS está implantado está na mesma região do Azure da implantação do Azure NetApp Files.
* Se você usar outra VNet na região em que o Azure NetApp Files está implantado, deverá criar um emparelhamento entre as duas VNets.
* O Azure NetApp Files dá suporte aos tipos `user` e `resource forest`.
* Para o tipo de sincronização, você pode selecionar `All` ou `Scoped`.   
    Se você selecionar `Scoped`, verifique se selecionou o grupo do Azure AD correto para acessar compartilhamentos SMB.  Se você não tem certeza, use o tipo de sincronização `All`.
* É obrigatório usar a SKU Enterprise ou Premium. Não há suporte ao SKU Standard.

Ao criar uma conexão do Active Directory, observe as seguintes especificações para o AADDS:

* Você pode encontrar informações sobre **DNS primário**, **DNS secundário** e **Nome de Domínio DNS do AD** no menu AADDS.  
Para servidores DNS, dois endereços IP serão usados para configurar a conexão do Active Directory. 
* O **caminho da unidade organizacional** é `OU=AADDC Computers`.  
Essa configuração é definida em **Conexões do Active Directory** em **Conta do NetApp**:

  ![Caminho da unidade organizacional](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* As credenciais de **Nome de Usuário** podem ser de qualquer usuário que seja membro do grupo do Azure AD **Administradores de DC do Azure AD**.


## <a name="create-an-active-directory-connection"></a>Criar uma conexão do Active Directory

1. Em sua conta do NetApp, clique em **Conexões do Active Directory** e clique em **Ingressar**.  

    ![Conexões do Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. Na janela Ingressar no Active Directory, forneça as seguintes informações com base nos serviços de domínio que você deseja usar:  

    Para obter informações específicas sobre os serviços de domínio que você usa, confira [Decidir quais Serviços de Domínio usar](#decide-which-domain-services-to-use). 

    * **DNS primário**  
        É o DNS exigido para as operações de autenticação SMB e de ingresso no domínio do Active Directory. 
    * **DNS secundário**   
        É o servidor DNS secundário que garante os serviços de nome redundantes. 
    * **Nome de Domínio DNS do AD**  
        É o nome de domínio do Active Directory Domain Services em que você deseja ingressar.
    * **Nome do site do AD**  
        Esse é o nome do site ao qual a descoberta do controlador de domínio será limitada. Isso deve corresponder ao nome do site em Active Directory sites e serviços.
    * **Prefixo do Servidor SMB (conta do computador)**  
        É o prefixo de nomenclatura da conta do computador no Active Directory que o Azure NetApp Files usará para criar novas contas.

        Por exemplo, se o padrão de nomenclatura que sua organização usa para servidores de arquivos for NAS-01, NAS-02..., NAS-045, você inserirá "NAS" como prefixo. 

        O serviço criará contas de computador adicionais no Active Directory conforme a necessidade.

        > [!IMPORTANT] 
        > A renomeação do prefixo do servidor SMB depois da criação da conexão do Active Directory a interrompe. Você precisará montar novamente os compartilhamentos SMB existentes depois de renomear o prefixo do servidor SMB.

    * **Caminho da unidade organizacional**  
        Esse é o caminho LDAP para a UO (unidade organizacional) no qual as contas de computador do servidor SMB serão criadas. Ou seja, OU=second level, OU=first level. 

        Se estiver usando o Azure NetApp Files com o Azure Active Directory Domain Services, o caminho da unidade organizacional será `OU=AADDC Computers` quando você configurar o Active Directory para sua conta do NetApp.

    ![Ingressar no Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

    * **Criptografia AES**   
        Marque esta caixa de seleção para habilitar a criptografia AES para um volume SMB. Consulte [requisitos para conexões de Active Directory](#requirements-for-active-directory-connections) para requisitos. 

        ![Active Directory criptografia AES](../media/azure-netapp-files/active-directory-aes-encryption.png)

        O recurso de **criptografia AES** está atualmente em versão prévia. Se esta for a primeira vez que você usa esse recurso, registre o recurso antes de usá-lo: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```

        Verifique o status do registro do recurso: 

        > [!NOTE]
        > O **RegistrationState** pode estar no `Registering` estado de até 60 minutos antes da alteração para `Registered` . Aguarde até que o status seja **registrado** antes de continuar.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```
        
        Você também pode usar [comandos de CLI do Azure](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` e `az feature show` para registrar o recurso e exibir o status do registro. 

    * **Assinatura LDAP**   
        Marque esta caixa de seleção para habilitar a assinatura LDAP. Essa funcionalidade permite pesquisas LDAP seguras entre o serviço Azure NetApp Files e os [controladores de domínio Active Directory Domain Services](/windows/win32/ad/active-directory-domain-services)especificados pelo usuário. Para obter mais informações, consulte [ADV190023 | Diretrizes da Microsoft para habilitar a assinatura LDAP e a associação de canal LDAP](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).  

        ![Assinatura de LDAP Active Directory](../media/azure-netapp-files/active-directory-ldap-signing.png) 

        O recurso de **assinatura LDAP** está atualmente em versão prévia. Se esta for a primeira vez que você usa esse recurso, registre o recurso antes de usá-lo: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```

        Verifique o status do registro do recurso: 

        > [!NOTE]
        > O **RegistrationState** pode estar no `Registering` estado de até 60 minutos antes da alteração para `Registered` . Aguarde até que o status seja **registrado** antes de continuar.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```
        
        Você também pode usar [comandos de CLI do Azure](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` e `az feature show` para registrar o recurso e exibir o status do registro. 

     * **Usuários da política de backup**  
        Você pode incluir outras contas que exigem privilégios elevados na conta de computador criada para uso com o Azure NetApp Files. As contas especificadas terão permissão para alterar as permissões de NTFS no nível do arquivo ou da pasta. Por exemplo, você pode especificar uma conta de serviço sem privilégios usada para migrar dados para um compartilhamento de arquivos SMB no Azure NetApp Files.  

        ![Active Directory usuários da política de backup](../media/azure-netapp-files/active-directory-backup-policy-users.png)

        O recurso **usuários da política de backup** está atualmente em visualização. Se esta for a primeira vez que você usa esse recurso, registre o recurso antes de usá-lo: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```

        Verifique o status do registro do recurso: 

        > [!NOTE]
        > O **RegistrationState** pode estar no `Registering` estado de até 60 minutos antes da alteração para `Registered` . Aguarde até que o status seja **registrado** antes de continuar.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```
        
        Você também pode usar [comandos de CLI do Azure](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` e `az feature show` para registrar o recurso e exibir o status do registro. 

    * Credenciais, incluindo seu **nome de usuário** e sua **senha**

        ![Credenciais do Active Directory](../media/azure-netapp-files/active-directory-credentials.png)

3. Clique em **Ingressar**.  

    A conexão do Active Directory que você criou é exibida.

    ![Conexões Active Directory criadas](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

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
* [Solucionar problemas de volumes SMB ou de protocolo duplo](troubleshoot-dual-protocol-volumes.md)
* [Saiba mais sobre a integração de rede virtual para os serviços do Azure](../virtual-network/virtual-network-for-azure-services.md)
* [Instalar uma nova floresta do Active Directory usando a CLI do Azure](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
