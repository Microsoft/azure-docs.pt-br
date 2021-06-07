---
title: 'Tutorial: Configurar o Druva para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no Druva.
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
ms.openlocfilehash: 2ef564ae61aab10e1d100ccaf2547248e4a6a8e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96352160"
---
# <a name="tutorial-configure-druva-for-automatic-user-provisioning"></a>Tutorial: Configurar o Druva para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Druva e no Azure AD (Active Directory) a fim de configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no Druva.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD.
* [Um locatário do Druva](https://www.druva.com/products/pricing-plans/).
* Uma conta de usuário no Druva com permissões de Administrador.

## <a name="assigning-users-to-druva"></a>Como atribuir usuários ao Druva

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou os grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Para configurar e habilitar o provisionamento automático de usuário, decida quais usuários e/ou grupos no Azure AD precisam de acesso ao Druva. Depois de decidir isso, você poderá atribuir esses usuários e/ou grupos ao Druva seguindo estas instruções:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-druva"></a>Dicas importantes para atribuir usuários ao Druva

* Recomendamos que um só usuário do Azure AD seja atribuído ao Druva para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Druva, você precisará selecionar qualquer função válida específica do aplicativo (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="setup-druva-for-provisioning"></a>Configurar o Druva para provisionamento

Antes de configurar o Druva para o provisionamento automático de usuário com o Azure AD, você precisará habilitar o provisionamento do SCIM no Druva.

1. Entre no [Console de Administração do Druva](https://console.druva.com). Procure **Druva > Em Sincronia**.

    ![Console de Administração do Druva](media/druva-provisioning-tutorial/menubar.png)

2. Procure **Gerenciar** > **Implantações** > **Usuários**.

    :::image type="content" source="media/druva-provisioning-tutorial/manage.png" alt-text="Captura de tela do console de administração do Druva. A opção Gerenciar está realçada e o menu Gerenciar está visível. Nesse menu, em Implantações, a opção Usuários está realçada." border="false":::

3.  Procure **Configurações**. Clique em **Gerar Token**.

    :::image type="content" source="media/druva-provisioning-tutorial/settings.png" alt-text="Captura de tela de uma página no console de administração do Druva. A opção Configurações está realçada e a guia Configurações está aberta. O botão Gerar token está realçado." border="false":::

4.  Copie o valor do **Token de autenticação**. Esse valor será inserido no campo **Token Secreto** na guia Provisionamento do aplicativo Druva no portal do Azure.
    
    :::image type="content" source="media/druva-provisioning-tutorial/auth.png" alt-text="Captura de tela da página Criar token no console de administração do Druva. Um link rotulado Copiar Token está disponível para a cópia do valor do token de autenticação." border="false":::

## <a name="add-druva-from-the-gallery"></a>Adicionar o Druva por meio da galeria

Para configurar o Druva para o provisionamento automático de usuário com o Azure AD, é necessário adicionar o Druva por meio da galeria de aplicativos do Azure AD à lista de aplicativos SaaS gerenciados.

**Para adicionar o Druva por meio da galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Druva**, selecione **Druva** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Druva na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-druva"></a>Como configurar o provisionamento automático de usuário no Druva 

Esta seção descreve as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Druva com base em atribuições de usuário e/ou de grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o Druva seguindo as instruções fornecidas no [tutorial de logon único do Druva](druva-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-druva-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Druva no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Druva**.

    ![O link do Druva na lista de Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções Gerenciar com a opção Provisionamento destacada.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa Modo de Provisionamento com a opção Automático destacada.](common/provisioning-automatic.png)

5.  Na seção Credenciais de Administrador, insira `https://apis.druva.com/insync/scim` em **URL do Locatário**. Insira o valor do **Token de autenticação** em **Token Secreto**. Clique em **Testar Conectividade** para verificar se o Azure AD pode se conectar ao Druva. Se a conexão falhar, verifique se a sua conta do Druva tem permissões de Administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Email de Notificação**, insira o endereço de email de uma pessoa ou um grupo que deverá receber as notificações de erro de provisionamento e selecione **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Save** (Salvar).

8. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Druva**.

    ![Mapeamentos de Usuário no Druva](media/druva-provisioning-tutorial/usermapping.png)

9. Examine os atributos de usuário que serão sincronizados do Azure AD para o Druva na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das contas de usuário no Druva em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de Usuário no Druva](media/druva-provisioning-tutorial/userattribute.png)


10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento do Azure AD no Druva, altere o **Status de provisionamento** para **Ativado** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou os grupos que deseja provisionar no Druva escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

    Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para o relatório das atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Druva.

    Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).
    
## <a name="connector-limitations"></a>Limitações do conector

* O Druva exige o **email** como um atributo obrigatório. 

## <a name="additional-resources"></a>Recursos adicionais

* [Como gerenciar o provisionamento de contas de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md).
