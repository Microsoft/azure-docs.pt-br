---
title: Incluir arquivo do PowerShell para o DNS do Azure
description: Incluir arquivo do PowerShell para o DNS do Azure
services: dns
author: subsarma
ms.service: dns
ms.topic: include file for PowerShell for Azure DNS
ms.date: 03/21/2018
ms.author: subsarma
ms.custom: include file for PowerShell for Azure DNS
ms.openlocfilehash: 32c516ccee3a9f4f7604a3e330285703a776b47d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "67132998"
---
## <a name="set-up-azure-powershell-for-azure-dns"></a>Configurar o Azure PowerShell para DNS do Azure

### <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [requires-azurerm](requires-azurerm.md)]

Antes de começar a configurar, verifique se você tem os itens a seguir.

* Uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Você precisa instalar a versão mais recente dos cmdlets do Azure Resource Manager do PowerShell. Para obter mais informações, confira [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs).

Além disso, para usar Zonas Privadas (Visualização Pública), é preciso ter os módulos e as versões do PowerShell a seguir. 
* AzureRM.Dns – [versão 4.1.0](https://www.powershellgallery.com/packages/AzureRM.Dns/4.1.0) ou superior
* AzureRM.Network – [versão 5.4.0](https://www.powershellgallery.com/packages/AzureRM.Network/5.4.0) ou superior

```powershell 
Find-Module -Name AzureRM.Dns 
``` 
 
```powershell 
Find-Module -Name AzureRM.Network 
``` 
 
A saída dos comandos acima precisa mostrar que a versão do AzureRM.Dns é a 4.1.0 ou superior e a versão do AzureRM.Network é a 5.4.0 ou superior.  

Caso seu sistema tenha versões anteriores, você pode instalar a versão mais recente do Azure PowerShell ou baixar e instalar os módulos acima a partir da Galeria do PowerShell, usando os links acima ao lado das Versões de módulo. Você pode instalá-los usando os comandos abaixo. Ambos os módulos são necessários e totalmente compatíveis. 

```powershell
Install-Module -Name AzureRM.Dns -Force
```

```powershell
Install-Module -Name AzureRM.Network -Force
```

### <a name="sign-in-to-your-azure-account"></a>Entre na sua conta do Azure

Abra o console do PowerShell e conecte-se à sua conta. Para obter mais informações, consulte [entrar com AzureRM](/powershell/azure/azurerm/authenticate-azureps).

```powershell
Connect-AzureRmAccount
```

### <a name="select-the-subscription"></a>Selecionar a assinatura
 
Verificar as assinaturas da conta.

```powershell
Get-AzureRmSubscription
```

Escolha quais das suas assinaturas do Azure deseja usar.

```powershell
Select-AzureRmSubscription -SubscriptionName "your_subscription_name"
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

O Azure Resource Manager requer que todos os grupos de recursos especifiquem um local. Esse local é usado como o local padrão para os recursos do grupo de recursos em questão. No entanto, como todos os recursos de DNS são globais, não regionais, a escolha do local do grupo de recursos não afeta o DNS do Azure.

Você pode ignorar esta etapa se está usando um grupo de recursos existente.

```powershell
New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"
```

### <a name="register-resource-provider"></a>Registrar provedor de recursos

O serviço de DNS do Azure é gerenciado pelo provedor de recursos Microsoft.Network. Para que você possa usar o DNS do Azure, sua assinatura do Azure deve ser registrada para usar esse provedor de recursos. Essa operação deve ser executa apenas uma vez para cada assinatura.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```
