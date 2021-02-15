---
title: Solucionar problemas de dados ausentes em logs de atividade | Microsoft Docs
description: Fornece uma resolução para dados ausentes nos logs de atividades do Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 7cbe4337-bb77-4ee0-b254-3e368be06db7
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f48c26a65314e2f23513fba155f07db3805a516
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93123057"
---
# <a name="troubleshoot-missing-data-in-the-azure-active-directory-activity-logs"></a>Solução de problemas: Dados ausentes nos logs de atividades do Azure Active Directory 

## <a name="i-cant-find-audit-logs-for-recent-actions-in-the-azure-portal"></a>Não consigo encontrar os logs de auditoria para ações mais recentes no portal do Azure

### <a name="symptoms"></a>Sintomas

Eu executei algumas ações no portal do Azure e esperava ver os logs de auditoria para essas ações na folha `Activity logs > Audit Logs`, mas não é possível encontrá-los.

 ![Captura de tela mostra entradas do log de auditoria.](./media/troubleshoot-missing-audit-data/01.png)
 
### <a name="cause"></a>Causa

As ações não são exibidas imediatamente nos logs de atividades. A tabela a seguir enumera nossos números de latência para os logs de atividades. 

| Relatório | Latência (P95) | Latência (P99) |
|--------|---------------|---------------|
| Auditoria de diretório | 2 minutos | 5 min |
| Atividade de entrada | 2 minutos | 5 min |

### <a name="resolution"></a>Resolução

Aguarde de 15 minutos a duas horas e verifique se as ações aparecem no log. Se você não vir os logs mesmo após duas horas, [crie um tíquete de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) e verificaremos o problema.

## <a name="i-cant-find-recent-user-sign-ins-in-the-azure-active-directory-sign-ins-activity-log"></a>Não consigo localizar entradas recentes do usuário no log de atividades de entradas do Azure Active Directory

### <a name="symptoms"></a>Sintomas

Eu entrei recentemente no portal do Azure e esperava ver os logs de auditoria para essas ações na folha `Activity logs > Sign-ins`, mas não é possível encontrá-los.

 ![Captura de tela mostra entradas no log de atividades.](./media/troubleshoot-missing-audit-data/02.png)
 
### <a name="cause"></a>Causa

As ações não são exibidas imediatamente nos logs de atividades. A tabela a seguir enumera nossos números de latência para os logs de atividades. 

| Relatório | Latência (P95) | Latência (P99) |
|--------|---------------|---------------|
| Auditoria de diretório | 2 minutos | 5 min |
| Atividade de entrada 2 minutos | 5 min |

### <a name="resolution"></a>Resolução

Aguarde de 15 minutos a duas horas e verifique se as ações aparecem no log. Se você não vir os logs mesmo após duas horas, [crie um tíquete de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) e verificaremos o problema.

## <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>Não consigo exibir mais de 30 dias de dados de relatório no portal do Azure

### <a name="symptoms"></a>Sintomas

Não consigo exibir mais de 30 dias de dados de entrada e de auditoria no portal do Azure. Por quê? 

 ![Captura de tela mostra o menu de data.](./media/troubleshoot-missing-audit-data/03.png)

### <a name="cause"></a>Causa

Dependendo da sua licença, as Ações do Azure Active Directory armazenam relatórios de atividades para as durações a seguir:

| Relatório           | AD do Azure Gratuito | Azure AD Premium P1 | Azure AD Premium P2 |
| ---              | ---           | ---                 | ---                 |
| Auditoria de Diretório  |  7 dias       | 30 dias             | 30 dias             |
| Atividade de Entrada | Não disponível. Você pode acessar sua própria atividade de entrada por 7 dias na folha de perfil do usuário individual | 30 dias | 30 dias             |

Para saber mais informações, confira [Políticas de retenção de relatório do Azure Active Directory](reference-reports-data-retention.md).  

### <a name="resolution"></a>Resolução

Você tem duas opções para manter os dados por mais de 30 dias. Você pode usar as [APIs de emissão de relatórios do Azure AD](concept-reporting-api.md) para recuperar os dados por meio de programação e armazená-los em um banco de dados. Como alternativa, você pode integrar os logs de auditoria em um sistema SIEM de terceiros, como o Splunk ou SumoLogic.

## <a name="next-steps"></a>Próximas etapas

* [API de retenção de relatório](reference-reports-data-retention.md).
* [Azure Active Directory latência de relatório](reference-reports-latencies.md).
* [Azure Active Directory perguntas frequentes sobre relatórios](reports-faq.md).

