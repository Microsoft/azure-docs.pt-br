---
title: 'Tutorial: Configurar o Meta Networks Connector para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no Meta Networks Connector.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: b6a8f192cd26639431cc9fcb6b43e1bc5e8e2843
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96353622"
---
# <a name="tutorial-configure-meta-networks-connector-for-automatic-user-provisioning"></a>Tutorial: Configurar o Meta Networks Connector para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Meta Networks Connector e no Azure AD (Active Directory) a fim de configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no Meta Networks Connector.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do Meta Networks Connector](https://www.metanetworks.com/)
* Uma conta de usuário no Meta Networks Connector com permissões de Administrador.

## <a name="assigning-users-to-meta-networks-connector"></a>Como atribuir usuários ao Meta Networks Connector

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou os grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Para configurar e habilitar o provisionamento automático de usuário, decida quais usuários e/ou grupos no Azure AD precisam de acesso ao Meta Networks Connector. Depois de decidir isso, você poderá atribuir esses usuários e/ou grupos ao Meta Networks Connector seguindo estas instruções:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-meta-networks-connector"></a>Dicas importantes para atribuir usuários ao Meta Networks Connector

* Recomendamos que um só usuário do Azure AD seja atribuído ao Meta Networks Connector para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Meta Networks Connector, você precisará selecionar qualquer função válida específica do aplicativo (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="setup-meta-networks-connector-for-provisioning"></a>Configurar o Meta Networks Connector para provisionamento

1. Entre no [Console de Administração do Meta Networks Connector](https://login.metanetworks.com/login/) usando o nome da sua organização. Procure **Administração > Chaves de API**.

    ![Console de Administração do Meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/apikey.png)

2.  Clique no sinal de adição no lado superior direito da tela para criar uma **Chave de API**.

    ![Ícone de adição no Meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/plusicon.png)

3.  Defina o **Nome da Chave de API** e a **Descrição da Chave de API**.

    :::image type="content" source="media/meta-networks-connector-provisioning-tutorial/keyname.png" alt-text="Captura de tela do Console de Administração do Meta Networks Connector com os valores de nome e descrição de chave de API do Azure AD realçados e uma chave de API." border="false":::

4.  Ative os privilégios de **Gravação** para **Grupos** e **Usuários**.

    ![Privilégios no Meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/privileges.png)

5.  Clique em **Adicionar**. Copie o **SEGREDO** e salve-o, porque essa será a única vez que poderá vê-lo. Esse valor será inserido no campo Token Secreto na guia Provisionamento do aplicativo Meta Networks Connector no portal do Azure.

    :::image type="content" source="media/meta-networks-connector-provisioning-tutorial/token.png" alt-text="Captura de tela de uma janela informando aos usuários que a chave de API foi adicionada. A caixa Segredo contém um valor indecifrável e está realçada." border="false":::

6.  Adicione um IdP procurando **Administração > Configurações > IdP > Criar**.

    ![Adicionar IdP no Meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/newidp.png)

7.  Na página **Configuração de IdP**, você pode dar um **Nome** à configuração de IdP e escolher um **Ícone**.

    ![Nome do IdP no Meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/idpname.png)

    ![Ícone do IdP no Meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/icon.png)

8.  Em **Configurar o SCIM**, selecione o nome da chave de API criada nas etapas anteriores. Clique em **Save**.

    ![Configurar SCIM no Meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/configure.png)

9.  Procure **Administração > Configurações > guia IdP**. Clique no nome da configuração do IdP criada nas etapas anteriores para ver a **ID do IdP**. Essa **ID** é adicionada ao final da **URL do Locatário** ao inserir o valor no campo **URL do Locatário** na guia Provisionamento do aplicativo Meta Networks Connector no portal do Azure.

    ![ID do IdP no Meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/idpid.png)

## <a name="add-meta-networks-connector-from-the-gallery"></a>Adicionar o Meta Networks Connector por meio da galeria

Antes de configurar o Meta Networks Connector para o provisionamento automático de usuário com o Azure AD, é necessário adicionar o Meta Networks Connector por meio da galeria de aplicativos do Azure AD à lista de aplicativos SaaS gerenciados.

**Para adicionar o Meta Networks Connector por meio da galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Meta Networks Connector**, selecione **Meta Networks Connector** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Meta redes conector na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-meta-networks-connector"></a>Como configurar o provisionamento automático de usuário no Meta Networks Connector 

Esta seção descreve as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Meta Networks Connector com base em atribuições de usuário e/ou de grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o Meta Networks Connector seguindo as instruções fornecidas no [tutorial de logon único do Meta Networks Connector](./metanetworksconnector-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares

### <a name="to-configure-automatic-user-provisioning-for-meta-networks-connector-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Meta Networks Connector no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Meta redes conector**.

    ![O link de Meta redes conector na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções Gerenciar com a opção Provisionamento destacada.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa Modo de Provisionamento com a opção Automático destacada.](common/provisioning-automatic.png)

5. Na seção **Credenciais de Administrador**, insira `https://api.metanetworks.com/v1/scim/<IdP ID>` em **URL do Locatário**. Insira o valor do **Token de Autenticação do SCIM** recuperado anteriormente em **Token Secreto**. Clique em **Testar Conectividade** para verificar se o Azure AD pode se conectar ao Meta Networks Connector. Se a conexão falhar, verifique se a sua conta do Meta Networks Connector tem permissões de Administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Save** (Salvar).

8. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Meta Networks Connector**.

    ![Mapeamentos de Usuário no Meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/usermappings.png)

9. Examine os atributos de usuário que serão sincronizados do Azure AD para o Meta Networks Connector na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das contas de usuário no Meta Networks Connector em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de Usuário no Meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/userattributes.png)

10. Na seção **Mapeamentos**, selecione **Sincronizar Grupos do Azure Active Directory com o Meta Networks Connector**.

    ![Mapeamentos do Grupo no Meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/groupmappings.png)

11. Examine os atributos de grupo que serão sincronizados do Azure AD para o Meta Networks Connector na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência dos grupos no Meta Networks Connector em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de Grupo no Meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/groupattributes.png)

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD no Meta Networks Connector, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou os grupos que deseja provisionar no Meta Networks Connector escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para o relatório das atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Meta Networks Connector.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)