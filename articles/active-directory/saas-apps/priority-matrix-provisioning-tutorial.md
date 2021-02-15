---
title: 'Tutorial: configurar a matriz de prioridade para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para a matriz de prioridade.
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
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357854"
---
# <a name="tutorial-configure-priority-matrix-for-automatic-user-provisioning"></a>Tutorial: configurar a matriz de prioridade para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas na matriz de prioridade e Azure Active Directory (AD do Azure) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para a matriz de prioridade.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário de matriz de prioridade](https://appfluence.com/pricing/)
* Uma conta de usuário em uma matriz de prioridade com permissões de administrador.

## <a name="assign-users-to-priority-matrix"></a>Atribuir usuários à matriz de prioridade

Azure Active Directory usa um conceito chamado atribuições para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso à matriz de prioridade. Depois de decidir, você pode atribuir esses usuários e/ou grupos à matriz de prioridade seguindo as instruções aqui:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-priority-matrix"></a>Dicas importantes para atribuir usuários à matriz de prioridade

* É recomendável que um único usuário do Azure AD seja atribuído à matriz de prioridade para testar a configuração automática de provisionamento de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário à matriz de prioridade, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="set-up-priority-matrix-for-provisioning"></a>Configurar a matriz de prioridade para provisionamento

Antes de configurar a matriz de prioridade para o provisionamento automático de usuário com o Azure AD, será necessário recuperar algumas informações de provisionamento da matriz de prioridade.

1. Entre em seu [console de administrador de matriz de prioridade](https://sync.appfluence.com/accounts/login/?next=/accounts/provisioning).

3. Clique em **token de logon OAuth** para matriz de prioridade

    ![Matriz de prioridade-adicionar SCIM](media/priority-matrix-provisioning-tutorial/oauthlogin.png)

4. Clique no botão **obter novo token** . Copie a **cadeia de caracteres do token**. Esse valor será inserido no campo **token secreto** na guia provisionamento do seu aplicativo de matriz de prioridade na portal do Azure. 

    ![Token de criação de matriz de prioridade](media/priority-matrix-provisioning-tutorial/token.png)

## <a name="add-priority-matrix-from-the-gallery"></a>Adicionar matriz de prioridade da Galeria

Para configurar a matriz de prioridade para o provisionamento automático de usuário com o Azure AD, você precisa adicionar a matriz de prioridade da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa** , em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **matriz de prioridade** , selecione **matriz de prioridade** no painel de resultados. 

    ![Matriz de prioridade na lista de resultados](common/search-new-app.png)

5. Selecione o botão **inscrever-se para a matriz de prioridade** , que o redirecionará para a página de logon da matriz de prioridade. 

    ![Adição de matriz de prioridade OIDC](media/priority-matrix-provisioning-tutorial/signup.png)

6. Como matriz de prioridade é um aplicativo OpenIDConnect, opte por fazer logon na matriz de prioridade usando sua conta corporativa da Microsoft.

    ![Logon OIDC de matriz de prioridade](media/priority-matrix-provisioning-tutorial/msftsignin.png)

7. Após uma autenticação bem-sucedida, aceite a solicitação de consentimento para a página de consentimento. O aplicativo será adicionado automaticamente ao seu locatário e você será redirecionado para sua conta de matriz de prioridade.

    ![Consentimento de OIDc de matriz de prioridade](media/priority-matrix-provisioning-tutorial/consent.png)

## <a name="configure-automatic-user-provisioning-to-priority-matrix"></a>Configurar o provisionamento automático de usuário para a matriz de prioridade 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos na matriz de prioridade com base em atribuições de usuário e/ou grupo no Azure AD.

> [!NOTE]
> Para saber mais sobre o ponto de extremidade SCIM da matriz de prioridade, consulte [provisionamento de usuário e matriz de prioridade](https://appfluence.com/help/article/user-provisioning/).

### <a name="to-configure-automatic-user-provisioning-for-priority-matrix-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para a matriz de prioridade no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **matriz de prioridade**.

    ![O link da matriz de prioridade na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções de gerenciamento com a opção de provisionamento chamada out.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa modo de provisionamento com a opção automática chamada out.](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira `https://sync.appfluence.com/scim/v2/` a **URL de locatário**. Insira o valor que você recuperou e salvou anteriormente da matriz de prioridade no **token secreto**. Clique em **testar conexão** para garantir que o Azure ad possa se conectar à matriz de prioridade. Se a conexão falhar, verifique se sua conta de matriz de prioridade tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email** , insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Save** (Salvar).

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários com a matriz de prioridade**.

    ![Mapeamentos de usuário da matriz de prioridade](media/priority-matrix-provisioning-tutorial/usermappings.png)

9. Examine os atributos de usuário que são sincronizados do Azure AD para a matriz de prioridade na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário na matriz de prioridade para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de usuário da matriz de prioridade](media/priority-matrix-provisioning-tutorial/userattributes.png)

10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento do Azure AD para a matriz de prioridade, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou grupos que você deseja provisionar para a matriz de prioridade escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure ad na matriz de prioridade.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)


