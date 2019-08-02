---
title: Aviso de desativação da família 1 de SOs convidados | Microsoft Docs
description: Fornece informações sobre quando ocorreu a desativação da família 1 sistemas operacionais convidados do Azure e determinar se você foi afetado
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
ms.service: cloud-services
ms.topic: article
ms.date: 5/21/2017
ms.author: raiye
ms.openlocfilehash: ae2df6f47d99fc5d452a6d3ea70f2dd2e4e7416b
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359589"
---
# <a name="guest-os-family-1-retirement-notice"></a>Aviso de desativação da família 1 de SO convidados
A desativação da família 1 do sistema operacional foi anunciada em 1º de junho de 2013.

**2 de setembro de 2014** A família 1.x do sistema operacional convidado do Azure (SO convidado), que se baseia no sistema operacional Windows Server 2008, foi oficialmente desativada. Todas as tentativas para implantar novos serviços ou atualizar serviços existentes usando a família 1 falharão com uma mensagem de erro informando que a família 1 dos sistemas operacionais convidados foi desativada.

**3 de novembro de 2014** O suporte estendido para a família 1 dos sistemas operacionais convidados terminou e está totalmente desativado. Todos os serviços que ainda estão na família 1 serão afetados. Podemos interromper esses serviços a qualquer momento. Não há nenhuma garantia de que seus serviços continuarão sendo executados, a menos que você os atualize manualmente por conta própria.

Se você tiver outras dúvidas, visite os [Fóruns dos Serviços de Nuvem](https://social.msdn.microsoft.com/Forums/home?forum=windowsazuredevelopment&filter=alltypes&sort=lastpostdesc) ou [entre em contato com o suporte do Azure](https://azure.microsoft.com/support/options/).

## <a name="are-you-affected"></a>Você foi afetado?
Os Serviços de Nuvem são afetados se qualquer uma das opções a seguir se aplicar:

1. Você tem um valor de "osFamily = "1" explicitamente especificado no arquivo ServiceConfiguration.cscfg para seu serviço de nuvem.
2. Você não tem um valor para osFamily especificado explicitamente no arquivo ServiceConfiguration.cscfg para seu serviço de nuvem. Atualmente, o sistema usa o valor padrão de "1" nesse caso.
3. O Portal do Azure lista o valor da família do Sistema Operacional Convidado como "Windows Server 2008".

Para definir quais dos serviços de nuvem estão executando qual família de SO, execute o script a seguir no Azure PowerShell, embora você deva [configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) primeiro. Para obter mais informações sobre o script, [consulte fim da vida útil da família 1 do SO convidado do Azure: Junho de](https://blogs.msdn.com/b/ryberry/archive/2014/04/02/azure-guest-os-family-1-end-of-life-june-2014.aspx)2014.

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName

    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""}

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

Os serviços de nuvem serão afetados pela desativação da família 1 do sistema operacional se a coluna osFamily na saída do script estiver vazia ou contiver um "1".

## <a name="recommendations-if-you-are-affected"></a>Recomendações se você for afetado
Recomendamos que você migre suas funções de serviço de nuvem para uma das famílias dos sistemas operacionais convidados com suporte:

**Família 4.x dos SOs convidados** - Windows Server 2012 R2 *(recomendado)*

1. Certifique-se de que seu aplicativo esteja usando o SDK 2.1 ou posterior com o .NET Framework 4.0, 4.5 ou 4.5.1.
2. Defina o atributo osFamily para "4" no arquivo ServiceConfiguration.cscfg e reimplante o serviço de nuvem.

**Família 3.x dos SOs convidados** - Windows Server 2012

1. Certifique-se de que seu aplicativo esteja usando o SDK 1.8 ou posterior com o .NET Framework 4.0 ou 4.5.
2. Defina o atributo osFamily como "3" no arquivo ServiceConfiguration.cscfg e reimplante o serviço de nuvem.

**Família 2.x dos SOs convidados** - Windows Server 2008 R2

1. Certifique-se de que seu aplicativo esteja usando o SDK 1.3 e posterior com o .NET Framework 3.5 ou 4.0.
2. Defina o atributo osFamily para "2" no arquivo ServiceConfiguration.cscfg e reimplante o serviço de nuvem.

## <a name="extended-support-for-guest-os-family-1-ended-nov-3-2014"></a>O suporte estendido para a Família 1 dos sistemas operacionais convidados terminou em 3 de novembro de 2014
Não há mais suporte para serviços de nuvem na família 1 dos sistemas operacionais convidados. Migre da família 1 assim que possível para evitar a interrupção do serviço.  

## <a name="next-steps"></a>Próximas etapas
Examine as últimas [versões do SO convidado](cloud-services-guestos-update-matrix.md).
