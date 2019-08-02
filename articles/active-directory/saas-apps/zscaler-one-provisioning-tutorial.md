---
title: 'Tutorial: Configurar o Zscaler One para provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar contas de usuário ao Zscaler One automaticamente.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 72f6ba2b-73ed-420a-863a-aff672f26fa3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 3d6210fa215792fc7ff049eb597df5905926c193
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670885"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>Tutorial: Configurar o Zscaler One para provisionamento automático de usuário

Este tutorial demonstra as etapas para executar no Zscaler One e Azure Active Directory (Azure AD) para configurar o Azure AD automaticamente provisionar e desprovisionar usuários e grupos ao Zscaler One.

> [!NOTE]
> Este tutorial descreve um conector que se baseia no serviço de provisionamento de usuário do Azure AD. Para obter informações sobre esse serviço faz, como ele funciona e perguntas frequentes, consulte [automatizar o provisionamento e desprovisionamento de usuários para aplicativos de software-como um serviço (SaaS) com o Azure Active Directory](../active-directory-saas-app-provisioning.md).
>
> Esse conector está atualmente disponível como uma visualização. Para obter mais informações sobre os termos do Microsoft Azure gerais de uso para recursos de visualização, consulte [termos de uso complementares para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você tenha:

* Um locatário do Azure AD.
* Um locatário do Zscaler One.
* Uma conta de usuário no Zscaler One com permissões de administrador.

> [!NOTE]
> Integração de provisionamento do Azure AD se baseia em uma API de SCIM o Zscaler. Essa API está disponível para desenvolvedores do Zscaler One para contas com o pacote Enterprise.

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>Adicionar o Zscaler One no Azure Marketplace

Antes de configurar o Zscaler One para provisionamento com o Azure AD automático de usuário, adicione o Zscaler One no Azure Marketplace para sua lista de aplicativos SaaS gerenciados.

Para adicionar o Zscaler One do Marketplace, siga estas etapas.

1. No [portal do Azure](https://portal.azure.com), no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![Ícone do Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Zscaler One** e selecione **Zscaler One** no painel de resultados. Para adicionar o aplicativo, selecione **adicionar**.

    ![Zscaler One na lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>Atribuir usuários ao Zscaler One

Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, decida quais usuários ou grupos no Azure AD precisam de acesso ao Zscaler One. Para atribuir esses usuários ou grupos ao Zscaler One, siga as instruções em [atribuir um usuário ou grupo a um aplicativo empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>Dicas importantes para atribuir usuários ao Zscaler One

* É recomendável que você atribua um único usuário do Azure AD ao Zscaler One para testar a configuração de provisionamento automático de usuário. Você pode atribuir usuários ou grupos adicionais mais tarde.

* Quando você atribui um usuário ao Zscaler One, selecione qualquer função específica do aplicativo válida, se disponível, na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>Configurar o provisionamento automático de usuário ao Zscaler One

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do AD do Azure. Use-o para criar, atualizar e desabilitar usuários ou grupos no Zscaler One com base nas atribuições de usuário ou grupo no AD do Azure.

> [!TIP]
> Você também pode habilitar baseado em SAML single sign-on para Zscaler One. Siga as instruções de [Zscaler um único logon tutorial](zscaler-One-tutorial.md). O logon único o pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementam.

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Configurar o provisionamento automático de usuário para o Zscaler One no Azure AD

1. Entre no [Portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais** > **todos os aplicativos** > **Zscaler One**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Zscaler One**.

    ![O link do Zscaler One na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Provisionamento do Zscaler One](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Zscaler um modo de provisionamento](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. Sob o **credenciais de administrador** seção, preencha o **URL do locatário** e **Token secreto** caixas com as configurações para o Zscaler One conta conforme descrito na etapa 6.

6. Para obter o locatário de URL e o token de segredo, vá para **Administration** > **configurações de autenticação** no portal do Zscaler One da interface do usuário. Em **Tipo de Autenticação**, selecione **SAML**.

    ![Configurações de autenticação do Zscaler One](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. Selecione **configurar SAML** para abrir o **configurar SAML** opções.

    ![Configurar o Zscaler One SAML](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. Selecione **provisionamento Enable SCIM-Based** para obter as configurações na **URL de Base** e **Token de portador**. Em seguida, salve as configurações. Cópia de **URL Base** definir como **URL do locatário** no portal do Azure. Cópia de **Token de portador** definir como **Token secreto** no portal do Azure.

7. Depois de preencher as caixas mostradas na etapa 5, selecione **Conexão de teste** para certificar-se de que o Azure AD pode se conectar ao Zscaler One. Se a conexão falhar, certifique-se de que sua conta do Zscaler One tem permissões de administrador e tente novamente.

    ![Conexão de um teste do Zscaler](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. No **Email de notificação** caixa, digite o endereço de email da pessoa ou grupo para receber as notificações de erro de provisionamento. Selecione o **enviar uma notificação por email quando ocorre uma falha** caixa de seleção.

    ![Email de notificação do Zscaler One](./media/zscaler-one-provisioning-tutorial/notification.png)

9. Clique em **Salvar**.

10. Sob o **mapeamentos** seção, selecione **sincronizar Azure usuários do Active Directory ao Zscaler One**.

    ![Sincronização de usuário Zscaler One](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. Examine os atributos de usuário que são sincronizados do Azure AD ao Zscaler One na **mapeamentos de atributo** seção. Os atributos selecionados como **correspondentes** propriedades são usadas para corresponder as contas de usuário do Zscaler One para operações de atualização. Para salvar as alterações, selecione **salvar**.

    ![Atributos de usuário correspondentes Zscaler One](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. Sob o **mapeamentos** seção, selecione **sincronizar grupos do Azure Active Directory ao Zscaler One**.

    ![Zscaler One do grupo de sincronização](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. Examine os atributos de grupo que são sincronizados do Azure AD ao Zscaler One na **mapeamentos de atributo** seção. Os atributos selecionados como **correspondentes** propriedades são usadas para corresponder os grupos no Zscaler One para operações de atualização. Para salvar as alterações, selecione **salvar**.

    ![Atributos de grupo correspondentes Zscaler One](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. Para configurar filtros de escopo, siga as instruções de [tutorial do filtro de escopo](./../active-directory-saas-scoping-filters.md).

15. Para habilitar o Zscaler One, serviço de provisionamento no Azure AD a **as configurações** seção, altere **Status de provisionamento** para **em**.

    ![Zscaler um Status de provisionamento](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Defina os usuários ou grupos que você deseja a serem provisionados no Zscaler One. No **as configurações** seção, selecione os valores desejados na **escopo**.

    ![Escopo do Zscaler One](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. Quando estiver pronto para provisionar, selecione **salvar**.

    ![Zscaler um salvar](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

Essa operação inicia a sincronização inicial de todos os usuários ou grupos definidos no **escopo** na **configurações** seção. A sincronização inicial levará mais tempo para que as sincronizações posteriores. Eles ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do AD do Azure é executado. 

Você pode usar o **detalhes de sincronização** seção para monitorar o progresso e siga os links para o relatório de atividade de provisionamento. O relatório descreve todas as ações executadas pelo serviço no Zscaler One de provisionamento do Azure AD.

Para obter informações sobre como ler os logs de provisionamento do Azure AD, confira [Relatórios sobre o provisionamento automático de contas de usuário](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png
