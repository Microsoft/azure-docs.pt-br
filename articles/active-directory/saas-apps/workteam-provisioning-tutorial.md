---
title: 'Tutorial: Configurar o Workteam para provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no Workteam.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/17/2019
ms.author: Zhchia
ms.openlocfilehash: f465438a6be80a1b487a4df7d3ee8bd38be9c102
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94354182"
---
# <a name="tutorial-configure-workteam--for-automatic-user-provisioning"></a>Tutorial: Configurar o Workteam para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Workteam e no Azure AD (Azure Active Directory) a fim de configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no Workteam.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD.
* [Um locatário do Workteam](https://workte.am/pricing.html)
* Uma conta de usuário no Workteam com permissões de Administrador.

## <a name="assigning-users-to-workteam"></a>Atribuir usuários ao Workteam 

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou os grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Para configurar e habilitar o provisionamento automático de usuário, decida quais usuários e/ou grupos no Azure AD precisam de acesso ao Workteam. Depois de decidir isso, você poderá atribuir esses usuários e/ou grupos ao Workteam seguindo estas instruções:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-workteam"></a>Dicas importantes para atribuir usuários ao Workteam 

* Recomendamos que um só usuário do Azure AD seja atribuído ao Workteam para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Workteam, selecione qualquer função válida específica do aplicativo (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="setup-workteam--for-provisioning"></a>Configurar o Workteam para provisionamento

Antes de configurar o Workteam para o provisionamento automático de usuário com o Azure AD, você precisará habilitar o provisionamento do SCIM no Workteam.

1. Faça logon no [Workteam](https://app.workte.am/account/signin). Clique em **Configurações da organização** > **CONFIGURAÇÕES**.

    ![Captura de tela da UI do Workteam com as opções Configurações da organização e CONFIGURAÇÕES destacadas.](media/workteam-provisioning-tutorial/settings.png)

2. Role até a parte inferior e habilite as funcionalidades de provisionamento do Workteam.

    ![Captura de tela da parte inferior da seção CONFIGURAÇÕES com o ícone de engrenagem de provisionamento de usuário S C I M destacado.](media/workteam-provisioning-tutorial/icon.png)

3. Copie a **URL base** e **Token de Portador**. Esses valores serão inseridos no campo **URL do Locatário** e **Token Secreto** na guia Provisionamento do aplicativo Workteam no portal do Azure.

    ![Captura de tela da caixa de diálogo Configurações do S C I M com as caixas de texto U R L BASE e TOKEN DE PORTADOR destacadas.](media/workteam-provisioning-tutorial/scim.png)


## <a name="add-workteam--from-the-gallery"></a>Adicionar o Workteam da galeria

Para configurar o Workteam para o provisionamento automático de usuário com o Azure AD, é necessário adicionar o Workteam por meio da galeria de aplicativos do Azure AD à lista de aplicativos SaaS gerenciados.

**Para adicionar o Workteam por meio da galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Workteam**, selecione **Workteam** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Workteam na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-workteam"></a>Configurar o provisionamento automático de usuário no Workteam  

Esta seção descreve as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários ou grupos no Workteam com base em atribuições de usuário ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o Workteam seguindo as instruções fornecidas no [tutorial de logon único do Workteam](workteam-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares

### <a name="to-configure-automatic-user-provisioning-for-workteam--in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Workteam no Azure AD, faça o seguinte:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Workteam**.

    ![O link Workteam na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções Gerenciar com a opção Provisionamento destacada.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa Modo de Provisionamento com a opção Automático destacada.](common/provisioning-automatic.png)

5. Na seção Credenciais de Administrador, insira os valores **URL Base** e **Token de Portador** recuperados anteriormente em **URL do Locatário** e **Token Secreto**, respectivamente. Clique em **Testar Conectividade** para verificar se o Azure AD pode se conectar ao Workteam. Se a conexão falhar, verifique se a sua conta do Workgrid tem permissões de Administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Save** (Salvar).

8. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Workteam**.

    ![Mapeamentos de usuário do Workteam](media/workteam-provisioning-tutorial/usermapping.png)

9. Examine os atributos de usuário que serão sincronizados do Azure AD com o Workteam na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das contas de usuário no Workteam em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de usuário do Workteam](media/workteam-provisioning-tutorial/userattribute.png)

11. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para habilitar o serviço de provisionamento do Azure AD para o Workteam, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

13. Defina os usuários e/ou os grupos que deseja provisionar no Workteam escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

14. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. A sincronização inicial demora mais para ser executada do que as posteriores. Para obter mais informações sobre o tempo necessário para o provisionamento de usuários e/ou grupos, confira [Quanto tempo levará para provisionar os usuários?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Use a seção **Status Atual** para monitorar o progresso e siga os links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Workteam. Para obter mais informações, consulte [Verificar o status do provisionamento de usuário](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os logs de provisionamento do Azure AD, confira [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
