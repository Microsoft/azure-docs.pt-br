---
title: 'Tutorial: configurar o SAAS Olfeo para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o Olfeo SAAS.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 5f6b0320-dfe7-451c-8cd8-6ba7f2e40434
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2021
ms.author: Zhchia
ms.openlocfilehash: b74175c7847bb19aa9410edd613afbfe1d762d05
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105549274"
---
# <a name="tutorial-configure-olfeo-saas-for-automatic-user-provisioning"></a>Tutorial: configurar o SAAS Olfeo para o provisionamento automático de usuário

Este tutorial descreve as etapas que você precisa fazer em Olfeo SAAS e Azure Active Directory (Azure AD) para configurar o provisionamento automático de usuário. Quando configurado, o Azure AD provisiona e desprovisiona automaticamente usuários e grupos para o [OLFEO SaaS](https://www.olfeo.com) usando o serviço de provisionamento do Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades com suporte
> [!div class="checklist"]
> * Criar usuários no Olfeo SAAS
> * Remover usuários no Olfeo SAAS quando eles não exigem mais acesso
> * Manter os atributos de usuário sincronizados entre o Azure AD e o Olfeo SAAS
> * Provisionar grupos e associações de grupo no SAAS Olfeo
> * [Logon único](olfeo-saas-tutorial.md) no Olfeo SaaS (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](../develop/quickstart-create-new-tenant.md) 
* Uma conta de usuário no Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento (por exemplo, Administrador de Aplicativo, Administrador de aplicativos de nuvem, Proprietário de Aplicativo ou Administrador Global). 
* Um [locatário OLFEO SaaS](https://www.olfeo.com/).
* Uma conta de usuário no Olfeo SAAS com permissões de administrador.

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje a implantação do provisionamento

1. Saiba mais sobre [como funciona o serviço de provisionamento](../app-provisioning/user-provisioning.md).
1. Determine quem estará no [escopo de provisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Determine quais dados [mapear entre o Azure AD e o OLFEO SaaS](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-olfeo-saas-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configurar o SAAS Olfeo para dar suporte ao provisionamento com o Azure AD

1. Faça logon no console de administração do Olfeo SAAS. 
1. Navegue até **configuração > annuaires**.
1. Crie um novo diretório e nomeie-o.
1. Selecione provedor **do Azure** e clique em **CR er** para salvar o novo diretório. 
1. Navegue até a guia **sincronização** para ver a **URL do locatário** e o **segredo Jeton**. Esses valores serão copiados e colados na **URL do locatário** e nos campos de **token secretos** na guia provisionamento do aplicativo SaaS Olfeo no portal do Azure.

## <a name="step-3-add-olfeo-saas-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar o Olfeo SAAS da Galeria de aplicativos do Azure AD

Adicione o Olfeo SAAS da Galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento no SAAS do Olfeo. Se você tiver configurado anteriormente o SAAS Olfeo para SSO, poderá usar o mesmo aplicativo. No entanto, é recomendável que você crie um aplicativo separado ao testar a integração inicialmente. Saiba mais sobre como adicionar um aplicativo da galeria [aqui](../manage-apps/add-gallery-app.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo de provisionamento 

No Azure AD, é possível definir quem estará no escopo de provisionamento com base na atribuição ao aplicativo ou nos atributos do usuário/grupo. Se você optar por definir quem estará no escopo de provisionamento com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você optar por definir quem estará no escopo de provisionamento com base somente em atributos do usuário ou do grupo, poderá usar um filtro de escopo, conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir usuários e grupos ao SAAS Olfeo, você deve selecionar uma função diferente de **acesso padrão**. Os usuários com a função Acesso Padrão são excluídos do provisionamento e serão marcados como "Não qualificado efetivamente" nos logs de provisionamento. Se a única função disponível no aplicativo for a de acesso padrão, você poderá [atualizar o manifesto do aplicativo](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar mais funções. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de implementar para todos. Quando o escopo de provisionamento é definido para usuários e grupos atribuídos, é possível controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, é possível especificar um [atributo com base no filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-olfeo-saas"></a>Etapa 5. Configurar o provisionamento automático de usuário para o SAAS Olfeo 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e grupos no aplicativo SAAS Olfeo com base em atribuições de usuário e de grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-olfeo-saas-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para Olfeo SAAS no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

1. Na lista de aplicativos, selecione **Olfeo SAAS**.

    ![O link do Olfeo SAAS na lista de aplicativos](common/all-applications.png)

1. Selecione a guia **Provisionamento**.

    ![Guia Provisionamento](common/provisioning.png)

1.  Defina o **Modo de Provisionamento** como **Automático**.

    ![Guia Provisionamento automático](common/provisioning-automatic.png)

1. Na seção **credenciais de administrador** , insira a URL do **locatário** do Olfeo SaaS e as informações do **token secreto** . Selecione **testar conexão** para garantir que o Azure ad possa se conectar ao SaaS Olfeo. Se a conexão falhar, verifique se sua conta Olfeo SAAS tem permissões de administrador e tente novamente.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

1. No campo **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento. Marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

1. Clique em **Salvar**.

1. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para Olfeo SaaS**.

1. Examine os atributos de usuário que são sincronizados do Azure AD para o Olfeo SAAS na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no SaaS Olfeo para operações de atualização. Se você alterar o [atributo de destino correspondente](../app-provisioning/customize-application-attributes.md), precisará garantir que a API Olfeo SaaS dê suporte à filtragem de usuários com base nesse atributo. Para confirmar eventuais alterações, selecione **Salvar**.

   |Atributo|Type|Com suporte para filtragem|
   |---|---|---|
   |userName|String|&check;|
   |displayName|String|
   |ativo|Boolean|
   |emails[type eq "work"].value|String|
   |preferredLanguage|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |externalId|String|  

1. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory para Olfeo SaaS**.

1. Examine os atributos de grupo que são sincronizados do Azure AD para o Olfeo SAAS na seção de **mapeamento de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos no SaaS Olfeo para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

      |Atributo|Type|Com suporte para filtragem|
      |---|---|---|
      |displayName|String|&check;|
      |externalId|String|
      |membros|Referência|

1. Para configurar filtros de escopo, consulte as instruções fornecidas no [tutorial de filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Para habilitar o serviço de provisionamento do Azure AD para o SAAS Olfeo, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

1. Defina os usuários ou grupos que você deseja provisionar para Olfeo SAAS selecionando os valores desejados em **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

1. Quando estiver pronto para fazer o provisionamento, selecione **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação começa o ciclo de sincronização inicial de todos os usuários e grupos definidos no **Escopo** na seção **Configurações**. O ciclo inicial leva mais tempo do que os próximos ciclos, que ocorrem a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução.

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação

Após configurar o provisionamento, use os seguintes recursos para monitorar a implantação:

* Use os [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais usuários foram provisionados com êxito ou sem êxito.
* Verifique a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o status do ciclo de provisionamento e como fechá-lo para conclusão.
* Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Para saber mais sobre os Estados de quarentena, consulte [status de provisionamento de aplicativo de quarentena](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="more-resources"></a>Mais recursos

* [Gerenciamento do provisionamento de conta de usuário para aplicativos empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)