---
title: O que são revisões de acesso? - Active Directory do Azure | Microsoft Docs
description: Usando as revisões de Azure Active Directory acesso, você pode controlar a associação de grupo e o acesso ao aplicativo para atender às iniciativas de governança, gerenciamento de riscos e conformidade em sua organização.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: da9bc3906e6f39b2d943708eb6a1b930ac8cc5a5
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68401960"
---
# <a name="what-are-azure-ad-access-reviews"></a>O que são as revisões de acesso do Azure AD?

As revisões de acesso do Azure Active Directory (Azure AD) permitem que as organizações gerenciem com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso do usuário pode ser examinado regularmente para garantir que somente as pessoas corretas tenham acesso contínuo.

Aqui está um vídeo que fornece uma visão geral das revisões de acesso:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Por que as revisão de acesso são importantes?

O Microsoft Azure Active Directory permite que você colabore internamente em sua organização e com os usuários de organizações externas, como parceiros. Os usuários podem ingressar em grupos, convidar pessoas, conectar-se aos aplicativos de nuvem e trabalhar remotamente com seus dispositivos pessoais ou de trabalho. A conveniência de aproveitar o poder do autoatendimento levou a uma necessidade de melhores recursos de gerenciamento de acesso.

- Conforme novos funcionários ingressam, como garantir que eles tenham o acesso correto para serem produtivos?
- Quando as pessoas mudam de equipes ou saem da empresa, como garantir que seu antigo acesso seja removido, especialmente quando envolve convidados?
- Direitos de acesso excessivos podem levar a conclusões e comprometimentos de auditoria pois indicam uma falta de controle de acesso.
- Você tem que se envolver proativamente com os proprietários do recurso para garantir que eles revisam regularmente quem tem acesso a seus recursos.

## <a name="when-to-use-access-reviews"></a>Quando usar as revisões de acesso?

- **Muitos usuários em funções com privilégios:** É uma boa ideia verificar quantos usuários têm acesso administrativo, quantos deles são administradores globais e se há algum convidado ou parceiro que não tenha sido removido depois de ser atribuído a uma tarefa de administração. Você pode recertificar os usuários de atribuição de função nas [funções do Azure ad](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) , como administradores globais, ou [funções de recursos do Azure](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) , como administrador de acesso do usuário na experiência do [Azure ad Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) .
- **Quando a automação é inviável:** Você pode criar regras para associação dinâmica em grupos de segurança ou grupos do Office 365, mas e se os dados de RH não estiverem no Azure AD, ou se os usuários ainda precisarem de acesso após deixar o grupo para treinar sua substituição? Em seguida, você pode criar uma revisão nesse grupo para garantir que aqueles que ainda precisam de acesso devem ter acesso contínuo.
- **Quando um grupo é usado para uma nova finalidade:** Se você tiver um grupo que será sincronizado com o Azure AD, ou se planeja habilitar o aplicativo Salesforce para todos no grupo de equipe de Vendas, será útil solicitar ao proprietário do grupo para revisar a associação ao grupo antes que o grupo seja utilizado em um conteúdo de risco diferente.
- **Acesso de dados críticos de negócios:** para certos recursos, talvez seja necessário pedir que as pessoas fora do departamento de TI regularmente, saiam e forneçam uma justificativa sobre por que eles precisam de acesso para fins de auditoria.
- **Para manter a lista de exceções de uma política:** Em um mundo ideal, todos os usuários seguirão as políticas de acesso para proteger o acesso aos recursos da sua organização. No entanto, às vezes, há casos comerciais em que é necessário fazer exceções. Como o administrador de TI, você pode gerenciar essa tarefa, evitar supervisão de exceções à política e fornecer os auditores a comprovação de que essas exceções são revisadas regularmente.
- **Solicite aos proprietários do grupo para confirmar se ainda precisam de convidados em seus grupos:** O acesso do funcionário pode ser automatizado com algum IAM local, mas não convidados. Se um grupo oferece acesso de convidados para conteúdo confidencial da empresa, em seguida, é responsabilidade do proprietário do grupo se confirmar os convidados ainda terão uma necessidade comercial legítima de acesso.
- **Realizar revisões periodicamente:** É possível configurar revisões de acesso recorrentes de usuários em frequências definidas como semanal, mensal, trimestral ou anual e os revisores serão notificados no início de cada revisão. Os revisores podem aprovar ou negar acesso com uma interface amigável e com a ajuda de recomendações inteligentes.

## <a name="where-do-you-create-reviews"></a>Onde você cria as revisões?

Dependendo do que você deseja examinar, você criará sua análise de acesso nas revisões de acesso do Azure AD, nos aplicativos do Azure AD Enterprise (em versão prévia) ou no Azure AD PIM.

| Direitos de acesso de usuários | Os revisores podem ser | Revisão criada em | Experiência do revisor |
| --- | --- | --- | --- |
| Membros do grupo de segurança</br>Membros do grupo do escritório | Revisores especificados</br>Proprietários do grupo</br>Análise automática | Revisões de acesso do Azure AD</br>Grupos do Azure AD | Painel de acesso |
| Atribuído a um aplicativo conectado | Revisores especificados</br>Análise automática | Revisões de acesso do Azure AD</br>Aplicativos corporativos do Azure Active Directory (visualização) | Painel de acesso |
| Função do Azure AD | Revisores especificados</br>Análise automática | [Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Portal do Azure |
| Função de recurso do Azure | Revisores especificados</br>Análise automática | [Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Portal do Azure |

## <a name="which-users-must-have-licenses"></a>Quais usuários precisam ter licenças?

Cada usuário que interage com as revisões de acesso deve ter uma licença paga de Azure AD Premium P2. Os exemplos incluem:

- Administradores que criam uma revisão de acesso
- Proprietários de grupo que executam uma revisão de acesso
- Usuários atribuídos como revisores
- Usuários que executam uma autoanálise

Você também pode solicitar que usuários convidados revisem o próprio acesso. Para cada licença paga do Azure AD Premium P2 que você atribui a um dos usuários da sua organização, você pode usar o B2B (Business-to-Business) do Azure AD para convidar até cinco usuários convidados sob a concessão do usuário externo. Esses usuários convidados também poderão usar os recursos do Azure AD Premium P2. Para obter mais informações, consulte Diretrizes de licenciamento da [colaboração B2B do Azure ad](../b2b/licensing-guidance.md).

Aqui estão alguns cenários de exemplo para ajudá-lo a determinar o número de licenças que você deve ter.

| Cenário | Cálculo | Número necessário de licenças |
| --- | --- | --- |
| Um administrador cria uma revisão de acesso do grupo A com 500 usuários.<br/>Atribui 3 proprietários de grupo como revisores. | 1 administrador + 3 proprietários do grupo | 4 |
| Um administrador cria uma revisão de acesso do grupo A com 500 usuários.<br/>O torna uma análise automática. | 1 administrador + 500 usuários como autovisores | 501 |
| Um administrador cria uma revisão de acesso do grupo A com 5 usuários e 25 usuários convidados.<br/>O torna uma análise automática. | 1 administrador + 5 usuários como autovisores<br/>(os usuários convidados são abordados na proporção 1:5 necessária) | 6 |
| Um administrador cria uma revisão de acesso do grupo A com 5 usuários e 28 usuários convidados.<br/>O torna uma análise automática. | 1 administrador + 5 usuários como auto-revisores + 1 usuário para cobrir os usuários convidados na proporção de 1:5 necessária | 7 |

Para obter informações sobre como atribuir licenças a seus usos, confira [Atribuir ou remover licenças usando o portal do Azure Active Directory](../fundamentals/license-users-groups.md).

## <a name="onboard-access-reviews"></a>Revisões de acesso integrado

Para carregar as revisões de acesso, siga estas etapas.

1. Como administrador global ou administrador de usuários, entre no [portal do Azure](https://portal.azure.com) em que você deseja usar as revisões de acesso.

1. Na navegação à esquerda, clique em **Active Directory do Azure**.

1. No menu à esquerda, clique em **governança de identidade**.

1. Clique em **revisões de acesso**.
 
    ![Página inicial de revisões de acesso](./media/access-reviews-overview/access-reviews-overview-onboard.png)

1. Na página, clique no botão **integrado agora** .
    
      ![Revisões de acesso integradas](./media/access-reviews-overview/access-reviews-overview-select-onboard.png)


## <a name="learn-about-access-reviews"></a>Saiba mais sobre as revisões de acesso

Para saber mais sobre como criar e executar as revisões de acesso, assista a este breve demonstração:

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

Se você estiver pronto para implantar as revisões de acesso em sua organização, siga estas etapas no vídeo para integrar, treinar seus administradores e criar sua primeira revisão de acesso!

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="next-steps"></a>Próximas etapas

- [Criar uma revisão de acesso de grupos ou aplicativos](create-access-review.md)
- [Criar uma revisão de acesso de usuários em uma função administrativa do Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Examinar o acesso a grupos ou aplicativos](perform-access-review.md)
- [Concluir uma revisão de acesso de grupos ou aplicativos](complete-access-review.md)
