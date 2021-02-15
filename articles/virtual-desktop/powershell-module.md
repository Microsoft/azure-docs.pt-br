---
title: Módulo do PowerShell da Área de Trabalho Virtual do Windows – Azure
description: Como instalar e configurar o módulo do PowerShell para a Área de Trabalho Virtual do Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f2f01e2b58c997db08ad4427de7eef1ee3760c4a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016804"
---
# <a name="set-up-the-powershell-module-for-windows-virtual-desktop"></a>Configurar o módulo do PowerShell para a Área de Trabalho Virtual do Windows

>[!IMPORTANT]
>Este conteúdo se aplica à área de trabalho virtual do Windows com integração Azure Resource Manager.

O módulo do PowerShell da Área de Trabalho Virtual do Windows vem integrado ao módulo do Microsoft Azure PowerShell. Este artigo mostrará como configurar o módulo do PowerShell para que você possa executar cmdlets na Área de Trabalho Virtual do Windows.

## <a name="set-up-your-powershell-environment"></a>Configurar o ambiente do PowerShell

Para começar a usar o módulo, primeiro instale a [versão mais recente do PowerShell Core](/powershell/scripting/install/installing-powershell#powershell-core). No momento, os cmdlets da Área de Trabalho Virtual do Windows funcionam apenas com o PowerShell Core.

Em seguida, será preciso instalar o módulo DesktopVirtualization para usar em sua sessão do PowerShell.

Execute o seguinte cmdlet do PowerShell no modo elevado para instalar o módulo:

```powershell
Install-Module -Name Az.DesktopVirtualization
```

>[!NOTE]
> Se esse cmdlet não funcionar, tente executá-lo novamente com permissões elevadas.

Em seguida, execute o seguinte cmdlet para se conectar ao Azure:

```powershell
Connect-AzAccount
```

>[!IMPORTANT]
>Se você estiver se conectando ao portal de US Gov, execute este cmdlet:
> 
> ```powershell
> Connect-AzAccount -EnvironmentName AzureUSGovernment
> ```

Para entrar em sua conta do Azure, você precisará de um código que é gerado quando você executa o cmdlet Connect. Para entrar, acesse <https://microsoft.com/devicelogin>, insira o código e conecte-se usando suas credenciais de administrador do Azure.

```powershell
Account SubscriptionName TenantId Environment

------- ---------------- -------- -----------

Youradminupn subscriptionname AzureADTenantID AzureCloud
```

Isso conectará você diretamente à assinatura padrão para suas credenciais de administrador.

## <a name="change-the-default-subscription"></a>Alterar a assinatura padrão

Se quiser alterar a assinatura padrão depois de entrar, execute este cmdlet:

```powershell
Select-AzSubscription -Subscription <preferredsubscriptionname>
```

Você também pode selecionar um de uma lista usando o cmdlet Out-GridView:

```powershell
Get-AzSubscription | Out-GridView -PassThru | Select-AzSubscription
```

Quando você seleciona uma nova assinatura para uso, não é necessário especificar a ID da assinatura nos cmdlets executados posteriormente. Por exemplo, o seguinte cmdlet recupera o host de uma sessão específica sem precisar da ID de assinatura:

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname>
```

Também é possível alterar assinaturas de acordo com cada cmdlet, adicionando como parâmetro o nome da assinatura desejada. O próximo cmdlet é o mesmo do exemplo anterior, só que com a ID de assinatura adicionada como parâmetro para alterar a assinatura usada pelo cmdlet.

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -SubscriptionId <subscriptionGUID>
```

## <a name="get-locations"></a>Obter localizações

O parâmetro de localização é obrigatório para todos os cmdlets **New-AzWVD** que criam objetos.

Execute o seguinte cmdlet para obter uma lista de locais aos quais sua assinatura dá suporte:

```powershell
Get-AzLocation
```

A saída do **Get-AzLocation** terá a seguinte aparência:

```powershell
Location : eastasia

DisplayName : East Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : southeastasia

DisplayName : Southeast Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : centralus

DisplayName : Central US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}

Location : eastus

DisplayName : East US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}
```

Depois de descobrir o local da sua conta, você poderá usá-lo em um cmdlet. Por exemplo, este é um cmdlet que cria um pool de hosts no local "southeastasia":

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -Location “southeastasia”
```

## <a name="next-steps"></a>Próximas etapas

Agora que você configurou o módulo do PowerShell, pode executar cmdlets para fazer todo tipo de coisa na Área de Trabalho Virtual do Windows. Estes são alguns dos locais em que você pode usar seu módulo:

- Percorrer nossos [tutoriais da Área de Trabalho Virtual do Windows]() para configurar seu ambiente da Área de Trabalho Virtual do Windows.
- [Criar um pool de host com o PowerShell](create-host-pools-powershell.md)
- [Configurar o método de balanceamento de carga da Área de Trabalho Virtual do Windows](configure-host-pool-load-balancing.md)
- [Configurar o tipo de atribuição do pool de host da área de trabalho pessoal](configure-host-pool-personal-desktop-assignment-type.md)
- E muito mais!

Se tiver problemas, encontre ajuda em nosso [artigo de solução de problemas do PowerShell](troubleshoot-powershell.md).

