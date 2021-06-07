---
title: 'Tutorial: Configurar o Foodee para provisionamento automático do usuário usando o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no Foodee.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: 8b4bfa7e9bf457d79c6c4a0b5255bce4fe36dff4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358670"
---
# <a name="tutorial-configure-foodee-for-automatic-user-provisioning"></a>Tutorial: Configurar o Foodee para provisionamento automático do usuário

Este artigo mostra como configurar o Azure AD (Active Directory) no Foodee e no Azure AD para provisionar e desprovisionar automaticamente usuários ou grupos no Foodee.

> [!NOTE]
> Este artigo descreve um conector baseado no serviço de Provisionamento de Usuário do Azure AD. Para saber o que esse serviço faz e como ele funciona, além de ver respostas para perguntas frequentes, confira [Automatizar o provisionamento e o desprovisionamento de usuário em aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia. Para saber mais sobre do recurso Termos de uso do Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial presume que você atende aos seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do Foodee](https://www.food.ee/about/)
* Uma conta de usuário no Foodee com permissões de administrador

## <a name="assign-users-to-foodee"></a>Atribuir usuários ao Foodee 

O Azure AD usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários ou os grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deverá decidir quais usuários ou grupos do Azure AD precisam acessar o Foodee. Depois de fazer isso, você pode atribuir esses usuários ou grupos ao Foodee seguindo as instruções em [Atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-foodee"></a>Dicas importantes para atribuir usuários ao Foodee 

Lembre-se das seguintes dicas ao atribuir usuários:

* Recomendamos que você atribua apenas um usuário do Azure AD ao Foodee para testar a configuração do provisionamento automático de usuário. Você pode atribuir usuários ou grupos adicionais mais tarde.

* Ao atribuir um usuário ao Foodee, selecione qualquer função válida específica do aplicativo, se disponível, no painel **Atribuição**. Usuários que têm a função *Acesso padrão* são excluídos do provisionamento.

## <a name="set-up-foodee-for-provisioning"></a>Configurar o Foodee para provisionamento

Antes de configurar o Foodee para o provisionamento automático de usuário com o Azure AD, habilite o provisionamento do SCIM (Sistema de Gerenciamento de Usuários entre Domínios) nele.

1. Entre no [Foodee](https://www.food.ee/login/) e selecione sua ID de locatário.

    :::image type="content" source="media/Foodee-provisioning-tutorial/tenant.png" alt-text="Captura de tela do menu principal do portal empresarial do Foodee. Um espaço reservado de ID de locatário está visível no menu." border="false":::

1. No **Enterprise Portal**, selecione **Logon Único**.

    ![Menu do painel esquerdo do Enterprise Portal do Foodee](media/Foodee-provisioning-tutorial/scim.png)

1. Copie o valor na caixa **Token da API** para uso posterior. Insira-o na caixa **Token Secreto** na guia **Provisionamento** do aplicativo Foodee no portal do Azure.

    :::image type="content" source="media/Foodee-provisioning-tutorial/token.png" alt-text="Captura de tela de uma página no Enterprise Portal do Foodee. Um valor de token de API está realçado." border="false":::

## <a name="add-foodee-from-the-gallery"></a>Adicionar o Foodee por meio da galeria

Para configurar o Foodee para o provisionamento automático de usuário com o Azure AD, é necessário adicioná-lo por meio da galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

Faça o seguinte para adicionar o Foodee por meio da galeria de aplicativos do Azure AD:

1. No [Portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**.

    ![O comando do Azure Active Directory](common/select-azuread.png)

1. Selecione **Aplicativos empresariais** > **Todos os aplicativos**.

    ![O painel Aplicativos Empresariais](common/enterprise-applications.png)

1. Para adicionar um novo aplicativo, selecione **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

1. Na caixa de pesquisa, insira **Foodee**, selecione o **Foodee** no painel de resultados e selecione **Adicionar** para adicionar o aplicativo.

    ![Foodee na lista de resultados](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-foodee"></a>Configurar o provisionamento automático de usuário no Foodee 

Nesta seção, você configura o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários ou grupos no Foodee com base nas atribuições dos usuários ou grupos no Azure AD.

> [!TIP]
> Também é possível habilitar o logon único baseado em SAML para o Foodee seguindo as instruções no [Tutorial de logon único do Foodee](Foodee-tutorial.md). Você pode configurar o logon único de maneira independente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

Faça o seguinte para configurar o provisionamento automático de usuário para o Foodee no Azure AD:

1. No [portal do Azure](https://portal.azure.com), selecione **Aplicativos Empresariais** > **Todos os aplicativos**.

    ![Painel Aplicativos empresariais](common/enterprise-applications.png)

1. Na lista **Aplicativos**, selecione o **Foodee**.

    ![O link do Foodee na lista Aplicativos](common/all-applications.png)

1. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções Gerenciar com a opção Provisionamento destacada.](common/provisioning.png)

1. Na lista suspensa **Modo de Provisionamento**, selecione **Automático**.

    ![Captura de tela da lista suspensa Modo de Provisionamento com a opção Automático destacada.](common/provisioning-automatic.png)

1. Em **Credenciais de Administrador**, faça o seguinte:

   a. Na caixa **URL do Locatário**, insira o valor de **https:\//concierge.food.ee/scim/v2** que você recuperou anteriormente.

   b. Na caixa **Token Secreto**, insira o valor do **Token da API** que você recuperou anteriormente.
   
   c. Selecione **Testar Conectividade** para verificar se o Azure AD pode se conectar ao Foodee. Se a conexão falhar, verifique se a sua conta do Foodee tem permissões de administrador e tente novamente.

    ![O link Testar Conectividade](common/provisioning-testconnection-tenanturltoken.png)

1. Na caixa **Notificação por Email**, insira o endereço de email de uma pessoa ou de um grupo que deverá receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![A caixa de texto Email de Notificação](common/provisioning-notification-email.png)

1. Selecione **Salvar**.

1. Em **Mapeamentos**, selecione **Sincronizar usuários do Azure Active Directory com o Foodee**.

    :::image type="content" source="media/Foodee-provisioning-tutorial/usermapping.png" alt-text="Captura de tela da seção Mapeamentos. Em Nome, Sincronizar Usuários do Azure Active Directory com o Foodee está realçado." border="false":::

1. Em **Mapeamentos de Atributos**, examine os atributos de usuário que serão sincronizados do Azure AD com o Foodee. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das *contas de usuário* no Foodee em operações de atualização. 

    :::image type="content" source="media/Foodee-provisioning-tutorial/userattribute.png" alt-text="Captura de tela da página Mapeamentos de Atributos. Uma tabela lista os atributos do Azure Active Directory e do Foodee e a precedência de correspondência." border="false":::

1. Para confirmar as alterações, selecione **Salvar**.
1. Em **Mapeamentos**, selecione **Sincronizar Grupos do Azure Active Directory com o Foodee**.

    :::image type="content" source="media/Foodee-provisioning-tutorial/groupmapping.png" alt-text="Captura de tela da seção Mapeamentos. Em Nome, Sincronizar Grupos do Azure Active Directory com o Foodee está realçado." border="false":::

1. Em **Mapeamentos de Atributos**, examine os atributos de usuário que serão sincronizados do Azure AD com o Foodee. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das *contas de grupo* no Foodee em operações de atualização.

    :::image type="content" source="media/Foodee-provisioning-tutorial/groupattribute.png" alt-text="Captura de tela da página Mapeamentos de Atributos. Uma tabela lista os atributos do Azure Active Directory e do Foodee e a precedência de correspondência." border="false":::

1. Para confirmar as alterações, selecione **Salvar**.
1. Configure os filtros de escopo. Para saber como fazer isso, confira as instruções no [Tutorial sobre filtros de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Para habilitar o serviço de provisionamento do Azure AD para o Foodee, na seção **Configurações**, altere o **Status de Provisionamento** para **Ativado**.

    ![O botão de opção Status de Provisionamento](common/provisioning-toggle-on.png)

1. Em **Configurações**, na lista suspensa **Escopo**, defina os usuários ou grupos que deseja provisionar para o Foodee.

    ![A lista suspensa Escopo de Provisionamento](common/provisioning-scope.png)

1. Quando estiver pronto para fazer o provisionamento, selecione **Salvar**.

    ![O botão Salvar da Configuração de Provisionamento](common/provisioning-configuration-save.png)

A operação anterior começa a sincronização inicial dos usuários ou grupos que você definiu na lista suspensa **Escopo**. A sincronização inicial demora mais para ser executada do que as posteriores. Para saber mais, confira [Quanto tempo levará para provisionar os usuários?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

É possível usar a seção **Status Atual** para monitorar o andamento e seguir os links para seu relatório de atividade de provisionamento. O relatório descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Foodee. Para obter mais informações, consulte [Verificar o status do provisionamento de usuário](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os logs de provisionamento do Azure AD, confira [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar provisionamento de conta de usuário para aplicativos empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
