---
title: "Pesquisa da Central de Segurança do Azure | Microsoft Docs"
description: "Saiba como a Central de Segurança do Azure usa a pesquisa do Log Analytics para recuperar e analisar seus dados de segurança."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: terrylan
ms.openlocfilehash: 513c98237a322dabd6b2bf13443e8998ca843b1d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-center-search"></a>Pesquisa da Central de Segurança do Azure
A Central de Segurança do Azure usa a [pesquisa do Log Analytics](../log-analytics/log-analytics-log-searches.md) para recuperar e analisar seus dados de segurança. O Log Analytics inclui uma linguagem de consulta para recuperar e consolidar dados rapidamente. Na Central de Segurança, você pode aproveitar a pesquisa do Log Analytics para construir consultas e analisar os dados coletados.

A pesquisa está disponível nas camadas Gratuita e Standard da Central de Segurança.  Os dados disponíveis em suas pesquisas de log dependem do nível de camada aplicado ao seu espaço de trabalho.  Para saber mais, confira a [página de preços](../security-center/security-center-pricing.md) da Central de Segurança.


> [!NOTE]
> A Central de Segurança não salva dados de segurança de um espaço de trabalho na Camada gratuita. Você pode enviar vários tipos de logs para um espaço de trabalho na Camada gratuita e pesquisar nesses dados, mas os resultados da pesquisa não incluirão os dados da Central de Segurança. A Central de Segurança só salva dados em um espaço de trabalho na camada Standard.
>
>

## <a name="access-search"></a>Pesquisa de acesso
1. No menu principal da Central de Segurança, selecione **Pesquisar**.

  ![Selecionar Pesquisa de log][1]

2. A Central de Segurança lista todos os espaços de trabalho em suas assinaturas do Azure. Selecione um espaço de trabalho. (Se você tiver apenas um espaço de trabalho, o seletor desse espaço de trabalho não aparecerá).

  ![Selecionar um espaço de trabalho][2]

3. A **Pesquisa de Logs** é aberta. Para consultar mais dados no espaço de trabalho selecionado, insira este exemplo de consulta:

  SecurityEvent | where EventID == 4625 | summarize count() by TargetAccount

  O resultado mostra todas as contas com falha de logon (evento 4625).

  ![Resultados da Pesquisa][3]

Confira [Linguagem de consulta do Log Analytics](../log-analytics/log-analytics-search-reference.md) para saber mais sobre como consultar os dados no espaço de trabalho selecionado.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a acessar a pesquisa na Central de Segurança. A Central de Segurança usa a pesquisa do Log Analytics. Para saber mais sobre a pesquisa do Log Analytics, confira:

- [O que é o Log Analytics?](../log-analytics/log-analytics-overview.md) – Visão geral sobre o Log Analytics
- [Compreender as pesquisas de logs no Log Analytics](../log-analytics/log-analytics-log-search-new.md) - descreve como as pesquisas de logs são utilizadas no Log Analytics e fornece conceitos que deverão ser compreendidos antes de criar uma pesquisa de log
- [Localizar dados usando as pesquisas de logs no Log Analytics](../log-analytics/log-analytics-log-searches.md) – tutorial sobre como usar a pesquisa de log
- [Referência de pesquisa do Log Analytics](../log-analytics/log-analytics-search-reference.md) – descreve a linguagem de consulta no Log Analytics

Para saber mais sobre a Central de Segurança, confira:

- [Visão geral da Central de Segurança](security-center-intro.md) – Descreve as principais funcionalidades da Central de Segurança

<!--Image references-->
[1]: ./media/security-center-search/search.png
[2]: ./media/security-center-search/workspace-selector.png
[3]: ./media/security-center-search/log-search.png
