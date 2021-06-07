---
title: 'Tutorial: Configurar o Dialpad para provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Dialpad.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: b88e618da3f8a23c0517aaeb251e54bf559fc468
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96014492"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>Tutorial: Configurar o Dialpad para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Dialpad e no Azure AD (Active Directory) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para o Dialpad.

> [!NOTE]
>  Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).

> Atualmente, esse conector está em versão prévia. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD.
* [Um locatário do Dialpad](https://www.dialpad.com/pricing/).
* Uma conta de usuário no Dialpad com permissões de administrador.

## <a name="assign-users-to-dialpad"></a>Atribuir usuários ao Dialpad
O Azure Active Directory usa um conceito chamado atribuições para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou os grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Para configurar e habilitar o provisionamento automático de usuário, decida quais usuários e/ou grupos no Azure AD precisam de acesso ao Dialpad. Depois de decidir, será possível atribuir esses usuários e/ou grupos ao Dialpad seguindo estas instruções:
 
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>Dicas importantes para atribuir usuários ao Dialpad

 * Recomendamos que somente um usuário do Azure AD seja atribuído ao Dialpad para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Dialpad, é necessário selecionar qualquer função específica ao aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função Acesso padrão são excluídos do provisionamento.

## <a name="setup-dialpad-for-provisioning"></a>Configurar o Dialpad para provisionamento

Antes de configurar o Dialpad para o provisionamento automático de usuário com o Azure AD, será necessário recuperar algumas informações de provisionamento do Dialpad.

1. Entre no [Console de Administração do Dialpad](https://dialpadbeta.com/login) e selecione **Configurações do administrador**. Verifique se **Minha Empresa** está selecionado na lista suspensa. Navegue até **Autenticação > Chaves de API**.

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad01.png" alt-text="Captura de tela do console de administração do Dialpad, com o ícone de configurações, Minha Empresa, Autenticação e chaves de API realçados e Minha Empresa selecionado." border="false":::

2. Gere uma nova chave clicando em **Adicionar uma chave** e configurando as propriedades do seu token secreto.

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad02.png" alt-text="Captura de tela da página Chaves de API no console de administração do Dialpad. Adicionar uma chave está realçado." border="false":::

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad03.png" alt-text="Captura de tela da página Editar chave de API no console de administração do Dialpad. O botão Salvar está realçado." border="false":::

3. Clique no botão **Clique para mostrar o valor** de sua chave de API criada recentemente e copie o valor mostrado. Esse valor será inserido no campo **Token Secreto** na guia Provisionamento do aplicativo Dialpad no portal do Azure. 

    ![Criar Token do Dialpad](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>Adicionar Dialpad da galeria

Antes de configurar o Dialpad para o provisionamento automático de usuário com o Azure AD, é necessário adicionar o Dialpad da galeria de aplicativos do Azure AD à lista de aplicativos SaaS gerenciados.

**Para adicionar o Dialpad da galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Dialpad** e selecione **Dialpad** no painel de resultados.
    ![Dialpad na lista de resultados](common/search-new-app.png)

5. Navegue até a **URL** realçada abaixo em um navegador separado. 

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad05.png" alt-text="Captura de tela de uma página exibindo informações sobre o aplicativo Dialpad. Em URL, um endereço é listado e realçado." border="false":::

6. No canto superior direito, selecione **Fazer logon > Usar o Dialpad online**.

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad06.png" alt-text="Captura de tela do site do Dialpad. A opção Fazer logon está realçada e a guia Fazer logon está aberta. A opção Usar o Dialpad online também está realçada." border="false":::

7. Como o Dialpad é um aplicativo OpenIDConnect, opte por fazer logon no Dialpad usando sua conta corporativa da Microsoft.

    :::image type="content" source="media/dialpad-provisioning-tutorial/loginpage.png" alt-text="Captura de tela da página Começar a fazer chamadas no site do Dialpad. O botão Fazer logon com o Office 365 está realçado." border="false":::

8. Após uma autenticação bem-sucedida, aceite a solicitação de consentimento da página de consentimento. O aplicativo será automaticamente adicionado ao seu locatário e você será redirecionado à sua conta do Dialpad.

    :::image type="content" source="media/dialpad-provisioning-tutorial/redirect.png" alt-text="Captura de tela mostrando uma página de autenticação da Microsoft informando que o aplicativo Dialpad solicitou acesso a alguns dados. O botão Aceitar está realçado." border="false":::

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>Configurar o provisionamento automático de usuário para o Dialpad

Esta seção orienta você pelas etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Dialpad com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Dialpad no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Dialpad**.

    ![O link do Dialpad na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções Gerenciar com a opção Provisionamento destacada.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa Modo de Provisionamento com a opção Automático destacada.](common/provisioning-automatic.png)

5. Na seção **Credenciais de Administrador**, insira `https://dialpad.com/scim` em **URL do Locatário**. Insira o valor que você recuperou e salvou anteriormente do Dialpad no **Token Secreto**. Clique em **Testar Conectividade** para verificar se o Azure AD pode se conectar ao Dialpad. Se a conexão falhar, verifique se a sua conta do Dialpad tem permissões de Administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Save** (Salvar).

8. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Dialpad**.

    ![Mapeamento de Usuário do Dialpad](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. Examine os atributos de usuário sincronizados do Azure AD com o Dialpad na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das contas de usuário no Dialpad para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de Usuário do Dialpad](media/dialpad-provisioning-tutorial/dialpad07.png)

10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento do Azure AD no Dialpad, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou grupos que você gostaria de provisionar para o Dialpad escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Dialpad.

Para obter mais informações sobre como ler os logs de provisionamento do Azure AD, confira [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md)
##  <a name="connector-limitations"></a>Limitações do conector
* O Dialpad não dá suporte a renomeações de grupo hoje. Isso significa que as alterações no **displayName** de um grupo no Azure AD não serão atualizadas nem refletidas no Dialpad.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
