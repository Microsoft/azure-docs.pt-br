---
title: Determine sua postura de segurança para colaboração externa com Azure Active Directory
description: Antes de poder executar um plano de segurança de acesso externo, você deve determinar o que está tentando alcançar.
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
ms.openlocfilehash: 37c27e84f15a01a2d8832baae137518685de59a8
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98725434"
---
# <a name="determine-your-security-posture-for-external-access"></a>Determinar sua postura de segurança para acesso externo 

Ao considerar o controle do acesso externo, você precisará avaliar as necessidades de segurança e colaboração de sua organização em geral e em cada cenário. No nível organizacional, considere a quantidade de controle que você precisa que sua equipe de ti tenha sobre a colaboração diária. As organizações de setores regulamentados podem exigir mais controle de ti. Por exemplo, um prestador de defesa pode ser solicitado a identificar e documentar positivamente cada usuário externo, seu acesso e a remoção do acesso. Esse requisito pode estar em todo o acesso ou em cenários ou cargas de trabalho específicas. Na outra extremidade do espectro, uma empresa de consultoria geralmente pode permitir que os usuários finais determinem os usuários externos com os quais eles precisam colaborar, dentro de determinados trilhos de proteção de ti. 

![TI versus controle de colaboração do usuário final](media/secure-external-access/1-overall-control.png)

> [!NOTE]
> O controle excessivamente restrito na colaboração pode levar a maiores orçamentos de ti, produtividade reduzida e resultados de negócios atrasados. Quando os canais de colaboração oficiais são percebidos como muito onerosos, os usuários finais tendem a contornar os sistemas fornecidos para que seus trabalhos sejam feitos, por exemplo, enviando documentos desprotegidos por email.

## <a name="think-in-terms-of-scenarios"></a>Considere em termos de cenários

Em muitos casos, ele pode delegar acesso de parceiro, pelo menos em alguns cenários, ao mesmo tempo em que fornece proteção de trilhos para segurança. Os trilhos de proteção de ti podem ajudar a garantir que a propriedade intelectual permaneça segura e, ao mesmo tempo, capacitar os funcionários a colaborar com parceiros para realizar o trabalho.

Ao considerar os cenários em sua organização, avalie a necessidade de acesso de funcionários versus parceiros de negócios aos recursos. Um banco pode ter necessidades de conformidade que restringem o acesso a determinados recursos, como informações de conta de usuário, a um pequeno grupo de funcionários internos. Por outro lado, o mesmo banco pode habilitar o acesso delegado para parceiros que trabalham em uma campanha de marketing.

![continuidade de governança por cenário](media\secure-external-access\1-scenarios.png)

Em cada cenário, considere 

* a sensibilidade das informações em risco

* Se você precisa restringir o que os parceiros podem ver sobre outros usuários

* o custo de uma violação versus o peso do controle centralizado e o conflito do usuário final

 Você também pode começar com controles gerenciados centralmente para atender aos destinos de conformidade e delegar o controle aos usuários finais ao longo do tempo. Todos os modelos de gerenciamento de acesso podem coexistir simultaneamente em uma organização. 

O uso de [credenciais gerenciadas por parceiros](../external-identities/what-is-b2b.md) fornece à sua organização um sinal essencial que encerra o acesso aos recursos quando o usuário externo perde o acesso aos recursos de sua própria empresa.

## <a name="goals-of-securing-external-access"></a>Metas de proteção do acesso externo

Os objetivos do acesso controlado por ti e delegado são diferentes.

**Os principais objetivos do acesso controlado por ti são:**

* Atenda aos destinos de governança, normativos e de conformidade (GRC). 

* Controle rigidamente o acesso do parceiro e quais parceiros podem ver sobre usuários Membros, grupos e outros parceiros.

**Os principais objetivos da delegação de acesso são:**

* Permitir que os proprietários de negócios controlem com quem eles colaboram, dentro das restrições de ti.

* Permita que os parceiros comerciais solicitem acesso com base nas regras definidas por proprietários de negócios.

Seja qual for sua aplicar para sua organização e cenários, você precisará: 

* **Controle o acesso a aplicativos, dados e conteúdo**. Isso pode ser feito por meio de uma variedade de métodos, dependendo de suas versões do [Azure ad](https://azure.microsoft.com/pricing/details/active-directory/) e [Microsoft 365](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans). 

* **Reduza a superfície de ataque**. O [Privileged Identity Management](../privileged-identity-management/pim-configure.md), a [DLP (prevenção de perda de dados)](/exchange/security-and-compliance/data-loss-prevention/data-loss-prevention) e os [recursos de criptografia](/exchange/security-and-compliance/data-loss-prevention/data-loss-prevention) reduzem a superfície de ataque.

* **Analise regularmente a atividade e o log de auditoria para confirmar a conformidade**. Ele pode delegar decisões de acesso a proprietários de negócios por meio do gerenciamento de direitos enquanto as revisões de acesso fornecem uma maneira de confirmar periodicamente o acesso contínuo. A classificação automatizada de dados com rótulos de sensibilidade ajuda a automatizar a criptografia de conteúdo confidencial, facilitando a conformidade dos usuários finais dos funcionários.

## <a name="next-steps"></a>Próximas etapas 

Consulte os artigos a seguir sobre como proteger o acesso externo aos recursos. Recomendamos que você execute as ações na ordem listada.

1. [Determine sua postura de segurança para acesso externo](1-secure-access-posture.md) (você está aqui.)

2. [Descobrir seu estado atual](2-secure-access-current-state.md)

3. [Criar um plano de governança](3-secure-access-plan.md)

4. [Usar grupos de segurança](4-secure-access-groups.md)

5. [Transição para B2B do Azure AD](5-secure-access-b2b.md)

6. [Acesso seguro com gerenciamento de direitos](6-secure-access-entitlement-managment.md)

7. [Acesso seguro com políticas de acesso condicional](7-secure-access-conditional-access.md)

8. [Acesso seguro com rótulos de sensibilidade](8-secure-access-sensitivity-labels.md)

9. [Proteger o acesso ao Microsoft Teams, OneDrive e SharePoint](9-secure-access-teams-sharepoint.md)
 

