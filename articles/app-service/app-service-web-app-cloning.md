---
title: Clonar aplicativo com o PowerShell
description: Saiba como clonar o seu Serviço de Aplicativo para um novo aplicativo usando o PowerShell. Uma variedade de cenários de clonagem é abordada, incluindo A integração do Gerenciador de tráfego.
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.topic: article
ms.date: 01/14/2016
ms.custom: seodec18
ms.openlocfilehash: e3ae342e7cbd8a9c2e126de7666d07f0664be407
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103573635"
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Clonagem de aplicativo do Serviço de Aplicativo do Azure usando o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Com o lançamento do Microsoft Azure PowerShell versão 1.1.0, uma nova opção foi adicionada ao `New-AzWebApp` que lhe permite clonar um aplicativo de Serviço existente como um aplicativo recém-criado em uma região diferente ou na mesma região. Essa opção permitirá que os clientes implantem vários aplicativos em diferentes regiões de forma rápida e fácil.

A clonagem de aplicativo tem suporte para planos de serviço de aplicativo padrão, Premium, Premium V2 e isolados. O novo recurso usa as mesmas limitações que o recurso de Backup de Serviço de Aplicativo; veja [Fazer backup de um aplicativo no Serviço de Aplicativo do Azure](manage-backup.md).

## <a name="cloning-an-existing-app"></a>Clonagem de um aplicativo existente
Cenário: um aplicativo existente na região do Sul EUA Central e você deseja clonar o conteúdo para um novo aplicativo na região do Norte EUA Central. Isso pode ser feito usando a versão do cmdlet do PowerShell do Azure Resource Manager para criar um novo aplicativo com a opção `-SourceWebApp`.

Se soubermos o nome do grupo de recursos que contém o aplicativo de origem, poderemos usar o seguinte comando do PowerShell para obter as informações do aplicativo de origem (nesse caso, o nome é `source-webapp`):

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Para criar um novo plano do Serviço de Aplicativo, podemos usar comando `New-AzAppServicePlan` como no exemplo a seguir

```powershell
New-AzAppServicePlan -Location "North Central US" -ResourceGroupName DestinationAzureResourceGroup -Name DestinationAppServicePlan -Tier Standard
```

Usando o `New-AzWebApp` comando, você pode criar o novo aplicativo na região do norte EUA Central e associá-lo a um plano do serviço de aplicativo existente. Além disso, você pode usar o mesmo grupo de recursos do aplicativo de origem ou definir um novo grupo de recursos, como mostrado no comando a seguir:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

Para clonar um aplicativo existente, incluindo todos os slots de implantação associados, você precisa usar o parâmetro `IncludeSourceWebAppSlots`.  Observe que o `IncludeSourceWebAppSlots` parâmetro só tem suporte para clonagem de um aplicativo inteiro, incluindo todos os seus slots. O comando do PowerShell a seguir demonstra o uso desse parâmetro com o comando `New-AzWebApp`:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

Para clonar um aplicativo existente na mesma região, você precisa criar um novo grupo de recursos e um novo plano do serviço de aplicativo na mesma região e usar o comando do PowerShell a seguir para clonar o aplicativo:

```powershell
$destapp = New-AzWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcapp
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Clonagem de um aplicativo existente como um Ambiente do Serviço de Aplicativo
Cenário: um aplicativo existente na região do Sul EUA Central e você deseja clonar o conteúdo para um novo aplicativo em um Ambiente do Serviço de Aplicativo (ASE) existente.

Se soubermos o nome do grupo de recursos que contém o aplicativo de origem, poderemos usar o seguinte comando do PowerShell para obter as informações do aplicativo de origem (nesse caso, o nome é `source-webapp`):

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Sabendo o nome do ASE e o nome do grupo de recursos ao qual o ASE pertence, você pode criar o novo aplicativo no ASE existente, conforme mostrado no comando a seguir:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

O parâmetro `Location` é necessário devido ao legado, mas é ignorado quando você criar o aplicativo em um ASE. 

## <a name="cloning-an-existing-app-slot"></a>Clonagem de um slot de aplicativo existente
Cenário: você deseja clonar um slot de implantação existente de um aplicativo para um novo aplicativo ou um novo slot. O novo aplicativo pode estar na mesma região que o slot original do aplicativo ou em uma região diferente.

Se soubermos o nome do grupo de recursos que contém o aplicativo de origem, poderemos usar o seguinte comando do PowerShell para obter as informações do slot do aplicativo de origem (nesse caso, o nome `source-appslot`) está vinculado a `source-app`:

```powershell
$srcappslot = Get-AzWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-app -Slot source-appslot
```

O comando a seguir demonstra como criar um clone do aplicativo de origem como um novo aplicativo:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-app -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>Configuração do Gerenciador de Tráfego durante a clonagem de um aplicativo
Criar aplicativos de várias regiões e configurar o Azure Traffic Manager para rotear o tráfego para todos esses aplicativos é um cenário importante para garantir que os aplicativos de clientes sejam altamente disponíveis. Ao clonar um aplicativo existente, você tem a opção de conectar ambos os aplicativos a um perfil novo ou existente do gerenciador de tráfego. Somente a versão do Azure Resource Manager de gerenciador de tráfego recebe suporte.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>Criando um novo perfil do Gerenciador de Tráfego durante a clonagem de um aplicativo
Cenário: você deseja clonar um aplicativo para outra região, enquanto configura um Azure Resource Manager perfil do Gerenciador de tráfego que inclui ambos os aplicativos. O comando a seguir demonstra como criar um clone do aplicativo de origem como um novo aplicativo ao configurar um novo perfil do Gerenciador de Tráfego:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-app-to-an-existing-traffic-manager-profile"></a>Adicionando novos aplicativos clonados a um perfil existente do Gerenciador de Tráfego
Cenário: você já tem um Azure Resource Manager perfil do Gerenciador de tráfego e deseja adicionar ambos os aplicativos como pontos de extremidade. Para fazer isso, primeiro é necessário montar o ID existente de perfil do gerenciador de tráfego. É necessário a ID de assinatura, o nome do grupo de recursos e o nome de perfil do gerenciador de tráfego existente.

```powershell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

Depois de obter a ID do gerenciador de tráfego, o comando a seguir demonstra como criar um clone do aplicativo de origem como um novo aplicativo enquanto eles são adicionados a um perfil existente do Gerenciador de Tráfego:

```powershell
$destapp = New-AzWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```
> [!NOTE]
> Se você estiver recebendo um erro afirmando: "a validação SSL no nome do host do Gerenciador de tráfego está falhando", sugerimos que você use o atributo-IgnoreCustomHostNames no cmdlet do PowerShell durante a execução da operação de clonagem ou, caso contrário, use o Portal.

## <a name="current-restrictions"></a>Restrições atuais
Estas são as restrições conhecidas da clonagem de aplicativos:

* As configurações de escala automática não são clonadas
* As configurações de agendamento de backup não são clonadas.
* As configurações da rede virtual não são clonadas
* O Application Insights não está automaticamente configurado no aplicativo de destino
* As configurações de Autenticação Fácil não são clonadas
* A extensão Kudu não é clonada
* As regras de TiP não são clonadas
* O conteúdo do banco de dados não é clonado
* Os endereços IP de saída são alterados em caso de clonagem para uma unidade de escala diferente
* Não disponível para aplicativos do Linux
* Identidades gerenciadas não são clonadas

### <a name="references"></a>Referências
* [Clonagem de Serviço de Aplicativo](app-service-web-app-cloning.md)
* [Fazer backup de um aplicativo no Serviço de Aplicativo do Azure](manage-backup.md)
* [Suporte do Azure Resource Manager para Visualização do Gerenciador de Tráfego do Azure](../traffic-manager/traffic-manager-powershell-arm.md)
* [Introdução ao ambiente de Serviço de Aplicativo](environment/intro.md)
* [Usando o Azure PowerShell com o Gerenciador de Recursos do Azure](../azure-resource-manager/management/manage-resources-powershell.md)

