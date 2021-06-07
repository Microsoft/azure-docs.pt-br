---
title: Criar, configurar clusters Enterprise Security Package – Azure
description: Saiba como criar e configurar clusters Enterprise Security Package no Azure HDInsight
services: hdinsight
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/10/2019
ms.openlocfilehash: 3e6a5742d2dc8079c36f2d9f39a827f8db99cd67
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867161"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Criar e configurar clusters Enterprise Security Package no Azure HDInsight

O Enterprise Security Package (ESP) para Azure HDInsight fornece acesso a autenticação baseada em Active Directory, suporte a multiusuário e controle de acesso baseado em função para seus clusters de Apache Hadoop no Azure. Os clusters de ESP do HDInsight permitem que as organizações que aderem às políticas de segurança corporativa possam processar dados confidenciais com segurança.

Este guia mostra como criar um cluster do Azure HDInsight habilitado para ESP. Ele também mostra como criar uma VM IaaS do Windows na qual Active Directory e o DNS (sistema de nomes de domínio) estão habilitados. Use este guia para configurar os recursos necessários para permitir que os usuários locais entrem em um cluster HDInsight habilitado para ESP.

O servidor criado funcionará como uma substituição para seu ambiente local *real* . Você o usará para as etapas de instalação e configuração. Posteriormente, você repetirá as etapas em seu próprio ambiente.

Este guia também ajudará você a criar um ambiente de identidade híbrida usando a sincronização de hash de senha com o Azure Active Directory (Azure AD). O guia complementa o [uso do ESP no HDInsight](apache-domain-joined-architecture.md).

Antes de usar esse processo em seu próprio ambiente:

* Configurar Active Directory e DNS.
* Habilitar o Azure AD.
* Sincronizar contas de usuário locais com o Azure AD.

:::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png" alt-text="Diagrama de arquitetura do Azure AD" border="false":::

## <a name="create-an-on-premises-environment"></a>Criar um ambiente local

Nesta seção, você usará um modelo de implantação de início rápido do Azure para criar novas VMs, configurar o DNS e adicionar uma nova floresta Active Directory.

1. Vá para o modelo de implantação de início rápido para [criar uma VM do Azure com uma nova floresta Active Directory](https://azure.microsoft.com/resources/templates/active-directory-new-domain/).

1. Selecione **implantar no Azure**.
1. Entre na sua assinatura do Azure.
1. Na página **criar uma VM do Azure com uma nova floresta do AD** , forneça as seguintes informações:

    |Propriedade | Valor |
    |---|---|
    |Assinatura|Selecione a assinatura na qual você deseja implantar os recursos.|
    |Resource group|Selecione **criar novo** e insira o nome `OnPremADVRG`|
    |Location|Selecione uma localização.|
    |Nome de Usuário do Administrador|`HDIFabrikamAdmin`|
    |Senha do Administrador|Digite uma senha.|
    |Nome de domínio|`HDIFabrikam.com`|
    |Prefixo DNS|`hdifabrikam`|

    Mantenha os valores padrão restantes.

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png" alt-text="Modelo para criar uma VM do Azure com uma nova floresta do Azure AD" border="true":::

1. Examine os **termos e condições** e, em seguida, selecione **concordo com os termos e condições declarados acima**.
1. Selecione **comprar** e monitore a implantação e aguarde sua conclusão. A implantação leva cerca de 30 minutos para ser concluída.

## <a name="configure-users-and-groups-for-cluster-access"></a>Configurar usuários e grupos para acesso ao cluster

Nesta seção, você criará os usuários que terão acesso ao cluster HDInsight no final deste guia.

1. Conecte-se ao controlador de domínio usando Área de Trabalho Remota.
    1. No portal do Azure, navegue até **grupos de recursos**  >  **OnPremADVRG**  >  **adVM**  >  **conectar**.
    1. Na lista suspensa **endereço IP** , selecione o endereço IP público.
    1. Selecione **baixar arquivo RDP** e, em seguida, abra o arquivo.
    1. Use `HDIFabrikam\HDIFabrikamAdmin` como o nome de usuário.
    1. Insira a senha que você escolheu para a conta de administrador.
    1. Selecione **OK**.

1. No painel de **Gerenciador do servidor** do controlador de domínio, navegue até **ferramentas**  >  **Active Directory usuários e computadores**.

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png" alt-text="No painel Gerenciador do Servidor, abra Active Directory Management" border="true":::

1. Crie dois novos usuários: **HDIAdmin** e **HDIUser**. Esses dois usuários entrarão em clusters HDInsight.

    1. Na página **Active Directory usuários e computadores** , clique com o botão direito do mouse em `HDIFabrikam.com` e navegue até **novo**  >  **usuário**.

        :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png" alt-text="Criar um novo usuário Active Directory" border="true":::

    1. Na página **novo objeto – usuário** , insira `HDIUser` para o **nome** e o **nome de logon do usuário**. Os outros campos serão preenchidos automaticamente. Em seguida, selecione **Avançar**.

        :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png" alt-text="Criar o primeiro objeto de usuário administrador" border="true":::

    1. Na janela pop-up exibida, insira uma senha para a nova conta. Selecione a **senha nunca expira** e, em seguida, **OK** na mensagem pop-up.
    1. Selecione **Avançar** e **concluir** para criar a nova conta.
    1. Repita as etapas acima para criar o usuário `HDIAdmin` .

        :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png" alt-text="Criar um segundo objeto de usuário administrador" border="true":::

1. Crie um grupo de segurança global.

    1. Em **Active Directory usuários e computadores**, clique com o botão direito do mouse em `HDIFabrikam.com` e navegue até **novo**  >  **grupo**.

    1. Digite `HDIUserGroup` na caixa de texto **nome do grupo** .

    1. Selecione **OK**.

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png" alt-text="Criar um novo grupo de Active Directory" border="true":::

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png" alt-text="Criar um novo objeto" border="true":::

1. Adicione membros a **HDIUserGroup**.

    1. Clique com o botão direito do mouse em **HDIUser** e selecione **Adicionar a um grupo...**.
    1. Na caixa de texto **digite os nomes de objeto a serem selecionados** , digite `HDIUserGroup` . Em seguida, selecione **OK** e **OK** novamente no pop-up.
    1. Repita as etapas anteriores para a conta **HDIAdmin** .

        :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png" alt-text="Adicione o membro HDIUser ao grupo HDIUserGroup" border="true":::

Agora você criou seu ambiente de Active Directory. Você adicionou dois usuários e um grupo de usuários que pode acessar o cluster HDInsight.

Os usuários serão sincronizados com o Azure AD.

### <a name="create-an-azure-ad-directory"></a>Criar um diretório do AD do Azure

1. Entre no portal do Azure.
1. Selecione **criar um recurso** e digite `directory` . Selecione **Azure Active Directory**  >  **criar**.
1. Em **nome da organização**, insira `HDIFabrikam` .
1. Em **nome de domínio inicial**, insira `HDIFabrikamoutlook` .
1. Selecione **Criar**.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png" alt-text="Criar um diretório do AD do Azure" border="true":::

### <a name="create-a-custom-domain"></a>Criar um domínio personalizado

1. Em seu novo **Azure Active Directory**, em **gerenciar**, selecione **nomes de domínio personalizados**.
1. Selecione **+ Adicionar domínio personalizado**.
1. Em **nome de domínio personalizado**, digite `HDIFabrikam.com` e, em seguida, selecione **Adicionar domínio**.
1. Em seguida, conclua a [adição de suas informações de DNS ao registrador de domínios](../../active-directory/fundamentals/add-custom-domain.md#add-your-dns-information-to-the-domain-registrar).

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png" alt-text="Criar um domínio personalizado" border="true":::

### <a name="create-a-group"></a>Criar um grupo

1. Em seu novo **Azure Active Directory**, em **gerenciar**, selecione **grupos**.
1. Selecione **+ novo grupo**.
1. Na caixa de texto **nome do grupo** , digite `AAD DC Administrators` .
1. Selecione **Criar**.

## <a name="configure-your-azure-ad-tenant"></a>Configurar seu locatário do Azure AD

Agora você configurará seu locatário do Azure AD para que você possa sincronizar usuários e grupos da instância de Active Directory local para a nuvem.

Crie um administrador de locatários Active Directory.

1. Entre no portal do Azure e selecione seu locatário do Azure AD, **HDIFabrikam**.

1. Navegue até **gerenciar**  >  **usuários**  >  **novo usuário**.

1. Insira os seguintes detalhes para o novo usuário:

   **Identidade**

   |Propriedade |Descrição |
   |---|---|
   |Nome de usuário|Insira `fabrikamazureadmin` na caixa de texto. Na lista suspensa nome de domínio, selecione `hdifabrikam.com`|
   |Nome| Digite `fabrikamazureadmin`.|

   **Senha**
   1. Selecione **deixe-me criar a senha**.
   1. Insira uma senha segura de sua escolha.

   **Grupos e funções**
   1. Selecione **0 grupos selecionados**.
   1. Selecione **Administradores do AAD DC** e, em seguida, **selecione**.

      :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-group-member.png" alt-text="A caixa de diálogo grupos do Azure AD" border="true":::

   1. Selecione **usuário**.
   1. Selecione **administrador global** e, em seguida, **selecione**.

      :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-role-member.png" alt-text="A caixa de diálogo função do Azure AD" border="true":::

1. Selecione **Criar**.

1. Em seguida, faça com que o novo usuário entre no portal do Azure em que será solicitado a alterar a senha. Você precisará fazer isso antes de configurar Microsoft Azure Active Directory Connect.

## <a name="sync-on-premises-users-to-azure-ad"></a>Sincronizar usuários locais com o Azure AD

### <a name="configure-microsoft-azure-active-directory-connect"></a>Configurar Microsoft Azure Active Directory Connect

1. No controlador de domínio, baixe [Microsoft Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594).

1. Abra o arquivo executável que você baixou e concorde com os termos de licença. Selecione **Continuar**.

1. Selecione **usar configurações expressas**.

1. Na página **conectar ao Azure ad** , insira o nome de usuário e a senha do administrador global do Azure AD. Use o nome de usuário `fabrikamazureadmin@hdifabrikam.com` que você criou quando configurou seu locatário de Active Directory. Em seguida, selecione **Avançar**.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png" alt-text="Conectar-se ao Azure A D" border="true":::

1. Na página **conectar a Active Directory Domain Services** , insira o nome de usuário e a senha de uma conta de administrador corporativo. Use o nome de usuário `HDIFabrikam\HDIFabrikamAdmin` e sua senha que você criou anteriormente. Em seguida, selecione **Avançar**.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png" alt-text="Conecte-se a uma página D D." border="true":::

1. Na página de **configuração de entrada do Azure ad** , selecione **Avançar**.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png" alt-text="Página de configuração de entrada do Azure AD" border="true":::

1. Na página **pronto para configurar** , selecione **instalar**.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png" alt-text="Pronto para configurar a página" border="true":::

1. Na página **Configuração completa** selecione **Sair**.
   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png" alt-text="Página configuração concluída" border="true":::

1. Após a conclusão da sincronização, confirme se os usuários que você criou no diretório IaaS estão sincronizados com o Azure AD.
   1. Entre no portal do Azure.
   1. Selecione **Azure Active Directory**  >    >  **usuários** do HDIFabrikam.

### <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerenciada atribuída ao usuário

Crie uma identidade gerenciada atribuída pelo usuário que você possa usar para configurar o Azure AD Domain Services (AD DS do Azure). Para obter mais informações, consulte [criar, listar, excluir ou atribuir uma função a uma identidade gerenciada atribuída pelo usuário usando o portal do Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Entre no portal do Azure.
1. Selecione **criar um recurso** e digite `managed identity` . Selecione criação de **identidade gerenciada atribuída pelo usuário**  >  .
1. Para o **nome do recurso**, digite `HDIFabrikamManagedIdentity` .
1. Selecione sua assinatura.
1. Em **grupo de recursos**, selecione **criar novo** e insira `HDIFabrikam-CentralUS` .
1. Em **local**, selecione **EUA Central**.
1. Selecione **Criar**.

:::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png" alt-text="Criar uma nova identidade gerenciada atribuída pelo usuário" border="true":::

### <a name="enable-azure-ad-ds"></a>Habilitar o Azure AD DS

Siga estas etapas para habilitar o Azure AD DS. Para obter mais informações, consulte [habilitar o Azure AD DS usando o portal do Azure](../../active-directory-domain-services/tutorial-create-instance.md).

1. Crie uma rede virtual para hospedar AD DS do Azure. Execute o código do PowerShell a seguir.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }

    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
    
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Entre no portal do Azure.
1. Selecione **criar recurso**, insira `Domain services` e selecione **Azure AD Domain Services**  >  **criar**.
1. Na página **noções básicas** :
   1. Em **nome do diretório**, selecione o diretório do Azure AD que você criou: **HDIFabrikam**.
   1. Para **nome de domínio DNS**, insira *HDIFabrikam.com*.
   1. Selecione sua assinatura.
   1. Especifique o grupo de recursos **HDIFabrikam-centralus**. Para **local**, selecione **EUA Central**.

      :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png" alt-text="Detalhes básicos do Azure AD DS" border="true":::

1. Na página **rede** , selecione a rede (**HDIFabrikam-VNET**) e a sub-rede (**AADDS**) que você criou usando o script do PowerShell. Ou escolha **criar novo** para criar uma rede virtual agora.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png" alt-text="Etapa criar rede virtual" border="true":::

1. Na página **grupo de administradores** , você deverá ver uma notificação de que um grupo chamado **Administradores do AAD DC** já foi criado para administrar esse grupo. Você pode modificar a associação desse grupo se desejar, mas nesse caso você não precisará alterá-la. Selecione **OK**.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png" alt-text="Exibir o grupo de administradores do Azure AD" border="true":::

1. Na página **sincronização** , habilite sincronização completa selecionando **tudo**  >  **OK**.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png" alt-text="Habilitar a sincronização de AD DS do Azure" border="true":::

1. Na página **Resumo** , verifique os detalhes do Azure AD DS e selecione **OK**.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png" alt-text="Habilitar os Serviços de Domínio do Azure AD" border="true":::

Depois de habilitar o Azure AD DS, um servidor DNS local é executado nas VMs do Azure AD.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Configurar sua rede virtual do Azure AD DS

Use as etapas a seguir para configurar sua rede virtual do Azure AD DS (**HDIFabrikam-AADDSVNET**) para usar seus servidores DNS personalizados.

1. Localize os endereços IP dos seus servidores DNS personalizados.
   1. Selecione o `HDIFabrikam.com` recurso de AD DS do Azure.
   1. Em **Gerenciar**, selecione **Propriedades**.
   1. Localize os endereços IP em **endereço IP na rede virtual**.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png" alt-text="Localizar endereços IP de DNS personalizados para o Azure AD DS" border="true":::

1. Configure **HDIFabrikam-AADDSVNET** para usar os endereços IP personalizados 10.0.0.4 e 10.0.0.5.

   1. Em **configurações**, selecione **servidores DNS**.
   1. selecione **Personalizado**.
   1. Na caixa de texto, insira o primeiro endereço IP (*10.0.0.4*).
   1. Clique em **Salvar**.
   1. Repita as etapas para adicionar o outro endereço IP (*10.0.0.5*).

Em nosso cenário, configuramos o Azure AD DS para usar os endereços IP 10.0.0.4 e 10.0.0.5, definindo o mesmo endereço IP na rede virtual do Azure AD DS:

:::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png" alt-text="A página servidores DNS personalizados" border="true":::

## <a name="securing-ldap-traffic"></a>Protegendo o tráfego LDAP

O protocolo LDAP (Lightweight Directory Access Protocol) é usado para ler e gravar em Azure Active Directory. Você pode tornar o tráfego LDAP confidencial e seguro usando protocolo SSL (SSL) ou a tecnologia TLS (Transport Layer Security). Você pode habilitar LDAP sobre SSL (LDAPs) instalando um certificado formatado corretamente.

Para obter mais informações sobre LDAP seguro, consulte [Configurar LDAPS para um domínio gerenciado do Azure AD DS](../../active-directory-domain-services/tutorial-configure-ldaps.md).

Nesta seção, você criará um certificado autoassinado, baixará o certificado e configurará os LDAPs para o domínio gerenciado do **HDIFabrikam** Azure AD DS.

O script a seguir cria um certificado para **HDIFabrikam**. O certificado é salvo no caminho *LocalMachine* .

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

> [!NOTE]  
> Qualquer utilitário ou aplicativo que cria uma solicitação PKCS (Public Key Cryptography Standards) \# 10 pode ser usado para formar a solicitação de certificado TLS/SSL.

Verifique se o certificado está instalado no repositório **pessoal** do computador:

1. Inicie o MMC (console de gerenciamento Microsoft).
1. Adicione o snap-in de **certificados** que gerencia certificados no computador local.
1. Expanda **Certificados (Computador Local)**  > **Pessoal** > **Certificados**. Um novo certificado deve existir no repositório **pessoal** . Esse certificado é emitido para o nome de host totalmente qualificado.

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png" alt-text="Verificar a criação do certificado local" border="true":::

1. No painel à direita, clique com o botão direito do mouse no certificado que você criou. Aponte para **todas as tarefas** e, em seguida, selecione **Exportar**.

1. Na página **Exportar chave privada** , selecione **Sim, exportar a chave privada**. O computador em que a chave será importada precisa da chave privada para ler as mensagens criptografadas.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png" alt-text="A página Exportar chave privada do assistente para exportação de certificados" border="true":::

1. Na página **formato do arquivo de exportação** , deixe as configurações padrão e, em seguida, selecione **Avançar**.
1. Na página **senha** , digite uma senha para a chave privada. Para **criptografia**, selecione **TripleDES-SHA1**. Em seguida, selecione **Avançar**.
1. Na página **arquivo a ser exportado** , digite o caminho e o nome do arquivo de certificado exportado e, em seguida, selecione **Avançar**. O nome do arquivo deve ter uma extensão. pfx. Esse arquivo é configurado no portal do Azure para estabelecer uma conexão segura.
1. Habilite LDAPs para um domínio gerenciado AD DS do Azure.
   1. No portal do Azure, selecione o domínio `HDIFabrikam.com` .
   1. Em **gerenciar**, selecione **LDAP seguro**.
   1. Na página **LDAP seguro** , em **LDAP seguro**, selecione **habilitar**.
   1. Procure o arquivo de certificado. pfx que você exportou em seu computador.
   1. Insira a senha do certificado.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png" alt-text="Habilitar LDAP seguro" border="true":::

1. Agora que você habilitou LDAPs, verifique se ele está acessível habilitando a porta 636.
   1. No grupo de recursos **HDIFabrikam-centralus** , selecione o grupo de segurança de rede **AADDS-HDIFabrikam.com-NSG**.
   1. Em **configurações**, selecione **regras de segurança de entrada**  >  **Adicionar**.
   1. Na página **Adicionar regra de segurança de entrada** , insira as seguintes propriedades e selecione **Adicionar**:

      | Propriedade | Valor |
      |---|---|
      | Fonte | Qualquer |
      | Intervalos de portas de origem | * |
      | Destino | Qualquer |
      | Intervalo de portas de destino | 636 |
      | Protocolo | Qualquer |
      | Ação | Allow |
      | Prioridade | \<Desired number> |
      | Nome | Port_LDAP_636 |

      :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png" alt-text="A caixa de diálogo Adicionar regra de segurança de entrada" border="true":::

**HDIFabrikamManagedIdentity** é a identidade gerenciada atribuída pelo usuário. A função colaborador de serviços de domínio do HDInsight está habilitada para a identidade gerenciada que permitirá que essa identidade Leia, crie, modifique e exclua operações de serviços de domínio.

:::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png" alt-text="Criar uma identidade gerenciada atribuída ao usuário" border="true":::

## <a name="create-an-esp-enabled-hdinsight-cluster"></a>Criar um cluster HDInsight habilitado para ESP

Esta etapa requer os seguintes pré-requisitos:

1. Crie um novo grupo de recursos *HDIFabrikam-westus* no local **oeste dos EUA**.
1. Crie uma rede virtual que hospedará o cluster HDInsight habilitado para ESP.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Crie uma relação de pares entre a rede virtual que hospeda o Azure AD DS ( `HDIFabrikam-AADDSVNET` ) e a rede virtual que hospedará o cluster HDInsight habilitado para ESP ( `HDIFabrikam-HDIVNet` ). Use o código do PowerShell a seguir para emparelhar as duas redes virtuais.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Crie uma nova conta de Azure Data Lake Storage Gen2 chamada **Hdigen2store**. Configure a conta com o **HDIFabrikamManagedIdentity** de identidade gerenciada pelo usuário. Para obter mais informações, consulte [usar Azure data Lake Storage Gen2 com clusters do Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. Configure o DNS personalizado na rede virtual **HDIFabrikam-AADDSVNET** .
    1. Vá para o portal do Azure > **grupos de recursos**  >  **OnPremADVRG**  >  **HDIFabrikam-AADDSVNET**  >  **servidores DNS**.
    1. Selecione **personalizado** e insira *10.0.0.4* e *10.0.0.5*.
    1. Clique em **Salvar**.

        :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png" alt-text="Salvar configurações personalizadas de DNS para uma rede virtual" border="true":::

1. Crie um novo cluster HDInsight Spark habilitado para ESP.
    1. Selecione **personalizado (tamanho, configurações, aplicativos)**.
    1. Insira detalhes para **noções básicas** (seção 1). Verifique se o **tipo de cluster** é **Spark 2,3 (HDI 3,6)**. Verifique se o **grupo de recursos** é **HDIFabrikam-centralus**.

    1. Para **segurança + rede** (seção 2), preencha os seguintes detalhes:
        * Em **Enterprise Security Package**, selecione **habilitado**.
        * Selecione **usuário administrador de cluster** e selecione a conta **HDIAdmin** que você criou como o usuário administrador local. Clique em **Selecionar**.
        * Selecione **grupo de acesso ao cluster**  >  **HDIUserGroup**. Qualquer usuário que você adicionar a esse grupo no futuro poderá acessar os clusters HDInsight.

            :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg" alt-text="Selecione o grupo de acesso ao cluster HDIUserGroup" border="true":::

    1. Conclua as outras etapas da configuração do cluster e verifique os detalhes no **Resumo do cluster**. Selecione **Criar**.

1. Entre na interface do usuário do amAmbari para o cluster recém-criado em `https://CLUSTERNAME.azurehdinsight.net` . Use o nome de usuário do administrador `hdiadmin@hdifabrikam.com` e sua senha.

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg" alt-text="A janela de entrada da interface do usuário do Apache Ambari" border="true":::

1. No painel do cluster, selecione **funções**.
1. Na página **funções** , em **atribuir funções a elas**, ao lado da função de **administrador de cluster** , insira o grupo *hdiusergroup*.

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg" alt-text="Atribuir a função de administrador de cluster ao hdiusergroup" border="true":::

1. Abra seu cliente do Secure Shell (SSH) e entre no cluster. Use o **hdiuser** que você criou na instância de Active Directory local.

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg" alt-text="Entrar no cluster usando o cliente SSH" border="true":::

Se você puder entrar com essa conta, você configurou o cluster do ESP corretamente para sincronizar com sua instância de Active Directory local.

## <a name="next-steps"></a>Próximas etapas

Leia [uma introdução à segurança de Apache Hadoop com o ESP](hdinsight-security-overview.md).
