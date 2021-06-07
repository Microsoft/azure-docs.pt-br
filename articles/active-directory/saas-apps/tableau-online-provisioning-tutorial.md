---
title: 'Tutorial: Configurar o Tableau Online para provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no Tableau Online.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: a42790e079985b003776b381c74f837b0ba619b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94359197"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Tutorial: Configurar o Tableau Online para provisionamento automático de usuário

Este tutorial demonstra as etapas a serem executadas no Tableau Online e no Azure AD (Active Directory) a fim de configurar o Azure AD para provisionar e desprovisionar automaticamente usuários ou grupos no Tableau Online.

> [!NOTE]
> Este tutorial descreve um conector baseado no serviço de provisionamento de usuário do Azure Active Directory. Para obter informações sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e o desprovisionamento de usuários para aplicativos SaaS (software como serviço) com o Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você possui:

*   Um locatário do Azure AD.
*   Um [locatário do Tableau Online](https://www.tableau.com/).
*   Uma conta de usuário do Tableau Online com permissões de administrador.

> [!NOTE]
> A integração de provisionamento do Azure AD depende da [API REST do Tableau Online](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm). Essa API está disponível para desenvolvedores do Tableau Online.

## <a name="add-tableau-online-from-the-azure-marketplace"></a>Adicionar o Tableau Online do Azure Marketplace
Antes de configurar o Tableau Online para o provisionamento automático de usuário com o Azure AD, adicione-o por meio do Azure Marketplace à lista de aplicativos SaaS gerenciados.

Para adicionar o Tableau Online por meio do Azure Marketplace, siga estas etapas.

1. No [Portal do Microsoft Azure](https://portal.azure.com), no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O ícone do Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Tableau Online** e selecione o **Tableau Online** no painel de resultados. Para adicionar o aplicativo, selecione **Adicionar**.

    ![Tableau Online na lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>Atribuir usuários ao Tableau Online

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários ou os grupos que foram atribuídos a um aplicativo no Azure Active Directory são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, decida quais usuários ou grupos do Azure AD precisam ter acesso ao Tableau Online. Para atribuir esses usuários ou grupos ao Tableau Online, siga as instruções descritas em [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Dicas importantes para atribuir usuários ao Tableau Online

*   Recomendamos que você atribua um só usuário do Azure AD ao Tableau Online para testar a configuração de provisionamento automático de usuário. Você pode atribuir usuários ou grupos adicionais mais tarde.

*   Ao atribuir um usuário ao Tableau Online, selecione qualquer função válida específica do aplicativo (se disponível) na caixa de diálogo da atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>Configurar o provisionamento automático de usuário no Tableau Online

Esta seção aborda as etapas para configurar o serviço de provisionamento do Azure Active Directory. Use-o para criar, atualizar e desabilitar usuários ou grupos no Tableau Online com base em atribuições de usuário ou de grupo no Azure AD.

> [!TIP]
> Habilite também o logon único baseado em SAML para o Tableau Online. Siga as instruções descritas no [Tutorial de logon único do Tableau Online](tableauonline-tutorial.md). O logon único pode ser configurado de modo independente do provisionamento automático de usuário, embora os dois sejam complementares.

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Configurar o provisionamento automático de usuário para o Tableau Online no Azure AD

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** > **Todos os aplicativos** > **Tableau Online**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Tableau Online**.

    ![O link do Tableau Online na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Provisionamento do Tableau Online](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Modo de Provisionamento do Tableau Online](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. Na seção **Credenciais de Administrador**, insira o domínio, o nome de usuário administrador, a senha de administrador e a URL de conteúdo de sua conta do Tableau Online:

   * Na caixa **Domínio**, preencha o subdomínio com base na Etapa 6.

   * Na caixa **Nome de Usuário Administrador**, preencha o nome de usuário da conta do administrador no locatário do Tableau Online. Um exemplo é admin@contoso.com.

   * Na caixa **Senha do Administrador**, insira a senha da conta do administrador que corresponde ao nome de usuário administrador.

   * Na caixa **URL de Conteúdo**, preencha o subdomínio com base na Etapa 6.

6. Depois de entrar na conta administrativa do Tableau Online, você poderá obter os valores do **Domínio** e da **URL de Conteúdo** na URL da página administrador.

    * O **Domínio** da conta do Tableau Online pode ser copiado desta parte da URL:

        ![Domínio do Tableau Online](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * A **URL de Conteúdo** da conta do Tableau Online pode ser copiada desta seção. Trata-se de um valor que é definido durante a configuração da conta. Neste exemplo, o valor é "contoso":

        ![URL de Conteúdo do Tableau Online](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > O **Domínio** pode ser diferente daquele mostrado aqui.

7. Depois de preencher as caixas mostradas na Etapa 5, selecione **Testar Conectividade** para verificar se o Azure AD pode se conectar ao Tableau Online. Se a conexão falhar, verifique se a conta do Tableau Online tem permissões de administrador e tente novamente.

    ![Testar Conectividade no Tableau Online](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. Na caixa **Email de Notificação**, insira o endereço de email da pessoa ou grupo que deve receber as notificações do erro de provisionamento. Marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de Notificação do Tableau Online](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. Selecione **Salvar**.

10. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Tableau**.

    ![Sincronização de usuário no Tableau Online](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Examine os atributos de usuário que são sincronizados do Azure AD para o Tableau Online na seção **Mapeamentos de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário no Tableau Online para operações de atualização. Selecione **Salvar** para salvar as alterações.

    ![Atributos de usuário correspondentes no Tableau Online](./media/tableau-online-provisioning-tutorial/attribute.png)

12. Na seção **Mapeamentos**, selecione **Sincronizar Grupos do Azure Active Directory com o Tableau**.

    ![Sincronização de grupo no Tableau Online](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Examine os atributos de grupo que são sincronizados do Azure AD para o Tableau Online na seção **Mapeamentos de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário no Tableau Online para operações de atualização. Selecione **Salvar** para salvar as alterações.

    ![Atributos de grupo correspondentes no Tableau Online](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. Para configurar filtros de escopo, siga as instruções fornecidas no [tutorial sobre filtros de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para habilitar o serviço de provisionamento do Azure AD no Tableau Online, na seção **Configurações**, altere o **Status de Provisionamento** para **Ativado**.

    ![Status de Provisionamento do Tableau Online](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Defina os usuários ou os grupos que deseja provisionar no Tableau Online. Na seção **Configurações**, selecione os valores desejados em **Escopo**.

    ![Escopo do Tableau Online](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. Quando estiver pronto para fazer o provisionamento, selecione **Salvar**.

    ![Salvar no Tableau Online](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

Essa operação inicia a sincronização inicial de todos os usuários ou grupos definidos em **Escopo**, na seção **Configurações**. A sincronização inicial demora mais para ser executada do que as sincronizações posteriores. Elas ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure Active Directory seja executado. 

É possível usar a seção **Detalhes de Sincronização** para monitorar o andamento e seguir os links para o relatório de atividade de provisionamento. O relatório descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Tableau Online.

Para obter informações sobre como ler os logs de provisionamento do Azure AD, confira [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="change-log"></a>Log de alterações
* 30/09/2020 – Adicionado suporte para o atributo "authSetting" para Usuários.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar provisionamento de conta de usuário para aplicativos empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png