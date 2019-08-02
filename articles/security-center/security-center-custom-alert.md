---
title: Regras de alerta personalizadas na Central de Segurança do Azure | Microsoft Docs
description: Este documento ajuda você a criar regras de alerta personalizadas na Central de Segurança do Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f335d8c4-0234-4304-b386-6f1ecda07833
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2018
ms.author: rkarlin
ms.openlocfilehash: 984bd4d5db210679884655721be0cbcdac8c1705
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485292"
---
# <a name="custom-alert-rules-in-azure-security-center-preview"></a>Regras de alerta personalizadas na Central de Segurança do Azure (versão prévia)
Este documento ajuda você a criar regras de alerta personalizadas na Central de Segurança do Azure.

> [!NOTE]
> Alertas personalizados foi desativado na Central de segurança. 

## <a name="retirement-of-custom-alert-rules-in-azure-security-center"></a>Regras de desativação de alerta personalizadas na Central de segurança do Azure

A experiência de alertas personalizados foi desativada em 30 de junho de 2019, devido à desativação da infraestrutura subjacente que contavam com ele. todos os alertas personalizados definidos antes da retriement são não entrarem em vigor e não estão sendo gerados alertas de segurança com base nessas regras de alertas personalizados. Você ainda pode exibir as consultas dos suas regras de alerta personalizadas na Central de segurança para recriá-los em alternativas mencionadas a seguir:

Os usuários são aconselhados a qualquer um:
- Habilitar [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) e use seu interno [analytics](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) funcionalidade para recriar suas regras de alerta
- Recriar seus alertas com alertas do log do Azure Monitor
                                     
Para manter seus alertas existentes e recriá-las no Azure Sentinel, por favor [inicie o Azure Sentinel](https://portal.azure.com/#create/Microsoft.ASI/preview). Como primeira etapa, selecione o espaço de trabalho onde os alertas personalizados são armazenados e, em seguida, selecione o item de menu 'Analytics' para configurar suas regras de alertas personalizados. Visite o [documentação](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) para obter informações adicionais.

> [!NOTE]
> Alertas personalizados usando [pesquisa](https://docs.microsoft.com/azure/azure-monitor/log-query/search-queries) ou [união](https://docs-analytics-eus.azurewebsites.net/queryLanguage/query_language_unionoperator.html) consultas de instruções não têm suporte no Azure Sentinel. Edite esses alertas antes de executar a migração.

Para recriar seus alertas usando alertas de log do Azure Monitor, consulte: [Criar, exibir e gerenciar alertas de log usando o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) para obter instruções sobre como criar alertas de log. Para obter uma visão geral de alertas de log no Azure Monitor, clique em [aqui](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

## <a name="what-are-custom-alert-rules-in-security-center"></a>O que são regras de alerta personalizadas na Central de Segurança?

A Central de Segurança tem um conjunto predefinido de [alertas de segurança](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts), que é disparado em caso de ameaça ou de atividade suspeita. Em alguns cenários, convém criar um alerta personalizado para atender a necessidades específicas do seu ambiente.

As regras de alerta personalizadas na Central de Segurança permitem que você defina novos alertas de segurança com base nos dados que já são coletados do seu ambiente. Você pode criar consultas e o resultado das consultas pode ser usado como critério para a regra personalizada; quando esse critério for atendido, a regra será executada. Você pode usar os eventos de segurança de computadores, logs de solução de segurança de parceiros ou dados ingeridos usando APIs para criar consultas personalizadas.

> [!NOTE]
> [Alertas personalizados não têm suporte no recurso de ](security-center-investigation.md)investigação da Central de segurança.
>
>

## <a name="how-to-create-a-custom-alert-rule-in-security-center"></a>Como criar uma regra de alerta personalizada na Central de Segurança?

Abra o painel **Central de Segurança** e execute as seguintes etapas para criar uma regra de alerta personalizada:

1.  No painel esquerdo, em **Detecção** clique em **Regras de alerta personalizados (Versão prévia)** .
2.  Na página **Central de Segurança – Regras de alerta personalizadas (Versão prévia)** , clique em **Nova regra de alerta personalizada**.

    ![Alerta personalizado](./media/security-center-custom-alert/security-center-custom-alert-fig1.png)

3.  A página Criar regra de alerta personalizada será exibida com as seguintes opções:

    ![Criar](./media/security-center-custom-alert/security-center-custom-alert-fig2.png)

4.  Digite o nome para a regra personalizada no campo **Nome**.
5.  Digite uma breve descrição que reflete a intenção da regra no campo **Descrição**.
6.  Selecione o nível de gravidade (Alto, Médio, Baixo) de acordo com suas necessidades no campo **Gravidade**.
7.  Selecione a assinatura à qual esta regra é aplicável no campo **Assinatura**.
8.  Selecione o workspace que você quer monitorar com essa regra no campo **Workspace** e, no campo **Consulta de Pesquisa**, a consulta que você quer usar para obter os resultados.

    > [!NOTE]
    > É necessário ter permissão de gravação no workspace que você selecionou para armazenar o alerta personalizado.
    >
    >

    O resultado da consulta dispara o alerta. Observe que, quando você digita uma consulta válida, a marca de seleção verde é exibida no canto direito deste campo:

    ![Consulta](./media/security-center-custom-alert/security-center-custom-alert-fig3.png)

10. Selecione o período de tempo em que a consulta acima será executada no campo **Período**. Observe que o resultado de pesquisa na parte inferior do campo será alterado de acordo com o período de tempo que você selecionar.

    ![Período](./media/security-center-custom-alert/security-center-custom-alert-fig4.png)

11. No campo **Avaliação**, selecione a frequência com a qual a regra deve ser avaliada e executada.
12. No campo **Número de resultados**, selecione o operador (maior que ou menor que).
13. No campo **Limite**, digite um número que será usado como referência para o operador que foi selecionado anteriormente.
14. **Habilite a opção Suprimir Alertas** se você deseja definir um tempo de espera antes que a Central de Segurança envie outro alerta para a regra.
15. Clique em **OK** para concluir.

Quando a nova regra de alerta for criada, ela aparecerá na lista de regras de alerta personalizadas. Quando as condições da regra são atendidas, um novo alerta é disparado e você pode ver no painel **Alertas de Segurança**.

![Alerta](./media/security-center-custom-alert/security-center-custom-alert-fig5.png)

Observe que os parâmetros (consulta de pesquisa, limite, etc.) estabelecidos durante a criação da regra estão disponíveis no alerta para essa regra personalizada.

## <a name="see-also"></a>Consulte também
Neste documento, você aprendeu a criar uma regra de alerta personalizada na Central de Segurança do Azure. Para saber mais sobre a Central de Segurança do Azure, veja o seguinte:

* [Gerenciando e respondendo aos alertas de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Saiba como gerenciar alertas e responder a incidentes de segurança na Central de Segurança.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md). Saiba como monitorar a integridade dos recursos do Azure.
* [Noções básicas de alertas de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Saiba mais sobre os diferentes tipos de alertas de segurança.
* [Guia de solução de problemas da Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Saiba como solucionar problemas comuns na Central de Segurança.
* [Perguntas Frequentes sobre a Central de Segurança do Azure](security-center-faq.md). Encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/). Encontre postagens no blog sobre a conformidade e segurança do Azure.
