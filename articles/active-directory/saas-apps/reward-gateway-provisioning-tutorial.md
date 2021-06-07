---
title: 'Tutorial: Configurar o Reward Gateway para provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Reward Gateway.
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
ms.openlocfilehash: 2d51903aff6f3fd1cd53d85a980f1b5dc2a893e9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94354245"
---
# <a name="tutorial-configure-reward-gateway-for-automatic-user-provisioning"></a>Tutorial: Configurar o Reward Gateway para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Reward Gateway e no Azure AD (Azure Active Directory) a fim de configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no Reward Gateway.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD.
* Um [locatário do Reward Gateway](https://www.rewardgateway.com/).
* Uma conta de usuário no Reward Gateway com permissões de Administrador.

## <a name="assigning-users-to-reward-gateway"></a>Atribuir usuários ao Reward Gateway 

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou os grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Para configurar e habilitar o provisionamento automático de usuário, decida quais usuários e/ou grupos no Azure AD precisam de acesso ao Reward Gateway. Depois de decidir isso, você poderá atribuir esses usuários e/ou grupos ao Reward Gateway seguindo as instruções em [Atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md).


## <a name="important-tips-for-assigning-users-to-reward-gateway"></a>Dicas importantes para atribuir usuários ao Reward Gateway 

* Recomendamos que um só usuário do Azure AD seja atribuído ao Reward Gateway para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Reward Gateway, selecione qualquer função válida específica do aplicativo (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="setup-reward-gateway--for-provisioning"></a>Configurar o Reward Gateway para provisionamento
Antes de configurar o Reward Gateway para o provisionamento automático de usuário com o Azure AD, você precisará habilitar o provisionamento do SCIM no Reward Gateway.

1. Entre no [Console de Administração do Reward Gateway](https://rewardgateway.photoshelter.com/login/). Clique em **Integrações**.

    ![Captura de tela do Console de Administração do Reward Gateway com a opção Integrações destacada.](media/reward-gateway-provisioning-tutorial/image00.png)

2.  Selecione **Minha Integração**.

    ![Captura de tela das duas opções Integrações com a opção Minhas Integrações destacadas.](media/reward-gateway-provisioning-tutorial/image001.png)

3.  Copie os valores de **URL do SCIM (v2)** e **Token de Portador OAuth**. Esses valores serão inseridos nos campos URL do Locatário e Token Secreto na guia Provisionamento do aplicativo Reward Gateway no portal do Azure.

    ![Captura de tela do painel Minhas Integrações com a caixa de texto Token de Portador OAuth destacada.](media/reward-gateway-provisioning-tutorial/image03.png)

## <a name="add-reward-gateway-from-the-gallery"></a>Adicionar o Reward Gateway da Galeria

Para configurar o Reward Gateway para o provisionamento automático de usuário com o Azure AD, é necessário adicionar o Reward Gateway por meio da galeria de aplicativos do Azure AD à lista de aplicativos SaaS gerenciados.

**Para adicionar o Reward Gateway por meio da galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Reward Gateway**, selecione **Reward Gateway** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Reward Gateway na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-reward-gateway"></a>Configurar o provisionamento automático de usuário no Reward Gateway  

Esta seção descreve as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários ou grupos no Reward Gateway com base em atribuições de usuário ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o Reward Gateway seguindo as instruções fornecidas no [tutorial de logon único do Reward Gateway](reward-gateway-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-reward-gateway-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Reward Gateway no Azure AD, faça o seguinte:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Reward Gateway**.

    ![O link do Reward Gateway na lista Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções Gerenciar com a opção Provisionamento destacada.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa Modo de Provisionamento com a opção Automático destacada.](common/provisioning-automatic.png)

5. Na seção **Credenciais de Administrador**, insira os valores **URL do SCIM (v2)** e **Token de Portador OAuth** recuperados anteriormente em **URL do Locatário** e **Token Secreto**, respectivamente. Clique em **Testar Conexão** para verificar se o Azure AD pode se conectar ao Reward Gateway. Se a conexão falhar, verifique se a sua conta do Reward Gateway tem permissões de Administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Save** (Salvar).

8. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Reward Gateway**.

    ![Captura de tela da seção Mapeamentos com a opção Sincronizar Usuários do Azure Active Directory com o Reward Gateway destacada.](media/reward-gateway-provisioning-tutorial/user-mappings.png)

9. Examine os atributos de usuário que serão sincronizados do Azure AD com o Reward Gateway na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das contas de usuário no Reward Gateway em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Captura de tela da seção Mapeamentos de Atributos com sete mapeamentos exibidos.](media/reward-gateway-provisioning-tutorial/user-attributes.png)

10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento do Azure AD para o Reward Gateway, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou os grupos que deseja provisionar no Reward Gateway escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Reward Gateway.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

O Reward Gateway não dá suporte ao provisionamento de grupo no momento.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

[Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)