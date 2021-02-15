---
title: Incidentes da central de segurança do Azure – correlação inteligente de alertas
description: Este tópico explica como o Fusion usa a correlação de alertas inteligentes na nuvem para gerar incidentes de segurança na central de segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e9d5a771-bfbe-458c-9a9b-a10ece895ec1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: 742d7122c60b383e8ab7aa73f73b11b47843a9d5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91613808"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Correlação de alertas inteligentes na nuvem na central de segurança do Azure (incidentes)

A central de segurança do Azure analisa continuamente as cargas de trabalho de nuvem híbrida usando a análise avançada e a inteligência contra ameaças para alertá-lo sobre atividades mal-intencionadas.

A amplitude da cobertura de ameaças está crescendo. A necessidade de detectar até mesmo o menor comprometimento é importante, e pode ser um desafio para os analistas de segurança fazer triagem dos diferentes alertas e identificar um ataque real. A central de segurança ajuda os analistas a lidar com esse alerta fadiga. Ele ajuda a diagnosticar ataques à medida que ocorrem, correlacionando alertas diferentes e sinais de baixa fidelidade em incidentes de segurança.

A análise de fusão é a tecnologia e o back-end analítico que capacita os incidentes da central de segurança, permitindo que ele correlacione alertas e sinais contextuais diferentes. A fusão examina os diferentes sinais relatados em uma assinatura entre os recursos. O Fusion localiza padrões que revelam a progressão de ataque ou sinais com informações contextuais compartilhadas, indicando que você deve usar um procedimento de resposta unificado para eles.

O Fusion Analytics combina o conhecimento do domínio de segurança com o ia para analisar alertas, descobrindo novos padrões de ataque conforme eles ocorrem. 

A central de segurança aproveita a matriz de ataques MITRE para associar alertas à sua intenção percebida, ajudando a formalizar o conhecimento do domínio de segurança. Além disso, usando as informações coletadas para cada etapa de um ataque, a central de segurança pode eliminar a atividade que parece ser uma das etapas de um ataque, mas, na verdade, não é.

Como os ataques geralmente ocorrem em diferentes locatários, a central de segurança pode combinar algoritmos de ia para analisar as sequências de ataque que são relatadas em cada assinatura. Essa técnica identifica as sequências de ataque como padrões de alerta predominantes, em vez de apenas ser associada incidentalmente entre si.

Durante uma investigação de um incidente, os analistas geralmente precisam de um contexto extra para alcançar uma veredicto sobre a natureza da ameaça e como atenuá-la. Por exemplo, mesmo quando uma anomalia de rede é detectada, sem entender o que mais está acontecendo na rede ou em relação ao recurso de destino, é difícil entender as ações a serem tomadas em seguida. Para ajudar, um incidente de segurança pode incluir artefatos, eventos relacionados e informações. As informações adicionais disponíveis para incidentes de segurança variam de acordo com o tipo de ameaça detectada e a configuração do seu ambiente. 

> [!TIP]
> Para obter uma lista de alertas de incidente de segurança que podem ser produzidos pela análise de fusão, consulte a [tabela de referência de alertas](alerts-reference.md#alerts-fusion).

:::image type="content" source="./media/security-center-alerts-cloud-smart/security-incident.png" alt-text="Captura de tela do relatório de incidente de segurança detectado":::


Para entender melhor os incidentes de segurança, consulte [como gerenciar incidentes de segurança na central de segurança do Azure](security-center-incident.md).

