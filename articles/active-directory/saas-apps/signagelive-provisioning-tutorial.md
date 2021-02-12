---
title: 'Tutorial: Configurar o Signagelive para provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Signagelive.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: Zhchia
ms.openlocfilehash: 10ad06041e8136b5661b1b1ff487cd4d3b0f5153
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358381"
---
# <a name="tutorial-configure-signagelive--for-automatic-user-provisioning"></a>Tutorial: Configurar o Signagelive para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Signagelive e no Azure AD (Active Directory) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para o Signagelive.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD.
* [Um locatário do Signagelive](https://signagelive.com/pricing/)
* Uma conta de usuário no Signagelive com permissões de Administrador.

## <a name="assigning-users-to-signagelive"></a>Atribuir usuários ao Signagelive   

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou os grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Para configurar e habilitar o provisionamento automático de usuário, decida quais usuários e/ou grupos no Azure AD precisam de acesso ao Signagelive. Depois de decidir, será possível atribuir esses usuários e/ou grupos ao Signagelive seguindo estas instruções:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-signagelive"></a>Dicas importantes para atribuir usuários ao Signagelive   

* Recomendamos que somente um usuário do Azure AD seja atribuído ao Signagelive para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Signagelive, é necessário selecionar qualquer função específica ao aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="setup-signagelive--for-provisioning"></a>Configurar o Signagelive para provisionamento

Antes de configurar o Signagelive para o provisionamento automático de usuário com o Azure AD, você precisará habilitar o provisionamento do SCIM no Signagelive.

  Entre em contato com o [Signagelive](mailto:development@signagelive.com) para obter o token secreto necessário para configurar o provisionamento do SCIM.

## <a name="add-signagelive-from-the-gallery"></a>Adicionar o Signagelive por meio da galeria

Antes de configurar o Signagelive para o provisionamento automático de usuário com o Azure AD, é necessário adicionar o Signagelive da galeria de aplicativos do Azure AD à lista de aplicativos SaaS gerenciados.

**Para adicionar o Signagelive da galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Signagelive**, selecione **Signagelive** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Signagelive na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-signagelive"></a>Configurar o provisionamento automático de usuário para o Signagelive    

Esta seção orienta você pelas etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Signagelive com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
>  Você também pode optar por habilitar o logon único baseado em SAML para o Signagelive, seguindo as instruções fornecidas no [tutorial de logon único do Signagelive](Signagelive-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-signagelive--in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Signagelive no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Signagelive**.

    ![O link do Signagelive na lista Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções Gerenciar com a opção Provisionamento destacada.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa Modo de Provisionamento com a opção Automático destacada.](common/provisioning-automatic.png)

5. Na seção Credenciais de Administrador, insira ` https://samlapi.signagelive.com/scim/v2` em **URL do Locatário**. No campo **Token Secreto**, insira o valor **Token de portador** fornecido pela equipe de desenvolvimento de engenharia. Clique em **Testar Conectividade** para verificar se o Azure AD pode se conectar ao Signagelive. Se a conexão falhar, verifique se a sua conta do Signagelive tem permissões de Administrador e tente novamente.
    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Save** (Salvar).

8. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Signagelive**.

    ![Captura de tela da seção Mapeamentos com a opção Sincronizar Usuários do Azure Active Directory com o Signagelive destacada.](media/signagelive-provisioning-tutorial/usermapping.png)

9. Examine os atributos de usuário sincronizados do Azure AD com o Signagelive na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das contas de usuário no Signagelive para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Captura de tela da seção Mapeamentos de Atributos com sete mapeamentos exibidos.](media/signagelive-provisioning-tutorial/userattribute.png)

10. Na seção **Mapeamentos**, selecione **Sincronizar Grupos do Azure Active Directory com o Signagelive**.

    ![Captura de tela da seção Mapeamentos com a opção Sincronizar Grupos do Azure Active Directory com o Signagelive destacada.](media/signagelive-provisioning-tutorial/groupmapping.png)

11. Examine os atributos de grupo sincronizados do Azure AD com o Signagelive na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das contas de grupo no Signagelive para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Captura de tela da seção Mapeamentos de Atributos com três mapeamentos exibidos.](media/signagelive-provisioning-tutorial/groupattribute.png)

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD no Signagelive, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você gostaria de provisionar para o Signagelive escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. A sincronização inicial demora mais para ser executada do que as posteriores. Para obter mais informações sobre o tempo necessário para o provisionamento de usuários e/ou grupos, confira [Quanto tempo levará para provisionar os usuários?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users). 

Use a seção **Status Atual** para monitorar o progresso e siga os links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Signagelive. Para obter mais informações, consulte [Verificar o status do provisionamento de usuário](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os logs de provisionamento do Azure AD, confira [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
