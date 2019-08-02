---
title: Criar e configurar clusters Enterprise Security Package no Azure HDInsight
description: Saiba como criar e configurar clusters Enterprise Security Package no Azure HDInsight
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/09/2019
ms.openlocfilehash: 8da50757182609402ecb035b6f3e92959758ef46
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442306"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Criar e configurar clusters Enterprise Security Package no Azure HDInsight

O Enterprise Security Package para o Azure HDInsight fornece acesso a autenticação baseada em Active Directory, suporte a vários usuários e controle de acesso baseado em função para seus clusters de Apache Hadoop no Azure. Os clusters de ESP do HDInsight permitem que as organizações, que aderem às políticas de segurança corporativa, processem dados confidenciais com segurança.

O objetivo deste guia é configurar corretamente os recursos necessários para que os usuários locais possam entrar em um cluster HDInsight habilitado para ESP. Este artigo percorre as etapas necessárias para criar um Enterprise Security Package cluster do Azure HDInsight habilitado. As etapas abrangem a criação de uma VM IaaS do Windows com Active Directory & DNS (serviços de nomes de domínio) habilitados. Esse servidor atuará como uma substituição para o seu ambiente local **real** e permitirá que você prossiga com as etapas de instalação e configuração para que você possa repeti-las posteriormente em seu próprio ambiente. Este guia também ajudará você a criar um ambiente de identidade híbrida usando a sincronização de hash de senha com o Azure Active Directory.

Este guia destina-se a complementar o [uso de Enterprise Security Package no HDInsight](apache-domain-joined-architecture.md)

Antes de usar esse processo em seu próprio ambiente, a instalação Active Directory e o DNS (serviços de nomes de domínio). Além disso, habilite Azure Active Directory e sincronize contas de usuário locais para Azure Active Directory.

![diagrama da arquitetura](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image002.png)

## <a name="create-on-premises-environment"></a>Criar um ambiente local

Visão geral: Nesta seção, você usará um modelo de implantação rápida do Azure para criar novas VMs, configurar o DNS (serviços de nomes de domínio) e uma nova floresta do AD.

1. Vá para [criar uma VM do Azure com uma nova floresta do AD](https://azure.microsoft.com/resources/templates/active-directory-new-domain/), para exibir o modelo de implantação rápida.

1. Clique em **implantar no Azure**.
1. Entre em sua assinatura do Azure.
1. Na tela **criar uma VM do Azure com uma nova floresta do AD** , conclua as seguintes etapas:
    1. Selecione a assinatura em que você deseja que os recursos sejam implantados na lista suspensa **assinatura** .
    1. Selecione **criar novo** ao lado de **grupo de recursos** e insira o nome **OnPremADVRG**
    1. Insira os seguintes detalhes para o restante dos campos de modelo:

        * **Localização**: EUA Central
        * **Nome de Usuário do Administrador**: HDIFabrikamAdmin
        * **Senha do administrador**: < YOUR_PASSWORD >
        * **Domínio**: HDIFabrikam.com
        * **Prefixo DNS**: hdifabrikam

        ![Modelo criar VM do Azure e floresta do AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

    1. Clique em **comprar**
    1. Monitore a implantação e aguarde sua conclusão.
    1. Confirme se os recursos foram criados no grupo `OnPremADVRG`de recursos correto.

## <a name="configure-users-and-groups-for-cluster-access"></a>Configurar usuários e grupos para acesso ao cluster

Visão geral: Nesta seção, você criará os usuários que terão acesso ao cluster HDInsight no final deste guia.

1. Conecte-se ao controlador de domínio usando Área de Trabalho Remota.
    1. Se você usou o modelo mencionado no início, o controlador de domínio é uma VM chamada **adVM** no grupo `OnPremADVRG` de recursos.
    1. Vá para o portal do Azure > **grupos** > de recursos**OnPremADVRG** > **adVM** > **conectar**.
    1. Clique na guia **RDP** e, em seguida, clique em **baixar arquivo RDP**.
    1. Salve o arquivo em seu computador e abra-o.
    1. Quando solicitado a fornecer credenciais, use `HDIFabrikam\HDIFabrikamAdmin` como o nome de usuário e, em seguida, insira a senha que você escolheu para a conta de administrador.

1. Quando a sessão de Área de Trabalho Remota for aberta na VM do controlador de domínio, inicie **Active Directory usuários e computadores** no painel **Gerenciador do servidor** . Clique em **ferramentas** no canto superior direito e, em seguida, **Active Directory usuários e computadores** na lista suspensa.

    ![Gerenciamento de Active Directory Gerenciador do Servidor Open](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Crie dois novos usuários, **HDIAdmin**, **HDIUser**. Esses dois usuários serão usados para entrar em clusters HDInsight.

    1. Na tela **Active Directory usuários e computadores** , clique em **ação** > **novo** > **usuário**.

        ![Criar novo Active Directory usuário](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-user.png)

    1. Na tela **novo objeto – usuário** , insira `HDIUser` como o **nome de logon do usuário** e clique em **Avançar**.

        ![Criar primeiro usuário administrador](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image020.png)

    1. No pop-up que aparece, insira a senha desejada para a nova conta. Marque a caixa que informa que a **senha nunca expira**. HDIClick **OK**.
    1. Clique em **concluir** para criar a nova conta.
    1. Crie outro usuário `HDIAdmin`.

        ![Criar segundo usuário administrador](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image024.png)

1. Na tela **Active Directory usuários e computadores** , clique em **ação** > **novo** > **grupo**. Crie `HDIUserGroup` como um novo grupo.

    ![Criar novo grupo de Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-group.png)

    ![criar novo group2](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image028.png)

1. Adicione o **HDIUser** criado na etapa anterior ao **HDIUserGroup** como um membro.

    1. Clique com o botão direito do mouse em **HDIUserGroup** e clique em **Propriedades**.
    1. Vá para a guia **Membros** e clique em **Adicionar**.
    1. Digite `HDIUser` na caixa rotulada **Insira os nomes de objeto a serem selecionados** e clique em **OK**.
    1. Repita as etapas anteriores para a outra conta`HDIAdmin`

        ![adicionar membros ao grupo](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

Agora você criou seu ambiente de Active Directory, juntamente com dois usuários e um grupo de usuários para acessar o cluster HDInsight.

Esses usuários serão sincronizados com o Azure AD.

### <a name="create-a-new-azure-active-directory"></a>Criar um novo Azure Active Directory

1. Entre no Portal do Azure.
1. Clique em **criar um recurso** e digite **diretório**. Selecione **Azure Active Directory** > **criar**.
1. Insira **HDIFabrikam** em **nome da organização**.
1. Insira **HDIFabrikamoutlook** em **nome de domínio inicial**.
1. Clique em **Criar**.
1. À esquerda no portal do Azure, clique em **Azure Active Directory**.
1. Se necessário, clique em **alternar diretório** para alterar para o novo diretório que você criou **HDIFabrikamoutlook**.
1. Em **gerenciar** , clique em **nomes** > de domínio personalizados**Adicionar domínio personalizado**.
1. Insira **HDIFabrikam.com** em **nome de domínio personalizado** e clique em **Adicionar domínio**.

![criar um novo Azure Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

![criar um novo domínio personalizado](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

## <a name="configure-your-azure-ad-tenant"></a>Configurar seu locatário do Azure AD

Visão geral: Agora você configurará seu locatário do Azure AD para que você possa sincronizar usuários e grupos do AD local para a nuvem.

1. Crie um administrador de locatários do AD.
    1. Entre no portal do Azure e selecione seu locatário do Azure AD **HDIFabrikam**
    1. Selecione **usuários** em **gerenciar** e em **novo usuário**.
    1. Insira os seguintes detalhes para o novo usuário:

        * Nome: fabrikamazureadmin
        * Nome de usuário:fabrikamazureadmin@hdifabrikam.com
        * Senha: uma senha segura de sua escolha

    1. Clique na seção **grupos** , procure administradores do **AAD DC**e clique em **selecionar**.

        ![Grupos](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image038.png)

    1. Clique na seção **função de diretório** e selecione **administrador global** no lado direito. Clique em **OK**.

        ![Função de diretório](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image040.png)

    1. Insira uma senha para o usuário. Clique em **Criar**.

1. Se desejar alterar a senha do usuário <fabrikamazureadmin@hdifabrikam.com>recém-criado. Entre no portal do Azure usando a identidade e, em seguida, você será solicitado a alterar a senha.

## <a name="sync-on-premises-users-to-azure-ad"></a>Sincronizar usuários locais com o Azure AD

### <a name="download-and-install-microsoft-azure-active-directory-connect"></a>Baixar e instalar o Microsoft Azure Active Directory Connect

1. [Baixar o Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).

1. Instale o Microsoft Azure Active Directory Connect no controlador de domínio.
    1. Abra o executável que você baixou na etapa anterior e concorde com os termos de licença. Clique em **Continue**.

        ![Azure AD Connect](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image052.png)

    1. Clique em **usar configurações expressas** e conclua a instalação.

        ![Usar configurações expressas](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image054.png)

### <a name="configure-sync-with-on-premises-domain-controller"></a>Configurar a sincronização com o controlador de domínio local

1. Na tela **conectar ao Azure ad** , insira o nome de usuário e a senha do administrador global do Azure AD. Clique em **Avançar**. Esse é o nome `fabrikamazureadmin@hdifabrikam.com` de usuário que você criou ao configurar seu locatário do AD.
    ![Conecte-se ao Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image058.png)
1. Na tela **conectar a Active Directory Domain Services** , insira o nome de usuário e a senha de uma conta de administrador corporativo. Clique em **Avançar**. Esse é o nome `HDIFabrikam\HDIFabrikamAdmin` de usuário e sua senha correspondente que você criou anteriormente.

   ![Conectar-se ao Active Directory Domain Services](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image060.png)
1. Na página de **configuração de entrada do Azure ad** , clique em **Avançar**.
    ![Configuração de entrada do Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image062.png)
1. Na tela pronto para configurar, clique em **instalar**.
    ![install](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image064.png)
1. Quando a tela **configuração concluída** for exibida, clique em **sair**.
    ![configuração concluída](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image078.png)

1. Após a conclusão da sincronização, confirme se os usuários que você criou no Active Directory de IAAS são sincronizados com Azure Active Directory.
    1. Entre no Portal do Azure.
    1. Selecione **Azure Active Directory** > **usuários**do**HDIFabrikam** > .

### <a name="create-an-user-assigned-managed-identity"></a>Criar uma identidade gerenciada atribuída pelo usuário

Crie uma identidade gerenciada atribuída pelo usuário que será usada para configurar o Azure Active Directory Domain Services (Azure AD-DS). Para obter mais informações sobre como criar uma identidade gerenciada atribuída pelo usuário, consulte [criar, listar, excluir ou atribuir uma função a uma identidade gerenciada atribuída pelo usuário usando o portal do Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Entre no Portal do Azure.
1. Clique em **criar um recurso** e digite **identidade gerenciada**. Selecione**criação**de >  **identidade gerenciada atribuída pelo usuário**.
1. Insira **HDIFabrikamManagedIdentity** como o **nome do recurso**.
1. Selecione sua assinatura.
1. Em **grupo de recursos** , clique em **criar novo** e digite **HDIFabrikam-centralus**.
1. Selecione **EUA Central** em **local**.
1. Clique em **Criar**.

![criar uma nova identidade gerenciada atribuída pelo usuário](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image082.png)

### <a name="enable-azure-active-directory-domain-services"></a>Habilitar o Azure Active Directory Domain Services

Para obter mais informações, consulte [Enable Azure Active Directory Domain Services using the Azure portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) (Habilitar o Azure Active Directory Domain Services usando o Portal do Azure).

1. Crie a rede virtual para hospedar o Azure AD-DS. Execute o código do PowerShell a seguir.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription 'SUBSCRIPTION_ID'
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Entre no Portal do Azure.
1. Clique em **criar recurso**, insira **serviços de domínio** e selecione **Azure AD Domain Services**.
1. Na tela **noções básicas** , conclua as seguintes etapas:
    1. Em **nome do diretório** , selecione o Azure Active Directory criado para este artigo, **HDIFabrikam**.
    1. Insira um **nome de domínio DNS** de **HDIFabrikam.com**.
    1. Selecione sua assinatura.
    1. Especifique o grupo de recursos **HDIFabrikam-centralus** e o **local** de **EUA Central**.

        ![detalhes básicos do Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image084.png)

1. Na tela **rede** concluída, selecione a rede (**HDIFabrikam-VNET**) e a sub-rede (**AADDS**) que você criou com o script do PowerShell anterior. Ou você pode usar a opção **criar novo** para criar uma rede virtual agora.

    ![selecionar rede](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image086.png)

1. Na tela **grupo de administradores** , você deverá ver uma notificação de que um grupo chamado **Administradores do AAD DC** já foi criado para administrar esse grupo. Opcionalmente, você pode modificar a associação desse grupo, mas ele não é necessário para as etapas deste artigo. Clique em **OK**.

    ![exibir grupo de administradores](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image088.png)

1. Na tela **sincronização** , habilite sincronização completa selecionando **tudo** e clique em **OK**.

    ![Habilitar sincronização](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image090.png)

1. Na tela **Resumo** , verifique os detalhes do Azure AD-DS e clique em **OK**.

    ![verificar detalhes](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image092.png)

1. Depois de habilitar o Azure AD-DS, um servidor DNS (Serviço de Nomes de Domínio) local é executado nas VMs (máquinas virtuais) do AD.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Configurar sua rede virtual do Azure AD-DS

As etapas nesta seção ajudarão você a configurar sua rede virtual do Azure AD-DS (**HDIFabrikam-AADDSVNET**) para usar seus servidores DNS personalizados.

1. Localize os endereços IP dos seus servidores DNS personalizados. Clique no recurso AD-DS do **HDIFabrikam.com** , clique em **Propriedades** em **gerenciar**   e examine os endereços IP listados em **endereço IP na rede virtual**.

    ![Localizar endereços IP de DNS personalizados para o Azure AD-DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image096.png)

1. Configure **HDIFabrikam-AADDSVNET** para IPS `10.0.0.4` personalizados e `10.0.0.5`.

    1. Selecione **servidores** DNS na categoria **configurações** . em seguida, clique no botão de opção ao lado de **personalizado**, insira o primeiro endereço IP (10.0.0.4) na caixa de texto abaixo e clique em **salvar**.
    1. Adicione endereços IP adicionais (10.0.0.5) usando as mesmas etapas.

1. Em nosso cenário, o Azure AD-DS foi configurado para usar os endereços IP 10.0.0.4 e 10.0.0.5, definindo o mesmo endereço IP na VNet AADDS como mostrado na imagem abaixo.

    ![Exibir servidores DNS personalizados](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image098.png)

## <a name="securing-ldap-traffic"></a>Protegendo o tráfego LDAP

O protocolo LDAP (Lightweight Directory Access Protocol) é usado para ler e gravar em Active Directory. Você pode tornar o tráfego LDAP confidencial e seguro usando a tecnologia de protocolo SSL (SSL)/TLS (Transport Layer Security). Você pode habilitar LDAP sobre SSL (LDAPs) instalando um certificado formatado corretamente.

Para obter mais informações sobre o LDAP seguro, consulte [Configurar LDAP seguro (LDAPS) para um domínio gerenciado Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap).

Nesta seção, você cria um certificado autoassinado, baixa o certificado e configura LDAPS seguros (LDAPs) para o domínio gerenciado do Azure AD-DS **hdifabrikam** .

O script a seguir cria um certificado para hdifabrikam. O certificado é salvo no caminho "LocalMachine".

> [!Note] 
> Qualquer utilitário ou aplicativo que cria uma solicitação PKCS \#10 válida pode ser usado para formar a solicitação de certificado SSL.

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

Verifique se o certificado está instalado no repositório pessoal\'do computador. Conclua as seguintes etapas:

1. Inicie o MMC (console de gerenciamento Microsoft).
1. Adicione o snap-in de certificados que gerencia certificados no computador local.
1. Expanda **certificados (computador local)** , expanda **pessoal**e, em seguida, expanda **certificados**. Um novo certificado deve existir no repositório pessoal. Esse certificado é emitido para o nome de host totalmente qualificado.

    ![verificar a criação do certificado](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image102.png)

1. No painel direito, clique com o botão direito do mouse no certificado que você criou na etapa anterior, aponte para **todas as tarefas**e clique em **Exportar**.

1. Na página **Exportar chave** privada, clique em **Sim, exportar a chave privada**. A chave privada é necessária para que as mensagens criptografadas sejam lidas do computador em que a chave será importada.

    ![exportar chave privada](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image103.png)

1. Na página **formato** do arquivo de exportação, deixe as configurações padrão e clique em **Avançar**. 
1. Na página **senha** , digite uma senha para a chave privada, selecione **TripleDES-SHA1** para **criptografia** e clique em **Avançar**.
1. Na página **arquivo a ser**exportado, digite o caminho e o nome do arquivo de certificado exportado e clique em **Avançar**.
1. O nome do arquivo deve ser a extensão. pfx, esse arquivo é configurado em portal do Azure para estabelecer uma conexão segura.
1. Habilite LDAP seguro (LDAPs) para um domínio gerenciado Azure AD Domain Services.
    1. Selecione o **HDIFabrikam.com** de domínio no portal do Azure.
    1. Clique em **LDAP seguro** em **gerenciar**.
    1. Na tela **LDAP seguro** , clique em **habilitar** em **LDAP seguro**.
    1. Procure o arquivo de certificado. pfx que você exportou em seu computador.
    1. Insira a senha do certificado.

    ![Habilitar LDAP seguro](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image113.png)

1. Agora que você habilitou LDAP Seguro, verifique se ele está acessível habilitando a porta 636.
    1. Clique no grupo de segurança de rede **AADDS-HDIFabrikam.com-NSG** no grupo de recursos **HDIFabrikam-centralus** .
    1. Em **configurações** , clique em **regras** > de segurança de entrada**Adicionar**.
    1. Na tela **Adicionar regra de segurança de entrada** , insira as seguintes propriedades e clique em **Adicionar**:

        | Propriedade | Valor |
        |---|---|
        | Source | Any |
        | Source port ranges | * |
        | Destino | Any |
        | Destination port range | 636 |
        | Protocol | Any |
        | Action | Allow |
        | Priority | \<Número desejado\> |
        | Nome | Port_LDAP_636 |

    ![regra de segurança de entrada](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

1. `HDIFabrikamManagedIdentity`é a identidade gerenciada atribuída pelo usuário, a função colaborador de serviços de domínio do HDInsight está habilitada para a identidade gerenciada que permitirá que essa identidade Leia, crie, modifique e exclua operações de serviços de domínio.

    ![criar identidade gerenciada atribuída pelo usuário](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image117.png)

## <a name="creating-enterprise-security-package-enabled-hdinsight-cluster"></a>Criando Enterprise Security Package cluster HDInsight habilitado

Esta etapa requer os seguintes pré-requisitos:

1. Crie um novo grupo `HDIFabrikam-WestUS` de recursos no local. `West US`
1. Crie uma rede virtual que hospedará o cluster HDInsight habilitado para ESP.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Crie uma relação de pares entre a rede virtual que está hospedando`HDIFabrikam-AADDSVNET`AADDS () e a rede virtual que hospedará o cluster HDInsight habilitado`HDIFabrikam-HDIVNet`para ESP (). Use o código do PowerShell a seguir para emparelhar essas duas redes virtuais.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Crie uma nova conta de Azure Data Lake Storage Gen2, **Hdigen2store**, configurada com a identidade gerenciada pelo usuário **HDIFabrikamManagedIdentity**. Para obter mais informações sobre como criar Data Lake Storage Gen2 contas habilitadas com identidades gerenciadas pelo usuário, consulte [usar o Azure data Lake Storage Gen2 com clusters do Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. Configure o DNS personalizado na rede virtual **HDIFabrikam-AADDSVNET** .
    1. Vá para o portal do Azure > **grupos** > de recursos**OnPremADVRG** > **HDIFabrikam-AADDSVNET** > **servidores DNS**.
    1. Selecione **personalizado** e insira `10.0.0.4` e `10.0.0.5`.
    1. Clique em **Salvar**.

        ![salvar configurações personalizadas de DNS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image123.png)

1. Crie um novo cluster HDInsight Spark habilitado para ESP.
    1. Clique em **personalizado (tamanho, configurações, aplicativos)** .
    2. Insira os detalhes desejados para as **noções básicas**da seção 1. Verifique se o **tipo de cluster** é **Spark 2,3 (HDI 3,6)** e se o **grupo de recursos** é **HDIFabrikam-centralus**

    1. Na seção 2 **segurança + rede**, conclua as seguintes etapas:
        1. Clique em **habilitado** em **Enterprise Security Package**.
        1. Clique em **usuário administrador de cluster** e selecione a conta **HDIAdmin** que você criou anteriormente como o usuário administrador local. Clique em **Selecionar**.

        1. Clique em **grupo de acesso ao cluster** e selecione **HDIUserGroup**. Qualquer usuário que você adicionar a esse grupo no futuro poderá acessar os clusters HDInsight.

            ![selecionar grupo de acesso ao cluster](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image129.jpg)

    1. Conclua as outras etapas da configuração do cluster e verifique os detalhes no **Resumo do cluster**. Clique em **Criar**.

1. Entre na interface do usuário do amAmbari para o cluster recém-criado `https://CLUSTERNAME.azurehdinsight.net` em usando seu nome `hdiadmin@hdifabrikam.com` de usuário e senha de administrador.

    ![entrar no Ambari](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image135.jpg)

1. Clique em **funções** no painel do cluster.
1. Na página **funções** , insira o grupo **hdiusergroup** para atribuí-lo à função de **administrador de cluster** em **atribuir funções a eles**.

    ![atribuir função de administrador de cluster ao hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image137.jpg)

1. Abra o cliente SSH e faça logon no cluster usando o **hdiuser** que você criou anteriormente no Active Directory local.

    ![fazer logon no cluster com o SSH](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image139.jpg)

Se você conseguir fazer logon com essa conta, você configurou o cluster do ESP corretamente para sincronizar com o Active Directory local.

## <a name="next-steps"></a>Próximas etapas

* [Uma introdução à segurança do Apache Hadoop com Enterprise Security Package](hdinsight-security-overview.md)
