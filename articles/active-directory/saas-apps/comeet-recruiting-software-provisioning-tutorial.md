---
title: 'Tutorial: Configurar o Comeet Recruiting Software para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no Comeet Recruiting Software.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: 288d1e6cec8ddcf7d4afe5a35f28a022c2a7be10
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006705"
---
# <a name="tutorial-configure-comeet-recruiting-software-for-automatic-user-provisioning"></a>Tutorial: Configurar o Comeet Recruiting Software para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Comeet Recruiting Software e no Azure AD (Azure Active Directory) a fim de configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no Comeet Recruiting Software.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do Comeet Recruiting Software](https://www.comeet.co/)
* Uma conta de usuário no Comeet Recruiting Software com permissões de Administrador.

## <a name="add-comeet-recruiting-software-from-the-gallery"></a>Adicionar o Comeet Recruiting Software por meio da galeria

Antes de configurar o Comeet Recruiting Software para o provisionamento automático de usuário com o Azure AD, é necessário adicionar o Comeet Recruiting Software por meio da galeria de aplicativos do Azure AD à lista de aplicativos SaaS gerenciados.

**Para adicionar o Comeet Recruiting Software por meio da galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Comeet Recruiting Software**, selecione **Comeet Recruiting Software** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Comeet Recruiting Software na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-comeet-recruiting-software"></a>Como atribuir usuários ao Comeet Recruiting Software

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou os grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, decida quais usuários e/ou grupos no Azure AD precisam ter acesso ao Comeet Recruiting Software. Depois de decidir isso, você poderá atribuir esses usuários e/ou grupos ao Comeet Recruiting Software seguindo estas instruções:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-comeet-recruiting-software"></a>Dicas importantes para atribuir usuários ao Comeet Recruiting Software

* Recomendamos que somente um usuário do Azure AD seja atribuído ao Comeet Recruiting Software para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Comeet Recruiting Software, você precisará selecionar qualquer função válida específica do aplicativo (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-comeet-recruiting-software"></a>Como configurar o provisionamento automático de usuário para o Comeet Recruiting Software 

Esta seção descreve as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Comeet Recruiting Software com base em atribuições de usuário e/ou de grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o Comeet Recruiting Software seguindo as instruções fornecidas no [tutorial de logon único do Comeet Recruiting Software](comeetrecruitingsoftware-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-comeet-recruiting-software-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Comeet Recruiting Software no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Comeet recrutamento Software**.

    ![O link do Software de recrutamento Comeet na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções Gerenciar com a opção Provisionamento destacada.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa Modo de Provisionamento com a opção Automático destacada.](common/provisioning-automatic.png)

5. Na seção **Credenciais de Administrador**, insira a **URL do Locatário** e o **Token Secreto** da sua conta do Comeet Recruiting Software, conforme descrito na Etapa 6.

6. No [console de administração do Comeet Recruiting Software](https://app.comeet.co/), procure **Comeet > Configurações > Autenticação > Microsoft Azure** e copie o valor do **Token Secreto da sua empresa** para o campo **Token Secreto** do Azure AD.

    ![Provisionamento do Comeet Recruiting Software](./media/comeet-recruiting-software-provisioning-tutorial/secret-token-1.png)

7. Depois de preencher os campos mostrados na Etapa 5, clique em **Testar Conectividade** para verificar se o Azure AD pode se conectar ao Comeet Recruiting Software. Se a conexão falhar, verifique se a sua conta do Comeet Recruiting Software tem permissões de Administrador e tente novamente.

    ![Token](common/provisioning-testconnection-token.png)

8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

9. Clique em **Save** (Salvar).

10. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Comeet**.

    ![Mapeamentos de usuário do Comeet Recruiting Software](media/comeet-recruiting-software-provisioning-tutorial/user-mappings.png)

11. Examine os atributos de usuário que serão sincronizados do Azure AD para o Comeet Recruiting Software na seção **Mapeamento de Atributo**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das contas de usuário no Comeet Recruiting Software em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de grupo do Comeet Recruiting Software](media/comeet-recruiting-software-provisioning-tutorial/user-mapping-attributes.png)

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD no Comeet Recruiting Software, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou os grupos que você quer provisionar no Comeet Recruiting Software escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Comeet Recruiting Software.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* No momento, o Comeet Recruiting Software não dá suporte a grupos.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

