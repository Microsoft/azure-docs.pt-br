---
title: Gerenciar o acesso externo com Azure Active Directory gerenciamento de direitos
description: Como usar Azure Active Directory gerenciamento de direitos como parte do seu plano de segurança de acesso externo geral.
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
ms.openlocfilehash: f06a54f59405d9833194b2e7d4488bc93d2437ae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98725171"
---
# <a name="manage-external-access-with-entitlement-management"></a>Gerenciar o acesso externo com gerenciamento de direitos 


O [Gerenciamento de direitos](../governance/entitlement-management-overview.md) é um recurso de governança de identidade que permite que as organizações gerenciem o ciclo de vida de identidade e acesso em escala automatizando fluxos de trabalho de solicitação de acesso, atribuições de acesso, revisões e expiração. O gerenciamento de direitos permite que não administradores delegados criem [pacotes de acesso](../governance/entitlement-management-overview.md) aos quais usuários externos de outras organizações podem solicitar acesso. Os fluxos de trabalho de aprovação de vários estágios podem ser configurados para avaliar solicitações e [provisionar](../governance/what-is-provisioning.md) usuários para acesso limitado a tempo com revisões recorrentes. O gerenciamento de direitos permite o provisionamento baseado em políticas e o desprovisionamento de contas externas.

## <a name="key-concepts-for-enabling-entitlement-management"></a>Conceitos principais para habilitar o gerenciamento de direitos

Os conceitos principais a seguir são importantes para entender o gerenciamento de direitos.

### <a name="access-packages"></a>Pacotes de acesso

Um [pacote do Access](../governance/entitlement-management-overview.md) é a base do gerenciamento de direitos. Pacotes de acesso são agrupamentos de recursos controlados por política que um usuário precisa para colaborar em um projeto ou realizar outras tarefas. Por exemplo, um pacote de acesso pode incluir:

* acesso a sites específicos do SharePoint.

* aplicativos empresariais, incluindo seus aplicativos internos e SaaS personalizados, como o Salesforce.

* Canais do Microsoft Teams.

* Grupos do Microsoft 365. 

### <a name="catalogs"></a>Catálogos

Os pacotes do Access residem em [catálogos](../governance/entitlement-management-catalog-create.md). Você cria um catálogo quando deseja agrupar recursos relacionados e pacotes de acesso e delega a capacidade de gerenciá-los. Primeiro, você adiciona recursos a um catálogo e, em seguida, pode adicionar esses recursos para acessar pacotes. Por exemplo, você pode querer criar um catálogo "Finance" e [delegar seu gerenciamento](../governance/entitlement-management-delegate.md) a um membro da equipe de finanças. Essa pessoa pode [Adicionar recursos](../governance/entitlement-management-catalog-create.md), criar pacotes de acesso e gerenciar a aprovação de acesso a esses pacotes.

O diagrama a seguir mostra um ciclo de vida de governança típico para um usuário externo que obtém acesso a um pacote de acesso que tem uma expiração.

![Um diagrama do ciclo de governança do usuário externo.](media/secure-external-access/6-governance-lifecycle.png)

### <a name="self-service-external-access"></a>Acesso externo de autoatendimento

Você pode orientar os pacotes de acesso por meio do [meu portal de acesso do Azure ad](../governance/entitlement-management-request-access.md) para permitir que usuários externos solicitem acesso. As políticas determinam quem pode solicitar um pacote de acesso. Você especifica quem tem permissão para solicitar o pacote de acesso:

* [Organizações conectadas](../governance/entitlement-management-organization.md) específicas

* Todas as organizações conectadas configuradas

* Todos os usuários de qualquer organização

* Membros ou usuários convidados que já estão em seu locatário

### <a name="approvals"></a>Aprovações   
Os pacotes do Access podem incluir a aprovação obrigatória para acesso. **Sempre implemente processos de aprovação para usuários externos**. As aprovações podem ser uma aprovação única ou de vários estágios. As aprovações são determinadas pelas políticas. Se os usuários internos e externos precisarem acessar o mesmo pacote, você provavelmente configurará políticas de acesso diferentes para diferentes categorias de organizações conectadas e para usuários internos.

### <a name="expiration"></a>Expiração  
Os pacotes do Access podem incluir uma data de validade. A expiração pode ser definida para um dia específico ou dar ao usuário um número específico de dias para o acesso. Quando o pacote de acesso expira e o usuário não tem nenhum outro acesso, o objeto de usuário convidado B2B que representa o usuário pode ser excluído ou bloqueado da entrada. Recomendamos que você imponha a expiração em pacotes de acesso para usuários externos. Nem todos os pacotes de acesso têm expirações. Para aqueles que não têm, certifique-se de executar revisões de acesso.

### <a name="access-reviews"></a>Análises de acesso

Os pacotes do Access podem exigir [revisões de acesso](../governance/manage-guest-access-with-access-reviews.md)periódicas, que exigem o proprietário do pacote ou um designador para atestar a necessidade contínua de acesso dos usuários. 

Antes de configurar sua análise, determine o seguinte.

* Quem

   * Quais são os critérios para o acesso contínuo?

   * Quem são os revisores especificados?

* Com que frequência as revisões agendadas devem ocorrer?

   * As opções internas incluem mensal, trimestral, bimestralmente ou anual. 

   * É recomendável trimestral ou com mais frequência para pacotes que dão suporte ao acesso externo. 

 

> [!IMPORTANT]
> As revisões de acesso de pacotes de acesso revisam o acesso concedido por meio do gerenciamento de direitos. Portanto, você deve configurar outros processos para revisar qualquer acesso fornecido a usuários externos fora do gerenciamento de direitos.

Para obter mais informações sobre revisões de acesso, consulte [planejando uma implantação de revisões de acesso do Azure ad](../governance/deploy-access-reviews.md).

## <a name="using-automation-in-entitlement-management"></a>Usando a automação no gerenciamento de direitos

Você pode executar [funções de gerenciamento de direitos usando Microsoft Graph](/graph/tutorial-access-package-api), incluindo

* [Gerenciar pacotes de acesso](/graph/api/resources/accesspackage?view=graph-rest-beta)

* [Gerenciar revisões de acesso](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta)

* [Gerenciar organizações conectadas](/graph/api/resources/connectedorganization?view=graph-rest-beta)

* [Gerenciar configurações de gerenciamento de direitos](/graph/api/resources/entitlementmanagementsettings?view=graph-rest-beta)

## <a name="recommendations"></a>Recomendações 

Recomendamos as práticas para controlar o acesso externo ao gerenciamento de direitos.

**Para projetos com um ou mais parceiros de negócios, [crie e use pacotes de acesso](../governance/entitlement-management-access-package-create.md) para carregar e provisionar o acesso dos usuários do parceiro aos recursos**. 

* Se você já tiver usuários B2B em seu diretório, também poderá atribuí-los diretamente aos pacotes de acesso apropriados.

* Você pode atribuir acesso no [portal do Azure](../governance/entitlement-management-access-package-assignments.md)ou via [Microsoft Graph](/graph/api/resources/accesspackageassignmentrequest?view=graph-rest-beta).

**Use suas configurações de governança de identidade para remover usuários do diretório quando seus pacotes de acesso expirarem**.

![Captura de tela de configuração gerenciar o ciclo de vida de usuários externos.](media/secure-external-access/6-manage-external-lifecycle.png)

Essas configurações se aplicam somente a usuários que foram integrados por meio do gerenciamento de direitos.

Delegue o **[Gerenciamento de catálogos e pacotes de acesso](../governance/entitlement-management-delegate.md) para proprietários de negócios, que têm mais informações sobre quem deve acessar**.

![Captura de tela da configuração de um catálogo.](media/secure-external-access/6-catalog-management.png)

**[Impor a expiração de pacotes de acesso](../governance/entitlement-management-access-package-lifecycle-policy.md) aos quais os usuários externos têm acesso.**


![Captura de tela da configuração da expiração do pacote de acesso.](media/secure-external-access/6-access-package-expiration.png)

* Se você souber a data de término de um pacote de acesso baseado em projeto, use a data de início para definir a data específica. 

* Caso contrário, recomendamos que a expiração não seja mais de 365 dias, a menos que seja conhecido como um compromisso de vários anos.

* Permitir que os usuários estendam o acesso.

* Exigir aprovação para conceder a extensão.

**[Impor revisões de acesso de pacotes](../governance/manage-guest-access-with-access-reviews.md) para evitar acesso impróprio para convidados.**

![Captura de tela da criação de um novo pacote de acesso.](media/secure-external-access/6-new-access-package.png)

* Impor análises trimestralmente.

* Para projetos sensíveis à conformidade, defina os revisores como revisores específicos, em vez de revisar automaticamente para usuários externos. Os usuários que estão acessam os gerenciadores de pacotes são um bom lugar para iniciar os revisores. 

* Para projetos menos confidenciais, ter os usuários autoReview reduzirá a carga da organização para remover o acesso de usuários que não estão mais em sua organização doméstica.

Para obter mais informações, consulte [controlar o acesso para usuários externos no gerenciamento de direitos do Azure ad](../governance/entitlement-management-external-users.md) 

### <a name="next-steps"></a>Próximas etapas

Consulte os artigos a seguir sobre como proteger o acesso externo aos recursos. Recomendamos que você execute as ações na ordem listada.

1. [Determinar sua postura de segurança para acesso externo](1-secure-access-posture.md)

2. [Descobrir seu estado atual](2-secure-access-current-state.md)

3. [Criar um plano de governança](3-secure-access-plan.md)

4. [Usar grupos de segurança](4-secure-access-groups.md)

5. [Transição para B2B do Azure AD](5-secure-access-b2b.md)

6. [Acesso seguro com gerenciamento de direitos](6-secure-access-entitlement-managment.md) (você está aqui).

7. [Acesso seguro com políticas de acesso condicional](7-secure-access-conditional-access.md)

8. [Acesso seguro com rótulos de sensibilidade](8-secure-access-sensitivity-labels.md)

9. [Proteger o acesso ao Microsoft Teams, OneDrive e SharePoint](9-secure-access-teams-sharepoint.md)

 

