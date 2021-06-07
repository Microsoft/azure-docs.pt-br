---
title: 'Tutorial: Configurar o Zendesk para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no Zendesk.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.openlocfilehash: 620dd8fd586352ebeaf097a8f870a606f8e06c01
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94359707"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Tutorial: Configurar o Zendesk para provisionamento automático de usuário

Este tutorial demonstra as etapas a serem executadas no Zendesk e no Azure AD (Active Directory) a fim de configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e grupos no Zendesk.

> [!NOTE]
> Este tutorial descreve um conector baseado no serviço de provisionamento de usuário do Azure Active Directory. Para obter informações sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e o desprovisionamento de usuários para aplicativos SaaS (software como serviço) com o Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você possui:

* Um locatário do Azure AD.
* Um locatário do Zendesk com o plano Professional ou outro melhor habilitado.
* Uma conta de usuário no Zendesk com permissões de administrador.

## <a name="add-zendesk-from-the-azure-marketplace"></a>Adicionar o Zendesk por meio do Azure Marketplace

Antes de configurar o Zendesk para o provisionamento automático de usuário com o Azure AD, adicione o Zendesk por meio do Azure Marketplace à lista de aplicativos SaaS gerenciados.

Para adicionar o Zendesk por meio do Marketplace, siga estas etapas.

1. No [Portal do Microsoft Azure](https://portal.azure.com), no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O ícone do Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Zendesk** e selecione **Zendesk** no painel de resultados. Para adicionar o aplicativo, selecione **Adicionar**.

    ![Zendesk na lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>Atribuir usuários ao Zendesk

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários ou os grupos que foram atribuídos a um aplicativo no Azure Active Directory são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, decida quais usuários ou grupos do Azure AD precisam acessar o Zendesk. Para atribuir esses usuários ou grupos ao Zendesk, siga as instruções descritas em [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Dicas importantes para atribuir usuários ao Zendesk

* Hoje, as funções do Zendesk são preenchidas de maneira dinâmica e automática na interface do usuário do portal do Azure. Antes de atribuir funções do Zendesk aos usuários, verifique se uma sincronização inicial foi concluída no Zendesk para recuperar as funções mais recentes no seu locatário do Zendesk.

* Recomendamos que você atribua um só usuário do Azure AD ao Zendesk para testar sua configuração inicial de provisionamento automático de usuário. Você poderá atribuir usuários ou grupos adicionais posteriormente depois que os testes forem bem-sucedidos.

* Ao atribuir um usuário ao Zendesk, selecione qualquer função válida específica do aplicativo, se disponível, na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>Configurar o provisionamento automático de usuário no Zendesk 

Esta seção aborda as etapas para configurar o serviço de provisionamento do Azure Active Directory. Use-o para criar, atualizar e desabilitar usuários ou grupos no Zendesk com base em atribuições de usuário ou de grupo no Azure AD.

> [!TIP]
> Habilite também o logon único baseado em SAML para o Zendesk. Siga as instruções descritas no [tutorial de logon único do Zendesk](zendesk-tutorial.md). O logon único pode ser configurado de modo independente do provisionamento automático de usuário, embora os dois sejam complementares.

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Configurar o provisionamento automático de usuário para o Zendesk no Azure AD

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos empresariais** > **Todos os aplicativos** > **Zendesk**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Zendesk**.

    ![O link do Zendesk na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Provisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Modo de Provisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. Na seção **Credenciais de Administrador**, insira o nome de usuário administrador, o token secreto e o domínio da sua conta do Zendesk. Exemplos desses valores são:

   * Na caixa **Nome de Usuário Administrador**, preencha o nome de usuário da conta do administrador no locatário do Zendesk. Um exemplo é admin@contoso.com.

   * Na caixa **Token Secreto**, preencha o token secreto conforme descrito na Etapa 6.

   * Na caixa **Domínio**, preencha o subdomínio do seu locatário do Zendesk. Por exemplo, para uma conta com uma URL de locatário igual a `https://my-tenant.zendesk.com`, o subdomínio será **meu-locatário**.

6. O token secreto da sua conta do Zendesk está localizado em **Administrador** > **API** > **Configurações**. Verifique se o **Token de Acesso** está definido como **Habilitado**.

    ![Configurações do administrador do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Token secreto do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. Depois de preencher as caixas mostradas na Etapa 5, selecione **Testar Conectividade** para verificar se o Azure AD pode se conectar ao Zendesk. Se a conexão falhar, verifique se a sua conta do Zendesk tem permissões de administrador e tente novamente.

    ![Testar Conectividade do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. Na caixa **Email de Notificação**, insira o endereço de email da pessoa ou grupo que deve receber as notificações do erro de provisionamento. Marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de Notificação do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Selecione **Salvar**.

10. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Zendesk**.

    ![Sincronização de usuário do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Examine os atributos de usuário sincronizados do Azure AD para o Zendesk na seção **Mapeamentos de Atributos**. Os atributos selecionados como propriedades **Correspondentes** serão usados para corresponder as contas de usuário no Zendesk para operações de atualização. Selecione **Salvar** para salvar as alterações.

    ![Atributos de usuário correspondentes do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. Na seção **Mapeamentos**, selecione **Sincronizar Grupos do Azure Active Directory com o Zendesk**.

    ![Sincronização de grupo do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Examine os atributos de grupo que serão sincronizados do Azure AD para o Zendesk na seção **Mapeamentos de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder os grupos do Zendesk em operações de atualização. Selecione **Salvar** para salvar as alterações.

    ![Atributos de grupo correspondentes do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Para configurar filtros de escopo, siga as instruções fornecidas no [tutorial sobre filtros de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para habilitar o serviço de provisionamento do Azure AD no Zendesk, na seção **Configurações**, altere o **Status de Provisionamento** para **Ativado**.

    ![Status do Provisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Defina os usuários ou os grupos que deseja provisionar no Zendesk. Na seção **Configurações**, selecione os valores desejados em **Escopo**.

    ![Escopo do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Quando estiver pronto para fazer o provisionamento, selecione **Salvar**.

    ![Salvamento no Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

Essa operação inicia a sincronização inicial de todos os usuários ou grupos definidos em **Escopo**, na seção **Configurações**. A sincronização inicial demora mais para ser executada do que as sincronizações posteriores. Elas ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure Active Directory seja executado. 

É possível usar a seção **Detalhes de Sincronização** para monitorar o andamento e seguir os links para o relatório de atividade de provisionamento. O relatório descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Zendesk.

Para obter informações sobre como ler os logs de provisionamento do Azure AD, confira [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* O Zendesk dá suporte ao uso de grupos somente para usuários com as funções **Agente**. Para obter mais informações, confira a [documentação do Zendesk](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups).

* Quando uma função personalizada é atribuída a um usuário ou um grupo, o serviço de provisionamento automático de usuário do Azure AD também atribui a função padrão **Agente**. Somente Agentes podem ser atribuídos a uma função personalizada. Para obter mais informações, confira a [documentação da API do Zendesk](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests). 

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar provisionamento de conta de usuário para aplicativos empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
