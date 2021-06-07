---
title: 'Tutorial: Configurar o Zscaler para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no Zscaler.
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
ms.openlocfilehash: 9f368a4aebc4d5de38ebbab800241366650633e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97936576"
---
# <a name="tutorial-configure-zscaler-for-automatic-user-provisioning"></a>Tutorial: Configurar o Zscaler para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Zscaler e no Azure AD (Azure Active Directory) para configurar o Azure AD a fim de provisionar e desprovisionar automaticamente usuários e/ou grupos no Zscaler.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já possui o seguinte:

* Um locatário do Azure AD.
* Um locatário do Zscaler.
* Uma conta de usuário no Zscaler com permissões de administrador.

> [!NOTE]
> A integração de provisionamento do Azure AD depende da API do SCIM do Zscaler, que está disponível para desenvolvedores do Zscaler que têm contas com o pacote Enterprise.

## <a name="adding-zscaler-from-the-gallery"></a>Adicionar o Zscaler da galeria

Antes de configurar o Zscaler para o provisionamento automático de usuário com o Azure AD, é necessário adicionar o Zscaler da galeria de aplicativos do Azure AD à lista de aplicativos SaaS gerenciados.

**Para adicionar o Zscaler da galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Zscaler**, selecione **Zscaler** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Zscaler na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-zscaler"></a>Como atribuir usuários ao Zscaler

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Microsoft Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, é necessário decidir quais usuários e/ou grupos do Azure AD precisam acessar o Zscaler. Depois de decidir isso, você poderá atribuir esses usuários e/ou grupos ao Zscaler seguindo estas instruções:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zscaler"></a>Dicas importantes para atribuir usuários ao Zscaler

* Recomendamos que apenas um usuário do Azure AD seja atribuído ao Zscaler para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Zscaler, você deverá selecionar qualquer função válida específica do aplicativo (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-zscaler"></a>Configuração do provisionamento automático de usuário no Zscaler

Esta seção descreve as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Zscaler com base nas atribuições de usuário e/ou de grupo do Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o Zscaler seguindo as instruções fornecidas no [tutorial de logon único do Zscaler](zscaler-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

> [!NOTE]
> Quando os usuários e grupos são provisionados ou desprovisionados, é recomendável reiniciar periodicamente o provisionamento para garantir que as associações de grupo sejam atualizadas corretamente. A realização de uma reinicialização forçará nosso serviço a reavaliar todos os grupos e atualizar as associações. 

### <a name="to-configure-automatic-user-provisioning-for-zscaler-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Zscaler no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com) e selecione **Aplicativos Empresariais**, **Todos os aplicativos** e **Zscaler**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Zscaler**.

    ![O link do Zscaler na lista Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela do Zscaler – Provisionamento da barra lateral do Aplicativo Empresarial com a opção Provisionamento realçada.](./media/zscaler-provisioning-tutorial/provisioning-tab.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da página Provisionamento com o Modo de Provisionamento definido como Automático.](./media/zscaler-provisioning-tutorial/provisioning-credentials.png)

5. Na seção **Credenciais de Administrador**, insira a **URL do Locatário** e o **Token Secreto** da conta do Zscaler, conforme descrito na Etapa 6.

6. Para obter a **URL do Locatário** e o **Token Secreto**, navegue até **Administração > Configurações de Autenticação** na interface do usuário do portal do Zscaler e clique em **SAML** em **Tipo de Autenticação**.

    ![Captura de tela da página Configurações de Autenticação.](./media/zscaler-provisioning-tutorial/secret-token-1.png)

    Clique em **Configurar o SAML** para abrir as opções de **Configuração do SAML**.

    ![Captura de tela da caixa de diálogo Configurar o SAML com as caixas de texto URL de Base e Token de Portador destacadas.](./media/zscaler-provisioning-tutorial/secret-token-2.png)

    Selecione **Habilitar o Provisionamento Baseado em SCIM** para recuperar a **URL de Base** e o **Token de Portador**, então salve as configurações. Copie a **URL de Base** para a **URL do Locatário** e o **Token de Portador** para o **Token Secreto** no portal do Azure.

7. Quando preencher os campos mostrados na Etapa 5, clique em **Testar Conexão** para verificar se o Azure AD pode se conectar ao Zscaler. Se a conexão falhar, verifique se a conta do Zscaler tem permissões de administrador e tente novamente.

    ![Captura de tela da seção Credenciais de Administrador com a opção Testar Conectividade destacada.](./media/zscaler-provisioning-tutorial/test-connection.png)

8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Captura de tela da caixa de texto Email de Notificação.](./media/zscaler-provisioning-tutorial/notification.png)

9. Clique em **Salvar**.

10. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Zscaler**.

    ![Captura de tela da seção Mapeamentos com a opção Sincronizar Usuários do Azure Active Directory com o Zscaler realçada.](./media/zscaler-provisioning-tutorial/user-mappings.png)

11. Examine os atributos de usuário que serão sincronizados entre o Azure AD e o Zscaler na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das contas de usuário no Zscaler em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Captura de tela da seção Mapeamentos de Atributos com sete mapeamentos exibidos.](./media/zscaler-provisioning-tutorial/user-attribute-mappings.png)

12. Na seção **Mapeamentos**, selecione **Sincronizar Grupos do Azure Active Directory com o Zscaler**.

    ![Captura de tela da seção Mapeamentos com a opção Sincronizar Grupos do Azure Active Directory com o Zscaler realçada.](./media/zscaler-provisioning-tutorial/group-mappings.png)

13. Examine os atributos de grupo que serão sincronizados do Azure AD para o Zscaler na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência dos grupos no Zscaler em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Captura de tela da seção Mapeamentos de Atributos com três mapeamentos exibidos.](./media/zscaler-provisioning-tutorial/group-attribute-mappings.png)

14. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para habilitar o serviço de provisionamento do Azure AD no Zscaler, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Captura de tela da opção Status de Provisionamento definida como Ativado.](./media/zscaler-provisioning-tutorial/provisioning-status.png)

16. Defina os usuários e/ou os grupos que você deseja provisionar no Zscaler escolhendo os valores desejados em **Escopo**, na seção **Configurações**.

    ![Captura de tela da configuração Escopo com a opção Sincronizar apenas usuários e grupos atribuídos realçada.](./media/zscaler-provisioning-tutorial/scoping.png)

17. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Captura de tela do Zscaler – Provisionamento da barra lateral do Aplicativo Empresarial com a opção Salvar destacada.](./media/zscaler-provisioning-tutorial/save-provisioning.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Zscaler.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-provisioning-tutorial/tutorial-general-03.png