---
title: Dimensionar um serviço de nuvem do Azure (clássico) no Windows PowerShell | Microsoft Docs
description: (clássico) Aprenda a usar o PowerShell para escalar ou reduzir horizontalmente uma função Web ou função de trabalho no Azure.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: a090da1933b0fcd6edb5b2415c773f9efcb27387
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743297"
---
# <a name="how-to-scale-an-azure-cloud-service-classic-in-powershell"></a>Como dimensionar um serviço de nuvem do Azure (clássico) no PowerShell

> [!IMPORTANT]
> Os [serviços de nuvem do Azure (suporte estendido)](../cloud-services-extended-support/overview.md) são um novo modelo de implantação baseado em Azure Resource Manager para o produto de serviços de nuvem do Azure.Com essa alteração, os serviços de nuvem do Azure em execução no modelo de implantação baseado no Azure Service Manager foram renomeados como serviços de nuvem (clássicos) e todas as novas implantações devem usar os [serviços de nuvem (suporte estendido)](../cloud-services-extended-support/overview.md).

Você pode usar o Windows PowerShell para escalar ou reduzir uma função Web ou função de trabalho adicionando ou removendo instâncias.  

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Antes de executar qualquer operação na sua assinatura por meio do PowerShell, você deve fazer logon:

```powershell
Add-AzureAccount
```

Se você tiver várias assinaturas associadas à sua conta, você precisará alterar a assinatura atual dependendo de onde reside o seu serviço de nuvem. Para verificar a assinatura atual, execute:

```powershell
Get-AzureSubscription -Current
```

Se você precisar alterar a assinatura atual, execute:

```powershell
Set-AzureSubscription -SubscriptionId <subscription_id>
```

## <a name="check-the-current-instance-count-for-your-role"></a>Verificar a contagem de instâncias atual da sua função

Para verificar o estado atual da sua função, execute:

```powershell
Get-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>'
```

Você deve receber informações sobre a função, incluindo sua versão de sistema operacional atual e contagem de instância atuais. Nesse caso, a função tem uma única instância.

![Informações sobre a função](./media/cloud-services-how-to-scale-powershell/get-azure-role.png)

## <a name="scale-out-the-role-by-adding-more-instances"></a>Escalar horizontalmente a função adicionando mais instâncias

Para escalar horizontalmente sua função, passe o número desejado de instâncias como o parâmetro **Contagem** do cmdlet **Set-AzureRole**:

```powershell
Set-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>' -Slot <target_slot> -Count <desired_instances>
```

Os cmdlet é bloqueado momentaneamente enquanto as novas instâncias são provisionadas e iniciadas. Nesse momento, se você abrir uma nova janela do PowerShell e chamar **Get-AzureRole** conforme mostrado anteriormente, verá a nova contagem de instância de destino. E se você inspecionar o status da função no portal, deverá ver a nova instância de sendo inicializada:

![Instância VM aberta no portal](./media/cloud-services-how-to-scale-powershell/role-instance-starting.png)

Depois que as novas instâncias forem iniciadas, o cmdlet as retornará com êxito:

![Aumentar o êxito da instância de função](./media/cloud-services-how-to-scale-powershell/set-azure-role-success.png)

## <a name="scale-in-the-role-by-removing-instances"></a>Reduzir horizontalmente a função removendo instâncias

Você pode reduzir horizontalmente uma função removendo instâncias da mesma maneira. Defina o parâmetro **Contagem** em **Set-AzureRole** para o número de instâncias que você deseja ter após a conclusão da operação de redução horizontal.

## <a name="next-steps"></a>Próximas etapas

Não é possível configurar o dimensionamento automático para serviços de nuvem do PowerShell. Para fazer isso, veja [Como dimensionar automaticamente um serviço de nuvem](cloud-services-how-to-scale-portal.md).
