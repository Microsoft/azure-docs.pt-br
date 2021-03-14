---
title: 'Tutorial: configurar o CybSafe para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o CybSafe.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 7255fe44-1662-4ae4-9ff3-9492911b7ce0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2020
ms.author: Zhchia
ms.openlocfilehash: b79a63d16229c761b29776667c771e940d8f69d7
ms.sourcegitcommit: e3151d9b352d4b69c4438c12b3b55413b4565e2f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2021
ms.locfileid: "100527115"
---
# <a name="tutorial-configure-cybsafe-for-automatic-user-provisioning"></a>Tutorial: configurar o CybSafe para o provisionamento automático de usuário

Este tutorial descreve as etapas que você precisa executar tanto no CybSafe quanto no Azure Active Directory (Azure AD) para configurar o provisionamento automático de usuário. Quando configurado, o Azure AD provisiona e desprovisiona automaticamente usuários e grupos para [CybSafe](https://app.cybsafe.com/login) usando o serviço de provisionamento do Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades com suporte
> [!div class="checklist"]
> * Criar usuários no CybSafe
> * Remover usuários no CybSafe quando eles não exigem mais acesso
> * Manter os atributos de usuário sincronizados entre o Azure AD e o CybSafe
> * Provisionar grupos e associações de grupo no CybSafe

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](../develop/quickstart-create-new-tenant.md) 
* Uma conta de usuário no Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento (por exemplo, Administrador de Aplicativo, Administrador de aplicativos de nuvem, Proprietário de Aplicativo ou Administrador Global). 
* Uma conta de administrador do [CybSafe](https://app.cybsafe.com/login) com uma assinatura do Enterprise.

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje a implantação do provisionamento
1. Saiba mais sobre [como funciona o serviço de provisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem estará no [escopo de provisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais dados [mapeados entre o Azure AD e o CybSafe](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-cybsafe-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configurar o CybSafe para dar suporte ao provisionamento com o Azure AD

1. A **URL do locatário** para CybSafe é: `https://app.cybsafe.com/scim/v2/` . Esse valor será inserido na guia provisionamento do seu aplicativo CybSafe no portal do Azure.
2. Faça logon no [CybSafe](https://app.cybsafe.com/login) com sua conta de administrador.
3. Navegue até [configurações->gerenciamento de identidades](https://app.cybsafe.com/settings/identity-management). 
4. Clique em **gerar token** na seção provisionamento de usuário. Esse valor será inserido na guia provisionamento do seu aplicativo CybSafe no portal do Azure.

## <a name="step-3-add-cybsafe-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar o CybSafe da Galeria de aplicativos do Azure AD

Adicione o CybSafe da Galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento no CybSafe. Se você tiver configurado anteriormente o CybSafe para SSO, poderá usar o mesmo aplicativo. No entanto, é recomendável que você crie um aplicativo diferente ao testar a integração no início. Saiba mais sobre como adicionar um aplicativo da galeria [aqui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo de provisionamento 

No Azure AD, é possível definir quem estará no escopo de provisionamento com base na atribuição ao aplicativo ou nos atributos do usuário/grupo. Se você optar por definir quem estará no escopo de provisionamento com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você optar por definir quem estará no escopo de provisionamento com base somente em atributos do usuário ou do grupo, poderá usar um filtro de escopo, conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir usuários e grupos ao CybSafe, você deve selecionar uma função diferente de **acesso padrão**. Os usuários com a função Acesso Padrão são excluídos do provisionamento e serão marcados como "Não qualificado efetivamente" nos logs de provisionamento. Se a única função disponível no aplicativo for a de acesso padrão, você poderá [atualizar o manifesto do aplicativo](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar outras funções. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de implementar para todos. Quando o escopo de provisionamento é definido para usuários e grupos atribuídos, é possível controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, é possível especificar um [atributo com base no filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-cybsafe"></a>Etapa 5. Configurar o provisionamento automático de usuário para o CybSafe 

Nesta seção, você verá orientações para seguir as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no TestApp com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-cybsafe-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para CybSafe no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **CybSafe**.

    ![O link do CybSafe na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia Provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Guia Provisionamento automático](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira a URL do locatário do CybSafe e o token secreto. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao CybSafe. Se a conexão falhar, verifique se sua conta do CybSafe tem permissões de administrador e tente novamente.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para CybSafe**.

9. Examine os atributos de usuário que são sincronizados do Azure AD para o CybSafe na seção de **mapeamento de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no CybSafe para operações de atualização. Se você optar por alterar o [atributo de destino correspondente](../app-provisioning/customize-application-attributes.md), será necessário garantir que a API CybSafe dê suporte à filtragem de usuários com base nesse atributo. Selecione o botão **Salvar** para confirmar as alterações.

   |Atributo|Type|Com suporte para filtragem|
   |---|---|---|
   |userName|String|&check;|
   |ativo|Boolean|
   |displayName|String|
   |título|String|
   |preferredLanguage|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |name.honorificPrefix|String|
   |name.honorificSuffix|String|
   |nickName|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |emails[type eq "work"].value|String|
   |localidade|String|
   |timezone|String|
   |userType|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|String|
   

10. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory para CybSafe**.

11. Examine os atributos de grupo que são sincronizados do Azure AD para o CybSafe na seção de **mapeamento de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos no CybSafe para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

      |Atributo|Type|Com suporte para filtragem|
      |---|---|---|
      |displayName|String|&check;|
      |membros|Referência|

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD para o CybSafe, altere o **status de provisionamento** para **ativado** na seção **configurações** .

      ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você deseja provisionar para o CybSafe escolhendo os valores desejados no **escopo** na seção **configurações** .

      ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

      ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação começa o ciclo de sincronização inicial de todos os usuários e grupos definidos no **Escopo** na seção **Configurações**. O ciclo inicial leva mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar a implantação:

1. Use os [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais usuários foram provisionados com êxito ou não
2. Confira a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o status do ciclo de provisionamento e saber como fechá-la para concluir
3. Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre os estados de quarentena [aqui](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="change-log"></a>Log de alterações

* 02/15/2021- **Departamento** de atributos de extensão da empresa do usuário, **divisão** e **organização** foram adicionados.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)