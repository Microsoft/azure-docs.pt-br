---
title: 'Tutorial: Configurar o SolarWinds Service Desk (anteriormente conhecido como Samanage) para provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o SolarWinds Service Desk (anteriormente conhecido como Samanage).
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2020
ms.author: Zhchia
ms.openlocfilehash: d68811c5e7b92698944d7b1b1736eef864d97d20
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104802294"
---
# <a name="tutorial-configure-solarwinds-service-desk-previously-samanage-for-automatic-user-provisioning"></a>Tutorial: Configurar o SolarWinds Service Desk (anteriormente conhecido como Samanage) para provisionamento automático de usuário

Este tutorial descreve as etapas que você precisa executar no SolarWinds Service Desk e no Azure Active Directory (Azure AD) para configurar o provisionamento automático de usuário. Quando configurado, o Azure AD provisiona e desprovisiona automaticamente usuários e grupos para o [SolarWinds Service Desk](https://www.samanage.com/pricing/) usando o serviço de provisionamento do Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="migrate-to-the-new-solarwinds-service-desk-application"></a>Fazer migração para o novo aplicativo SolarWinds Service Desk

Se você tiver uma integração existente com o SolarWinds Service Desk, confira a seção a seguir sobre alterações futuras. Se você estiver configurando o SolarWinds Service Desk pela primeira vez, poderá ignorar esta seção e passar para as **Funcionalidades com suporte**.

#### <a name="whats-changing"></a>O que está sendo alterado?

* Alterações no Azure AD: O método de autorização para provisionar usuários no Samange tem sido historicamente **Autenticação básica**. Em breve, você verá o método de autorização alterado para **Token secreto de vida útil longa**.


#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>O que preciso fazer para migrar minha integração personalizada existente para o novo aplicativo?

Se você tiver uma integração do SolarWinds Service Desk existente com credenciais de administrador válidas, **nenhuma ação será necessária**. Migramos automaticamente os clientes para o novo aplicativo. Esse processo é feito totalmente nos bastidores. Se as credenciais existentes expirarem ou se você precisar autorizar o acesso ao aplicativo novamente, será necessário gerar um token secreto de vida útil longa. Para gerar um novo token, veja a Etapa 2 deste artigo.


#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>Como saber se meu aplicativo foi migrado? 

Quando seu aplicativo for migrado, na seção **Credenciais de Administrador**, os campos **Nome de Usuário Administrador** e **Senha de Administrador** serão substituídos por um campo **Token Secreto**.

## <a name="capabilities-supported"></a>Funcionalidades com suporte

> [!div class="checklist"]
> * Criar usuários no SolarWinds Service Desk
> * Remover usuários no SolarWinds Service Desk quando eles não precisarem mais de acesso
> * Manter os atributos de usuário sincronizados entre o Azure AD e o SolarWinds Service Desk
> * Provisionar grupos e associações a um grupo no SolarWinds Service Desk
> * [Logon único](./samanage-tutorial.md) para o SolarWinds Service Desk (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](../develop/quickstart-create-new-tenant.md) 
* Uma conta de usuário no Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento (por exemplo, Administrador de Aplicativo, Administrador de aplicativos de nuvem, Proprietário de Aplicativo ou Administrador Global). 
* Um [locatário do SolarWinds Service Desk](https://www.samanage.com/pricing/) com o pacote Professional.
* Uma conta de usuário no SolarWinds Service Desk com permissões de administrador.

> [!Note]
> As funções não devem ser editadas manualmente no Azure Active Directory ao fazer importações de função.

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje a implantação do provisionamento
1. Saiba mais sobre [como funciona o serviço de provisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem estará no [escopo de provisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais dados [mapear entre o Azure AD e o SolarWinds Service Desk](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-solarwinds-service-desk-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configurar o SolarWinds Service Desk para dar suporte ao provisionamento com o Azure AD

Para gerar um token secreto para autenticação, confira [Tutorial de autenticação de tokens para a integração da API](https://help.samanage.com/s/article/Tutorial-Tokens-Authentication-for-API-Integration-1536721557657).

## <a name="step-3-add-solarwinds-service-desk-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar o SolarWinds Service Desk da galeria de aplicativos do Azure AD

Adicione o SolarWinds Service Desk da galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento para ele. Se você já tiver configurado o SolarWinds Service Desk para o SSO, poderá usar o mesmo aplicativo. No entanto, recomendamos que você crie um aplicativo diferente ao testar a integração no início. Saiba mais sobre como adicionar um aplicativo da galeria [aqui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo de provisionamento 

No Azure AD, é possível definir quem estará no escopo de provisionamento com base na atribuição ao aplicativo ou nos atributos do usuário/grupo. Se você optar por definir quem estará no escopo de provisionamento com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você optar por definir quem estará no escopo de provisionamento com base somente em atributos do usuário ou do grupo, poderá usar um filtro de escopo, conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir usuários e grupos ao SolarWinds Service Desk, é preciso selecionar uma função diferente do **Acesso Padrão**. Os usuários com a função Acesso Padrão são excluídos do provisionamento e serão marcados como "Não qualificado efetivamente" nos logs de provisionamento. Se a única função disponível no aplicativo for a de acesso padrão, você poderá [atualizar o manifesto do aplicativo](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar outras funções. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de implementar para todos. Quando o escopo de provisionamento é definido para usuários e grupos atribuídos, é possível controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, é possível especificar um [atributo com base no filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-solarwinds-service-desk"></a>Etapa 5. Configurar o provisionamento automático de usuário para o SolarWinds Service Desk 

Nesta seção, você verá orientações para seguir as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no TestApp com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-solarwinds-service-desk-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o SolarWinds Service Desk no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **SolarWinds Service Desk**.

3. Selecione a guia **Provisionamento**.

    ![Captura de tela que mostra a guia Provisionamento selecionada.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela que mostra o Modo de Provisionamento definido como Automático.](common/provisioning-automatic.png)

5. Na seção **Credenciais de Administrador**, insira `https://api.samanage.com` em **URL do Locatário**.  Insira o valor do token secreto recuperado anteriormente em **Token Secreto**. Selecione **Testar Conectividade** para verificar se o Azure AD pode se conectar ao SolarWinds Service Desk. Se a conexão falhar, verifique se a sua conta do SolarWinds Service Desk tem permissões de Administrador e tente novamente.

    ![Captura de tela que mostra o botão Testar Conectividade selecionado.](./media/samanage-provisioning-tutorial/provisioning.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o SolarWinds Service Desk**.

9. Examine os atributos de usuário sincronizados do Azure AD com o SolarWinds Service Desk na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das contas de usuário no SolarWinds Service Desk para operações de atualização. Se você optar por alterar o [atributo de destino correspondente](../app-provisioning/customize-application-attributes.md), precisará verificar se a API do SolarWinds Service Desk é compatível com a filtragem de usuários com base nesse atributo. Selecione o botão **Salvar** para confirmar as alterações.

      ![Mapeamentos de usuário do Samange](./media/samanage-provisioning-tutorial/user-attributes.png)

10. Na seção **Mapeamentos**, selecione **Sincronizar Grupos do Azure Active Directory com o SolarWinds Service Desk**.

11. Examine os atributos de grupo sincronizados do Azure AD com o SolarWinds Service Desk na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência dos grupos no SolarWinds Service Desk para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

      ![Mapeamentos de grupo do Samange](./media/samanage-provisioning-tutorial/group-attributes.png)

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD no SolarWinds Service Desk, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você gostaria de provisionar para o SolarWinds Service Desk escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, selecione **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação começa o ciclo de sincronização inicial de todos os usuários e grupos definidos no **Escopo** na seção **Configurações**. O ciclo inicial leva mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar a implantação:

1. Use os [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais usuários foram provisionados com êxito ou não
2. Confira a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o status do ciclo de provisionamento e saber como fechá-la para concluir
3. Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre os estados de quarentena [aqui](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="connector-limitations"></a>Limitações do conector

Se você selecionar a opção **Sincronizar todos os usuários e grupos** e configurar um valor para o atributo de **funções** do SolarWinds Service Desk, o valor na caixa **Valor padrão se nulo (é opcional)** precisará ser expresso no seguinte formato:

- {"displayName":"role"}, em que "role" é o valor padrão desejado.

## <a name="change-log"></a>Log de alterações

* 14/09/2020 – Alteração do nome da empresa em dois tutoriais de SaaS do Samanage para o SolarWinds Service Desk (anteriormente conhecido como Samanage) de acordo com https://github.com/ravitmorales.
* 22/04/2020 – Atualização do método de autorização da autenticação básica para o token secreto de vida útil longa.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
