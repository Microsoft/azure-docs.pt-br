---
title: Azure IoT Suite e Azure Active Directory | Microsoft Docs
description: "Descreve como o Pacote IoT do Azure usa o Active Directory do Azure para gerenciar permissões."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 246228ba-954a-4d96-b6d6-e53e4590cb4f
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: dobett
ms.openlocfilehash: f67c7bfa3f0ea7b720c8684cc0c501be3e464373
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="permissions-on-the-azureiotsuitecom-site"></a>Permissões no site azureiotsuite.com

## <a name="what-happens-when-you-sign-in"></a>O que acontece quando você entra

Quando você entra pela primeira vez no [azureiotsuite.com][lnk-azureiotsuite], o site determina os níveis de permissão que você tem com base no locatário do AAD (Azure Active Directory) e na assinatura do Azure selecionados no momento.

1. Primeiro, para preencher a lista de locatários logo ao lado de seu nome de usuário, site descobre a quais locatários do AAD no Azure você pertence. No momento, o site só consegue obter os tokens de usuário de um locatário por vez. Por isso, quando você alternar locatários usando o menu suspenso no canto superior direito, o site conectará você a esse locatário para obter os tokens para ele.

2. Em seguida, o site descobre no Azure quais assinaturas estão associadas ao locatário selecionado. Você verá as assinaturas quando criar uma nova solução pré-configurada.

3. Por fim, o site recuperará todos os recursos nas assinaturas e nos grupos de recursos marcados como soluções pré-configuradas e preencherá os blocos na home page.

As seções a seguir descrevem as funções que controlam o acesso às soluções pré-configuradas.

## <a name="aad-roles"></a>Funções do AAD

As funções do AAD controlam as soluções pré-configuradas de provisão de capacidade e gerenciam usuários em uma solução pré-configurada.

Saiba mais sobre funções de administrador no AAD em [Atribuir funções de administrador no Azure AD][lnk-aad-admin]. O artigo atual enfoca as funções do diretório **Administrador Global** e **Usuário** conforme utilizadas pelas soluções pré-configuradas.

### <a name="global-administrator"></a>Administrador global

Pode haver muitos administradores globais por locatário do AAD:

* Quando você cria um locatário do AAD, por padrão vira o administrador global desse locatário.
* O administrador global pode provisionar uma solução pré-configurada e recebe uma função de **Administrador** para o aplicativo dentro do seu locatário do AAD.
* Se outro usuário no mesmo locatário do AAD criar um aplicativo, a função padrão concedida ao administrador global será **ReadOnly**.
* Um administrador global pode atribuir usuários a funções para aplicativos usando o [Portal do Azure][lnk-portal].

### <a name="domain-user"></a>Usuário de domínio

Pode haver muitos usuários de domínio por locatário do AAD:

* Um usuário do domínio pode provisionar uma solução pré-configurada por meio do site [azureiotsuite.com][lnk-azureiotsuite]. Por padrão, a função **Admin** é concedida ao usuário de domínio no aplicativo provisionado.
* Um usuário de domínio pode criar um aplicativo usando o script build.cmd no repositório [azure-iot-remote-monitoring][lnk-rm-github-repo], [azure-iot-predictive-maintenance][lnk-pm-github-repo] ou [azure-iot-connected-factory][lnk-cf-github-repo]. No entanto, a função padrão concedida ao usuário de domínio é **ReadOnly**, porque um usuário de domínio não tem permissão para atribuir funções.
* Se outro usuário no locatário do AAD criar um aplicativo, o usuário de domínio será atribuído à função **ReadOnly** por padrão para esse aplicativo.
* O usuário de domínio não terá a capacidade de atribuir funções para aplicativos, portanto, não poderá adicionar usuários ou funções para usuários para um aplicativo, mesmo se o tiver provisionado.

### <a name="guest-user"></a>Usuário Convidado

Pode haver muitos usuários convidados por locatário do AAD. Os usuários convidados têm um conjunto limitado de direitos no locatário do AAD. Como resultado, os usuários convidados não podem provisionar uma solução pré-configurada no locatário do AAD.

Para obter mais informações sobre usuários e funções no AAD, confira os seguintes recursos:

* [Criar usuários no Azure AD][lnk-create-edit-users]
* [Atribuir usuários a aplicativos][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Funções de administrador da assinatura do Azure

As funções de administrador do Azure controlam a capacidade de mapear uma assinatura do Azure para um locatário do AD.

Descubra mais sobre as funções de administrador do Azure no artigo [Como adicionar ou alterar o Coadministrador, o Administrador de Serviços e o Administrador da Conta do Azure][lnk-admin-roles].

## <a name="application-roles"></a>Funções de aplicativo

As funções do aplicativo controlam o acesso a dispositivos em sua solução pré-configurada.

Há duas funções definidas e uma função implícita definida em um aplicativo provisionado:

* **Administrador:** tem controle total para adicionar, gerenciar, remover dispositivos e modificar configurações.
* **Somente Leitura:** pode exibir dispositivos, regras, ações, trabalhos e telemetria.

Você pode encontrar as permissões atribuídas a cada função no arquivo de origem [RolePermissions.cs][lnk-resource-cs].

### <a name="changing-application-roles-for-a-user"></a>Alterando as funções de aplicativo para um usuário

Você pode usar o procedimento a seguir para tornar um usuário em seu Active Directory um administrador de sua solução pré-configurada.

Você deve ser um administrador global do AAD para alterar funções para um usuário:

1. Acesse o [Portal do Azure][lnk-portal].
2. Selecione **Azure Active Directory**.
3. Verifique se que você está usando o diretório que você escolheu em azureiotsuite.com ao provisionar sua solução. Se você tiver vários diretórios associados à sua assinatura, poderá alternar entre eles se clicar no nome da conta na parte superior direita do portal.
4. Clique em **Aplicativos empresariais** e, em seguida, **Todos os aplicativos**.
4. Mostrar **Todos os aplicativos** com **Qualquer** status. Então pesquise um aplicativo com o nome da sua solução pré-configurada.
5. Clique no nome do aplicativo que corresponda ao nome da solução pré-configurada.
6. Clique em **Usuários e grupos**.
7. Selecione o usuário para o qual você deseja alternar funções.
8. Clique em **Atribuir** e selecione a função (como **Administrador**) que você deseja atribuir ao usuário, então clique na marca de seleção.

## <a name="faq"></a>Perguntas frequentes

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-complete-this-task"></a>Sou um administrador de serviços e gostaria de alterar o mapeamento de diretório entre minha assinatura e um locatário do AAD específico. Como posso concluir esta tarefa?

1. Vá para o [Portal Clássico do Azure][lnk-classic-portal], clique em **Configurações** na lista de serviços no lado esquerdo.
2. Selecione a assinatura para a qual você deseja alterar o mapeamento de diretórios.
3. Clique em **Editar Diretório**.
4. Selecione o **Diretório** que você deseja usar na lista suspensa. Clique na seta para frente.
5. Confirme o mapeamento de diretórios e os coadministradores afetados. Se você estiver fazendo a transferência de outro diretório, todos os administradores do diretório original serão removidos.

### <a name="im-a-domain-usermember-on-the-aad-tenant-and-ive-created-a-preconfigured-solution-how-do-i-get-assigned-a-role-for-my-application"></a>Sou um usuário/membro do domínio no locatário do AAD e criei uma solução pré-configurada. Como recebo uma função para o meu aplicativo?

Peça ao administrador global para tornar você um administrador global no locatário AAD e, em seguida, atribua funções aos usuários por conta própria. Como alternativa, peça ao administrador global para atribuir uma função a você diretamente. Se desejar alterar o locatário do AAD em que sua solução pré-configurada foi implantada, consulte a próxima pergunta.

### <a name="how-do-i-switch-the-aad-tenant-my-remote-monitoring-preconfigured-solution-and-application-are-assigned-to"></a>Como alternar o locatário do AAD ao qual minha solução pré-configurada de monitoramento remoto e o aplicativo estão atribuídos?

É possível executar uma implantação de nuvem do <https://github.com/Azure/azure-iot-remote-monitoring> e reimplantar com um locatário do AAD recém-criado. Já que você é, por padrão, um administrador global quando cria um novo locatário do AAD, você tem permissões para adicionar usuários e atribuir funções a eles.

1. Crie um diretório do AAD no [Portal do Azure][lnk-portal].
2. Acesse <https://github.com/Azure/azure-iot-remote-monitoring>.
3. Execute `build.cmd cloud [debug | release] {name of previously deployed remote monitoring solution}` (por exemplo, `build.cmd cloud debug myRMSolution`)
4. Quando solicitado, defina a **tenantid** para seu locatário recém-criado em vez do locatário anterior.

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organisational-account"></a>Quero alterar um Administrador de Serviços ou um Coadministrador quando o logon for feito com uma conta organizacional

Consulte o artigo de suporte [Alterando um Administrador de Serviços e um Coadministrador quando o logon for feito com uma conta organizacional][lnk-service-admins].

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Por que vejo este erro? "Sua conta não tem as permissões adequadas para criar uma solução. Verifique com o administrador da conta ou tente com uma conta diferente."

Observe o seguinte diagrama para obter orientação:

![][img-flowchart]

> [!NOTE]
> Se você continuar recebendo o erro após validar que é um administrador global no locatário do AAD e um coadministrador na assinatura, peça ao administrador da conta que remova o usuário e reatribua as permissões necessárias nesta ordem. Primeiro, adicione o usuário como um administrador global e adicionar o usuário como um coadministrador na assinatura do Azure. Se o problema persistir, entre em contato com [Ajuda e Suporte][lnk-help-support].

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Por que estou vendo este erro se eu tenho uma assinatura do Azure? “Uma assinatura do Azure é necessária para criar soluções pré-configuradas. Você pode criar uma conta de avaliação gratuita em apenas alguns minutos.”

Se você tiver certeza de que tem uma assinatura do Azure, valide o mapeamento do locatário para a sua assinatura e certifique-se de que o locatário correto tenha sido selecionado na lista suspensa. Se você tiver validado corretamente o locatário desejado, siga o diagrama acima e valide o mapeamento de sua assinatura e este locatário do AAD.

## <a name="next-steps"></a>Próximas etapas
Para continuar aprendendo sobre o IoT Suite, veja como é possível [personalizar uma solução pré-configurada][lnk-customize].

[img-flowchart]: media/iot-suite-permissions/flowchart.png

[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-cf-github-repo]: https://github.com/Azure/azure-iot-connected-factory
[lnk-aad-admin]: ../active-directory/active-directory-assign-admin-roles.md
[lnk-classic-portal]: https://manage.windowsazure.com/
[lnk-portal]: https://portal.azure.com/
[lnk-create-edit-users]: ../active-directory/active-directory-create-users.md
[lnk-assign-app-roles]: ../active-directory/active-directory-coreapps-assign-user-azure-portal.md
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: ../billing/billing-add-change-azure-subscription-administrator.md
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
