---
title: 'Tutorial: Configurar o BlueJeans para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no BlueJeans.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 58cd69ebe97d9d0965d7e648b0ded012ac71cd0d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101646108"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>Tutorial: Configurar o BlueJeans para o provisionamento automático de usuário

Este tutorial descreve as etapas que você precisa executar no BlueJeans e no Azure Active Directory (Azure AD) para configurar o provisionamento automático de usuário. Quando configurado, o Azure AD provisiona e desprovisiona automaticamente usuários para o [BlueJeans](https://www.bluejeans.com/pricing) usando o serviço de provisionamento do Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Funcionalidades com suporte
> [!div class="checklist"]
> * Criar usuários no BlueJeans
> * Remover usuários no BlueJeans quando eles não precisarem mais de acesso
> * Manter os atributos de usuário sincronizados entre o Azure AD e o BlueJeans
> * [Logon único](./bluejeans-tutorial.md) para o BlueJeans (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](../develop/quickstart-create-new-tenant.md).
* Uma conta de usuário no Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento (por exemplo, Administrador de Aplicativo, Administrador de aplicativos de nuvem, Proprietário de Aplicativo ou Administrador Global). 
* Um locatário do BlueJeans com o plano [Minha Empresa](https://www.bluejeans.com/pricing) ou outro com mais recursos habilitado.
* Uma conta de usuário no BlueJeans com permissões de Administrador.
* Provisionamento do SCIM habilitado no BlueJeans Enterprise.

> [!NOTE]
> A integração de provisionamento do Azure AD depende da [API do BlueJeans](https://BlueJeans.github.io/developer), disponível para as equipes do BlueJeans no plano Standard ou em outro com mais recursos.

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje a implantação do provisionamento
1. Saiba mais sobre [como funciona o serviço de provisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem estará no [escopo de provisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais dados [mapear entre o Azure AD e o BlueJeans](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-bluejeans-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configurar o BlueJeans para dar suporte ao provisionamento com o Azure AD

1. Faça logon no console do administrador do BlueJeans. Navegue até Configurações de Grupo > Segurança.
2. Selecione **Logon Único** e **Configurar Agora**.

    ![now](./media/bluejeans-provisioning-tutorial/configure.png)

3. Na janela **Provisionamento e Integração**, selecione **Criar e gerenciar contas de usuário por meio de IDP** e clique em **GERAR TOKEN**.

    ![generate](./media/bluejeans-provisioning-tutorial/token.png)
    
4. Copie e salve o token. 
5. A URL do Locatário do BlueJeans é `https://api.bluejeans.com/v2/scim`. A **URL do Locatário** e o **Token Secreto** da etapa anterior serão inseridos na guia Provisionamento do aplicativo BlueJeans no portal do Azure.

## <a name="step-3-add-bluejeans-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar o BlueJeans da galeria de aplicativos do Azure AD

Adicione o BlueJeans da galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento para ele. Se você já tiver configurado o BlueJeans para SSO, poderá usar o mesmo aplicativo. No entanto, é recomendável que você crie um aplicativo diferente ao testar a integração no início. Saiba mais sobre como adicionar um aplicativo da galeria [aqui](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo de provisionamento 

No Azure AD, é possível definir quem estará no escopo de provisionamento com base na atribuição ao aplicativo ou nos atributos do usuário. Se você optar por definir quem estará no escopo de provisionamento com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários ao aplicativo. Se você optar por definir quem estará no escopo de provisionamento com base somente em atributos do usuário, poderá usar um filtro de escopo, conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir usuários ao BlueJeans, é necessário selecionar uma função diferente do **Acesso Padrão**. Os usuários com a função Acesso Padrão são excluídos do provisionamento e serão marcados como "Não qualificado efetivamente" nos logs de provisionamento. Se a única função disponível no aplicativo for a de acesso padrão, você poderá [atualizar o manifesto do aplicativo](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar outras funções. 

* Comece pequeno. Faça o teste com um pequeno conjunto de usuários antes de implementar para todos. Quando o escopo de provisionamento é definido para usuários atribuídos, é possível controlar isso atribuindo um ou dois usuários ao aplicativo. Quando o escopo é definido para todos os usuários, é possível especificar um [atributo com base no filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="step-5-configure-automatic-user-provisioning-to-bluejeans"></a>Etapa 5. Configurar o provisionamento automático de usuário para o BlueJeans

Nesta seção, você verá orientações para seguir as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários no TestApp com base em atribuições de usuário no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o BlueJeans no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **BlueJeans**.

    ![O link do BlueJeans na lista Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções Gerenciar com a opção Provisionamento destacada.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Guia Provisionamento automático](common/provisioning-automatic.png)

5. Na seção **Credenciais de Administrador**, insira a URL do Locatário e o Token Secreto do BlueJeans recuperado na Etapa 2. Clique em **Testar Conectividade** para verificar se o Azure AD pode se conectar ao BlueJeans. Se a conexão falhar, verifique se a conta do BlueJeans tem permissões de Administrador e tente novamente.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)


6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **Mapeamentos**, selecione **Sincronizar usuários do Azure Active Directory com o BlueJeans**.

9. Examine os atributos de usuário que serão sincronizados do Azure AD para o BlueJeans na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência entre as contas de usuário no BlueJeans para operações de atualização. Se você optar por alterar o [atributo de destino correspondente](../app-provisioning/customize-application-attributes.md), precisará verificar se a API do BlueJeans é compatível com a filtragem de usuários com base nesse atributo. Selecione o botão **Salvar** para confirmar as alterações.

|Atributo|Type|Com suporte para filtragem|
|---|---|---|
|userName|String|&check;|
|ativo|Boolean|
|título|String|
|emails[type eq "work"].value|String|
|name.givenName|String|
|name.familyName|String|
|phoneNumbers[type eq "work"].value|String|
|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|String|

10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento do Azure AD no BlueJeans, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários que você gostaria de provisionar para o BlueJeans escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários definidos em **Escopo**, na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar a implantação:

1. Use os [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais usuários foram provisionados com êxito ou não
2. Confira a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o status do ciclo de provisionamento e saber como fechá-la para concluir
3. Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre os estados de quarentena [aqui](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Limitações do conector

* O Bluejeans não permite userNames com mais de 30 caracteres.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)