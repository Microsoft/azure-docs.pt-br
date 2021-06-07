---
title: 'Tutorial: configurar getabstract para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e desprovisionar automaticamente contas de usuário de Azure Active Directory para getabstract.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: bd8898f9-7a01-4e85-9dd4-61ae4b01ab5b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2021
ms.author: Zhchia
ms.openlocfilehash: 1d1b2417750b917f5b09bb53ee980887218a785c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102616104"
---
# <a name="tutorial-configure-getabstract-for-automatic-user-provisioning"></a>Tutorial: configurar getabstract para provisionamento automático de usuário

Este tutorial descreve as etapas que você precisa executar tanto no getabstract quanto no Azure Active Directory (Azure AD) para configurar o provisionamento automático de usuário. Quando configurado, o AD do Azure provisiona e desprovisiona automaticamente usuários e grupos para [getabstract](https://www.getabstract.com) usando o serviço de provisionamento do Azure AD. Para obter detalhes importantes sobre o que esse serviço faz, como ele funciona e perguntas frequentes, consulte [automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS (software como serviço) com o Azure ad](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Funcionalidades com suporte

> [!div class="checklist"]
> * Crie usuários em getabstract.
> * Remova os usuários em getabstract quando eles não exigem mais acesso.
> * Mantenha os atributos de usuário sincronizados entre o Azure AD e o getabstract.
> * Provisione grupos e associações de grupo em getabstract.
> * Habilite o [SSO (logon único)](getabstract-tutorial.md) para getabstract (recomendado).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](../develop/quickstart-create-new-tenant.md).
* Uma conta de usuário no Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento. Os exemplos são administrador de aplicativos, administrador de aplicativos de nuvem, proprietário do aplicativo ou administrador global.
* Um locatário getabstract (licença corporativa getabstract).
* SSO habilitado no locatário do Azure AD e no locatário getabstract.
* Aprovação e sistema para o SCIM (gerenciamento de identidade entre domínios) habilitando o getabstract. (Enviar email para b2b.itsupport@getabstract.com .)

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje a implantação do provisionamento

1. Saiba mais sobre [como funciona o serviço de provisionamento](../app-provisioning/user-provisioning.md).
1. Determine quem estará no [escopo de provisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Determine quais dados [mapear entre o Azure AD e getabstract](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-getabstract-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configurar getabstract para dar suporte ao provisionamento com o Azure AD

1. Entre em getabstract.
1. Selecione o ícone de configurações localizado no canto superior direito e selecione a opção **meu administrador central** .

    ![Captura de tela que mostra a minha administração central do getabstract.](media/getabstract-provisioning-tutorial/my-account.png)

1. Localize e selecione a opção de **administrador do scim** .

    ![Captura de tela que mostra o administrador SCIM do getabstract.](media/getabstract-provisioning-tutorial/scim-admin.png)

1. Selecione **Ir**.

    ![Captura de tela que mostra a ID do cliente getabstract SCIM.](media/getabstract-provisioning-tutorial/scim-client-go.png)

1. Selecione **Gerar novo token**.

    ![Captura de tela que mostra o token 1 do getabstract SCIM.](media/getabstract-provisioning-tutorial/scim-generate-token-step-2.png)

1. Se tiver certeza, selecione **gerar novo token**. Caso contrário, selecione **Cancelar**.

    ![Captura de tela que mostra o token 2 do getabstract SCIM.](media/getabstract-provisioning-tutorial/scim-generate-token-step-1.png)

1. Selecione o ícone Copiar para a área de transferência ou selecione o token inteiro e copie-o. Também Anote que o locatário/URL base é `https://www.getabstract.com/api/scim/v2` . Esses valores serão inseridos nas caixas **token secreto** e **URL do locatário** na guia **provisionamento** do aplicativo getabstract na portal do Azure.

    ![Captura de tela que mostra o token 3 do SCIM do getabstract.](media/getabstract-provisioning-tutorial/scim-generate-token-step-3.png)

## <a name="step-3-add-getabstract-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar getabstract da Galeria de aplicativos do Azure AD

Adicione getabstract da Galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento para getabstract. Se você configurou o getabstract para SSO anteriormente, poderá usar o mesmo aplicativo. Recomendamos que você crie um aplicativo separado ao testar a integração inicialmente. Para saber mais sobre como adicionar um aplicativo da galeria, consulte este guia de [início rápido](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo de provisionamento

Você pode usar o serviço de provisionamento do Azure AD para o escopo que será provisionado com base na atribuição para o aplicativo ou com base em atributos do usuário ou grupo. Se você optar por definir quem estará no escopo de provisionamento com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você optar pelo escopo que será provisionado com base apenas em atributos do usuário ou grupo, poderá usar um filtro de escopo conforme descrito em [provisionar aplicativos com filtros de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* Ao atribuir usuários e grupos ao getabstract, você deve selecionar uma função diferente de **acesso padrão**. Os usuários com a função Acesso Padrão são excluídos do provisionamento marcados como "Não qualificado efetivamente" nos logs de provisionamento. Se a única função disponível no aplicativo for a de acesso padrão, você poderá [atualizar o manifesto do aplicativo](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar mais funções.

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de implementar para todos. Quando o escopo para provisionamento é definido como usuários e grupos atribuídos, você pode controlar essa opção atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, é possível especificar um [atributo com base no filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="step-5-configure-automatic-user-provisioning-to-getabstract"></a>Etapa 5. Configurar o provisionamento automático de usuário para getabstract

Nesta seção, você verá orientações para seguir as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários ou grupos no TestApp com base em atribuições de usuário ou grupo no Azure AD.

### <a name="configure-automatic-user-provisioning-for-getabstract-in-azure-ad"></a>Configurar o provisionamento automático de usuário para getabstract no Azure AD

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos empresariais** > **Todos os aplicativos**.

    ![Captura de tela que mostra o painel Aplicativos empresariais.](common/enterprise-applications.png)

1. Na lista de aplicativos, selecione **getabstract**.

    ![Captura de tela que mostra o link getabstract na lista de aplicativos.](common/all-applications.png)

1. Selecione a guia **Provisionamento**.

    ![Captura de tela que mostra a guia Provisionamento.](common/provisioning.png)

1. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela que mostra o Modo de Provisionamento definido como Automático.](common/provisioning-automatic.png)

1. Na seção **credenciais de administrador** , insira as informações de **URL do locatário** do getabstract e do **token secreto** . Selecione **testar conexão** para garantir que o Azure ad possa se conectar ao getabstract. Se a conexão falhar, verifique se sua conta getabstract tem permissões de administrador e tente novamente.

    ![Captura de tela que mostra as caixas URL do locatário e token secreto.](common/provisioning-testconnection-tenanturltoken.png)

1. Na caixa **Email de Notificação**, insira o endereço de email de uma pessoa ou de um grupo que deve receber as notificações de erro de provisionamento. Marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Captura de tela que mostra a caixa Email de Notificação.](common/provisioning-notification-email.png)

1. Selecione **Salvar**.

1. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para getabstract**.

1. Examine os atributos de usuário que são sincronizados do Azure AD para getabstract na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário em getabstract para operações de atualização. Se você alterar o [atributo de destino correspondente](../app-provisioning/customize-application-attributes.md), precisará garantir que a API getabstract dê suporte à filtragem de usuários com base nesse atributo. Para confirmar eventuais alterações, selecione **Salvar**.

   |Atributo|Type|Com suporte para filtragem|
   |---|---|---|
   |userName|String|&check;|
   |ativo|Boolean|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |externalId|String|
   |preferredLanguage|String|

1. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory para getabstract**.

1. Examine os atributos de grupo que são sincronizados do Azure AD para getabstract na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos em getabstract para operações de atualização. Para confirmar eventuais alterações, selecione **Salvar**.

    |Atributo|Type|Com suporte para filtragem|
    |---|---|---|
    |displayName|String|&check;|
    |externalId|String|
    |membros|Referência|

1. Para configurar filtros de escopo, consulte as instruções fornecidas no [tutorial de filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Para habilitar o serviço de provisionamento do Azure AD para getabstract, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Captura de tela que mostra o Status de Provisionamento Ativado.](common/provisioning-toggle-on.png)

1. Defina os usuários ou grupos que você deseja provisionar para getabstract selecionando os valores desejados no **escopo** na seção **configurações** .

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
