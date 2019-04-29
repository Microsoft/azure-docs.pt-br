---
title: Gerenciando recomendações de segurança na Central de Segurança do Azure | Microsoft Docs
description: Este documento mostra como as recomendações na Central de Segurança do Azure ajudam a proteger os recursos do Azure e a cumprir as políticas de segurança.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2018
ms.author: rkarlin
ms.openlocfilehash: e3b4da1c1d835e9d630c000055af058aa7b45968
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60905906"
---
# <a name="managing-security-recommendations-in-azure-security-center"></a>Gerenciando recomendações de segurança na Central de Segurança do Azure
Este documento mostra como usar as recomendações na Central de Segurança do Azure para ajudar a proteger os recursos do Azure.

> [!NOTE]
> Este documento apresenta o serviço usando uma implantação de exemplo.  Este documento não é um guia passo a passo.
>
>

## <a name="what-are-security-recommendations"></a>O que são recomendações de segurança?
A Central de Segurança analisa periodicamente o estado de segurança de seus recursos do Azure. Quando a Central de Segurança identifica possíveis vulnerabilidades de segurança, cria recomendações. As recomendações o orientam ao longo do processo de configuração dos controles necessários.

## <a name="implementing-security-recommendations"></a>Implementando recomendações de segurança
### <a name="set-recommendations"></a>Definir recomendações
Em [Configurando políticas de segurança na Central de Segurança do Azure](tutorial-security-policy.md), você aprenderá a:

* Configurar políticas de segurança.
* Habilitar a coleta de dados.
* Escolher quais recomendações ver como parte da política de segurança.

As recomendações de política atuais giram em torno de atualizações do sistema, regras de linha de base, programas antimalware, [grupos de segurança de rede](../virtual-network/security-overview.md) em sub-redes e em interfaces de rede, auditoria do banco de dados SQL, Transparent Data Encryption do banco de dados SQL e firewalls do aplicativo Web.  [Configurando políticas de segurança](tutorial-security-policy.md) fornece uma descrição de cada opção de recomendação.

### <a name="monitor-recommendations"></a>Monitorar as recomendações
Depois de definir uma política de segurança, a Central de Segurança analisa o estado de segurança de seus recursos para identificar possíveis vulnerabilidades. O bloco **Recomendações** na folha **Visão geral** permite saber o número total de recomendações identificadas pela Central de Segurança.

![Bloco Recomendações][1]

Para ver os detalhes de cada recomendação, selecione o **bloco Recomendações** em **Visão geral**. **Recomendações** se abre.

![Recomendações de filtro][2]

Você pode filtrar as recomendações. Para filtrar as recomendações, selecione **Filtro** na folha **Recomendações**. A folha **Filtro** é aberta e você seleciona os valores de gravidade e de estado que deseja ver.


* **RECOMENDAÇÕES**: A recomendação.
* **PROTEGER O IMPACTO DE PONTUAÇÃO**:
* **RECURSO:** lista os recursos aos quais essa recomendação se aplica.
* **BARRAS DE STATUS**:  Descreve a gravidade dessa recomendação específica:
   * **Alta (vermelho)**: existe uma vulnerabilidade em um recurso significativo (como um aplicativo, uma VM ou um grupo de segurança de rede) e ela requer atenção.
   * **Médio (laranja)**: existe uma vulnerabilidade e etapas não críticas ou adicionais são necessárias para eliminá-la ou para concluir um processo.
   * **Baixa (azul)**: existe uma vulnerabilidade que deve ser resolvida, mas não exige atenção imediata. (Por padrão, não são apresentadas recomendações baixas, mas você pode filtrar as recomendações baixas caso deseje vê-las.) 
   * **Íntegro (verde)**:
   * **Não disponível (cinza)**:
 


> [!NOTE]
> Você deve compreender os [modelos de implantação clássica e do Gerenciador de Recursos](../azure-classic-rm.md) para recursos do Azure.
> 
> 
> ### <a name="apply-recommendations"></a>Aplicar recomendações
> Depois de examinar todas as recomendações, decida qual delas aplicar primeiro. É recomendável usar a classificação de gravidade como o parâmetro principal para avaliar quais recomendações devem ser aplicadas primeiro.



## <a name="next-steps"></a>Próximas etapas
Neste documento, você foi apresentado às recomendações de segurança da Central de Segurança. Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Configurando políticas de segurança na Central de Segurança do Azure](tutorial-security-policy.md) : saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) : saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciando e respondendo aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) : aprenda a gerenciar e responder aos alertas de segurança.
* [Monitorando as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md) : saiba como monitorar o status de integridade de suas soluções de parceiros.
* [Perguntas frequentes da Central de Segurança do Azure](security-center-faq.md) : encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) : encontre postagens no blog sobre conformidade e segurança do Azure.

<!--Image references-->
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png
