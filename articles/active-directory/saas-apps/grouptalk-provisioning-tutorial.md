---
title: 'Tutorial: configurar o GroupTalk para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o GroupTalk.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: e537d393-2724-450f-9f5b-4611cdc9237c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2020
ms.author: Zhchia
ms.openlocfilehash: 0af41127577c172cdab74ae908f0645733d49a42
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98735751"
---
# <a name="tutorial-configure-grouptalk-for-automatic-user-provisioning"></a>Tutorial: configurar o GroupTalk para o provisionamento automático de usuário

Este tutorial descreve as etapas que você precisa executar tanto no GroupTalk quanto no Azure Active Directory (Azure AD) para configurar o provisionamento automático de usuário. Quando configurado, o Azure AD provisiona e desprovisiona automaticamente usuários e grupos para [GroupTalk](https://www.grouptalk.com/) usando o serviço de provisionamento do Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades com suporte
> [!div class="checklist"]
> * Criar usuários no GroupTalk
> * Remover usuários no GroupTalk quando eles não exigem mais acesso
> * Manter os atributos de usuário sincronizados entre o Azure AD e o GroupTalk
> * Provisionar grupos e associações de grupo no GroupTalk

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](../develop/quickstart-create-new-tenant.md) 
* Uma conta de usuário no Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento (por exemplo, Administrador de Aplicativo, Administrador de aplicativos de nuvem, Proprietário de Aplicativo ou Administrador Global). 
* Uma conta de usuário no GroupTalk com permissões de administrador.

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje a implantação do provisionamento
1. Saiba mais sobre [como funciona o serviço de provisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem estará no [escopo de provisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais dados [mapeados entre o Azure AD e o GroupTalk](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-grouptalk-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configurar o GroupTalk para dar suporte ao provisionamento com o Azure AD

1. Entre em contato com o suporte support@grouptalk.com do GroupTalk com o **nome** e a **ID** do locatário que você deseja integrar com o Azure AD.
2. Quando você foi notificado de que a configuração necessária para a integração do Azure AD está pronta, faça logon no administrador do GroupTalk e navegue até a exibição da organização. 
3. Um item de configuração de integração do Azure AD deve estar visível. Clique nele para verificar o nome e a **ID** do **locatário** para obter um **JWT (token secreto)**. 
4. A URL do locatário GroupTalk é `https://api.grouptalk.com/api/scim/` . A **URL do locatário** e o **token secreto** recuperado na etapa anterior serão inseridos na guia provisionamento do aplicativo GroupTalk no portal do Azure. 

## <a name="step-3-add-grouptalk-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar o GroupTalk da Galeria de aplicativos do Azure AD

Adicione o **GroupTalk** da Galeria de aplicativos do Azure ad para começar a gerenciar o provisionamento no GroupTalk.

1. Clique no botão **inscrever-se no GroupTalk** , que o roteará para o aplicativo administrativo GroupTalk.
2. Se você já estiver conectado ao GroupTalk, faça logoff para obter a tela de logon. Selecione a guia Azure AD e clique no botão **entrar** .

    ![GroupTalk](media/grouptalk-provisioning-tutorial/login.png)

3. Faça logon com sua conta administrativa do AD e aceite os direitos de acesso do aplicativo GroupTalk. Você receberá uma mensagem de erro depois que isso for feito indicando que o usuário não está presente. Isso é esperado, pois o usuário ainda não está provisionado para GroupTalk, mas agora você adicionou o GroupTalk ao seu locatário.
4. Volte para a portal do Azure e verifique se **GroupTalk** agora está adicionado aos seus aplicativos empresariais.

Saiba mais sobre como adicionar um aplicativo da galeria [aqui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo de provisionamento 

No Azure AD, é possível definir quem estará no escopo de provisionamento com base na atribuição ao aplicativo ou nos atributos do usuário/grupo. Se você optar por definir quem estará no escopo de provisionamento com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você optar por definir quem estará no escopo de provisionamento com base somente em atributos do usuário ou do grupo, poderá usar um filtro de escopo, conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir usuários e grupos ao GroupTalk, você deve selecionar uma função diferente de **acesso padrão**. Os usuários com a função Acesso Padrão são excluídos do provisionamento e serão marcados como "Não qualificado efetivamente" nos logs de provisionamento. Se a única função disponível no aplicativo for a de acesso padrão, você poderá [atualizar o manifesto do aplicativo](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar outras funções. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de implementar para todos. Quando o escopo de provisionamento é definido para usuários e grupos atribuídos, é possível controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, é possível especificar um [atributo com base no filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-grouptalk"></a>Etapa 5. Configurar o provisionamento automático de usuário para o GroupTalk 

Nesta seção, você verá orientações para seguir as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no TestApp com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-grouptalk-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para GroupTalk no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **GroupTalk**.

    ![O link do GroupTalk na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia Provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Guia Provisionamento automático](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira a URL do locatário do GroupTalk e o token secreto recuperado anteriormente da etapa 2. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao GroupTalk. Se a conexão falhar, verifique se sua conta do GroupTalk tem permissões de administrador e tente novamente. Você sempre pode obter um novo token secreto, conforme descrito na etapa 2.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para GroupTalk**.

9. Examine os atributos de usuário que são sincronizados do Azure AD para o GroupTalk na seção de **mapeamento de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no GroupTalk para operações de atualização. Se você optar por alterar o [atributo de destino correspondente](../app-provisioning/customize-application-attributes.md), será necessário garantir que a API GroupTalk dê suporte à filtragem de usuários com base nesse atributo. Selecione o botão **Salvar** para confirmar as alterações.

   |Atributo|Type|Com suporte para filtragem|
   |---|---|---|
   |userName|String|&check;|
   |phoneNumbers[type eq "mobile"].value|String|&check;|
   |emails[type eq "work"].value|String|&check;|
   |ativo|Boolean|
   |name.givenName|String|
   |name.familyName|String|
   |externalId|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|String|
   |urn: IETF: params: SCIM: schemas: Extension: grouptalk: 2.0: User: Label1|String|
   |urn: IETF: params: SCIM: schemas: Extension: grouptalk: 2.0: User: Label2|String|
   |urn: IETF: params: SCIM: schemas: Extension: grouptalk: 2.0: User: Label3|String|
   |urn: IETF: params: SCIM: schemas: Extension: grouptalk: 2.0: User: Label4|String|
   |urn: IETF: params: SCIM: schemas: Extension: grouptalk: 2.0: User: Label5|String|


10. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory para GroupTalk**.

11. Examine os atributos de grupo que são sincronizados do Azure AD para o GroupTalk na seção de **mapeamento de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos no GroupTalk para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

      |Atributo|Type|Com suporte para filtragem|
      |---|---|---|
      |displayName|String|&check;|
      |membros|Referência|
      |externalId|String|      
      |urn: IETF: params: SCIM: esquemas: extensão: grouptalk: 2.0: Grupo: Descrição|String|

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD para o GroupTalk, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você deseja provisionar para o GroupTalk escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação começa o ciclo de sincronização inicial de todos os usuários e grupos definidos no **Escopo** na seção **Configurações**. O ciclo inicial leva mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar a implantação:

1. Use os [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais usuários foram provisionados com êxito ou não
2. Confira a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o status do ciclo de provisionamento e saber como fechá-la para concluir
3. Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre os estados de quarentena [aqui](../app-provisioning/application-provisioning-quarantine-status.md).
4. Você pode contatar o suporte do GroupTalk para logs de provisionamento adicionais configurados como relatórios personalizados dentro do administrador do GroupTalk. Eles podem fornecer dicas adicionais, por que os usuários e grupos não conseguem provisionar corretamente.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)