---
title: O que são revisões de acesso? - Active Directory do Azure | Microsoft Docs
description: Usando as revisões de acesso do Azure Active Directory, você pode controlar a associação a grupos e o acesso a aplicativos para atender às iniciativas de governança, gerenciamento de riscos e conformidade da sua organização.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.subservice: compliance
ms.date: 10/29/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.custom: contperf-fy21q1
ms.openlocfilehash: 22bfef17f68b2e83e4f7462d8e9af6fb8aacc284
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98246733"
---
# <a name="what-are-azure-ad-access-reviews"></a>Quais são as revisões de acesso do Azure AD?

As Revisões de Acesso do Microsoft Azure Active Directory (Azure AD) permitem que as organizações gerenciem com eficiência as associações a grupos, o acesso a aplicativos empresariais e atribuições de função. O acesso do usuário pode ser examinado regularmente para garantir que somente as pessoas corretas tenham acesso contínuo.

Aqui está um vídeo que fornece uma visão geral das revisões de acesso:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Por que as revisão de acesso são importantes?

O Azure AD permite que você colabore com usuários de dentro de sua organização e com usuários externos. Os usuários podem ingressar em grupos, convidar pessoas, conectar-se aos aplicativos de nuvem e trabalhar remotamente com seus dispositivos pessoais ou de trabalho. A conveniência de usar o autoatendimento levou a uma necessidade de melhores recursos de gerenciamento de acesso.

- Conforme novos funcionários ingressam, como garantir que eles tenham o acesso de que precisam para serem produtivos?
- Quando as pessoas mudam de equipes ou saem da empresa, como garantir que seu antigo acesso seja removido?
- Direitos de acesso excessivos podem levar a comprometimentos.
- Eles também podem levar a descobertas em auditorias, pois indicam uma falta de controle sobre o acesso.
- Você tem que se envolver proativamente com os proprietários do recurso para garantir que eles revisam regularmente quem tem acesso a seus recursos.

## <a name="when-should-you-use-access-reviews"></a>Quando usar revisões de acesso?

- **Muitos usuários em funções com privilégios:** é recomendável verificar quantos usuários têm acesso administrativo, quantos deles são Administradores Globais e se há convidados ou parceiros que não foram removidos após serem atribuídos para executar uma tarefa administrativa. Você pode renovar os usuários de atribuição de função nas [funções do Azure AD](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json), como administradores globais ou [funções de recursos do Azure](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json), como o administrador de acesso do usuário na experiência do [Azure AD PIM (Privileged Identity Management)](../privileged-identity-management/pim-configure.md).
- **Quando a automação não é possível:** Você pode criar regras para associação dinâmica em grupos de segurança ou grupos do Microsoft 365, mas e se os dados de RH não estiverem no Azure AD ou se os usuários ainda precisarem de acesso após deixar o grupo para treinar a substituição deles? Em seguida, você pode criar uma revisão nesse grupo para garantir que aqueles que ainda precisam de acesso devem ter acesso contínuo.
- **Quando um grupo é usado para uma nova finalidade:** Se você tiver um grupo que será sincronizado com o Azure AD, ou se planeja habilitar o aplicativo Salesforce para todos no grupo de equipe de Vendas, será útil solicitar ao proprietário do grupo para revisar a associação ao grupo antes que o grupo seja utilizado em um conteúdo de risco diferente.
- **Acesso a dados comercialmente críticos:** para certos recursos, talvez seja necessário pedir que as pessoas fora do departamento de TI desconectem-se regularmente e forneçam uma justificativa sobre por que eles precisam de acesso para fins de auditoria.
- **Para manter a lista de exceções de uma política:** em um mundo ideal, todos os usuários seguiriam as políticas de acesso para proteger o acesso aos recursos de sua organização. No entanto, às vezes, há casos comerciais em que é necessário fazer exceções. Como o administrador de TI, você pode gerenciar essa tarefa, evitar supervisão de exceções à política e fornecer os auditores a comprovação de que essas exceções são revisadas regularmente.
- **Solicite aos proprietários do grupo para confirmar se ainda precisam de convidados em seus grupos:** o acesso dos funcionários pode ser automatizado com algum IAM (Gerenciamento de Identidades e Acesso) local, mas não como convidados. Se um grupo oferece acesso de convidados para conteúdo confidencial da empresa, em seguida, é responsabilidade do proprietário do grupo se confirmar os convidados ainda terão uma necessidade comercial legítima de acesso.
- **Realizar revisões periodicamente:** É possível configurar revisões de acesso recorrentes de usuários em frequências definidas como semanal, mensal, trimestral ou anual e os revisores serão notificados no início de cada revisão. Os revisores podem aprovar ou negar acesso com uma interface amigável e com a ajuda de recomendações inteligentes.

>[!NOTE]
>Se estiver pronto para experimentar as revisões de acesso, dê uma olhada em [Criar uma revisão de acesso de grupos ou aplicativos](create-access-review.md)

## <a name="where-do-you-create-reviews"></a>Onde você cria as revisões?

Dependendo do que você deseja revisar, você criará sua revisão de acesso nas revisões de acesso do Azure AD, nos aplicativos empresariais do Azure AD (em versão prévia) ou no Azure AD PIM.

| Direitos de acesso de usuários | Os revisores podem ser | Revisão criada em | Experiência do revisor |
| --- | --- | --- | --- |
| Membros do grupo de segurança</br>Membros do grupo do escritório | Revisores especificados</br>Proprietários do grupo</br>Autorrevisão | Revisões de acesso do Azure AD</br>Grupos do Azure AD | Painel de acesso |
| Atribuído a um aplicativo conectado | Revisores especificados</br>Autorrevisão | Revisões de acesso do Azure AD</br>Aplicativos corporativos do Azure Active Directory (visualização) | Painel de acesso |
| Função do Azure AD | Revisores especificados</br>Autorrevisão | [Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Portal do Azure |
| Função de recurso do Azure | Revisores especificados</br>Autorrevisão | [Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Portal do Azure |

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="how-many-licenses-must-you-have"></a>Quantas licenças você precisa ter?

O diretório precisa ter, pelo menos, um número de licenças do Azure AD Premium P2 igual ou superior ao número de funcionários que executarão as seguintes tarefas:

-   Usuários membros que são atribuídos como revisores
-   Usuários membros que executam uma autorrevisão
-   Usuários membros como proprietários de grupo que executam uma revisão de acesso
-   Usuários membros como proprietários de aplicativo que executam uma revisão de acesso

Para os usuários convidados, as necessidades de licenciamento dependerão do modelo de licenciamento utilizado. No entanto, as atividades dos usuários convidados abaixo são consideradas como uso do Azure AD Premium P2:

-   Usuários convidados que são atribuídos como revisores
-   Usuários convidados que executam uma autorrevisão
-   Usuários convidados como proprietários de grupo que executam uma revisão de acesso
-   Usuários convidados como proprietários de aplicativo que executam uma revisão de acesso


As licenças do Azure AD Premium P2 **não** são necessárias para usuários com funções de Administrador Global ou de Administrador de Usuários que configuram revisões de acesso, definem configurações ou aplicam as decisões das revisões.

O acesso do usuário convidado do Azure AD é baseado em um modelo de cobrança de MAU (usuários ativos mensais), que substitui o modelo de cobrança de índice 1:5. Para obter mais informações, confira [Preços das Identidades Externas do Azure AD](../external-identities/external-identities-pricing.md).

Para obter mais informações sobre licenças, confira [Atribuir ou remover licenças usando o portal do Azure Active Directory](../fundamentals/license-users-groups.md).

### <a name="example-license-scenarios"></a>Cenários de licença de exemplo

Aqui estão alguns exemplos de cenários de licença para ajudá-lo a determinar o número de licenças que você precisa ter.

| Cenário | Cálculo | Número de licenças |
| --- | --- | --- |
| Um administrador cria uma revisão de acesso do grupo A com 75 usuários e um proprietário do grupo e atribui o proprietário do grupo como o revisor. | Uma licença para o proprietário do grupo como revisor | 1 |
| Um administrador cria uma revisão de acesso do grupo A com 500 usuários e três proprietários do grupo e atribui os três como revisores. | Três licenças para cada proprietário do grupo como revisores | 3 |
| Um administrador cria uma revisão de acesso do grupo B com 500 usuários. Ele a torna uma autorrevisão. | 500 licenças para cada usuário como autorrevisores | 500 |
| Um administrador cria uma revisão de acesso do grupo C com 50 usuários membros e 25 usuários convidados. Ele a torna uma autorrevisão. | 50 licenças para cada usuário como autorrevisores.* | 50 |
| Um administrador cria uma revisão de acesso do grupo D com seis usuários membros e 108 usuários convidados. Ele a torna uma autorrevisão. | 6 licenças para cada usuário como autorrevisores. Usuários convidados são cobrados com base em um MAU (usuário ativo mensal). Não há licenças adicionais necessárias. *  | 6 |

\* O preço das Identidades Externas (usuários convidados) do Azure AD baseiam-se em MAUs (usuários ativos mensais), que é a contagem de usuários exclusivos que realizam atividades de autenticação em um mês civil. Esse modelo substitui o modelo de cobrança com proporção de 1:5, que permitia até cinco usuários convidados para cada licença do Azure AD Premium no locatário. Quando o locatário estiver vinculado a uma assinatura e você usar recursos de Identidades Externas para colaborar com usuários convidados, você será cobrado automaticamente de acordo com o modelo de cobrança baseado em MAU. Para obter mais informações, confira Modelo de cobrança para Identidades Externas do Azure AD.

## <a name="next-steps"></a>Próximas etapas

- [Criar uma revisão de acesso de grupos ou aplicativos](create-access-review.md)
- [Criar uma revisão de acesso de usuários em uma função administrativa do Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Examinar o acesso a grupos ou aplicativos](perform-access-review.md)
- [Concluir uma revisão de acesso de grupos ou aplicativos](complete-access-review.md)
