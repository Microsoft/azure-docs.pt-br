---
title: Plataformas compatíveis com a Central de Segurança do Azure | Microsoft Docs
description: Este documento fornece uma lista de plataformas compatíveis com a Central de Segurança do Azure.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 03/31/2020
ms.author: memildin
ms.openlocfilehash: 88dc0760f320a99b0cbc99b7637dc34dd11dfecc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103465439"
---
# <a name="supported-platforms"></a>Plataformas compatíveis 

Esta página mostra as plataformas e os ambientes compatíveis com a Central de Segurança do Azure.

## <a name="combinations-of-environments"></a>Combinações de ambientes <a name="vm-server"></a>

A Central de Segurança do Azure é compatível com máquinas virtuais e servidores em diferentes tipos de ambientes híbridos:

* Somente Azure
* Azure e local
* Azure e outras nuvens
* Azure, outras nuvens e local

Para um ambiente do Azure ativado em uma assinatura do Azure, a Central de Segurança do Azure descobrirá automaticamente os recursos de IaaS implantados na assinatura.

## <a name="supported-operating-systems"></a>Sistemas operacionais compatíveis

A Central de Segurança depende do [agente do Log Analytics](../azure-monitor/agents/agents-overview.md#log-analytics-agent). Verifique se os computadores estão sendo executados um dos sistemas operacionais compatíveis com esse agente, conforme descrito nas seguintes páginas:

* [Agente do Log Analytics para sistemas operacionais compatíveis com o Windows](../azure-monitor/agents/agents-overview.md#supported-operating-systems)
* [Agente do Log Analytics para sistemas operacionais compatíveis com o Linux](../azure-monitor/agents/agents-overview.md#supported-operating-systems)

Verifique também se o Agente do Log Analytics está [corretamente configurado para enviar dados à Central de Segurança](security-center-enable-data-collection.md#manual-agent)

Para saber mais sobre os recursos específicos da Central de Segurança disponíveis no Windows e no Linux, confira [Cobertura dos recursos para os computadores](security-center-services.md).

> [!NOTE]
> Embora o Azure Defender tenha a finalidade de proteger servidores, a maioria dos recursos do **Azure Defender para servidores** tem suporte para computadores com Windows 10. Um recurso que atualmente não tem suporte é a [Solução de EDR integrada da Central de Segurança: Microsoft Defender para ponto de extremidade](security-center-wdatp.md).

## <a name="managed-virtual-machine-services"></a>Serviços de máquina virtual gerenciados <a name="virtual-machine"></a>

As máquinas virtuais também são criadas em uma assinatura de cliente como parte de alguns serviços gerenciados pelo Azure também, como o AKS (Azure Kubernetes), o Azure Databricks e muito mais. A central de segurança descobre também essas máquinas virtuais e o agente de Log Analytics poderá ser instalado e configurado se um sistema operacional compatível estiver disponível.

## <a name="cloud-services"></a>Serviços de Nuvem <a name="cloud-services"></a>

Máquinas virtuais que são executadas em um serviço de nuvem também são compatíveis. Apenas funções Web e de trabalho de serviços de nuvem que são executadas em slots de produção são monitorados. Para saber mais sobre os serviços de nuvem, confira [Visão geral dos Serviços de Nuvem do Azure](../cloud-services/cloud-services-choose-me.md).

Também é compatível a proteção para VMs que residem no Azure Stack Hub. Para obter mais informações sobre a integração da Central de Segurança com o Azure Stack hub, confira [Integrar suas máquinas virtuais do Azure Stack Hub à Central de Segurança](quickstart-onboard-machines.md?pivots=azure-portal#onboard-your-azure-stack-hub-vms). 

## <a name="next-steps"></a>Próximas etapas

- Saiba como a [Central de Segurança coleta dados usando o Agente do Log Analytics](security-center-enable-data-collection.md).
- Saiba como a [Central de Segurança gerencia e protege os dados](security-center-data-security.md).
- Saiba como [planejar e entender as considerações de design para adotar a Central de Segurança do Azure](security-center-planning-and-operations-guide.md).