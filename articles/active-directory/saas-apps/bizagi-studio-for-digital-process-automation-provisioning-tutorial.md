---
title: 'Tutorial: configurar o BizAgi Studio para a automação de processos digitais para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o BizAgi Studio para a automação de processo digital.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 2fbff65a-5345-4c08-a6c7-60b80d867a3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2020
ms.author: Zhchia
ms.openlocfilehash: 72e021f47bb8db4dedf0e434d0d94bb2118a4c00
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98728145"
---
# <a name="tutorial-configure-bizagi-studio-for-digital-process-automation-for-automatic-user-provisioning"></a>Tutorial: configurar o BizAgi Studio para a automação de processos digitais para o provisionamento automático de usuário

Este tutorial descreve as etapas que você precisa executar no BizAgi Studio para automação de processo digital e Azure Active Directory (AD do Azure) para configurar o provisionamento automático de usuário. Quando configurado para fazer isso, o Azure AD provisiona e desprovisiona automaticamente usuários e grupos para o [BizAgi Studio para a automação de processo digital](https://www.bizagi.com/) usando o serviço de provisionamento do Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades com suporte
> [!div class="checklist"]
> * Criar usuários no BizAgi Studio para automação de processo digital
> * Remova os usuários no BizAgi Studio para automação de processo digital quando eles não exigem mais acesso
> * Manter os atributos de usuário sincronizados entre o Azure AD e o BizAgi Studio para a automação de processo digital
> * [Logon único](./bizagi-studio-for-digital-process-automation-tutorial.md) no BizAgi Studio para automação de processo digital (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já possui o seguinte:

* [Um locatário do Azure AD](../develop/quickstart-create-new-tenant.md). 
* Uma conta de usuário no Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento. Os exemplos incluem administrador de aplicativos, administrador de aplicativos de nuvem, proprietário do aplicativo ou administrador global. 
* BizAgi Studio para a automação de processo digital versão 11.2.4.2 X ou posterior.

## <a name="plan-your-provisioning-deployment"></a>Planeje a implantação do provisionamento
Siga estas etapas para planejar:

1. Saiba mais sobre [como funciona o serviço de provisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem estará [no escopo do provisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais dados [mapear entre o Azure AD e o BizAgi Studio para a automação de processo digital](../app-provisioning/customize-application-attributes.md). 

## <a name="configure-to-support-provisioning-with-azure-ad"></a>Configurar o para oferecer suporte ao provisionamento com o Azure AD
Para configurar o BizAgi Studio para a automação de processo digital para dar suporte ao provisionamento com o Azure AD, siga estas etapas:

1. Entre no portal de trabalho como um usuário com **permissões de administrador**.

2. Vá para segurança do **administrador**  >    >  **aplicativos OAuth 2**.

   ![Captura de tela de BizAgi, com aplicativos OAuth 2 realçados.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/admin.png)

3. Selecione **Adicionar**.
4. Para **tipo de concessão**, selecione **token de portador**. Para **escopo permitido**, selecione **API** e **sincronização de usuário**. Em seguida, selecione **Salvar**.

   ![Captura de tela do aplicativo de registro, com tipo de concessão e escopo permitido realçados.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/token.png)

5. Copie e salve o **segredo do cliente**. Na portal do Azure, para seu aplicativo do BizAgi Studio para automação de processo digital, na guia **provisionamento** , o valor segredo do cliente é inserido no campo **token secreto** .

   ![Captura de tela do OAuth, com o segredo do cliente highlighed.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/secret.png)

## <a name="add-the-application-from-the-azure-ad-gallery"></a>Adicionar o aplicativo da galeria do Azure AD

Para começar a gerenciar o provisionamento para o BizAgi Studio para automação de processo digital, adicione o aplicativo da Galeria de aplicativos do Azure AD. Se você tiver configurado anteriormente o BizAgi Studio para a automação de processo digital para logon único, poderá usar o mesmo aplicativo. No entanto, quando você estiver testando a integração inicialmente, deverá criar um aplicativo separado. Para obter mais informações, consulte [início rápido: adicionar um aplicativo ao seu locatário do Azure Active Directory (Azure AD)](../manage-apps/add-application-portal.md). 

## <a name="define-who-is-in-scope-for-provisioning"></a>Definir quem está no escopo para provisionamento 

Com o serviço de provisionamento do Azure AD, você pode fazer o escopo que é provisionado com base na atribuição para o aplicativo, com base nos atributos do usuário e do grupo, ou ambos. Se você definir o escopo com base na atribuição, consulte as etapas em [atribuir ou Cancelar atribuição de usuários e grupos para um aplicativo usando o API do Graph](../manage-apps/assign-user-or-group-access-portal.md) para atribuir usuários e grupos ao aplicativo. Se o escopo for baseado apenas em atributos do usuário ou grupo, você poderá usar um filtro de escopo. Para obter mais informações, consulte [provisionamento de aplicativo baseado em atributo com filtros de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

Observe os seguintes pontos sobre o escopo:

* Quando você estiver atribuindo usuários e grupos ao BizAgi Studio para automação de processo digital, deverá selecionar uma função diferente de **acesso padrão**. Os usuários com a função de acesso padrão são excluídos do provisionamento e são marcados nos logs de provisionamento, conforme serão marcados como não tendo sido efetivamente qualificados. Se a única função disponível no aplicativo for a de acesso padrão, você poderá [atualizar o manifesto do aplicativo](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar mais funções. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de implementar para todos. Quando o escopo do provisionamento é definido como usuários e grupos atribuídos, você pode controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido como todos os usuários e grupos, você pode especificar um [filtro de escopo baseado em atributo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="configure-automatic-user-provisioning"></a>Configurar o provisionamento automático de usuário 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e grupos. Você está fazendo isso em seu aplicativo de teste, com base em atribuições de usuário e de grupo no Azure AD.

### <a name="configure-automatic-user-provisioning-for-bizagi-studio-for-digital-process-automation-in-azure-ad"></a>Configurar o provisionamento automático de usuário para o BizAgi Studio para automação de processo digital no Azure AD

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** > **Todos os aplicativos**.

    ![Captura de tela da portal do Azure, com aplicativos empresariais e todos os aplicativos realçados.](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **BizAgi Studio para automação de processo digital**.

3. Selecione a guia **Provisionamento**.

    ![Captura de tela de gerenciar opções, com provisionamento realçado.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Controle de modo de provisionamento Screenshotof, com realce automático.](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira a URL do locatário e o token secreto para o BizAgi Studio para automação de processo digital. 

      * **URL do locatário:** Insira o ponto de extremidade BizAgi SCIM, com a seguinte estrutura:  `<Your_Bizagi_Project>/scim/v2/` .
         Por exemplo: `https://my-company.bizagi.com/scim/v2/`.

      * **Token secreto:** Esse valor é recuperado da etapa discutida anteriormente neste artigo.

      Para garantir que o Azure AD possa se conectar ao BizAgi Studio para a automação de processo digital, selecione **testar conexão**. Se a conexão falhar, verifique se sua conta do BizAgi Studio para a automação de processos digitais tem permissões de administrador e tente novamente.

   ![Captura de tela de credenciais de administrador, com conexão de teste realçada.](common/provisioning-testconnection-tenanturltoken.png)

6. Para **email de notificação**, insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento. Selecione a opção para **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Captura de tela das opções de email de notificação.](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para o BizAgi Studio para automação de processo digital**.

9. Na seção **mapeamento de atributo** , examine os atributos de usuário que são sincronizados do Azure ad para o BizAgi Studio para a automação de processo digital. Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no BizAgi Studio para a automação de processo digital para operações de atualização. Se você alterar o [atributo de destino correspondente](../app-provisioning/customize-application-attributes.md), deverá garantir que o BizAgi Studio para a API de automação de processo digital dê suporte à filtragem de usuários com base nesse atributo. Para confirmar eventuais alterações, selecione **Salvar**.

   |Atributo|Type|Com suporte para filtragem|
   |---|---|---|
   |userName|String|&check;|
   |ativo|Boolean|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |phoneNumbers[type eq "mobile"].value|String|

   Atributos de extensão personalizados podem ser adicionados navegando para **Mostrar opções avançadas > editar lista de atributos para BizAgi**. Os atributos de extensão personalizados devem ser prefixados com **urn: IETF: params: SCIM: esquemas: extensão: BizAgi: 2.0: UserProperties:**. Por exemplo, se o atributo de extensão personalizado for **IdentificationNumber**, o atributo deverá ser adicionado como **urn: IETF: params: SCIM: esquemas: extensão: BizAgi: 2.0: UserProperties: IdentificationNumber**. Para confirmar eventuais alterações, selecione **Salvar**.
   
    ![Editar lista de atributos.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/edit.png)  

   Mais informações sobre como adicionar atributos personalizados podem ser encontradas em [Personalizar atributos de aplicativo](../app-provisioning/customize-application-attributes.md).

> [!NOTE]
> Somente as propriedades básicas de tipo têm suporte (por exemplo, Cadeia de caracteres, inteiro, booliano, DateTime, etc.). As propriedades vinculadas a tabelas paramétricas ou vários tipos ainda não são suportadas.

10. Para configurar filtros de escopo, consulte o [tutorial de filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento do Azure AD para o BizAgi Studio para a automação de processo digital, na seção **configurações** , altere o **status de provisionamento** para **ativado**.

    ![Captura de tela de alternância de status de provisionamento.](common/provisioning-toggle-on.png)

12. Defina os usuários e grupos que você deseja provisionar para o BizAgi Studio para automação de processo digital. Na seção **configurações** , escolha os valores desejados no **escopo**.

    ![Captura de tela das opções de escopo.](common/provisioning-scope.png)

13. Quando estiver pronto para fazer o provisionamento, selecione **Salvar**.

    ![Captura de tela de salvar controle.](common/provisioning-configuration-save.png)

Essa operação começa o ciclo de sincronização inicial de todos os usuários e grupos definidos no **Escopo** na seção **Configurações**. O ciclo inicial leva mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. 

## <a name="monitor-your-deployment"></a>Monitorar a implantação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar sua implantação:

- Use os [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais usuários foram provisionados com êxito ou não.
- Verifique a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o status do ciclo de provisionamento e como fechá-lo para conclusão.
- Se a configuração de provisionamento estiver em um estado não íntegro, o aplicativo entrará em quarentena. Para obter mais informações, consulte [provisionamento de aplicativos em status de quarentena](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)