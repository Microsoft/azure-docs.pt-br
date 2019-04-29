---
title: Dimensionar um serviço de nuvem do Azure no Windows PowerShell | Microsoft Docs
description: (clássico) Aprenda a usar o PowerShell para escalar ou reduzir horizontalmente uma função Web ou função de trabalho no Azure.
services: cloud-services
documentationcenter: ''
author: mmccrory
manager: timlt
editor: ''
ms.assetid: ee37dd8c-6714-4c61-adb8-03d6bbf76c9a
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: mmccrory
ms.openlocfilehash: 240b34abd2f9b937c8a7ea449ad7f148870a9d33
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61433210"
---
# <a name="how-to-scale-a-cloud-service-in-powershell"></a>Como escalar um serviço de nuvem no PowerShell

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
