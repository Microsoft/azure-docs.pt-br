---
title: Clonagem de Aplicativo Web usando o PowerShell
description: Saiba como clonar seus Aplicativos Web em novos Aplicativos Web usando o Azure PowerShell.
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2016
ms.author: aelnably
ms.openlocfilehash: dc252903571857b5fc89d1d9a2c63cd6b44e9021
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Clonagem de aplicativo do Serviço de Aplicativo do Azure usando o Azure PowerShell
Com o lançamento do Microsoft Azure PowerShell versão 1.1.0, uma nova opção foi adicionada ao New-AzureRMWebApp para dar ao usuário a capacidade de clonar um aplicativo Web existente como um aplicativo recém-criado em uma região diferente ou na mesma região. Isso permitirá que os clientes implantem vários aplicativos em diferentes regiões de forma rápida e fácil.

A clonagem de aplicativo atualmente só tem suporte para planos de serviço de aplicativos de camada Premium. O novo recurso usa as mesmas limitações que o recurso de Backup dos Aplicativos Web; veja [Fazer backup de um aplicativo Web no Serviço de Aplicativo do Azure](web-sites-backup.md).

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="cloning-an-existing-app"></a>Clonagem de um aplicativo existente
Cenário: um aplicativo Web existente na região Centro-Sul dos EUA, o usuário gostaria de clonar o conteúdo como um novo aplicativo Web na região Centro-Norte dos EUA. Isso pode ser feito usando a versão do cmdlet do PowerShell do Azure Resource Manager para criar um novo aplicativo Web com a opção -SourceWebApp.

Se soubermos o nome do grupo de recursos que contém o aplicativo Web de origem, poderemos usar o seguinte comando do PowerShell para obter as informações do aplicativo Web de origem (nesse caso, o nome é source-webapp):

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

Para criar um novo plano do Serviço de Aplicativo, podemos usar comando New-AzureRmAppServicePlan como no exemplo a seguir

    New-AzureRmAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium

Usando o comando New-AzureRmWebApp, podemos criar o novo aplicativo Web na região Centro-Norte dos EUA e vinculá-lo a uma plano do Serviço de Aplicativo de camada Premium existente. Além disso, podemos usar o mesmo grupo de recursos do aplicativo Web de origem ou definir um novo grupo de recursos, como demonstrado abaixo:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp

Para clonar um aplicativo Web existente, incluindo a todos os respectivos slots de implantação, o usuário precisará usar o parâmetro IncludeSourceWebAppSlots. O comando PowerShell abaixo demonstra o uso desse parâmetro com o comando New-AzureRmWebApp:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots

Para clonar um aplicativo Web existente na mesma região, o usuário precisará criar um novo grupo de recursos e um novo plano do serviço de aplicativo na mesma região e usar o comando do PowerShell a seguir para clonar o aplicativo Web

    $destapp = New-AzureRmWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Clonagem de um aplicativo existente como um Ambiente do Serviço de Aplicativo
Cenário: um aplicativo Web existente na região Centro-Sul dos EUA; o usuário gostaria de clonar o conteúdo como um novo aplicativo Web em um ASE (Ambiente de Serviço de Aplicativo) existente.

Se soubermos o nome do grupo de recursos que contém o aplicativo Web de origem, poderemos usar o seguinte comando do PowerShell para obter as informações do aplicativo Web de origem (nesse caso, o nome é source-webapp):

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

Se souber o nome do ASE e do grupo de recursos a que o ASE pertence, o usuário poderá usar o comando New-AzureRmWebApp para criar o novo aplicativo Web no ASE existente, como demonstrado abaixo:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp

O parâmetro Location é necessário devido ao que foi herdado, mas, no caso da criação de um aplicativo em um ASE, ele será ignorado. 

## <a name="cloning-an-existing-app-slot"></a>Clonagem de um slot de aplicativo existente
Cenário: o usuário deseja clonar um slot do aplicativo Web existente como a um novo aplicativo Web ou um novo slot de aplicativo Web. O novo aplicativo Web pode estar na mesma região que o slot original do aplicativo Web ou em uma região diferente.

Se soubermos o nome do grupo de recursos que contém o aplicativo Web de origem, poderemos usar o seguinte comando do PowerShell para obter as informações do slot do aplicativo Web de origem (nesse caso, o nome é source-webappslot) vinculado a source-webapp do aplicativo Web:

    $srcappslot = Get-AzureRmWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-webapp -Slot source-webappslot

O exemplo a seguir demonstra como criar um clone do aplicativo Web de origem como um novo aplicativo Web:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot

## <a name="configuring-traffic-manager-while-cloning-a-app"></a>Configuração do Gerenciador de Tráfego durante a clonagem de um aplicativo
A criação de aplicativos Web de várias regiões e a configuração do Gerenciador de Tráfego do Azure para rotear tráfego a todos esses aplicativos Web é um cenário importante para garantir que os aplicativos dos clientes estejam altamente disponíveis; durante a clonagem de um aplicativo Web existente, você tem a opção de conectar os dois aplicativos Web a um novo perfil do gerenciador de tráfego ou a um existente. Observe que apenas a versão Azure Resource Manager do Gerenciador de Tráfego é permitida.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-a-app"></a>Criando um novo perfil do Gerenciador de Tráfego durante a clonagem de um aplicativo
Cenário: o usuário deseja clonar um aplicativo Web em outra região enquanto configura um perfil do gerenciador de tráfego do Azure Resource Manager que inclui ambos os aplicativos Web. O exemplo a seguir demonstra como criar um clone do aplicativo Web de origem como um novo aplicativo Web ao configurar um novo perfil do Gerenciador de Tráfego:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile

### <a name="adding-new-cloned-web-app-to-an-existing-traffic-manager-profile"></a>Adicionando novos aplicativos Web clonados a um perfil existente do Gerenciador de Tráfego
Cenário: o usuário já tem um perfil do gerenciador de tráfego do Azure Resource Manager ao qual ele gostaria de adicionar ambos os aplicativos Web como pontos de extremidade. Para fazer isso, primeiro precisamos montar a ID de perfil do Gerenciador de Tráfego existente; precisamos da ID de assinatura, do nome do grupo de recursos e do nome de perfil de Gerenciador de Tráfego existente.

    $TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"

Depois de obter a ID do Gerenciador de Tráfego, o código a seguir demonstra como criar um clone do aplicativo Web de origem como um novo aplicativo Web enquanto eles são adicionados a um perfil existente do Gerenciador de Tráfego:

    $destapp = New-AzureRmWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID

## <a name="current-restrictions"></a>Restrições atuais
Esse recurso está atualmente em visualização. Estamos trabalhando para adicionar novos recursos ao longo do tempo; a lista abaixo tem as restrições conhecidas de clonagem de aplicativo da versão atual:

* As configurações de escala automática não são clonadas
* As configurações de agendamento de backup não são clonadas.
* As configurações da rede virtual não são clonadas
* O Application Insights não está automaticamente configurado no aplicativo Web de destino
* As configurações de Autenticação Fácil não são clonadas
* A extensão Kudu não é clonada
* As regras de TiP não são clonadas
* O conteúdo do banco de dados não é clonado

### <a name="references"></a>Referências
* [Clonagem de aplicativo Web](app-service-web-app-cloning.md)
* [Fazer backup de um aplicativo Web no Serviço de Aplicativo do Azure](web-sites-backup.md)
* [Suporte do Azure Resource Manager para Visualização do Gerenciador de Tráfego do Azure](../traffic-manager/traffic-manager-powershell-arm.md)
* [Introdução ao ambiente de Serviço de Aplicativo](environment/intro.md)
* [Usando o Azure PowerShell com o Azure Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md)

