---
title: 'Tutorial: Configurar o Zscaler Three para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Neste tutorial, você aprenderá a configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no Zscaler Three.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 385a1153-0f47-4e41-8f44-da1b49d7629e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 43ae028e57578634c34c69357a264fdb180b8a1f
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68515370"
---
# <a name="tutorial-configure-zscaler-three-for-automatic-user-provisioning"></a>Tutorial: Configurar o Zscaler Three para o provisionamento automático de usuário

Neste tutorial, você aprenderá a configurar o Azure AD (Azure Active Directory) para provisionar e desprovisionar automaticamente usuários e/ou grupos no Zscaler Three.

> [!NOTE]
> Este tutorial descreve um conector baseado no provisionamento de usuário do Azure AD. Para obter detalhes importantes sobre o que esse serviço faz e como ele funciona, bem como respostas a perguntas frequentes, confira [Automatizar o provisionamento e o desprovisionamento de usuário em aplicativos SaaS com o Azure Active Directory](../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas deste tutorial, você precisará do seguinte:

* Um locatário do Azure AD.
* Um locatário do Zscaler Three.
* Uma conta de usuário no Zscaler Three com permissões de administrador.

> [!NOTE]
> A integração de provisionamento do Azure AD depende da API do SCIM do Zscaler ZSCloud, que está disponível para contas empresariais.

## <a name="adding-zscaler-three-from-the-gallery"></a>Adicionando Zscaler Três da Galeria

Antes de configurar o Zscaler Three para o provisionamento automático de usuário com o Azure AD, é necessário adicionar o Zscaler Three por meio da galeria de aplicativos do Azure AD à lista de aplicativos SaaS gerenciados.

No [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**:

![Selecione Azure Active Directory](common/select-azuread.png)

Acesse **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**:

![Aplicativos empresariais](common/enterprise-applications.png)

Para adicionar um aplicativo, selecione **Novo aplicativo** na parte superior da janela:

![Selecionar Novo aplicativo](common/add-new-app.png)

Na caixa de pesquisa, insira **Zscaler Three**. Selecione **Zscaler Three** nos resultados e, em seguida, selecione **Adicionar**.

![Lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-zscaler-three"></a>Atribuir usuários ao Zscaler Three

Os usuários do Azure AD precisam ter acesso aos aplicativos selecionados para usá-los. No contexto do provisionamento automático de usuário, somente os usuários ou os grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deverá decidir quais usuários e/ou grupos do Azure AD precisam acessar o Zscaler Three. Depois de decidir isso, você poderá atribuir esses usuários e grupos ao Zscaler Three seguindo as instruções descritas em [Atribuir um usuário ou um grupo a um aplicativo empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-three"></a>Dicas importantes para atribuir usuários ao Zscaler Three

* Recomendamos que você primeiro atribua um único usuário do Azure AD ao Zscaler Three para testar a configuração de provisionamento automático de usuário. Você poderá atribuir mais usuários e grupos posteriormente.

* Ao atribuir um usuário ao Zscaler Three, você precisará selecionar qualquer função válida específica do aplicativo (se disponível) na caixa de diálogo da atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="set-up-automatic-user-provisioning"></a>Configurar o provisionamento automático de usuário

Esta seção descreve as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e grupos no Zscaler Three, com base nas atribuições de usuário e grupo no Azure AD.

> [!TIP]
> Talvez você também deseje habilitar o logon único baseado no SAML para o Zscaler Three. Se você fizer isso, siga as instruções descritas no [tutorial de logon único do Zscaler Three](zscaler-three-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora os dois recursos sejam complementares.

1. Entre no [portal do Azure](https://portal.azure.com) e selecione **Aplicativos empresariais** > **Todos os aplicativos** > **Zscaler Three**:

    ![Aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Zscaler Three**:

    ![Lista Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**:

    ![Provisionamento do Zscaler Three](./media/zscaler-three-provisioning-tutorial/provisioning-tab.png)

4. Defina o **Modo de Provisionamento** como **Automático**:

    ![Definir o modo de provisionamento](./media/zscaler-three-provisioning-tutorial/provisioning-credentials.png)

5. Na seção **Credenciais de Administrador**, insira a **URL do Locatário** e o **Token Secreto** da conta do Zscaler Three, conforme descrito na próxima etapa.

6. Para obter a **URL do Locatário** e o **Token Secreto**, acesse **Administração** > **Configurações de Autenticação** no portal do Zscaler Three e selecione **SAML** em **Tipo de Autenticação**:

    ![Configurações de autenticação do Zscaler Three](./media/zscaler-three-provisioning-tutorial/secret-token-1.png)

    Selecione **Configurar o SAML** para abrir a janela **Configurar o SAML**:

    ![Configurar a janela do SAML](./media/zscaler-three-provisioning-tutorial/secret-token-2.png)

    Selecione **Habilitar o Provisionamento Baseado no SCIM**, copie a **URL Base** e o **Token de Portador** e, em seguida, salve as configurações. No portal do Azure, cole a **URL Base** na caixa **URL do Locatário** e o **Token de Portador** na caixa **Token Secreto**.

7. Depois de inserir os valores nas caixas **URL do Locatário** e **Token Secreto**, selecione **Testar Conectividade** para garantir que o Azure AD possa se conectar ao Zscaler Three. Se a conexão falhar, verifique se a sua conta do Zscaler Three tem permissões de administrador e tente novamente.

    ![Testar a conexão](./media/zscaler-three-provisioning-tutorial/test-connection.png)

8. Na caixa **Email de Notificação**, insira o endereço de email de uma pessoa ou um grupo que deve receber as notificações do erro de provisionamento. Selecione **Enviar uma notificação por email quando ocorrer uma falha**:

    ![Configurar o email de notificação](./media/zscaler-three-provisioning-tutorial/notification.png)

9. Clique em **Salvar**.

10. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Zscaler Three**:

    ![Sincronizar usuários do Azure AD](./media/zscaler-three-provisioning-tutorial/user-mappings.png)

11. Examine os atributos de usuário que são sincronizados do Azure AD para o Zscaler Three na seção **Mapeamentos de Atributo**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das contas de usuário no Zscaler Three em operações de atualização. Para confirmar eventuais alterações, selecione **Salvar**.

    ![Mapeamentos de atributo](./media/zscaler-three-provisioning-tutorial/user-attribute-mappings.png)

12. Na seção **Mapeamentos**, selecione **Sincronizar Grupos do Azure Active Directory com o Zscaler Three**:

    ![Sincronizar grupos do Azure AD](./media/zscaler-three-provisioning-tutorial/group-mappings.png)

13. Examine os atributos de grupo que são sincronizados do Azure AD para o Zscaler Three na seção **Mapeamentos de Atributo**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência dos grupos no Zscaler Three em operações de atualização. Para confirmar eventuais alterações, selecione **Salvar**.

    ![Mapeamentos de atributo](./media/zscaler-three-provisioning-tutorial/group-attribute-mappings.png)

14. Para configurar filtros de escopo, veja as instruções fornecidas no [tutorial sobre filtros de escopo](./../active-directory-saas-scoping-filters.md).

15. Para habilitar o serviço de provisionamento do Azure AD no Zscaler Three, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**:

    ![Status de provisionamento](./media/zscaler-three-provisioning-tutorial/provisioning-status.png)

16. Defina os usuários e/ou os grupos que deseja provisionar para o Zscaler Three escolhendo os valores desejados em **Escopo** na seção **Configurações**:

    ![Valores de escopo](./media/zscaler-three-provisioning-tutorial/scoping.png)

17. Quando estiver pronto para fazer o provisionamento, selecione **Salvar**:

    ![Selecionar Salvar](./media/zscaler-three-provisioning-tutorial/save-provisioning.png)

Essa operação inicia a sincronização inicial de todos os usuários e grupos definidos em **Escopo** na seção **Configurações**. A sincronização inicial leva mais tempo do que as sincronizações seguintes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Monitore o progresso na seção **Detalhes de Sincronização**. Você também pode seguir os links para um relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Zscaler Three.

Para obter informações sobre como ler os logs de provisionamento do Azure AD, confira [Relatórios sobre o provisionamento automático de contas de usuário](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-03.png
