---
title: 'Tutorial: Bonusly para provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Aprenda a configurar o Azure Active Directory para provisionar e descontinuar automaticamente as contas de usuário para o Bonusly.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 879b0ee9-042a-441b-90a7-8c364d62426a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8368fc5369ee6cd93bbf472d4904cd647d66058d
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673030"
---
# <a name="tutorial-configure-bonusly-for-automatic-user-provisioning"></a>Tutorial: Configurar o Bonusly para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Bonusly e no Microsoft Azure Active Directory (Azure Active Directory) para configurar o Microsoft Azure Active Directory para provisionar e desprovisionar automaticamente usuários e/ou grupos no Bonusly.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>pré-requisitos

O cenário descrito neste tutorial pressupõe que você já possui o seguinte:

* Um locatário do Azure AD
* Um [locatário do Bonusly](https://bonus.ly/pricing)
* Uma conta de usuário no Bonusly com permissões de administrador

> [!NOTE]
> A integração do provisionamento do Azure AD depende da [API Rest do Bonusly](https://bonusly.gelato.io/reference), que está disponível para os desenvolvedores do Bonusly.

## <a name="adding-bonusly-from-the-gallery"></a>Adicionando o Bonusly da galeria

Antes de configurar o Bonusly para o provisionamento automático de usuário com o Microsoft Azure Active Directory, é necessário adicionar o Bonusly por meio da galeria de aplicativos do Microsoft Azure Active Directory à lista de aplicativos SaaS gerenciados.

**Para adicionar o Bonusly da galeria de aplicativos do Azure AD, siga estas etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Bonusly**, selecione **Bonusly** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Bonusly na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-bonusly"></a>Atribuindo usuários ao Bonusly

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Microsoft Azure AD são sincronizados. 

Antes de configurar e habilitar o provisionamento automático de usuário, é necessário decidir quais usuários e/ou grupos no Microsoft Azure Active Directory precisam acessar o Bonusly. Depois de decidir, é possível atribuir esses usuários e/ou grupos ao Bonusly seguindo estas instruções:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-bonusly"></a>Dicas importantes para atribuir usuários ao Bonusly

* É recomendável que um único usuário do Azure AD seja atribuído ao Bonusly para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Bonusly, é necessário selecionar qualquer função específica ao aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-bonusly"></a>Configurando o provisionamento automático de usuário no Bonusly

Esta seção orienta você pelas etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Bonusly com base em atribuições de usuário e/ou de grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o Bonusly, seguindo as instruções fornecidas no [tutorial de logon único do Bonusly](bonus-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-bonusly-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Bonusly no Azure AD:

1. Entrar para o [portal do Azure](https://portal.azure.com) e selecione **aplicativos empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Bonusly**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha o **Bonusly**.

    ![O link do Bonusly na lista de Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Provisionamento Bonusly](./media/bonusly-provisioning-tutorial/ProvisioningTab.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Provisionamento Bonusly](./media/bonusly-provisioning-tutorial/ProvisioningCredentials.png)

5. Na seção **Credenciais de administrador**, insira o **Token secreto** da sua conta Bonusly conforme descrito na etapa 6.

    ![Provisionamento Bonusly](./media/bonusly-provisioning-tutorial/secrettoken.png)

6. O **Token Secreto** da sua conta Bonusly está localizado em **Administrador > Empresa > Integrações**. Na seção **Se desejar codificar**, clique em **API > Criar novo token de acesso de API** para criar um novo token secreto.

    ![Provisionamento Bonusly](./media/bonusly-provisioning-tutorial/BonuslyIntegrations.png)

    ![Provisionamento Bonusly](./media/bonusly-provisioning-tutorial/BonsulyRestApi.png)

    ![Provisionamento Bonusly](./media/bonusly-provisioning-tutorial/CreateToken.png)

7. Na tela seguinte, digite um nome para o token de acesso na caixa de texto fornecida e pressione **Criar chave de Api**. O novo token de acesso será exibido durante alguns segundos em uma pop-up.

    ![Provisionamento Bonusly](./media/bonusly-provisioning-tutorial/Token01.png)

    ![Provisionamento Bonusly](./media/bonusly-provisioning-tutorial/Token02.png)

8. Ao popular os campos mostrados na Etapa 5, clique em **Testar conexão** para garantir que o Azure AD pode se conectar ao Bonusly. Se a conexão falhar, verifique se sua conta Bonusly tem permissões de administrador e tente novamente.

    ![Provisionamento Bonusly](./media/bonusly-provisioning-tutorial/TestConnection.png)

9. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Provisionamento Bonusly](./media/bonusly-provisioning-tutorial/EmailNotification.png)

10. Clique em **Salvar**.

11. Na seção **Mapeamentos**, selecione **Sincronizar usuários do Azure Active Directory com o Bonusly**.

    ![Provisionamento Bonusly](./media/bonusly-provisioning-tutorial/UserMappings.png)

12. Examine os atributos de usuário que serão sincronizados do Azure AD com o Bonusly na seção **Mapeamento de atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência entre as contas de usuário no Bonusly para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Provisionamento Bonusly](./media/bonusly-provisioning-tutorial/UserAttributeMapping.png)

13. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Para habilitar o serviço de provisionamento do Azure AD para o Bonusly, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Provisionamento Bonusly](./media/bonusly-provisioning-tutorial/ProvisioningStatus.png)

15. Defina os usuários e/ou grupos que você gostaria de provisionar no Bonusly escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Provisionamento Bonusly](./media/bonusly-provisioning-tutorial/ScopeSync.png)

16. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Provisionamento Bonusly](./media/bonusly-provisioning-tutorial/SaveProvisioning.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Bonusly.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/bonusly-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bonusly-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/bonusly-provisioning-tutorial/tutorial_general_03.png
