---
title: Como administrar o Cache Redis do Azure | Microsoft Docs
description: "Saiba como executar tarefas administrativas, como atualizações de reinicialização e agenda para o Cache Redis do Azure"
services: redis-cache
documentationcenter: na
author: steved0x
manager: douge
editor: tysonn
ms.assetid: 8c915ae6-5322-4046-9938-8f7832403000
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 07/05/2017
ms.author: sdanie
ms.openlocfilehash: 3352fec59d7dfbfab9b0416992a60f11d0ec2402
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-administer-azure-redis-cache"></a>Como administrar o Cache Redis do Azure
Este tópico descreve como executar tarefas administrativas, como [reinicializar](#reboot) e [agendar atualizações](#schedule-updates) para as instâncias de Cache Redis do Azure.

## <a name="reboot"></a>Reboot
A folha **Reinicializar** permite a reinicialização de um ou mais nós do cache. Essa funcionalidade de reinicialização permite que você teste seu aplicativo para garantir a resiliência caso ocorra uma falha de um nó de cache.

![Reboot](./media/cache-administration/redis-cache-administration-reboot.png)

Selecione os nós a serem reinicializados e clique em **Reinicializar**.

![Reboot](./media/cache-administration/redis-cache-reboot.png)

Se tiver um cache premium com clustering habilitado, você poderá selecionar quais fragmentos do cache serão reinicializados.

![Reboot](./media/cache-administration/redis-cache-reboot-cluster.png)

Para reinicializar um ou mais nós do cache, selecione os nós desejados e clique em **Reinicializar**. Se tiver um cache premium com clustering habilitado, escolha os fragmentos que deseja reinicializar e clique em **Reinicializar**. Depois de alguns minutos, os nós selecionados são reinicializados e voltam a ficar online alguns minutos mais tarde.

O impacto em aplicativos cliente varia de acordo com quais nós você reinicializa.

* **Mestre** - quando o nó mestre é reinicializado, o Cache Redis do Azure realiza o failover para o nó de réplica e o promove a mestre. Durante esse failover, pode haver um breve intervalo em que as conexões podem falhar para o cache.
* **Subordinado** - quando o nó subordinado é reinicializado, geralmente não há impacto para os clientes de cache.
* **Mestre e subordinado** - quando ambos os nós de cache são reinicializados, todos os dados são perdidos no cache, e as conexões com o cache falham até que o nó primário fique online novamente. Se você tiver configurado [persistência de dados](cache-how-to-premium-persistence.md), o backup mais recente será restaurado quando o cache voltar a ficar online, mas quaisquer gravações em cache que tiverem ocorrido após o backup mais recente serão perdidas.
* **Nós de um cache Premium com cluster habilitado** – quando você reinicializa um ou mais nós de um cache Premium com clustering habilitado, o comportamento dos nós selecionados é o mesmo de quando você reinicializa o nó ou nós correspondentes de um cache não clusterizado.

> [!IMPORTANT]
> A reinicialização agora está disponível para todos os tipos de preço.
> 
> 

## <a name="reboot-faq"></a>Perguntas frequentes sobre reinicialização
* [Qual nó devo reinicializar para testar o aplicativo?](#which-node-should-i-reboot-to-test-my-application)
* [Posso reinicializar o cache para limpar conexões de cliente?](#can-i-reboot-the-cache-to-clear-client-connections)
* [Perderei dados do cache se eu fizer uma reinicialização?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [Posso reinicializar o cache usando o PowerShell, a CLI ou outras ferramentas de gerenciamento?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)
* [Que tipos de preços podem usar a funcionalidade de reinicialização?](#what-pricing-tiers-can-use-the-reboot-functionality)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>Qual nó devo reinicializar para testar o aplicativo?
Para testar a resiliência do aplicativo contra falhas do nó principal do cache, reinicialize o nó **Mestre** . Para testar a resiliência do aplicativo contra falhas do nó secundário, reinicialize o nó **Subordinado** . Para testar a resiliência do aplicativo contra falha total do cache, reinicialize **Ambos** os nós.

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>Posso reinicializar o cache para limpar conexões de cliente?
Sim, se você reinicializar o cache, todas as conexões de cliente serão limpas. Reinicializar pode ser útil quando todas as conexões de cliente são usadas, por exemplo, devido a um erro lógico ou um bug no aplicativo cliente. Cada tipo de preços tem diferentes [limites de conexão do cliente](cache-configure.md#default-redis-server-configuration) para os vários portes, e quando os limites são atingidos, não são mais aceitas mais conexões de cliente. Reinicializar o cache fornece uma maneira de limpar todas as conexões de cliente.

> [!IMPORTANT]
> Se você reinicializar o cache para limpar as conexões de cliente, o StackExchange.Redis se reconectará automaticamente quando o nó do Redis estiver online novamente. Se o problema subjacente não for resolvido, as conexões de cliente continuarão a ser usadas.
> 
> 

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Perderei dados do cache se eu fizer uma reinicialização?
Se você reinicializar os nós **Mestre** e **Subordinado**, todos os dados no cache (ou nesse fragmento, se você estiver usando um cache premium com clustering habilitado) serão perdidos. Se você tiver configurado [persistência de dados](cache-how-to-premium-persistence.md), o backup mais recente será restaurado quando o cache voltar a ficar online, mas quaisquer gravações em cache que tiverem ocorrido após o backup ter sido realizado serão perdidas.

Se você reinicializar apenas um dos nós, normalmente os dados não serão perdidos, mas isso ainda poderá ocorrer. Por exemplo, se o nó mestre for reinicializado e uma gravação de cache estiver em andamento, os dados da gravação do cache serão perdidos. Outro cenário de perda de dados ocorre se você reinicializa um nó e o outro nó ocorre fica inoperante devido a uma falha ao mesmo tempo. Para saber mais sobre as possíveis causas da perda de dados, confira [What happened to my data in Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) (O que aconteceu com meus dados no Redis?)

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>Posso reinicializar o cache usando o PowerShell, a CLI ou outras ferramentas de gerenciamento?
Sim, para ver as instruções do PowerShell, confira [Para reinicializar um cache Redis](cache-howto-manage-redis-cache-powershell.md#to-reboot-a-redis-cache).

### <a name="what-pricing-tiers-can-use-the-reboot-functionality"></a>Que tipos de preços podem usar a funcionalidade de reinicialização?
A reinicialização está disponível para todos os tipos de preço.

## <a name="schedule-updates"></a>Agendar atualizações
A folha **Agendar atualizações** permite designar uma -janela de manutenção para seu cache de camada Premium. Quando a janela de manutenção é especificada, as atualizações do servidor Redis são feitas durante essa janela. 

> [!NOTE] 
> A janela de manutenção se aplica apenas às atualizações do servidor Redis e não a quaisquer atualizações do Azure ou atualizações do sistema operacional das VMs que hospedam o cache.
> 
> 

![Agendar atualizações](./media/cache-administration/redis-schedule-updates.png)

Para especificar uma janela de manutenção, marque os dias desejados, especifique a hora de início da janela de manutenção para cada dia e clique em **OK**. Observe que o horário da janela de manutenção é em UTC. 

> [!NOTE]
> A janela de manutenção padrão para atualizações é de cinco horas. Esse valor não é configurável no portal do Azure, mas você pode configurá-lo no PowerShell usando o parâmetro `MaintenanceWindow` do cmdlet [New-AzureRmRedisCacheScheduleEntry](/powershell/module/azurerm.rediscache/new-azurermrediscachescheduleentry) . Para saber mais, confira [Posso gerenciar as atualizações agendadas usando o PowerShell, a CLI ou outras ferramentas de gerenciamento?](#can-i-manage-scheduled-updates-using-powershell-cli-or-other-management-tools)
> 
> 

## <a name="schedule-updates-faq"></a>Perguntas frequentes sobre agendamento de atualizações
* [Quando as atualizações ocorrerão se eu não usar o recurso de agendamento de atualizações?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [Que tipos de atualizações são feitas durante a janela de manutenção agendada?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [Posso gerenciar as atualizações agendadas usando o PowerShell, a CLI ou outras ferramentas de gerenciamento?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)
* [Que tipos de preços podem usar a funcionalidade de agendamento de atualizações?](#what-pricing-tiers-can-use-the-schedule-updates-functionality)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>Quando as atualizações ocorrerão se eu não usar o recurso de agendamento de atualizações?
Se você não especificar uma janela de manutenção, as atualizações poderão ser feitas a qualquer momento.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Que tipos de atualizações são feitas durante a janela de manutenção agendada?
Apenas as atualizações do servidor Redis são realizadas durante a janela de manutenção agendada. A janela de manutenção se aplica a atualizações do Azure ou do sistema operacional da VM.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>Posso gerenciar as atualizações agendadas usando o PowerShell, a CLI ou outras ferramentas de gerenciamento?
Sim, você pode gerenciar as atualizações agendadas usando os cmdlets do PowerShell a seguir:

* [Get-AzureRmRedisCachePatchSchedule](/powershell/module/azurerm.rediscache/get-azurermrediscachepatchschedule)
* [New-AzureRmRedisCachePatchSchedule](/powershell/module/azurerm.rediscache/new-azurermrediscachepatchschedule)
* [New-AzureRmRedisCacheScheduleEntry](/powershell/module/azurerm.rediscache/new-azurermrediscachescheduleentry)
* [Remove-AzureRmRedisCachePatchSchedule](/powershell/module/azurerm.rediscache/remove-azurermrediscachepatchschedule)

### <a name="what-pricing-tiers-can-use-the-schedule-updates-functionality"></a>Que tipos de preços podem usar a funcionalidade de agendamento de atualizações?
O recurso **Agendar atualizações** está disponível apenas no tipo de preço premium.

## <a name="next-steps"></a>Próximas etapas
* Explore mais recursos da [camada premium do Cache Redis do Azure](cache-premium-tier-intro.md) .

