---
title: 'Tutorial: configurar a gramática para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o de forma gramatical.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: cd2dd9d7-4901-40c8-8888-98850557b072
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2021
ms.author: Zhchia
ms.openlocfilehash: ca01289ce66afe642081e5be17373e640dd1e46d
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864798"
---
# <a name="tutorial-configure-grammarly-for-automatic-user-provisioning"></a>Tutorial: configurar a gramática para o provisionamento automático de usuário

Este tutorial descreve as etapas que você precisa executar tanto em gramática quanto Azure Active Directory (Azure AD) para configurar o provisionamento automático de usuário. Quando configurado, o Azure AD provisiona e desprovisiona automaticamente usuários e grupos para usar a [gramática](https://www.grammarly.com/) usando o serviço de provisionamento do Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades com suporte
> [!div class="checklist"]
> * Criar usuários em gramática
> * Remover usuários em gramática quando eles não exigem mais acesso
> * Manter os atributos de usuário sincronizados entre o Azure AD e gramatical

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de usuário no Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicativo, Administrador de aplicativos de nuvem, Proprietário de Aplicativo ou Administrador global). 
* Uma conta de negócios gramatical com acesso de administrador.

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje a implantação do provisionamento
1. Saiba mais sobre [como funciona o serviço de provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
1. Determine quem estará no [escopo de provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
1. Determine quais dados [mapeados entre o Azure AD e gramatical](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-grammarly-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configurar a gramática para oferecer suporte ao provisionamento com o Azure AD

Entre em contato com seu representante de gramática ou grave para <support@grammarly.com> solicitar o token de provisionamento.

## <a name="step-3-add-grammarly-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar gramáticas da Galeria de aplicativos do Azure AD

Adicione gramáticas da Galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento para gramática. Se você tiver configurado anteriormente de forma gramatical para o SSO, poderá usar o mesmo aplicativo. Recomendamos que você crie um aplicativo separado ao testar a integração inicialmente. Para saber mais sobre como adicionar um aplicativo da galeria, consulte este guia de [início rápido](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo de provisionamento

Você pode usar o serviço de provisionamento do Azure AD para o escopo que será provisionado com base na atribuição para o aplicativo ou com base em atributos do usuário ou grupo. Se você optar por definir quem estará no escopo de provisionamento com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você optar pelo escopo que será provisionado com base apenas em atributos do usuário ou grupo, poderá usar um filtro de escopo conforme descrito em [provisionar aplicativos com filtros de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* Ao atribuir usuários e grupos à gramática, você deve selecionar uma função diferente de **acesso padrão**. Os usuários com a função Acesso Padrão são excluídos do provisionamento marcados como "Não qualificado efetivamente" nos logs de provisionamento. Se a única função disponível no aplicativo for a de acesso padrão, você poderá [atualizar o manifesto do aplicativo](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar mais funções.

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de implementar para todos. Quando o escopo para provisionamento é definido como usuários e grupos atribuídos, você pode controlar essa opção atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, é possível especificar um [atributo com base no filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).


## <a name="step-5-configure-automatic-user-provisioning-to-grammarly"></a>Etapa 5. Configurar o provisionamento automático de usuário para gramatical

Nesta seção, você verá orientações para seguir as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários ou grupos no TestApp com base em atribuições de usuário ou grupo no Azure AD.

### <a name="configure-automatic-user-provisioning-for-grammarly-in-azure-ad"></a>Configurar o provisionamento automático de usuário para gramática no Azure AD

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos empresariais** > **Todos os aplicativos**.

    ![Captura de tela que mostra o painel Aplicativos empresariais.](common/enterprise-applications.png)

1. Na lista de aplicativos, selecione **gramática**.

    ![Captura de tela que mostra o link gramatical na lista de aplicativos.](common/all-applications.png)

1. Selecione a guia **Provisionamento**.

    ![Captura de tela que mostra a guia Provisionamento.](common/provisioning.png)

1. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela que mostra o Modo de Provisionamento definido como Automático.](common/provisioning-automatic.png)

1. Na seção **credenciais de administrador** , no campo inserir **URL do locatário** `https://sso.grammarly.com/scim/v2` , digite e, no campo **token secreto** , insira o token fornecido por gramática (consulte a etapa 2 acima). Clique em **testar conexão** para garantir que o Azure ad possa se conectar à gramática. Se a conexão falhar, verifique se sua conta gramatical tem permissões de administrador e tente novamente.

    ![Captura de tela que mostra as caixas URL do locatário e token secreto.](common/provisioning-testconnection-tenanturltoken.png)

1. Na caixa **Email de Notificação**, insira o endereço de email de uma pessoa ou de um grupo que deve receber as notificações de erro de provisionamento. Marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Captura de tela que mostra a caixa Email de Notificação.](common/provisioning-notification-email.png)

1. Selecione **Salvar**.

1. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para gramática**.

1. Examine os atributos de usuário que são sincronizados do Azure AD para gramática na seção de **mapeamento de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário em gramática para operações de atualização. Se você alterar o [atributo de destino correspondente](../app-provisioning/customize-application-attributes.md), precisará garantir que a API gramatical dê suporte à filtragem de usuários com base nesse atributo. Para confirmar eventuais alterações, selecione **Salvar**.

   |Atributo|Type|Com suporte para filtragem|
   |---|---|---|
   |userName|String|&check;|
   |externalId|String|
   |ativo|Boolean|
   |displayName|String|
   |emails[type eq "work"].value|String|


1. Para configurar filtros de escopo, consulte as instruções fornecidas no [tutorial de filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Para habilitar o serviço de provisionamento do Azure AD para a gramática, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Captura de tela que mostra o Status de Provisionamento Ativado.](common/provisioning-toggle-on.png)

1. Defina os usuários ou grupos que você deseja provisionar para gramática, selecionando os valores desejados em **escopo** na seção **configurações** .

    ![Captura de tela que mostra o escopo de provisionamento.](common/provisioning-scope.png)

1. Quando estiver pronto para fazer o provisionamento, selecione **Salvar**.

    ![Captura de tela que mostra o botão salvar.](common/provisioning-configuration-save.png)

Essa operação começa o ciclo de sincronização inicial de todos os usuários e grupos definidos no **Escopo** na seção **Configurações**. O ciclo inicial leva mais tempo para executar do que os ciclos subsequentes, que ocorrem a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução.

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação

Após configurar o provisionamento, use os seguintes recursos para monitorar a implantação:

* Use os [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais usuários foram provisionados com êxito ou sem êxito.
* Confira a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o status do ciclo de provisionamento e saber como fechá-la para concluir.
* Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Para saber mais sobre os Estados de quarentena, consulte [status de provisionamento de aplicativo de quarentena](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para aplicativos empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
