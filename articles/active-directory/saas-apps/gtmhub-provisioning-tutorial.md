---
title: 'Tutorial: configurar o Gtmhub para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o Gtmhub.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 10b68d00-a544-480b-9bd6-f6ac291a90d0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2020
ms.author: Zhchia
ms.openlocfilehash: 0e160def31a43bc94e4f6151b46efe72e585e953
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98735700"
---
# <a name="tutorial-configure-gtmhub-for-automatic-user-provisioning"></a>Tutorial: configurar o Gtmhub para o provisionamento automático de usuário

Este tutorial descreve as etapas que você precisa executar tanto no Gtmhub quanto no Azure Active Directory (Azure AD) para configurar o provisionamento automático de usuário. Quando configurado, o Azure AD provisiona e desprovisiona automaticamente usuários e grupos para [Gtmhub](https://www.gtmhub.com/) usando o serviço de provisionamento do Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md). 

>[!NOTE]
>Atualmente, quando o provisionamento automático de usuário é configurado, o Azure AD apenas desprovisiona automaticamente usuários e grupos para Gtmhub, bem como mapear usuários para suas respectivas equipes usando o serviço de provisionamento do Azure AD. Mas, no 2021 depois que o SSO for habilitado com o Gtmhub, os usuários serão automaticamente provisionados quando fizerem logon por meio de SSO e serão atribuídos à sua respectiva equipe.


## <a name="capabilities-supported"></a>Funcionalidades com suporte
> [!div class="checklist"]
> * Remova os usuários no Gtmhub quando eles não precisarem de mais acesso.
> * Mantenha os atributos de usuário sincronizados entre o Azure AD e o Gtmhub.
> * Mapeie os usuários em suas equipes automaticamente e os alinhe.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](../develop/quickstart-create-new-tenant.md).
* Uma conta de usuário no Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento (por exemplo, Administrador de Aplicativo, Administrador de aplicativos de nuvem, Proprietário de Aplicativo ou Administrador Global). 
* Uma conta do Enterprise Gtmhub.

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje a implantação do provisionamento
1. Saiba mais sobre [como funciona o serviço de provisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem estará no [escopo de provisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais dados [mapeados entre o Azure AD e o Gtmhub](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-gtmhub-to-support-team-mapping-and-user-de-provisioning-with-azure-ad"></a>Etapa 2. Configurar o Gtmhub para dar suporte ao mapeamento de equipe e ao desprovisionamento de usuário com o Azure AD

Para conectar seu aplicativo de provisionamento à sua conta do Gtmhub, você precisará emitir um token SCIM e compilar a URL do locatário.

### <a name="to-issue-a-new-scim-token"></a>Para emitir um novo token SCIM:

1. Entre em sua **conta do Gtmhub**. Navegue até **configurações > tokens de API de configuração >**.

    ![Guia tokens de API](media/gtmhub-provisioning-tutorial/api-tokens.png)
2. Clique no **token de emissão** e selecione **scim**. Insira um nome para o token e clique no botão **gerar token de API** .

    ![Guia gerar tokens](media/gtmhub-provisioning-tutorial/generate-token.png)
3. Depois que o token for gerado, você poderá copiá-lo e usá-lo em seu aplicativo de provisionamento do Azure AD.

    ![Copiar token](media/gtmhub-provisioning-tutorial/token.png)

### <a name="to-compile-the-tenant-url"></a>Para compilar a URL do locatário:

1. A URL do locatário deve estar no seguinte formato:

    `https://app.gtmhub.com/api/v1/scim/azure/{account_id}`

2. Se sua conta do Gtmhub estiver localizada nos EUA data center você também terá que adicionar o data center à URL:
    
     `https://app.us.gtmhub.com/api/v1/scim/azure/{account_id}`

3. Para obter a ID da conta, acesse **configurações** e, em seguida, selecione a guia **tokens de API** e copie a ID da conta:  ![ ID da conta](media/gtmhub-provisioning-tutorial/account-id.png)

## <a name="step-3-add-gtmhub-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar o Gtmhub da Galeria de aplicativos do Azure AD

Adicione o Gtmhub da Galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento no Gtmhub. Se você tiver configurado anteriormente o Gtmhub para SSO, poderá usar o mesmo aplicativo. No entanto, recomendamos que você crie um aplicativo diferente ao testar a integração no início. Saiba mais sobre como adicionar um aplicativo da galeria [aqui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo de provisionamento 

No Azure AD, é possível definir quem estará no escopo de provisionamento com base na atribuição ao aplicativo ou nos atributos do usuário/grupo. Se você optar por definir quem estará no escopo de provisionamento com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você optar por definir quem estará no escopo de provisionamento com base somente em atributos do usuário ou do grupo, poderá usar um filtro de escopo, conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir usuários e grupos ao Gtmhub, você deve selecionar uma função diferente de **acesso padrão**. Os usuários com a função Acesso Padrão são excluídos do provisionamento e serão marcados como "Não qualificado efetivamente" nos logs de provisionamento. Se a única função disponível no aplicativo for a de acesso padrão, você poderá [atualizar o manifesto do aplicativo](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar outras funções. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de implementar para todos. Quando o escopo de provisionamento é definido para usuários e grupos atribuídos, é possível controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, é possível especificar um [atributo com base no filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-gtmhub"></a>Etapa 5. Configurar o provisionamento automático de usuário para o Gtmhub 

Nesta seção, você verá orientações para seguir as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no TestApp com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-gtmhub-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para Gtmhub no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Gtmhub**.

    ![O link do Gtmhub na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia Provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Guia Provisionamento automático](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira a URL do locatário do Gtmhub e o token secreto. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao Gtmhub. Se a conexão falhar, verifique se sua conta do Gtmhub tem permissões de administrador e tente novamente.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para Gtmhub**.

9. Examine os atributos de usuário que são sincronizados do Azure AD para o Gtmhub na seção de **mapeamento de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no Gtmhub para operações de atualização. Se você optar por alterar o [atributo de destino correspondente](../app-provisioning/customize-application-attributes.md), será necessário garantir que a API Gtmhub dê suporte à filtragem de usuários com base nesse atributo. Selecione o botão **Salvar** para confirmar as alterações.

   |Atributo|Type|Com suporte para filtragem|
   |---|---|---|
   |userName|String|&check;|
   |externalId|String|&check;|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Referência|

10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD para o Gtmhub, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você deseja provisionar para o Gtmhub escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação começa o ciclo de sincronização inicial de todos os usuários e grupos definidos no **Escopo** na seção **Configurações**. O ciclo inicial leva mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar a implantação:

* Use os [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais usuários foram provisionados com êxito ou não
* Confira a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o status do ciclo de provisionamento e saber como fechá-la para concluir
* Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre os estados de quarentena [aqui](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)