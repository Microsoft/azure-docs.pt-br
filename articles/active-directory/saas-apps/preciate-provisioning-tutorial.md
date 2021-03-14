---
title: 'Tutorial: configurar o preciate para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o preciate.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: fa640971-87e7-49f2-933b-bc7c95fe51e2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2020
ms.author: Zhchia
ms.openlocfilehash: ab6b17d2db2fd45578f74da9750d3f50dba2636f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101645647"
---
# <a name="tutorial-configure-preciate-for-automatic-user-provisioning"></a>Tutorial: configurar o preciate para o provisionamento automático de usuário

Este tutorial descreve as etapas que você precisa executar tanto no preciate quanto no Azure Active Directory (Azure AD) para configurar o provisionamento automático de usuário. Quando configurado, o Azure AD provisiona e desprovisiona automaticamente usuários e grupos para [preciate](https://www.preciate.org/) usando o serviço de provisionamento do Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades com suporte
> [!div class="checklist"]
> * Criar usuários no preciate
> * Remover usuários no preciate quando eles não exigem mais acesso
> * Manter os atributos de usuário sincronizados entre o Azure AD e o preciate

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](../develop/quickstart-create-new-tenant.md) 
* Uma conta de usuário no Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento (por exemplo, Administrador de Aplicativo, Administrador de aplicativos de nuvem, Proprietário de Aplicativo ou Administrador Global). 
* Um locatário do preciate.
* Uma conta de usuário no preciate com permissões de administrador.

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje a implantação do provisionamento
1. Saiba mais sobre [como funciona o serviço de provisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem estará no [escopo de provisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais dados [mapeados entre o Azure AD e o preciate](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-preciate-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configurar o preciate para dar suporte ao provisionamento com o Azure AD

1.  Faça logon no [portal de administração do preciate](https://preciate.com/web/admin/keys) e navegue até a página **integrações** .

    ![Segredo do preciate](media/preciate-provisioning-tutorial/preciate-secret-path.png)

2.  Selecione o botão **gerar** onde ele indica Active Directory chave secreta de integração. 
 
    ![Preciate gerar](media/preciate-provisioning-tutorial/preciate-secret-generate.png)

3.  Uma nova **chave secreta** será exibida. Copie e salve a **chave secreta**. Anote também a URL do locatário `https://preciate.com/api/v1/scim` . Esses valores serão inseridos no campo **URL do locatário** e **token secreto** na guia provisionamento do aplicativo do preciate na portal do Azure.
 
> [!NOTE]
>Sempre que você clicar no botão gerar, uma nova chave secreta será criada. Isso invalidará imediatamente o atual. Se uma integração já estiver usando ativamente a chave atual, gerar a nova fará com que a integração pare de funcionar até que o token secreto seja atualizado no aplicativo do preciate no Microsoft Azure.


## <a name="step-3-add-preciate-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar o preciate da Galeria de aplicativos do Azure AD

Adicione o preciate da Galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento no preciate. Se você tiver configurado anteriormente o preciate para SSO, poderá usar o mesmo aplicativo. No entanto, recomendamos que você crie um aplicativo diferente ao testar a integração no início. Saiba mais sobre como adicionar um aplicativo da [Galeria](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo de provisionamento 

No Azure AD, é possível definir quem estará no escopo de provisionamento com base na atribuição ao aplicativo ou nos atributos do usuário/grupo. Se você optar por definir quem estará no escopo de provisionamento com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você optar por definir quem estará no escopo de provisionamento com base somente em atributos do usuário ou do grupo, poderá usar um filtro de escopo, conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir usuários e grupos ao preciate, você deve selecionar uma função diferente de **acesso padrão**. Os usuários com a função Acesso Padrão são excluídos do provisionamento e serão marcados como "Não qualificado efetivamente" nos logs de provisionamento. Se a única função disponível no aplicativo for a função de acesso padrão, você poderá [atualizar o manifesto do aplicativo](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar outras funções. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de implementar para todos. Quando o escopo de provisionamento é definido para usuários e grupos atribuídos, é possível controlá-lo atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, é possível especificar um [atributo baseado no filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-preciate"></a>Etapa 5. Configurar o provisionamento automático de usuário para o preciate 

Nesta seção, você verá orientações para seguir as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no TestApp com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-preciate-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para preciate no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **preciate**.

    ![O link do preciate na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia Provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Guia Provisionamento automático](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira a URL do locatário do preciate e o token secreto. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao preciate. Se a conexão falhar, verifique se sua conta do preciate tem permissões de administrador e tente novamente.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para preciate**.

9. Examine os atributos de usuário que são sincronizados do Azure AD para o preciate na seção de **mapeamento de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no preciate para operações de atualização. Se você optar por alterar o [atributo de destino correspondente](../app-provisioning/customize-application-attributes.md), será necessário garantir que a API preciate dê suporte à filtragem de usuários com base nesse atributo. Selecione o botão **Salvar** para confirmar as alterações.

   |Atributo|Type|Com suporte para filtragem|
   |---|---|---|
   |userName|String|&check;|
   |ativo|Boolean|
   |displayName|String|
   |título|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |externalId|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|

10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento do Azure AD para o preciate, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou grupos que você deseja provisionar para o preciate escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação começa o ciclo de sincronização inicial de todos os usuários e grupos definidos no **Escopo** na seção **Configurações**. O ciclo inicial leva mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar a implantação:

* Use os [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais usuários foram provisionados com êxito ou não
* Confira a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o status do ciclo de provisionamento e saber como fechá-la para concluir
* Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre os estados de quarentena [aqui](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)