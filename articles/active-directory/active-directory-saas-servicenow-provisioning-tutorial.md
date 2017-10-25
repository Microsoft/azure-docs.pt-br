---
title: "Tutorial: Configurar o ServiceNow para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs"
description: "Saiba como provisionar e desprovisionar automaticamente contas de usuário do Azure AD para o ServiceNow."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: e338804a0c5fcb09dfdfd9bf18ae4d0466cd9a82
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-configuring-servicenow-for-automatic-user-provisioning-with-azure-active-directory"></a>Tutorial: Configurar o ServiceNow para o provisionamento automático de usuário com o Azure Active Directory

O objetivo deste tutorial é mostrar as etapas que precisam ser realizadas no ServiceNow e no Azure AD para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o ServiceNow.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

*   Um locatário do Azure Active Directory.
*   É preciso ter um locatário válido para o ServiceNow for Work ou ServiceNow for Education. Você pode usar uma conta de avaliação gratuita para qualquer serviço.
*   Uma conta de usuário no ServiceNow com permissões de Administrador de Equipe.

## <a name="assigning-users-to-servicenow"></a>Atribuir usuários ao ServiceNow

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e os grupos que foram "atribuídos" a um aplicativo no Azure AD serão sincronizados.

Antes de configurar e habilitar o serviço de provisionamento, você precisa decidir quais usuários e/ou grupos no Azure AD representam os usuários que precisam de acesso ao aplicativo ServiceNow. Depois de decidir, atribua esses usuários ao aplicativo ServiceNow seguindo estas instruções: [Atribuir um usuário ou grupo a um aplicativo empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)


> [!IMPORTANT]
>*   Recomendamos a atribuição de um único usuário do Azure AD ao ServiceNow para testar a configuração de provisionamento. Outros usuários e/ou grupos podem ser atribuídos mais tarde.
>*   Ao atribuir um usuário ao ServiceNow, você deve selecionar uma função de usuário válida. A função de "Acesso Padrão" não funciona para provisionamento.

## <a name="enable-automated-user-provisioning"></a>Habilitar o provisionamento automatizado de usuários

Esta seção explica como conectar o Azure AD à API de provisionamento de conta de usuário do ServiceNow e como configurar o serviço de provisionamento para criar, atualizar e desabilitar contas de usuário atribuídas no ServiceNow, com base na atribuição de usuário e de grupo do Azure AD.

> [!TIP]
>Você também pode optar por habilitar o Logon Único baseado em SAML no ServiceNow, seguindo as instruções fornecidas no [portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos sejam complementares.

### <a name="configure-automatic-user-account-provisioning"></a>Configurar o provisionamento automático de conta de usuário

1. No [Portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory > Aplicativos Empresariais > Todos os aplicativos**.

2. Se você já tiver configurado o ServiceNow para logon único, pesquise a instância do ServiceNow usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e pesquise **ServiceNow** na galeria de aplicativos. Selecione o ServiceNow nos resultados da pesquisa e adicione-o à lista de aplicativos.

3. Selecione a instância do ServiceNow e, depois, a guia **Provisionamento**.

4. Defina o **Modo de Provisionamento** como **Automático**. 

    ![provisionamento](./media/active-directory-saas-servicenow-provisioning-tutorial/provisioning.png)

5. Na seção “Credenciais de Administrador”, realize as seguintes etapas:
   
    a. Na caixa de texto **Nome de Instância do ServiceNow** , digite o nome da instância do ServiceNow.

    b. Na caixa de texto **Nome de Usuário do Administrador do ServiceNow**, digite o nome de usuário de um administrador.

    c. Na caixa de texto **Senha do Administrador do ServiceNow**, digite a senha do administrador.

6. No portal do Azure, clique em **Testar conectividade** para garantir que o Azure AD pode se conectar ao aplicativo ServiceNow. Se a conexão falhar, verifique se a sua conta do ServiceNow tem permissões de Administrador de Equipe e repita a etapa **"Credenciais de Administrador"**.

7. Insira o endereço de email de uma pessoa ou um grupo que deve receber notificações de erro de provisionamento no campo **Email de Notificação** e marque a caixa de seleção.

8. Clique em **Salvar.**

9. Na seção Mapeamentos, selecione **Sincronizar Usuários do Azure Active Directory com o ServiceNow.**

10. Na seção **Mapeamentos de Atributo**, examine os atributos de usuário que são sincronizados do Azure AD para o ServiceNow. Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário do ServiceNow em operações de atualização. Selecione o botão Salvar para confirmar as alterações.

11. Para habilitar o serviço de provisionamento do Azure AD no ServiceNow, altere o **Status de Provisionamento** para **Ativado** na seção Configurações

12. Clique em **Salvar.**

Isso inicia a sincronização inicial de todos os usuários e/ou grupos atribuídos ao ServiceNow na seção Usuários e Grupos. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 20 minutos, desde que o serviço esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para os relatórios de atividade de provisionamento, que descrevem todas as ações executadas pelo serviço de provisionamento em seu aplicativo ServiceNow.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configurar Logon Único](active-directory-saas-servicenow-tutorial.md)


