---
title: 'Tutorial: Configurar o Elium para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no Elium.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/19/2019
ms.author: Zhchia
ms.openlocfilehash: e8f027ccc577df79e561fca7194c20b6cc7ef2c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005496"
---
# <a name="tutorial-configure-elium-for-automatic-user-provisioning"></a>Tutorial: Configurar o Elium para o provisionamento automático de usuário

Este tutorial mostra como configurar o Elium e o Azure AD (Azure Active Directory) para provisionar e desprovisionar automaticamente usuários ou grupos no Elium.

> [!NOTE]
> Ele descreve um conector baseado no serviço Provisionamento de Usuário do Azure AD. Para obter detalhes importantes sobre o que esse serviço faz e como ele funciona, além de respostas a perguntas frequentes, confira [Automatizar o provisionamento e o desprovisionamento de usuário em aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia. Para obter os termos de uso gerais dos recursos do Azure em versão prévia, confira [Termos de uso complementares para as versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que você atenda aos seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do Elium](https://www.elium.com/pricing/)
* Uma conta de usuário no Elium com permissões de administrador

## <a name="assigning-users-to-elium"></a>Como atribuir usuários ao Elium

O Azure AD usa um conceito chamado *atribuições* para determinar quais usuários receberão acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e os grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, decida quais usuários e grupos do Azure AD precisam ter acesso ao Elium. Em seguida, atribua esses usuários e grupos ao Elium seguindo as etapas descritas em [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-elium"></a>Dicas importantes para atribuir usuários ao Elium 

Recomendamos que você atribua apenas um usuário do Azure AD ao Elium para testar a configuração de provisionamento automático de usuário. Mais usuários e grupos poderão ser atribuídos posteriormente.

Ao atribuir um usuário ao Elium, você precisará selecionar qualquer função válida específica do aplicativo (se disponível) na caixa de diálogo de atribuição. Usuários que têm a função **Acesso padrão** são excluídos do provisionamento.

## <a name="set-up-elium-for-provisioning"></a>Configurar o Elium para provisionamento

Antes de configurar o Elium para o provisionamento automático de usuário com o Azure AD, você precisará habilitar o provisionamento do SCIM (Sistema de Gerenciamento de Usuários entre Domínios) no Elium. Siga estas etapas:

1. Entre no Elium e acesse **Meu Perfil** > **Configurações**.

    ![Item de menu Configurações no Elium](media/Elium-provisioning-tutorial/setting.png)

1. No canto inferior esquerdo, em **AVANÇADO**, selecione **Segurança**.

    ![Link Segurança no Elium](media/Elium-provisioning-tutorial/security.png)

1. Copie os valores de **URL do Locatário** e **Token Secreto**. Você usará esses valores mais tarde, em campos correspondentes na guia **Provisionamento** do aplicativo Elium no portal do Azure.

    ![Campos URL do locatário e Token secreto no Elium](media/Elium-provisioning-tutorial/token.png)

## <a name="add-elium-from-the-gallery"></a>Adicionar o Elium por meio da galeria

Para configurar o Elium para o provisionamento automático de usuário com o Azure AD, você também precisará adicionar o Elium por meio da galeria de aplicativos do Azure AD à lista de aplicativos SaaS (software como serviço) gerenciados. Siga estas etapas:

1. No [portal do Azure](https://portal.azure.com) , no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![Item de menu Azure Active Directory](common/select-azuread.png)

1. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

     ![Folha Aplicativos empresariais no Azure AD](common/enterprise-applications.png)

1. Para adicionar um novo aplicativo, selecione **Novo aplicativo** na parte superior do painel.

    ![Link Novo aplicativo](common/add-new-app.png)

1. Na caixa de pesquisa, digite **Elium**, selecione **Elium** na lista de resultados e escolha **Adicionar** para adicionar o aplicativo.

    ![Caixa de pesquisa da Galeria](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-elium"></a>Configurar o provisionamento automático de usuário para o Elium

Esta seção descreve as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e grupos no Elium com base em atribuições de usuário e grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único para o Elium baseado em SAML (Security Assertion Markup Language) seguindo as instruções descritas no [tutorial de logon único do Elium](Elium-tutorial.md). Você pode configurar o logon único de maneira independente do provisionamento automático de usuário, embora os dois recursos sejam complementares.

Para configurar o provisionamento automático de usuário para o Elium no Azure AD, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com), selecione **Aplicativos empresariais** e escolha **Todos os aplicativos**.

    ![Folha Aplicativos empresariais no Azure AD](common/enterprise-applications.png)

1. Na lista de aplicativos, escolha **Elium**.

    ![Lista Aplicativos na folha Aplicativos empresariais](common/all-applications.png)

1. Selecione a guia **Provisionamento**.

    ![Guia Provisionamento na folha Aplicativos empresariais](common/provisioning.png)

1. Defina o **Modo de Provisionamento** como **Automático**.

    ![Configuração de Automático para Modo de Provisionamento](common/provisioning-automatic.png)

1. Na seção **Credenciais de Administrador**, digite **\<tenantURL\>/scim/v2** no campo **URL do Locatário**. (A **tenantURL** é o valor recuperado anteriormente do console do administrador do Elium.) Além disso, digite o valor do **Token secreto** do Elium no campo **Token Secreto**. Por fim, selecione **Testar Conectividade** para verificar se o Azure AD pode se conectar ao Elium. Se a conexão falhar, verifique se a sua conta do Elium tem permissões de administrador e tente novamente.

    ![Campos URL do Locatário e Token Secreto em Credenciais de Administrador](common/provisioning-testconnection-tenanturltoken.png)

1. No campo **Email de Notificação**, insira o endereço de email de uma pessoa ou um grupo que receberá as notificações de erro de provisionamento. Depois, marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

1. Clique em **Save** (Salvar).

1. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Elium**.

    ![Link Sincronizar para mapear usuários do Azure AD para o Elium](media/Elium-provisioning-tutorial/usermapping.png)

1. Examine os atributos de usuário que serão sincronizados do Azure AD para o Elium na seção **Mapeamentos de Atributo**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência entre as contas de usuário no Elium em operações de atualização. Para confirmar eventuais alterações, selecione **Salvar**.

    ![Mapeamentos de atributo entre o Azure AD e o Elium](media/Elium-provisioning-tutorial/userattribute.png)

1. Para configurar filtros de escopo, siga as instruções no [Tutorial sobre filtros de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Para habilitar o serviço de provisionamento do Azure AD para o Elium, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status de Provisionamento definido como Ativado](common/provisioning-toggle-on.png)

1. Defina os usuários e os grupos que você quer provisionar no Elium selecionando os valores desejados na caixa de listagem suspensa **Escopo** na seção **Configurações**.

    ![Caixa de listagem Escopo de Provisionamento](common/provisioning-scope.png)

1. Quando estiver pronto para fazer o provisionamento, selecione **Salvar**.

    ![Botão Salvar da configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e os grupos definidos em **Escopo** na seção **Configurações**. Esse processo de sincronização inicial leva mais tempo do que as sincronizações posteriores. Para obter mais informações sobre o tempo necessário para o provisionamento, confira [Quanto tempo levará para provisionar os usuários?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Use a seção **Status Atual** para monitorar o progresso e siga os links para o relatório de atividades de provisionamento. O relatório de atividades de provisionamento descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Elium. Para obter mais informações, consulte [Verificar o status do provisionamento de usuário](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os logs de provisionamento do Azure AD, confira [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Como gerenciar o provisionamento de contas de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
