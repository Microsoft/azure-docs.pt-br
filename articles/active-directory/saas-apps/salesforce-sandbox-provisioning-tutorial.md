---
title: 'Tutorial: Configurar a Área Restrita Salesforce para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Conheça as etapas necessárias a serem executadas na Área Restrita do Salesforce e no Azure AD para provisionar e desprovisionar automaticamente contas de usuário do Azure AD para a Área Restrita do Salesforce.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 7e3f8e5e975468b468712ae8907cdca0e80a5f9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94352601"
---
# <a name="tutorial-configure-salesforce-sandbox-for-automatic-user-provisioning"></a>Tutorial: Configurar a Área Restrita Salesforce para o provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas que precisam ser executadas na Área Restrita Salesforce e no Azure AD para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para a Área Restrita do Salesforce.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

*   Um locatário do Azure Active Directory.
*   Um locatário válido para a Área Restrita Salesforce for Work ou Área Restrita Salesforce for Education. Você pode usar uma conta de avaliação gratuita para qualquer serviço.
*   Uma conta de usuário na Área Restrita Salesforce com permissões de Administrador de Equipe.

## <a name="assigning-users-to-salesforce-sandbox"></a>Atribuir usuários à Área Restrita Salesforce

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e os grupos que foram "atribuídos" a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o serviço de provisionamento, você precisa decidir quais usuários ou grupos no Azure AD precisam de acesso ao aplicativo de Área Restrita Salesforce. Depois de decidir, você pode atribuir esses usuários ao aplicativo de Área Restrita Salesforce seguindo as instruções em [Atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-salesforce-sandbox"></a>Dicas importantes para atribuir usuários à Área Restrita Salesforce

* Recomendamos a atribuição de um único usuário do Azure AD à Área Restrita Salesforce para testar a configuração de provisionamento. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário à Área Restrita Salesforce, você deve selecionar uma função de usuário válida. A função de "Acesso Padrão" não funciona para provisionamento.

> [!NOTE]
> Este aplicativo importa funções personalizadas da Área Restrita Salesforce como parte do processo de provisionamento, que o cliente deve desejar selecionar durante a atribuição de usuários.

## <a name="enable-automated-user-provisioning"></a>Habilitar o provisionamento automatizado de usuários

Esta seção orienta você pela conexão do Azure AD com a API de provisionamento de conta de usuário da Área Restrita Salesforce e pela configuração do serviço de provisionamento, a fim de criar, atualizar e desabilitar contas de usuário atribuídas na Área Restrita Salesforce com base na atribuição de usuário e de grupo do Azure AD.

>[!Tip]
>Você também pode optar por habilitar o Logon Único baseado em SAML para a Área Restrita Salesforce, seguindo as instruções fornecidas no [Portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos sejam complementares.

### <a name="configure-automatic-user-account-provisioning"></a>Configurar o provisionamento automático de conta de usuário

O objetivo desta seção é descrever como habilitar o provisionamento de contas de usuário do Active Directory na Área Restrita Salesforce.

1. No [Portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory > Aplicativos Empresariais > Todos os aplicativos**.

1. Se você já tiver configurado a Área Restrita Salesforce para logon único, pesquise sua instância da Área Restrita Salesforce usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e pesquise **Área Restrita Salesforce** na galeria de aplicativos. Selecione a Área Restrita Salesforce nos resultados da pesquisa e adicione-o à lista de aplicativos.

1. Selecione sua instância da Área Restrita Salesforce e selecione a guia **Provisionamento**.

1. Defina o **Modo de Provisionamento** como **Automático**.

    ![A captura de tela mostra a página Provisionamento da Área Restrita do Salesforce, com o Modo de Provisionamento definido como Automático e outros valores que você pode definir.](./media/salesforce-sandbox-provisioning-tutorial/provisioning.png)

1. Na seção **Credenciais de Administrador**, forneça as seguintes definições de configuração:
   
    a. Na caixa de texto **Nome de Usuário Administrador**, digite o nome da conta da Área Restrita Salesforce com o perfil **Administrador de Sistema** do Salesforce.com atribuído.
   
    b. Na caixa de texto **Senha do Administrador**, digite a senha dessa conta.

1. Para obter o token de segurança da Área Restrita Salesforce, abra uma nova guia e entre na mesma conta do administrador da Área Restrita Salesforce. No canto superior direito da página, clique em seu nome e em **Configurações**.

     ![A captura de tela mostra o link Configurações selecionado.](./media/salesforce-sandbox-provisioning-tutorial/sf-my-settings.png "Habilitar o provisionamento automático de usuários.")

1. No painel de navegação esquerdo, clique em **Minhas Informações Pessoais** para expandir a seção correspondente e clique em **Redefinir Meu Token de Segurança**.
  
    ![A captura de tela mostra a opção Redefinir Meu Token de Segurança selecionada em Minhas Informações Pessoais.](./media/salesforce-sandbox-provisioning-tutorial/sf-personal-reset.png "Habilitar o provisionamento automático de usuários.")

1. Na página **Redefinir Token de Segurança**, clique no botão **Redefinir Token de Segurança**.

    ![A captura de tela mostra a página Redefinir Token de Segurança, com um texto explicativo e a opção Redefinir Token de Segurança](./media/salesforce-sandbox-provisioning-tutorial/sf-reset-token.png "Habilitar o provisionamento automático de usuários.")

1. Marque a caixa de entrada de email associada a essa conta de administrador. Procure um email do Salesforce Sandbox.com que contenha o novo token de segurança.

1. Copie o token, vá até a janela do Azure AD e cole-o no campo **Token Secreto**.

1. No portal do Azure, clique em **Testar Conexão** para garantir que o Azure AD possa se conectar ao seu aplicativo Área Restrita Salesforce.

1. No campo **Email de Notificação**, insira o endereço de email de uma pessoa ou um grupo que deve receber notificações de erro de provisionamento e marque a caixa de seleção.

1. Clique em **Salvar.**  
    
1.  Na seção Mapeamentos, selecione **Sincronizar Usuários do Azure Active Directory com a Área Restrita Salesforce.**

1. Na seção **Mapeamentos de Atributo**, revise os atributos de usuário que serão sincronizados do Azure AD para a Área Restrita Salesforce. Observe que os atributos selecionados como propriedades **Correspondentes** serão usados para corresponder as contas de usuário na Área Restrita Salesforce para operações de atualização. Selecione o botão Salvar para confirmar as alterações.

1. Para habilitar o serviço de provisionamento do Azure AD para a Área Restrita Salesforce, altere o **Status de Provisionamento** para **Ativado** na seção Configurações

1. Clique em **Salvar.**

Isso iniciará a sincronização inicial de todos os usuários e/ou grupos atribuídos à Área Restrita Salesforce na seção Usuários e Grupos. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para os logs de atividade de provisionamento, que descrevem todas as ações executadas pelo serviço de provisionamento em seu aplicativo Área Restrita Salesforce.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Configurar Logon Único](./salesforce-sandbox-tutorial.md)