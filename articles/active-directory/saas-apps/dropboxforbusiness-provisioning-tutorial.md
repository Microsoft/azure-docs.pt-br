---
title: 'Tutorial: Configurar o Dropbox for Business para provisionamento automático de usuário com o Azure Active Directory   Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no Dropbox for Business.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 04d17e17ef11696efd52f04ea83639f2a9b81fea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96938742"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Tutorial: Configurar o Dropbox for Business para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Dropbox for Business e no Azure AD (Azure Active Directory) a fim de configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no Dropbox for Business.

> [!IMPORTANT]
> A partir de 01/04/2021, a antiga integração do Dropbox será preterida pela Microsoft e pela Dropbox. Para evitar a interrupção do serviço, recomendamos fazer a migração para a nova integração do Dropbox que dá suporte a grupos. Para fazer a migração para a nova integração do Dropbox, adicione e configure uma nova instância do Dropbox para provisionamento no seu locatário do Azure AD usando as etapas abaixo. Depois de configurar a nova integração do Dropbox, desabilite o provisionamento na antiga integração do Dropbox para evitar conflitos de provisionamento. Para obter etapas mais detalhadas sobre como migrar para a nova integração do Dropbox, confira [Fazer a atualização para o aplicativo Dropbox for Business mais recente usando o Azure AD](https://help.dropbox.com/installs-integrations/third-party/update-dropbox-azure-ad-connector).

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do Dropbox for Business](https://www.dropbox.com/business/pricing)
* Uma conta de usuário no Dropbox for Business com permissões de Administrador.

## <a name="add-dropbox-for-business-from-the-gallery"></a>Adicionar o Dropbox for Business por meio da galeria

Antes de configurar o Dropbox for Business para provisionamento automático de usuário com o Azure AD, é necessário adicionar o Dropbox for Business por meio da galeria de aplicativos do Azure AD à lista de aplicativos SaaS gerenciados.

**Para adicionar o Dropbox for Business por meio da galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Dropbox for Business**, selecione **Dropbox for Business** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Dropbox for Business na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-dropbox-for-business"></a>Atribuindo usuários ao Dropbox for Business

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou os grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, decida quais usuários e/ou grupos no Azure AD precisam ter acesso ao Dropbox for Business. Depois de decidir isso, você poderá atribuir esses usuários e/ou grupos ao Dropbox for Business seguindo estas instruções:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Dicas importantes para atribuir usuários ao Dropbox for Business

* Recomendamos a atribuição de um só usuário do Azure AD ao Dropbox for Business para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Dropbox for Business, você precisará selecionar qualquer função válida específica do aplicativo (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-dropbox-for-business"></a>Como configurar o provisionamento automático de usuário no Dropbox for Business 

Esta seção descreve as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Dropbox for Business com base em atribuições de usuário e/ou de grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o Dropbox for Business seguindo as instruções fornecidas no [tutorial de logon único do Dropbox for Business](dropboxforbusiness-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-dropbox-for-business-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Dropbox for Business no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Dropbox for Business**.

    ![O link do Dropbox for Business na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções Gerenciar com a opção Provisionamento destacada.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa Modo de Provisionamento com a opção Automático destacada.](common/provisioning-automatic.png)

5. Na seção **Credenciais de Administrador**, clique em **Autorizar**. Ela abre uma caixa de diálogo de logon do Dropbox for Business em uma nova janela do navegador.

    ![Provisionamento ](common/provisioning-oauth.png)

6. Na caixa de diálogo **Entrar no Dropbox for Business para vinculá-lo ao Azure AD**, entre no locatário do Dropbox for Business e confirme sua identidade.

    ![Credenciais do Dropbox for Business](media/dropboxforbusiness-provisioning-tutorial/dropbox01.png)

7. Depois de concluir as etapas 5 e 6, clique em **Testar Conectividade** para garantir que o Azure AD possa se conectar ao Dropbox for Business. Se a conexão falhar, verifique se a sua conta do Dropbox for Business tem permissões de Administrador e tente novamente.

    ![Token](common/provisioning-testconnection-oauth.png)

8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

9. Clique em **Save** (Salvar).

10. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Dropbox**.

    ![Mapeamentos de Usuário do Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-mapping.png)

11. Examine os atributos de usuário que são sincronizados do Azure AD para o Dropbox na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência entre as contas de usuário do Dropbox em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de Usuário do Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-attributes.png)

12. Na seção **Mapeamentos**, selecione **Sincronizar Grupos do Azure Active Directory com o Dropbox**.

    ![Mapeamentos de Grupo do Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-mapping.png)

13. Examine os atributos de grupo que são sincronizados do Azure AD para o Dropbox na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência dos grupos no Dropbox em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de Grupo do Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-attributes.png)

14. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para habilitar o serviço de provisionamento do Azure AD para o Dropbox, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

16. Defina os usuários e/ou os grupos que deseja provisionar no Dropbox escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

17. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para o relatório das atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Dropbox.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector
 
* O Dropbox não dá suporte à suspensão de usuários convidados. Se um usuário convidado for suspenso, esse usuário será excluído.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

