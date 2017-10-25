---
title: "Tutorial: Configurando o LucidChart para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs"
description: "Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o LucidChart."
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
ms.openlocfilehash: 1f9344a5e750360e21ed7dc8e3ed013c2c2e1a45
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-configuring-lucidchart-for-automatic-user-provisioning"></a>Tutorial: Configurando o LucidChart para o provisionamento automático de usuário


O objetivo deste tutorial é mostrar as etapas que precisam ser realizadas no LucidChart e no Azure AD para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o LucidChart. 

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

*   Um locatário do Azure Active Directory
*   Um locatário do LucidChart com o [plano Enterprise](https://www.lucidchart.com/user/117598685#/subscriptionLevel) ou outro melhor habilitado 
*   Uma conta de usuário no LucidChart com permissões de Administrador 

## <a name="assigning-users-to-lucidchart"></a>Atribuindo usuários ao LucidChart

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e os grupos que foram "atribuídos" a um aplicativo no Azure AD serão sincronizados. 

Antes de configurar e habilitar o serviço de provisionamento, você precisa decidir quais usuários e/ou grupos no Azure AD representam os usuários que precisam de acesso ao aplicativo LucidChart. Depois de decidir, atribua esses usuários ao aplicativo LucidChart seguindo estas instruções:

[Atribuir um usuário ou um grupo a um aplicativo empresarial](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-lucidchart"></a>Dicas importantes para atribuir usuários ao LucidChart

*   Recomendamos a atribuição de um único usuário do Azure AD ao LucidChart para testar a configuração de provisionamento. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um usuário ao LucidChart, é necessário selecionar a função **Usuário** ou outra função válida específica ao aplicativo (se disponível) na caixa de diálogo de atribuição. A função **Acesso Padrão** não funciona para o provisionamento e esses usuários são ignorados.


## <a name="configuring-user-provisioning-to-lucidchart"></a>Configurando o provisionamento de usuário para o LucidChart 

Esta seção explica como conectar o Azure AD à API de provisionamento de conta de usuário do LucidChart e como configurar o serviço de provisionamento para criar, atualizar e desabilitar contas de usuário atribuídas no LucidChart, com base na atribuição de usuário e de grupo do Azure AD.

> [!TIP]
> Você também pode optar por habilitar o Logon Único baseado em SAML no LucidChart, seguindo as instruções fornecidas no [portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos sejam complementares.


### <a name="configure-automatic-user-account-provisioning-to-lucidchart-in-azure-ad"></a>Configurar o provisionamento automático de conta de usuário para o LucidChart no Azure AD


1. No [Portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory > Aplicativos Empresariais > Todos os aplicativos**.

2. Se você já tiver configurado o LucidChart para logon único, pesquise a instância do LucidChart usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e pesquise **LucidChart** na galeria de aplicativos. Selecione o LucidChart nos resultados da pesquisa e adicione-o à lista de aplicativos.

3. Selecione a instância do LucidChart e, depois, a guia **Provisionamento**.

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Provisionamento do LucidChart](./media/active-directory-saas-lucidchart-provisioning-tutorial/LucidChart1.png)

5. Na seção **Credenciais de Administrador**, insira o **Token Secreto** gerado pela conta do LucidChart (o token pode ser encontrado em sua conta: **Equipe** > **Integração de Aplicativos** > **SCIM**). 

    ![Provisionamento do LucidChart](./media/active-directory-saas-lucidchart-provisioning-tutorial/LucidChart2.png)

6. No portal do Azure, clique em **Testar conectividade** para garantir que o Azure AD pode se conectar ao aplicativo LucidChart. Se a conexão falhar, verifique se sua conta do LucidChart tem permissões de Administrador e repita a etapa 5.

7. Insira o endereço de email de uma pessoa ou um grupo que deve receber notificações de erro de provisionamento no campo **Email de Notificação** e marque a caixa de seleção “Enviar uma notificação por email quando ocorrer uma falha”.

8. Clique em **Salvar**. 

9. Na seção Mapeamentos, selecione **Sincronizar Usuários do Azure Active Directory com o LucidChart**.

10. Na seção **Mapeamentos de Atributo**, examine os atributos de usuário que são sincronizados do Azure AD para o LucidChart. Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário do LucidChart em operações de atualização. Selecione o botão Salvar para confirmar as alterações.

11. Para habilitar o serviço de provisionamento do Azure AD no LucidChart, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**

12. Clique em **Salvar**. 

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos atribuídos ao LucidChart na seção Usuários e Grupos. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 20 minutos, desde que o serviço esteja em execução. É possível usar a seção **Detalhes de Sincronização** para monitorar o andamento e seguir os links para os relatórios de atividade de provisionamento, que descrevem todas as ações executadas pelo serviço de provisionamento.

Para obter mais informações sobre como ler os logs de provisionamento do Azure AD, consulte [Relatando o provisionamento automático de conta de usuário](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-provisioning-reporting).


## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](active-directory-enterprise-apps-manage-provisioning.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](active-directory-saas-provisioning-reporting.md)
