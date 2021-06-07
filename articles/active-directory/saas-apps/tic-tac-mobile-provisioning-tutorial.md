---
title: 'Tutorial: Configurar o Tic-Tac Mobile para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e desprovisionar automaticamente contas de usuário do Azure AD para o Tic-Tac Mobile.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d0f24e81-fecf-4e71-bd8a-ab911366fdf5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: Zhchia
ms.openlocfilehash: 91ae51b9a2785dbc40c55fa58b26763916e8d16c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101644537"
---
# <a name="tutorial-configure-tic-tac-mobile-for-automatic-user-provisioning"></a>Tutorial: Configurar o Tic-Tac Mobile para o provisionamento automático de usuário

Este tutorial descreve as etapas que você precisa executar no Tic-Tac Mobile e no Azure AD (Azure Active Directory) para configurar o provisionamento automático de usuário. Quando configurado, o Azure AD provisiona e desprovisiona automaticamente usuários e grupos no [Tic-Tac Mobile](https://www.tictacmobile.com/) usando o serviço de provisionamento do Azure AD. Para obter informações sobre o que o serviço faz, como ele funciona e as perguntas frequentes, confira [Automatizar o provisionamento e o desprovisionamento de usuário para aplicativos SaaS (software como serviço) com o Azure AD](../app-provisioning/user-provisioning.md).


## <a name="capabilities-supported"></a>Funcionalidades com suporte

> [!div class="checklist"]
> * Criar usuários no Tic-Tac Mobile.
> * Remover usuários no Tic-Tac Mobile quando eles não precisarem mais de acesso.
> * Manter os atributos de usuário sincronizados entre o Azure AD e o Tic-Tac Mobile.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](../develop/quickstart-create-new-tenant.md).
* Uma conta de usuário no Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento. São exemplos o Administrador de aplicativos, o Administrador de aplicativos de nuvem, o Proprietário do aplicativo ou o Administrador global.
* Uma conta do [Tic-Tac Mobile](https://www.tictacmobile.com/) com uma função de superadministrador.


## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje a implantação do provisionamento

1. Saiba mais sobre [como funciona o serviço de provisionamento](../app-provisioning/user-provisioning.md).
1. Determine quem estará no [escopo de provisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Determine quais dados serão [mapeados entre o Azure AD e o Tic-Tac Mobile](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-tic-tac-mobile-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configurar o Tic-Tac Mobile para dar suporte ao provisionamento com o Azure AD

Entre em contato com support@tictacmobile.com para obter a **URL do locatário** e o **Token Secreto**. Você deve ter uma função de superadministrador no Tic-Tac Mobile para receber um token. Esse token será inserido na caixa **Token Secreto** na guia **Provisionamento** do aplicativo Tic-Tac Mobile no portal do Azure.

## <a name="step-3-add-tic-tac-mobile-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar o Tic-Tac Mobile por meio da galeria de aplicativos do Azure AD

Adicione o Tic-Tac Mobile por meio da galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento nele. Se já tiver configurado o Tic-Tac Mobile para logon único, você poderá usar o mesmo aplicativo. Ao testar a integração inicialmente, crie um aplicativo separado. Para saber mais sobre como adicionar um aplicativo por meio da galeria, confira [Provisionamento de aplicativos baseado em atributo com filtros de escopo](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo de provisionamento

Com o serviço de provisionamento do Azure AD, você pode definir o escopo de quem será provisionado com base na atribuição ao aplicativo ou nos atributos do usuário ou do grupo. Se você optar por definir quem estará no escopo de provisionamento com base na atribuição, siga as etapas em [Gerenciar a atribuição de usuário para um aplicativo no Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md) para atribuir usuários e grupos ao aplicativo. Se você optar por definir quem estará no escopo de provisionamento com base somente em atributos do usuário ou do grupo, use um filtro de escopo conforme descrito em [Provisionamento de aplicativo com base em atributo com filtros de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* Ao atribuir usuários e grupos ao Tic-Tac Mobile, você precisa selecionar uma função diferente de **Acesso Padrão**. Os usuários com a função Acesso Padrão são excluídos do provisionamento marcados como "Não qualificado efetivamente" nos logs de provisionamento. Se a única função disponível no aplicativo for a de acesso padrão, você poderá [atualizar o manifesto do aplicativo](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar mais funções.
* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de implementar para todos. Quando o escopo de provisionamento é definido para usuários e grupos atribuídos, é possível manter o controle atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, é possível especificar um [atributo com base no filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="step-5-configure-automatic-user-provisioning-to-tic-tac-mobile"></a>Etapa 5. Configurar o provisionamento automático de usuário no Tic-Tac Mobile

Nesta seção, você verá orientações para seguir as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários ou grupos no TestApp com base em atribuições de usuário ou grupo no Azure AD.

### <a name="configure-automatic-user-provisioning-for-tic-tac-mobile-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Tic-Tac Mobile no Azure AD

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos empresariais** > **Todos os aplicativos**.

    ![Captura de tela que mostra o painel Aplicativos empresariais.](common/enterprise-applications.png)

1. Na lista de aplicativos, selecione **Tic-Tac Mobile**.

    ![Captura de tela que mostra o link do Tic-Tac Mobile na lista de aplicativos.](common/all-applications.png)

1. Selecione a guia **Provisionamento**.

    ![Captura de tela que mostra a guia Provisionamento.](common/provisioning.png)

1. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela que mostra a opção Automático na guia Provisionamento.](common/provisioning-automatic.png)

1. Na seção **Credenciais de Administrador**, insira a **URL do Locatário** e o **Token Secreto** do Tic-Tac Mobile. Selecione **Testar Conectividade** para verificar se o Azure AD pode se conectar ao Tic-Tac Mobile. Se a conexão falhar, verifique se a sua conta do Tic-Tac Mobile tem permissões de administrador e tente novamente.

    ![Captura de tela que mostra a caixa Token Secreto.](common/provisioning-testconnection-tenanturltoken.png)

1. Na caixa **Email de Notificação**, insira o endereço de email de uma pessoa ou de um grupo que deve receber as notificações de erro de provisionamento. Marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Captura de tela que mostra a caixa Email de Notificação.](common/provisioning-notification-email.png)

1. Selecione **Salvar**.

1. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Tic-Tac Mobile**.

1. Examine os atributos de usuário que serão sincronizados do Azure AD para o Tic-Tac Mobile na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das contas de usuário no Tic-Tac Mobile em operações de atualização. Se alterar o [atributo de destino correspondente](../app-provisioning/customize-application-attributes.md), você precisará garantir que a API do Tic-Tac Mobile seja compatível com a filtragem de usuários com base nesse atributo. Selecione o botão **Salvar** para confirmar as alterações.

   |Atributo|Type|
   |---|---|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |externalId|String|
   |título|String|
   |emails[type eq "work"].value|String|
   |preferredLanguage|String|
   |externalId|String|
   |userType|String|
   |localidade|String|
   |timezone|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|

1. Para configurar filtros de escopo, confira as instruções fornecidas no [tutorial sobre filtros de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Para habilitar o serviço de provisionamento do Azure AD no Tic-Tac Mobile, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Captura de tela que mostra o Status de Provisionamento Ativado.](common/provisioning-toggle-on.png)

1. Defina os usuários ou grupos que deseja provisionar no Tic-Tac Mobile selecionando os valores desejados em **Escopo** na seção **Configurações**.

    ![Captura de tela que mostra o Escopo de provisionamento.](common/provisioning-scope.png)

1. Quando estiver pronto para fazer o provisionamento, selecione **Salvar**.

    ![Captura de tela que mostra como salvar a configuração de provisionamento.](common/provisioning-configuration-save.png)

Essa operação começa o ciclo de sincronização inicial de todos os usuários e grupos definidos no **Escopo** na seção **Configurações**. O ciclo inicial leva mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução.

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação

Após configurar o provisionamento, use os recursos a seguir para monitorar a implantação.

1. Use os [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais usuários foram provisionados com êxito ou não.
1. Confira a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o status do ciclo de provisionamento e saber como fechá-la para concluir.
1. Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Para saber mais sobre os estados de quarentena, confira [Provisionamento de aplicativo no status de quarentena](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para aplicativos empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)