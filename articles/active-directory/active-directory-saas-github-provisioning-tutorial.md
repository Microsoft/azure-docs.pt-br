---
title: "Tutorial: Configurando o GitHub para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs"
description: "Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o GitHub."
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: stevenpo
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: asmalser-msft
ms.openlocfilehash: 3cc70273e95dbf4913e7bbcd8a37bd9a52987b60
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-configuring-github-for-automatic-user-provisioning"></a>Tutorial: Configurando o GitHub para o provisionamento automático de usuário


O objetivo deste tutorial é mostrar as etapas que precisam ser realizadas no GitHub e no Azure AD para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o GitHub. 

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

*   Um locatário do Azure Active Directory
*   Um locatário do GitHub com o [plano Business](https://help.github.com/articles/organization-billing-plans/#business-plan) ou outro melhor habilitado 
*   Uma conta de usuário no GitHub com permissões de Administrador 

> [!NOTE]
> A integração de provisionamento do Azure AD depende da [API de SCIM do GitHub](https://developer.github.com/v3/scim/), disponível para as equipes do GitHub no plano Business ou outro melhor.

## <a name="assigning-users-to-github"></a>Atribuindo usuários ao GitHub

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e os grupos que foram "atribuídos" a um aplicativo no Azure AD serão sincronizados. 

Antes de configurar e habilitar o serviço de provisionamento, você precisa decidir quais usuários e/ou grupos no Azure AD representam os usuários que precisam de acesso ao aplicativo GitHub. Depois de decidir, atribua esses usuários ao aplicativo GitHub seguindo estas instruções:

[Atribuir um usuário ou um grupo a um aplicativo empresarial](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-github"></a>Dicas importantes para atribuir usuários ao GitHub

*   Recomendamos a atribuição de um único usuário do Azure AD ao GitHub para testar a configuração de provisionamento. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um usuário ao GitHub, é necessário selecionar a função **Usuário** ou outra função válida específica ao aplicativo (se disponível) na caixa de diálogo de atribuição. A função **Acesso Padrão** não funciona para o provisionamento e esses usuários são ignorados.


## <a name="configuring-user-provisioning-to-github"></a>Configurando o provisionamento de usuário para o GitHub 

Esta seção explica como conectar o Azure AD à API de provisionamento de conta de usuário do GitHub e como configurar o serviço de provisionamento para criar, atualizar e desabilitar contas de usuário atribuídas no GitHub, com base na atribuição de usuário e de grupo do Azure AD.

> [!TIP]
> Você também pode optar por habilitar o Logon Único baseado em SAML no GitHub, seguindo as instruções fornecidas no [portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos sejam complementares.


### <a name="configure-automatic-user-account-provisioning-to-github-in-azure-ad"></a>Configurar o provisionamento automático de conta de usuário para o GitHub no Azure AD


1. No [Portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory > Aplicativos Empresariais > Todos os aplicativos**.

2. Se você já tiver configurado o GitHub para logon único, pesquise a instância do GitHub usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e pesquise **GitHub** na galeria de aplicativos. Selecione o GitHub nos resultados da pesquisa e adicione-o à lista de aplicativos.

3. Selecione a instância do GitHub e, depois, a guia **Provisionamento**.

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Provisionamento do GitHub](./media/active-directory-saas-github-provisioning-tutorial/GitHub1.png)

5. Na seção **Credenciais de Administrador**, clique em **Autorizar**. Essa operação abre uma caixa de diálogo de autorização do GitHub em uma nova janela do navegador. 

6. Na nova janela, entre no GitHub usando sua conta do Administrador. Na caixa de diálogo de autorização resultante, selecione a equipe do GitHub para a qual você deseja habilitar o provisionamento e, em seguida, selecione **Autorizar**. Depois de concluído, retorne ao Portal do Azure para concluir a configuração de provisionamento.

    ![Caixa de diálogo Autorização](./media/active-directory-saas-github-provisioning-tutorial/GitHub2.png)

7. No portal do Azure, insira a **URL de Locatário** e clique em **Testar Conectividade** para garantir que o Azure AD pode se conectar ao aplicativo GitHub. Se a conexão falhar, verifique se sua conta do GitHub tem permissões de Administrador e se a **URI de Locatário** foi inserida corretamente. Em seguida, repita a etapa “Autorizar” (a **URL de Locatário** pode ser constituída pela regra: “https://api.github.com/scim/v2/organizations/ + <Organizations_name>”. Encontre as organizações em sua conta do GitHub: **Configurações** > **Organizações**).

    ![Caixa de diálogo Autorização](./media/active-directory-saas-github-provisioning-tutorial/GitHub3.png)

8. Insira o endereço de email de uma pessoa ou um grupo que deve receber notificações de erro de provisionamento no campo **Email de Notificação** e marque a caixa de seleção “Enviar uma notificação por email quando ocorrer uma falha”.

9. Clique em **Salvar**. 

10. Na seção Mapeamentos, selecione **Sincronizar Usuários do Azure Active Directory com o GitHub**.

11. Na seção **Mapeamentos de Atributo**, examine os atributos de usuário que são sincronizados do Azure AD para o GitHub. Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário do GitHub em operações de atualização. Selecione o botão Salvar para confirmar as alterações.

12. Para habilitar o serviço de provisionamento do Azure AD no GitHub, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**

13. Clique em **Salvar**. 

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos atribuídos ao GitHub na seção Usuários e Grupos. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 20 minutos, desde que o serviço esteja em execução. É possível usar a seção **Detalhes de Sincronização** para monitorar o andamento e seguir os links para os relatórios de atividade de provisionamento, que descrevem todas as ações executadas pelo serviço de provisionamento.

Para obter mais informações sobre como ler os logs de provisionamento do Azure AD, consulte [Relatando o provisionamento automático de conta de usuário](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-provisioning-reporting).


## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](active-directory-enterprise-apps-manage-provisioning.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](active-directory-saas-provisioning-reporting.md)
