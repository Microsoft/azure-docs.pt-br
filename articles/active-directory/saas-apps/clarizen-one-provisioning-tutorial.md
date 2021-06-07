---
title: 'Tutorial: Configurar o Clarizen One para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e desprovisionar automaticamente contas de usuário do Azure AD para o Clarizen One.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d8021105-eb5b-4a20-8739-f02e0e22c147
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: Zhchia
ms.openlocfilehash: f3a19d3c3bf3e4340bb36fd683453541fa15eb6c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101650806"
---
# <a name="tutorial-configure-clarizen-one-for-automatic-user-provisioning"></a>Tutorial: Configurar o Clarizen One para o provisionamento automático de usuário

Este tutorial descreve as etapas que você precisa executar no Clarizen One e no Azure AD (Active Directory) para configurar o provisionamento automático de usuário. Quando configurado, o Azure AD provisiona e desprovisiona automaticamente usuários e grupos no [Clarizen One](https://www.clarizen.com/) usando o serviço de provisionamento do Azure AD. Para obter informações sobre o que o serviço faz, como ele funciona e as perguntas frequentes, confira [Automatizar o provisionamento e o desprovisionamento de usuário para aplicativos SaaS (software como serviço) com o Azure AD](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Funcionalidades com suporte

> [!div class="checklist"]
> * Criar usuários no Clarizen One.
> * Remover usuários no Clarizen One quando eles não precisarem mais de acesso.
> * Manter os atributos de usuário sincronizados entre o Azure AD e o Clarizen One.
> * Provisionar grupos e associações de grupo no Clarizen One.
> * O [SSO (logon único)](./clarizen-tutorial.md) para o Clarizen One é recomendado.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](../develop/quickstart-create-new-tenant.md).
* Uma conta de usuário no Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento. São exemplos o Administrador de aplicativos, o Administrador de aplicativos de nuvem, o Proprietário do aplicativo ou o Administrador global.
* Uma conta de usuário no Clarizen One com as [permissões](https://success.clarizen.com/hc/articles/360011833079-API-Keys-Support) **Usuário de Integração** e **Lite Admin**.

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje a implantação do provisionamento

1. Saiba mais sobre [como funciona o serviço de provisionamento](../app-provisioning/user-provisioning.md).
1. Determine quem estará no [escopo de provisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Determine quais dados serão [mapeados entre o Azure AD e o Clarizen One](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-clarizen-one-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configurar o Clarizen One para dar suporte ao provisionamento com o Azure AD

1. Selecione uma das quatro URLs de locatário seguintes de acordo com seu ambiente e data center do Clarizen One:
      * Data center de Produção nos EUA: https://servicesapp2.clarizen.com/scim/v2
      * Data center de Produção na UE: https://serviceseu1.clarizen.com/scim/v2
      * Data center de Área restrita nos EUA: https://servicesapp.clarizentb.com/scim/v2
      * Data center de Área restrita na UE: https://serviceseu.clarizentb.com/scim/v2

1. Gere uma [chave de API](https://success.clarizen.com/hc/articles/360011833079-API-Keys-Support). Esse valor será inserido na caixa **Token Secreto** na guia **Provisionamento** do aplicativo Clarizen One no portal do Azure.

## <a name="step-3-add-clarizen-one-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar o Clarizen One por meio da galeria de aplicativos do Azure AD

Adicione o Clarizen One por meio da galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento nele. Se já tiver configurado o Clarizen One para o SSO, você poderá usar o mesmo aplicativo. Ao testar a integração inicialmente, crie um aplicativo separado. Para saber mais sobre como adicionar um aplicativo por meio da galeria, confira [Adicionar um aplicativo ao locatário do Azure AD](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo de provisionamento

Com o serviço de provisionamento do Azure AD, você pode definir o escopo de quem será provisionado com base na atribuição ao aplicativo ou nos atributos do usuário ou do grupo. Se você optar por definir quem estará no escopo de provisionamento com base na atribuição, siga as etapas em [Gerenciar a atribuição de usuário para um aplicativo no Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md) para atribuir usuários e grupos ao aplicativo. Se você optar por definir quem estará no escopo de provisionamento com base somente em atributos do usuário ou do grupo, use um filtro de escopo conforme descrito em [Provisionamento de aplicativo com base em atributo com filtros de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* Ao atribuir usuários e grupos ao Clarizen One, você precisa selecionar uma função diferente de **Acesso Padrão**. Os usuários com a função Acesso Padrão são excluídos do provisionamento marcados como "Não qualificado efetivamente" nos logs de provisionamento. Se a única função disponível no aplicativo for a de acesso padrão, você poderá [atualizar o manifesto do aplicativo](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar mais funções.
* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de implementar para todos. Quando o escopo de provisionamento é definido para usuários e grupos atribuídos, é possível manter o controle atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, é possível especificar um [atributo com base no filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="step-5-configure-automatic-user-provisioning-to-clarizen-one"></a>Etapa 5. Configurar o provisionamento automático de usuário no Clarizen One

Nesta seção, você verá orientações para seguir as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários ou grupos no TestApp com base em atribuições de usuário ou grupo no Azure AD.

### <a name="configure-automatic-user-provisioning-for-clarizen-one-in-azure-ad"></a>Configurar o provisionamento automático de usuário para o Clarizen One no Azure AD

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos empresariais** > **Todos os aplicativos**.

      ![Captura de tela que mostra o painel Aplicativos empresariais.](common/enterprise-applications.png)

1. Na lista de aplicativos, selecione o **Clarizen One**.

      ![Captura de tela que mostra o link do Clarizen One na lista de aplicativos.](common/all-applications.png)

1. Selecione a guia **Provisionamento**.

      ![Captura de tela que mostra a guia Provisionamento.](common/provisioning.png)

1. Defina o **Modo de Provisionamento** como **Automático**.

      ![Captura de tela que mostra a opção Automático na guia Provisionamento.](common/provisioning-automatic.png)

1. Na seção **Credenciais de Administrador**, insira a **URL do Locatário** e o **Token Secreto** do Clarizen One. Selecione **Testar Conectividade** para verificar se o Azure AD pode se conectar ao Clarizen One. Se a conexão falhar, verifique se a conta do Clarizen One tem permissões de administrador e tente novamente.

    ![Captura de tela que mostra a caixa Token Secreto.](common/provisioning-testconnection-tenanturltoken.png)

1. Na caixa **Email de Notificação**, insira o endereço de email de uma pessoa ou de um grupo que deve receber as notificações de erro de provisionamento. Marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Captura de tela que mostra a caixa Email de Notificação.](common/provisioning-notification-email.png)

1. Selecione **Salvar**.

1. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Clarizen One**.

1. Examine os atributos de usuário que serão sincronizados do Azure AD para o Clarizen One na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das contas de usuário do Clarizen One em operações de atualização. Se alterar o [atributo de destino correspondente](../app-provisioning/customize-application-attributes.md), você precisará garantir que a API do Clarizen One seja compatível com a filtragem de usuários com base nesse atributo. Selecione o botão **Salvar** para confirmar as alterações.

   |Atributo|Type|
   |---|---|
   |userName|String|
   |displayName|String|
   |ativo|Boolean|
   |título|String|
   |emails[type eq "work"].value|String|
   |emails[type eq "home"].value|String|
   |emails[type eq "other"].value|String|
   |preferredLanguage|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |name.honorificPrefix|String|
   |name.honorificSuffix|String|
   |addresses[type eq "other"].formatted|String|
   |addresses[type eq "work"].formatted|String|
   |addresses[type eq "work"].country|String|
   |addresses[type eq "work"].region|String|
   |addresses[type eq "work"].locality|String|
   |addresses[type eq "work"].postalCode|String|
   |addresses[type eq "work"].streetAddress|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "fax"].value|String|
   |phoneNumbers[type eq "home"].value|String|
   |phoneNumbers[type eq "other"].value|String|
   |phoneNumbers[type eq "pager"].value|String|
   |externalId|String|
   |nickName|String|
   |localidade|String|
   |roles[primary eq "True".type]|String|
   |roles[primary eq "True".value]|String|
   |timezone|String|
   |userType|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Referência|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|

1. Na seção **Mapeamentos**, selecione **Sincronizar Grupos do Azure Active Directory com o Clarizen One**.

1. Examine os atributos de grupo que serão sincronizados do Azure AD para o Clarizen One na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência dos grupos do Clarizen One em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

      |Atributo|Type|
      |---|---|
      |displayName|String|
      |externalId|String|
      |membros|Referência|

1. Para configurar filtros de escopo, confira as instruções fornecidas no [tutorial sobre filtros de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Para habilitar o serviço de provisionamento do Azure AD no Clarizen One, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

      ![Captura de tela que mostra o Status de Provisionamento Ativado.](common/provisioning-toggle-on.png)

1. Defina os usuários ou grupos que deseja provisionar no Clarizen One selecionando os valores desejados em **Escopo** na seção **Configurações**.

      ![Captura de tela que mostra o Escopo de provisionamento.](common/provisioning-scope.png)

1. Quando estiver pronto para fazer o provisionamento, selecione **Salvar**.

      ![Captura de tela que mostra como salvar a configuração de provisionamento.](common/provisioning-configuration-save.png)

Essa operação começa o ciclo de sincronização inicial de todos os usuários e grupos definidos no **Escopo** na seção **Configurações**. O ciclo inicial leva mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução.

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação

Após configurar o provisionamento, use os recursos a seguir para monitorar a implantação.

1. Use os [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais usuários foram provisionados com êxito ou não.
1. Confira a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o status do ciclo de provisionamento e saber como fechá-la para concluir.
1. Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Para saber mais sobre os estados de quarentena, confira [Provisionamento de aplicativo no status de quarentena](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="troubleshooting-tips"></a>Dicas de solução de problemas

Ao atribuir um usuário ao aplicativo da galeria Clarizen One, selecione somente a função de **Usuário**. As seguintes funções são inválidas:

* Administrador (admin)
* Usuário de Relatório de Email
* Usuário externo
* Usuário financeiro
* Usuário social
* Superusuário
* Usuário de Tempo & Despesas

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para aplicativos empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)