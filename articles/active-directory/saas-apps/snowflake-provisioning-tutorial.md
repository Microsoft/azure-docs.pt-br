---
title: 'Tutorial: Configurar o Snowflake para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no Snowflake.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 9190585face277d92ef86c9bfa045d6d8c05b01c
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98734858"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>Tutorial: Configurar o Snowflake para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Snowflake e no Azure AD (Azure Active Directory) a fim de configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no [Snowflake](https://www.Snowflake.com/pricing/). Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md). 


> [!NOTE]
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="capabilities-supported"></a>Funcionalidades com suporte
> [!div class="checklist"]
> * Criar usuários no Snowflake
> * Remover usuários no Snowflake quando eles não precisarem mais de acesso
> * Manter os atributos de usuário sincronizados entre o Azure AD e o Snowflake
> * Provisionar grupos e associações a um grupo no Snowflake
> * [Logon único](./snowflake-tutorial.md) para o Snowflake (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](../develop/quickstart-create-new-tenant.md).
* Uma conta de usuário no Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento (por exemplo, Administrador de Aplicativo, Administrador de aplicativos de nuvem, Proprietário de Aplicativo ou Administrador global).
* [Um locatário do Snowflake](https://www.Snowflake.com/pricing/).
* Uma conta de usuário no Snowflake com permissões de Administrador.

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje a implantação do provisionamento
1. Saiba mais sobre [como funciona o serviço de provisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem estará no [escopo de provisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais dados serão [mapeados entre o Azure AD e o Snowflake](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-snowflake-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configurar o Snowflake para dar suporte ao provisionamento com o Azure AD

Antes de configurar o Snowflake para o provisionamento automático de usuário com o Azure AD, você precisará habilitar o provisionamento do SCIM no Snowflake.

1. Entre no console de administração do Snowflake. Insira a consulta mostrada abaixo na planilha realçada e clique em **Executar**.

    ![Console de administração do Snowflake](media/Snowflake-provisioning-tutorial/image00.png)

2.  Um token de acesso do SCIM será gerado para o locatário do Snowflake. Para recuperá-lo, clique no link realçado abaixo.

    ![Captura de tela de uma planilha na Interface do Usuário do Snowflake com o token de acesso do S C I M realçado.](media/Snowflake-provisioning-tutorial/image01.png)

3. Copie o valor de token gerado e clique em **Concluído**. Esse valor será inserido no campo **Token Secreto** na guia Provisionamento do aplicativo do Snowflake no portal do Azure.

    ![Captura de tela da seção Detalhes mostrando o token copiado no campo de texto e a opção Concluído realçada.](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="step-3-add-snowflake-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar o Snowflake por meio da galeria de aplicativos do Azure AD

Adicione o Snowflake por meio da galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento para ele. Se já tiver configurado o Snowflake para SSO, você poderá usar o mesmo aplicativo. No entanto, recomendamos que você crie um aplicativo diferente ao testar a integração no início. Saiba mais sobre como adicionar um aplicativo da galeria [aqui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo de provisionamento 

No Azure AD, é possível definir quem estará no escopo de provisionamento com base na atribuição ao aplicativo ou nos atributos do usuário/grupo. Se você optar por definir quem estará no escopo de provisionamento com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você optar por definir quem estará no escopo de provisionamento com base somente em atributos do usuário ou do grupo, poderá usar um filtro de escopo, conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir usuários e grupos ao Snowflake, você precisará selecionar uma função diferente de **Acesso Padrão**. Os usuários com a função Acesso Padrão são excluídos do provisionamento e serão marcados como "Não qualificado efetivamente" nos logs de provisionamento. Se a única função disponível no aplicativo for a de acesso padrão, você poderá [atualizar o manifesto do aplicativo](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar outras funções. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de implementar para todos. Quando o escopo de provisionamento é definido para usuários e grupos atribuídos, é possível controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, é possível especificar um [atributo com base no filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-snowflake"></a>Etapa 5. Configurar o provisionamento automático de usuário para o Snowflake 

Esta seção descreve as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Snowflake com base em atribuições de usuário e/ou de grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-snowflake-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Snowflake no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **floco de neve**.

    ![O link de floco de neve na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções Gerenciar com a opção Provisionamento destacada.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa Modo de Provisionamento com a opção Automático destacada.](common/provisioning-automatic.png)

5. Na seção Credenciais de Administrador, insira os valores **URL base do SCIM 2.0 e Token de Autenticação** recuperados anteriormente nos campos **URL do Locatário** e **Token Secreto**, respectivamente. Clique em **Testar Conectividade** para verificar se o Azure AD pode se conectar ao Snowflake. Se a conexão falhar, verifique se a sua conta do Snowflake tem permissões de Administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

7. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

8. Clique em **Save** (Salvar).

9. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Snowflake**.

10. Examine os atributos de usuário que serão sincronizados do Azure AD para o Snowflake na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das contas de usuário no Snowflake em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

   |Atributo|Type|
   |---|---|
   |ativo|Boolean|
   |displayName|String|
   |emails[type eq "work"].value|String|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:defaultRole|Cadeia de caracteres|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:defaultWarehouse|Cadeia de caracteres|

11. Na seção **Mapeamentos**, selecione **Sincronizar Grupos do Azure Active Directory com o Snowflake**.

12. Examine os atributos de grupo que serão sincronizados do Azure AD para o Snowflake na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência dos grupos no Snowflake em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

      |Atributo|Type|
      |---|---|
      |displayName|String|
      |membros|Referência|

13. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Para habilitar o serviço de provisionamento do Azure AD no Snowflake, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

15. Defina os usuários e/ou os grupos que deseja provisionar no Snowflake escolhendo os valores desejados em **Escopo** na seção **Configurações**. Se essa opção não estiver disponível, configure os campos obrigatórios em Credenciais de Administrador; clique em **Salvar** e atualize a página. 

    ![Escopo de provisionamento](common/provisioning-scope.png)

16. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

    Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução.

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar a implantação:

1. Use os [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais usuários foram provisionados com êxito ou não
2. Confira a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o status do ciclo de provisionamento e saber como fechá-la para concluir
3. Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre os estados de quarentena [aqui](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Limitações do conector

* Os tokens SCIM gerados pelo Snowflake expiram em 6 meses. Lembre-se de que eles precisam ser atualizados antes de expirarem para permitir que as sincronizações de provisionamento continuem funcionando. 

## <a name="troubleshooting-tips"></a>Dicas de solução de problemas

* **Intervalos de IP** 

   O serviço de provisionamento do Azure AD opera atualmente em intervalos de IP específicos. Portanto, se necessário, você pode restringir outros intervalos de IP e incluir esses intervalos de IP específicos na lista de permitidos do seu aplicativo para permitir o fluxo de tráfego do serviço de provisionamento do Azure AD para seu aplicativo. Confira a documentação em [Intervalos de IP](../app-provisioning/use-scim-to-provision-users-and-groups.md#ip-ranges).

## <a name="change-log"></a>Log de Alterações

* 21/07/2020 – exclusão reversível habilitada para todos os usuários (por meio do atributo ativo).

## <a name="additional-resources"></a>Recursos adicionais

* [Como gerenciar o provisionamento de contas de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas
* [Saiba como fazer revisão de logs e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md).