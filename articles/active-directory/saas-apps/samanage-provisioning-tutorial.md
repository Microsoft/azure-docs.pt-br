---
title: 'Tutorial: Configurar o Samanage para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar contas de usuário ao Samanage automaticamente.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 62d0392f-37d4-436e-9aff-22f4e5b83623
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 67cfe5a26740837508ea3a3e76295a896c3cc107
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670915"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Tutorial: Configurar o Samanage para provisionamento automático do usuário

Este tutorial demonstra as etapas para executar no Samanage e no Azure Active Directory (Azure AD) para configurar o Azure AD automaticamente provisionar e desprovisionar usuários e grupos ao Samanage.

> [!NOTE]
> Este tutorial descreve um conector que se baseia no serviço de provisionamento de usuário do Azure AD. Para obter informações sobre esse serviço faz, como ele funciona e perguntas frequentes, consulte [automatizar o provisionamento e desprovisionamento de usuários para aplicativos de software-como um serviço (SaaS) com o Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você tenha:

* Um locatário do Azure AD.
* Um [locatário do Samanage](https://www.samanage.com/pricing/) com o pacote profissional.
* Uma conta de usuário do Samanage com permissões de administrador.

> [!NOTE]
> Integração de provisionamento do Azure AD depende de [API Rest do Samanage](https://www.samanage.com/api/). Essa API está disponível para desenvolvedores do Samanage para contas com o pacote Professional.

## <a name="add-samanage-from-the-azure-marketplace"></a>Adicionar o Samanage no Azure Marketplace

Antes de configurar o Samanage para provisionamento com o Azure AD automático de usuário, adicione o Samanage no Azure Marketplace para sua lista de aplicativos SaaS gerenciados.

Para adicionar o Samanage no Marketplace, siga estas etapas.

1. No [portal do Azure](https://portal.azure.com), no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![Ícone do Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Samanage** e selecione **Samanage** no painel de resultados. Para adicionar o aplicativo, selecione **adicionar**.

    ![Samanage na lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-samanage"></a>Atribuir usuários ao Samanage

Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, decida quais usuários ou grupos no Azure AD precisam de acesso ao Samanage. Para atribuir esses usuários ou grupos ao Samanage, siga as instruções em [atribuir um usuário ou grupo a um aplicativo empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-samanage"></a>Dicas importantes para atribuir usuários ao Samanage

*    Hoje em dia, as funções do Samanage são dinamicamente e automaticamente preenchidas no portal do Azure da interface do usuário. Antes de atribuir funções de Samanage para os usuários, certifique-se de que uma sincronização inicial seja completada em Samanage para recuperar as funções mais recentes no seu locatário do Samanage.

*    É recomendável que você atribua um único usuário do Azure AD ao Samanage para testar sua configuração de provisionamento de usuário automático inicial. Você pode atribuir usuários e grupos adicionais mais tarde depois que os testes forem bem-sucedidos.

*    Quando você atribui um usuário ao Samanage, selecione qualquer função específica ao aplicativo válida, se disponível, na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configure-automatic-user-provisioning-to-samanage"></a>Configurar o provisionamento automático de usuário ao Samanage

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do AD do Azure. Usá-lo para criar, atualizar e desabilitar usuários ou grupos no Samanage, com base nas atribuições de usuário ou grupo no AD do Azure.

> [!TIP]
> Você também pode habilitar baseado em SAML SSO para o Samanage. Siga as instruções de [tutorial do logon único Samanage](samanage-tutorial.md). O logon único o pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementam.

### <a name="configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Configurar o provisionamento automático de usuário para o Samanage no Azure AD

1. Entre no [Portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais** > **todos os aplicativos** > **Samanage**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Samanage**.

    ![O link do Samanage na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Provisionamento do Samanage](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Modo de provisionamento do Samanage](./media/samanage-provisioning-tutorial/ProvisioningCredentials.png)

5. Sob o **credenciais de administrador** , digite o nome de usuário administrador e a senha de administrador da sua conta do Samanage. Exemplos desses valores são:

   * No **Admin Username** caixa, preencha o nome de usuário da conta de administrador no locatário do Samanage. Um exemplo é admin@contoso.com.

   * No **senha de administrador** caixa, preencha a senha da conta de administrador que corresponde ao nome de usuário administrador.

6. Depois de preencher as caixas mostradas na etapa 5, selecione **Conexão de teste** para certificar-se de que o Azure AD pode se conectar ao Samanage. Se a conexão falhar, certifique-se de que sua conta do Samanage tem permissões de administrador e tente novamente.

    ![Conexão de teste de Samanage](./media/samanage-provisioning-tutorial/TestConnection.png)

7. No **Email de notificação** caixa, digite o endereço de email da pessoa ou grupo para receber as notificações de erro de provisionamento. Selecione o **enviar uma notificação por email quando ocorre uma falha** caixa de seleção.

    ![Email de notificação do Samanage](./media/samanage-provisioning-tutorial/EmailNotification.png)

8. Clique em **Salvar**.

9. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Samanage**.

    ![Sincronização de usuário do Samanage](./media/samanage-provisioning-tutorial/UserMappings.png)

10. Revise os atributos de usuário que são sincronizados do Azure AD para Samanage na seção **Mapeamentos de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário do Samanage em operações de atualização. Para salvar as alterações, selecione **salvar**.

    ![Atributos de usuário correspondentes do Samanage](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

11. Para habilitar mapeamentos de grupo, na seção **Mapeamentos**, selecione **Sincronizar grupos do Azure Active Directory com Samanage**.

    ![Grupo de sincronização do Samanage](./media/samanage-provisioning-tutorial/GroupMappings.png)

12. Defina **Habilitado** para **Sim** para sincronizar grupos. Revise os atributos do grupo que são sincronizados do Azure AD para Samanage na seção**Mapeamentos de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário do Samanage em operações de atualização. Para salvar as alterações, selecione **salvar**.

    ![Atributos de grupo correspondentes do Samanage](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. Para configurar filtros de escopo, siga as instruções de [tutorial do filtro de escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Para habilitar o provisionamento de serviço para o Samanage, do Azure AD a **as configurações** seção, altere **Status de provisionamento** para **em**.

    ![Status de provisionamento do Samanage](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

15. Defina os usuários ou grupos que você deseja provisionar ao Samanage. No **as configurações** seção, selecione os valores desejados na **escopo**. Quando você seleciona os **todos os usuários e grupos de sincronização** opção, considere as limitações, conforme descrito na seção "Limitações do conector".

    ![Escopo do Samanage](./media/samanage-provisioning-tutorial/ScopeSync.png)

16. Quando estiver pronto para provisionar, selecione **salvar**.

    ![Salvar Samanage](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


Essa operação inicia a sincronização inicial de todos os usuários ou grupos definidos no **escopo** na **configurações** seção. A sincronização inicial levará mais tempo para que as sincronizações posteriores. Eles ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do AD do Azure é executado. 

Você pode usar o **detalhes de sincronização** seção para monitorar o progresso e siga os links para o relatório de atividade de provisionamento. O relatório descreve todas as ações executadas pelo serviço no Samanage de provisionamento do Azure AD.

Para obter informações sobre como ler os logs de provisionamento do Azure AD, confira [Relatórios sobre o provisionamento automático de contas de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

Se você selecionar o **sincronizar todos os usuários e grupos** opção e configurar um valor para o Samanage **funções** de atributo, o valor sob o **valor padrão se for null (é opcional)** caixa deve ser expressa no formato a seguir:

- {"displayName": "função"}, em que a função é o valor padrão que você deseja.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
