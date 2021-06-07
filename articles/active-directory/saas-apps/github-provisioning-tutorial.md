---
title: 'Tutorial: Provisionamento de usuário para o GitHub – Azure AD'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o GitHub.
services: active-directory
author: Zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: Zhchia
ms.openlocfilehash: 9d9699c564476e116654f700c32dd47b7f6d5b81
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504559"
---
# <a name="tutorial-configure-github-for-automatic-user-provisioning"></a>Tutorial: Configurar o GitHub para provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas que precisam ser realizadas no GitHub e no Azure AD para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o GitHub.

> [!NOTE]
> A integração de provisionamento do Azure AD depende da [API do SCIM no GitHub](https://developer.github.com/v3/scim/), que está disponível para os clientes do [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise) no [plano de cobrança GitHub Enterprise](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Um locatário do Azure Active Directory
* Uma organização do GitHub criada na [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise), que requer o [plano de cobrança do GitHub Enterprise](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)
* Uma conta de usuário no GitHub com permissões de Administrador na organização
* [SAML configurado para a organização do GitHub Enterprise Cloud](./github-tutorial.md)
* Verifique se o acesso ao OAuth foi fornecido para sua organização, conforme descrito [aqui](https://help.github.com/en/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization)
* Só há suporte ao provisionamento do SCIM para uma organização individual quando o SSO está habilitado no nível da organização

> [!NOTE]
> Essa integração também está disponível para uso do ambiente de Nuvem do Governo dos EUA do Azure AD. Encontre esse aplicativo na Galeria de Aplicativos de Nuvem do Governo dos EUA do Azure AD e configure-o da mesma forma que na nuvem pública.

## <a name="assigning-users-to-github"></a>Atribuindo usuários ao GitHub

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e os grupos que foram "atribuídos" a um aplicativo no Azure AD serão sincronizados. 

Antes de configurar e habilitar o serviço de provisionamento, você precisa decidir quais usuários e/ou grupos no Azure AD representam os usuários que precisam de acesso ao aplicativo GitHub. Depois de decidir, atribua esses usuários ao aplicativo GitHub seguindo estas instruções:

[Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-github"></a>Dicas importantes para atribuir usuários ao GitHub

* Recomendamos a atribuição de um único usuário do Azure AD ao GitHub para testar a configuração de provisionamento. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao GitHub, é necessário selecionar a função **Usuário** ou outra função válida específica ao aplicativo (se disponível) na caixa de diálogo de atribuição. A função **Acesso Padrão** não funciona para o provisionamento e esses usuários são ignorados.

## <a name="configuring-user-provisioning-to-github"></a>Configurando o provisionamento de usuário para o GitHub

Esta seção explica como conectar o Azure AD à API de provisionamento de conta de usuário do GitHub e como configurar o serviço de provisionamento para criar, atualizar e desabilitar contas de usuário atribuídas no GitHub, com base na atribuição de usuário e de grupo do Azure AD.

### <a name="configure-automatic-user-account-provisioning-to-github-in-azure-ad"></a>Configurar o provisionamento automático de conta de usuário para o GitHub no Azure AD

1. No [Portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory > Aplicativos Empresariais > Todos os aplicativos**.

2. Se você já tiver configurado o GitHub para logon único, pesquise a instância do GitHub usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e pesquise **GitHub** na galeria de aplicativos. Selecione o GitHub nos resultados da pesquisa e adicione-o à lista de aplicativos.

3. Selecione a instância do GitHub e, depois, a guia **Provisionamento**.

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Provisionamento do GitHub](./media/github-provisioning-tutorial/GitHub1.png)

5. Na seção **Credenciais de Administrador**, clique em **Autorizar**. Essa operação abre uma caixa de diálogo de autorização do GitHub em uma nova janela do navegador. Observe que você precisará verificar se está aprovado para autorizar o acesso. Siga as instruções descritas [aqui](https://help.github.com/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization).

6. Na nova janela, entre no GitHub usando sua conta do Administrador. Na caixa de diálogo de autorização resultante, selecione a equipe do GitHub para a qual você deseja habilitar o provisionamento e, em seguida, selecione **Autorizar**. Depois de concluído, retorne ao Portal do Azure para concluir a configuração de provisionamento.

    ![Captura de tela mostrando a página de entrada do GitHub.](./media/github-provisioning-tutorial/GitHub2.png)

7. No portal do Azure, insira a **URL de Locatário** e clique em **Testar Conectividade** para garantir que o Azure AD pode se conectar ao aplicativo GitHub. Se a conexão falhar, verifique se sua conta do GitHub tem permissões de Administrador e se a **URI de Locatário** foi inserida corretamente. Em seguida, repita a etapa "Autorizar" (a **URL de Locatário** pode ser constituída pela regra: `https://api.github.com/scim/v2/organizations/<Organization_name>`. Encontre as organizações em sua conta do GitHub: **Configurações** > **Organizações**).

    ![Captura de tela mostrando a página Organizações no GitHub.](./media/github-provisioning-tutorial/GitHub3.png)

8. Insira o endereço de email de uma pessoa ou um grupo que deve receber notificações de erro de provisionamento no campo **Email de Notificação** e marque a caixa de seleção “Enviar uma notificação por email quando ocorrer uma falha”.

9. Clique em **Salvar**.

10. Na seção Mapeamentos, selecione **Sincronizar Usuários do Azure Active Directory com o GitHub**.

11. Na seção **Mapeamentos de Atributo**, examine os atributos de usuário que são sincronizados do Azure AD para o GitHub. Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário do GitHub em operações de atualização. Selecione o botão Salvar para confirmar as alterações.

12. Para habilitar o serviço de provisionamento do Azure AD no GitHub, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**

13. Clique em **Salvar**.

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos atribuídos ao GitHub na seção Usuários e Grupos. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em execução. É possível usar a seção **Detalhes de Sincronização** para monitorar o andamento e seguir os links para os logs de atividade de provisionamento, que descrevem todas as ações executadas pelo serviço de provisionamento.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)