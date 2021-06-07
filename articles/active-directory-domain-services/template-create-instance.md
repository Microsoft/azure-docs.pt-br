---
title: Habilitar o Azure DS Domain Services usando um modelo | Microsoft Docs
description: Saiba como configurar e habilitar o Azure Active Directory Domain Services usando um modelo do Azure Resource Manager
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: sample
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: e18825da64d0d200f55ce72985ac843b93b1e612
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618783"
---
# <a name="create-an-azure-active-directory-domain-services-managed-domain-using-an-azure-resource-manager-template"></a>Criar um domínio gerenciado do Azure Active Directory Domain Services usando um modelo do Azure Resource Manager

O Azure AD DS (Azure Active Directory Domain Services) fornece serviços de domínio gerenciado, como ingresso no domínio, Política de Grupo, LDAP e autenticação Kerberos/NTLM, que são totalmente compatíveis com o Active Directory do Windows Server. Você consome esses serviços de domínio sem implantar, gerenciar e aplicar patches aos controladores de domínio por conta própria. O Azure AD DS integra-se com o seu locatário existente do Azure AD. Essa integração permite que os usuários entrem usando suas credenciais corporativas e você pode usar os grupos e as contas de usuário existentes para proteger o acesso aos recursos.

Este artigo mostra como criar um domínio gerenciado usando um modelo do Azure Resource Manager. Os recursos de suporte são criados usando o Azure PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa dos seguintes recursos:

* Instalar e configurar o PowerShell do Azure.
    * Se necessário, siga as instruções para [instalar o módulo do Azure PowerShell e conectar-se à sua assinatura do Azure](/powershell/azure/install-az-ps).
    * Lembre-se de entrar em sua assinatura do Azure usando o cmdlet [Connect-AzAccount][Connect-AzAccount].
* Instale e configure o Azure AD PowerShell.
    * Se necessário, siga as instruções para [instalar o módulo do Azure AD PowerShell e conectar-se ao Azure AD](/powershell/azure/active-directory/install-adv2).
    * Lembre-se de entrar em seu locatário do Azure AD usando o cmdlet [Connect-AzureAD][Connect-AzureAD].
* É necessário ter privilégios de *administrador global* no locatário do Azure AD para habilitar o Azure AD DS.
* Você precisa de privilégios de *Colaborador* em sua assinatura do Azure para criar os recursos necessários do Azure AD DS.

## <a name="dns-naming-requirements"></a>Requisitos de nomenclatura de DNS

Ao criar um domínio gerenciado do Azure AD DS, você especifica um nome DNS. Eis algumas considerações a serem feitas ao escolher o nome DNS:

* **Nome de domínio interno:** Por padrão, o nome de domínio interno do diretório é usado (com sufixo *.onmicrosoft.com*). Se quiser habilitar o acesso LDAP seguro ao domínio gerenciado pela Internet, você não poderá criar um certificado digital para proteger a conexão com esse domínio padrão. A Microsoft é proprietária do domínio *.onmicrosoft.com*, portanto, nenhuma AC (Autoridade de Certificação) emitirá um certificado.
* **Nomes de domínio personalizados:** A abordagem mais comum é especificar um nome de domínio personalizado, normalmente um que você já tenha e seja roteável. Quando você usa um domínio roteável personalizado, o tráfego pode fluir corretamente conforme necessário para dar suporte aos seus aplicativos.
* **Sufixos de domínio não roteáveis:** De modo geral, recomendamos que você evite sufixos de nome de domínio não roteáveis, tal como *contoso.local*. O sufixo *.local* não é roteável e pode causar problemas com a resolução do DNS.

> [!TIP]
> Se você criar um nome de domínio personalizado, tome cuidado com os namespaces DNS existentes. Recomendamos usar um nome de domínio separado de qualquer namespace DNS local ou do Azure existente.
>
> Por exemplo, se você tiver um namespace DNS existente *contoso.com*, crie um domínio gerenciado com o nome de domínio personalizado *aaddscontoso.com*. Caso precise usar o LDAP Seguro, registre e seja o proprietário desse nome de domínio personalizado para gerar os certificados necessários.
>
> Talvez seja necessário criar alguns registros DNS adicionais para outros serviços no ambiente ou encaminhadores DNS condicionais entre os namespaces DNS existentes no ambiente. Por exemplo, se você executar um servidor Web que hospeda um site usando o nome DNS raiz, poderá haver conflitos de nomenclatura que exigem entradas DNS adicionais.
>
> Neste exemplo e nestes artigos de instruções, o domínio personalizado *aaddscontoso.com* é usado como um breve exemplo. Em todos os comandos, especifique o próprio nome de domínio.

As seguintes restrições de nome DNS também se aplicam:

* **Restrições do prefixo do domínio:** Você não pode criar um domínio gerenciado com um prefixo de mais de 15 caracteres. O prefixo do nome de domínio especificado (como *aaddscontoso* no nome de domínio *aaddscontoso.com*) precisa conter até 15 caracteres.
* **Conflitos de nome de rede:** O nome de domínio DNS para seu domínio gerenciado não deve já existir na rede virtual. Especificamente, verifique os seguintes cenários que podem resultar em conflitos de nome:
    * Se você já tiver um domínio do Active Directory com o mesmo nome de domínio DNS na rede virtual do Azure.
    * Se a rede virtual em que você planeja habilitar o domínio gerenciado tiver uma conexão VPN com sua rede local. Nesse cenário, verifique se você não tem um domínio com o mesmo nome de domínio DNS na rede local.
    * Se você tiver um serviço de nuvem existente do Azure com esse mesmo nome na rede virtual do Azure.

## <a name="create-required-azure-ad-resources"></a>Criar recursos do Azure AD necessários

O Azure AD DS requer uma entidade de serviço e um grupo do Azure AD. Esses recursos permitem que o domínio gerenciado sincronize dados e defina quais usuários têm permissões administrativas no domínio gerenciado.

Primeiro, registre o provedor de recursos do Azure AD Domain Services usando o cmdlet [Register-AzResourceProvider][Register-AzResourceProvider]:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Crie uma entidade de serviço do Azure AD usando o cmdlet [New-AzureADServicePrincipal][New-AzureADServicePrincipal] para o Azure AD DS se comunicar e autenticar-se. É usada uma ID de aplicativo específica denominada *Serviços de Controlador de Domínio* com uma ID de *6ba9a5d4-8456-4118-b521-9c5ca10cdf84*. Não altere essa ID de aplicativo.

```powershell
New-AzureADServicePrincipal -AppId "6ba9a5d4-8456-4118-b521-9c5ca10cdf84"
```

Agora crie um grupo do Azure AD chamado *Administradores do AAD DC* usando o cmdlet [New-AzureADGroup][New-AzureADGroup]. Os usuários adicionados a esse grupo recebem permissões para executar tarefas de administração no domínio gerenciado.

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Com o grupo *Administradores do AAD DC* criado, adicione um usuário a ele usando o cmdlet [Add-AzureADGroupMember][Add-AzureADGroupMember]. Primeiro, você obtém a ID de objeto do grupo *Administradores do AAD DC* usando o cmdlet [Get-AzureADGroup][Get-AzureADGroup] e, em seguida, a ID de objeto do usuário desejado usando o cmdlet [Get-AzureADUser][Get-AzureADUser].

No exemplo a seguir, a ID de objeto do usuário para a conta com um UPN de `admin@contoso.onmicrosoft.com`. Substitua essa conta de usuário pelo UPN do usuário que você deseja adicionar ao grupo *Administradores do AAD DC*:

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

Finalmente, crie um grupo de recursos usando o cmdlet [New-AzResourceGroup][New-AzResourceGroup]. No exemplo a seguir, o grupo de recursos é chamado *myResourceGroup* e é criado na região *westus*. Use seu nome e sua região desejada:

```powershell
New-AzResourceGroup `
  -Name "myResourceGroup" `
  -Location "WestUS"
```

Se você escolher uma região com suporte a Zonas de Disponibilidade, os recursos do Azure AD DS serão distribuídos entre as zonas para proporcionar redundância adicional. As Zonas de Disponibilidade são locais físicos exclusivos em uma região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, resfriamento e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas.

Não é preciso configurar nada para que o Azure AD DS seja distribuído entre as zonas. A plataforma do Azure lida automaticamente com a distribuição de recursos na zona. Para obter mais informações e ver a disponibilidade da região, confira [O que são as Zonas de Disponibilidade no Azure?][availability-zones].

## <a name="resource-definition-for-azure-ad-ds"></a>Definição de recurso para Azure AD DS

Como parte da definição de recurso do Resource Manager, os seguintes parâmetros de configuração são necessários:

| Parâmetro               | Valor |
|-------------------------|---------|
| domainName              | O nome de domínio DNS para seu domínio gerenciado, levando em consideração os pontos anteriores sobre prefixos e conflitos de nomenclatura. |
| filteredSync            | O Azure AD DS permite que você sincronize *todos* os usuários e grupos disponíveis no Azure AD ou uma sincronização *com escopo* incluindo apenas grupos específicos.<br /><br /> Para mais informações sobre a sincronização com escopo, confira [Sincronização com do Azure AD Domain Services][scoped-sync].|
| notificationSettings    | Se houver alertas gerados no domínio gerenciado, as notificações por email poderão ser enviadas. <br /><br />*Administradores globais* do locatário do Azure e membros do grupo de *Administradores do AAD DC* podem ser *Habilitados* para essas notificações.<br /><br /> Se desejar, você poderá adicionar destinatários adicionais para notificações quando houver alertas que exijam atenção.|
| domainConfigurationType | Por padrão, um domínio gerenciado é criado como uma floresta de *Usuários*. Esse tipo de floresta sincroniza todos os objetos do Azure AD, incluindo qualquer conta de usuário criada em um ambiente do AD DS local. Você não precisa especificar um valor *domainConfiguration* para criar uma floresta de usuário.<br /><br /> Uma floresta de *Recursos* sincroniza apenas usuários e grupos criados diretamente no Azure AD. Defina o valor como *ResourceTrusting* para criar uma floresta de recursos.<br /><br />Para saber mais sobre florestas de *Recursos*, incluindo por que você usaria uma e como criar relações de confiança das floresta com domínios locais do AD DS, confira [Visão geral das florestas de recursos do Azure AD DS][resource-forests].|

A definição de parâmetros condensada a seguir mostra como esses valores são declarados. Uma floresta de usuário chamada *aaddscontoso.com* é criada com todos os usuários do Azure AD sincronizados com o domínio gerenciado:

```json
"parameters": {
    "domainName": {
        "value": "aaddscontoso.com"
    },
    "filteredSync": {
        "value": "Disabled"
    },
    "notificationSettings": {
        "value": {
            "notifyGlobalAdmins": "Enabled",
            "notifyDcAdmins": "Enabled",
            "additionalRecipients": []
        }
    },
    [...]
}
```

O tipo de recurso de modelo do Resource Manager condensado a seguir é usado para definir e criar o domínio gerenciado. Uma rede virtual e uma sub-rede do Azure já devem existir ou ser criadas como parte do modelo do Resource Manager. O domínio gerenciado está conectado a essa sub-rede.

```json
"resources": [
    {
        "apiVersion": "2017-06-01",
        "type": "Microsoft.AAD/DomainServices",
        "name": "[parameters('domainName')]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
        ],
        "properties": {
            "domainName": "[parameters('domainName')]",
            "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
            "filteredSync": "[parameters('filteredSync')]",
            "notificationSettings": "[parameters('notificationSettings')]"
        }
    },
    [...]
]
```

Esses parâmetros e o tipo de recurso podem ser usados como parte de um modelo mais amplo do Resource Manager para implantar um domínio gerenciado, conforme mostrado na seção a seguir.

## <a name="create-a-managed-domain-using-sample-template"></a>Criar um domínio gerenciado usando o modelo de exemplo

O modelo de exemplo completo do Resource Manager a seguir cria um domínio gerenciado e a rede virtual, a sub-rede e as regras do grupo de segurança de rede de suporte. As regras do grupo de segurança de rede são necessárias para proteger o domínio gerenciado e verificar se o tráfego pode fluir corretamente. Uma floresta de usuário com o nome DNS de *aaddscontoso.com* é criada, com todos os usuários sincronizados do Azure AD:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "apiVersion": {
            "value": "2017-06-01"
        },
        "domainConfigurationType": {
            "value": "FullySynced"
        },
        "domainName": {
            "value": "aaddscontoso.com"
        },
        "filteredSync": {
            "value": "Disabled"
        },
        "location": {
            "value": "westus"
        },
        "notificationSettings": {
            "value": {
                "notifyGlobalAdmins": "Enabled",
                "notifyDcAdmins": "Enabled",
                "additionalRecipients": []
            }
        },
        "subnetName": {
            "value": "aadds-subnet"
        },
        "vnetName": {
            "value": "aadds-vnet"
        },
        "vnetAddressPrefixes": {
            "value": [
                "10.1.0.0/24"
            ]
        },
        "subnetAddressPrefix": {
            "value": "10.1.0.0/24"
        },
        "nsgName": {
            "value": "aadds-nsg"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-06-01",
            "type": "Microsoft.AAD/DomainServices",
            "name": "[parameters('domainName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
            ],
            "properties": {
                "domainName": "[parameters('domainName')]",
                "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
                "filteredSync": "[parameters('filteredSync')]",
                "domainConfigurationType": "[parameters('domainConfigurationType')]",
                "notificationSettings": "[parameters('notificationSettings')]"
            }
        },
        {
            "type": "Microsoft.Network/NetworkSecurityGroups",
            "name": "[parameters('nsgName')]",
            "location": "[parameters('location')]",
            "properties": {
                "securityRules": [
                    {
                        "name": "AllowSyncWithAzureAD",
                        "properties": {
                            "access": "Allow",
                            "priority": 101,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "443"
                        }
                    },
                    {
                        "name": "AllowPSRemoting",
                        "properties": {
                            "access": "Allow",
                            "priority": 301,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "5986"
                        }
                    },
                    {
                        "name": "AllowRD",
                        "properties": {
                            "access": "Allow",
                            "priority": 201,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "CorpNetSaw",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "3389"
                        }
                    }
                ]
            },
            "apiVersion": "2018-04-01"
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('vnetName')]",
            "location": "[parameters('location')]",
            "apiVersion": "2018-04-01",
            "dependsOn": [
                "[concat('Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
            ],
            "properties": {
                "addressSpace": {
                    "addressPrefixes": "[parameters('vnetAddressPrefixes')]"
                },
                "subnets": [
                    {
                        "name": "[parameters('subnetName')]",
                        "properties": {
                            "addressPrefix": "[parameters('subnetAddressPrefix')]",
                            "networkSecurityGroup": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
                            }
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {}
}
```

Esse modelo pode ser implantado usando seu método de implantação preferencial, como o [portal do Azure][portal-deploy], o [Azure PowerShell][powershell-deploy] ou um pipeline de CI/CD. O exemplo a seguir usa o cmdlet [New-AzResourceGroupDeployment][New-AzResourceGroupDeployment]. Especifique o nome do grupo de recursos e o nome do arquivo do modelo:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myResourceGroup" -TemplateFile <path-to-template>
```

Leva alguns minutos para criar o recurso e retornar o controle para o prompt do PowerShell. O domínio gerenciado continua sendo provisionado em segundo plano e a implantação pode levar até uma hora para ser concluída. No portal do Azure, a página **Visão Geral** do seu domínio gerenciado mostra o status atual durante essa fase de implantação.

Quando o portal do Azure mostra que o domínio gerenciado concluiu o provisionamento, as seguintes tarefas precisam ser concluídas:

* Atualize as configurações de DNS da rede virtual, de modo que as máquinas virtuais possam encontrar o domínio gerenciado para ingresso no domínio ou autenticação.
    * Para configurar o DNS, selecione o domínio gerenciado no portal. Na janela **Visão Geral**, você deve definir automaticamente essas configurações de DNS.
* [Habilite a sincronização de senha para o Azure AD DS](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) para que os usuários possam entrar no domínio gerenciado usando as credenciais corporativas deles.

## <a name="next-steps"></a>Próximas etapas

Para ver o domínio gerenciado em ação, você pode [ingressar uma VM do Windows em domínio][windows-join], [configurar o LDAP Seguro][tutorial-ldaps] e [configurar a sincronização de hash de senha][tutorial-phs].

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md
[availability-zones]: ../availability-zones/az-overview.md
[portal-deploy]: ../azure-resource-manager/templates/deploy-portal.md
[powershell-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[scoped-sync]: scoped-synchronization.md
[resource-forests]: concepts-resource-forest.md

<!-- EXTERNAL LINKS -->
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[New-AzureADGroup]: /powershell/module/AzureAD/New-AzureADGroup
[Add-AzureADGroupMember]: /powershell/module/AzureAD/Add-AzureADGroupMember
[Get-AzureADGroup]: /powershell/module/AzureAD/Get-AzureADGroup
[Get-AzureADUser]: /powershell/module/AzureAD/Get-AzureADUser
[Register-AzResourceProvider]: /powershell/module/Az.Resources/Register-AzResourceProvider
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: ../cloud-shell/cloud-shell-windows-users.md
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain
[New-AzResourceGroupDeployment]: /powershell/module/Az.Resources/New-AzResourceGroupDeployment