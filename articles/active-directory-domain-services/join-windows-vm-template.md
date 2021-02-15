---
title: Usar um modelo para unir uma VM do Windows ao Azure AD DS | Microsoft Docs
description: Saiba como usar modelos de Azure Resource Manager para unir uma VM do Windows Server nova ou existente a um domínio Azure Active Directory Domain Services gerenciado.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: e7245e8e468ea051ee095d97cc250ad303aa80a5
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619429"
---
# <a name="join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain-using-a-resource-manager-template"></a>Ingressar uma máquina virtual do Windows Server em um Azure Active Directory Domain Services domínio gerenciado usando um modelo do Resource Manager

Para automatizar a implantação e a configuração de VMs (máquinas virtuais) do Azure, você pode usar um modelo do Resource Manager. Esses modelos permitem criar implantações consistentes a cada vez. As extensões também podem ser incluídas em modelos para configurar automaticamente uma VM como parte da implantação. Uma extensão útil une VMs a um domínio, que pode ser usada com domínios gerenciados Azure Active Directory Domain Services (AD DS do Azure).

Este artigo mostra como criar e ingressar uma VM do Windows Server em um domínio gerenciado AD DS do Azure usando modelos do Resource Manager. Você também aprende como unir uma VM do Windows Server existente a um domínio de AD DS do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes recursos e privilégios:

* Uma assinatura ativa do Azure.
    * Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um domínio gerenciado do Azure Active Directory Domain Services habilitado e configurado no locatário do Azure AD.
    * Se necessário, o primeiro tutorial [cria e configura um domínio gerenciado do Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Uma conta de usuário que é parte do domínio gerenciado.

## <a name="azure-resource-manager-template-overview"></a>Visão geral do modelo de Azure Resource Manager

Os modelos do Resource Manager permitem definir a infraestrutura do Azure no código. Os recursos necessários, as conexões de rede ou a configuração de VMs podem ser todos definidos em um modelo. Esses modelos criam implantações consistentes e reproduzíveis a cada vez e podem ter controle de versão à medida que você faz alterações. Para obter mais informações, consulte [visão geral dos modelos de Azure Resource Manager][template-overview].

Cada recurso é definido em um modelo usando JavaScript Object Notation (JSON). O exemplo JSON a seguir usa o tipo de recurso *Microsoft. Compute/virtualMachines/Extensions* para instalar a Active Directory extensão de ingresso no domínio. São usados parâmetros que você especifica no momento da implantação. Quando a extensão é implantada, a VM é unida ao domínio gerenciado especificado.

```json
 {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('dnsLabelPrefix'),'/joindomain')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('dnsLabelPrefix'))]"
      ],
      "properties": {
        "publisher": "Microsoft.Compute",
        "type": "JsonADDomainExtension",
        "typeHandlerVersion": "1.3",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "Name": "[parameters('domainToJoin')]",
          "OUPath": "[parameters('ouPath')]",
          "User": "[concat(parameters('domainToJoin'), '\\', parameters('domainUsername'))]",
          "Restart": "true",
          "Options": "[parameters('domainJoinOptions')]"
        },
        "protectedSettings": {
          "Password": "[parameters('domainPassword')]"
        }
      }
    }
```

Essa extensão de VM pode ser implantada mesmo que você não crie uma VM no mesmo modelo. Os exemplos neste artigo mostram as duas abordagens a seguir:

* [Criar uma VM do Windows Server e ingressar em um domínio gerenciado](#create-a-windows-server-vm-and-join-to-a-managed-domain)
* [Unir uma VM do Windows Server existente em um domínio gerenciado](#join-an-existing-windows-server-vm-to-a-managed-domain)

## <a name="create-a-windows-server-vm-and-join-to-a-managed-domain"></a>Criar uma VM do Windows Server e ingressar em um domínio gerenciado

Se precisar de uma VM do Windows Server, você poderá criar e configurar uma usando um modelo do Resource Manager. Quando a VM é implantada, uma extensão é instalada para ingressar a VM em um domínio gerenciado. Se você já tiver uma VM que deseja ingressar em um domínio gerenciado, pule para [ingressar uma VM do Windows Server existente em um domínio gerenciado](#join-an-existing-windows-server-vm-to-a-managed-domain).

Para criar uma VM do Windows Server e, em seguida, associá-la a um domínio gerenciado, conclua as seguintes etapas:

1. Navegue até o [modelo de início rápido](https://azure.microsoft.com/resources/templates/201-vm-domain-join/). Selecione a opção para **implantar no Azure**.
1. Na página **implantação personalizada** , insira as informações a seguir para criar e ingressar uma VM do Windows Server para o domínio gerenciado:

    | Configuração                   | Valor |
    |---------------------------|-------|
    | Subscription              | Escolha a mesma assinatura do Azure em que você habilitou o Azure AD Domain Services. |
    | Resource group            | Escolha o grupo de recursos para sua VM. |
    | Location                  | Selecione o local de para sua VM. |
    | Nome da VNET existente        | O nome da rede virtual existente à qual conectar a VM, como *myVnet*. |
    | Nome da sub-rede existente      | O nome da sub-rede de rede virtual existente, como *cargas de trabalho*. |
    | Prefixo do rótulo DNS          | Insira um nome DNS a ser usado para a VM, como *MyVM*. |
    | Tamanho da VM                   | Especifique um tamanho de VM, como *Standard_DS2_v2*. |
    | Domínio para ingressar            | O nome DNS do domínio gerenciado, como *aaddscontoso.com*. |
    | Nome de usuário de domínio           | A conta de usuário no domínio gerenciado que deve ser usada para ingressar a VM no domínio gerenciado, como `contosoadmin@aaddscontoso.com` . Essa conta deve fazer parte do domínio gerenciado. |
    | Senha do domínio           | A senha da conta de usuário especificada na configuração anterior. |
    | Caminho de UO opcional          | A UO personalizada na qual adicionar a VM. Se você não especificar um valor para esse parâmetro, a VM será adicionada à UO *computadores DC do AAD* padrão. |
    | Nome de usuário do administrador da VM         | Especifique uma conta de administrador local para criar na VM. |
    | Senha de administrador da VM         | Especifique uma senha de administrador local para a VM. Crie uma senha forte de administrador local para proteger contra ataques de força bruta de senha. |

1. Examine os termos e condições e marque a caixa para **que eu concorde com os termos e condições declarados acima**. Quando estiver pronto, selecione **comprar** para criar e ingressar a VM no domínio gerenciado.

> [!WARNING]
> **Trate as senhas com cuidado.**
> O arquivo de parâmetro de modelo solicita a senha para uma conta de usuário que faz parte do domínio gerenciado. Não insira manualmente valores nesse arquivo e deixe-o acessível em compartilhamentos de arquivos ou em outros locais compartilhados.

Demora alguns minutos para a implantação ser concluída com êxito. Quando terminar, a VM do Windows é criada e unida ao domínio gerenciado. A VM pode ser gerenciada ou conectada usando contas de domínio.

## <a name="join-an-existing-windows-server-vm-to-a-managed-domain"></a>Unir uma VM do Windows Server existente em um domínio gerenciado

Se você tiver uma VM existente ou um grupo de VMs que deseja unir a um domínio gerenciado, poderá usar um modelo do Resource Manager para simplesmente implantar a extensão de VM.

Para ingressar uma VM do Windows Server existente em um domínio gerenciado, conclua as seguintes etapas:

1. Navegue até o [modelo de início rápido](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/). Selecione a opção para **implantar no Azure**.
1. Na página **implantação personalizada** , insira as informações a seguir para ingressar a VM no domínio gerenciado:

    | Configuração                   | Valor |
    |---------------------------|-------|
    | Subscription              | Escolha a mesma assinatura do Azure em que você habilitou o Azure AD Domain Services. |
    | Resource group            | Escolha o grupo de recursos com sua VM existente. |
    | Location                  | Selecione o local da VM existente. |
    | Lista de VMs                   | Insira a lista separada por vírgulas das VMs existentes para ingressar no domínio gerenciado, como *myVM1, myVM2*. |
    | Nome de usuário de ingresso no domínio     | A conta de usuário no domínio gerenciado que deve ser usada para ingressar a VM no domínio gerenciado, como `contosoadmin@aaddscontoso.com` . Essa conta deve fazer parte do domínio gerenciado. |
    | Senha do usuário de ingresso no domínio | A senha da conta de usuário especificada na configuração anterior. |
    | Caminho de UO opcional          | A UO personalizada na qual adicionar a VM. Se você não especificar um valor para esse parâmetro, a VM será adicionada à UO *computadores DC do AAD* padrão. |

1. Examine os termos e condições e marque a caixa para **que eu concorde com os termos e condições declarados acima**. Quando estiver pronto, selecione **comprar** para ingressar a VM no domínio gerenciado.

> [!WARNING]
> **Trate as senhas com cuidado.**
> O arquivo de parâmetro de modelo solicita a senha para uma conta de usuário que faz parte do domínio gerenciado. Não insira manualmente valores nesse arquivo e deixe-o acessível em compartilhamentos de arquivos ou em outros locais compartilhados.

Demora alguns minutos para a implantação ser concluída com êxito. Quando terminar, as VMs do Windows especificadas serão Unidas ao domínio gerenciado e poderão ser gerenciadas ou conectadas usando contas de domínio.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você usou a portal do Azure para configurar e implantar recursos usando modelos. Você também pode implantar recursos com modelos do Resource Manager usando [Azure PowerShell][deploy-powershell] ou o [CLI do Azure][deploy-cli].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[template-overview]: ../azure-resource-manager/templates/overview.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
