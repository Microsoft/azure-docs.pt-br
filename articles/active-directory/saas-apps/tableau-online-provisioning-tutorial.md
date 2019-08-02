---
title: 'Tutorial: Configurar o Tableau Online para o provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar contas de usuário ao Tableau Online automaticamente.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 0be9c435-f9a1-484d-8059-e578d5797d8e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e203e88de8d806f489e5a7ab9bfd227c8232f84
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670908"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Tutorial: Configurar o Tableau Online para o provisionamento automático do usuário

Este tutorial demonstra as etapas para executar no Tableau Online e Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar usuários e grupos ao Tableau Online automaticamente.

> [!NOTE]
> Este tutorial descreve um conector que se baseia no serviço de provisionamento de usuário do Azure AD. Para obter informações sobre esse serviço faz, como ele funciona e perguntas frequentes, consulte [automatizar o provisionamento e desprovisionamento de usuários para aplicativos de software-como um serviço (SaaS) com o Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você tenha:

*   Um locatário do Azure AD.
*   Um [locatário Tableau Online](https://www.tableau.com/).
*   Uma conta de usuário do Tableau Online com permissões de administrador.

> [!NOTE]
> Integração de provisionamento do Azure AD depende de [Tableau Online Rest API](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm). Essa API está disponível para desenvolvedores Tableau Online.

## <a name="add-tableau-online-from-the-azure-marketplace"></a>Adicionar o Tableau Online no Azure Marketplace
Antes de configurar o Tableau Online para provisionamento com o Azure AD automático de usuário, adicione o Tableau Online no Azure Marketplace para sua lista de aplicativos SaaS gerenciados.

Para adicionar o Tableau Online do Marketplace, siga estas etapas.

1. No [portal do Azure](https://portal.azure.com), no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![Ícone do Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Tableauonline** e selecione **Tableau Online** no painel de resultados. Para adicionar o aplicativo, selecione **adicionar**.

    ![Tableau Online na lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>Atribuir usuários ao Tableau Online

Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, decida quais usuários ou grupos no Azure AD precisam de acesso ao Tableau Online. Para atribuir esses usuários ou grupos ao Tableau Online, siga as instruções em [atribuir um usuário ou grupo a um aplicativo empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Dicas importantes para atribuir usuários ao Tableau Online

*   É recomendável que você atribua um único usuário do Azure AD ao Tableau Online para testar a configuração de provisionamento automático de usuário. Você pode atribuir usuários ou grupos adicionais mais tarde.

*   Quando você atribui um usuário ao Tableau Online, selecione qualquer função específica ao aplicativo válida, se disponível, na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>Configurar o provisionamento automático de usuário ao Tableau Online

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do AD do Azure. Use-o para criar, atualizar e desabilitar usuários ou grupos no Tableau Online com base nas atribuições de usuário ou grupo no AD do Azure.

> [!TIP]
> Você também pode habilitar baseado em SAML SSO para o Tableau Online. Siga as instruções de [Tableau Online único tutorial do logon](tableauonline-tutorial.md). O logon único o pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementam.

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Configurar o provisionamento automático de usuário do Tableau Online no Azure AD

1. Entre no [Portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais** > **todos os aplicativos** > **Tableau Online**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Tableau Online**.

    ![O link do Tableau Online na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Provisionamento do Tableau Online](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Modo de provisionamento tableau Online](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. Sob o **credenciais de administrador** seção, o domínio, nome de usuário administrador, senha de administrador e URL do conteúdo da sua conta do Tableau Online de entrada:

   * No **domínio** caixa, preencha o subdomínio com base na etapa 6.

   * No **Admin Username** caixa, preencha o nome de usuário da conta de administrador no locatário do Clarizen. Um exemplo é admin@contoso.com.

   * No **senha de administrador** caixa, preencha a senha da conta de administrador que corresponde ao nome de usuário administrador.

   * No **URL de conteúdo** caixa, preencha o subdomínio com base na etapa 6.

6. Depois de entrar sua conta administrativa para o Tableau Online, você pode obter os valores para **domínio** e **URL de conteúdo** na URL da página de administração.

    * O **Domínio** da conta do Tableau Online pode ser copiado desta parte da URL:

        ![Domínio do tableau Online](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * O **URL de conteúdo** para o Tableau Online conta pode ser copiada dessa seção. É um valor que é definido durante a configuração da conta. Neste exemplo, o valor é "contoso":

        ![URL de conteúdo tableau Online](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > Sua **domínio** pode ser diferente do mostrado aqui.

7. Depois de preencher as caixas mostradas na etapa 5, selecione **Conexão de teste** para certificar-se de que o Azure AD pode se conectar ao Tableau Online. Se a conexão falhar, certifique-se de que sua conta do Tableau Online tem permissões de administrador e tente novamente.

    ![Conexão de teste Online tableau](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. No **Email de notificação** caixa, digite o endereço de email da pessoa ou grupo para receber as notificações de erro de provisionamento. Selecione o **enviar uma notificação por email quando ocorre uma falha** caixa de seleção.

    ![Email de notificação Online tableau](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. Clique em **Salvar**.

10. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Tableau**.

    ![Sincronização do tableau Online de usuário](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Examine os atributos de usuário que são sincronizados do Azure AD ao Tableau Online na **mapeamentos de atributo** seção. Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário no Tableau Online para operações de atualização. Para salvar as alterações, selecione **salvar**.

    ![Correspondência usuário atributos do tableau Online](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

12. Na seção **Mapeamentos**, selecione **Sincronizar Grupos do Azure Active Directory com o Tableau**.

    ![Tableau Online do grupo de sincronização](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Examine os atributos de grupo que são sincronizados do Azure AD ao Tableau Online na **mapeamentos de atributo** seção. Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário no Tableau Online para operações de atualização. Para salvar as alterações, selecione **salvar**.

    ![Correspondência grupo atributos do tableau Online](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. Para configurar filtros de escopo, siga as instruções de [tutorial do filtro de escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para habilitar o Tableau Online, serviço de provisionamento no Azure AD a **as configurações** seção, altere **Status de provisionamento** para **em**.

    ![Status de provisionamento tableau Online](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Defina os usuários ou grupos que você deseja provisionar ao Tableau Online. No **as configurações** seção, selecione os valores desejados na **escopo**.

    ![Escopo de tableau Online](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. Quando estiver pronto para provisionar, selecione **salvar**.

    ![Tableau Online de salvamento](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

Essa operação inicia a sincronização inicial de todos os usuários ou grupos definidos no **escopo** na **configurações** seção. A sincronização inicial levará mais tempo para que as sincronizações posteriores. Eles ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do AD do Azure é executado. 

Você pode usar o **detalhes de sincronização** seção para monitorar o progresso e siga os links para o relatório de atividade de provisionamento. O relatório descreve todas as ações executadas pelo serviço no Tableau Online de provisionamento do Azure AD.

Para obter informações sobre como ler os logs de provisionamento do Azure AD, confira [Relatórios sobre o provisionamento automático de contas de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
