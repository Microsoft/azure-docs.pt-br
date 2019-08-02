---
title: 'Tutorial: Configurar o Cornerstone OnDemand para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar contas de usuário para Cornerstone OnDemand automaticamente.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8170fc094f1515783fa51a0d579bc12094aab836
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672950"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Tutorial: Configurar o Cornerstone OnDemand para o provisionamento automático de usuário

Este tutorial demonstra as etapas para executar no Cornerstone OnDemand e Azure Active Directory (Azure AD) para configurar o Azure AD automaticamente provisionar e desprovisionar usuários ou grupos à Cornerstone OnDemand.

> [!NOTE]
> Este tutorial descreve um conector que se baseia no serviço de provisionamento de usuário do Azure AD. Para obter informações sobre esse serviço faz, como ele funciona e perguntas frequentes, consulte [automatizar o provisionamento e desprovisionamento de usuários para aplicativos de software-como um serviço (SaaS) com o Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você tenha:

* Um locatário do Azure AD.
* Um locatário do Cornerstone OnDemand.
* Uma conta de usuário do Cornerstone OnDemand com permissões de administrador.

> [!NOTE]
> Integração de provisionamento do Azure AD depende de [serviço de web da Cornerstone OnDemand](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf). Esse serviço está disponível para as equipes da Cornerstone OnDemand.

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>Adicionar a Cornerstone OnDemand no Azure Marketplace

Antes de configurar a Cornerstone OnDemand para provisionamento com o Azure AD automático de usuário, adicione a Cornerstone OnDemand do Marketplace à sua lista de aplicativos SaaS gerenciados.

Para adicionar o Cornerstone OnDemand do Marketplace, siga estas etapas.

1. No [portal do Azure](https://portal.azure.com), no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![Ícone do Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Cornerstone OnDemand** e selecione **Cornerstone OnDemand** no painel de resultados. Para adicionar o aplicativo, selecione **adicionar**.

    ![Cornerstone OnDemand na lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>Atribuir usuários ao Cornerstone OnDemand

Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, decida quais usuários ou grupos no Azure AD precisam de acesso ao Cornerstone OnDemand. Para atribuir esses usuários ou grupos à Cornerstone OnDemand, siga as instruções em [atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Dicas importantes para atribuir usuários à Cornerstone OnDemand

* É recomendável que você atribua um único usuário do Azure AD ao Cornerstone OnDemand para testar a configuração de provisionamento automático de usuário. Você pode atribuir usuários ou grupos adicionais mais tarde.

* Quando você atribuir um usuário à Cornerstone OnDemand, selecione qualquer função específica do aplicativo válida, se disponível, na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>Configurar o provisionamento automático de usuário para Cornerstone OnDemand

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do AD do Azure. Use-o para criar, atualizar e desabilitar usuários ou grupos na Cornerstone OnDemand com base nas atribuições de usuário ou grupo no AD do Azure.

Para configurar o provisionamento automático de usuário para o Cornerstone OnDemand no Azure AD, siga estas etapas.

1. Entre no [Portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais** > **todos os aplicativos** > **Cornerstone OnDemand**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Cornerstone OnDemand**.

    ![O link do Cornerstone OnDemand na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Provisionamento da Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Modo de provisionamento do cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. Sob o **credenciais de administrador** , digite o nome de usuário administrador, a senha de administrador e o domínio da conta da Cornerstone OnDemand:

    * No **Admin Username** caixa, preencha o domínio ou nome de usuário da conta de administrador no locatário do Cornerstone OnDemand. Um exemplo é contoso\admin.

    * No **senha de administrador** caixa, preencha a senha que corresponde ao nome de usuário administrador.

    * No **domínio** caixa, preencha a URL do serviço web do locatário do Cornerstone OnDemand. Por exemplo, o serviço está localizado em `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`, e para a Contoso o domínio é `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`. Para obter mais informações sobre como recuperar a URL do serviço web, consulte [esse pdf](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf).

6. Depois de preencher as caixas mostradas na etapa 5, selecione **Conexão de teste** para certificar-se de que o Azure AD pode se conectar ao Cornerstone OnDemand. Se a conexão falhar, certifique-se de que sua conta da Cornerstone OnDemand tenha permissões de administrador e tente novamente.

    ![Conexão do cornerstone OnDemand teste](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. No **Email de notificação** caixa, digite o endereço de email da pessoa ou grupo para receber as notificações de erro de provisionamento. Selecione o **enviar uma notificação por email quando ocorre uma falha** caixa de seleção.

    ![Email de notificação do cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. Clique em **Salvar**.

9. Na seção **Mapeamentos**, selecione **Sincronizar usuários do Azure Active Directory com a Cornerstone OnDemand**.

    ![Sincronização do cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. Examine os atributos de usuário que são sincronizados do Azure AD ao Cornerstone OnDemand na **mapeamentos de atributo** seção. Os atributos selecionados como propriedades **Correspondentes** são utilizados para corresponder as contas de usuários na Cornerstone OnDemand para operações de atualização. Para salvar as alterações, selecione **salvar**.

    ![Mapeamentos do cornerstone OnDemand atributo](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. Para configurar filtros de escopo, siga as instruções de [tutorial do filtro de escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para habilitar o provisionamento de serviço para o Cornerstone OnDemand do Azure AD a **as configurações** seção, altere **Status de provisionamento** para **em**.

    ![Status de provisionamento do cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Defina os usuários ou grupos que você deseja provisionar à Cornerstone OnDemand. No **as configurações** seção, selecione os valores desejados na **escopo**.

    ![Escopo do cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. Quando estiver pronto para provisionar, selecione **salvar**.

    ![Salvar do cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

Essa operação inicia a sincronização inicial de todos os usuários ou grupos definidos no **escopo** na **configurações** seção. A sincronização inicial levará mais tempo para que as sincronizações posteriores. Eles ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do AD do Azure é executado. 

Você pode usar o **detalhes de sincronização** seção para monitorar o progresso e siga os links para o relatório de atividade de provisionamento. O relatório descreve todas as ações executadas pelo serviço no Cornerstone OnDemand de provisionamento do Azure AD.

Para obter informações sobre como ler os logs de provisionamento do Azure AD, confira [Relatórios sobre o provisionamento automático de contas de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

O atributo Cornerstone OnDemand **posição** espera um valor que corresponde às funções no portal da Cornerstone OnDemand. Para obter uma lista de válidos **posição** valores, acesse **editar registro de usuário > estrutura da organização > posição** no portal do Cornerstone OnDemand.

![Provisionamento da cornerstone OnDemand editar registro de usuário](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![Posição de provisionamento do cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![Lista de posição do cornerstone OnDemand provisionamento](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
