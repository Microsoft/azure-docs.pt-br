---
title: "Solução de problemas: dados ausentes no logs de atividades do Azure Active Directory baixados | Microsoft Docs"
description: "Fornece uma resolução para dados ausentes nos logs de atividade do Azure Active Directory baixados."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 3d56f89035da4d1a0074256b165663f81fc2b01e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="i-cant-find-any-data-in-the-azure-active-directory-activity-logs-i-have-downloaded"></a>Não consigo encontrar dados nos logs de atividade do Azure Active Directory que baixei


## <a name="symptoms"></a>Sintomas

Baixei os logs de atividade (auditoria ou entradas) e não vejo todos os registros para o momento que escolhi. Por quê? 

 ![Relatórios](./media/active-directory-reporting-troubleshoot-missing-data-download/01.png)
 

## <a name="cause"></a>Causa

Quando você baixa os logs de atividade no portal do Azure, limitamos a escala para 120 mil registros, classificados pelos mais recentes. 

## <a name="resolution"></a>Resolução

Você pode aproveitar as [APIs de relatórios do Azure AD](active-directory-reporting-api-getting-started.md) para buscar até um milhões de registros em qualquer momento determinado. Nossa abordagem recomendada é executar um script de uma forma agendada que chame as APIs de geração de relatórios para buscar registros de maneira incremental durante um período de tempo (por exemplo, por dia ou por semana).

## <a name="next-steps"></a>Próximas etapas
Veja as [Perguntas frequentes sobre os relatórios do Azure Active Directory](active-directory-reporting-faq.md).

