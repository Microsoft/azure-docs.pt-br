---
title: 'Tutorial: Configurar o MediusFlow para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e descontinuar automaticamente as contas de usuário do Azure AD para o MediusFlow.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/30/2020
ms.author: Zhchia
ms.openlocfilehash: a49258208f7a5945ac71c8f17db56fccfdcd6515
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98661993"
---
# <a name="tutorial-configure-mediusflow-for-automatic-user-provisioning"></a>Tutorial: Configurar o MediusFlow para provisionamento automático de usuário

Este tutorial descreve as etapas que você precisa executar tanto no MediusFlow quanto no Azure Active Directory (Azure AD) para configurar o provisionamento automático de usuário. Quando configurado, o Azure AD provisiona e desprovisiona automaticamente usuários e grupos para o [MediusFlow](https://www.mediusflow.com/) usando o serviço de provisionamento do Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Recursos com suporte
> [!div class="checklist"]
> * Criar usuários no MediusFlow
> * Remover usuários no MediusFlow quando eles não exigem mais acesso
> * Manter os atributos de usuário sincronizados entre o Azure AD e o MediusFlow
> * Provisionar grupos e associações de grupo no MediusFlow
> * Logon único no ServiceNow (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](../develop/quickstart-create-new-tenant.md). 
* Uma conta de usuário no Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento (por exemplo, Administrador de Aplicativo, Administrador de aplicativos de nuvem, Proprietário de Aplicativo ou Administrador Global). 
* Uma assinatura do MediusFlow ativa com garantia de qualidade ou um locatário de produção.
* Uma conta de usuário no MediusFlow com direitos de acesso de administrador para poder realizar a configuração no MediusFlow.
* As empresas adicionadas no locatário MediusFlow para o qual os usuários devem ser provisionados.

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje a implantação de provisionamento
1. Saiba mais sobre [como funciona o serviço de provisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem estará no [escopo de provisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais dados serão [mapeados entre o Azure AD e o ServiceNow](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-mediusflow-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configure o MediusFlow para dar suporte ao provisionamento do Azure AD

### <a name="activate-the-microsoft-365-app-within-mediusflow"></a>Ativar o aplicativo Microsoft 365 no MediusFlow
Comece habilitando o acesso do logon do Azure AD e o recurso de configuração do Azure AD no MediusFlow executando as seguintes etapas:

#### <a name="user-login"></a>Logon de usuário
Para habilitar o fluxo de logon no Microsoft 365/Azure AD consulte [este] artigo https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-login-setup).

#### <a name="user-transfer-configuration"></a>Configuração de transferência de usuário
Para habilitar o portal de configuração dos usuários para o provisionamento no Azure AD, consulte [este](
https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-sync-setup) artigo.

#### <a name="configure-user-provisioning"></a>Configurar provisionamento do usuário

1.  Faça logon para o [console de administração do MediusFlow](https://office365.cloudapp.mediusflow.com/) fornecendo a ID do locatário.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/1-auth.png" alt-text="Captura de tela do console de administração do MediusFlow. A caixa nome do locatário do MediusFlow e o botão Autenticar estão realçados na primeira etapa de integração." border="false":::

2. Verificar a conexão com o MediusFlow.

    ![Verificar](./media/mediusflow-provisioning-tutorial/2-verify-connection.png)

3. Forneça a ID de locatário do Microsoft Azure AD.

    ![fornecer a ID de locatário](./media/mediusflow-provisioning-tutorial/3-provide-azuread-tenantid.png)

   Você pode ler mais informações nas [perguntas frequentes](https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#how-do-i-get-the-azure-tenant-id) sobre como encontrá-la.

4. Salve a configuração.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/4-save-config.png" alt-text="Captura de tela do console de administração do MediusFlow que mostra a quarta etapa de integração. O botão Salvar configuração está realçado." border="false":::

5. Selecione o provisionamento de usuário e clique em **OK**.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/5-select-user-provisioning.png" alt-text="Captura de tela do console de administração do MediusFlow que mostra a quinta etapa de integração. Os botões Usar provisionamento de usuário e OK estão realçados." border="false":::

6. Clique em **Gerar Chave Secreta**. Copie e salve esse valor. Ele será inserido no campo **Token secreto** na guia **Provisionamento** do aplicativo MediusFLow no portal do Azure.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/6-create-secret-1.png" alt-text="Captura de tela da guia Configuração de provisionamento de usuário no console de administração do MediusFlow. Os botões Gerar chave secreta e Copiar estão realçados." border="false":::

7. Clique em **OK**.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/7-confirm-secret.png" alt-text="Captura de tela do console de administração do MediusFlow com uma notificação informando aos usuários para clicar em OK para gerar uma nova chave secreta. O botão OK está realçado." border="false":::

8. Para que os usuários sejam importados com um conjunto predefinido de funções, empresas e outras configurações gerais no MediusFlow, você precisará configurá-lo primeiro. Comece adicionando a configuração clicando em **Adicionar nova configuração**.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/8-configure-user-configuration-1.png" alt-text="Captura de tela da guia Configuração de provisionamento de usuário no console de administração do MediusFlow. O botão Adicionar nova configuração está realçado." border="false":::

9. Habilitar as configurações padrão para os usuários. Nessa exibição, é possível definir o atributo padrão. Se as configurações padrão estiverem ok, será suficiente fornecer apenas um nome de empresa válido. Como essas definições de configuração são buscadas no Mediusflow, elas precisam ser configuradas primeiro. Para obter mais informações, consulte a seção **Pré-requisitos** neste artigo.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/9-configure-user-config-detail-1.png" alt-text="Captura de tela da janela Adicionar nova configuração do MediusFlow. Muitas configurações estão visíveis, incluindo configurações de localidade, um filtro e funções de usuário." border="false":::

10. Clique em **Salvar** para salvar a configuração do usuário.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/10-done-1.png" alt-text="Captura de tela da guia Configuração de provisionamento de usuário no console de administração do MediusFlow. O botão Salvar está realçado." border="false":::

11. Para obter o link de provisionamento de usuário, clique no link **Copiar Link SCIM**. Copie e salve esse valor. Esse valor é inserido no campo **URL do locatário** na guia **Provisionamento** do aplicativo MediusFlow no portal do Azure.
 
    :::image type="content" source="./media/mediusflow-provisioning-tutorial/11-get-scim-link.png" alt-text="Captura de tela da guia Configuração de provisionamento de usuário no console de administração do MediusFlow. O botão copiar link do S C I M está realçado." border="false":::

## <a name="step-3-add-mediusflow-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicione o MediusFlow por meio da galeria de aplicativos do Azure AD

Adicione o MediusFlow por meio da galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento dele. Se você já tiver configurado o MediusFlow para SSO, poderá usar o mesmo aplicativo. No entanto, é recomendável que você crie um aplicativo diferente ao testar a integração no início. Saiba mais sobre como adicionar um aplicativo da galeria [aqui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo de provisionamento 

No Azure AD, é possível definir quem estará no escopo de provisionamento com base na atribuição ao aplicativo ou nos atributos do usuário/grupo. Se você optar por definir quem estará no escopo de provisionamento com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você optar por definir quem estará no escopo de provisionamento com base somente em atributos do usuário ou do grupo, poderá usar um filtro de escopo, conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir usuários e grupos ao MediusFlow, é preciso selecionar uma função diferente do **Acesso Padrão**. Os usuários com a função de Acesso Padrão são excluídos do provisionamento e serão marcados como não qualificados efetivamente nos logs de provisionamento. Se a única função disponível no aplicativo for a de acesso padrão, você poderá [atualizar o manifesto do aplicativo](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar outras funções. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de implementar para todos. Quando o escopo de provisionamento é definido para usuários e grupos atribuídos, é possível controlá-lo atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, é possível especificar um [atributo baseado no filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-mediusflow"></a>Etapa 5. Configure o provisionamento automático de usuários para o MediusFlow 

Nesta seção, você verá orientações para seguir as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no TestApp com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-mediusflow-in-azure-ad"></a>Para configurar o provisionamento automático de usuários para o MediusFlow no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **MediusFlow**.

    ![O link do MediusFlow na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções Gerenciar com a opção Provisionamento destacada.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa Modo de Provisionamento com a opção Automático destacada.](common/provisioning-automatic.png)

5. Na seção **Credenciais de Administrador**, insira o valor da URL do locatário recuperado anteriormente em **URL do locatário**. Insira o valor do token secreto recuperado anteriormente em **Token Secreto**. Clique em **Testar Conexão** para verificar se o Azure AD pode se conectar ao MediusFlow. Se a conexão falhar, verifique se sua conta no MediusFlow tem permissões de administrador e tente novamente.

      ![Captura de tela mostrando a caixa de diálogo Credenciais de Administrador, em que você pode inserir a URL do Locatário e o Token Secreto.](./media/mediusflow-provisioning-tutorial/provisioning.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory no MediusFlow**.

9. Examine os atributos de usuário que serão sincronizados do Azure AD no MediusFlow na seção **Mapeamento de atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário do MediusFlow em operações de atualização. Se você optar por alterar o [atributo de destino correspondente](../app-provisioning/customize-application-attributes.md), precisará garantir que a API do MediusFlow seja compatível com a filtragem de usuários com base nesse atributo. Selecione o botão **Salvar** para confirmar as alterações.

   |Atributo|Type|Com suporte para filtragem|
   |---|---|---|
   |userName|String|&check;|
   |emails[type eq "work"].value|String|
   |name.displayName|String|
   |ativo|Boolean|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |externalId|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Referência|
   |urn:ietf:params:scim:schemas:extension:medius:2.0:User:configurationFilter|String|
   |urn:ietf:params:scim:schemas:extension:medius:2.0:User:identityProvider|String|
   |urn:ietf:params:scim:schemas:extension:medius:2.0:User:nameIdentifier|String|
   |urn:ietf:params:scim:schemas:extension:medius:2.0:User:customFieldText1|String|
   |urn:ietf:params:scim:schemas:extension:medius:2.0:User:customFieldText2|String|
   |urn:ietf:params:scim:schemas:extension:medius:2.0:User:customFieldText3|String|
   |urn:ietf:params:scim:schemas:extension:medius:2.0:User:customFieldText4|String|
   |urn:ietf:params:scim:schemas:extension:medius:2.0:User:customFieldText5|String|


10. Na seção **Mapeamentos**, selecione **Sincronizar Grupos do Azure Active Directory com o MediusFlow**.

11. Examine os atributos de grupo que serão sincronizados do Azure AD no MediusFlow na seção **Mapeamento de atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder os grupos do MediusFlow em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    | Atributo | Type |
    |--|--|
    | displayName | String |
    | externalID | String |
    | membros | Referência |

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD no MediusFlow, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você gostaria de provisionar no MediusFlow escolhendo os valores desejados em **Escopo** na seção **Configurações**.

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

* 21/01/2021 – Os atributos de extensão personalizados foram adicionados e incluem: **configurationFilter**, **identityProvider**, **nameIdentifier**, **customFieldText1**, **customFieldText2**, **customFieldText3**, **customFieldText4** e **customFieldText5**.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)