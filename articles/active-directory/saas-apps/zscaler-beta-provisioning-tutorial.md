---
title: 'Tutorial: Configurar o Zscaler Beta para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no Zscaler Beta.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 0d4945ee97a46c78aac3c4ac508c5f89f5942296
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97937154"
---
# <a name="tutorial-configure-zscaler-beta-for-automatic-user-provisioning"></a>Tutorial: Configurar o Zscaler Beta para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Zscaler Beta e no Azure AD (Azure Active Directory) para configurar o Azure AD a fim de provisionar e desprovisionar automaticamente usuários e/ou grupos no Zscaler Beta.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>


## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já possui o seguinte:

* Um locatário do Azure AD
* Um locatário do Zscaler Beta
* Uma conta de usuário no Zscaler Beta com permissões de Administrador

> [!NOTE]
> A integração de provisionamento do Azure AD depende da API do SCIM do Zscaler Beta, que está disponível para desenvolvedores do Zscaler Beta que têm contas com o pacote Enterprise.

## <a name="adding-zscaler-beta-from-the-gallery"></a>Adicionar o Zscaler Beta da galeria

Antes de configurar o Zscaler Beta para o provisionamento automático de usuário com o Azure AD, é necessário adicionar o Zscaler Beta da galeria de aplicativos do Azure AD à lista de aplicativos SaaS gerenciados.

**Para adicionar o Zscaler Beta da galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Zscaler Beta**, selecione **Zscaler Beta** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Zscaler Beta na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-zscaler-beta"></a>Como atribuir usuários ao Zscaler Beta

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Microsoft Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, é necessário decidir quais usuários e/ou grupos do Azure AD precisam acessar o Zscaler Beta. Depois de decidir isso, você poderá atribuir esses usuários e/ou grupos ao Zscaler Beta seguindo estas instruções:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zscaler-beta"></a>Dicas importantes para atribuir usuários ao Zscaler Beta

* Recomendamos que apenas um usuário do Azure AD seja atribuído ao Zscaler Beta para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Zscaler Beta, você deverá selecionar qualquer função válida específica do aplicativo (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-zscaler-beta"></a>Configuração do provisionamento automático de usuário no Zscaler Beta

Esta seção descreve as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Zscaler Beta com base nas atribuições de usuário e/ou de grupo do Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o Zscaler Beta seguindo as instruções fornecidas no [tutorial de logon único do Zscaler Beta](zscaler-beta-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

> [!NOTE]
> Quando os usuários e grupos são provisionados ou desprovisionados, é recomendável reiniciar periodicamente o provisionamento para garantir que as associações de grupo sejam atualizadas corretamente. A realização de uma reinicialização forçará nosso serviço a reavaliar todos os grupos e atualizar as associações.  

### <a name="to-configure-automatic-user-provisioning-for-zscaler-beta-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Zscaler Beta no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com) e selecione **Aplicativos Empresariais**, **Todos os aplicativos** e **Zscaler Beta**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Zscaler Beta**.

    ![O link do Zscaler Beta na lista Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Há uma lista de guias organizadas em categorias e intituladas ZScaler Beta – Provisionamento/Aplicativo Empresarial. A guia Provisionar da categoria Gerenciar está selecionada.](./media/zscaler-beta-provisioning-tutorial/provisioning-tab.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![O modo Automático foi selecionado na lista suspensa Modo de Provisionamento. Há campos para Credenciais de Administrador, usados para se conectar à API do Zscaler Beta e há o botão Testar Conexão.](./media/zscaler-beta-provisioning-tutorial/provisioning-credentials.png)

5. Na seção **Credenciais de Administrador**, insira a **URL do Locatário** e o **Token Secreto** da conta do Zscaler Beta, conforme descrito na Etapa 6.

6. Para obter a **URL do Locatário** e o **Token Secreto**, navegue até **Administração > Configurações de Autenticação** na interface do usuário do portal do Zscaler Beta e clique em **SAML** em **Tipo de Autenticação**.

    ![Em Configurações de Autenticação, no Perfil de Autenticação, o Tipo de Diretório selecionado é o BD Hospedado e o Tipo de Autenticação selecionado é o SAML.](./media/zscaler-beta-provisioning-tutorial/secret-token-1.png)

    Clique em **Configurar o SAML** para abrir as opções de **Configuração do SAML**.

    ![Em Configurar o SAML, as opções Habilitar o Provisionamento Automático do SAML e Habilitar o Provisionamento Baseado em SCIM são selecionadas. As caixas de texto URL de Base e Token de Portador são realçadas.](./media/zscaler-beta-provisioning-tutorial/secret-token-2.png)

    Selecione **Habilitar o Provisionamento Baseado em SCIM** para recuperar a **URL de Base** e o **Token de Portador**, então salve as configurações. Copie a **URL de Base** para a **URL do Locatário** e o **Token de Portador** para o **Token Secreto** no portal do Azure.

7. Quando preencher os campos mostrados na Etapa 5, clique em **Testar Conexão** para verificar se o Azure AD pode se conectar ao Zscaler Beta. Se a conexão falhar, verifique se a conta do Zscaler Beta tem permissões de administrador e tente novamente.

    ![Nas Credenciais de Administrador, os campos URL do Locatário e Token Secreto têm valores, e o botão Testar Conexão está realçado.](./media/zscaler-beta-provisioning-tutorial/test-connection.png)

8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![A caixa de texto Email de Notificação está vazia e a caixa de seleção Enviar uma notificação por email quando ocorrer uma falha está desmarcada.](./media/zscaler-beta-provisioning-tutorial/notification.png)

9. Clique em **Save** (Salvar).

10. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Zscaler Beta**.

    ![A opção Sincronizar Usuários do Azure Active Directory com o Zscaler Beta está selecionada e habilitada.](./media/zscaler-beta-provisioning-tutorial/user-mappings.png)

11. Examine os atributos de usuário que serão sincronizados entre o Azure AD e o Zscaler Beta na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das contas de usuário no Zscaler Beta para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Na seção Mapeamentos de Atributos dos atributos de usuário, os atributos do Active Directory serão mostrados ao lado dos atributos do Zscaler Beta com os quais serão sincronizados. Cada par de atributos é mostrado como Correspondentes.](./media/zscaler-beta-provisioning-tutorial/user-attribute-mappings.png)

12. Na seção **Mapeamentos**, selecione **Sincronizar Grupos do Azure Active Directory com o Zscaler Beta**.

    ![A opção Sincronizar Grupos do Azure Active Directory com o Zscaler Beta está selecionada e habilitada.](./media/zscaler-beta-provisioning-tutorial/group-mappings.png)

13. Examine os atributos de grupo que serão sincronizados entre o Azure AD e o Zscaler Beta na seção **Mapeamentos de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência dos grupos no Zscaler Beta para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Na seção Mapeamentos de Atributos dos atributos de grupo, os atributos do Active Directory serão mostrados ao lado dos atributos do Zscaler Beta com os quais serão sincronizados. Cada par de atributos é mostrado como Correspondentes.](./media/zscaler-beta-provisioning-tutorial/group-attribute-mappings.png)

14. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para habilitar o serviço de provisionamento do Azure AD no Zscaler Beta, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![O status de provisionamento é mostrado e definido como ativado.](./media/zscaler-beta-provisioning-tutorial/provisioning-status.png)

16. Defina os usuários e/ou os grupos que você deseja provisionar no Zscaler Beta escolhendo os valores desejados em **Escopo**, na seção **Configurações**.

    ![A lista suspensa Escopo é mostrada e a opção Sincronizar apenas usuários e grupos atribuídos está selecionada. O outro valor disponível é Sincronizar todos os usuários e grupos.](./media/zscaler-beta-provisioning-tutorial/scoping.png)

17. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![O botão Salvar, na parte superior do Zscaler Beta – Provisionamento está realçado. Também há o botão Descartar.](./media/zscaler-beta-provisioning-tutorial/save-provisioning.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Zscaler Beta.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-03.png