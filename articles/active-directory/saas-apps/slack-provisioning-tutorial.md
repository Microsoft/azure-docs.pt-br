---
title: 'Tutorial: Provisionamento de usuário para o Slack – Azure AD'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no Slack.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: arvinh
ms.openlocfilehash: 92c2ae13b840d7a73d86365ce88584bcafc878e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181443"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Tutorial: Configurar Slack para provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas que precisam ser executadas no Slack e no Azure AD para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o Slack. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades com suporte
> [!div class="checklist"]
> * Criar usuários no Slack
> * Remover usuários no Slack quando eles não precisarem mais de acesso
> * Manter os atributos de usuário sincronizados entre o Azure AD e o Slack
> * Provisionar grupos e associações a um grupo no Slack
> * [Logon único](./slack-tutorial.md) para o Slack (recomendado)


## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* [Um locatário do Azure AD](../develop/quickstart-create-new-tenant.md).
* Uma conta de usuário no Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento (por exemplo, Administrador de Aplicativo, Administrador de aplicativos de nuvem, Proprietário de Aplicativo ou Administrador global).
* Um locatário do Slack com o [plano Plus](https://aadsyncfabric.slack.com/pricing) ou superior habilitado.
* Uma conta de usuário no Slack com permissões de Administrador de Equipe.

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje a implantação do provisionamento
1. Saiba mais sobre [como funciona o serviço de provisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem estará no [escopo de provisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais dados serão [mapeados entre o Azure AD e o Slack](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-add-slack-from-the-azure-ad-application-gallery"></a>Etapa 2. Adicionar o Slack por meio da galeria de aplicativos do Azure AD

Adicione o Slack por meio da galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento no Slack. Se você já tiver configurado o Slack para SSO, poderá usar o mesmo aplicativo. No entanto, recomendamos que você crie um aplicativo diferente ao testar a integração no início. Saiba mais sobre como adicionar um aplicativo da galeria [aqui](../manage-apps/add-application-portal.md). 

## <a name="step-3-define-who-will-be-in-scope-for-provisioning"></a>Etapa 3. Defina quem estará no escopo de provisionamento 

No Azure AD, é possível definir quem estará no escopo de provisionamento com base na atribuição ao aplicativo ou nos atributos do usuário/grupo. Se você optar por definir quem estará no escopo de provisionamento com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você optar por definir quem estará no escopo de provisionamento com base somente em atributos do usuário ou do grupo, poderá usar um filtro de escopo, conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir usuários e grupos ao Slack, é preciso selecionar uma função diferente do **Acesso Padrão**. Os usuários com a função Acesso Padrão são excluídos do provisionamento e serão marcados como "Não qualificado efetivamente" nos logs de provisionamento. Se a única função disponível no aplicativo for a de acesso padrão, você poderá [atualizar o manifesto do aplicativo](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar outras funções. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de implementar para todos. Quando o escopo de provisionamento é definido para usuários e grupos atribuídos, é possível controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, é possível especificar um [atributo com base no filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="step-4-configure-automatic-user-provisioning-to-slack"></a>Etapa 4. Configurar o provisionamento automático de usuário no Slack 

Esta seção orienta você pela conexão do Azure AD com a API de provisionamento de conta de usuário do Slack e pela configuração do serviço de provisionamento, a fim de criar, atualizar e desabilitar contas de usuário atribuídas no Slack com base na atribuição de usuário e de grupo do Azure AD.

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Slack no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **Slack**.

    ![O link do Slack na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções Gerenciar com a opção Provisionamento destacada.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa Modo de Provisionamento com a opção Automático destacada.](common/provisioning-automatic.png)

5. Na seção **Credenciais de Administrador**, clique em **Autorizar**. Isso abre uma caixa de diálogo de autorização do Slack em uma nova janela do navegador.

    ![Captura de tela mostrando o botão Autorizar Credenciais de Administrador.](media/slack-provisioning-tutorial/authorization.png)


6. Na nova janela, entre no Slack usando sua conta de Administrador de Equipe. Na caixa de diálogo de autorização resultante, selecione a equipe do Slack para a qual você deseja habilitar o provisionamento e, em seguida, selecione **Autorizar**. Depois de concluído, retorne ao Portal do Azure para concluir a configuração de provisionamento.

    ![Caixa de diálogo Autorização](./media/slack-provisioning-tutorial/slackauthorize.png)

7. No Portal do Azure, clique em **Testar Conexão** para garantir que o Azure AD possa se conectar ao seu aplicativo Slack. Se a conexão falhar, verifique se a sua conta do Slack tem permissões de Administrador de Equipe e repita a etapa "Autorizar".

8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

9. Clique em **Salvar**.

10. Na seção Mapeamentos, selecione **Sincronizar usuários do Azure Active Directory com o Slack**.

11. Na seção **Mapeamentos de Atributo**, revise os atributos de usuário que serão sincronizados do Azure AD para o Slack. Observe que os atributos selecionados como propriedades **Correspondentes** serão usados para corresponder as contas de usuário no Slack para operações de atualização. Selecione o botão Salvar para confirmar as alterações.

   |Atributo|Type|
   |---|---|
   |ativo|Boolean|
   |externalId|String|
   |displayName|String|
   |name.familyName|String|
   |name.givenName|String|
   |título|String|
   |emails[type eq "work"].value|String|
   |userName|String|
   |nickName|String|
   |addresses[type eq "untyped"].streetAddress|String|
   |addresses[type eq "untyped"].locality|String|
   |addresses[type eq "untyped"].region|String|
   |addresses[type eq "untyped"].postalCode|String|
   |addresses[type eq "untyped"].country|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "work"].value|String|
   |roles[primary eq "True"].value|String|
   |localidade|String|
   |name.honorificPrefix|String|
   |photos[type eq "photo"].value|String|
   |profileUrl|String|
   |timezone|String|
   |userType|String|
   |urn:scim:schemas:extension:enterprise:1.0.department|String|
   |urn:scim:schemas:extension:enterprise:1.0.manager|Referência|
   |urn:scim:schemas:extension:enterprise:1.0.employeeNumber|String|
   |urn:scim:schemas:extension:enterprise:1.0.costCenter|String|
   |urn:scim:schemas:extension:enterprise:1.0.organization|String|
   |urn:scim:schemas:extension:enterprise:1.0.division|String|

12. Na seção **Mapeamentos**, selecione **Sincronizar Grupos do Azure Active Directory com o Slack**.

13. Na seção **Mapeamentos de Atributo**, revise os atributos de grupo que serão sincronizados do Azure AD para o Slack. Observe que os atributos selecionados como propriedades **Correspondentes** serão usados para corresponder os grupos no Slack para operações de atualização. Selecione o botão Salvar para confirmar as alterações.

      |Atributo|Type|
      |---|---|
      |displayName|String|
      |membros|Referência|

14. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para habilitar o serviço de provisionamento do Azure AD para o Slack, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

16. Defina os usuários e/ou os grupos que deseja provisionar no Slack escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

17. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação começa o ciclo de sincronização inicial de todos os usuários e grupos definidos no **Escopo** na seção **Configurações**. O ciclo inicial leva mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. 

## <a name="step-5-monitor-your-deployment"></a>Etapa 5. Monitorar a implantação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar a implantação:

1. Use os [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais usuários foram provisionados com êxito ou não
2. Confira a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o status do ciclo de provisionamento e saber como fechá-la para concluir
3. Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre os estados de quarentena [aqui](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="troubleshooting-tips"></a>Dicas de solução de problemas

* Ao configurar o atributo **displayName** do Slack, esteja ciente dos seguintes comportamentos:

  * Os valores não são totalmente exclusivos (por exemplo, 2 usuários podem ter o mesmo nome de exibição)

  * Suporta caracteres não ingleses, espaços e letras maiúsculas. 
  
  * As pontuações permitidas são pontos, sublinhados, hifens, apóstrofos, colchetes (por exemplo, **( [ { } ] )**) e separadores (por exemplo, **, /;**).
  
  * A propriedade displayName não pode ter um caractere '\@'. Se um '\@' for incluído, você poderá encontrar um evento ignorado nos logs de provisionamento com a descrição "AttributeValidationFailed".

  * Apenas atualiza se essas duas configurações estiverem configuradas no local de trabalho / organização do Slack - **A sincronização de perfil está ativada** e **Os usuários não podem alterar seu nome de exibição**.

* O atributo **userName** do Slack precisa ter menos de 21 caracteres e ter um valor exclusivo.

* O Slack só permite a correspondência com os atributos **userName** e **email**.  
  
* Os códigos de erros comuns são listados na documentação oficial do Slack: https://api.slack.com/scim#errors

## <a name="change-log"></a>Log de alterações

* 16/06/2020: modificação do atributo DisplayName para que ele seja atualizado somente durante a criação de usuário.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)