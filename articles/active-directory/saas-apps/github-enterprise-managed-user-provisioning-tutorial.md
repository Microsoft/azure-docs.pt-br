---
title: 'Tutorial: configurar o usuário gerenciado do GitHub Enterprise para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o usuário gerenciado do GitHub Enterprise.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 6aee39c7-08a1-4110-b936-4c85d129743b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2021
ms.author: Zhchia
ms.openlocfilehash: 3efb346bc8796c82adb403ad65834cadc6782c09
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103196677"
---
# <a name="tutorial-configure-github-enterprise-managed-user-for-automatic-user-provisioning"></a>Tutorial: configurar o usuário gerenciado do GitHub Enterprise para provisionamento automático de usuário

Este tutorial descreve as etapas que você precisa executar no usuário gerenciado do GitHub Enterprise e no Azure Active Directory (AD do Azure) para configurar o provisionamento automático de usuário. Quando configurado, o Azure AD provisiona e desprovisiona automaticamente usuários e grupos para o usuário gerenciado do GitHub Enterprise usando o serviço de provisionamento do Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md).


## <a name="capabilities-supported"></a>Funcionalidades com suporte
> [!div class="checklist"]
> * Criar usuários no GitHub Enterprise Managed User
> * Remova os usuários do usuário gerenciado do GitHub Enterprise quando eles não exigem mais acesso
> * Manter os atributos de usuário sincronizados entre o Azure AD e o GitHub Enterprise Managed User
> * Provisionar grupos e associações de grupo no usuário gerenciado do GitHub Enterprise
> * Logon único para usuário gerenciado do GitHub Enterprise (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)
* Uma conta de usuário no Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicativo, Administrador de aplicativos de nuvem, Proprietário de Aplicativo ou Administrador Global).
* Usuários gerenciados da empresa habilitaram o GitHub Enterprise e configuraram para fazer logon com o SSO do SAML por meio de seu locatário do Azure AD

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje a implantação do provisionamento
1. Saiba mais sobre [como funciona o serviço de provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determine quem estará no [escopo de provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine quais dados [mapear entre o Azure AD e o usuário gerenciado do GitHub Enterprise](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-github-enterprise-managed-user-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configurar o usuário gerenciado do GitHub Enterprise para dar suporte ao provisionamento com o Azure AD

1. A URL do locatário é `https://api.github.com/scim/v2/enterprises/{enterprise}` . Esse valor será inserido no campo URL do locatário na guia provisionamento do seu aplicativo de usuário gerenciado do GitHub Enterprise no portal do Azure.

2. Como um administrador gerenciado do GitHub Enterprise, navegue até o canto superior direito-> clique na foto do seu perfil >, em seguida, clique em **configurações**.

3. Na barra lateral esquerda, clique em **configurações do desenvolvedor**.

4. Na barra lateral esquerda, clique em **tokens de acesso pessoal**.

5. Clique em **gerar novo token**.

6. Selecione o escopo **admin: Enterprise** para este token.

7. Clique em **Gerar Token**.

8. Copie e salve o **token secreto**. Esse valor será inserido no campo token secreto na guia provisionamento do aplicativo de usuário gerenciado do GitHub Enterprise no portal do Azure.

## <a name="step-3-add-github-enterprise-managed-user-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar usuário gerenciado do GitHub Enterprise da Galeria de aplicativos do Azure AD

Adicione o usuário gerenciado do GitHub Enterprise da Galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento para o usuário gerenciado do GitHub Enterprise. Se você tiver configurado anteriormente o usuário gerenciado do GitHub Enterprise para SSO, poderá usar o mesmo aplicativo. No entanto, recomendamos que você crie um aplicativo diferente ao testar a integração no início. Saiba mais sobre como adicionar um aplicativo da galeria [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo de provisionamento

No Azure AD, é possível definir quem estará no escopo de provisionamento com base na atribuição ao aplicativo ou nos atributos do usuário/grupo. Se você optar por definir quem estará no escopo de provisionamento com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você optar por definir quem estará no escopo de provisionamento com base somente em atributos do usuário ou do grupo, poderá usar um filtro de escopo, conforme descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).

* Ao atribuir usuários e grupos ao usuário gerenciado do GitHub Enterprise, você deve selecionar uma função diferente de **acesso padrão**. Os usuários com a função Acesso Padrão são excluídos do provisionamento e serão marcados como "Não qualificado efetivamente" nos logs de provisionamento.

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de implementar para todos. Quando o escopo de provisionamento é definido para usuários e grupos atribuídos, é possível controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, é possível especificar um [atributo com base no filtro de escopo](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).


## <a name="step-5-configure-automatic-user-provisioning-to-github-enterprise-managed-user"></a>Etapa 5. Configurar o provisionamento automático de usuário para o usuário gerenciado do GitHub Enterprise

Nesta seção, você verá orientações para seguir as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no TestApp com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-github-enterprise-managed-user-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o usuário gerenciado do GitHub Enterprise no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **GitHub Enterprise Managed User**.

    ![O link do usuário gerenciado do GitHub Enterprise na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia Provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Guia Provisionamento automático](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira a URL do locatário do usuário gerenciado do GitHub Enterprise e o token secreto. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao usuário gerenciado do GitHub Enterprise. Se a conexão falhar, verifique se sua conta de usuário gerenciado do GitHub Enterprise criou o token secreto como um proprietário corporativo e tente novamente.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para o usuário gerenciado do GitHub Enterprise**.

9. Examine os atributos de usuário que são sincronizados do Azure AD para o usuário gerenciado do GitHub Enterprise na seção de **mapeamento de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no GitHub Enterprise Managed User para operações de atualização. Se você optar por alterar o [atributo de destino correspondente](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), será necessário garantir que a API de usuário gerenciado do GitHub Enterprise dê suporte à filtragem de usuários com base nesse atributo. Selecione o botão **Salvar** para confirmar as alterações.

   |Atributo|Type|Com suporte para filtragem|
   |---|---|---|
   |externalId|String|&check;|
   |userName|String|
   |ativo|Boolean|
   |funções|String|
   |displayName|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |emails[type eq "work"].value|String|
   |emails[type eq "home"].value|String|
   |emails[type eq "other"].value|String|

10. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory para o usuário gerenciado do GitHub Enterprise**.

11. Examine os atributos de grupo que são sincronizados do Azure AD para o usuário gerenciado do GitHub Enterprise na seção de **mapeamento de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos no usuário gerenciado do GitHub Enterprise para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

      |Atributo|Type|Com suporte para filtragem|
      |---|---|---|
      |externalId|String|&check;|
      |displayName|String|
      |membros|Referência|

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD para o usuário gerenciado do GitHub Enterprise, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você deseja provisionar para o usuário gerenciado do GitHub Enterprise escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação começa o ciclo de sincronização inicial de todos os usuários e grupos definidos no **Escopo** na seção **Configurações**. O ciclo inicial leva mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução.

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar a implantação:

1. Use os [logs de provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais usuários foram provisionados com êxito ou não
2. Confira a [barra de progresso](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) para ver o status do ciclo de provisionamento e saber como fechá-la para concluir
3. Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre os estados de quarentena [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
