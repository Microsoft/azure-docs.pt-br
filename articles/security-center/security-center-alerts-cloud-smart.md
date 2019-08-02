---
title: Correlação de alertas inteligentes na nuvem na central de segurança do Azure (incidentes) | Microsoft Docs
description: Este tópico explica como o Fusion usa a correlação de alertas inteligentes na nuvem para gerar incidentes de segurança na central de segurança do Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: e9d5a771-bfbe-458c-9a9b-a10ece895ec1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: v-mohabe
ms.openlocfilehash: 7ba2cf14d9fac100f44a1ef23997b27ba062bee0
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295854"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Correlação de alertas inteligentes na nuvem na central de segurança do Azure (incidentes)

A central de segurança analisa continuamente as cargas de trabalho de nuvem híbrida usando a análise avançada e a inteligência contra ameaças para alertá-lo sobre atividades mal-intencionadas.

À medida que aumenta a abrangência da cobertura de ameaças e a necessidade de detectar até mesmo a mais pequena indicação dos aumentos comprometidos, pode ser desafiador para os analistas de segurança fazer a triagem dos diferentes alertas e identificar um ataque real. A central de segurança ajuda os analistas a lidar com alertas fadiga e a diagnosticar ataques à medida que ocorrem, correlacionando alertas diferentes e sinais de baixa fidelidade em incidentes de segurança.

Fusion é a tecnologia e o back-end analítico que capacita os incidentes da central de segurança, permitindo que ele correlacione alertas e sinais contextuais diferentes. O Fusion funciona examinando os diferentes sinais relatados em uma assinatura em todos os recursos e localizando padrões predominantes que mostram a progressão de ataque ou sinais com informações contextuais compartilhadas que indicam que um procedimento de resposta unificada deve ser tomadas para eles.

O Fusion Analytics combina o conhecimento do domínio de segurança com o ia para analisar alertas, descobrindo novos padrões de ataque conforme eles ocorrem. 

A central de segurança aproveita a matriz de ataques MITRE para associar alertas à sua intenção percebida, ajudando a formalizar o conhecimento do domínio de segurança. Além disso, usando as informações coletadas para cada etapa de um ataque, a central de segurança pode eliminar a atividade que parece ser uma das etapas de um ataque, mas não é.  

Como os ataques geralmente ocorrem em diferentes locatários, a central de segurança pode combinar algoritmos de ia para analisar as sequências de ataque que são relatadas em cada assinatura para identificá-las como padrões de alerta predominantes em vez de serem associadas a cada uma outros.

Durante uma investigação de um incidente, os analistas geralmente precisam de um contexto extra para alcançar uma veredicto sobre a natureza da ameaça e como atenuá-la. Por exemplo, mesmo quando uma anomalia de rede é detectada, sem entender o que mais está acontecendo na rede ou em relação ao recurso de destino, é difícil entender as ações a serem tomadas em seguida. Para ajudar, um incidente de segurança pode incluir artefatos, eventos relacionados e informações. As informações adicionais disponíveis para incidentes de segurança variam de acordo com o tipo de ameaça detectado e a configuração do seu ambiente. 

![Detalhes do incidente de segurança](./media/security-center-alerts-cloud-smart/security-incident.png)

Para entender melhor os incidentes de segurança, consulte [como lidar com incidentes de segurança na central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-incident).

