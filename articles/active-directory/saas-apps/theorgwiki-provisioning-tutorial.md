---
title: 'Tutorial: Configurar o TheOrgWiki para provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o TheOrgWiki.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 8238b9902aafcabc079c551a0eabc7170042209a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94357616"
---
# <a name="tutorial-configure-theorgwiki-for-automatic-user-provisioning"></a>Tutorial: Configurar o TheOrgWiki para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no TheOrgWiki e no Azure AD (Active Directory) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para o TheOrgWiki.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD.
* [Um locatário do OrgWiki](https://www.theorgwiki.com/welcome/).
* Uma conta de usuário no TheOrgWiki com permissões de administrador.

## <a name="assign-users-to-theorgwiki"></a>Atribuir usuários ao TheOrgWiki

O Azure Active Directory usa um conceito chamado atribuições para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou os grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Para configurar e habilitar o provisionamento automático de usuário, decida quais usuários e/ou grupos no Azure AD precisam de acesso ao TheOrgWiki. Depois de decidir, será possível atribuir esses usuários e/ou grupos ao TheOrgWiki seguindo estas instruções:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-theorgwiki"></a>Dicas importantes para atribuir usuários ao TheOrgWiki

* Recomendamos que somente um usuário do Azure AD seja atribuído ao TheOrgWiki para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao TheOrgWiki, é necessário selecionar qualquer função específica ao aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="set-up-theorgwiki-for-provisioning"></a>Configurar o TheOrgWiki para provisionamento

Antes de configurar o TheOrgWiki para o provisionamento automático de usuário com o Azure AD, você precisará habilitar o provisionamento do SCIM no TheOrgWiki.

1. Entre no [Console de Administração do TheOrgWiki](https://www.theorgwiki.com/login/). Clique em **Console de Administração**.

    ![Captura de tela do Org Wiki com o avatar do usuário e a opção Console de Administração destacada.](media/theorgwiki-provisioning-tutorial/login.png)

2. No Console de Administração, clique na **guia Configurações**. 

    ![Captura de tela do Console de Administração do The Org Wiki com a guia Configurações destacada.](media/theorgwiki-provisioning-tutorial/settings.png)
    
3. Navegue até **Contas de Serviço**.

    ![Captura de tela da página Contas de Serviço no Console de Administração do The Org Wiki.](media/theorgwiki-provisioning-tutorial/serviceaccount.png)

4. Clique em **+Conta de Serviço**. Em **Tipo de Conta de Serviço**, selecione **Baseada em Token**. Clique em **Salvar**.

    ![Captura de tela da caixa de diálogo Nova Conta de Serviço com as opções Tipo de Conta de Serviço, Baseada em Token e Salvar destacadas.](media/theorgwiki-provisioning-tutorial/auth.png)

5.  Copie os **Tokens Ativos**. Esse valor será inserido no campo Token Secreto na guia Provisionamento do aplicativo TheOrgWiki no portal do Azure.
     
    ![Captura de tela da caixa de diálogo Gerenciar Tokens para provisionamento do SCIM.](media/theorgwiki-provisioning-tutorial/token.png)

## <a name="add-theorgwiki-from-the-gallery"></a>Adicionar o TheOrgWiki da galeria

Antes de configurar o TheOrgWiki para o provisionamento automático de usuário com o Azure AD, é necessário adicionar o TheOrgWiki da galeria de aplicativos do Azure AD à lista de aplicativos SaaS gerenciados.

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **TheOrgWiki** e selecione **TheOrgWiki** no painel de resultados. 

    ![TheOrgWiki na lista de resultados](common/search-new-app.png)

5. Selecione o botão **Inscrever-se no TheOrgWiki**, que redirecionará você à página de logon do TheOrgWiki. 

    ![Captura de tela da página de logon do The Org Wiki da opção URL destacada](media/theorgwiki-provisioning-tutorial/image00.png)

6.  No canto superior direito, selecione **Logon**.

    ![Captura de tela do canto superior direito da página de logon com a opção Fazer Logon destacada.](media/theorgwiki-provisioning-tutorial/image02.png)

7. Como o TheOrgWiki é um aplicativo OpenIDConnect, opte por fazer logon no OrgWiki usando sua conta corporativa da Microsoft.

    ![Captura de tela da página de entrada do The Org Wiki com a opção Entrar com a conta da Microsoft destacada.](media/theorgwiki-provisioning-tutorial/image03.png)
    
8. Após a autenticação bem-sucedida, o aplicativo será adicionado automaticamente ao seu locatário e você será redirecionado à conta do TheOrgWiki.

    ![Adicionar SCIM do OrgWiki](media/theorgwiki-provisioning-tutorial/image04.png)

## <a name="configure-automatic-user-provisioning-to-theorgwiki"></a>Configurar o provisionamento automático de usuário para o TheOrgWiki 

Esta seção orienta você pelas etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no TheOrgWiki com base em atribuições de usuário e/ou grupo no Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-theorgwiki-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o TheOrgWiki no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **TheOrgWiki**.

    ![O link do OrgWiki na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções Gerenciar com a opção Provisionamento destacada.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa Modo de Provisionamento com a opção Automático destacada.](common/provisioning-automatic.png)

5. Na seção **Credenciais de Administrador**, insira `https://<TheOrgWiki Subdomain        value>.theorgwiki.com/api/v2/scim/v2/` em **URL do Locatário**. 

    Exemplo: `https://test1.theorgwiki.com/api/v2/scim/v2/`

> [!NOTE]
> O **Valor de Subdomínio** só pode ser definido durante o processo de inscrição inicial para o TheOrgWiki.
 
6. Insira o valor do token no campo **Token Secreto**, que você recuperou anteriormente do TheOrgWiki. Clique em **Testar Conectividade** para verificar se o Azure AD pode se conectar ao TheOrgWiki. Se a conexão falhar, verifique se a sua conta do TheOrgWiki tem permissões de Administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

7. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

8. Clique em **Save** (Salvar).

9. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o TheOrgWiki**.

    ![Mapeamentos de Usuário do TheOrgWiki](media/theorgwiki-provisioning-tutorial/usermapping.png)

10. Examine os atributos de usuário sincronizados do Azure AD com o TheOrgWiki na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das contas de usuário no TheOrgWiki para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de Usuário do TheOrgWiki](media/theorgwiki-provisioning-tutorial/userattribute.png).

11. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para habilitar o serviço de provisionamento do Azure AD no TheOrgWiki, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

13. Defina os usuários e/ou grupos que você gostaria de provisionar para o OrgWiki escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

14. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. A sincronização inicial demora mais para ser executada do que as posteriores. Para obter mais informações sobre o tempo necessário para o provisionamento de usuários e/ou grupos, confira [Quanto tempo levará para provisionar os usuários?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Use a seção **Status Atual** para monitorar o progresso e siga os links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no TheOrgWiki. Para obter mais informações, consulte [Verificar o status do provisionamento de usuário](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os logs de provisionamento do Azure AD, confira [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Como gerenciar o provisionamento de contas de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md).