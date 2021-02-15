---
title: Por quanto tempo o Azure AD armazena dados de relatório? | Microsoft Docs
description: Saiba por quanto tempo o Azure armazena os vários tipos de dados de relatório.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/05/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e240e8ec53ce007be1a858af7b6e41273ca8831
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093631"
---
# <a name="how-long-does-azure-ad-store-reporting-data"></a>Por quanto tempo o Azure AD armazena dados de relatório?


Neste artigo, você saberá mais sobre as políticas de retenção de dados dos diferentes relatórios de atividade no Azure Active Directory. 

### <a name="when-does-azure-ad-start-collecting-data"></a>Quando o Azure AD inicia a coleção de dados?

| Edição do Azure AD | Início da Coleta |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | Quando você se inscrever para uma assinatura |
| AD do Azure Gratuito| Na primeira vez que você abrir a [folha do Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) ou usar as [APIs de relatório](./overview-reports.md)  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>Quando os dados da atividade estarão disponíveis no portal do Azure?

- **Imediatamente** -se você já tiver trabalhado com relatórios no portal do Azure.
- **Dentro de 2 horas**: se você não tiver ativado os relatórios no portal do Azure.

---

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Em quanto tempo devo ver os dados das atividades após obter uma licença Premium?

Se você já tiver dados de atividades com sua licença gratuita, poderá vê-los imediatamente ao atualizar. Se você não tiver dados, levará até três dias para que os dados sejam exibidos nos relatórios após a atualização para uma licença Premium.

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Quando o Azure AD inicia a coleção de dados do sinal de segurança?  

Para sinais de segurança, o processo de coleção é iniciado quando você aceita usar a **Central do Identity Protection**. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Por quanto tempo o Azure AD armazena os dados?

**Relatórios de atividades**    

| Relatório                 | AD do Azure Gratuito | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Logs de auditoria             | 7 dias        | 30 dias             | 30 dias             |
| Entradas               | 7 dias        | 30 dias             | 30 dias             |
| Uso do Azure AD MFA        | 30 dias       | 30 dias             | 30 dias             |

É possível manter os dados da atividade de entrada e de auditoria por mais tempo que o período de retenção padrão descrito acima, roteando-os para uma conta de armazenamento do Azure usando o Azure Monitor. Para obter mais informações, consulte [Arquivar logs do Azure AD em uma conta de armazenamento do Azure](quickstart-azure-monitor-route-logs-to-storage-account.md).

**Sinais de Segurança**

| Relatório         | AD do Azure Gratuito | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| Usuários em risco  | 7 dias        | 30 dias             | 90 dias             |
| Entradas suspeitas | 7 dias        | 30 dias             | 90 dias             |

---

### <a name="can-i-see-last-months-data-after-getting-an-azure-ad-premium-license"></a>Eu poderei visualizar os dados do último mês, após obter uma licença Premium do Azure AD?

**Não**, você não pode. O Azure armazena até sete dias de dados de atividade para uma versão gratuita. Isso significa que, quando você alternar de uma versão gratuita para uma para a Premium, só poderá ver até 7 dias de dados.

---
