---
title: Use Azure AD Identity Governance para revisar e remover usuários externos que não têm mais acesso a recursos
description: Use as revisões de acesso para estender o acesso de remoção de membros de organizações parceiras
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/06/2020
ms.author: ajburnle
ms.openlocfilehash: fe68ec498d17ec20778c8f34fc6ffa1f0964c44e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176953"
---
# <a name="use-azure-active-directory-azure-ad-identity-governance-to-review-and-remove-external-users-who-no-longer-have-resource-access"></a>Use o governança de identidade Azure Active Directory (AD do Azure) para revisar e remover usuários externos que não têm mais acesso a recursos

Este artigo descreve os recursos e métodos que permitem identificar e selecionar identidades externas para que você possa examiná-las e removê-las do Azure AD se elas não forem mais necessárias. A nuvem facilita ainda mais a colaboração com usuários internos ou externos. Adotando o Office 365, as organizações começam a ver a proliferação de identidades externas (incluindo convidados), pois os usuários trabalham juntos em dados, documentos ou espaços de trabalho digitais, como equipes. As organizações precisam balancear, permitindo a colaboração e atendendo aos requisitos de segurança e governança. Parte desses esforços deve incluir a avaliação e a limpeza de usuários externos, que foram convidados para colaboração em seu locatário, que se originam de organizações parceiras e a remoção deles de seu Azure AD quando eles não são mais necessários.

>[!NOTE]
>Para usar as revisões de acesso do Azure AD, é necessário ter uma licença válida do Azure AD Premium P2, uma licença paga do Enterprise Mobility + Security E5 ou uma licença de avaliação. Para obter mais informações, consulte [Edições do Active Directory do Azure](../fundamentals/active-directory-whatis.md).

## <a name="why-review-users-from-external-organizations-in-your-tenant"></a>Por que revisar os usuários de organizações externas em seu locatário?

Na maioria das organizações, os usuários finais iniciam o processo de convidar parceiros de negócios e fornecedores para colaboração. A necessidade de colaborar orienta as organizações a fornecer aos proprietários de recursos e aos usuários finais uma maneira de avaliar e atestar usuários externos regularmente. Geralmente, o processo de integração de novos parceiros de colaboração é planejado e contabilizado, mas com muitas colaborações sem uma data de término clara, nem sempre é óbvio quando um usuário não precisa mais de acesso. Além disso, o gerenciamento do ciclo de vida da identidade conduz as empresas para manter o Azure AD limpo e remover usuários que não precisam mais de acesso aos recursos da organização. Manter apenas as referências de identidade relevantes para parceiros e fornecedores no diretório ajuda a reduzir o risco de seus funcionários, selecionando e concedendo inadvertidamente acesso a usuários externos que deveriam ter sido removidos. Este documento explica várias opções que variam de sugestões proativas recomendadas para atividades reativas e limpas para controlar identidades externas.

## <a name="use-entitlement-management-to-grant-and-revoke-access"></a>Use o gerenciamento de direitos para conceder e revogar o acesso

Os recursos de gerenciamento de direitos permitem o [ciclo de vida automatizado de identidades externas](entitlement-management-external-users.md#manage-the-lifecycle-of-external-users) com acesso a recursos. Ao estabelecer processos e procedimentos para gerenciar o acesso por meio do gerenciamento de direitos e de publicar recursos por meio de pacotes do Access, manter o controle do acesso de usuários externos aos recursos torna-se um problema muito menos complicado de resolver. Ao gerenciar o acesso por meio de [pacotes de acesso de gerenciamento de direitos](entitlement-management-overview.md) no Azure AD, sua organização pode definir e gerenciar centralmente o acesso para seus usuários, bem como usuários de organizações parceiras. O gerenciamento de direitos usa aprovações e atribuições de pacotes de acesso para controlar onde os usuários externos solicitaram e receberam acesso. Se um usuário externo perder todas as suas atribuições, o gerenciamento de direitos poderá remover esses usuários externos automaticamente do locatário. 

## <a name="find-guests-not-invited-through-entitlement-management"></a>Encontre convidados não convidados por meio do gerenciamento de direitos

Quando os funcionários estão autorizados a colaborar com usuários externos, eles podem convidar qualquer número de usuários de fora da sua organização. Procurar e agrupar parceiros externos em grupos dinâmicos alinhados à empresa e analisá-los pode não ser viável, pois pode haver muitas empresas individuais diferentes para revisar ou não há nenhum proprietário ou patrocinador para a organização. A Microsoft fornece um exemplo de script do PowerShell que pode ajudá-lo a analisar o uso de identidades externas em um locatário. O script enumera as identidades externas e as categoriza. O script pode ajudá-lo a identificar e limpar identidades externas que podem não ser mais necessárias. Como parte da saída do script, o exemplo de script dá suporte à criação automatizada de grupos de segurança que contêm os parceiros externos sem grupo identificados – para análise e uso posteriores com as revisões de acesso do Azure AD.
O script está disponível no [GitHub](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse). Depois que o script termina de ser executado, ele gera um arquivo de saída HTML que descreve as identidades externas que:

- Não tem mais nenhuma associação de grupo no locatário
- Ter uma atribuição para uma função privilegiada no locatário
- Ter uma atribuição para um aplicativo no locatário

A saída também inclui os domínios individuais para cada uma dessas identidades externas. 

>[!NOTE]
>O script mencionado acima é um script de exemplo que verifica a associação de grupo, as atribuições de função e as atribuições de aplicativo no Azure AD. Pode haver outras atribuições em aplicativos que usuários externos receberam fora do Azure AD, como o SharePoint (atribuição de associação direta) ou o Azure RBAC ou o Azure DevOps.

## <a name="review-resources-used-by-external-identities"></a>Examinar os recursos usados por identidades externas

Se você tiver identidades externas usando recursos como equipes ou outros aplicativos ainda não governados pelo gerenciamento de direitos, talvez seja melhor analisar o acesso a esses recursos regularmente também. As [revisões de acesso](create-access-review.md) do Azure ad fornecem a capacidade de revisar o acesso de identidades externas, permitindo que o proprietário do recurso, identidades externas em si ou outra pessoa delegada que você confie no atestado se o acesso contínuo for necessário. As revisões de acesso direcionam um recurso e criam uma atividade de revisão com escopo definido para qualquer pessoa que tenha acesso somente ao recurso ou aos usuários convidados. Em seguida, o revisor verá a lista resultante de usuários que eles precisam examinar – todos os usuários, incluindo os funcionários de sua organização ou identidades externas somente.

![usando um grupo para revisar o acesso](media/access-reviews-external-users/group-members.png)

O estabelecimento de uma cultura de revisão controlada pelo proprietário do recurso ajuda a controlar o acesso para identidades externas. Proprietários de recursos, responsáveis pelo acesso, disponibilidade e segurança das informações que eles possuem, são, na maioria dos casos, o melhor público-alvo de impulsionar as decisões sobre o acesso a seus recursos e estão mais próximos dos usuários que os acessam do que a ti central ou um patrocinador que gerencia muitas externas.

## <a name="create-access-reviews-for-external-identities"></a>Criar revisões de acesso para identidades externas

Os usuários que não tiverem mais acesso a nenhum recurso em seu locatário poderão ser removidos se não trabalharem mais com sua organização. Antes de bloquear e excluir essas identidades externas, talvez você queira entrar em contato com esses usuários externos e certificar-se de que não tenha ignorado um projeto ou acesso à sua posição que eles ainda precisam. Quando você cria um grupo que contém todas as identidades externas como membros que você encontrou não tem acesso a nenhum recurso em seu locatário, você pode usar as revisões de acesso para ter todos os external-atestados se eles ainda precisarem ou tiverem acesso – ou ainda precisarão de acesso no futuro. Como parte da revisão, o criador de revisão nas revisões de acesso pode usar a função **exigir motivo na aprovação** para exigir que os usuários externos forneçam uma justificativa para o acesso contínuo, por meio do qual você pode aprender onde e como eles ainda precisam de acesso em seu locatário. Além disso, você pode habilitar a configuração **de conteúdo adicional para o recurso de email do revisor** , para permitir que os usuários saibam que eles perderão o acesso se não responderem e, se ainda precisarem de acesso, será necessária uma justificativa. Se você quiser continuar e permitir que as revisões de acesso **desabilitem e excluam** identidades externas, caso eles não respondam ou forneçam um motivo válido para o acesso contínuo, você poderá usar a opção desabilitar e excluir, conforme descrito na próxima seção.

![limitando o escopo da revisão somente aos usuários convidados](media/access-reviews-external-users/guest-users-only.png)

Quando a revisão for concluída, a página **resultados** mostrará uma visão geral da resposta fornecida por cada identidade externa. Você pode optar por aplicar os resultados automaticamente e permitir que as revisões de acesso as desabilitem e excluam. Como alternativa, você pode examinar as respostas dadas e decidir se deseja remover o acesso de um usuário ou acompanhá-lo e obter informações adicionais antes de tomar uma decisão. Se alguns usuários ainda tiverem acesso aos recursos que você ainda não analisou, você poderá usar a análise como parte de sua descoberta e enriquecer seu próximo ciclo de análise e atestado.

## <a name="disable-and-delete-external-identities-with-azure-ad-access-reviews"></a>Desabilitar e excluir identidades externas com revisões de acesso do Azure AD

Além da opção de remover identidades externas indesejadas de recursos como grupos ou aplicativos, as revisões de acesso do Azure AD podem bloquear identidades externas de entrar em seu locatário e excluir as identidades externas do seu locatário após 30 dias. Depois de selecionar **Bloquear usuário de entrar por 30 dias, em seguida, remover o usuário do locatário**, a revisão permanecerá no estado "aplicando" por 30 dias. Durante esse período, as configurações, os resultados, os revisores ou os logs de auditoria na revisão atual não poderão ser exibidos ou configuráveis. 

![após as configurações de conclusão](media/access-reviews-external-users/upon-completion-settings.png)

Ao criar uma nova revisão de acesso, na seção "configurações de conclusão", para **ação a ser aplicada a usuários negados** , você pode definir **impedir que os usuários façam logon por 30 dias e, em seguida, remover o usuário do locatário**.

Essa configuração permite que você identifique, bloqueie e exclua identidades externas do seu locatário do Azure AD. Identidades externas que são revisadas e com negação de acesso contínuo pelo revisor serão bloqueadas e excluídas, independentemente do acesso a recursos ou da Associação de grupo que têm. Essa configuração é melhor usada como última etapa depois que você valida que os usuários externos em revisão não carregam mais o acesso aos recursos e podem ser removidos com segurança do seu locatário ou se você quiser ter certeza de que eles foram removidos, independentemente de seu acesso à sua posição. O recurso "desabilitar e excluir" bloqueia o usuário externo primeiro, tirando sua capacidade de entrar em seu locatário e acessar os recursos. O acesso aos recursos não é revogado neste estágio e, caso você quisesse recriar a instância do usuário externo, sua capacidade de fazer logon pode ser reconfigurada. Após nenhuma ação adicional, uma identidade externa bloqueada será excluída do diretório após 30 dias, removendo a conta, bem como seu acesso.

## <a name="next-steps"></a>Próximas etapas

- [Revisões de acesso – API do Graph](/graph/api/resources/accessreviews-root?view=graph-rest-beta)
- [Gerenciamento de direitos – API do Graph](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta)
