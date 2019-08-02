---
title: 'Tutorial: Configurar o teclado para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no teclado.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 32e634bc089417aaa8080b30a5f77f663a3d8b33
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67611777"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>Tutorial: Configurar o teclado para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no teclado e o Azure Active Directory (Azure AD) para configurar o Azure AD automaticamente provisionar e desprovisionar usuários e/ou grupos para teclado.

> [!NOTE]
>  Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md).

> Esse conector está atualmente em visualização. Para obter mais informações sobre os termos do Microsoft Azure gerais de uso para recursos de visualização, consulte [termos de uso adicionais para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD.
* [Um locatário de teclado](https://www.dialpad.com/pricing/).
* Uma conta de usuário no teclado com permissões de administrador.

## <a name="assign-users-to-dialpad"></a>Atribuir usuários ao teclado
O Azure Active Directory usa um conceito chamado atribuições para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao teclado. Depois de decidir, você pode atribuir esses usuários e/ou grupos para teclado, seguindo as instruções aqui:
 
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>Dicas importantes para atribuir usuários ao teclado

 * É recomendável que um único usuário do Azure AD seja atribuído para teclado para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao teclado, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função de acesso padrão são excluídos do provisionamento.

## <a name="setup-dialpad-for-provisioning"></a>Configurar o teclado para provisionamento

Antes de configurar o teclado para provisionamento com o Azure AD automático de usuário, você precisará recuperar algumas informações de provisionamento do teclado.

1. Entrar no seu [Console de administração do teclado](https://dialpadbeta.com/login) e selecione **configurações de administração**. Certifique-se de que **minha empresa** está selecionado na lista suspensa. Navegue até **autenticação > chaves de API**.

    ![Teclado adicionar SCIM](media/dialpad-provisioning-tutorial/dialpad01.png)

2. Gerar uma nova chave clicando **adicionar uma chave** e configurando as propriedades de seu token do segredo.

    ![Teclado adicionar SCIM](media/dialpad-provisioning-tutorial/dialpad02.png)

    ![Teclado adicionar SCIM](media/dialpad-provisioning-tutorial/dialpad03.png)

3. Clique o **clique para mostrar o valor de** botão para a sua chave de API criado recentemente e copie o valor mostrado. Esse valor será inserido na **segredo do Token** campo na guia provisionamento do seu aplicativo de teclado no portal do Azure. 

    ![Teclado criar Token](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>Adicionar o teclado da Galeria

Para configurar o teclado para provisionamento com o Azure AD automático de usuário, você precisará adicionar o teclado da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o teclado da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No  **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione a **novo aplicativo** botão na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **teclado**, selecione **teclado** no painel de resultados.
    ![Teclado na lista de resultados](common/search-new-app.png)

5. Navegue até a **URL** realçado abaixo em um navegador separado. 

    ![Teclado adicionar SCIM](media/dialpad-provisioning-tutorial/dialpad05.png)

6. No canto superior direito, selecione **efetuar login > teclado de uso online**.

    ![Teclado adicionar SCIM](media/dialpad-provisioning-tutorial/dialpad06.png)

7. Como o teclado é um aplicativo de OpenIDConnect, optar por fazer logon usando sua conta corporativa da Microsoft de teclado.

    ![Teclado adicionar SCIM](media/dialpad-provisioning-tutorial/loginpage.png)

8. Após uma autenticação bem-sucedida, aceite a solicitação de consentimento para a página de consentimento. O aplicativo, em seguida, será adicionado automaticamente ao seu locatário e você será redirecionado para sua conta do teclado.

    ![Teclado adicionar SCIM](media/dialpad-provisioning-tutorial/redirect.png)

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>Configurar o provisionamento automático de usuário para o teclado

Esta seção orienta você pelas etapas de configuração do AD do Azure serviço de provisionamento para criar, atualizar e desabilitar usuários e/ou grupos no teclado com base nas atribuições de usuário e/ou grupo no AD do Azure.

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>Para configurar o provisionamento automático de usuário de teclado no Azure AD:

1. Entre no [Portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**, em seguida, selecione **todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **teclado**.

    ![O link do teclado na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Sob o **credenciais de administrador** seção, de entrada `https://dialpad.com/scim` na **URL de locatário**. Adicione o valor recuperado e salvo anteriormente do teclado no **segredo do Token**. Clique em **Conexão de teste** para garantir que o Azure AD pode se conectar ao teclado. Se a conexão falhar, verifique se que sua conta do teclado tem permissões de administrador e tente novamente.

    ![URL do locatário + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Sob o **mapeamentos** seção, selecione **sincronizar o Azure Active Directory Users para teclado**.

    ![Mapeamentos de usuário de teclado](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. Examine os atributos de usuário que são sincronizados do Azure AD para teclado na **mapeamento de atributos** seção. Os atributos selecionados como **correspondentes** propriedades são usadas para corresponder as contas de usuário do teclado para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de usuário de teclado](media/dialpad-provisioning-tutorial/dialpad07.png)

10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o provisionamento de serviço para o teclado do Azure AD, altere o **Status de provisionamento** ao **na** no **configurações** seção.

    ![Status de provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou grupos que você gostaria serem provisionados para teclado, escolhendo os valores desejados em **escopo** na **configurações** seção.

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar o **detalhes de sincronização** seção para monitorar o progresso e siga os links para relatório de atividade, que descreve todas as ações executadas pelo serviço no teclado de provisionamento do Azure AD de provisionamento.

Para obter mais informações sobre como ler os logs de provisionamento do Azure AD, consulte [relatórios sobre o provisionamento automático de usuário](../manage-apps/check-status-user-account-provisioning.md)
##  <a name="connector-limitations"></a>Limitações do conector
* Teclado não dá suporte atualmente renomeações de grupo. Isso significa que todas as alterações para o **displayName** de um grupo no Azure AD não será atualizado e refletido no teclado.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
