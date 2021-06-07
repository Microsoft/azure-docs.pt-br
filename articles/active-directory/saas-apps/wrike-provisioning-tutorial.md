---
title: 'Tutorial: Configurar o Wrike para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no Wrike.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: Zhchia
ms.openlocfilehash: 53b1db1a8c4da59055c0af5f448fa0c8a6933daf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95988086"
---
# <a name="tutorial-configure-wrike-for-automatic-user-provisioning"></a>Tutorial: Configurar o Wrike para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Wrike e no Azure AD (Azure Active Directory) a fim de configurar o Azure AD para provisionar e desprovisionar automaticamente usuários ou grupos no Wrike.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para ver detalhes importantes sobre o que o serviço faz, como ele funciona e as perguntas frequentes, confira [Automatizar o provisionamento e o desprovisionamento de usuário para aplicativos SaaS (software como serviço) com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para saber mais sobre os termos de uso gerais do Microsoft Azure para os recursos em versão prévia, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do Wrike](https://www.wrike.com/price/)
* Uma conta de usuário no Wrike com permissões de administrador

## <a name="assign-users-to-wrike"></a>Atribuir usuários ao Wrike
O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários ou os grupos que foram atribuídos a um aplicativo no Azure Active Directory são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, decida quais usuários ou grupos no Azure AD precisam acessar o Wrike. Depois, atribua esses usuários ou grupos ao Wrike seguindo estas instruções:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-wrike"></a>Dicas importantes para atribuir usuários ao Wrike

* Recomendamos que você atribua apenas um usuário do Azure AD ao Wrike para testar a configuração do provisionamento automático de usuário. Usuários ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Wrike, selecione qualquer função válida específica do aplicativo (se disponível) na caixa de diálogo de atribuição. Usuários com a função Acesso padrão são excluídos do provisionamento.

## <a name="set-up-wrike-for-provisioning"></a>Configurar o Wrike para o provisionamento

Antes de configurar o Wrike para o provisionamento automático de usuário com o Azure AD, habilite o provisionamento do SCIM (Sistema de Gerenciamento de Usuários entre Domínios) no Wrike.

1. Entre no [console de administração do Wrike](https://www.Wrike.com/login/). Acesse sua ID do Locatário. Selecione **Aplicativos e Integrações**.

    ![Aplicativos e Integrações](media/Wrike-provisioning-tutorial/admin.png)

2.  Acesse o **Azure AD** e selecione-o.

    ![Azure AD](media/Wrike-provisioning-tutorial/Capture01.png)

3.  Selecione o SCIM. Copie a **URL Base**.

    ![URL base](media/Wrike-provisioning-tutorial/Wrike-tenanturl.png)

4. Selecione **API** > **SCIM do Azure**.

    ![SCIM do Azure](media/Wrike-provisioning-tutorial/Wrike-add-scim.png)

5.  Um pop-up será aberto. Insira a mesma senha que você criou anteriormente para criar uma conta.

    ![Criar token no Wrike](media/Wrike-provisioning-tutorial/password.png)

6.  Copie o **Token Secreto** e cole-o no Azure AD. Selecione **Salvar** para concluir a configuração de provisionamento no Wrike.

    ![Token de acesso permanente](media/Wrike-provisioning-tutorial/Wrike-create-token.png)


## <a name="add-wrike-from-the-gallery"></a>Adicionar o Wrike por meio da galeria

Antes de configurar o Wrike para o provisionamento automático de usuário com o Azure AD, adicione o Wrike por meio da galeria de aplicativos do Azure AD à lista de aplicativos SaaS gerenciados.

Para adicionar o Wrike por meio da galeria de aplicativos do Azure AD, siga estas etapas.

1. No [Portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Wrike**, selecione **Wrike** no painel de resultados e escolha **Adicionar** para adicionar o aplicativo.

    ![Wrike na lista de resultados](common/search-new-app.png)


## <a name="configure-automatic-user-provisioning-to-wrike"></a>Configurar o provisionamento automático de usuário no Wrike 

Esta seção descreve as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários ou grupos no Wrike com base em atribuições de usuário ou grupo no Azure AD.

> [!TIP]
> Para habilitar o logon único baseado em SAML no Wrike, siga as instruções no [tutorial de logon único do Wrike](wrike-tutorial.md). O logon único pode ser configurado de modo independente do provisionamento automático de usuário, embora os dois sejam complementares.

### <a name="configure-automatic-user-provisioning-for-wrike-in-azure-ad"></a>Configurar o provisionamento automático de usuário para o Wrike no Azure AD

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** > **Todos os aplicativos**.

    ![Todos os aplicativos](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Wrike**.

    ![O link do Wrike na lista Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia Provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Modo de Provisionamento definido como Automático](common/provisioning-automatic.png)

5. Na seção Credenciais de Administrador, insira os valores de **URL Base** e **Token de acesso permanente** recuperados anteriormente em **URL do Locatário** e **Token Secreto**, respectivamente. Selecione **Testar Conectividade** para verificar se o Azure AD pode se conectar ao Wrike. Se a conexão falhar, verifique se a sua conta do Wrike tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

7. Na caixa **Email de Notificação**, insira o endereço de email de uma pessoa ou de um grupo que deve receber as notificações de erro de provisionamento. Marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Notificação por email](common/provisioning-notification-email.png)

8. Selecione **Salvar**.

9. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Wrike**.

    ![Mapeamentos de usuário do Wrike](media/Wrike-provisioning-tutorial/Wrike-user-mappings.png)

10. Examine os atributos de usuário que serão sincronizados do Azure AD para o Wrike na seção **Mapeamentos de Atributo**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das contas de usuário no Wrike em operações de atualização. Para confirmar eventuais alterações, selecione **Salvar**.

    ![Atributos de usuário do Wrike](media/Wrike-provisioning-tutorial/Wrike-user-attributes.png)

11. Para configurar filtros de escopo, siga as instruções no [Tutorial sobre filtros de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para habilitar o serviço de provisionamento do Azure AD no Wrike, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status de Provisionamento Ativado](common/provisioning-toggle-on.png)

13. Defina os usuários ou os grupos que você quer provisionar no Wrike escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

14. Quando estiver pronto para fazer o provisionamento, selecione **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários ou grupos definidos em **Escopo**, na seção **Configurações**. A sincronização inicial demora mais para ser executada do que as posteriores. Para saber mais sobre quanto tempo leva o provisionamento de usuários ou grupos, confira [Quanto tempo levará para provisionar os usuários?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Use a seção **Status Atual** para monitorar o progresso e siga os links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Wrike. Para obter mais informações, consulte [Verificar o status do provisionamento de usuário](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os logs de provisionamento do Azure AD, confira [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar provisionamento de conta de usuário para aplicativos empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)