---
title: "Monitorar identidade e acesso na Central de Segurança do Azure | Microsoft Docs"
description: "Aprenda a usar os recursos de identidade e acesso na Central de Segurança do Azure para monitorar a atividade de acesso e os problemas com identidade do usuário."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: yurid
ms.openlocfilehash: 842045fbcb5b4f661cc006a207f4087370a7b4ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-identity-and-access-in-azure-security-center"></a>Monitorar identidade e acesso na Central de Segurança do Azure
Este artigo ajuda você a usar a Central de Segurança do Azure para monitorar a identidade e a atividade de acesso do usuário.

## <a name="why-monitor-identity-and-access"></a>Por que monitorar a identidade e o acesso?
A identidade deve ser o plano de controle de sua empresa e a proteção de sua identidade deve ser sua prioridade. No passado, havia perímetros em torno das organizações, e esses perímetros eram um dos limites de defesa principais. Hoje em dia, com mais dados e aplicativos se mudando para a nuvem, a identidade se tornou o novo perímetro.

Ao monitorar as atividades de identidade, você poderá agir de forma proativa antes que um incidente local ocorra ou agir de forma reativa para interromper uma tentativa de ataque. O painel Acesso e Identidade fornece uma visão geral do seu estado de identidade, incluindo:

* Número de tentativas de logon com falha. 
* Contas de usuários que foram usadas durante as tentativas.
* Contas que foram bloqueadas.
* Contas com senhas alteradas ou redefinidas. 
* Número de contas que estão conectadas no momento.

## <a name="monitor-identity-and-access-activities"></a>Monitorar a identidade e as atividades de acesso
Para ver atividades atuais relacionadas à identidade e ao acesso, você precisa acessar o painel **Identidade e Acesso**.

1. Abra o painel **Central de Segurança**.

2. No painel esquerdo, em **Prevenção**, selecione **Identidade e Acesso**. Se você tiver vários espaços de trabalho, o seletor de espaço de trabalho será exibido.

    ![Seleção de espaço de trabalho](./media/security-center-identity-access\security-center-identity-access-fig1.png)

    > [!NOTE]
    > Se a coluna na ponta direita mostra **ATUALIZAR PLANO**, esse espaço de trabalho está usando a assinatura gratuita. Atualize para a assinatura Standard a fim de usar o recurso. Se a coluna na ponta direita mostra **REQUER ATUALIZAÇÃO**, atualize o [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) para usar esse recurso. Para saber mais sobre o plano de preço, leia Preços da Central de Segurança. 
    > 
3. Se você tem mais de um espaço de trabalho para investigar, você pode priorizar a investigação de acordo com a coluna **LOGONS COM FALHA**. Ela mostra o número atual de tentativas de logon com falha no espaço de trabalho. Selecione o espaço de trabalho que você deseja usar e o painel **Identidade e Acesso** será exibido.

    ![Identidade e acesso](./media/security-center-identity-access\security-center-identity-access-fig2.png)

4. As informações disponíveis neste painel podem ajudá-lo imediatamente a identificar uma possível atividade suspeita. O painel é dividido em três áreas principais:

    a. **Postura de identidade**. Resume as atividades relacionadas à identidade que ocorrem no espaço de trabalho.

    b. **Falhas de logon**. Ajuda a identificar rapidamente a causa principal das tentativas de logon com falha. Mostra uma lista das 10 principais contas com falha na maioria das tentativas de logon.

    c. **Logons ao longo do tempo**. Ajuda a identificar rapidamente o número de logons ao longo do tempo. Ele mostra uma lista das principais tentativas de logon em contas de computador.
    
Independentemente do bloco selecionado, o painel exibido se baseia na consulta da Pesquisa de Logs. A única diferença é o tipo de consulta e o resultado. Você ainda pode selecionar um item, como um computador, e ver dados relevantes. 

## <a name="see-also"></a>Consulte também
Neste artigo, você aprendeu como monitorar a identidade e o acesso na Central de Segurança. Para saber mais sobre a Central de Segurança, confira estes artigos:

* [Gerencie e responda a alertas de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Saiba como gerenciar alertas e responder a incidentes de segurança na Central de Segurança.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md). Saiba como monitorar a integridade dos recursos do Azure.
* [Noções básicas de alertas de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Saiba mais sobre os diferentes tipos de alertas de segurança.
* [Guia de solução de problemas da Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Saiba como solucionar problemas comuns na Central de Segurança. 
* [Perguntas Frequentes sobre a Central de Segurança do Azure](security-center-faq.md). Encontre respostas para perguntas frequentes sobre como usar a Central de Segurança.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/). Encontre postagens no blog sobre a conformidade e segurança do Azure.

