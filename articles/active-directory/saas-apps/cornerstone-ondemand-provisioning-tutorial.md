---
title: 'Tutorial: Configurar o Cornerstone OnDemand para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para a Cornerstone OnDemand.
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
ms.openlocfilehash: 59c599167089d222324ed880c18e68d763f5e468
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358446"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Tutorial: Configurar o Cornerstone OnDemand para o provisionamento automático de usuário

Este tutorial demonstra as etapas a serem executadas na Cornerstone OnDemand e no Microsoft Azure AD (Azure Active Directory) para configurar o Microsoft Azure AD para provisionar e desprovisionar automaticamente usuários ou grupos para a Cornerstone OnDemand.

> [!NOTE]
> Este tutorial descreve um conector baseado no serviço de provisionamento de usuário do Azure Active Directory. Para obter informações sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e o desprovisionamento de usuários para aplicativos SaaS (software como serviço) com o Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você possui:

* Um locatário do Azure AD.
* Um locatário do Cornerstone OnDemand.
* Uma conta de usuário na Cornerstone OnDemand com permissões de administrador.

> [!NOTE]
> A integração de provisionamento do Azure Active Directory depende do [serviço Web da Cornerstone OnDemand](https://www.cornerstoneondemand.com/). Esse serviço está disponível para as equipes da Cornerstone OnDemand.

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>Adicionar Cornerstone OnDemand do Azure Marketplace

Antes de configurar a Cornerstone OnDemand para provisionamento automático de usuário com o Azure Active Directory, adicione a Cornerstone OnDemand do Marketplace à sua lista de aplicativos de SaaS gerenciados.

Para adicionar a Cornerstone OnDemand do Marketplace, siga estas etapas.

1. No [Portal do Microsoft Azure](https://portal.azure.com), no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O ícone do Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Cornerstone OnDemand** e selecione **Cornerstone OnDemand** no painel de resultados. Para adicionar o aplicativo, selecione **Adicionar**.

    ![Cornerstone OnDemand na lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>Atribuir usuários à Cornerstone OnDemand

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários ou os grupos que foram atribuídos a um aplicativo no Azure Active Directory são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, decida quais usuários ou grupos do Azure Active Directory precisam acessar o Cornerstone OnDemand. Para atribuir esses usuários ou grupos à Cornerstone OnDemand, siga as instruções em [Atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Dicas importantes para atribuir usuários à Cornerstone OnDemand

* Recomendamos que você atribua um único usuário do Microsoft Azure Active Directory à Cornerstone OnDemand para testar a configuração de provisionamento automático de usuário. Você pode atribuir usuários ou grupos adicionais mais tarde.

* Ao atribuir um usuário à Cornerstone OnDemand, selecione qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>Configurar o provisionamento automático de usuário para a Cornerstone OnDemand

Esta seção aborda as etapas para configurar o serviço de provisionamento do Azure Active Directory. Use-o para criar, atualizar e desabilitar usuários ou grupos na Cornerstone OnDemand com base em atribuições de usuário ou de grupo no Azure Active Directory.

Para configurar o provisionamento automático de usuário para a Cornerstone OnDemand no Microsoft Azure AD, siga as etapas a seguir.

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** > **Todos os aplicativos** > **Cornerstone OnDemand**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Cornerstone OnDemand**.

    ![O link da Cornerstone OnDemand na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Provisionamento da Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Modo de provisionamento da Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. Na seção **Credenciais de Administrador**, insira o nome de usuário do administrador, a senha de administrador e o domínio da conta da Cornerstone OnDemand:

    * No campo **Nome de Usuário do Administrador**, insira o nome de usuário ou o domínio da conta de administrador no Locatário da Cornerstone OnDemand. Por exemplo, contoso\administrador.

    * Na caixa **Senha do Administrador**, insira a senha que corresponde ao nome de usuário administrador.

    * No campo **Domínio**, insira a URL do serviço Web do locatário Cornerstone OnDemand. Por exemplo, o serviço está localizado em `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`, e para a Contoso o domínio é `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`. Para obter mais informações sobre como recuperar a URL do serviço Web, consulte [este PDF](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf).

6. Depois de preencher as caixas mostradas na etapa 5, selecione **Testar Conexão** para garantir que o Azure Active Directory possa se conectar à Cornerstone OnDemand. Se a conexão falhar, garanta que a conta da Cornerstone OnDemand tenha permissões de administrador e tente novamente.

    ![Conexão-teste da Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. Na caixa **Email de Notificação**, insira o endereço de email da pessoa ou grupo que deve receber as notificações do erro de provisionamento. Marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação da Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. Clique em **Salvar**.

9. Na seção **Mapeamentos**, selecione **Sincronizar usuários do Azure Active Directory com a Cornerstone OnDemand**.

    ![A sincronização da Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. Revise os atributos de usuário que são sincronizados do Azure Active Directory com a Cornerstone OnDemand na seção **Mapeamentos de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são utilizados para corresponder as contas de usuários na Cornerstone OnDemand para operações de atualização. Selecione **Salvar** para salvar as alterações.

    ![Mapeamentos de atributo da Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. Para configurar filtros de escopo, siga as instruções fornecidas no [tutorial sobre filtros de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para habilitar o serviço de provisionamento do Azure Active Directory para a Cornerstone OnDemand, na seção **Configurações**, altere **Status de Provisionamento** para **Ativado**.

    ![Status de provisionamento da Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Defina os usuários ou os grupos que você deseja provisionar para a Cornerstone OnDemand. Na seção **Configurações**, selecione os valores desejados em **Escopo**.

    ![Escopo da Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. Quando estiver pronto para fazer o provisionamento, selecione **Salvar**.

    ![Salvamento da Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

Essa operação inicia a sincronização inicial de todos os usuários ou grupos definidos em **Escopo**, na seção **Configurações**. A sincronização inicial demora mais para ser executada do que as sincronizações posteriores. Elas ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure Active Directory seja executado. 

É possível usar a seção **Detalhes de Sincronização** para monitorar o andamento e seguir os links para o relatório de atividade de provisionamento. O relatório descreve todas as ações executadas pelo serviço de provisionamento do Azure Active Directory na Cornerstone OnDemand.

Para obter informações sobre como ler os logs de provisionamento do Azure AD, confira [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

O atributo Cornerstone OnDemand **posição** espera um valor que corresponde às funções no portal da Cornerstone OnDemand. Para obter uma lista de valores válidos de **Posição**, acesse **Editar Registro de Usuário > Estrutura Organizacional > Posição** no portal da Cornerstone OnDemand.

![Edição de registro do usuário do provisionamento da Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![Posição de provisionamento da Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![Lista de posição do provisionamento da Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar provisionamento de conta de usuário para aplicativos empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
