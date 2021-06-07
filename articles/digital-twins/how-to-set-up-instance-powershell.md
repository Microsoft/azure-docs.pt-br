---
title: Configurar uma instância e autenticação (PowerShell)
titleSuffix: Azure Digital Twins
description: Consulte como configurar uma instância do serviço gêmeos do Azure digital usando Azure PowerShell
author: baanders
ms.author: baanders
ms.date: 12/16/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 024b238ef9a6330831ae6cf4dcd6bb72d72dcc74
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98044264"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-powershell"></a>Configurar uma instância e autenticação do gêmeos digital do Azure (PowerShell)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Este artigo aborda as etapas para **Configurar uma nova instância de gêmeos digital do Azure**, incluindo a criação da instância e a configuração da autenticação. Depois de concluir este artigo, você terá uma instância do gêmeos digital do Azure pronta para começar a programar.

Esta versão deste artigo percorre essas etapas manualmente, uma a uma, usando [Azure PowerShell](/powershell/azure/new-azureps-module-az).

* Para percorrer essas etapas manualmente usando o portal do Azure, consulte a versão do portal deste artigo: [*como configurar uma instância e autenticação (Portal)*](how-to-set-up-instance-portal.md).
* Para executar uma configuração automatizada usando um exemplo de script de implantação, consulte a versão com script deste artigo: [*como: configurar uma instância e autenticação (script)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="prepare-your-environment"></a>Prepare o seu ambiente

1. Primeiro, escolha onde executar os comandos neste artigo. Você pode optar por executar comandos do Azure PowerShell usando uma instalação local do Azure PowerShell ou em uma janela do navegador usando [Azure cloud Shell](https://shell.azure.com).
    * Se você optar por usar o Azure PowerShell localmente:
       1. [Instale o módulo Az PowerShell](/powershell/azure/install-az-ps).
       1. Abra uma janela do PowerShell em seu computador.
       1. Conecte-se à sua conta do Azure usando o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).
    * Se você optar por usar o Azure Cloud Shell:
        1. Consulte [visão geral de Azure cloud Shell](../cloud-shell/overview.md) para obter mais informações sobre Cloud Shell.
        1. Abra uma janela de Cloud Shell seguindo [este link](https://shell.azure.com) no seu navegador.
        1. Na barra de ícones Cloud Shell, verifique se o Cloud Shell está configurado para executar a versão do PowerShell.
    
            :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="Janela Cloud Shell mostrando a seleção da versão do PowerShell":::
    
1. Se tiver várias assinaturas do Azure, escolha a que for adequada para cobrança do recurso. Selecione uma assinatura específica usando o cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

   ```azurepowershell-interactive
   Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
   ```

1. Se esta for a primeira vez que você usa o Azure digital gêmeos com essa assinatura, você deverá registrar o provedor de recursos **Microsoft. DigitalTwins** . (Se você não tiver certeza disso, não haverá problemas em executá-lo novamente mesmo que já tenha feito isso no passado.)

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.DigitalTwins
   ```

1. Use o comando a seguir para instalar o módulo do PowerShell **AZ. DigitalTwins** .
    ```azurepowershell-interactive
    Install-Module -Name Az.DigitalTwins
    ```

> [!IMPORTANT]
> Enquanto o módulo **AZ. DigitalTwins** do PowerShell está em versão prévia, você deve instalá-lo separadamente usando o `Install-Module` cmdlet descrito acima. Depois que esse módulo do PowerShell estiver em disponibilidade geral, ele fará parte das versões futuras do módulo Az PowerShell e estará disponível por padrão no Azure Cloud Shell.

## <a name="create-the-azure-digital-twins-instance"></a>Criar a instância de gêmeos digital do Azure

Nesta seção, você **criará uma nova instância do Azure digital gêmeos** usando Azure PowerShell.
Você precisará fornecer:

* Um [grupo de recursos do Azure](../azure-resource-manager/management/overview.md) em que a instância será implantada. Se você ainda não tiver um grupo de recursos existente, poderá criar um usando o cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) :

  ```azurepowershell-interactive
  New-AzResourceGroup -Name <name-for-your-resource-group> -Location <region>
  ```

* Uma região para a implantação. Para ver quais regiões dão suporte ao Azure digital gêmeos, visite [*produtos do Azure disponíveis por região*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* Um nome para sua instância. O nome da nova instância deve ser exclusivo dentro da região para sua assinatura. Se sua assinatura tiver outra instância de gêmeos digital do Azure na região que já está usando o nome especificado, você será solicitado a escolher um nome diferente.

Use seus valores no comando a seguir para criar a instância:

```azurepowershell-interactive
New-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> -Location <region>
```

### <a name="verify-success-and-collect-important-values"></a>Verificar o êxito e coletar valores importantes

Se a instância foi criada com êxito, o resultado será semelhante à seguinte saída que contém informações sobre o recurso que você criou:

```Output
Location   Name                                         Type
--------   ----                                         ----
<region>   <name-for-your-Azure-Digital-Twins-instance> Microsoft.DigitalTwins/digitalTwinsInstances
```

Em seguida, exiba as propriedades da nova instância Executando e direcionando `Get-AzDigitalTwinsInstance` para `Select-Object -Property *` , da seguinte maneira:

```azurepowershell-interactive
Get-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> |
  Select-Object -Property *
```

> [!TIP]
> Você pode usar esse comando para ver todas as propriedades de sua instância a qualquer momento.

Anote o nome de **host** da instância do Azure digital gêmeos, o **Name** e o **resourcegroup**. Esses são valores importantes que podem ser necessários à medida que você continuar trabalhando com sua instância do gêmeos digital do Azure, para configurar a autenticação e os recursos do Azure relacionados. Se outros usuários estiverem programando em relação à instância, você deverá compartilhar esses valores com eles.

Agora você tem uma instância de gêmeos digital do Azure pronta para uso. Em seguida, você dará as permissões apropriadas de usuário do Azure para gerenciá-lo.

## <a name="set-up-user-access-permissions"></a>Configurar permissões de acesso do usuário

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Primeiro, determine o **ObjectID** da conta do Azure AD do usuário que deve receber a função. Você pode encontrar esse valor usando o cmdlet [Get-AzAdUser](/powershell/module/az.resources/get-azaduser) , passando o nome principal do usuário na conta do Azure ad para recuperar seu ObjectId (e outras informações do usuário). Na maioria dos casos, o nome principal do usuário corresponderá ao email do usuário na conta do Azure AD.

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName <Azure-AD-user-principal-name-of-user-to-assign>
```

Em seguida, use o **ObjectID** no comando a seguir para atribuir a função. O comando também exige que você insira a mesma ID de assinatura, o nome do grupo de recursos e o nome da instância do gêmeos digital do Azure que você escolheu anteriormente ao criar a instância. O comando deve ser executado por um usuário com [permissões suficientes](#prerequisites-permission-requirements) na assinatura do Azure.

```azurepowershell-interactive
$Params = @{
  ObjectId = '<Azure-AD-user-object-id-of-user-to-assign>'
  RoleDefinitionName = 'Azure Digital Twins Data Owner'
  Scope = '/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<name-for-your-Azure-Digital-Twins-instance>'
}
New-AzRoleAssignment @Params
```

O resultado desse comando são informações de saída sobre a atribuição de função que foi criada.

### <a name="verify-success"></a>Verificar êxito

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

Agora você tem uma instância de gêmeos digital do Azure pronta para uso e atribuiu permissões para gerenciá-la.

## <a name="next-steps"></a>Próximas etapas

Consulte Como conectar um aplicativo cliente à sua instância com o código de autenticação:
* [*Como: escrever código de autenticação do aplicativo*](how-to-authenticate-client.md)
