---
title: Auditoria e relatórios de um usuário de colaboração B2B - Azure AD
description: As propriedades do usuário convidado são configuráveis na colaboração B2B do Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a805f643e5f93270fe4721f527073baa9a1e4c8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87908087"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>Auditoria e relatórios de um usuário de colaboração B2B
Com usuários convidados, você conta com funcionalidades de auditoria semelhantes àquelas dos usuários membros. 

## <a name="access-reviews"></a>Análises de acesso
É possível usar as revisões de acesso para verificar periodicamente se usuários convidados ainda precisam acessar os recursos. O recurso **Revisões de acesso** está disponível no **Azure Active Directory** em **Identidades Externas** > **Revisões de acesso**. Também é possível procurar por "revisões de acesso" em **Todos os serviços** no portal do Azure. Para saber como usar as revisões de acesso, consulte [Gerenciar acesso de convidado com revisões de acesso do Azure AD](../governance/manage-guest-access-with-access-reviews.md).

## <a name="audit-logs"></a>Logs de auditoria

Os logs de auditoria do Azure AD fornecem registros das atividades do usuário e do sistema, incluindo atividades iniciadas por usuários convidados. Para acessar os logs de auditoria, no **Azure Active Directory**, em **monitoramento**, selecione **Logs de auditoria**. Este é um exemplo do histórico de convites e resgates do convidado Davi Barros:

![Captura de tela mostrando um exemplo de saída do log de auditoria](./media/auditing-and-reporting/audit-log.png)

Você pode se aprofundar em cada um desses eventos para obter detalhes. Por exemplo, vamos examinar os detalhes de aceitação.

![Captura de tela mostrando um exemplo de saída dos detalhes da atividade](./media/auditing-and-reporting/activity-details.png)

Você também pode exportar esses logs do Azure AD e usar a ferramenta de relatório de sua preferência para obter relatórios personalizados.

### <a name="next-steps"></a>Próximas etapas

- [Propriedades de usuário de colaboração B2B](user-properties.md)

