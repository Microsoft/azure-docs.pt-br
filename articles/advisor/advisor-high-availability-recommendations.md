---
title: "Recomendações de alta disponibilidade do Azure Advisor | Microsoft Docs"
description: "Use o Azure Advisor para melhorar a alta disponibilidade das implantações do Azure."
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 7b63de2453180e562596c211d40cebe85b95bd54
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="advisor-high-availability-recommendations"></a>Recomendações de alta disponibilidade do Advisor

O Assistente do Azure ajuda a garantir e melhorar a continuidade de aplicativos críticos aos negócios. Você pode obter recomendações de alta disponibilidade do Advisor na guia **Alta Disponibilidade** do painel Advisor.

![Botão de alta disponibilidade no painel do Assistente](./media/advisor-high-availability-recommendations/advisor-high-availability-tab.png)


## <a name="ensure-virtual-machine-fault-tolerance"></a>Assegurar tolerância a falhas de máquina virtual

O Advisor identifica máquinas virtuais que não fazem parte de um conjunto de disponibilidade e recomenda movê-las para um conjunto de disponibilidade. Para oferecer redundância para o seu aplicativo, recomendamos que agrupe uma ou mais máquinas virtuais em um conjunto de disponibilidade. Essa configuração garante que durante um evento de manutenção planejada ou não planejada, pelo menos, uma máquina virtual esteja disponível e cumpra o SLA de máquina virtual do Azure. Você pode optar por criar um conjunto de disponibilidade para a máquina virtual ou adicionar a máquina virtual a um conjunto de disponibilidade existente.

> [!NOTE]
> Se você optar por criar um conjunto de disponibilidade, será preciso adicionar pelo menos mais uma máquina virtual nele. É recomendável agrupar duas ou mais máquinas virtuais em um conjunto de disponibilidade para garantir que pelo menos uma delas esteja disponível durante uma interrupção.

![Recomendação do Assistente: para redundância de máquina virtual, use conjuntos de disponibilidade](./media/advisor-high-availability-recommendations/advisor-high-availability-create-availability-set.png)

## <a name="ensure-availability-set-fault-tolerance"></a>Assegure que haja tolerância a falhas de conjunto de disponibilidade 

O Assistente identifica os conjuntos de disponibilidade que contêm uma única máquina virtual e recomenda a adição de uma ou mais máquinas virtuais a ele. Para oferecer redundância para o seu aplicativo, recomendamos que agrupe uma ou mais máquinas virtuais em um conjunto de disponibilidade. Essa configuração garante que durante um evento de manutenção planejada ou não planejada, pelo menos, uma máquina virtual esteja disponível e cumpra o SLA de máquina virtual do Azure. Você pode optar por criar uma máquina virtual ou então por usar uma máquina virtual existente e adicioná-la ao conjunto de disponibilidade.  

![Recomendação do Assistente: adicionar uma ou mais VMs a esse conjunto de disponibilidade](./media/advisor-high-availability-recommendations/advisor-high-availability-add-vm-to-availability-set.png)


## <a name="ensure-application-gateway-fault-tolerance"></a>Garantir tolerância a falhas de Gateway de Aplicativo
Para garantir a continuidade de negócios de aplicativos críticos que são ativados pelos Gateways de Aplicativo, o Assistente identifica as instâncias de Gateway de Aplicativo que não estão configuradas para tolerância a falhas e sugere ações de correção que você pode realizar. O Assistente identifica Gateways de Aplicativo de instância única médios ou grandes e recomenda adicionar pelo menos uma instância a mais. Ele também identifica Gateways de Aplicativo de instância única ou de várias instâncias pequenos e recomenda a migração para SKUs médios ou grandes. O Assistente recomenda essas ações para garantir que suas instâncias de Gateway de Aplicativo estejam configuradas para satisfazer os requisitos de SLA atuais para esses recursos.

![Recomendação do Assistente: implantar duas ou mais instâncias de Gateway de Aplicativo de porte médio ou grande](./media/advisor-high-availability-recommendations/advisor-high-availability-application-gateway.png)

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks"></a>Melhorar o desempenho e a confiabilidade dos discos da máquina virtual

O Assistente identifica máquinas virtuais com discos standard e recomenda a atualização para discos premium.
 
O Armazenamento Premium do Azure dá suporte de disco de alto desempenho e baixa latência para máquinas virtuais executando cargas de trabalho com uso intensivo de E/S. Os discos da máquina virtual que usam contas de Armazenamento Premium armazenam dados em SSDs (unidades de estado sólido). Para o melhor desempenho em seu aplicativo, recomendamos migrar qualquer disco de máquina virtual que exija IOPS alta para o Armazenamento Premium. 

Se os discos não exigirem IOPS alta, você poderá limitar os custos mantendo-o em Armazenamento Standard. O Armazenamento Standard armazena dados de disco da máquina virtual em HDDs (unidades de disco rígido) em vez de SSDs. Você pode optar por migrar seus discos de máquina virtual para discos premium. Os discos premium tem suporte com a maioria dos SKUs de máquina virtual. No entanto, em alguns casos, se você quiser usar discos premium, talvez também seja preciso atualizar o SKU da máquina virtual.

![Recomendação do Assistente: aumentar a confiabilidade de seus discos de máquina virtual com a atualização para discos premium](./media/advisor-high-availability-recommendations/advisor-high-availability-upgrade-to-premium-disks.png)

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Proteger seus dados de máquina virtual contra exclusão acidental
O Assistente identifica as máquinas virtuais em que o backup não está habilitado e recomenda habilitar o backup. Configurar o backup da máquina virtual garante a disponibilidade de seus dados críticos de negócios e oferece proteção contra corrupção ou exclusão acidental.

![Recomendação do Assistente: configurar o backup de máquinas virtuais para proteger seus dados críticos](./media/advisor-high-availability-recommendations/advisor-high-availability-virtual-machine-backup.png)

## <a name="access-high-availability-recommendations-in-advisor"></a>Como acessar as recomendações de alta disponibilidade no Assistente

1. Entre no [Portal do Azure](https://portal.azure.com).

2. No painel esquerdo, clique em **Mais serviços**.

3. No painel do menu de serviço, em **Monitoramento e Gerenciamento**, clique em **Assistente do Azure**.  
 O painel Assistente é exibido.

4. No painel do Assistente, clique na guia **Alta Disponibilidade** e selecione a assinatura para a qual você deseja receber recomendações.

> [!NOTE]
> Para acessar as recomendações do Assistente, você deve primeiro *registrar sua assinatura* no Assistente. Uma assinatura é registrada quando um *Proprietário de assinatura* inicia o painel do Assistente e clica no botão **Obter recomendações**. Essa é uma *operação única*. Depois que a assinatura for registrada, você poderá acessar as recomendações do Assistente como *Proprietário*, *Colaborador* ou *Leitor* de uma assinatura, um grupo de recursos ou um recurso específico.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre as recomendações do Assistente, consulte:
* [Introdução ao Azure Advisor](advisor-overview.md)
* [Introdução ao Advisor](advisor-get-started.md)
* [Recomendações de custo do Advisor](advisor-performance-recommendations.md)
* [Recomendações de desempenho do Advisor](advisor-performance-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)

