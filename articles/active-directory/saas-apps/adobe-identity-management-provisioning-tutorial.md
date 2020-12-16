---
title: 'Tutorial: configurar o gerenciamento de identidades do Adobe para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o gerenciamento de identidades da Adobe.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 6ae05dc7-1265-44b4-a20c-512b5218b9d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2020
ms.author: Zhchia
ms.openlocfilehash: a87a08db672c459138fc1efd865332dc0f19944e
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586583"
---
# <a name="tutorial-configure-adobe-identity-management-for-automatic-user-provisioning"></a>Tutorial: configurar o gerenciamento de identidades do Adobe para provisionamento automático de usuário

Este tutorial descreve as etapas que você precisa executar tanto no gerenciamento de identidades do Adobe quanto no Azure Active Directory (AD do Azure) para configurar o provisionamento automático de usuário. Quando configurado, o Azure AD provisiona e desprovisiona automaticamente usuários e grupos para o gerenciamento de identidades da Adobe usando o serviço de provisionamento do Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades com suporte
> [!div class="checklist"]
> * Criar usuários no Adobe Identity Management
> * Remover usuários do Adobe Identity Management quando eles não precisam mais de acesso
> * Manter os atributos de usuário sincronizados entre o Azure AD e o Adobe Identity Management
> * Provisionar grupos e associações de grupo no Adobe Identity Management
> * Logon único no Adobe Identity Management (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](../develop/quickstart-create-new-tenant.md).
* Uma conta de usuário no Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento (por exemplo, Administrador de Aplicativo, Administrador de aplicativos de nuvem, Proprietário de Aplicativo ou Administrador Global). 
* Um diretório federado no [console de administração do Adobe](https://adminconsole.adobe.com/) com domínios verificados.

> [!NOTE]
> Se sua organização usa a ferramenta de sincronização do usuário ou uma integração do UMAPI, você deve primeiro pausar a integração. Em seguida, adicione o provisionamento automático do Azure AD para automatizar o gerenciamento de usuários no portal do Azure. Depois que o provisionamento automático do Azure AD estiver configurado e em execução, você poderá remover completamente a ferramenta de sincronização do usuário ou a integração do UMAPI.

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje a implantação do provisionamento
1. Saiba mais sobre [como funciona o serviço de provisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem estará no [escopo de provisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais dados [mapear entre o Azure AD e o gerenciamento de identidades da Adobe](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-adobe-identity-management-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configurar o gerenciamento de identidades do Adobe para dar suporte ao provisionamento com o Azure AD

1. Faça logon no [console de administração do Adobe](https://adminconsole.adobe.com/). Navegue até **configurações > detalhes do diretório > sincronizar**. 

2. Clique em **Adicionar sincronização**.

      ![Add](media/adobe-identity-management-provisioning-tutorial/add-sync.png)

3. Selecione **sincronizar usuários de Microsoft Azure** e clique em **Avançar**.

      ![Captura de tela que mostra ' sincronizar usuários de Microsoft Azure Active Directory ' selecionados.](media/adobe-identity-management-provisioning-tutorial/sync-users.png)

4. Copie e salve a **URL do locatário** e o **token secreto**. Esses valores serão inseridos nos campos **URL do locatário** e **token secreto** na guia provisionamento do aplicativo de gerenciamento de identidade do Adobe no portal do Azure.

      ![Sincronização](media/adobe-identity-management-provisioning-tutorial/token.png)

## <a name="step-3-add-adobe-identity-management-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar o Adobe Identity Management da Galeria de aplicativos do Azure AD

Adicione o Adobe Identity Management da Galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento para o gerenciamento de identidades da Adobe. Se você tiver configurado anteriormente o Adobe Identity Management para SSO, poderá usar o mesmo aplicativo. No entanto, recomendamos que você crie um aplicativo diferente ao testar a integração no início. Saiba mais sobre como adicionar um aplicativo da galeria [aqui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo de provisionamento 

No Azure AD, é possível definir quem estará no escopo de provisionamento com base na atribuição ao aplicativo ou nos atributos do usuário/grupo. Se você optar por definir quem estará no escopo de provisionamento com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você optar por definir quem estará no escopo de provisionamento com base somente em atributos do usuário ou do grupo, poderá usar um filtro de escopo, conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir usuários e grupos ao gerenciamento de identidades da Adobe, você deve selecionar uma função diferente de **acesso padrão**. Os usuários com a função Acesso Padrão são excluídos do provisionamento e serão marcados como "Não qualificado efetivamente" nos logs de provisionamento. Se a única função disponível no aplicativo for a de acesso padrão, você poderá [atualizar o manifesto do aplicativo](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar outras funções. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de implementar para todos. Quando o escopo de provisionamento é definido para usuários e grupos atribuídos, é possível controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, é possível especificar um [atributo com base no filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-adobe-identity-management"></a>Etapa 5. Configurar o provisionamento automático de usuário para o Adobe Identity Management 

Nesta seção, você verá orientações para seguir as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no TestApp com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-adobe-identity-management-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Adobe Identity Management no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Adobe Identity Management**.

    ![O link do Adobe Identity Management na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia Provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Guia Provisionamento automático](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira a URL do locatário do Adobe Identity Management e o token secreto recuperado anteriormente da etapa 2. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao Adobe Identity Management. Se a conexão falhar, verifique se sua conta do gerenciamento de identidades do Adobe tem permissões de administrador e tente novamente.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários com o gerenciamento de identidades da Adobe**.

9. Examine os atributos de usuário que são sincronizados do Azure AD para o gerenciamento de identidades da Adobe na seção **mapeamento de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no Adobe Identity Management para operações de atualização. Se você optar por alterar o [atributo de destino correspondente](../app-provisioning/customize-application-attributes.md), será necessário garantir que a API de gerenciamento de identidades da Adobe dê suporte à filtragem de usuários com base nesse atributo. Selecione o botão **Salvar** para confirmar as alterações.

   |Atributo|Type|
   |---|---|
   |userName|String|
   |emails[type eq "work"].value|String|
   |ativo|Boolean|
   |addresses[type eq "work"].country|String|
   |name.givenName|String|
   |name.familyName|String|
   |urn: IETF: params: SCIM: esquemas: extensão: Adobe: 2.0: User: emailAliases|String|

10. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory com o gerenciamento de identidades da Adobe**.

11. Examine os atributos de grupo que são sincronizados do Azure AD para o gerenciamento de identidades da Adobe na seção **mapeamento de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos no Adobe Identity Management para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

      |Atributo|Type|
      |---|---|
      |displayName|String|
      |membros|Referência|

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD para o Adobe Identity Management, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você deseja provisionar para o gerenciamento de identidades da Adobe escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação começa o ciclo de sincronização inicial de todos os usuários e grupos definidos no **Escopo** na seção **Configurações**. O ciclo inicial leva mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar a implantação:

1. Use os [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais usuários foram provisionados com êxito ou não
2. Confira a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o status do ciclo de provisionamento e saber como fechá-la para concluir
3. Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre os estados de quarentena [aqui](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)