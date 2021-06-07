---
title: 'Tutorial: Configurar o Priority Matrix para provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Priority Matrix.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/08/2019
ms.author: Zhchia
ms.openlocfilehash: e79f21300325c6b451dd564bf2c69830f003f55c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94357854"
---
# <a name="tutorial-configure-priority-matrix-for-automatic-user-provisioning"></a>Tutorial: Configurar o Priority Matrix para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Priority Matrix e no Azure AD (Azure Active Directory) a fim de configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no Priority Matrix.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do Priority Matrix](https://appfluence.com/pricing/)
* Uma conta de usuário no Priority Matrix com permissões de Administrador.

## <a name="assign-users-to-priority-matrix"></a>Atribuir usuários ao Priority Matrix

O Azure Active Directory usa um conceito chamado atribuições para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou os grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Para configurar e habilitar o provisionamento automático de usuário, decida quais usuários e/ou grupos no Azure AD precisam de acesso ao Priority Matrix. Depois de decidir isso, você poderá atribuir esses usuários e/ou grupos ao Priority Matrix seguindo estas instruções:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-priority-matrix"></a>Dicas importantes para atribuir usuários ao Priority Matrix

* Recomendamos que um só usuário do Azure AD seja atribuído ao Priority Matrix para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Priority Matrix, selecione qualquer função válida específica do aplicativo (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="set-up-priority-matrix-for-provisioning"></a>Configurar o Priority Matrix para provisionamento

Antes de configurar o Priority Matrix para o provisionamento automático de usuário com o Azure AD, você precisará habilitar o provisionamento do SCIM no Priority Matrix.

1. Entre no [Console de Administração do Priority Matrix](https://sync.appfluence.com/accounts/login/?next=/accounts/provisioning).

3. Clique em **Token de logon Oauth** para o Priority Matrix

    ![Adicionar SCIM do Priority Matrix](media/priority-matrix-provisioning-tutorial/oauthlogin.png)

4. Clique no botão **OBTER NOVO TOKEN**. Copie a **Cadeia de Caracteres do Token**. Esse valor será inserido no campo **Token Secreto** na guia Provisionamento do aplicativo Priority Matrix no portal do Azure. 

    ![Criar Token do Priority Matrix](media/priority-matrix-provisioning-tutorial/token.png)

## <a name="add-priority-matrix-from-the-gallery"></a>Adicionar o Priority Matrix da galeria

Para configurar o Priority Matrix para o provisionamento automático de usuário com o Azure AD, é necessário adicionar o Priority Matrix por meio da galeria de aplicativos do Azure AD à lista de aplicativos SaaS gerenciados.

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Priority Matrix**, selecione **Priority Matrix** no painel de resultados. 

    ![Priority Matrix na lista de resultados](common/search-new-app.png)

5. Selecione o botão **Inscrever-se no Priority Matrix**, que redirecionará você à página de logon do Priority Matrix. 

    ![Adicionar OIDC do Priority Matrix](media/priority-matrix-provisioning-tutorial/signup.png)

6. Como o Priority Matrix é um aplicativo OpenIDConnect, opte por fazer logon no Priority Matrix usando sua conta corporativa da Microsoft.

    ![Logon do OIDC do Priority Matrix](media/priority-matrix-provisioning-tutorial/msftsignin.png)

7. Após uma autenticação bem-sucedida, aceite a solicitação de consentimento da página de consentimento. O aplicativo será automaticamente adicionado ao seu locatário e você será redirecionado à sua conta do Priority Matrix.

    ![Consentimento do OIDC do Priority Matrix](media/priority-matrix-provisioning-tutorial/consent.png)

## <a name="configure-automatic-user-provisioning-to-priority-matrix"></a>Configurar o provisionamento automático de usuário para o Priority Matrix 

Esta seção descreve as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários ou grupos no Priority Matrix com base em atribuições de usuário ou grupo no Azure AD.

> [!NOTE]
> Para saber mais sobre o ponto de extremidade SCIM do Priority Matrix, confira [Provisionamento de usuário e Priority Matrix](https://appfluence.com/help/article/user-provisioning/).

### <a name="to-configure-automatic-user-provisioning-for-priority-matrix-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Priority Matrix no Azure AD, faça o seguinte:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Priority Matrix**.

    ![O link do Priority Matrix na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções Gerenciar com a opção Provisionamento destacada.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa Modo de Provisionamento com a opção Automático destacada.](common/provisioning-automatic.png)

5. Na seção **Credenciais de Administrador**, insira `https://sync.appfluence.com/scim/v2/` em **URL do Locatário**. Insira o valor que você recuperou e salvou anteriormente do Priority Matrix no **Token Secreto**. Clique em **Testar Conexão** para verificar se o Azure AD pode se conectar ao Priority Matrix. Se a conexão falhar, verifique se a sua conta do Priority Matrix tem permissões de Administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Save** (Salvar).

8. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Priority Matrix**.

    ![Mapeamentos de usuário do Priority Matrix](media/priority-matrix-provisioning-tutorial/usermappings.png)

9. Examine os atributos de usuário que serão sincronizados do Azure AD com o Priority Matrix na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das contas de usuário no Priority Matrix em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de usuário do Priority Matrix](media/priority-matrix-provisioning-tutorial/userattributes.png)

10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento do Azure AD para o Priority Matrix, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou os grupos que deseja provisionar no Priority Matrix escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Priority Matrix.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)


