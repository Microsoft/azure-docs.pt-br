---
title: 'Tutorial: Configurar o Looop para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Looop.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: 528003ac482da6f254bf437321c70c389d23844b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94834988"
---
# <a name="tutorial-configure-looop-for-automatic-user-provisioning"></a>Tutorial: Configurar o Looop para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Looop e no Azure AD (Active Directory) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para o Looop.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do Looop](https://www.looop.co/pricing/)
* Uma conta de usuário em um Looop com permissões de administrador.

## <a name="assign-users-to-looop"></a>Atribuir usuários ao Looop

O Azure Active Directory usa um conceito chamado atribuições para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou os grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Para configurar e habilitar o provisionamento automático de usuário, decida quais usuários e/ou grupos no Azure AD precisam de acesso ao Looop. Depois de decidir, é possível atribuir esses usuários e/ou grupos ao Looop seguindo estas instruções:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-looop"></a>Dicas importantes para atribuir usuários ao Looop

* Recomendamos que somente um usuário do Azure AD seja atribuído ao Looop para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Quando você atribui um usuário ao Looop, é necessário selecionar qualquer função específica ao aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="set-up-looop-for-provisioning"></a>Configurar o Looop para provisionamento

Antes de configurar o Loop para o provisionamento automático de usuário com o Azure AD, será necessário recuperar algumas informações de provisionamento do Looop.

1. Entre em seu [Console de Administração do Looop](https://app.looop.co/#/login) e selecione **Conta**. Em **Configurações da Conta**, selecione **Autenticação**.

    :::image type="content" source="media/looop-provisioning-tutorial/admin.png" alt-text="Captura de tela do console de administração do Looop. A guia Conta está realçada e aberta. Nas configurações da Conta, a opção Autenticação está realçada." border="false":::

2. Gere um novo token clicando em **Redefinir Token** em **Integração do SCIM**.

    :::image type="content" source="media/looop-provisioning-tutorial/resettoken.png" alt-text="Captura de tela da seção Integração do SCIM de uma página no console do administração do Looop. O botão Redefinir token está realçado." border="false":::

3. Copie o **Ponto de Extremidade do SCIM** e o **Token**. Esses valores serão inseridos nos campos **URL do Locatário** e **Token Secreto** na guia Provisionamento do aplicativo Looop no portal do Azure. 

    ![Criar Token do Looop](media/looop-provisioning-tutorial/token.png)

## <a name="add-looop-from-the-gallery"></a>Adicionar Looop da galeria

Para configurar o Looop para o provisionamento automático de usuário com o Azure AD, é necessário adicionar o Looop da galeria de aplicativos do Azure AD à lista de aplicativos SaaS gerenciados.

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Looop** e selecione **Looop** no painel de resultados. 

    ![Looop na lista de resultados](common/search-new-app.png)

5. Selecione o botão **Inscrever-se no Looop**, que redirecionará você à página de logon do Looop. 

    ![Adicionar do OIDC do Looop](media/looop-provisioning-tutorial/signup.png)

6. Como o Looop é um aplicativo OpenIDConnect, opte por fazer logon no Looop usando sua conta corporativa da Microsoft.

    ![Logon do OIDC do Looop](media/looop-provisioning-tutorial/msftlogin.png)

7. Após uma autenticação bem-sucedida, aceite a solicitação de consentimento da página de consentimento. O aplicativo será automaticamente adicionado ao seu locatário e você será redirecionado à sua conta do Looop.

    ![Consentimento do OIDC do Looop](media/looop-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-looop"></a>Configurar o provisionamento automático de usuário para o Looop 

Nesta seção, você verá orientações para seguir as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Looop com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-looop-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Looop no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Looop**.

    ![O link do Looop na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções Gerenciar com a opção Provisionamento destacada.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa Modo de Provisionamento com a opção Automático destacada.](common/provisioning-automatic.png)

5. Na seção **Credenciais de Administrador**, insira `https://<organisation_domain>.looop.co/scim/v2` em **URL do Locatário**. Por exemplo, `https://demo.looop.co/scim/v2`. Insira o valor que você recuperou e salvou anteriormente do Looop no **Token Secreto**. Clique em **Testar Conectividade** para verificar se o Azure AD pode se conectar ao Looop. Se a conexão falhar, verifique se a sua conta do Looop tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Save** (Salvar).

8. Na seção **Mapeamentos**, selecione **Sincronizar usuários do Azure Active Directory com o Looop**.

    ![Mapeamentos de Usuário do Looop](media/looop-provisioning-tutorial/usermappings.png)

9. Examine os atributos de usuário que serão sincronizados do Azure AD com o Looop na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** serão usados para fazer a correspondência entre as contas de usuário no Looop para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

   |Atributo|Type|Com suporte para filtragem|
   |---|---|---|
   |userName|String|&check;|
   |ativo|Boolean|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |externalId|String|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:area|String|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:custom_1|String|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:custom_2|String|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:custom_3|String|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:employee_id|String|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:location|String|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:position|String|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:startAt|String|

10. Na seção **Mapeamentos**, selecione **Sincronizar Grupos do Azure Active Directory com o Meta Networks Connector**.

    ![Mapeamentos de Grupo no Looop](media/looop-provisioning-tutorial/groupmappings.png)

11. Examine os atributos de grupo que serão sincronizados do Azure AD para o Meta Networks Connector na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência dos grupos no Meta Networks Connector em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    |Atributo|Type|Com suporte para filtragem|
    |---|---|---|
    |displayName|String|&check;|
    |membros|Referência|
    |externalId|String|


10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento do Azure AD para o Looop, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou grupos que você gostaria de provisionar no Looop escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Looop.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)


