---
title: 'Tutorial: Configurar o G Suite para provisionamento automático de usuários com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e descontinuar automaticamente as contas de usuário do Azure AD para o G Suite.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/18/2021
ms.author: Zhchia
ms.openlocfilehash: f6ef2f91d178db01e618c19df55705bc9c1209e4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104583744"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Tutorial: configurar o G Suite para provisionamento automático de usuários

Este tutorial descreve as etapas que você precisará executar no G Suite e no Azure AD (Active Directory) para configurar o provisionamento automático de usuário. Quando configurado, o Azure AD provisiona e desprovisiona automaticamente usuários e grupos no [G Suite](https://gsuite.google.com/) usando o serviço de Provisionamento do Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md). 

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Funcionalidades com suporte
> [!div class="checklist"]
> * Criar usuários no G Suite
> * Remover usuários no G Suite quando eles não precisarem mais de acesso
> * Manter os atributos de usuário sincronizados entre o Azure AD e o G Suite
> * Provisionar grupos e associações a um grupo no G Suite
> * [Logon único](./google-apps-tutorial.md) no G Suite (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](../develop/quickstart-create-new-tenant.md) 
* Uma conta de usuário no Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento (por exemplo, Administrador de Aplicativo, Administrador de aplicativos de nuvem, Proprietário de Aplicativo ou Administrador global). 
* [Um locatário do G Suite](https://gsuite.google.com/pricing.html)
* Uma conta de usuário no G Suite com permissões de Administrador.

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje a implantação do provisionamento
1. Saiba mais sobre [como funciona o serviço de provisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem estará no [escopo de provisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais dados serão [mapeados entre o Azure AD e o G Suite](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-g-suite-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configurar o G Suite para dar suporte ao provisionamento com o Azure AD

Antes de configurar o G Suite para o provisionamento automático de usuário com o Azure AD, você precisará habilitar o provisionamento do SCIM no G Suite.

1. Entre no [Console de Administração do G Suite](https://admin.google.com/) com a sua conta de administrador e selecione **Segurança**. Se você não vê o link, ele pode estar oculto sob o menu **Mais Controles**, na parte inferior da tela.

    ![Segurança no G Suite](./media/g-suite-provisioning-tutorial/gapps-security.png)

2. Na página **Segurança**, selecione **Referência de API**.

    ![API do G Suite](./media/g-suite-provisioning-tutorial/gapps-api.png)

3. Selecione **Habilitar acesso à API**.

    ![API do G Suite habilitada](./media/g-suite-provisioning-tutorial/gapps-api-enabled.png)

    > [!IMPORTANT]
   > Todo usuário que você pretende provisionar no G Suite **precisa** ter o nome de usuário no Azure AD vinculado a um domínio personalizado. Por exemplo, nomes de usuários do tipo bob@contoso.onmicrosoft.com não são aceitos pelo G Suite. Por outro lado, bob@contoso.com é aceito. Altere o domínio de um usuário existente seguindo as instruções descritas [aqui](../fundamentals/add-custom-domain.md).

4. Depois de adicionar e confirmar os domínios personalizados desejados com o Azure AD, você precisará confirmá-los novamente com o G Suite. Para verificar os domínios no G Suite, veja as seguintes etapas:

    a. No [Console de Administração do G Suite](https://admin.google.com/), selecione **Domínios**.

    ![Domínios no G Suite](./media/g-suite-provisioning-tutorial/gapps-domains.png)

    b. Selecione **Adicionar um domínio ou um alias de domínio**.

    ![Adicionar Domínio no G Suite](./media/g-suite-provisioning-tutorial/gapps-add-domain.png)

    c. Selecione **Adicionar outro domínio** e digite o nome do domínio que você deseja adicionar.

    ![Adicionar Outro no G Suite](./media/g-suite-provisioning-tutorial/gapps-add-another.png)

    d. Selecione **Continuar e confirmar a propriedade do domínio**. Em seguida, siga as etapas para verificar se você possui o nome de domínio. Para obter instruções completas sobre como confirmar seu domínio no Google, confira [Confirmar sua propriedade do site](https://support.google.com/webmasters/answer/35179).

    e. Repita as etapas anteriores para os domínios adicionais que você pretende adicionar ao G Suite.

5. Depois, determine a conta do administrador que deseja usar para gerenciar o provisionamento de usuário no G Suite. Procure **Funções de Administrador**.

    ![Administrador do G Suite](./media/g-suite-provisioning-tutorial/gapps-admin.png)

6. Para a **função Administrador** dessa conta, edite os **Privilégios** da função. Verifique se você habilitou todos os **Privilégios de API de Administrador**, de modo que essa conta possa ser usada para o provisionamento.

    ![Privilégios de administrador no G Suite](./media/g-suite-provisioning-tutorial/gapps-admin-privileges.png)

## <a name="step-3-add-g-suite-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicione o G Suite por meio da galeria de aplicativos do Azure AD

Adicione o G Suite por meio da galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento no G Suite. Se você já configurou o G Suite para SSO, use o mesmo aplicativo. No entanto, recomendamos que você crie um aplicativo diferente ao testar a integração no início. Saiba mais sobre como adicionar um aplicativo da galeria [aqui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo de provisionamento 

No Azure AD, é possível definir quem estará no escopo de provisionamento com base na atribuição ao aplicativo ou nos atributos do usuário/grupo. Se você optar por definir quem estará no escopo de provisionamento com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você optar por definir quem estará no escopo de provisionamento com base somente em atributos do usuário ou do grupo, poderá usar um filtro de escopo, conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir usuários e grupos ao G Suite, você precisará selecionar uma função diferente de **Acesso Padrão**. Os usuários com a função Acesso Padrão são excluídos do provisionamento e serão marcados como "Não qualificado efetivamente" nos logs de provisionamento. Se a única função disponível no aplicativo for a de acesso padrão, você poderá [atualizar o manifesto do aplicativo](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar outras funções. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de implementar para todos. Quando o escopo de provisionamento é definido para usuários e grupos atribuídos, é possível controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, é possível especificar um [atributo com base no filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-g-suite"></a>Etapa 5. Configurar o provisionamento automático de usuário no G Suite 

Nesta seção, você verá orientações para seguir as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no TestApp com base em atribuições de usuário e/ou grupo no Azure AD.

> [!NOTE]
> Para saber mais sobre o ponto de extremidade de API do Diretório do G Suite, veja [API do Diretório](https://developers.google.com/admin-sdk/directory).

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o G Suite no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**. Os usuários precisarão fazer logon em portal.azure.com e não poderão usar aad.portal.azure.com

    ![Folha de aplicativos empresariais](./media/g-suite-provisioning-tutorial/enterprise-applications.png)

    ![Folha Todos os aplicativos](./media/g-suite-provisioning-tutorial/all-applications.png)

2. Na lista de aplicativos, selecione **G Suite**.

    ![O link do G Suite na lista de Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**. Clique em **Introdução**.

    ![Captura de tela das opções Gerenciar com a opção Provisionamento destacada.](common/provisioning.png)

      ![Folha de introdução](./media/g-suite-provisioning-tutorial/get-started.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa Modo de Provisionamento com a opção Automático destacada.](common/provisioning-automatic.png)

5. Na seção **Credenciais de Administrador**, clique em **Autorizar**. Você será redirecionado para uma caixa de diálogo de autorização do Google em uma nova janela do navegador.

      ![Autorizar no G Suite](./media/g-suite-provisioning-tutorial/authorize-1.png)

6. Confirme que deseja dar permissão ao Azure AD para fazer alterações no seu locatário do G Suite. Selecione **Aceitar**.

     ![Autenticação de Locatário no G Suite](./media/g-suite-provisioning-tutorial/gapps-auth.png)

7. No portal do Azure, clique em **Testar Conectividade** para verificar se o Azure AD pode se conectar ao G Suite. Se a conexão falhar, verifique se a sua conta do G Suite tem permissões de Administrador e tente novamente. Em seguida, tente a etapa **Autorizar** novamente.

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **Mapeamentos**, selecione **Provisionar usuários do Azure Active Directory**.

9. Examine os atributos de usuário que serão sincronizados do Azure AD para o G Suite na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das contas de usuário no G Suite em operações de atualização. Se você optar por alterar o [atributo de destino correspondente](../app-provisioning/customize-application-attributes.md), precisará verificar se a API do G Suite dá suporte à filtragem de usuários com base nesse atributo. Selecione o botão **Salvar** para confirmar as alterações.

   |Atributo|Type|
   |---|---|
   |primaryEmail|String|
   |relations.[type eq "manager"].value|String|
   |name.familyName|String|
   |name.givenName|String|
   |suspenso|String|
   |externalIds.[type eq "custom"].value|String|
   |externalIds.[type eq "organization"].value|String|
   |addresses.[type eq "work"].country|String|
   |addresses.[type eq "work"].streetAddress|String|
   |addresses.[type eq "work"].region|String|
   |addresses.[type eq "work"].locality|String|
   |addresses.[type eq "work"].postalCode|String|
   |emails.[type eq "work"].address|String|
   |organizations.[type eq "work"].department|String|
   |organizations.[type eq "work"].title|String|
   |phoneNumbers.[type eq "work"].value|String|
   |phoneNumbers.[type eq "mobile"].value|String|
   |phoneNumbers.[type eq "work_fax"].value|String|
   |emails.[type eq "work"].address|String|
   |organizations.[type eq "work"].department|String|
   |organizations.[type eq "work"].title|String|
   |phoneNumbers.[type eq "work"].value|String|
   |phoneNumbers.[type eq "mobile"].value|String|
   |phoneNumbers.[type eq "work_fax"].value|String|
   |addresses.[type eq "home"].country|String|
   |addresses.[type eq "home"].formatted|String|
   |addresses.[type eq "home"].locality|String|
   |addresses.[type eq "home"].postalCode|String|
   |addresses.[type eq "home"].region|String|
   |addresses.[type eq "home"].streetAddress|String|
   |addresses.[type eq "other"].country|String|
   |addresses.[type eq "other"].formatted|String|
   |addresses.[type eq "other"].locality|String|
   |addresses.[type eq "other"].postalCode|String|
   |addresses.[type eq "other"].region|String|
   |addresses.[type eq "other"].streetAddress|String|
   |addresses.[type eq "work"].formatted|String|
   |changePasswordAtNextLogin|String|
   |emails.[type eq "home"].address|String|
   |emails.[type eq "other"].address|String|
   |externalIds.[type eq "account"].value|String|
   |externalIds.[type eq "custom"].customType|String|
   |externalIds.[type eq "customer"].value|String|
   |externalIds.[type eq "login_id"].value|String|
   |externalIds.[type eq "network"].value|String|
   |gender.type|String|
   |GeneratedImmutableId|String|
   |Identificador|String|
   |ims.[type eq "home"].protocol|String|
   |ims.[type eq "other"].protocol|String|
   |ims.[type eq "work"].protocol|String|
   |includeInGlobalAddressList|String|
   |ipWhitelisted|String|
   |organizations.[type eq "school"].costCenter|String|
   |organizations.[type eq "school"].department|String|
   |organizations.[type eq "school"].domain|String|
   |organizations.[type eq "school"].fullTimeEquivalent|String|
   |organizations.[type eq "school"].location|String|
   |organizations.[type eq "school"].name|String|
   |organizations.[type eq "school"].symbol|String|
   |organizations.[type eq "school"].title|String|
   |organizations.[type eq "work"].costCenter|String|
   |organizations.[type eq "work"].domain|String|
   |organizations.[type eq "work"].fullTimeEquivalent|String|
   |organizations.[type eq "work"].location|String|
   |organizations.[type eq "work"].name|String|
   |organizations.[type eq "work"].symbol|String|
   |OrgUnitPath|String|
   |phoneNumbers.[type eq "home"].value|String|
   |phoneNumbers.[type eq "other"].value|String|
   |websites.[type eq "home"].value|String|
   |websites.[type eq "other"].value|String|
   |websites.[type eq "work"].value|String|
   

10. Na seção **Mapeamentos**, selecione **Provisionar Grupos do Azure Active Directory**.

11. Examine os atributos de grupo que serão sincronizados do Azure AD para o G Suite na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência dos grupos no G Suite em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

      |Atributo|Type|
      |---|---|
      |email|String|
      |Membros|String|
      |name|String|
      |descrição|String|

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD no G Suite, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou os grupos que deseja provisionar no G Suite escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação começa o ciclo de sincronização inicial de todos os usuários e grupos definidos no **Escopo** na seção **Configurações**. O ciclo inicial leva mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução.

> [!NOTE]
> Se os usuários já tiverem uma conta pessoal/de consumidor existente que usa o endereço de email do usuário do Azure AD, isso poderá causar algum problema que pode ser resolvido por meio da Google Transfer Tool antes da execução da sincronização de diretório.

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar a implantação:

1. Use os [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais usuários foram provisionados com êxito ou não
2. Confira a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o status do ciclo de provisionamento e saber como fechá-la para concluir
3. Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre os estados de quarentena [aqui](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="change-log"></a>Log de alterações

* 17/10/2020 – Adição de suporte a outros atributos de usuário e grupo do G Suite.
* 17/10/2020 – Atualização dos nomes de atributos de destino do G Suite para que correspondam ao que está definido [aqui](https://developers.google.com/admin-sdk/directory).
* 17/10/2020 – Atualização dos mapeamentos de atributo padrão.
* 18/03/2021 – O email do gerente agora está sincronizado em vez da ID para todos os novos usuários. Para todos os usuários existentes que foram provisionados com um gerente como uma ID, você pode fazer uma reinicialização por meio do [Microsoft Graph](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http) com o escopo "completo" para garantir que o email seja provisionado. Essa alteração afeta apenas o trabalho de provisionamento GSuite e não o trabalho de provisionamento mais antigo começando com Goov2OutDelta. Observe que o email do gerente é provisionado quando o usuário é criado pela primeira vez ou quando o gerente é alterado. O email do gerente não será provisionado se o gerente alterar o próprio endereço de email. 

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
