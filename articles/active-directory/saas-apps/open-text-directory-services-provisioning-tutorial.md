---
title: 'Tutorial: Configurar o OpenText Directory Services para provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o OpenText Directory Services.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: ad55ba5f-c56c-4ed0-bdfd-163d2883ed80
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: Zhchia
ms.openlocfilehash: 2f31eddab1070d073d3fd5a4761dad597e42a2e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181876"
---
# <a name="tutorial-configure-opentext-directory-services-for-automatic-user-provisioning"></a>Tutorial: Configurar o OpenText Directory Services para provisionamento automático de usuário

Este tutorial descreve as etapas que você precisa executar no OpenText Directory Services e no Azure Active Directory (Azure AD) para configurar o provisionamento automático de usuário. Quando configurado, o Azure AD provisiona e desprovisiona automaticamente usuários e grupos para o OpenText Directory Services usando o serviço de provisionamento do Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades com suporte
> [!div class="checklist"]
> * Criar usuários no OpenText Directory Services
> * Remover usuários no OpenText Directory Services quando eles não precisarem mais de acesso
> * Manter os atributos de usuário sincronizados entre o Azure AD e o OpenText Directory Services
> * Provisionar grupos e associações a um grupo no OpenText Directory Services
> * [Logon único](./opentext-directory-services-tutorial.md) para o OpenText Directory Services (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](../develop/quickstart-create-new-tenant.md) 
* Uma conta de usuário no Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento (por exemplo, Administrador de Aplicativo, Administrador de aplicativos de nuvem, Proprietário de Aplicativo ou Administrador Global). 
* Uma instalação do OTDS acessível no Azure AD.

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje a implantação do provisionamento
1. Saiba mais sobre [como funciona o serviço de provisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem estará no [escopo de provisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais dados [mapear entre o Azure AD e o OpenText Directory Services](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-opentext-directory-services-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configurar o OpenText Directory Services para dar suporte ao provisionamento com o Azure AD

> [!NOTE]
> As etapas a seguir se aplicam a uma instalação do OpenText Directory Services. Elas não se aplicam a locatários do OpenText CoreShare ou do OpenText OT2.

1. Crie um **cliente OAuth** confidencial dedicado.
2. Defina um longo **Tempo de vida do token de acesso**.

      ![Vida do token de acesso](media/open-text-directory-services-provisioning-tutorial/token-life.png)

3. Não especifique nenhuma URL de redirecionamento. Elas não são necessárias. 
4. O OTDS vai gerar e exibir o **segredo do cliente**. Salve a **ID do cliente** e o **segredo do cliente** em uma localização segura.

      ![Segredo do cliente](media/open-text-directory-services-provisioning-tutorial/client-secret.png)

5. Crie uma partição para os usuários e grupos a serem sincronizados do Azure AD.

      ![Página de partição](media/open-text-directory-services-provisioning-tutorial/partition.png)

6. Conceda direitos administrativos ao cliente OAuth que você criou na partição que você usará para os usuários e grupos do Azure AD que estão sendo sincronizados.    
      * Partição -> Ações -> Editar Administradores

      ![Página do administrator](media/open-text-directory-services-provisioning-tutorial/administrator.png)

5. Um token secreto precisa ser recuperado e configurado no Azure AD. Qualquer aplicativo cliente HTTP pode ser usado para isso. Abaixo estão as etapas a serem recuperadas usando o aplicativo de API do Swagger incluído no OTDS.
      * Em um navegador da Web, acesse {URL do OTDS}/otdsws/oauth2
      * Vá para /token e clique no ícone de cadeado na parte superior direita. Insira a ID e o segredo do Cliente OAuth recuperados anteriormente como o nome de usuário e a senha respectivamente. Clique em Autorizar.

      ![Botão Autorização](media/open-text-directory-services-provisioning-tutorial/authorization.png)

6. Selecione **client_credentials** para o grant_type e clique em **Executar**.

      ![Botão Executar](media/open-text-directory-services-provisioning-tutorial/execute.png)

7. O token de acesso na resposta deve ser usado no campo **Token Secreto** no Azure AD.

      ![Token de acesso](media/open-text-directory-services-provisioning-tutorial/access-token.png)

## <a name="step-3-add-opentext-directory-services-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar o OpenText Directory Services da galeria de aplicativos do Azure AD

Adicione o OpenText Directory Services da galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento para ele. Se você já tiver configurado o OpenText Directory Services para SSO, poderá usar o mesmo aplicativo. No entanto, recomendamos que você crie um aplicativo diferente ao testar a integração no início. Saiba mais sobre como adicionar um aplicativo da galeria [aqui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo de provisionamento 

No Azure AD, é possível definir quem estará no escopo de provisionamento com base na atribuição ao aplicativo ou nos atributos do usuário/grupo. Se você optar por definir quem estará no escopo de provisionamento com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você optar por definir quem estará no escopo de provisionamento com base somente em atributos do usuário ou do grupo, poderá usar um filtro de escopo, conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir usuários e grupos ao OpenText Directory Services, é preciso selecionar uma função diferente do **Acesso Padrão**. Os usuários com a função Acesso Padrão são excluídos do provisionamento e serão marcados como "Não qualificado efetivamente" nos logs de provisionamento. Se a única função disponível no aplicativo for a de acesso padrão, você poderá [atualizar o manifesto do aplicativo](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar outras funções. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de implementar para todos. Quando o escopo de provisionamento é definido para usuários e grupos atribuídos, é possível controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, é possível especificar um [atributo com base no filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-opentext-directory-services"></a>Etapa 5. Configurar o provisionamento automático de usuário para o OpenText Directory Services 

Nesta seção, você verá orientações para seguir as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no TestApp com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-opentext-directory-services-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o OpenText Directory Services no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Serviços de Diretório OpenText**.

    ![O link do OpenText Directory Services na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia Provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Guia Provisionamento automático](common/provisioning-automatic.png)

5. Na seção **Credenciais de Administrador**, insira a URL do Locatário do OpenText Directory Services
   * URL do locatário não específica: {URL do OTDS}/scim/{NomeDaPartição}
   * URL do locatário específica: {URL do OTDS}/otdstenant/{IDdoLocatário}/scim/{NomeDaPartição}

6. Insira o Token Secreto recuperado na Etapa 2. Clique em **Testar Conectividade** para verificar se o Azure AD pode se conectar ao OpenText Directory Services. Se a conexão falhar, verifique se a sua conta do OpenText Directory Services tem permissões de Administrador e tente novamente.

      ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o OpenText Directory Services**.

9. Examine os atributos de usuário sincronizados do Azure AD com o OpenText Directory Services na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das contas de usuário no OpenText Directory Services para operações de atualização. Se você optar por alterar o [atributo de destino correspondente](../app-provisioning/customize-application-attributes.md), precisará verificar se a API do OpenText Directory Services é compatível com a filtragem de usuários com base nesse atributo. Selecione o botão **Salvar** para confirmar as alterações.

   |Atributo|Type|
   |---|---|
   |userName|String|
   |ativo|Boolean|
   |displayName|String|
   |título|String|
   |emails[type eq "work"].value|String|
   |preferredLanguage|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |addresses[type eq "work"].formatted|String|
   |addresses[type eq "work"].streetAddress|String|
   |addresses[type eq "work"].locality|String|
   |addresses[type eq "work"].region|String|
   |addresses[type eq "work"].postalCode|String|
   |addresses[type eq "work"].country|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "fax"].value|String|
   |externalId|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Referência| 

10. Na seção **Mapeamentos**, selecione **Sincronizar Grupos do Azure Active Directory com o OpenText Directory Services**.

11. Examine os atributos de grupo sincronizados do Azure AD com o OpenText Directory Services na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência dos grupos no OpenText Directory Services para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

      |Atributo|Type|
      |---|---|
      |displayName|String|
      |externalId|String|
      |membros|Referência|

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD no OpenText Directory Services, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você gostaria de provisionar para o OpenText Directory Services escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação começa o ciclo de sincronização inicial de todos os usuários e grupos definidos no **Escopo** na seção **Configurações**. O ciclo inicial leva mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar a implantação:

1. Use os [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais usuários foram provisionados com êxito ou não
2. Confira a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o status do ciclo de provisionamento e saber como fechá-la para concluir
3. Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre os estados de quarentena [aqui](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)