---
title: 'Tutorial: Configurar o Harness para provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no Harness.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: 13ae960f5d259314f00f8f09b2999a36c0919bc5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94353706"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>Tutorial: Configurar o Harness para provisionamento automático de usuário

Neste artigo, você vai aprender a configurar o Azure AD (Azure Active Directory) para provisionar e desprovisionar automaticamente usuários ou grupos no Harness.

> [!NOTE]
> Este artigo descreve um conector baseado no serviço de provisionamento de usuário do Azure AD. Para obter informações importantes sobre esse serviço e respostas a perguntas frequentes, confira [Automatizar o provisionamento e o desprovisionamento de usuário em aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste artigo pressupõe que você já tenha os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do Harness](https://harness.io/pricing/)
* Uma conta de usuário no Harness com permissões de *administrador*

## <a name="assign-users-to-harness"></a>Atribuir usuários ao Harness

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários ou os grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Para configurar e habilitar o provisionamento automático de usuário, decida quais usuários ou grupos no Azure AD precisam acessar o Harness. Depois disso, você pode atribuir esses usuários ou grupos ao Harness seguindo a instruções em [Atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-harness"></a>Dicas importantes para atribuir usuários ao Harness

* Recomendamos que você atribua apenas um usuário do Azure AD ao Harness para testar a configuração do provisionamento automático de usuário. Usuários ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Harness, selecione qualquer função válida específica do aplicativo (se disponível) na caixa de diálogo **Atribuição**. Usuários com a função *Acesso padrão* são excluídos do provisionamento.

## <a name="set-up-harness-for-provisioning"></a>Configurar o Harness para provisionamento

1. Entre no [Console de Administração do Harness](https://app.harness.io/#/login) e acesse **Segurança Contínua** > **Gerenciamento de Acesso**.

    ![Console de Administração do Harness](media/harness-provisioning-tutorial/admin.png)

1. Selecione **Chaves de API**.

    ![Link Chaves de API do Harness](media/harness-provisioning-tutorial/apikeys.png)

1. Selecione **Adicionar Chave de API**. 

    ![Link Adicionar Chave de API do Harness](media/harness-provisioning-tutorial/addkey.png)

1. No painel **Adicionar Chave de API**, faça o seguinte:

    ![Painel Adicionar Chave de API do Harness](media/harness-provisioning-tutorial/title.png)
   
   a. Na caixa **Nome**, forneça um nome para a chave.  
   b. Na lista suspensa **Permissões Herdadas de**, selecione uma opção. 
   
1. Selecione **Enviar**.

1. Copie a **Chave** para uso posterior neste tutorial.

    ![Criar Token do Harness](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>Adicionar o Harness da galeria

Para configurar o Harness para o provisionamento automático de usuário com o Azure AD, adicione o Harness por meio da galeria de aplicativos do Azure AD à lista de aplicativos SaaS gerenciados.

1. No [Portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**.

    ![Botão "Azure Active Directory"](common/select-azuread.png)

1. Selecione **Aplicativos empresariais** > **Todos os aplicativos**.

    ![O link "Todos os aplicativos"](common/enterprise-applications.png)

1. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão “Novo aplicativo”](common/add-new-app.png)

1. Na caixa de pesquisa, insira **Harness**, selecione **Harness** na lista de resultados e, depois, selecione o botão **Adicionar** para adicionar o aplicativo.

    ![Harness na lista de resultados](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-harness"></a>Configurar o provisionamento automático de usuário para o Harness 

Esta seção descreve as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários ou grupos no Harness com base em atribuições de usuário ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o Harness seguindo as instruções no [tutorial de logon único do Harness](./harness-tutorial.md). Você pode configurar o logon único de maneira independente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

> [!NOTE]
> Para saber mais sobre o ponto de extremidade SCIM do Harness, confira o artigo [Chaves de API](https://docs.harness.io/article/smloyragsm-api-keys) do Harness.

Para configurar o provisionamento automático de usuário para o Harness no Azure AD, faça o seguinte:

1. No [portal do Azure](https://portal.azure.com), selecione **Aplicativos Empresariais** > **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

1. Na lista de aplicativos, selecione **Harness**.

    ![O link do Harness na lista de aplicativos](common/all-applications.png)

1. Selecione **Provisionamento**.

    ![O botão Provisionamento](common/provisioning.png)

1. Na lista suspensa **Modo de Provisionamento**, selecione **Automático**.

    ![A lista suspensa "Modo de Provisionamento"](common/provisioning-automatic.png)

1. Em **Credenciais de Administrador**, faça o seguinte:

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)
 
   a. Na caixa **URL do Locatário**, insira **`https://app.harness.io/gateway/api/scim/account/<your_harness_account_ID>`** . Você poderá obter a ID da conta do Harness na URL no seu navegador quando estiver conectado ao Harness.
   b. Na caixa **Token Secreto**, insira o valor do token de autenticação SCIM que você salvou na etapa 6 da seção "Configurar o Harness para provisionamento".  
   c. Selecione **Testar Conexão** para verificar se o Azure AD pode se conectar ao Harness. Se a conexão falhar, verifique se a conta do Harness tem permissões de *Administrador* e tente novamente.

1. Na caixa **Notificação por Email**, insira o endereço de email de uma pessoa ou de um grupo que deverá receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![A caixa "Notificação por Email"](common/provisioning-notification-email.png)

1. Clique em **Salvar**.

1. Em **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Harness**.

    ![Link do Harness "Sincronizar os Usuários do Azure Active Directory com o Harness"](media/harness-provisioning-tutorial/usermappings.png)

1. Em **Mapeamentos de Atributos**, examine os atributos de usuário que serão sincronizados do Azure AD com o Harness. Os atributos selecionados como *Correspondentes* são usados para fazer a correspondência entre as contas de usuário no Harness em operações de atualização. Para confirmar eventuais alterações, selecione **Salvar**.

    ![Painel "Mapeamentos de Atributo" do usuário do Harness](media/harness-provisioning-tutorial/userattributes.png)

1. Em **Mapeamentos**, selecione **Sincronizar Grupos do Azure Active Directory com o Harness**.

    ![Link do Harness "Sincronizar os Grupos do Azure Active Directory com o Harness"](media/harness-provisioning-tutorial/groupmappings.png)

1. Em **Mapeamentos de Atributos**, examine os atributos de grupo que serão sincronizados do Azure AD com o Harness. Os atributos selecionados como propriedades *Correspondentes* são usados para fazer a correspondência dos grupos no Harness em operações de atualização. Para confirmar eventuais alterações, selecione **Salvar**.

    ![Painel "Mapeamentos de Atributo" do grupo do Harness](media/harness-provisioning-tutorial/groupattributes.png)

1. Para configurar filtros de escopo, confira [Provisionamento de aplicativos com base em atributo com filtros de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Em **Configurações**, para habilitar o serviço de provisionamento do Azure AD para o Harness, alterne a opção **Status de Provisionamento** para **Ativado**.

    ![Comutador do status de provisionamento definido como "Ativado"](common/provisioning-toggle-on.png)

1. Em **Configurações**, na lista suspensa **Escopo**, selecione como deseja sincronizar os usuários ou grupos que você está provisionando para o Harness.

    ![Escopo de provisionamento](common/provisioning-scope.png)

1. Quando estiver pronto para fazer o provisionamento, selecione **Salvar**.

    ![O botão Salvar do provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial dos usuários ou grupos que você está provisionando. A sincronização inicial demora mais para ser executada do que as posteriores. As sincronizações ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Para monitorar o progresso, acesse a seção **Detalhes de Sincronização**. Você também pode seguir os links para um relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Harness.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, confira [Relatório sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar provisionamento de conta de usuário para aplicativos empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)