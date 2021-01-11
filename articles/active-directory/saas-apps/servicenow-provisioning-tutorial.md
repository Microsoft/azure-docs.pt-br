---
title: 'Tutorial: Configurar o ServiceNow para provisionamento automático de usuários com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e desprovisionar automaticamente contas de usuário do Azure AD para o ServiceNow.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: 71f4cfa9e7b69e51bafa5364f857f8ab689f2ad0
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97955460"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Tutorial: Configurar o ServiceNow para o provisionamento automático de usuários

Neste tutorial, você verá uma descrição das etapas que precisam ser seguidas no ServiceNow e no Azure AD (Azure Active Directory) para configurar o provisionamento automático de usuários. Quando configurado, o Azure AD provisiona e desprovisiona automaticamente usuários e grupos para o [ServiceNow](https://www.servicenow.com/) usando o serviço de provisionamento do Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades com suporte
> [!div class="checklist"]
> * Criar usuários no ServiceNow
> * Remover usuários no ServiceNow quando eles não precisam mais de acesso
> * Manter os atributos de usuário sincronizados entre o Azure AD e o ServiceNow
> * Provisionar grupos e associações de grupo no ServiceNow
> * [Logon único](servicenow-tutorial.md) no ServiceNow (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](../develop/quickstart-create-new-tenant.md) 
* Uma conta de usuário no Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento (por exemplo, Administrador de Aplicativo, Administrador de aplicativos de nuvem, Proprietário de Aplicativo ou Administrador global). 
* Uma [instância do ServiceNow](https://www.servicenow.com/) de Calgary ou superior
* Uma [instância do ServiceNow Express](https://www.servicenow.com/) de Helsinque ou superior
* Uma conta de usuário no ServiceNow com a função de administrador

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje a implantação do provisionamento
1. Saiba mais sobre [como funciona o serviço de provisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem estará no [escopo de provisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais dados serão [mapeados entre o Azure AD e o ServiceNow](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configure o ServiceNow para ser compatível com o provisionamento do Azure AD

1. Identifique o nome da instância do ServiceNow. O nome da instância pode ser encontrado na URL que você usa para acessar o ServiceNow. No exemplo a seguir, o nome da instância é dev35214.

   ![Instância do ServiceNow](media/servicenow-provisioning-tutorial/servicenow-instance.png)

2. Obtenha as credenciais de um administrador no ServiceNow. Navegue até o perfil do usuário no ServiceNow e verifique se o usuário tem a função de administrador. 

   ![Função de administrador do ServiceNow](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)


## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicione o ServiceNow por meio da galeria de aplicativos do Azure AD

Adicione o ServiceNow por meio da galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento dele. Se você já tiver configurado o ServiceNow para SSO, poderá usar o mesmo aplicativo. No entanto, recomendamos que você crie um aplicativo diferente ao testar a integração no início. Saiba mais sobre como adicionar um aplicativo da galeria [aqui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo de provisionamento 

No Azure AD, é possível definir quem estará no escopo de provisionamento com base na atribuição ao aplicativo ou nos atributos do usuário/grupo. Se você optar por definir quem estará no escopo de provisionamento com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você optar por definir quem estará no escopo de provisionamento com base somente em atributos do usuário ou do grupo, poderá usar um filtro de escopo, conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir usuários e grupos ao ServiceNow, é preciso selecionar uma função diferente de **Acesso Padrão**. Os usuários com a função Acesso Padrão são excluídos do provisionamento e serão marcados como "Não qualificado efetivamente" nos logs de provisionamento. Se a única função disponível no aplicativo for a de acesso padrão, você poderá [atualizar o manifesto do aplicativo](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar outras funções. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de implementar para todos. Quando o escopo de provisionamento é definido para usuários e grupos atribuídos, é possível controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, é possível especificar um [atributo com base no filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>Etapa 5. Configure o provisionamento automático de usuários para o ServiceNow 

Nesta seção, você verá orientações para seguir as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no TestApp com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-servicenow-in-azure-ad"></a>Para configurar o provisionamento automático de usuários para o ServiceNow no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **ServiceNow**.

    ![O link do ServiceNow na lista de Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções Gerenciar com a opção Provisionamento destacada.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa Modo de Provisionamento com a opção Automático destacada.](common/provisioning-automatic.png)

5. Na seção **Credenciais do Administrador**, insira as credenciais de administrador e o nome de usuário do ServiceNow. Clique em **Testar Conexão** para verificar se o Azure AD pode se conectar ao ServiceNow. Se a conexão falhar, verifique se a conta do ServiceNow tem permissões de Administrador e tente novamente.

    ![Captura de tela mostrando a página Provisionamento de Serviço, em que você pode inserir as Credenciais de Administrador.](./media/servicenow-provisioning-tutorial/servicenow-provisioning.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o ServiceNow**.

9. Examine os atributos de usuário sincronizados do Azure AD para o ServiceNow na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário do ServiceNow em operações de atualização. Se você optar por alterar o [atributo de destino correspondente](../app-provisioning/customize-application-attributes.md), precisará garantir que a API do ServiceNow seja compatível com a filtragem de usuários com base no atributo em questão. Selecione o botão **Salvar** para confirmar as alterações.

10. Na seção **Mapeamentos**, selecione **Sincronizar Grupos do Azure Active Directory com o ServiceNow**.

11. Examine os atributos de grupo sincronizados do Azure AD para o ServiceNow na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder os grupos no ServiceNow em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD no ServiceNow, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos a serem provisionados para o ServiceNow escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação começa o ciclo de sincronização inicial de todos os usuários e grupos definidos no **Escopo** na seção **Configurações**. O ciclo inicial leva mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar a implantação:

1. Use os [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais usuários foram provisionados com êxito ou não
2. Confira a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o status do ciclo de provisionamento e saber como fechá-la para concluir
3. Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre os estados de quarentena [aqui](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="troubleshooting-tips"></a>Dicas de solução de problemas
* **InvalidLookupReference:** ao provisionar determinados atributos, como Departamento e Localização, os valores já devem existir em uma tabela de referência no ServiceNow. Por exemplo, você pode ter dois locais (Seattle, Los Angeles) e três departamentos (Vendas, Finanças, Marketing) na tabela **inserir nome da tabela** no ServiceNow. Se você tentar provisionar um usuário do departamento de "Vendas" e da localização "Seattle", ele será provisionado com sucesso. Se você tentar provisionar um usuário do departamento "Vendas" e da localização "LA", ele não será provisionado. A localização LA deve ser adicionada à tabela de referência no ServiceNow ou o atributo de usuário no Azure AD deve ser atualizado para corresponder ao formato no ServiceNow. 
* **EntryJoiningPropertyValueIsMissing:** examine os [mapeamentos de atributo](../app-provisioning/customize-application-attributes.md) para identificar o atributo correspondente. Esse valor precisa estar presente no usuário ou no grupo que você quer provisionar. 
* Examine a [API SOAP do ServiceNow](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html) para entender os requisitos ou as limitações (por exemplo, o formato para especificar o código de país de um usuário).
* As solicitações de provisionamento são enviadas por padrão para https://{nome-da-instância}.service-now.com/{nome-da-tabela}. Se você precisar de uma URL de locatário personalizada, forneça a URL completa no campo de nome da instância.
* **ServiceNowInstanceInvalid** 
  
  `Details: Your ServiceNow instance name appears to be invalid.  Please provide a current ServiceNow administrative user name and          password along with the name of a valid ServiceNow instance.`                                                              

   Esse erro indica um problema de comunicação com a instância do ServiceNow. 
   
   Se você estiver tendo problemas de conexão de teste, tente deixar as seguintes configurações como **desabilitado** no ServiceNow:
   
   1. Selecione **Segurança do Sistema** > **Configurações de Alta Segurança** > **Exigir autenticação Básica para solicitações SCHEMA de entrada**.
   2. Selecione **Propriedades do Sistema** > **Serviços Web** > **Exigir autorização básica para solicitações SOAP de entrada**.

   ![Autorizando a solicitação de SOAP](media/servicenow-provisioning-tutorial/servicenow-webservice.png)

   Se resolver seus problemas, entre em contato com o suporte do ServiceNow e solicite a ativação da depuração de SOAP para ajudar a solucionar problemas. 

* **Intervalos de IP** 

   No momento, o serviço de provisionamento do Azure AD opera em intervalos de IP específicos. Portanto, se necessário, você pode restringir outros intervalos de IP e incluir esses intervalos de IP específicos na lista de permitidos do seu aplicativo para permitir o fluxo de tráfego do serviço de provisionamento do Azure AD para seu aplicativo. Confira a documentação em [Intervalos de IP](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#ip-ranges).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)