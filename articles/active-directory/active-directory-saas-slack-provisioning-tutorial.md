---
title: "Tutorial: Configurar o Slack para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs"
description: "Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no Slack."
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: sakula
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: asmalser-msft
ms.reviewer: asmalser
ms.openlocfilehash: 3cb49a4abb26c34a938c963c4cf326b5ccd490de
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-configuring-slack-for-automatic-user-provisioning"></a>Tutorial: Configurar o Slack para provisionamento automático de usuário


O objetivo deste tutorial é mostrar as etapas que precisam ser executadas no Slack e no Azure AD para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o Slack. 

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

*   Um locatário de diretório do Azure Active Directory
*   Um locatário do Slack com o [plano Plus](https://aadsyncfabric.slack.com/pricing), ou outro com mais recursos, habilitado 
*   Uma conta de usuário no Slack com permissões de Administrador de Equipe 

Observação: a integração de provisionamento do Azure AD depende da [API de SCIM do Slack](https://api.slack.com/scim), disponível para as equipes do Slack no plano Plus ou outro melhor.

## <a name="assigning-users-to-slack"></a>Como atribuir usuários ao Slack

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e grupos que foram "atribuídos" a um aplicativo no Azure AD serão sincronizados. 

Antes de configurar e habilitar o serviço de provisionamento, você precisará decidir quais usuários e/ou grupos no Azure AD representam os usuários que precisam de acesso ao seu aplicativo Slack. Depois de decidir, atribua esses usuários ao seu aplicativo Slack seguindo estas instruções:

[Atribuir um usuário ou um grupo a um aplicativo empresarial](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-slack"></a>Dicas importantes para atribuir usuários ao Slack

*   Recomendamos a atribuição de um único usuário do Azure AD ao Slack para testar a configuração de provisionamento. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um usuário ao Slack, selecione a função **Usuário** ou "Grupo" na caixa de diálogo de atribuição. A função de "Acesso Padrão" não funciona para provisionamento.


## <a name="configuring-user-provisioning-to-slack"></a>Como configurar o provisionamento de usuários no Slack 

Esta seção orienta você pela conexão do Azure AD com a API de provisionamento de conta de usuário do Slack e pela configuração do serviço de provisionamento, a fim de criar, atualizar e desabilitar contas de usuário atribuídas no Slack com base na atribuição de usuário e de grupo do Azure AD.

**Dica:** você também pode optar por habilitar o SSO baseado em SAML para o Slack seguindo as instruções fornecidas no (Portal do Azure) [https://portal.azure.com]. O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos sejam complementares.


### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Slack no Azure AD:


1)  No [Portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory > Aplicativos Empresariais > Todos os aplicativos**.

2) Se você já tiver configurado o Slack para logon único, procure sua instância do Slack usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e procure **Slack** na galeria de aplicativos. Selecione o Slack nos resultados da pesquisa e adicione-o à lista de aplicativos.

3)  Selecione sua instância do Slack e selecione a guia **Provisionamento**.

4)  Defina o **Modo de Provisionamento** como **Automático**.

![Provisionamento do Slack](./media/active-directory-saas-slack-provisioning-tutorial/Slack1.PNG)

5)  Na seção **Credenciais de Administrador**, clique em **Autorizar**. Isso abre uma caixa de diálogo de autorização do Slack em uma nova janela do navegador. 

6) Na nova janela, entre no Slack usando sua conta de Administrador de Equipe. Na caixa de diálogo de autorização resultante, selecione a equipe do Slack para a qual você deseja habilitar o provisionamento e, em seguida, selecione **Autorizar**. Depois de concluído, retorne ao Portal do Azure para concluir a configuração de provisionamento.

![Caixa de diálogo Autorização](./media/active-directory-saas-slack-provisioning-tutorial/Slack3.PNG)

7) No Portal do Azure, clique em **Testar Conexão** para garantir que o Azure AD possa se conectar ao seu aplicativo Slack. Se a conexão falhar, verifique se a sua conta do Slack tem permissões de Administrador de Equipe e repita a etapa "Autorizar".

8) Insira o endereço de email de uma pessoa ou grupo que deve receber notificações de erro de provisionamento no campo **Email de Notificação** e marque a caixa de seleção abaixo.

9) Clique em **Salvar**. 

10) Na seção Mapeamentos, selecione **Sincronizar usuários do Azure Active Directory com o Slack**.

11) Na seção **Mapeamentos de Atributo**, revise os atributos de usuário que serão sincronizados do Azure AD para o Slack. Observe que os atributos selecionados como propriedades **Correspondentes** serão usados para corresponder as contas de usuário no Slack para operações de atualização. Selecione o botão Salvar para confirmar as alterações.

12) Para habilitar o serviço de provisionamento do Azure AD para o Slack, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**

13) Clique em **Salvar**. 

Isso iniciará a sincronização inicial de todos os usuários e/ou grupos atribuídos ao Slack na seção Usuários e Grupos. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 10 minutos, desde que o serviço esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para os relatórios de atividade de provisionamento, que descrevem todas as ações executadas pelo serviço de provisionamento em seu aplicativo Slack.

## <a name="optional-configuring-group-object-provisioning-to-slack"></a>[Opcional] Como configurar o provisionamento do objeto de grupo para o Slack 

Opcionalmente, você pode habilitar o provisionamento de objetos de grupo do Azure AD para o Slack. Isso é diferente da "atribuição de grupos de usuários", pois o objeto de grupo real, junto com seus membros, será replicado do Azure AD para o Slack. Por exemplo, se você tiver um grupo chamado "Meu Grupo" no Azure AD, um grupo de idêntico chamado "Meu Grupos" será criado dentro do Slack.

### <a name="to-enable-provisioning-of-group-objects"></a>Para habilitar o provisionamento de objetos de grupo:

1) Na seção Mapeamentos, selecione **Sincronizar grupos do Azure Active Directory com o Slack**.

2) Na folha Mapeamento de Atributos, defina Habilitado como Sim.

3) Na seção **Mapeamentos de Atributo**, revise os atributos de grupo que serão sincronizados do Azure AD para o Slack. Observe que os atributos selecionados como propriedades **Correspondentes** serão usados para corresponder os grupos no Slack para operações de atualização. 

4) Clique em **Salvar**.

Isso resulta na sincronização total de qualquer objeto de grupo atribuído ao Slack na seção **Usuários e Grupos** do Azure AD para o Slack. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para os relatórios de atividade de provisionamento, que descrevem todas as ações executadas pelo serviço de provisionamento em seu aplicativo Slack.


## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](active-directory-enterprise-apps-manage-provisioning.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)
