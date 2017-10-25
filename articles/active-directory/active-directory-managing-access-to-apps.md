---
title: Gerenciando o acesso a aplicativos que usam o Azure AD | Microsoft Docs
description: "Descreve como o Active Directory do Azure permite que as organizações especifiquem os aplicativos aos quais cada usuário tem acesso."
services: active-directory
documentationcenter: 
author: femila
manager: femila
editor: 
ms.assetid: b0829f18-9e57-4107-925d-5f0457d81671
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: markvi
ms.openlocfilehash: 46e001b440802e0d5d16b7cf75344c7b9ce6fad3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="managing-access-to-apps"></a>Gerenciando o acesso a aplicativos
Gerenciamento de acesso contínuo, avaliação de uso e relatórios continuam a ser um desafio depois que um aplicativo é integrado ao sistema de identidade da sua organização. Em muitos casos, os administradores de TI ou a assistência técnica deve ter uma função ativa contínua no gerenciamento de acesso aos seus aplicativos. Às vezes, a atribuição é executada por uma equipe de TI geral ou de divisão. Frequentemente, a decisão de atribuição deve ser delegada ao tomador de decisões comerciais, exigindo sua aprovação antes de a TI fazer a atribuição.  Outras organizações investem na integração com um sistema de gerenciamento automatizado de identidade e acesso existente, como RBAC (controle de acesso baseado em função) ou ABAC (controle de acesso baseado em atributos). A integração e o desenvolvimento de regras tendem a ser especializados e caros. Em cada abordagem de gerenciamento, monitoramento e relatórios exigem um investimento separado, caro e complexo.

## <a name="how-does-azure-active-directory-help"></a>Como o Active Directory do Azure ajuda?
 O AD do Azure oferece suporte a gerenciamento de acesso abrangente para aplicativos configurados, permitindo que as organizações obtenham facilmente as políticas de acesso corretas desde a atribuição automática, com base em atributo (cenários de ABAC ou RBAC) por meio da delegação e incluindo gerenciamento de administradores. Com o AD do Azure, você pode facilmente obter políticas complexas, combinando vários modelos de gerenciamento de um único aplicativo e pode até reutilizar regras de gerenciamento entre aplicativos com o mesmo público.

* [Adicionando aplicativos novos ou existentes](active-directory-sso-integrate-saas-apps.md)

 A atribuição de aplicativo do AD do Azure se concentra em dois modos de atribuição principais:

* **Atribuição individual** Um administrador de TI com permissões de Administrador Global de diretório pode selecionar as contas de usuário individuais e conceder a elas acesso ao aplicativo.
* **Atribuição baseada em grupo (somente no AD do Azure pago)** Um administrador de TI com permissões de Administrador Global de diretório pode atribuir um grupo ao aplicativo. O acesso de usuários específicos é determinado dependendo se eles são membros do grupo no momento em que tenta acessar o aplicativo. Em outras palavras, um administrador pode efetivamente criar uma regra de atribuição informando que "qualquer membro atual do grupo atribuído tem acesso ao aplicativo". Usando essa opção de atribuição, os administradores podem se beneficiar de qualquer uma das opções de gerenciamento de grupo do Azure AD, incluindo [grupos dinâmicos baseados em atributo](active-directory-accessmanagement-manage-groups.md), grupos de sistema externo (por exemplo, Active Directory local ou Workday) ou grupos gerenciados de autoatendimento ou gerenciados pelo administrador. Um único grupo pode ser atribuído facilmente a vários aplicativos, garantindo que os aplicativos com afinidade de atribuição possam compartilhar as regras de atribuição, reduzindo a complexidade do gerenciamento geral. Por favor, note que associações de grupo aninhadas não têm suporte para atribuição com base em grupo de aplicativos atualmente.

Usando esses dois modos de atribuição, os administradores podem obter qualquer abordagem de gerenciamento de atribuição desejada.

Com o AD do Azure, relatórios de atribuição e uso são totalmente integrados, permitindo que os administradores facilmente criem relatórios sobre estado da atribuição, erros de atribuição e até mesmo uso.

## <a name="complex-application-assignment-with-azure-ad"></a>Atribuição de aplicativo complexo com o AD do Azure
Considere um aplicativo como o Salesforce. Em muitas organizações, o Salesforce é usado principalmente por departamentos de marketing e vendas. Geralmente, membros da equipe de marketing têm acesso altamente privilegiado ao Salesforce, enquanto os membros da equipe de vendas têm acesso limitado. Em muitos casos, uma grande população de operadores de informações tem acesso restrito ao aplicativo. Exceções a essas regras complicam as coisas. Geralmente é privilégio das equipes de liderança de marketing ou vendas conceder acesso a um usuário ou alterar suas funções independentemente dessas regras genéricas.

Com o AD do Azure, aplicativos como o Salesforce podem ser pré-configurados para logon único (SSO) e provisionamento automatizado. Quando o aplicativo é configurado, um administrador pode realizar a ação única de criar e atribuir os grupos apropriados. Neste exemplo, um administrador pode executar as atribuições a seguir:

* [grupos dinâmicos](active-directory-accessmanagement-manage-groups.md) podem ser definidos para representar automaticamente todos os membros das equipes de marketing e vendas usando atributos como o departamento ou a função:
  
  * Todos os membros de grupos de marketing seriam atribuídos à função de "marketing" no Salesforce
  * Todos os membros dos grupos da equipe de vendas seriam atribuídos à função de "vendas" no Salesforce. Um ajuste adicional poderia usar vários grupos que representam equipes de vendas regionais que são atribuídos a funções diferentes no Salesforce.
* Para habilitar o mecanismo de exceção, um grupo de autoatendimento poderia ser criado para cada função. Por exemplo, o grupo "exceção de marketing do Salesforce" pode ser criado como um grupo de autoatendimento. O grupo pode ser atribuído à função de marketing do Salesforce e a equipe de liderança de marketing pode ser definida como proprietária. Membros da equipe de liderança de marketing podem adicionar ou remover usuários, definir uma política de associação ou até mesmo aprovar ou negar as solicitações de usuários individuais para ingressar. Isso é suportado por meio de uma experiência adequada ao operador de informações que não exige treinamento especializado para os proprietários ou membros.

Nesse caso, todos os usuários atribuídos poderiam ser provisionados automaticamente no Salesforce. Como são adicionados a grupos diferentes, sua atribuição de função seria atualizada no Salesforce. Os usuários poderiam descobrir e acessar o Salesforce por meio do painel de acesso de aplicativos Microsoft, clientes do Office na web ou até mesmo navegando para sua página de logon organizacional do Salesforce. Os administradores poderiam exibir facilmente o status de atribuição e uso usando os relatórios do AD do Azure.

Os administradores podem empregar o [acesso condicional do AD do Azure](active-directory-conditional-access.md) para definir políticas de acesso para funções específicas. Essas políticas podem incluir se o acesso é permitido fora do ambiente corporativo e até mesmo requisitos de autenticação multifator ou dispositivo para obter acesso em vários casos.

## <a name="how-can-i-get-started"></a>Como posso começar?
Primeiro, se você ainda não estiver usando o AD do Azure e for um administrador de TI:

* [Experimente!](https://azure.microsoft.com/trial/get-started-active-directory/) - você pode se inscrever em uma avaliação gratuita de 30 dias hoje mesmo e implantar sua primeira solução de nuvem em menos de 5 minutos usando este link

Recursos do AD do Azure que permitem o compartilhamento de contas incluem:

* [Atribuição de grupo](active-directory-accessmanagement-self-service-group-management.md)
* Adicionando aplicativos ao AD do Azure
* Introdução à atribuição
* Perguntas frequentes sobre a atribuição de aplicativo
* [Painel/relatórios de uso de aplicativo](active-directory-passwords-get-insights.md)

## <a name="where-can-i-learn-more"></a>Onde posso saber mais?
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)
* [Proteger aplicativos com acesso condicional](active-directory-conditional-access.md)
* [Gerenciamento de grupo de autoatendimento/SSAA](active-directory-accessmanagement-self-service-group-management.md)

