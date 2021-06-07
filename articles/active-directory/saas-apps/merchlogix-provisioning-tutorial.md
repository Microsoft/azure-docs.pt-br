---
title: 'Tutorial: Configurar o MerchLogix para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no MerchLogix.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: zhchia
ms.openlocfilehash: 4d0a52f06a751fba57a00615e2d57485ff740d04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94359690"
---
# <a name="tutorial-configure-merchlogix-for-automatic-user-provisioning"></a>Tutorial: Configurar o MerchLogix para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no MerchLogix e no Azure AD (Active Directory) a fim de configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no MerchLogix.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* Um locatário do MerchLogix
* Um contato técnico do MerchLogix que possa fornecer a URL do ponto de extremidade do SCIM e o token secreto necessários para o provisionamento de usuário

## <a name="adding-merchlogix-from-the-gallery"></a>Adicionar o MerchLogix por meio da galeria

Antes de configurar o MerchLogix para o provisionamento automático de usuário com o Azure AD, é necessário adicionar o MerchLogix por meio da galeria de aplicativos do Azure AD à lista de aplicativos SaaS gerenciados.

**Para adicionar o MerchLogix por meio da galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **Aplicativos empresariais** > **Todos os aplicativos**.

    ![Seção de Aplicativos empresariais][2]

3. Para adicionar o MerchLogix, clique no botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **MerchLogix**.

5. No painel de resultados, selecione **MerchLogix** e clique no botão **Adicionar** para adicionar o MerchLogix à lista de aplicativos SaaS.

    ![Captura de tela da seção Adicionar por meio da galeria com a caixa de texto Insira um nome em destaque.][4]

## <a name="assigning-users-to-merchlogix"></a>Como atribuir usuários ao MerchLogix

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Microsoft Azure AD são sincronizados. 

Para configurar e habilitar o provisionamento automático de usuário, decida quais usuários e/ou grupos no Azure AD precisam de acesso ao MerchLogix. Depois de decidir isso, você poderá atribuir esses usuários e/ou grupos ao MerchLogix seguindo estas instruções:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-merchlogix"></a>Dicas importantes para atribuir usuários ao MerchLogix

* Recomendamos que um só usuário do Azure AD seja atribuído ao MerchLogix para testar a configuração inicial de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos posteriormente, após os testes serem bem-sucedidos.

* Ao atribuir um usuário ao MerchLogix, você precisará selecionar qualquer função válida específica do aplicativo (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-merchlogix"></a>Como configurar o provisionamento automático de usuário no MerchLogix 

Esta seção descreve as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no MerchLogix com base em atribuições de usuário e/ou de grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o MerchLogix seguindo as instruções fornecidas no [tutorial de logon único do MerchLogix](merchlogix-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-merchlogix-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o MerchLogix no Azure AD:

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até **Azure Active Directory > Aplicativos empresariais > Todos os aplicativos**.

2. Selecione MerchLogix na lista de aplicativos SaaS.

3. Selecione a guia **Provisionamento**.

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da seção MerchLogix – Provisionamento com a opção Provisionamento em destaque, o Modo de Provisionamento definido como Automático e a opção Testar Conectividade em destaque.](./media/merchlogix-provisioning-tutorial/Merchlogix1.png)

5. Na seção **Credenciais de Administrador**:

    * No campo **URL do Locatário**, insira a URL do ponto de extremidade do SCIM fornecida pelo contato técnico do MerchLogix.

    * No campo **Token Secreto**, insira o token secreto fornecido pelo contato técnico do MerchLogix.

6. Ao preencher os campos mostrados na Etapa 5, clique em **Testar Conectividade** para verificar se o Azure AD pode se conectar ao MerchLogix. Se a conexão falhar, verifique se a sua conta do MerchLogix tem permissões de Administrador e tente novamente.

7. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

8. Clique em **Salvar**.

9. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o MerchLogix**.

10. Examine os atributos de usuário que serão sincronizados do Azure AD para o MerchLogix na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das contas de usuário no MerchLogix em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

11. Na seção **Mapeamentos**, selecione **Sincronizar Grupos do Azure Active Directory com o MerchLogix**.

12. Examine os atributos de grupo que serão sincronizados do Azure AD para o MerchLogix na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência dos grupos no MerchLogix em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

13. Para habilitar o serviço de provisionamento do Azure AD no MerchLogix, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

14. Quando estiver pronto para provisionar, clique em **Salvar**.

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para o relatório das atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no MerchLogix.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: common/select-azuread.png
[2]: common/enterprise-applications.png
[3]: common/add-new-app.png
[4]: common/search-new-app.png
