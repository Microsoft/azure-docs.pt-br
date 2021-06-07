---
title: Criar uma floresta de recursos de Azure AD Domain Services usando Azure PowerShell | Microsoft Docs
description: Neste artigo, saiba como criar e configurar uma floresta de recursos Azure Active Directory Domain Services e uma floresta de saída para um ambiente de Active Directory Domain Services local usando o Azure PowerShell.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: justinha
ms.openlocfilehash: ebfc2476b7955b926f86094de03973155386eb8f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619960"
---
# <a name="create-an-azure-active-directory-domain-services-resource-forest-and-outbound-forest-trust-to-an-on-premises-domain-using-azure-powershell"></a>Criar uma floresta de recursos de Azure Active Directory Domain Services e uma relação de confiança de floresta de saída para um domínio local usando Azure PowerShell

Em ambientes em que você não pode sincronizar hashes de senha ou tem usuários que entram exclusivamente usando cartões inteligentes para que não conheçam a senha deles, você pode usar uma floresta de recursos no Azure AD DS (Azure Active Directory Domain Services). Uma floresta de recursos usa uma relação de confiança de saída unidirecional do Azure AD DS com um ou mais ambientes do AD DS locais. Essa relação de confiança permite que usuários, aplicativos e computadores se autentiquem em um domínio local por meio do domínio gerenciado do Azure AD DS. Em uma floresta de recursos, os hashes de senha locais nunca são sincronizados.

![Diagrama da relação de confiança de floresta do Azure AD DS com o AD DS local](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Criar uma floresta de recursos do Azure AD DS usando Azure PowerShell
> * Criar uma relação de confiança de floresta de saída unidirecional no domínio gerenciado usando Azure PowerShell
> * Configurar o DNS em um ambiente de AD DS local para dar suporte à conectividade de domínio gerenciado
> * Criar uma relação de confiança de floresta de entrada unidirecional em um ambiente do AD DS local
> * Testar e validar a relação de confiança para autenticação e acesso a recursos

Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!IMPORTANT]
> Atualmente, as florestas de recursos de domínio gerenciados não dão suporte a arquivos Azure HDInsight ou Azure. As florestas de usuário de domínio gerenciado padrão oferecem suporte a esses dois serviços adicionais.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisará dos seguintes recursos e privilégios:

* Uma assinatura ativa do Azure.
    * Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].

* Instalar e configurar o PowerShell do Azure.
    * Se necessário, siga as instruções para [instalar o módulo do Azure PowerShell e conectar-se à sua assinatura do Azure](/powershell/azure/install-az-ps).
    * Lembre-se de entrar em sua assinatura do Azure usando o cmdlet [Connect-AzAccount][Connect-AzAccount].
* Instale e configure o Azure AD PowerShell.
    * Se necessário, siga as instruções para [instalar o módulo do Azure AD PowerShell e conectar-se ao Azure AD](/powershell/azure/active-directory/install-adv2).
    * Lembre-se de entrar em seu locatário do Azure AD usando o cmdlet [Connect-AzureAD][Connect-AzureAD].
* É necessário ter privilégios de *administrador global* no locatário do Azure AD para habilitar o Azure AD DS.
* Você precisa de privilégios de *Colaborador* em sua assinatura do Azure para criar os recursos necessários do Azure AD DS.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Neste artigo, você cria e configura a relação de confiança de floresta de saída de um domínio gerenciado usando o portal do Azure. Para começar, primeiro entre no [portal do Azure](https://portal.azure.com).

## <a name="deployment-process"></a>Processo de implantação

Trata-se de um processo de várias partes para criar uma floresta de recursos de domínio gerenciado e a relação de confiança com um AD DS local. As etapas de alto nível a seguir criam seu ambiente híbrido confiável:

1. Crie uma entidade de serviço de domínio gerenciado.
1. Crie uma floresta de recursos de domínio gerenciado.
1. Crie conectividade de rede híbrida usando VPN site a site ou rota expressa.
1. Crie o lado do domínio gerenciado da relação de confiança.
1. Crie o lado AD DS local da relação de confiança.

Antes de começar, certifique-se de entender as [considerações de rede, a nomenclatura de floresta e os requisitos de DNS](tutorial-create-forest-trust.md#networking-considerations). Você não pode alterar o nome da floresta de domínio gerenciado depois que ele é implantado.

## <a name="create-the-azure-ad-service-principal"></a>Criar a entidade de serviço do Azure AD

O AD DS do Azure exige que uma entidade de serviço sincronize dados do Azure AD. Essa entidade de segurança deve ser criada em seu locatário do Azure AD antes de você criar a floresta de recursos de domínio gerenciado.

Crie uma entidade de serviço do Azure AD para o Azure AD DS para se comunicar e se autenticar. É usada uma ID de aplicativo específica denominada *Serviços de Controlador de Domínio* com uma ID de *6ba9a5d4-8456-4118-b521-9c5ca10cdf84*. Não altere essa ID de aplicativo.

Crie uma entidade de serviço do Azure AD usando o cmdlet [New-AzureADServicePrincipal][New-AzureADServicePrincipal]:

```powershell
New-AzureADServicePrincipal -AppId "6ba9a5d4-8456-4118-b521-9c5ca10cdf84"
```

## <a name="create-a-managed-domain-resource-forest"></a>Criar uma floresta de recursos de domínio gerenciado

Para criar uma floresta de recursos de domínio gerenciado, use o `New-AzureAaddsForest` script. Esse script faz parte de um conjunto mais amplo de comandos que dão suporte à criação e ao gerenciamento de florestas de recursos de domínio gerenciado, incluindo a criação da floresta de limite unidirecional em uma seção a seguir. Esses scripts estão disponíveis na [Galeria do PowerShell](https://www.powershellgallery.com/) e são assinados digitalmente pela equipe de engenharia do Azure AD.

1. Primeiro, crie um grupo de recursos usando o cmdlet [New-AzResourceGroup][New-AzResourceGroup] . No exemplo a seguir, o grupo de recursos é chamado *myResourceGroup* e é criado na região *westus*. Use seu nome e sua região desejada:

    ```azurepowershell
    New-AzResourceGroup `
      -Name "myResourceGroup" `
      -Location "WestUS"
    ```

1. Instale o `New-AaddsResourceForestTrust` script do [Galeria do PowerShell][powershell-gallery] usando o cmdlet [install-script][Install-Script] :

    ```powershell
    Install-Script -Name New-AaddsResourceForestTrust
    ```

1. Revise os parâmetros a seguir necessários para o `New-AzureAaddsForest` script. Verifique se você também tem os pré-requisitos **Azure PowerShell** e módulos **do PowerShell do Azure ad** . Verifique se você planejou os requisitos de rede virtual para fornecer conectividade de aplicativo e local.

    | Nome                         | Parâmetro de script          | Descrição |
    |:-----------------------------|---------------------------|:------------|
    | Subscription                 | *-azureSubscriptionId*    | ID da assinatura usada para a cobrança do AD DS do Azure. Você pode obter a lista de assinaturas usando o cmdlet [Get-AzureRMSubscription][Get-AzureRMSubscription] . |
    | Grupo de recursos               | *-aaddsResourceGroupName* | Nome do grupo de recursos para o domínio gerenciado e recursos associados. |
    | Location                     | *-aaddsLocation*          | A região do Azure para hospedar seu domínio gerenciado. Para as regiões disponíveis, consulte [regiões com suporte para o AD DS do Azure.](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all) |
    | Administrador de AD DS do Azure    | *-aaddsAdminUser*         | O nome principal do usuário do primeiro administrador de domínio gerenciado. Essa conta deve ser uma conta de usuário de nuvem existente no seu Azure Active Directory. O usuário e o usuário que executa o script são adicionados ao grupo de *Administradores de DC do AAD* . |
    | Nome de domínio do AD DS do Azure      | *-aaddsDomainName*        | O FQDN do domínio gerenciado, com base nas diretrizes anteriores sobre como escolher um nome de floresta. |

    O `New-AzureAaddsForest` script pode criar a rede virtual do Azure e a sub-rede AD DS do Azure se esses recursos ainda não existirem. O script pode, opcionalmente, criar as sub-redes de carga de trabalho, quando especificado:

    | Nome                              | Parâmetro de script                  | Descrição |
    |:----------------------------------|:----------------------------------|:------------|
    | Nome da rede virtual              | *-aaddsVnetName*                  | Nome da rede virtual para o domínio gerenciado.|
    | Espaço de endereço                     | *-aaddsVnetCIDRAddressSpace*      | Intervalo de endereços da rede virtual na notação CIDR (se estiver criando a rede virtual).|
    | Nome da sub-rede AD DS do Azure           | *-aaddsSubnetName*                | Nome da sub-rede da rede virtual *aaddsVnetName* que hospeda o domínio gerenciado. Não implante suas próprias VMs e cargas de trabalho nessa sub-rede. |
    | Intervalo de endereços do AD DS do Azure         | *-aaddsSubnetCIDRAddressRange*    | Intervalo de endereços de sub-rede na notação CIDR para a instância do AAD DS, como *192.168.1.0/24*. O intervalo de endereços deve estar contido no intervalo de endereços da rede virtual e diferente de outras sub-redes. |
    | Nome da sub-rede de carga de trabalho (opcional)   | *-workloadSubnetName*             | Nome opcional de uma sub-rede na rede virtual *aaddsVnetName* a ser criada para suas próprias cargas de trabalho de aplicativo. VMs e aplicativos e também estar conectados a uma rede virtual emparelhada do Azure. |
    | Intervalo de endereços de carga de trabalho (opcional) | *-workloadSubnetCIDRAddressRange* | Intervalo de endereços de sub-rede opcional na notação CIDR para carga de trabalho do aplicativo, como *192.168.2.0/24*. O intervalo de endereços deve estar contido no intervalo de endereços da rede virtual e diferente de outras sub-redes.|

1. Agora, crie uma floresta de recursos de domínio gerenciado usando o `New-AzureAaaddsForest` script. O exemplo a seguir cria uma floresta chamada *addscontoso.com* e cria uma sub-rede de carga de trabalho. Forneça seus próprios nomes de parâmetro e intervalos de endereços IP ou redes virtuais existentes.

    ```azurepowershell
    New-AzureAaddsForest `
        -azureSubscriptionId <subscriptionId> `
        -aaddsResourceGroupName "myResourceGroup" `
        -aaddsLocation "WestUS" `
        -aaddsAdminUser "contosoadmin@contoso.com" `
        -aaddsDomainName "aaddscontoso.com" `
        -aaddsVnetName "myVnet" `
        -aaddsVnetCIDRAddressSpace "192.168.0.0/16" `
        -aaddsSubnetName "AzureADDS" `
        -aaddsSubnetCIDRAddressRange "192.168.1.0/24" `
        -workloadSubnetName "myWorkloads" `
        -workloadSubnetCIDRAddressRange "192.168.2.0/24"
    ```

    Leva muito tempo para criar a floresta de recursos de domínio gerenciado e recursos de suporte. Permitir que o script seja concluído. Continue na próxima seção para configurar a conectividade de rede local enquanto a floresta de recursos do Azure AD provisiona em segundo plano.

## <a name="configure-and-validate-network-settings"></a>Configurar e validar as configurações de rede

À medida que o domínio gerenciado continua a implantar, configure e valide a conectividade de rede híbrida para o datacenter local. Você também precisa de uma VM de gerenciamento para usar com o domínio gerenciado para manutenção regular. Algumas das conectividades híbridas podem já existir em seu ambiente ou talvez você precise trabalhar com outras pessoas em sua equipe para configurar as conexões.

Antes de começar, certifique-se de entender as [considerações e as recomendações de rede](tutorial-create-forest-trust.md#networking-considerations).

1. Crie a conectividade híbrida para sua rede local para o Azure usando uma VPN do Azure ou uma conexão do Azure ExpressRoute. A configuração de rede híbrida está além do escopo desta documentação e talvez já exista em seu ambiente. Para obter detalhes sobre cenários específicos, consulte os seguintes artigos:

    * [VPN site a site do Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md).
    * [Visão geral do Azure ExpressRoute](../expressroute/expressroute-introduction.md).

    > [!IMPORTANT]
    > Se você criar a conexão diretamente com a rede virtual do seu domínio gerenciado, use uma sub-rede de gateway separada. Não crie o gateway na sub-rede do domínio gerenciado.

1. Para administrar um domínio gerenciado, crie uma VM de gerenciamento, ingresse-a no domínio gerenciado e instale as ferramentas de gerenciamento de AD DS necessárias.

    Enquanto a floresta de recursos de domínio gerenciado estiver sendo implantada, [crie uma VM do Windows Server](./join-windows-vm.md) e [Instale as ferramentas de gerenciamento de AD DS básicas](./tutorial-create-management-vm.md) para instalar as ferramentas de gerenciamento necessárias. Aguarde para unir a VM de gerenciamento ao domínio gerenciado até uma das etapas a seguir depois que o domínio for implantado com êxito.

1. Valide a conectividade de rede entre sua rede local e a rede virtual do Azure.

    * Confirme se o controlador de domínio local pode se conectar à VM gerenciada usando o `ping` ou a área de trabalho remota, por exemplo.
    * Verifique se a VM de gerenciamento pode se conectar aos controladores de domínio locais, usando novamente um utilitário como o `ping` .

1. No portal do Azure, pesquise e selecione **Azure AD Domain Services**. Escolha seu domínio gerenciado, como *aaddscontoso.com* e aguarde o status para relatar como **em execução**.

    Durante a execução, [atualize as configurações de DNS para a rede virtual do Azure](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network) e, em seguida, [habilite as contas de usuário do Azure AD DS](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) para finalizar as configurações de sua floresta de recursos de domínio gerenciado.

1. Anote os endereços DNS mostrados na página Visão geral. Você precisará desses endereços ao configurar o lado Active Directory local da relação de confiança em uma seção a seguir.
1. Reinicie a VM de gerenciamento para que ela receba as novas configurações de DNS e [ingresse a VM no domínio gerenciado](join-windows-vm.md#join-the-vm-to-the-managed-domain).
1. Depois que a VM de gerenciamento for unida ao domínio gerenciado, conecte-se novamente usando a área de trabalho remota.

    Em um prompt de comando, use `nslookup` e o nome da floresta do recurso de domínio gerenciado para validar a resolução de nomes para a floresta de recursos.

    ```console
    nslookup aaddscontoso.com
    ```

    O comando deve retornar dois endereços IP para a floresta de recursos.

## <a name="create-the-forest-trust"></a>Criar a relação de confiança de floresta

A relação de confiança de floresta tem duas partes: a relação de confiança de floresta de saída unidirecional na floresta de recursos de domínio gerenciado e a relação de confiança de floresta de entrada unidirecional na floresta AD DS local. Você cria manualmente os dois lados dessa relação de confiança. Quando ambos os lados são criados, os usuários e os recursos podem se autenticar com êxito usando a relação de confiança de floresta. Uma floresta de recursos de domínio gerenciado dá suporte a relações de confiança de floresta de saída de até 5 1 vias para florestas locais.

### <a name="create-the-managed-domain-side-of-the-trust-relationship"></a>Criar o lado do domínio gerenciado da relação de confiança

Use o `Add-AaddsResourceForestTrust` script para criar o lado do domínio gerenciado da relação de confiança. Primeiro, instale o `Add-AaddsResourceForestTrust` script do [Galeria do PowerShell][powershell-gallery] usando o cmdlet [install-script][Install-Script] :

```powershell
Install-Script -Name Add-AaddsResourceForestTrust
```

Agora forneça ao script as seguintes informações:

| Nome                               | Parâmetro de script     | Descrição |
|:-----------------------------------|:---------------------|:------------|
| Nome de domínio do AD DS do Azure            | *-ManagedDomainFqdn* | FQDN do domínio gerenciado, como *aaddscontoso.com* |
| Nome de domínio AD DS local      | *-TrustFqdn*         | O FQDN da floresta confiável, como *OnPrem.contoso.com* |
| Nome amigável da relação de confiança                | *-TrustFriendlyName* | Nome amigável da relação de confiança. |
| Endereços IP de DNS AD DS locais | *-TrustDnsIPs*       | Uma lista delimitada por vírgulas de endereços IPv4 do servidor DNS para o domínio confiável listado. |
| Senha de confiança                     | *-TrustPassword*     | Uma senha complexa para a relação de confiança. Essa senha também é inserida ao criar a relação de confiança de entrada unidirecional no AD DS local. |
| Credenciais                        | *-Credenciais*       | As credenciais usadas para autenticar no Azure. O usuário deve estar no *grupo de administradores do AAD DC*. Se não for fornecido, o script solicitará a autenticação. |

O exemplo a seguir cria uma relação de confiança chamada *myAzureADDSTrust* para *OnPrem.contoso.com*. Use seus próprios nomes de parâmetro e senhas:.

```azurepowershell
Add-AaddsResourceForestTrust `
    -ManagedDomainFqdn "aaddscontoso.com" `
    -TrustFqdn "onprem.contoso.com" `
    -TrustFriendlyName "myAzureADDSTrust" `
    -TrustDnsIPs "10.0.1.10,10.0.1.11" `
    -TrustPassword <complexPassword>
```

> [!IMPORTANT]
> Lembre-se da sua senha de confiança. Você deve usar a mesma senha quando criar o lado local da relação de confiança.

## <a name="configure-dns-in-the-on-premises-domain"></a>Configurar a DNS no domínio local

Para resolver corretamente o domínio gerenciado do ambiente local, talvez seja necessário adicionar encaminhadores aos servidores DNS existentes. Se você não configurou o ambiente local para se comunicar com o domínio gerenciado, conclua as seguintes etapas de uma estação de trabalho de gerenciamento para o domínio do AD DS local:

1. Selecione **Iniciar | Ferramentas Administrativas | DNS**
1. Selecione com o botão direito do mouse o servidor DNS, como *myAD01* e selecione **Propriedades**
1. Escolha **Encaminhadores** e **Editar** para adicionar encaminhadores adicionais.
1. Adicione os endereços IP do domínio gerenciado, como *10.0.1.4* e *10.0.1.5*.
1. Em um prompt de comando local, valide a resolução de nomes usando o **nslookup** do nome de domínio da floresta do recurso de domínio gerenciado. Por exemplo, `Nslookup aaddscontoso.com` deve retornar os dois endereços IP para a floresta de recursos de domínio gerenciado.

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>Criar a relação de confiança de floresta de entrada no domínio local

O domínio do AD DS local precisa de uma relação de confiança de floresta de entrada para o domínio gerenciado. Essa relação de confiança deve ser criada manualmente no domínio do AD DS local, não pode ser criada no portal do Azure.

Para configurar a relação de confiança de entrada no domínio do AD DS local, conclua as seguintes etapas em uma estação de trabalho de gerenciamento para o domínio do AD DS local:

1. Selecione **Iniciar | Ferramentas Administrativas | Domínios e Relações de Confiança do Active Directory**
1. Selecione com o botão direito do mouse o domínio, como *onprem.contoso.com*, e selecione **Propriedades**
1. Escolha **Relações de Confiança** e, em seguida, **Nova Relação de Confiança**
1. Insira o nome do domínio gerenciado, como *aaddscontoso.com*, e selecione **Avançar**
1. Selecione a opção para criar uma **Relação de confiança de floresta** e, em seguida, para criar uma relação de confiança **Unidirecional: entrada**.
1. Escolha criar a relação de confiança para **Somente este domínio**. Na próxima etapa, você criará a relação de confiança no portal do Azure para o domínio gerenciado.
1. Escolha usar a **Autenticação em toda a floresta**; em seguida, insira a senha da relação de confiança e confirme-a. Essa mesma senha também é inserida no portal do Azure na próxima seção.
1. Percorra as próximas janelas com as opções padrão e, em seguida, escolha a opção **Não, não confirme a relação de confiança de saída**. Não é possível validar a relação de confiança porque sua conta de administrador delegada para a floresta de recursos de domínio gerenciado não tem as permissões necessárias. Este comportamento ocorre por design.
1. Selecione **Concluir**

## <a name="validate-resource-authentication"></a>Validar a autenticação de recurso

Os seguintes cenários comuns permitem que você valide se a relação de confiança de floresta autentica corretamente os usuários e o acesso a recursos:

* [Autenticação de usuário local na floresta de recursos do Azure AD DS](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [Acessar recursos na floresta de recursos do Azure AD DS usando o usuário local](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [Habilitar compartilhamento de arquivos e impressora](#enable-file-and-printer-sharing)
    * [Criar um grupo de segurança e adicionar membros](#create-a-security-group-and-add-members)
    * [Criar um compartilhamento de arquivo para acesso entre florestas](#create-a-file-share-for-cross-forest-access)
    * [Validar a autenticação entre florestas para um recurso](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>Autenticação de usuário local na floresta de recursos do Azure AD DS

Você deve ter a máquina virtual do Windows Server ingressada no domínio do recurso de domínio gerenciado. Use esta máquina virtual para testar se o usuário local pode se autenticar em uma máquina virtual.

1. Conecte-se à VM do Windows Server ingressada na floresta de recursos de domínio gerenciado usando Área de Trabalho Remota e suas credenciais de administrador de domínio gerenciado. Se você receber um erro de Autenticação no Nível da Rede (NLA), verifique se a conta de usuário usada não é uma conta de usuário de domínio.

    > [!TIP]
    > Para se conectar com segurança às suas VMs ingressadas no Azure AD Domain Services, você pode usar o [serviço do host de bastiões do Azure](../bastion/bastion-overview.md) em regiões do Azure com suporte.

1. Abra um prompt de comando e use o comando `whoami` para mostrar o nome diferenciado do usuário autenticado no momento:

    ```console
    whoami /fqdn
    ```

1. Use o comando `runas` para se autenticar como um usuário no domínio local. No comando a seguir, substitua `userUpn@trusteddomain.com` pelo UPN de um usuário do domínio local confiável. O comando solicita a senha do usuário:

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. Se a autenticação for bem-sucedida, um novo prompt de comando será aberto. O título do novo prompt de comando inclui `running as userUpn@trusteddomain.com`.
1. Use `whoami /fqdn` no novo prompt de comando para ver o nome diferenciado do usuário autenticado no Active Directory local.

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>Acessar recursos na floresta de recursos do Azure AD DS usando o usuário local

Usando a VM do Windows Server unida à floresta de recursos de domínio gerenciado, você pode testar o cenário em que os usuários podem acessar recursos hospedados na floresta de recursos quando eles se autenticam de computadores no domínio local com usuários do domínio local. Os exemplos a seguir mostram como criar e testar vários cenários comuns.

#### <a name="enable-file-and-printer-sharing"></a>Habilitar compartilhamento de arquivos e impressora

1. Conecte-se à VM do Windows Server ingressada na floresta de recursos de domínio gerenciado usando Área de Trabalho Remota e suas credenciais de administrador de domínio gerenciado. Se você receber um erro de Autenticação no Nível da Rede (NLA), verifique se a conta de usuário usada não é uma conta de usuário de domínio.

    > [!TIP]
    > Para se conectar com segurança às suas VMs ingressadas no Azure AD Domain Services, você pode usar o [serviço do host de bastiões do Azure](../bastion/bastion-overview.md) em regiões do Azure com suporte.

1. Abra as **Configurações do Windows**, pesquise e selecione **Central de Rede e Compartilhamento**.
1. Escolha a opção para as configurações **Alterar compartilhamento avançado**.
1. No **Perfil de Domínio**, selecione **Ativar compartilhamento de arquivos e impressora** e, em seguida, **Salvar alterações**.
1. Feche a **Central de Rede e Compartilhamento**.

#### <a name="create-a-security-group-and-add-members"></a>Criar um grupo de segurança e adicionar membros

1. Abra **Computadores e Usuários do Active Directory**.
1. Selecione com o botão direito do mouse o nome de domínio, escolha **Novo** e, em seguida, selecione **Unidade Organizacional**.
1. Na caixa de nome, digite *LocalObjects* e, em seguida, selecione **OK**.
1. Selecione e clique com o botão direito do mouse em **LocalObjects** no painel de navegação. Selecione **Novo** e, em seguida, **Grupo**.
1. Digite *FileServerAccess* na caixa **Nome do grupo**. Para o **Escopo do Grupo**, selecione **Local do domínio** e, em seguida, **OK**.
1. No painel de conteúdo, clique duas vezes em **FileServerAccess**. Selecione **Membros**, escolha **Adicionar** e selecione **Locais**.
1. Selecione o Active Directory local na exibição **Localização** e escolha **OK**.
1. Digite os *Usuários de Domínio* na caixa **Insira os nomes de objeto a serem selecionados**. Selecione **Verificar Nomes**, forneça credenciais para o Active Directory local e, em seguida, selecione **OK**.

    > [!NOTE]
    > Você deve fornecer credenciais porque a relação de confiança é somente unidirecional. Isso significa que os usuários do domínio gerenciado não podem acessar recursos ou Pesquisar usuários ou grupos no domínio confiável (local).

1. O grupo **Usuários de Domínio** do seu Active Directory local deve ser membro do grupo **FileServerAccess**. Selecione **OK** para salvar o grupo e fechar a janela.

#### <a name="create-a-file-share-for-cross-forest-access"></a>Criar um compartilhamento de arquivo para acesso entre florestas

1. Na VM do Windows Server ingressada na floresta de recursos de domínio gerenciado, crie uma pasta e forneça um nome como *CrossForestShare*.
1. Selecione com o botão direito do mouse a pasta e escolha **Propriedades**.
1. Na guia **Segurança** e, em seguida, escolha **Editar**.
1. Na caixa de diálogo *Permissões para CrossForestShare*, selecione **Adicionar**.
1. Digite *FileServerAccess* em **Insira os nomes de objeto a serem selecionados** e, em seguida, selecione **OK**.
1. Selecione *FileServerAccess* na lista **Grupos ou nomes de usuário**. Na lista **Permissões para FileServerAccess**, escolha *Permitir* para as permissões **Modificar** e **Gravar**; em seguida, selecione **OK**.
1. Selecione a guia **Compartilhamento** e, em seguida, escolha **Compartilhamento Avançado…**
1. Escolha **Compartilhar esta pasta** e, em seguida, insira um nome fácil de memorizar para o compartilhamento de arquivo em **Nome do compartilhamento** como *CrossForestShare*.
1. Selecione **Permissões**. Na lista **Permissões para Todos**, escolha **Permitir** para a permissão **Alterar**.
1. Selecione **OK** duas vezes e, em seguida, **Fechar**.

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>Validar a autenticação entre florestas para um recurso

1. Entre em um computador Windows ingressado em seu Active Directory local usando uma conta de usuário do Active Directory local.
1. Usando o **Windows Explorer**, conecte-se ao compartilhamento que você criou usando o nome de host totalmente qualificado e o compartilhamento, como `\\fs1.aaddscontoso.com\CrossforestShare`.
1. Para validar a permissão de gravação, selecione com o botão direito do mouse a pasta, escolha **Novo** e, em seguida, selecione **Documento de Texto**. Use o nome padrão **Novo Documento de Texto**.

    Se as permissões de gravação estiverem definidas corretamente, um documento de texto será criado. As etapas a seguir abrirão, editarão e excluirão o arquivo conforme apropriado.
1. Para validar a permissão Ler, abra **Novo Documento de Texto**.
1. Para validar a permissão Modificar, adicione texto ao arquivo e feche o **Bloco de Notas**. Quando for solicitado a salvar as alterações, escolha **Salvar**.
1. Para validar a permissão Excluir, selecione com o botão direito do mouse **Novo Documento de Texto** e escolha **Excluir**. Escolha **Sim** para confirmar a exclusão do arquivo.

## <a name="update-or-remove-outbound-forest-trust"></a>Atualizar ou remover a relação de confiança de floresta de saída

Se precisar atualizar uma floresta de saída unidirecional existente do domínio gerenciado, você poderá usar os `Get-AaddsResourceForestTrusts` `Set-AaddsResourceForestTrust` scripts e. Esses scripts ajudam em cenários em que você deseja atualizar o nome amigável da relação de confiança de floresta ou a senha de confiança. Para remover uma relação de confiança de saída unidirecional do domínio gerenciado, você pode usar o `Remove-AaddsResourceForestTrust` script. Você deve remover manualmente a relação de confiança de floresta de entrada unidirecional na floresta AD DS local associada.

### <a name="update-a-forest-trust"></a>Atualizar uma relação de confiança de floresta

Na operação normal, a floresta de domínio gerenciado e a floresta local negociam um processo de atualização de senha regular entre si. Isso faz parte do processo de segurança normal AD DS relação de confiança. Você não precisa girar manualmente a senha de confiança, a menos que a relação de confiança tenha tido um problema e você queira redefinir manualmente para uma senha conhecida. Para obter mais informações, consulte [alterações de senha de objeto de domínio confiável](concepts-forest-trust.md#tdo-password-changes).

As etapas de exemplo a seguir mostram como atualizar uma relação de confiança existente se você precisar redefinir manualmente a senha de confiança de saída:

1. Instale os `Get-AaddsResourceForestTrusts` `Set-AaddsResourceForestTrust` scripts e do [Galeria do PowerShell][powershell-gallery] usando o cmdlet [install-script][Install-Script] :

    ```powershell
    Install-Script -Name Get-AaddsResourceForestTrusts,Set-AaddsResourceForestTrust
    ```

1. Para poder atualizar uma relação de confiança existente, primeiro obtenha o recurso de confiança usando o `Get-AaddsResourceForestTrusts` script. No exemplo a seguir, a relação de confiança existente é atribuída a um objeto chamado *existingTrust*. Especifique seu próprio nome de floresta de domínio gerenciado e nome de floresta local para atualizar:

    ```powershell
    $existingTrust = Get-AaddsResourceForestTrust `
        -ManagedDomainFqdn "aaddscontoso.com" `
        -TrustFqdn "onprem.contoso.com" `
        -TrustFriendlyName "myAzureADDSTrust"
    ```

1. Para atualizar a senha de confiança existente, use o `Set-AaddsResourceForestTrust` script. Especifique o objeto de confiança existente da etapa anterior e uma nova senha de relação de confiança. Nenhuma complexidade de senha é imposta pelo PowerShell, portanto, certifique-se de gerar e usar uma senha segura para seu ambiente.

    ```powershell
    Set-AaddsResourceForestTrust `
        -Trust $existingTrust `
        -TrustPassword <newComplexPassword>
    ```

### <a name="delete-a-forest-trust"></a>Excluir uma relação de confiança de floresta

Se você não precisar mais da relação de confiança de floresta de saída unidirecional do domínio gerenciado para uma floresta AD DS local, poderá removê-la. Certifique-se de que nenhum aplicativo ou serviço precise se autenticar na floresta AD DS local antes de remover a relação de confiança. Você deve remover manualmente a relação de confiança de entrada unidirecional na floresta AD DS local.

1. Instale o `Remove-AaddsResourceForestTrust` script do [Galeria do PowerShell][powershell-gallery] usando o cmdlet [install-script][Install-Script] :

    ```powershell
    Install-Script -Name Remove-AaddsResourceForestTrust
    ```

1. Agora, remova a relação de confiança de floresta usando o `Remove-AaddsResourceForestTrust` script. No exemplo a seguir, a relação de confiança chamada *myAzureADDSTrust* entre a floresta de domínio gerenciado chamada *aaddscontoso.com* e a floresta local *OnPrem.contoso.com* é removida. Especifique seu próprio nome de floresta de domínio gerenciado e nome de floresta local para remover:

    ```powershell
    Remove-AaddsResourceForestTrust `
        -ManagedDomainFqdn "aaddscontoso.com" `
        -TrustFqdn "onprem.contoso.com" `
        -TrustFriendlyName "myAzureADDSTrust"
    ```

Para remover a relação de confiança de entrada unidirecional da floresta AD DS local, conecte-se a um computador de gerenciamento com acesso à floresta AD DS local e conclua as seguintes etapas:

1. Selecione **Iniciar | Ferramentas Administrativas | Domínios e Relações de Confiança do Active Directory**
1. Selecione com o botão direito do mouse o domínio, como *onprem.contoso.com*, e selecione **Propriedades**
1. Escolha a guia **relações de confiança** e, em seguida, selecione a relação de confiança de entrada existente da floresta de domínio gerenciado.
1. Selecione **remover** e confirme que deseja remover a relação de confiança de entrada.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a:

> [!div class="checklist"]
> * Criar uma floresta de recursos de domínio gerenciado usando Azure PowerShell
> * Criar uma relação de confiança de floresta de saída unidirecional no domínio gerenciado usando Azure PowerShell
> * Configurar o DNS em um ambiente de AD DS local para dar suporte à conectividade de domínio gerenciado
> * Criar uma relação de confiança de floresta de entrada unidirecional em um ambiente do AD DS local
> * Testar e validar a relação de confiança para autenticação e acesso a recursos

Para obter mais informações conceituais sobre os tipos de floresta no Azure AD DS, confira [O que são florestas de recursos?][concepts-forest] e [Como relações de confiança de floresta funcionam no Azure AD DS?][concepts-trust]

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[network-peering]: ../virtual-network/virtual-network-peering-overview.md
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[Get-AzureRMSubscription]: /powershell/module/AzureRM.Profile/Get-AzureRmSubscription
[Install-Script]: /powershell/module/powershellget/install-script

<!-- EXTERNAL LINKS -->
[powershell-gallery]: https://www.powershellgallery.com/