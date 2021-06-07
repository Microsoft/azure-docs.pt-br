---
title: 'Tutorial: Configurar o BitaBIZ para provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o BitaBIZ.
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
ms.openlocfilehash: 8eccc3be7da201ee1e2af046c6b515871ef05adc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96350765"
---
# <a name="tutorial-configure-bitabiz-for-automatic-user-provisioning"></a>Tutorial: Configurar o BitaBIZ para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no BitaBIZ e no Azure AD (Azure Active Directory) a fim de configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para o BitaBIZ.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD.
* [Um locatário do BitaBIZ](https://bitabiz.dk/en/price/).
* Uma conta de usuário no BitaBIZ com permissões de Administrador.

## <a name="assigning-users-to-bitabiz"></a>Como atribuir usuários ao BitaBIZ

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou os grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao BitaBIZ. Depois de decidir, você poderá atribuir esses usuários e/ou grupos ao BitaBIZ seguindo as instruções aqui:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-bitabiz"></a>Dicas importantes para atribuir usuários ao BitaBIZ

* É recomendável que somente um usuário do Azure AD seja atribuído ao BitaBIZ para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao BitaBIZ, você precisa selecionar qualquer função específica ao aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="setup-bitabiz-for-provisioning"></a>Configurar o BitaBIZ para provisionamento

Antes de configurar o BitaBIZ para o provisionamento automático de usuário com o Azure AD, você precisará habilitar o provisionamento do SCIM no BitaBIZ.

1. Entre em seu [Console de Administração do BitaBIZ](https://www.bitabiz.com/login?lang=en). Clique em **CONFIGURAR ADMIN**.

    :::image type="content" source="media/bitabiz-provisioning-tutorial/setup-admin.png" alt-text="Captura de tela do Console de Administração do BitaBIZ, com a opção Configurar administrador realçada." border="false":::

2.  Navegue até **INTEGRAÇÃO**.

    :::image type="content" source="media/bitabiz-provisioning-tutorial/integration.png" alt-text="Captura de tela do Console de Administração do BitaBIZ, com a opção Integração realçada." border="false":::

2.  Navegue até **Provisionamento do Microsoft Azure AD**.  Selecione **Habilitar** no Provisionamento automático de usuário. Copie os valores para **URL de ponto de extremidade de provisionamento do SCIM** e **Token de Portador**. Esses valores serão inseridos nos campos URL do Locatário e Token Secreto na guia Provisionamento do aplicativo BitaBIZ no portal do Azure.

    ![Adicionar SCIM do BitaBIZ](media/bitabiz-provisioning-tutorial/authentication.png)


## <a name="add-bitabiz-from-the-gallery"></a>Adicionar o BitaBIZ da galeria

Para configurar o BitaBIZ para o provisionamento automático de usuário com o Azure AD, é necessário adicioná-lo da galeria de aplicativos do Azure AD à lista de aplicativos SaaS gerenciados.

**Para adicionar o BitaBIZ da galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **BitaBIZ**, selecione **BitaBIZ** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![BitaBIZ na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-bitabiz"></a>Como configurar o provisionamento automático de usuário para o BitaBIZ 

Nesta seção, você verá orientações para seguir as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no BitaBIZ com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o BitaBIZ seguindo as instruções fornecidas no [tutorial de logon único do BitaBIZ](BitaBIZ-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares

### <a name="to-configure-automatic-user-provisioning-for-bitabiz-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o BitaBIZ no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **BitaBIZ**.

    ![O link do BitaBIZ na lista de Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções Gerenciar com a opção Provisionamento destacada.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa Modo de Provisionamento com a opção Automático destacada.](common/provisioning-automatic.png)

5. Na seção Credenciais de Administrador, insira os valores **URL do ponto de extremidade de provisionamento do SCIM** e **Token de Portador** recuperados anteriormente na URL do Locatário e no Token Secreto, respectivamente. Clique em **Testar Conexão** para verificar se o Azure AD pode se conectar ao BitaBIZ. Se a conexão falhar, verifique se a sua conta do BitaBIZ tem permissões de Administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Save** (Salvar).

8. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o BitaBIZ**.

    ![Mapeamentos de Usuário do BitaBIZ](media/bitabiz-provisioning-tutorial/usermapping.png)

9. Examine os atributos de usuário sincronizados do Azure AD com o BitaBIZ na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das contas de usuário no BitaBIZ para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de Usuário do BitaBIZ](media/bitabiz-provisioning-tutorial/user-attribute.png)


10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento do Azure AD para BitaBIZ, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou grupos que você gostaria de provisionar para o BitaBIZ escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para o relatório das atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no BitaBIZ.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* O BitaBIZ requer **userName**, **email**, **firstName** e **lastName** como atributos obrigatórios. 
* No momento, o BitaBIZ não dá suporte a exclusões irreversíveis.

## <a name="additional-resources"></a>Recursos adicionais

* [Como gerenciar o provisionamento de contas de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md).
