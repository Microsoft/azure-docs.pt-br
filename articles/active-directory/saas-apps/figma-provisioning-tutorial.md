---
title: 'Tutorial: Configurar o Figma para provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Figma.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: ec509cc04db87602b3e29230253d9b363a5e30d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96353911"
---
# <a name="tutorial-configure-figma-for-automatic-user-provisioning"></a>Tutorial: Configurar o Figma para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Figma e no Azure AD (Azure Active Directory) a fim de configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para o Figma.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD.
* [Um locatário do Figma](https://www.figma.com/pricing/).
* Uma conta de usuário no Figma com permissões de Administrador.

## <a name="assign-users-to-figma"></a>Atribua usuários ao Figma.
O Azure Active Directory usa um conceito chamado atribuições para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou os grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao Figma. Depois de decidir, você poderá atribuir esses usuários e/ou grupos ao Figma seguindo as instruções aqui:
 
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)
## <a name="important-tips-for-assigning-users-to-figma"></a>Dicas importantes para atribuir usuários ao Figma

 * É recomendável que somente um usuário do Azure AD seja atribuído ao Figma para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Figma, você precisa selecionar qualquer função específica ao aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função Acesso padrão são excluídos do provisionamento.

## <a name="set-up-figma-for-provisioning"></a>Configurar o Figma para provisionamento

Antes de configurar o Figma para o provisionamento automático de usuário com o Azure AD, será necessário recuperar algumas informações de provisionamento do Figma.

1. Entre em seu [Console de Administração do Figma](https://www.Figma.com/). Clique no ícone de engrenagem ao lado do seu locatário.

    :::image type="content" source="media/Figma-provisioning-tutorial/image0.png" alt-text="Captura de tela do console de administração do Figma. Um locatário chamado de Teste do SCIM do AAD está visível. Ao lado do locatário, um ícone de engrenagem está realçado." border="false":::

2. Navegue até **Geral > Atualizar Log em Configurações**.

    :::image type="content" source="media/Figma-provisioning-tutorial/figma03.png" alt-text="Captura de tela da guia Geral do console de administração do Figma. Em Logon e provisionamento, a opção Atualizar log em Configurações está realçada." border="false":::

3. Copie a **ID do Locatário**. Esse valor será usado para construir a URL de ponto de extremidade do SCIM a ser inserida no campo **URL do Locatário** na guia Provisionamento do seu aplicativo Figma no portal do Azure.

    :::image type="content" source="media/Figma-provisioning-tutorial/figma-tenantid.png" alt-text="Captura de tela da seção SSO do SAML no console de administração do Figma. Um rótulo de ID do locatário e um link adjacente que informa Cópia estão realçados." border="false":::

4. Role para baixo e clique em **Gerar Token de API**.

    :::image type="content" source="media/Figma-provisioning-tutorial/token.png" alt-text="Captura de tela da seção de provisionamento do SCIM no console de administração do Figma. Um link rotulado como Gerar token de API está realçado." border="false":::

5. Copie o valor **Token de API**. Esse valor será inserido no campo **Token Secreto** na guia Provisionamento do aplicativo Figma no portal do Azure. 

    :::image type="content" source="media/Figma-provisioning-tutorial/figma04.png" alt-text="Captura de tela de uma página no console de administração do Figma. Em Seu token da API de provisionamento, um espaço reservado para o token está realçado." border="false":::

## <a name="add-figma-from-the-gallery"></a>Adicionar Figma da galeria

Para configurar o Figma para o provisionamento automático de usuário com o Azure AD, é necessário adicioná-lo da galeria de aplicativos do Azure AD à lista de aplicativos SaaS gerenciados.

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Figma**, selecione **Figma** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Figma na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-figma"></a>Como configurar o provisionamento automático de usuário para o Figma 

Nesta seção, você verá orientações para seguir as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Figma com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o Figma seguindo as instruções fornecidas no [tutorial de logon único do Figma](figma-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-figma--in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Figma no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **Figma**.

    ![O link do Figma na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções Gerenciar com a opção Provisionamento destacada.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa Modo de Provisionamento com a opção Automático destacada.](common/provisioning-automatic.png)

5. Na seção **Credenciais de Administrador**, insira `https://www.figma.com/scim/v2/<TenantID>` na **URL do Locatário**, em que **TenantID** é o valor recuperado do Figma anteriormente. Insira o valor do **Token de API** em **Token Secreto**. Clique em **Testar Conexão** para verificar se o Azure AD pode se conectar ao Figma. Se a conexão falhar, verifique se a sua conta do Figma tem permissões de Administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

9. Clique em **Save** (Salvar).

10. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Figma**.

    ![Mapeamentos de Usuário do Figma](media/Figma-provisioning-tutorial/figma05.png)

11. Examine os atributos de usuário sincronizados do Azure AD com o Figma na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das contas de usuário no Figma para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de Usuário do Figma](media/Figma-provisioning-tutorial/figma06.png)

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD para Figma, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você gostaria de provisionar para o Figma escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para o relatório das atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Figma.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)