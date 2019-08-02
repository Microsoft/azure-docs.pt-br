---
title: 'Tutorial: Configurar o Zendesk para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar contas de usuário ao Zendesk automaticamente.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 01d5e4d5-d856-42c4-a504-96fa554baf66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 166b7727362549aaf054e3f0282c564eca687eb9
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672873"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Tutorial: Configurar o Zendesk para provisionamento automático do usuário

Este tutorial demonstra as etapas para executar no Zendesk e no Azure Active Directory (Azure AD) para configurar o Azure AD automaticamente provisionar e desprovisionar usuários e grupos ao Zendesk.

> [!NOTE]
> Este tutorial descreve um conector que se baseia no serviço de provisionamento de usuário do Azure AD. Para obter informações sobre esse serviço faz, como ele funciona e perguntas frequentes, consulte [automatizar o provisionamento e desprovisionamento de usuários para aplicativos de software-como um serviço (SaaS) com o Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você tenha:

* Um locatário do Azure AD.
* Um locatário do Zendesk com o [Enterprise](https://www.zendesk.com/product/pricing/) planejar ou outro melhor habilitado.
* Uma conta de usuário no Zendesk com permissões de administrador.

> [!NOTE]
> Integração de provisionamento do Azure AD depende de [API Rest do Zendesk](https://developer.zendesk.com/rest_api/docs/core/introduction). Essa API está disponível para as equipes do Zendesk no plano Enterprise ou superior.

## <a name="add-zendesk-from-the-azure-marketplace"></a>Adicionar o Zendesk no Azure Marketplace

Antes de configurar o Zendesk para provisionamento com o Azure AD automático de usuário, adicione o Zendesk no Azure Marketplace para sua lista de aplicativos SaaS gerenciados.

Para adicionar o Zendesk no Marketplace, siga estas etapas.

1. No [portal do Azure](https://portal.azure.com), no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![Ícone do Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Zendesk** e selecione **Zendesk** no painel de resultados. Para adicionar o aplicativo, selecione **adicionar**.

    ![Zendesk na lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>Atribuir usuários ao Zendesk

Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, decida quais usuários ou grupos no Azure AD precisam de acesso ao Zendesk. Para atribuir esses usuários ou grupos ao Zendesk, siga as instruções em [atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Dicas importantes para atribuir usuários ao Zendesk

* Hoje em dia, Zendesk funções são dinamicamente e automaticamente preenchidas na interface do usuário do portal do Azure. Antes de atribuir funções do Zendesk para os usuários, certifique-se de que uma sincronização inicial seja completada em Zendesk para recuperar as funções mais recentes em seu locatário do Zendesk.

* É recomendável que você atribua um único usuário do Azure AD ao Zendesk para testar sua configuração de provisionamento de usuário automático inicial. Você pode atribuir usuários ou grupos adicionais mais tarde depois que os testes forem bem-sucedidos.

* Quando você atribui um usuário ao Zendesk, selecione qualquer função específica ao aplicativo válida, se disponível, na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>Configurar o provisionamento automático de usuário ao Zendesk 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do AD do Azure. Usá-lo para criar, atualizar e desabilitar usuários ou grupos no Zendesk com base em atribuições de usuário ou grupo no AD do Azure.

> [!TIP]
> Você também pode habilitar baseado em SAML SSO para o Zendesk. Siga as instruções de [tutorial do logon único Zendesk](zendesk-tutorial.md). O logon único o pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementam.

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Configurar o provisionamento automático de usuário para o Zendesk no Azure AD

1. Entre no [Portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais** > **todos os aplicativos** > **Zendesk**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Zendesk**.

    ![O link do Zendesk na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Provisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Modo de provisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. Sob o **credenciais de administrador** seção, insira o nome de usuário administrador, o token secreto e o domínio da sua conta do Zendesk. Exemplos desses valores são:

   * No **Admin Username** caixa, preencha o nome de usuário da conta de administrador no locatário do Zendesk. Um exemplo é admin@contoso.com.

   * No **segredo do Token** caixa, preencha o token de segredo, conforme descrito na etapa 6.

   * No **domínio** caixa, preencha o subdomínio do seu locatário do Zendesk. Por exemplo, para uma conta com uma URL do locatário do `https://my-tenant.zendesk.com`, seu subdomínio for **meu locatário**.

6. O token secreto para sua conta do Zendesk está localizado em **Admin** > **API** > **configurações**. Certifique-se de que **Token de acesso** é definido como **habilitado**.

    ![Configurações de administrador do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Token de segredo do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. Depois de preencher as caixas mostradas na etapa 5, selecione **Conexão de teste** para certificar-se de que o Azure AD pode se conectar ao Zendesk. Se a conexão falhar, certifique-se de que sua conta do Zendesk tem permissões de administrador e tente novamente.

    ![Conexão de teste do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. No **Email de notificação** caixa, digite o endereço de email da pessoa ou grupo para receber as notificações de erro de provisionamento. Selecione o **enviar uma notificação por email quando ocorre uma falha** caixa de seleção.

    ![Zendesk Notification Email](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Clique em **Salvar**.

10. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Zendesk**.

    ![Sincronização de usuário do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Examine os atributos de usuário que são sincronizados do Azure AD para o Zendesk na **mapeamentos de atributo** seção. Os atributos selecionados como propriedades **Correspondentes** serão usados para corresponder as contas de usuário no Zendesk para operações de atualização. Para salvar as alterações, selecione **salvar**.

    ![Atributos de usuário correspondentes do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. Sob o **mapeamentos** seção, selecione **sincronizar grupos do Azure Active Directory ao Zendesk**.

    ![Grupo de sincronização do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Examine os atributos de grupo que são sincronizados do Azure AD para o Zendesk na **mapeamentos de atributo** seção. Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder os grupos do Zendesk em operações de atualização. Para salvar as alterações, selecione **salvar**.

    ![Atributos de grupo correspondentes do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Para configurar filtros de escopo, siga as instruções de [tutorial do filtro de escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para habilitar o provisionamento de serviço para o Zendesk, do Azure AD a **as configurações** seção, altere **Status de provisionamento** para **em**.

    ![Status de provisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Defina os usuários ou grupos que você deseja provisionar ao Zendesk. No **as configurações** seção, selecione os valores desejados na **escopo**.

    ![Escopo do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Quando estiver pronto para provisionar, selecione **salvar**.

    ![Zendesk Save](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

Essa operação inicia a sincronização inicial de todos os usuários ou grupos definidos no **escopo** na **configurações** seção. A sincronização inicial levará mais tempo para que as sincronizações posteriores. Eles ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do AD do Azure é executado. 

Você pode usar o **detalhes de sincronização** seção para monitorar o progresso e siga os links para o relatório de atividade de provisionamento. O relatório descreve todas as ações executadas pelo serviço no Zendesk de provisionamento do Azure AD.

Para obter informações sobre como ler os logs de provisionamento do Azure AD, confira [Relatórios sobre o provisionamento automático de contas de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* Zendesk oferece suporte ao uso de grupos para usuários com **agente** somente funções. Para obter mais informações, consulte o [documentação do Zendesk](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups).

* Quando uma função personalizada é atribuída a um usuário ou grupo, o Azure AD automático de usuário serviço de provisionamento também atribui a função padrão **agente**. Somente os agentes podem ser atribuídos a uma função personalizada. Para obter mais informações, consulte o [documentação da API do Zendesk](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests). 

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
