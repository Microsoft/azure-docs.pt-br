---
title: Alertas de segurança na central de segurança do Azure | Microsoft Docs
description: Este tópico explica o que são alertas de segurança e os diferentes tipos disponíveis na central de segurança do Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: v-mohabe
ms.openlocfilehash: 91dd397095718b3b43e41767af422801fd50b7f1
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295696"
---
# <a name="security-alerts-in-azure-security-center"></a>Alertas de segurança na central de segurança do Azure

Este artigo apresenta os diferentes tipos de alertas de segurança disponíveis na central de segurança do Azure (ASC). Há uma variedade de alertas para os vários tipos de recursos diferentes. O ASC gera alertas para os recursos implantados no Azure e os recursos implantados em ambientes de nuvem híbrida e locais. 

## <a name="what-are-security-alerts"></a>O que são alertas de segurança?

Alertas são as notificações que a central de segurança gera quando detecta ameaças em seus recursos. Ele prioriza e lista os alertas junto com as informações necessárias para que você investigue rapidamente o problema. A central de segurança também fornece recomendações sobre como você pode corrigir um ataque.

## <a name="how-does-security-center-detect-threats"></a>Como a central de segurança detecta ameaças?

Para detectar ameaças reais e reduzir falsos positivos, a central de segurança coleta, analisa e integra dados de log de seus recursos do Azure, da rede e das soluções de parceiros conectadas, como soluções de firewall e Endpoint Protection. A central de segurança analisa essas informações, geralmente correlacionando informações de várias fontes, para identificar ameaças.

O ASC monitora os recursos se implantados no Azure ou implantados em outros ambientes de nuvem híbrida e locais. Para saber mais sobre como detectar e responder a ameaças, consulte [como a central de segurança detecta e responde a ameaças](security-center-detection-capabilities.md#asc-detects).

## <a name="security-alert-types"></a>Tipos de alertas de segurança

Os tópicos a seguir orientam você pelos diferentes alertas ASC de acordo com os tipos de recursos:

* [Alertas de servidores & de VMs IaaS](security-center-alerts-iaas.md)
* [Alertas de computação nativos](security-center-alerts-compute.md)
* [Alertas de serviços de dados](security-center-alerts-data-services.md)

Os tópicos a seguir explicam como a central de segurança aproveita a telemetria diferente que coleta a integração com a infraestrutura do Azure para aplicar camadas de proteção adicionais para recursos implantados no Azure:

* [Alertas da camada de serviço](security-center-alerts-service-layer.md)
* [Integração com os produtos de segurança do Azure](security-center-alerts-integration.md)

## <a name="what-are-alert-incidents"></a>O que são incidentes de alerta?

Um incidente de segurança é uma coleção de alertas relacionados, em vez de listar cada alerta individualmente. A central de segurança usa a fusão para correlacionar diferentes alertas e sinais de baixa fidelidade em incidentes de segurança.

Usando incidentes, a central de segurança fornece uma única exibição de uma campanha de ataque e todos os alertas relacionados. Essa exibição permite que você entenda rapidamente quais ações foram tomadas pelo invasor e quais recursos foram afetados. Para obter mais informações, consulte [correlação de alertas inteligentes na nuvem](security-center-alerts-cloud-smart.md).

## <a name="get-started-with-alerts"></a>Introdução aos alertas

Consulte os tópicos a seguir para entender mais sobre os recursos monitorados pelo ASC e para obter diretrizes sobre como responder aos alertas apresentados pelo ASC.

* Para ver quais plataformas e recursos são protegidos por ASC, consulte [plataformas e recursos com suporte na central de segurança do Azure](security-center-os-coverage.md).  
* Para entender o que são incidentes de segurança e como o ASC responde a eles, consulte [como lidar com incidentes de segurança na central de segurança do Azure](security-center-incident.md). 
* Para saber como gerenciar os alertas recebidos, consulte Gerenciando [e respondendo a alertas de segurança na central de segurança do Azure](security-center-managing-and-responding-alerts.md).
* Para obter informações sobre como validar se a central de segurança está configurada corretamente e para estimular um alerta de teste, consulte [validação de alertas na central de segurança do Azure](security-center-alert-validation.md).  


## <a name="upgrade-to-standard-for-advanced-detections"></a>Atualizar para Standard para detecções avançadas

Para configurar as detecções avançadas, atualize para a Central de Segurança do Azure Standard. 

1. No menu central de segurança, selecione **política de segurança**.
2. Para as assinaturas que você deseja mover para a camada Standard, clique em **Editar configurações**. 
3. Na página configurações, selecione **tipo de preço**. 
   Uma avaliação gratuita está disponível por um mês. Para saber mais, consulte a [página de preços](https://azure.microsoft.com/pricing/details/security-center/). 

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu o que são alertas de segurança e os diferentes tipos de alertas disponíveis na central de segurança. Para mais informações, consulte os seguintes tópicos:

* [Guia de planejamento e operações da Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Perguntas frequentes sobre a Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-faq): Encontre as perguntas frequentes sobre como usar o serviço.

