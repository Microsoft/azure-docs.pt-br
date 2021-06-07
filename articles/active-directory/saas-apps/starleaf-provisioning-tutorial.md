---
title: 'Tutorial: Configurar o StarLeaf para provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o StarLeaf.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/19/2019
ms.author: zhchia
ms.openlocfilehash: 07c476c0de644ac63c577d466f4691b5cf415334
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94357922"
---
# <a name="tutorial-configure-starleaf-for-automatic-user-provisioning"></a>Tutorial: Configurar o StarLeaf para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no StarLeaf e no Azure AD (Azure Active Directory) a fim de configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no StarLeaf.

> [!NOTE]
>  Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD.
* [Um locatário do StarLeaf](https://starleaf.com/).
* Uma conta de usuário no StarLeaf com permissões de Administrador.

## <a name="assign-users-to-starleaf"></a>Atribuir usuários ao StarLeaf
O Azure Active Directory usa um conceito chamado atribuições para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou os grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Para configurar e habilitar o provisionamento automático de usuário, decida quais usuários e/ou grupos no Azure AD precisam de acesso ao StarLeaf. Em seguida, você pode atribuir os usuários e grupos ao StarLeaf seguindo [estas instruções](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-starleaf"></a>Dicas importantes para atribuir usuários ao StarLeaf

* Recomendamos que somente um usuário do Azure AD seja atribuído ao StarLeaf para testar a configuração de provisionamento automático de usuário. Usuários e grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao StarLeaf, selecione qualquer função válida específica do aplicativo (se disponível) na caixa de diálogo de atribuição. Usuários com a função Acesso padrão são excluídos do provisionamento.

## <a name="set-up-starleaf-for-provisioning"></a>Configurar o StarLeaf para provisionamento

Antes de configurar o StarLeaf para o provisionamento automático de usuário com o Azure AD, você precisará configurar o provisionamento do SCIM no StarLeaf:

1. Entre no [Console de Administração do StarLeaf](https://portal.starleaf.com/#page=login). Navegue até **Integrações** > **Adicionar integração**.

    ![Captura de tela do Console de Administração do StarLeaf com as opções Integração e Adicionar integração destacadas.](media/starleaf-provisioning-tutorial/image00.png)

2. Selecione o **Tipo** como Microsoft Azure Active Directory. Insira um nome adequado em **Nome**. Clique em **Aplicar**.

    ![Captura de tela da caixa de diálogo Adicionar integração com as caixas de texto Tipo e Nome destacadas.](media/starleaf-provisioning-tutorial/image01.png)

3.  Os valores **URL base do SCIM** e **Token de acesso** serão exibidos. Esses valores serão inseridos nos campos **URL do Locatário** e **Token Secreto** na guia Provisionamento do seu aplicativo StarLeaf no portal do Azure. 

    ![Captura de tela da caixa de diálogo Editar integração com as caixas de texto Tipo, Nome e URL base do SCIM destacadas.](media/starleaf-provisioning-tutorial/image02.png)

## <a name="add-starleaf-from-the-gallery"></a>Adicionar o StarLeaf da galeria

Para configurar o StarLeaf para o provisionamento automático de usuário com o Azure AD, é necessário adicionar o StarLeaf por meio da galeria de aplicativos do Azure AD à lista de aplicativos SaaS gerenciados.

**Para adicionar o StarLeaf por meio da galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **StarLeaf**, selecione **StarLeaf** no painel de resultados.
    ![StarLeaf na lista de resultados](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-starleaf"></a>Configurar o provisionamento automático de usuário para o StarLeaf

Esta seção descreve as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários ou grupos no StarLeaf com base em atribuições de usuário ou grupo no Azure AD.

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **StarLeaf**.

    ![O link do StarLeaf na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções Gerenciar com a opção Provisionamento destacada.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa Modo de Provisionamento com a opção Automático destacada.](common/provisioning-automatic.png)

5. Na seção Credenciais de Administrador, insira os valores **URL Base do SCIM** e **Token de Acesso** recuperados anteriormente em **URL do Locatário** e **Token Secreto**, respectivamente. Clique em **Testar Conexão** para verificar se o Azure AD pode se conectar ao StarLeaf. Se a conexão falhar, verifique se a sua conta do StarLeaf tem permissões de Administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Save** (Salvar).

8. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o StarLeaf**.

    ![Captura de tela da seção Mapeamentos mostrando a opção Sincronizar Usuários do Azure Active Directory com o StarLeaf.](media/starleaf-provisioning-tutorial/usermapping.png)

9. Examine os atributos de usuário que serão sincronizados do Azure AD com o StarLeaf na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das contas de usuário no StarLeaf em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Captura de tela da seção Mapeamentos de Atributos mostrando nove mapeamentos.](media/starleaf-provisioning-tutorial/userattribute.png)


10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).


11. Para habilitar o serviço de provisionamento do Azure AD para o StarLeaf, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou os grupos que você quer provisionar no StarLeaf escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no StarLeaf.

Para obter mais informações sobre como ler os logs de provisionamento do Azure AD, confira [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Limitações do conector

* No momento, o StarLeaf não dá suporte ao provisionamento de grupo. 
* O StarLeaf requer que os valores **emails** e **userName** tenham o mesmo valor de origem.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar provisionamento de conta de usuário para aplicativos empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* Saiba como [fazer a revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md).
