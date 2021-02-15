---
title: Entenda a delegação de função de administrador - Azure Active Directory | Microsoft Docs
description: Modelos de delegação, exemplos e segurança de função no Azure Active Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77ba93b9017fa75f7247bda639880415eda1d280
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98740305"
---
# <a name="delegate-administration-in-azure-active-directory"></a>Delegar administração no Azure Active Directory

Com o crescimento organizacional vem a complexidade. Uma resposta comum é a reduzir parte da carga de trabalho de gerenciamento de acesso com as funções de administrador do Azure AD (Active Directory). É possível atribuir os menores privilégios possíveis para os usuários acessarem seus aplicativos e realizarem suas tarefas. Mesmo que você não atribua a função de Administrador Global a cada proprietário do aplicativo, está colocando as responsabilidades de gerenciamento de aplicativo nos Administradores Globais existentes. Há muitas razões para mudar uma organização rumo a uma administração mais descentralizada. Este artigo pode ajudá-lo a planejar a delegação em sua organização.

<!--What about reporting? Who has which role and how do I audit?-->

## <a name="centralized-versus-delegated-permissions"></a>Permissões centralizadas versus delegadas

À medida que uma organização cresce, pode ser difícil de controlar quais usuários têm funções administrativas específicas. Se um funcionário tiver direitos de administrador que não deva ter, sua organização poderá estar mais suscetível a violações de segurança. Em geral, o número de administradores aos quais você dá suporte e o nível de granularidade das permissões deles dependem do tamanho e da complexidade da sua implantação.

* Em implantações pequenas ou de prova de conceito, um ou alguns administradores fazem tudo, não há delegação. Nesse caso, crie cada administrador com a função de Administrador Global.
* Em implantações maiores com mais computadores, aplicativos e áreas de trabalho, mais delegação é necessária. Vários administradores podem ter responsabilidades funcionais (funções) mais específicas. Por exemplo, alguns podem ser Administradores de Identidades Privilegiadas e outras podem ser Administradores de Aplicativos. Além disso, um administrador pode gerenciar apenas determinados grupos de objetos, como dispositivos.
* Implantações ainda maiores podem exigir permissões ainda mais granulares, além de possivelmente administradores com funções híbridas ou não convencionais.

No portal do Azure AD, é possível [exibir todos os membros de qualquer função](manage-roles-portal.md), o que pode ajudá-lo a verificar rapidamente a implantação e as permissões delegadas.

Se você estiver interessado em delegar acesso a recursos do Azure em vez de acesso administrativo no Azure AD, consulte [atribuir uma função do Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="delegation-planning"></a>Planejamento de delegação

O trabalho dele é desenvolver um modelo de delegação que atenda às suas necessidades. Desenvolver um modelo de delegação é um processo de design iterativo e sugerimos que você siga estas etapas:

* Definir as funções necessárias
* Delegar a administração de aplicativos
* Conceder a capacidade de registrar aplicativos
* Delegar a propriedade de aplicativos
* Desenvolver um plano de segurança
* Estabelecer contas de emergência
* Proteger as funções de administrador
* Tornar temporária a elevação de privilégios

## <a name="define-roles"></a>Definir funções

Determine as tarefas do Active Directory realizadas por administradores e como são mapeadas para funções. É possível [exibir as descrições detalhada da função ](manage-roles-portal.md) no portal do Azure.

Cada tarefa deve ser avaliada por frequência, importância e dificuldade. Esses critérios são aspectos vitais da definição de tarefa, porque decidem se uma permissão deve ser delegada:

* Tarefas que você executa rotineiramente têm risco limitado, e cuja conclusão é trivial, são excelentes candidatas para delegação.
* Tarefas que você raramente executa, mas têm alto impacto em toda a organização e exigem altos níveis de habilidade devem ser consideradas com muito cuidado antes de delegar. Em vez disso, é possível [elevar temporariamente uma conta para a função requerida](../privileged-identity-management/pim-configure.md) ou reatribuir a tarefa.

## <a name="delegate-app-administration"></a>Delegar a administração de aplicativos

A proliferação de aplicativos dentro de sua organização pode sobrecarregar o modelo de delegação. Se a organização coloca a responsabilidade sobre o gerenciamento do acesso de aplicativos no Administrador Global, é provável que esse modelo aumente sua sobrecarga com o passar do tempo. Caso tenha concedido a função de Administrador Global para algo como configurar aplicativos corporativos a pessoas, agora você pode descarregá-las para as seguintes funções com menos privilégios. Isso ajuda a melhorar sua postura de segurança e reduz a probabilidade de erros inconvenientes. As funções de administrador de aplicativos com mais privilégios são:

* A função **Administrador de Aplicativos**, que concede a capacidade de gerenciar todos os aplicativos no diretório, incluindo registros, configurações de logon único, licenciamento e atribuições de usuários e grupos, configurações do Proxy de Aplicativo e o consentimento. Ela não concede a capacidade de gerenciar o Acesso Condicional.
* A função **Administrador de Aplicativos de Nuvem**, que concede todas as capacidades do Administrador de aplicativos, exceto que não permite acesso às configurações do Proxy de Aplicativo (porque ele não tem nenhuma permissão local).

## <a name="delegate-app-registration"></a>Delegar o registro do aplicativo

Por padrão, todos os usuários podem criar registros do aplicativo. Para conceder seletivamente a capacidade de criar registros de aplicativo:

* Defina **Usuários podem registrar aplicativos** como Não em **Configurações do usuário**
* Atribua o usuário à função de Desenvolvedor de Aplicativo

Para conceder seletivamente a capacidade de dar consentimento para permitir que um aplicativo acesse os dados:

* Defina **Usuários podem consentir que os aplicativos acessem dados da empresa em nome deles** como Não em **Configurações do usuário**
* Atribua o usuário à função de Desenvolvedor de Aplicativo

Quando um Desenvolvedor de Aplicativos cria um novo registro de aplicativo, ele é automaticamente adicionado como o primeiro proprietário.

## <a name="delegate-app-ownership"></a>Delegar a propriedade de aplicativos

Para refinar ainda mais a delegação de acesso do aplicativo, é possível atribuir a propriedade para aplicativos empresariais individuais. Isso complementa o suporte existente para atribuir proprietários de registro de aplicativo. A propriedade é atribuída em uma base de aplicativo por empresa na folha Aplicativos Empresariais. A vantagem é que os proprietários podem gerenciar apenas os aplicativos empresariais que possuem. Por exemplo, é possível atribuir um proprietário para o aplicativo Salesforce e esse proprietário pode gerenciar o acesso e a configuração para o Salesforce e nenhum outro aplicativo. Um aplicativo empresarial pode ter vários proprietários e um usuário pode ser o proprietário de muitos aplicativos empresariais. Há duas funções de proprietário de aplicativo:

* A função **Proprietário de Aplicativo Empresarial** concede a capacidade de gerenciar os aplicativos empresariais que o usuário possui, incluindo configurações de logon único, atribuições de usuários e grupos e adicionar mais proprietários. Ele não concede a capacidade de gerenciar as configurações de proxy de aplicativo ou o acesso condicional.
* A função **Proprietário de Registro de Aplicativo** concede a capacidade de gerenciar registros de aplicativo para o aplicativo que o usuário possui, incluindo o manifesto do aplicativo e adicionar mais proprietários.

## <a name="develop-a-security-plan"></a>Desenvolver um plano de segurança

O Azure AD fornece um guia abrangente para planejar e executar um plano de segurança nas funções de administrador do Azure AD, [Acesso privilegiado de proteção para implantações de nuvem e híbridas](security-planning.md).

## <a name="establish-emergency-accounts"></a>Estabelecer contas de emergência

Para manter o acesso ao repositório de gerenciamento de identidade quando surgem problemas, prepare contas de acesso de emergência de acordo com [Criar contas administrativas de acesso de emergência](security-emergency-access.md).

## <a name="secure-your-administrator-roles"></a>Proteger as funções de administrador

Os invasores que recebem o controle de contas com privilégios podem fazer danos enormes, portanto, proteja essas contas primeiro, usando a [política de acesso de linha de base](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/22/baseline-security-policy-for-azure-ad-admin-accounts-in-public-preview/) que está disponível por padrão para todas as organizações do Azure AD (em visualização pública). A política impõe a autenticação multifator em contas do Azure AD com privilégios. As seguintes funções do Azure AD são cobertas pela política de linha de base do Azure AD:

* Administrador global
* Administrador do SharePoint
* Administrador do Exchange
* Administrador de acesso condicional
* Administrador de segurança

## <a name="elevate-privilege-temporarily"></a>Elevar o privilégio temporariamente

Na maioria das atividades diárias, nem todos os usuários precisam de direitos de administrador global e nem todos eles devem ser atribuídos permanentemente à função de Administrador Global. Quando os usuários precisam das permissões de um Administrador Global, eles devem ativar a atribuição de função no [Privileged Identity Management](../privileged-identity-management/pim-configure.md) do Azure AD em sua própria conta ou em uma conta administrativa alternativa.

## <a name="next-steps"></a>Próximas etapas

Para obter uma referência para as descrições de função do Azure AD, consulte [Atribuir funções de administrador no Azure AD](permissions-reference.md)