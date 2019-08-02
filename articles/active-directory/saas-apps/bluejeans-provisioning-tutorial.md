---
title: 'Tutorial: Configurar BlueJeans para provisionamento automático do usuário com Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no BlueJeans.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
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
ms.openlocfilehash: 84870ef03e07cdabe994da7c04a103bead48d34f
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672987"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>Tutorial: Configurar BlueJeans para provisionamento automático do usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no BlueJeans e no Azure AD (Azure Active Directory) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no BlueJeans.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>pré-requisitos

O cenário descrito neste tutorial pressupõe que você já possui o seguinte:

* Um locatário do Azure AD
* Um locatário do BlueJeans com o plano [Minha Empresa](https://www.BlueJeans.com/pricing) ou outro com mais recursos, habilitado
* Uma conta de usuário no BlueJeans com permissões de Administrador

> [!NOTE]
> A integração de provisionamento do Azure AD depende da [API do BlueJeans](https://BlueJeans.github.io/developer), disponível para as equipes do BlueJeans no plano Standard ou em outro com mais recursos.

## <a name="adding-bluejeans-from-the-gallery"></a>Adicionando o BlueJeans por meio da galeria

Antes de configurar o BlueJeans para o provisionamento automático de usuário com o Azure AD, é necessário adicionar o BlueJeans por meio da galeria de aplicativos do Azure AD à lista de aplicativos SaaS gerenciados.

**Para adicionar o BlueJeans por meio da galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No  **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione a **novo aplicativo** botão na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **BlueJeans**, selecione **BlueJeans** no painel de resultados e, em seguida, selecione o **Add** botão para adicionar o aplicativo.

    ![BlueJeans na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-bluejeans"></a>Atribuindo usuários ao BlueJeans

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Microsoft Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, é necessário decidir quais usuários e/ou grupos no Azure AD precisam acessar o BlueJeans. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao BlueJeans seguindo estas instruções:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-bluejeans"></a>Dicas importantes para atribuir usuários ao BlueJeans

* É recomendável que um único usuário do Azure AD seja atribuído ao BlueJeans para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao BlueJeans, é necessário selecionar qualquer função específica ao aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-bluejeans"></a>Configurando o provisionamento automático de usuário no BlueJeans

Esta seção orienta você pelas etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no BlueJeans com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o BlueJeans, seguindo as instruções fornecidas no [tutorial do logon único do BlueJeans](bluejeans-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o BlueJeans no Azure AD:

1. Entrar para o [portal do Azure](https://portal.azure.com) e selecione **aplicativos empresariais**, selecione **todos os aplicativos**, em seguida, selecione **BlueJeans**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **BlueJeans**.

    ![O link do BlueJeans na lista Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Provisionamento do BlueJeans](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningTab.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Provisionamento do BlueJeans](./media/bluejeans-provisioning-tutorial/Bluejeans1.png)

5. Na seção **Credenciais de Administrador**, insira o **Nome de Usuário Administrador** e a **Senha do Administrador** de sua conta do BlueJeans. Exemplos desses valores são:

   * No campo **Nome de Usuário Administrador**, popule o nome de usuário da conta do administrador no locatário do BlueJeans. Exemplo: admin@contoso.com.

   * No campo **Senha de Administrador**, preencha a senha correspondente ao nome de usuário do administrador.

6. Ao preencher os campos mostrados na Etapa 5, clique em **Testar Conectividade** para garantir que o Azure AD possa se conectar ao BlueJeans. Se a conexão falhar, verifique se a conta do BlueJeans tem permissões de Administrador e tente novamente.

    ![Provisionamento do BlueJeans](./media/bluejeans-provisioning-tutorial/BluejeansTestConnection.png)

7. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Provisionamento do BlueJeans](./media/bluejeans-provisioning-tutorial/BluejeansNotificationEmail.png)

8. Clique em **Salvar**.

9. Na seção **Mapeamentos**, selecione **Sincronizar usuários do Azure Active Directory com o BlueJeans**.

    ![Provisionamento do BlueJeans](./media/bluejeans-provisioning-tutorial/BluejeansMapping.png)

10. Examine os atributos de usuário que serão sincronizados do Azure AD para o BlueJeans na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência entre as contas de usuário no BlueJeans para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Provisionamento do BlueJeans](./media/bluejeans-provisioning-tutorial/BluejeansUserMappingAtrributes.png)

11. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para habilitar o serviço de provisionamento do Azure AD no BlueJeans, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Provisionamento do BlueJeans](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningStatus.png)

13. Defina os usuários e/ou grupos a serem provisionados para o BlueJeans escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Provisionamento do BlueJeans](./media/bluejeans-provisioning-tutorial/UserGroupSelection.png)

14. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Provisionamento do BlueJeans](./media/bluejeans-provisioning-tutorial/SaveProvisioning.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no BlueJeans.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* Bluejeans não permite nomes de usuários com mais de 30 caracteres.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->

[1]: ./media/bluejeans-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png
