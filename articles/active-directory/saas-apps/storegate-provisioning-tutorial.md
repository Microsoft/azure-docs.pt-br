---
title: 'Tutorial: Configurar o Storegate para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Storegate.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/15/2019
ms.author: Zhchia
ms.openlocfilehash: c984beff630ef90ea33a13e2fef1bca0189c2314
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94357925"
---
# <a name="tutorial-configure-storegate-for-automatic-user-provisioning"></a>Tutorial: Configurar o Storegate para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Storegate e no Azure AD (Active Directory) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para o Storegate.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do Storegate](https://www.storegate.com)
* Uma conta de usuário em um Storegate com permissões de administrador.

## <a name="assign-users-to-storegate"></a>Atribuir usuários ao Storegate

O Azure Active Directory usa um conceito chamado atribuições para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou os grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Para configurar e habilitar o provisionamento automático de usuário, decida quais usuários e/ou grupos no Azure AD precisam de acesso ao Storegate. Depois de decidir, você poderá atribuir esses usuários e/ou grupos ao Storegate seguindo estas instruções:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-storegate"></a>Dicas importantes para atribuir usuários ao Storegate

* Recomendamos que somente um usuário do Azure AD seja atribuído ao Storegate para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Storegate, você precisa selecionar qualquer função específica de aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="set-up-storegate-for-provisioning"></a>Configurar o Storegate para provisionamento

Antes de configurar o Storegate para o provisionamento automático de usuário com o Azure AD, será necessário recuperar algumas informações de provisionamento do Storegate.

1. Entre em seu [Console de Administração do Storegate](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) e navegue até as configurações clicando no ícone do usuário no canto superior direito e selecione **Configurações da Conta**.

    ![Adicionar SCIM do Storegate](media/storegate-provisioning-tutorial/admin.png)

2. Em configurações, navegue até **Equipe > Configurações** e verifique se o comutador de alternância está ativado na seção **Logon único**.

    ![Configurações do Storegate](media/storegate-provisioning-tutorial/team.png)

    ![Botão de alternância do Storegate](media/storegate-provisioning-tutorial/sso.png)

3. Copie a **URL do Locatário** e **Token**. Esses valores serão inseridos nos campos **URL do Locatário** e **Token Secreto**, respectivamente, na guia Provisionamento do aplicativo Storegate no portal do Azure. 

    ![Token de Criação do Storegate](media/storegate-provisioning-tutorial/token.png)

## <a name="add-storegate-from-the-gallery"></a>Adicionar o Storegate da galeria

Para configurar o Storegate para o provisionamento automático de usuário com o Azure AD, é necessário adicionar o Storegate da galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Storegate**, selecione **Storegate** no painel de resultados. 

    ![Storegate na lista de resultados](common/search-new-app.png)

5. Selecione o botão **Inscrever-se no Storegate**, que redirecionará você à página de logon do Storegate. 

    ![Adição do OIDC do Storegate](media/storegate-provisioning-tutorial/signup.png)

6.  Entre em seu [Console de Administração do Storegate](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) e navegue até as configurações clicando no ícone do usuário no canto superior direito e selecione **Configurações da Conta**.

    ![Logon do Storegate](media/storegate-provisioning-tutorial/admin.png)

7. Em configurações, navegue até **Equipe > Configurações** e clique no comutador de alternância na seção Logon único. Isso iniciará o fluxo de consentimento. Clique em **Ativar**.

    ![Equipe do Storegate](media/storegate-provisioning-tutorial/team.png)

    ![SSO do Storegate](media/storegate-provisioning-tutorial/sso.png)

    ![Ativação do Storegate](media/storegate-provisioning-tutorial/activate.png)

8. Como o Storegate é um aplicativo OpenIDConnect, opte por fazer logon no Storegate usando sua conta corporativa da Microsoft.

    ![Logon do OIDC do Storegate](media/storegate-provisioning-tutorial/login.png)

9. Após uma autenticação bem-sucedida, aceite a solicitação de consentimento da página de consentimento. O aplicativo será automaticamente adicionado ao seu locatário e você será redirecionado à sua conta do Storegate.

    ![Consentimento do OIDC do Storegate](media/storegate-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-storegate"></a>Configurar o provisionamento automático de usuário para o Storegate 

Esta seção orienta você pelas etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Storegate com base em atribuições de usuário e/ou grupo no Azure AD.

> [!NOTE]
> Para saber mais sobre o ponto de extremidade do SCIM do Storegate, confira [isto](https://en-support.storegate.com/article/step-by-step-instruction-how-to-enable-azure-provisioning-to-your-storegate-team-account/).

### <a name="to-configure-automatic-user-provisioning-for-storegate-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Storegate no Azure AD

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Storegate**.

    ![O link do Storegate na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções Gerenciar com a opção Provisionamento destacada.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa Modo de Provisionamento com a opção Automático destacada.](common/provisioning-automatic.png)

5. Na seção **Credenciais de Administrador**, insira `https://dialpad.com/scim` em **URL do Locatário**. Insira o valor que você recuperou e salvou anteriormente do Storegate no **Token Secreto**. Clique em **Testar Conectividade** para verificar se o Azure AD pode se conectar ao Storegate. Se a conexão falhar, verifique se a sua conta do Storegate tem permissões de Administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Save** (Salvar).

8. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Storegate**.

    ![Mapeamentos de Usuário do Storegate](media/storegate-provisioning-tutorial/usermappings.png)

9. Examine os atributos de usuário sincronizados do Azure AD com o Storegate na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das contas de usuário no Storegate para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de Usuário do Storegate](media/storegate-provisioning-tutorial/userattributes.png)

10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento do Azure AD para Storegate, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou grupos que você gostaria de provisionar para o Storegate escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Storegate.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
