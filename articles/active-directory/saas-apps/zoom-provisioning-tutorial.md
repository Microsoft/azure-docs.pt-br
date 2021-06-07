---
title: 'Tutorial: Configurar o Zoom para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o Zoom.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/3/2019
ms.author: Zhchia
ms.openlocfilehash: c87c3692d7e1106e0952b85791da663e03c6001d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96182471"
---
# <a name="tutorial-configure-zoom-for-automatic-user-provisioning"></a>Tutorial: Configurar o Zoom para provisionamento automático de usuário

Este tutorial descreve as etapas que você precisa executar no Zoom e no Azure Active Directory (Azure AD) para configurar o provisionamento automático de usuário. Quando configurado, o Azure AD provisiona e desprovisiona automaticamente usuários e grupos para o [Zoom](https://zoom.us/pricing/) usando o serviço de provisionamento do Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades com suporte
> [!div class="checklist"]
> * Criar usuários no Zoom
> * Remover usuários no Zoom quando eles não precisarem mais de acesso
> * Manter os atributos de usuário sincronizados entre o Azure AD e o Zoom
> * [Logon único](./zoom-tutorial.md) para o Zoom (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](../develop/quickstart-create-new-tenant.md).
* Uma conta de usuário no Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento (por exemplo, Administrador de Aplicativo, Administrador de aplicativos de nuvem, Proprietário de Aplicativo ou Administrador global). 
* [Um locatário do Zoom](https://zoom.us/pricing).
* Uma conta de usuário no Zoom com permissões de Administrador.

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje a implantação do provisionamento
1. Saiba mais sobre [como funciona o serviço de provisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem estará no [escopo de provisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais dados [mapear entre o Azure AD e o Zoom](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-zoom-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configurar o Zoom para dar suporte ao provisionamento com o Azure AD

1. Entre no [Console de Administração do Zoom](https://zoom.us/signin). Navegue até **Avançado > Marketplace de Aplicativo** no painel de navegação esquerdo.

    ![Integrações do Zoom](media/zoom-provisioning-tutorial/zoom01.png)

2. Navegue até **Gerenciar** no canto superior direito da página. 

    ![Captura de tela do Marketplace de Aplicativo do Zoom com a opção Gerenciar realçada.](media/zoom-provisioning-tutorial/zoom02.png)

3. Navegue até o aplicativo do Azure AD criado. 
    
    ![Captura de tela da seção Aplicativos Criados com o aplicativo do Azure A D realçado.](media/zoom-provisioning-tutorial/zoom03.png)

4. Selecione **Credenciais do Aplicativo** no painel de navegação esquerdo.

    ![Captura de tela do painel de navegação esquerdo com a opção Credenciais do Aplicativo realçada.](media/zoom-provisioning-tutorial/zoom04.png)

5. Copie e salve o **Token JWT**. Esse valor será inserido no campo **Token Secreto** na guia Provisionamento do aplicativo Zoom no portal do Azure. Se você precisar de um novo token sem expiração, será necessário reconfigurar o tempo de expiração que vai gerar automaticamente um novo token. 

    ![Captura de tela da página Credenciais do Aplicativo.](media/zoom-provisioning-tutorial/zoom05.png)

## <a name="step-3-add-zoom-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar o Zoom por meio da galeria de aplicativos do Azure AD

Adicione o Zoom por meio da galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento para ele. Se você já tiver configurado o Zoom para SSO, poderá usar o mesmo aplicativo. No entanto, recomendamos que você crie um aplicativo diferente ao testar a integração no início. Saiba mais sobre como adicionar um aplicativo da galeria [aqui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo de provisionamento 

No Azure AD, é possível definir quem estará no escopo de provisionamento com base na atribuição ao aplicativo ou nos atributos do usuário/grupo. Se você optar por definir quem estará no escopo de provisionamento com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você optar por definir quem estará no escopo de provisionamento com base somente em atributos do usuário ou do grupo, poderá usar um filtro de escopo, conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir usuários e grupos ao Zoom, é preciso selecionar uma função diferente do **Acesso Padrão**. Os usuários com a função Acesso Padrão são excluídos do provisionamento e serão marcados como "Não qualificado efetivamente" nos logs de provisionamento. Se a única função disponível no aplicativo for a de acesso padrão, você poderá [atualizar o manifesto do aplicativo](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar outras funções. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de implementar para todos. Quando o escopo de provisionamento é definido para usuários e grupos atribuídos, é possível controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, é possível especificar um [atributo com base no filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-zoom"></a>Etapa 5. Configurar o provisionamento automático de usuário para o Zoom 

Nesta seção, você verá orientações para seguir as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no TestApp com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-zoom-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Zoom no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Zoom**.

    ![O link do Zoom na lista de Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções Gerenciar com a opção Provisionamento destacada.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa Modo de Provisionamento com a opção Automático destacada.](common/provisioning-automatic.png)

5. Na seção **Credenciais de Administrador**, digite `https://api.zoom.us/scim` em **URL do Locatário**. Insira o valor do **Token JWT** recuperado anteriormente no **Token Secreto**. Clique em **Testar Conectividade** para verificar se o Azure AD pode se conectar ao Zoom. Se a conexão falhar, verifique se a sua conta do Zoom tem permissões de administrador e tente novamente.

    ![Provisionamento do Zoom](./media/zoom-provisioning-tutorial/provisioning.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **Mapeamentos**, selecione **Sincronizar usuários do Azure Active Directory com o Zoom**.

9. Examine os atributos de usuário que serão sincronizados do Azure AD com o Zoom na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** serão usados para fazer a correspondência entre as contas de usuário no Zoom para operações de atualização. Se você optar por alterar o [atributo de destino correspondente](../app-provisioning/customize-application-attributes.md), precisará verificar se a API do Zoom é compatível com a filtragem de usuários com base nesse atributo. Selecione o botão **Salvar** para confirmar as alterações.

   |Atributo|Type|
   |---|---|
   |userName|String|
   |ativo|Boolean|
   |name.givenName|String|
   |name.familyName|String|
   |emails[type eq "work"]|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|

10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento do Azure AD para o Zoom, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou grupos que você gostaria de provisionar no Zoom escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação começa o ciclo de sincronização inicial de todos os usuários e grupos definidos no **Escopo** na seção **Configurações**. O ciclo inicial leva mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar a implantação:

1. Use os [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais usuários foram provisionados com êxito ou não
2. Confira a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o status do ciclo de provisionamento e saber como fechá-la para concluir
3. Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre os estados de quarentena [aqui](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Limitações do conector
* O zoom só permite o máximo de 9.999 usuários básicos de atualmente.

## <a name="change-log"></a>Log de alterações
* 14/05/2020 – adicionado suporte para operações de atualização para o atributo emails[type eq "work"].
* 20/10/2020 – suporte adicionado para duas novas funções "Licenciado" e "Local" para substituir as funções existentes "Pro" e "Corp". O suporte para as funções "Pro" e "Corp" será removido no futuro.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)