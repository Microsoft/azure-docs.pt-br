---
title: 'Tutorial: Configurar o Console da Oracle Cloud Infrastructure para o provisionamento automático de usuário com o Azure Active Directory   Microsoft Docs'
description: Saiba como provisionar e desprovisionar automaticamente contas de usuário do Azure AD para o Console da Oracle Cloud Infrastructure.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: Zhchia
ms.openlocfilehash: 94de0ca0a5393c891e567e558cbbadd0ca1f453b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97832070"
---
# <a name="tutorial-configure-oracle-cloud-infrastructure-console-for-automatic-user-provisioning"></a>Tutorial: Configurar o Console da Oracle Cloud Infrastructure para o provisionamento automático de usuário

Este tutorial descreve as etapas que você precisará executar no Console da Oracle Cloud Infrastructure e no Azure AD (Active Directory) para configurar o provisionamento automático de usuário. Quando configurado, o Azure AD provisiona e desprovisiona automaticamente usuários e grupos no [Console da Oracle Cloud Infrastructure](https://www.oracle.com/cloud/free/?source=:ow:o:p:nav:0916BCButton&intcmp=:ow:o:p:nav:0916BCButton) usando o serviço de Provisionamento do Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades com suporte
> [!div class="checklist"]
> * Criar usuários no Console da Oracle Cloud Infrastructure
> * Remover usuários no Console da Oracle Cloud Infrastructure quando eles não precisarem mais de acesso
> * Manter os atributos de usuário sincronizados entre o Azure AD e o Console da Oracle Cloud Infrastructure
> * Provisionar grupos e associações a um grupo no Console da Oracle Cloud Infrastructure
> * [Logon único](./oracle-cloud-tutorial.md) no Console da Oracle Cloud Infrastructure (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](../develop/quickstart-create-new-tenant.md) 
* Uma conta de usuário no Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento (por exemplo, Administrador de Aplicativo, Administrador de aplicativos de nuvem, Proprietário de Aplicativo ou Administrador global). 
* Um [locatário](https://www.oracle.com/cloud/sign-in.html?intcmp=OcomFreeTier&source=:ow:o:p:nav:0916BCButton) do Console da Oracle Cloud Infrastructure.
* Uma conta de usuário no Console da Oracle Cloud Infrastructure com permissões de Administrador.

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje a implantação do provisionamento
1. Saiba mais sobre [como funciona o serviço de provisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem estará no [escopo de provisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais dados serão [mapeados entre o Azure AD e o Console da Oracle Cloud Infrastructure](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-oracle-cloud-infrastructure-console-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configurar o Console da Oracle Cloud Infrastructure para dar suporte ao provisionamento com o Azure AD

1. Faça logon no portal de administração do Console da Oracle Cloud Infrastructure. No canto superior esquerdo da tela, procure **Identidade > Federação**.

    ![Administrador do Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/identity.png)

2. Clique na URL exibida na página ao lado do Console do Oracle Identity Cloud Service.

    ![URL do Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/url.png)

3. Clique em **Adicionar Provedor de Identidade** para criar um provedor de identidade. Salve a ID do IdP a ser usada como parte da URL do locatário. Clique no ícone de adição ao lado da guia **Aplicativos** para criar um cliente OAuth e conceder uma AppRole ao Administrador de Domínio de Identidade do IDCS.

    ![Ícone do Oracle Cloud](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/add.png)

4. Siga as capturas de tela abaixo para configurar seu aplicativo. Quando a configuração for concluída, clique em **Salvar**.

    ![Configuração do Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/configuration.png)

    ![Política de Emissão de Token do Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/token-issuance.png)

5. Na guia Configurações do seu aplicativo, expanda a opção **Informações Gerais** para recuperar a ID do cliente e o segredo do cliente.

    ![Geração de token do Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/general-information.png)

6. Para gerar um token secreto Base64, codifique a ID do cliente e o segredo do cliente no formato **ID do cliente:Segredo do Cliente**. Salve o token secreto. Esse valor será inserido no campo **Token Secreto** na guia Provisionamento do aplicativo Console da Oracle Cloud Infrastructure no portal do Azure.

## <a name="step-3-add-oracle-cloud-infrastructure-console-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar o Console da Oracle Cloud Infrastructure por meio da galeria de aplicativos do Azure AD

Adicione o Console da Oracle Cloud Infrastructure por meio da galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento no Console da Oracle Cloud Infrastructure. Se você já configurou o Console da Oracle Cloud Infrastructure para SSO, use o mesmo aplicativo. No entanto, recomendamos que você crie um aplicativo diferente ao testar a integração no início. Saiba mais sobre como adicionar um aplicativo da galeria [aqui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo de provisionamento 

No Azure AD, é possível definir quem estará no escopo de provisionamento com base na atribuição ao aplicativo ou nos atributos do usuário/grupo. Se você optar por definir quem estará no escopo de provisionamento com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você optar por definir quem estará no escopo de provisionamento com base somente em atributos do usuário ou do grupo, poderá usar um filtro de escopo, conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir usuários e grupos ao Console da Oracle Cloud Infrastructure, você precisará selecionar uma função diferente de **Acesso Padrão**. Os usuários com a função Acesso Padrão são excluídos do provisionamento e serão marcados como "Não qualificado efetivamente" nos logs de provisionamento. Se a única função disponível no aplicativo for a de acesso padrão, você poderá [atualizar o manifesto do aplicativo](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar outras funções. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de implementar para todos. Quando o escopo de provisionamento é definido para usuários e grupos atribuídos, é possível controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, é possível especificar um [atributo com base no filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-oracle-cloud-infrastructure-console"></a>Etapa 5. Configurar o provisionamento automático de usuário no Console da Oracle Cloud Infrastructure 

Nesta seção, você verá orientações para seguir as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no TestApp com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-oracle-cloud-infrastructure-console-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Console da Oracle Cloud Infrastructure no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Console da Infraestrutura de Nuvem da Oracle**.

    ![O link do Console da Oracle Cloud Infrastructure na lista Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções Gerenciar com a opção Provisionamento destacada.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa Modo de Provisionamento com a opção Automático destacada.](common/provisioning-automatic.png)

5. Na seção **Credenciais de Administrador**, insira a **URL do Locatário** no formato `https://<IdP ID>.identity.oraclecloud.com/admin/v1`. Por exemplo, `https://idcs-0bfd023ff2xx4a98a760fa2c31k92b1d.identity.oraclecloud.com/admin/v1`. Insira o valor do token secreto recuperado anteriormente em **Token Secreto**. Clique em **Testar Conectividade** para verificar se o Azure AD pode se conectar ao Console da Oracle Cloud Infrastructure. Se a conexão falhar, verifique se a sua conta do Console da Oracle Cloud Infrastructure tem permissões de administrador e tente novamente.

    ![Captura de tela mostrando a caixa de diálogo Credenciais de Administrador, em que você pode inserir a URL do Locatário e o Token Secreto.](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/provisioning.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Console da Oracle Cloud Infrastructure**.

9. Examine os atributos de usuário que serão sincronizados do Azure AD para o Console da Oracle Cloud Infrastructure na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das contas de usuário no Console da Oracle Cloud Infrastructure em operações de atualização. Se você optar por alterar o [atributo de destino correspondente](../app-provisioning/customize-application-attributes.md), precisará verificar se a API do Console da Oracle Cloud Infrastructure dá suporte à filtragem de usuários com base nesse atributo. Selecione o botão **Salvar** para confirmar as alterações.

    |Atributo|Type|
    |---|---|
    |displayName|String|
    |userName|String|
    |ativo|Boolean|
    |título|String|
    |emails[type eq "work"].value|String|
    |preferredLanguage|String|
    |name.givenName|String|
    |name.familyName|String|
    |addresses[type eq "work"].formatted|String|
    |addresses[type eq "work"].locality|String|
    |addresses[type eq "work"].region|String|
    |addresses[type eq "work"].postalCode|String|
    |addresses[type eq "work"].country|String|
    |addresses[type eq "work"].streetAddress|String|
    |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
    |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
    |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
    |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
    |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Referência|
    |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|String|
    |urn:ietf:params:scim:schemas:oracle:idcs:extension:user:User:bypassNotification|Boolean|
    |urn:ietf:params:scim:schemas:oracle:idcs:extension:user:User:isFederatedUser|Boolean|

10. Na seção **Mapeamentos**, selecione **Sincronizar Grupos do Azure Active Directory com o Console da Oracle Cloud Infrastructure**.

11. Examine os atributos de grupo que serão sincronizados do Azure AD para o Console da Oracle Cloud Infrastructure na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência dos grupos no Console da Oracle Cloud Infrastructure em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    | Atributo | Type |
    |--|--|
    | displayName | String |
    | externalId | String |
    | membros | Referência |

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD no Console da Oracle Cloud Infrastructure, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou os grupos que deseja provisionar no Console da Oracle Cloud Infrastructure escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

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
