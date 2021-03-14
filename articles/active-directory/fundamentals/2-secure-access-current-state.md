---
title: Descobrir o estado atual da colaboração externa com o Azure Active Directory
description: Aprenda métodos para descobrir o estado atual da sua colaboração.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30858e9978f7e8857c5f8a2dcdfd7455f6e97b60
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553418"
---
# <a name="discover-the-current-state-of-external-collaboration-in-your-organization"></a>Descubra o estado atual da colaboração externa em sua organização 

Antes de descobrir o estado atual de sua colaboração externa, você deve [determinar a postura de segurança desejada](1-secure-access-posture.md). Você considerará as necessidades de sua organização para controle centralizado vs. delegado e quaisquer metas relevantes de governança, normativas e de conformidade. 

Os indivíduos em sua organização provavelmente já estão colaborando com usuários de outras organizações. A colaboração pode ser por meio de recursos em aplicativos de produtividade, como Microsoft 365, por email ou compartilhando recursos com usuários externos. Os pilares do seu plano de governança serão formados à medida que você descobrir 
*   os usuários que iniciam a colaboração externa.
*   os usuários externos e as organizações com as quais você está colaborando.
*   o acesso que está sendo concedido a usuários externos.


## <a name="users-initiating-external-collaboration"></a>Usuários que iniciam colaboração externa

Os usuários que iniciam a colaboração externa melhor entendem os aplicativos mais relevantes para colaboração externa e quando esse acesso deve terminar. Entender esses usuários pode ajudá-lo a determinar quem deve ser delegado a permissão para convidar usuários externos, criar pacotes de acesso e concluir as revisões de acesso.

Para localizar os usuários que estão colaborando no momento, examine o [log de auditoria Microsoft 365 para compartilhar e acessar atividades de solicitação](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance#sharing-and-access-request-activities). Você também pode examinar o [log de auditoria do Azure ad para obter detalhes sobre quem convidou usuários B2B](../external-identities/auditing-and-reporting.md) para seu diretório.

## <a name="find-current-collaboration-partners"></a>Encontre parceiros de colaboração atuais

Usuários externos podem ser [usuários B2B do Azure ad](../external-identities/what-is-b2b.md) (preferível) com credenciais gerenciadas por parceiros ou usuários externos com credenciais provisionadas localmente. Esses usuários normalmente são (mas nem sempre) marcados com um UserType de convidado. Você pode enumerar usuários convidados por meio da [API Microsoft Graph](/graph/api/user-list?tabs=http), do [PowerShell](/graph/api/user-list?tabs=http)ou do [portal do Azure](../enterprise-users/users-bulk-download.md).

### <a name="use-email-domains-and-companyname-property"></a>Usar domínios de email e a propriedade companyName

As organizações externas podem ser determinadas pelos nomes de domínio dos endereços de email do usuário externo. Se os provedores de identidade do consumidor, como o Google, forem compatíveis, isso pode não ser possível. Nesse caso, recomendamos que você escreva o atributo companyName para identificar claramente a organização externa do usuário.

### <a name="use-allow-or-deny-lists"></a>Usar listas de permissão ou negação

Considere se sua organização deseja permitir a colaboração com apenas organizações específicas. Como alternativa, considere se sua organização deseja bloquear a colaboração com organizações específicas.  No nível do locatário, há uma [lista de permitir ou negar](../external-identities/allow-deny-list.md), que pode ser usada para controlar os convites e os resgates de B2B gerais, independentemente da origem (por exemplo, equipes, SharePoint e portal do Azure).
Se você estiver usando o gerenciamento de direitos, também poderá definir o escopo de pacotes de acesso para um subconjunto de seus parceiros usando a configuração de organizações conectadas específicas, conforme mostrado abaixo.


![Captura de tela de permitir lista de negações na criação de um novo pacote de acesso.](media/secure-external-access/2-new-access-package.png)


## <a name="find-access-being-granted-to-external-users"></a>Encontrar acesso concedido a usuários externos

Depois de ter um inventário de usuários e organizações externos, você pode determinar o acesso concedido a esses usuários usando a API de Microsoft Graph para determinar a [Associação de grupo](/graph/api/resources/groups-overview) do Azure ad ou a [atribuição direta de aplicativo](/graph/api/resources/approleassignment) no Azure AD.


### <a name="enumerate-application-specific-permissions"></a>Enumerar permissões específicas do aplicativo

Você também pode executar a enumeração de permissão específica do aplicativo. Por exemplo, você pode gerar programaticamente um relatório de permissão para o SharePoint online usando [esse script](https://gallery.technet.microsoft.com/office/SharePoint-Online-c9ec4f64).

Investigue especificamente o acesso a todos os seus aplicativos comerciais e críticos para os negócios para que você esteja totalmente atento a qualquer acesso externo.

### <a name="detect-ad-hoc-sharing"></a>Detectar compartilhamento ad hoc
Se seus planos de email e rede o habilitarem, você poderá investigar o conteúdo que está sendo compartilhado por email ou por meio de aplicativos SaaS (software como serviço) não autorizados. [Microsoft 365 proteção contra perda de dados](/microsoft-365/compliance/data-loss-prevention-policies) ajuda a identificar, impedir e monitorar o compartilhamento acidental de informações confidenciais em sua infraestrutura de Microsoft 365. [Microsoft Cloud app Security](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/cloud-app-security) pode ajudá-lo a identificar o uso de aplicativos SaaS não autorizados em seu ambiente.

## <a name="next-steps"></a>Próximas etapas

Consulte os artigos a seguir sobre como proteger o acesso externo aos recursos. Recomendamos que você execute as ações na ordem listada.

1. [Determinar sua postura de segurança para acesso externo](1-secure-access-posture.md)

2. [Descubra seu estado atual](2-secure-access-current-state.md) (você está aqui.)

3. [Criar um plano de governança](3-secure-access-plan.md)

4. [Usar grupos de segurança](4-secure-access-groups.md)

5. [Transição para B2B do Azure AD](5-secure-access-b2b.md)

6. [Acesso seguro com gerenciamento de direitos](6-secure-access-entitlement-managment.md)

7. [Acesso seguro com políticas de acesso condicional](7-secure-access-conditional-access.md)

8. [Acesso seguro com rótulos de sensibilidade](8-secure-access-sensitivity-labels.md)

9. [Proteger o acesso ao Microsoft Teams, OneDrive e SharePoint](9-secure-access-teams-sharepoint.md)