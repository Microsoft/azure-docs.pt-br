---
title: 'Tutorial: Configurar o 15Five para o provisionamento automático de usuários com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o 15Five.
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
ms.openlocfilehash: 00f713e8d319d7ad8dcea014429c57d7fba40541
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181213"
---
# <a name="tutorial-configure-15five-for-automatic-user-provisioning"></a>Tutorial: Configurar o 15Five para provisionamento automático de usuários

O objetivo deste tutorial é demonstrar as etapas a serem executadas no 15Five e no Azure AD (Azure Active Directory) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para o [15Five](https://www.15five.com/pricing/). Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory.

> [!NOTE]
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="capabilities-supported"></a>Funcionalidades com suporte
> [!div class="checklist"]
> * Criar usuários no 15Five
> * Remover usuários no 15Five quando eles não precisarem mais de acesso
> * Manter os atributos de usuário sincronizados entre o Azure AD e o 15Five
> * Provisionar grupos e associações a um grupo no 15Five
> * [Logon único](./15five-tutorial.md) no 15Five (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](../develop/quickstart-create-new-tenant.md).
* Uma conta de usuário no Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento (por exemplo, Administrador de Aplicativo, Administrador de aplicativos de nuvem, Proprietário de Aplicativo ou Administrador global).
* [Um locatário do 15Five](https://www.15five.com/pricing/).
* Uma conta de usuário no 15Five com permissões de administrador.

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje a implantação do provisionamento
1. Saiba mais sobre [como funciona o serviço de provisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem estará no [escopo de provisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais dados [mapear entre o Azure AD e o 15Five](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-15five-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configurar o 15Five para dar suporte ao provisionamento com o Azure AD

Antes de configurar o 15Five para o provisionamento automático de usuário com o Azure AD, você precisará habilitar o provisionamento do SCIM no 15Five.

1. Entre no [console de administração do 15Five](https://my.15five.com/). Navegue até **Recursos > Integrações**.

    :::image type="content" source="media/15five-provisioning-tutorial/integration.png" alt-text="Captura de tela do console de administração do 15Five. As integrações aparecem em Recursos em um menu e os Recursos e as Integrações são realçados." border="false":::

2.  Clique em **SCIM 2.0**.

    :::image type="content" source="media/15five-provisioning-tutorial/image00.png" alt-text="Captura de tela da página Integrações no console de administração do 15Five. Em Ferramenta, S C I M 2.0 está realçado." border="false":::

3.  Navegue até **Integração do SCIM > Gerar token OAuth**.

    :::image type="content" source="media/15five-provisioning-tutorial/image02.png" alt-text="Captura de tela da página Integrações do S C I M no console de administração do 15Five. A geração do token OAuth está realçada." border="false":::

4.  Copie os valores para **URL base do SCIM 2.0** e **Token de Acesso**. Esse valor será inserido nos campos **URL do Locatário** e **Token Secreto** na guia Provisionamento do aplicativo 15Five no portal do Azure.
    
    :::image type="content" source="media/15five-provisioning-tutorial/image03.png" alt-text="Captura de tela da página de integração do S C I M. Na tabela de Token, os valores ao lado da U R L base do S C I M 2.0 e do Token de acesso estão realçados." border="false":::

## <a name="step-3-add-15five-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar o 15Five da galeria de aplicativos do Azure AD

Adicione o 15Five por meio da galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento para ele. Se você já tiver configurado o 15Five para SSO, poderá usar o mesmo aplicativo. No entanto, recomendamos que você crie um aplicativo diferente ao testar a integração no início. Saiba mais sobre como adicionar um aplicativo da galeria [aqui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo de provisionamento 

No Azure AD, é possível definir quem estará no escopo de provisionamento com base na atribuição ao aplicativo ou nos atributos do usuário/grupo. Se você optar por definir quem estará no escopo de provisionamento com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você optar por definir quem estará no escopo de provisionamento com base somente em atributos do usuário ou do grupo, poderá usar um filtro de escopo, conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir usuários e grupos ao 15Five, é preciso selecionar uma função diferente do **Acesso Padrão**. Os usuários com a função Acesso Padrão são excluídos do provisionamento e serão marcados como "Não qualificado efetivamente" nos logs de provisionamento. Se a única função disponível no aplicativo for a de acesso padrão, você poderá [atualizar o manifesto do aplicativo](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar outras funções. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de implementar para todos. Quando o escopo de provisionamento é definido para usuários e grupos atribuídos, é possível controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, é possível especificar um [atributo com base no filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="step-5-configure-automatic-user-provisioning-to-15five"></a>Etapa 5. Configurar o provisionamento automático de usuário para o 15Five 

Esta seção orienta você pelas etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no 15Five com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-15five-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o 15Five no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **15Five**.

    ![O link do 15Five na lista de Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções Gerenciar com a opção Provisionamento destacada.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa Modo de Provisionamento com a opção Automático destacada.](common/provisioning-automatic.png)

5.  Na seção Credenciais de Administrador, insira os valores **URL base do SCIM 2.0 e Token de Acesso** recuperados anteriormente nos campos **URL do Locatário** e **Token Secreto**, respectivamente. Clique em **Testar Conectividade** para verificar se o Azure AD pode se conectar ao 15Five. Se a conexão falhar, verifique se a sua conta do 15Five tem permissões de Administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Save** (Salvar).

8. Na seção **Mapeamentos**, selecione **Sincronizar usuários do Azure Active Directory com o 15Five**.

9. Examine os atributos de usuário sincronizados do Azure AD com o 15Five na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário do 15Five em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.


   |Atributo|Type|
   |---|---|
   |ativo|Boolean|
   |título|String|
   |emails[type eq "work"].value|String|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |externalId|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Referência|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:15Five:2.0:User:location|Cadeia de caracteres|
   |urn:ietf:params:scim:schemas:extension:15Five:2.0:User:startDate|Cadeia de caracteres|

10. Na seção **Mapeamentos**, selecione **Sincronizar Grupos do Azure Active Directory com o 15Five**.

11. Examine os atributos de grupo sincronizados do Azure AD com o 15Five na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência dos grupos no 15Five para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

      |Atributo|Type|
      |---|---|
      |externalId|String|
      |displayName|String|
      |membros|Referência|

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD no 15Five, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você gostaria de provisionar para o 15Five escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

    Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução.

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar a implantação:

1. Use os [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais usuários foram provisionados com êxito ou não
2. Confira a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o status do ciclo de provisionamento e saber como fechá-la para concluir
3. Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre os estados de quarentena [aqui](../app-provisioning/application-provisioning-quarantine-status.md).  
    
## <a name="connector-limitations"></a>Limitações do conector

* O 15Five não dá suporte a exclusões temporárias para usuários.

## <a name="change-log"></a>Log de alterações

* 16/06/2020 – suporte adicionado para o atributo de extensão empresarial "Gerente" e atributos personalizados "Local" e "Data de início" para os usuários.

## <a name="additional-resources"></a>Recursos adicionais

* [Como gerenciar o provisionamento de contas de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md).