---
title: 'Tutorial: Configurar o Zscaler One para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no Zscaler One.
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
ms.openlocfilehash: f6725045064b74079e00ca5bbe1d560f3b19f3ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97937120"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>Tutorial: Configurar o Zscaler One para o provisionamento automático de usuário

Este tutorial demonstra as etapas a serem executadas no Zscaler One e no Azure AD (Active Directory) a fim de configurar o Azure AD para provisionar e desprovisionar automaticamente usuários ou grupos no Zscaler One.

> [!NOTE]
> Este tutorial descreve um conector baseado no serviço de provisionamento de usuário do Azure Active Directory. Para obter informações sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e o desprovisionamento de usuários para aplicativos SaaS (software como serviço) com o Azure Active Directory](../app-provisioning/user-provisioning.md).


## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você possui:

* Um locatário do Azure AD.
* Um locatário do Zscaler One.
* Uma conta de usuário no Zscaler One com permissões de administrador.

> [!NOTE]
> A integração de provisionamento do Azure AD depende da API do SCIM no Zscaler One. Essa API está disponível para os desenvolvedores do Zscaler One nas contas com o pacote Enterprise.

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>Adicionar o Zscaler One por meio do Azure Marketplace

Antes de configurar o Zscaler One para o provisionamento automático de usuário com o Azure AD, adicione o Zscaler One por meio do Azure Marketplace à lista de aplicativos SaaS gerenciados.

Para adicionar o Zscaler One por meio do Azure Marketplace, siga estas etapas.

1. No [Portal do Microsoft Azure](https://portal.azure.com), no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O ícone do Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Zscaler One** e selecione **Zscaler One** no painel de resultados. Para adicionar o aplicativo, selecione **Adicionar**.

    ![Zscaler One na lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>Atribuir usuários ao Zscaler One

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários ou os grupos que foram atribuídos a um aplicativo no Azure Active Directory são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, decida quais usuários ou grupos do Azure AD precisam ter acesso ao Zscaler One. Para atribuir esses usuários ou grupos ao Zscaler One, siga as instruções descritas em [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>Dicas importantes para atribuir usuários ao Zscaler One

* Recomendamos que você atribua um só usuário do Azure AD ao Zscaler One para testar a configuração de provisionamento automático de usuário. Você pode atribuir usuários ou grupos adicionais mais tarde.

* Ao atribuir um usuário ao Zscaler One, selecione qualquer função válida específica do aplicativo (se disponível) na caixa de diálogo da atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>Configurar o provisionamento automático de usuário no Zscaler One

Esta seção aborda as etapas para configurar o serviço de provisionamento do Azure Active Directory. Use-o para criar, atualizar e desabilitar usuários ou grupos no Zscaler One com base em atribuições de usuário ou de grupo no Azure AD.

> [!TIP]
> Habilite também o logon único baseado em SAML para o Zscaler One. Siga as instruções descritas no [tutorial de logon único do Zscaler One](zscaler-One-tutorial.md). O logon único pode ser configurado de modo independente do provisionamento automático de usuário, embora os dois sejam complementares.

> [!NOTE]
> Quando os usuários e grupos são provisionados ou desprovisionados, é recomendável reiniciar periodicamente o provisionamento para garantir que as associações de grupo sejam atualizadas corretamente. A realização de uma reinicialização forçará nosso serviço a reavaliar todos os grupos e atualizar as associações.  

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Configurar o provisionamento automático de usuário para o Zscaler One no Azure AD

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos empresariais** > **Todos os aplicativos** > **Zscaler One**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Zscaler One**.

    ![O link do Zscaler One na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Provisionamento no Zscaler One](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Modo de Provisionamento no Zscaler One](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. Na seção **Credenciais de Administrador**, preencha as caixas **URL do Locatário** e **Token Secreto** com as configurações da sua conta do Zscaler One, conforme descrito na Etapa 6.

6. Para obter a URL do locatário e o token secreto, acesse **Administração** > **Configurações de Autenticação** na interface do usuário do portal do Zscaler One. Em **Tipo de Autenticação**, selecione **SAML**.

    ![Configurações de Autenticação do Zscaler One](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. Selecione **Configurar o SAML** para abrir as opções **Configurar o SAML**.

    ![Configurar o SAML no Zscaler One](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. Selecione **Habilitar o Provisionamento Baseado no SCIM** para obter as configurações de **URL Base** e **Token de Portador**. Depois, salve as configurações. Copie a configuração de **URL Base** para **URL do Locatário** no portal do Azure. Copie a configuração de **Token de Portador** para **Token Secreto** no portal do Azure.

7. Depois de preencher as caixas mostradas na Etapa 5, selecione **Testar Conectividade** para verificar se o Azure AD pode se conectar ao Zscaler One. Se a conexão falhar, verifique se a sua conta do Zscaler One tem permissões de administrador e tente novamente.

    ![Testar Conectividade no Zscaler One](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. Na caixa **Email de Notificação**, insira o endereço de email da pessoa ou grupo que deve receber as notificações do erro de provisionamento. Marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação no Zscaler One](./media/zscaler-one-provisioning-tutorial/notification.png)

9. Selecione **Salvar**.

10. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Zscaler One**.

    ![Sincronização de usuário no Zscaler One](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. Examine os atributos de usuário que serão sincronizados do Azure AD para o Zscaler One na seção **Mapeamentos de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das contas de usuário no Zscaler One em operações de atualização. Selecione **Salvar** para salvar as alterações.

    ![Atributos de usuário correspondentes no Zscaler One](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. Na seção **Mapeamentos**, selecione **Sincronizar Grupos do Azure Active Directory com o Zscaler One**.

    ![Sincronização de grupo no Zscaler One](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. Examine os atributos de grupo que serão sincronizados do Azure AD para o Zscaler One na seção **Mapeamentos de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência dos grupos no Zscaler One em operações de atualização. Selecione **Salvar** para salvar as alterações.

    ![Atributos de grupo correspondentes no Zscaler One](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. Para configurar filtros de escopo, siga as instruções fornecidas no [tutorial sobre filtros de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para habilitar o serviço de provisionamento do Azure AD no Zscaler One, na seção **Configurações**, altere o **Status de Provisionamento** para **Ativado**.

    ![Status de Provisionamento no Zscaler One](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Defina os usuários ou os grupos que deseja provisionar no Zscaler One. Na seção **Configurações**, selecione os valores desejados em **Escopo**.

    ![Escopo no Zscaler One](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. Quando estiver pronto para fazer o provisionamento, selecione **Salvar**.

    ![Salvamento no Zscaler One](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

Essa operação inicia a sincronização inicial de todos os usuários ou grupos definidos em **Escopo**, na seção **Configurações**. A sincronização inicial demora mais para ser executada do que as sincronizações posteriores. Elas ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure Active Directory seja executado. 

É possível usar a seção **Detalhes de Sincronização** para monitorar o andamento e seguir os links para o relatório de atividade de provisionamento. O relatório descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Zscaler One.

Para obter informações sobre como ler os logs de provisionamento do Azure AD, confira [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar provisionamento de conta de usuário para aplicativos empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png