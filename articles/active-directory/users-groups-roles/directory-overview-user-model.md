---
title: Visão geral de usuários, grupos, licenciamento e funções – Azure Active Directory | Microsoft Docs
description: A relação entre usuários e licenças atribuídas, funções de administrador, associação de grupo no Azure Active Directory
keywords: ''
author: curtand
manager: mtillman
ms.author: curtand
ms.reviewer: vincesm
ms.date: 01/28/2019
ms.topic: overview
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
services: active-directory
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2053ad07e555cc04cea714524141068f1404796
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083889"
---
# <a name="users-groups-licensing-and-roles-for-large-organizations"></a>Usuários, grupos, licenciamento e funções para organizações de grandes porte

Este artigo apresenta ao administrador do Azure AD a relação entre as principais tarefas de [gerenciamento de identidade](/azure/active-directory/fundamentals/identity-fundamentals?context=azure/active-directory/users-groups-roles/context/ugr-context) para os usuários em termos de seus grupos, licenças, aplicativos empresariais implantados e as funções de administrador. À medida que sua organização cresce, você pode usar grupos do Azure AD e as funções de administrador para:

* Atribuir licenças a grupos, em vez de individualmente
* Delegar permissões para distribuir o trabalho de gerenciamento do Azure AD às funções com menos privilégios
* Atribuir acesso de aplicativo empresarial a grupos

## <a name="assign-users-to-groups"></a>Atribuir usuários a grupos

Você pode usar grupos no Azure AD para atribuir licenças a um grande número de usuários ou atribuir acesso de usuário a aplicativos empresariais implantados. Você pode usar grupos para atribuir funções de administrador no Azure AD, ou pode permitir acesso a recursos externos, como aplicativos de SaaS ou sites do SharePoint.

Para proporcionar mais flexibilidade e reduzir o trabalho de gerenciar a associação de grupo, você pode usar [grupos dinâmicos](groups-create-rule.md) no Azure AD para expandir e contrair a associação de grupo automaticamente. Você precisará de uma licença do Azure AD Premium P1 para cada usuário único que é um membro de um ou mais grupos dinâmicos.

## <a name="assign-licenses-to-groups"></a>Atribuir licenças a grupos

Atribuir ou remover licenças de usuários individualmente pode exigir tempo e atenção. Se você [atribuir licenças a grupos](/azure/active-directory/fundamentals/license-users-groups?context=azure/active-directory/users-groups-roles/context/ugr-context) em vez disso, poderá facilitar o gerenciamento de licença em grande escala.

No Azure AD, quando os usuários ingressam em um grupo licenciado, são atribuídas a eles as licenças apropriadas automaticamente. Quando os usuários deixam o grupo, o Azure AD remove suas atribuições de licença. Sem grupos do Azure AD, você precisaria escrever um script do PowerShell ou usar a API do Graph para adicionar ou remover em massa licenças de usuário para usuários que estejam ingressando ou saindo da organização.

Se não houver licenças suficientes disponíveis ou se ocorrer um problema, como planos de serviço que não podem ser atribuídos ao mesmo tempo, você poderá ver o status de qualquer problema de licenciamento para o grupo no portal do Azure.

>[!NOTE]
>O recurso de licenciamento baseado em grupo está em versão prévia pública no momento. Durante a versão prévia, o recurso está disponível com qualquer plano de licença pago do Azure AD (Azure Active Directory).

## <a name="delegate-administrator-roles"></a>Delegar funções de administrador

Muitas organizações de grande porte desejam ter opções para seus usuários obterem permissões suficientes para suas tarefas de trabalho sem atribuir a poderosa função de Administrador Global a, por exemplo, usuários que devem registrar aplicativos. Aqui está um exemplo das novas funções de administrador do Azure AD para ajudá-lo a distribuir o trabalho de gerenciamento de aplicativos com maior granularidade:

 Nome da função | Resumo de permissões
 --------- | -------------------
 **Administrador de Aplicativos** | Pode adicionar e gerenciar aplicativos empresariais e registros de aplicativo e definir configurações de proxy do aplicativo. Administradores de aplicativo podem exibir as políticas de acesso condicional e os dispositivos, mas não gerenciá-los.
 **Administrador de Aplicativos de Nuvem** | Pode adicionar e gerenciar aplicativos empresariais e Registros de aplicativo empresarial. Essa função tem todas as permissões do administrador do aplicativo, exceto que não pode gerenciar as configurações de proxy de aplicativo.
**Desenvolvedor de Aplicativo** | Pode adicionar e atualizar registros de aplicativo, mas não pode gerenciar aplicativos empresariais nem configurar um proxy de aplicativo.

Novas funções de administrador do Azure AD estão sendo adicionadas. Verifique o portal do Azure ou a [referência de permissão de função de administrador](directory-assign-admin-roles.md) para as funções disponíveis atuais.

## <a name="assign-app-access"></a>Atribuir acesso de aplicativo

Você pode usar o Azure AD para atribuir acesso de grupo para [aplicativos empresariais implantados em seu locatário do Azure AD](/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups?context=azure/active-directory/users-groups-roles/context/ugr-context). Se você combinar grupos dinâmicos com a atribuição de grupo a aplicativos, poderá automatizar as atribuições de acesso de aplicativo do usuário conforme sua organização crescer. Você precisará de uma licença do Azure Active Directory Premium P1 ou P2 para atribuir acesso a aplicativos empresariais.

O Azure AD também fornece controle granular dos dados que fluem entre o aplicativo e os grupos aos quais você atribui acesso. Em [Aplicativos Empresariais](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps), abra um aplicativo e selecione **Provisionamento** para:

* Configurar provisionamento automático para aplicativos que dão suporte a ele
* Fornecer as credenciais para conectar-se à API de gerenciamento de usuário do aplicativo
* Configurar os mapeamentos que controlam os atributos de usuário de fluxo entre o Azure AD e o aplicativo quando as contas de usuário são provisionadas ou atualizadas
* Iniciar e parar o serviço de provisionamento do Azure AD para um aplicativo, limpar o cache de provisionamento ou reiniciar o serviço
* Exibir o **Relatório de atividade de provisionamento**, que fornece um log de todos os usuários e grupos criados, atualizados e removidos entre o Azure AD e o aplicativo, e o **Relatório de erros de provisionamento**, que fornece mensagens de erro mais detalhadas

## <a name="next-steps"></a>Próximas etapas

Se você for um administrador do Azure AD, obtenha as noções básicas nos [Fundamentos do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/index).

Ou você pode começar a [criar grupos](/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context), [atribuir licenças](/azure/active-directory/fundamentals/license-users-groups?context=azure/active-directory/users-groups-roles/context/ugr-context), [atribuir acesso do aplicativo](/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups?context=azure/active-directory/users-groups-roles/context/ugr-context) ou [atribuir funções de administrador](directory-assign-admin-roles.md).
