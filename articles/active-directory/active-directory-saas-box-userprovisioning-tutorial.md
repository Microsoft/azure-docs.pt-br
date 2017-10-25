---
title: "Tutorial: integração do Azure Active Directory com o Box | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Box."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 1c959595-6e57-4954-9c0d-67ba03ee212b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: 9f061f3f5a0a4825854b893150ceccc8951487de
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-configuring-box-for-automatic-user-provisioning"></a>Tutorial: Configurar o Box para provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas que precisam ser executadas no Box e no Azure AD para provisionar e desprovisionar automaticamente contas de usuário do Azure AD no Box.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

*   Um locatário do Azure Active Directory.
*   Uma assinatura do Box habilitada para logon único.
*   Uma conta de usuário do Box com permissões de Administrador de Equipe.

## <a name="assigning-users-to-box"></a>Atribuindo usuários ao Box 

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e os grupos que foram "atribuídos" a um aplicativo no Azure AD serão sincronizados.

Antes de configurar e habilitar o serviço de provisionamento, você precisa decidir quais usuários e/ou grupos do Azure AD representam os usuários que precisam de acesso ao aplicativo Box. Depois de decidir, você pode atribuir esses usuários ao aplicativo Box seguindo estas instruções:

[Atribuir um usuário ou um grupo a um aplicativo empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

## <a name="assign-users-and-groups"></a>Atribuir usuários e grupos
A guia **Box > Usuários e Grupos** no portal do Azure permite especificar quais usuários e grupos devem ter acesso ao Box. A atribuição de um usuário ou grupo faz com que as seguintes ações ocorram:

* O Azure AD permite que o usuário atribuído (seja por atribuição direta ou associação de grupo) realize a autenticação no Box. Se um usuário não for atribuído, o Azure AD não permitirá que ele entre no Box e retornará um erro na página de entrada do Azure AD.
* Um bloco de aplicativo do Box é adicionado ao [iniciador do aplicativo](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)do usuário.
* Se o provisionamento automático estiver habilitado, os usuários e/ou grupos atribuídos serão adicionados à fila de provisionamento para serem provisionado automaticamente.
  
  * Se apenas objetos de usuário tiverem sido configurados para serem provisionados, todos os usuários atribuídos diretamente e todos os usuários que são membros de grupos atribuídos serão colocados na fila de provisionamento. 
  * Se os objetos de grupo tiverem sido configurados para serem provisionados, todos os objetos de grupo atribuídos serão provisionados para o Box, bem como todos os usuários que são membros desses grupos. As associações de grupo e usuário são preservadas ao serem gravadas no Box.

Você pode usar a guia **Atributos > Logon único** para configurar quais atributos de usuário (ou declarações) são apresentadas ao Box durante a autenticação baseada em SAML e a guia **Atributos > Provisionamento** para configurar como os atributos de usuário e grupo fluem do Azure AD para o Box durante as operações de provisionamento.

### <a name="important-tips-for-assigning-users-to-box"></a>Dicas importantes para atribuir usuários ao Box 

*   Recomendamos atribuir um único usuário do Azure AD ao Box para testar a configuração de provisionamento. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um usuário ao Box, você precisa selecionar uma função de usuário válida. A função de "Acesso Padrão" não funciona para provisionamento.

## <a name="enable-automated-user-provisioning"></a>Habilitar o Provisionamento Automatizado de Usuários

Esta seção orienta você quanto à conexão do Azure AD com a API de provisionamento de conta de usuário do Box e à configuração do serviço de provisionamento, a fim de criar, atualizar e desabilitar contas de usuário atribuídas no Box com base na atribuição de usuário e de grupo do Azure AD.

Se o provisionamento automático estiver habilitado, os usuários e/ou grupos atribuídos serão adicionados à fila de provisionamento para serem provisionado automaticamente.
    
 * Se apenas objetos de usuário tiverem sido configurados para serem provisionados, os usuários atribuídos diretamente e os usuários que são membros de grupos atribuídos serão colocados na fila de provisionamento. 
    
 * Se os objetos de grupo tiverem sido configurados para serem provisionados, todos os objetos de grupo atribuídos serão provisionados para o Box, bem como todos os usuários que são membros desses grupos. As associações de grupo e usuário são preservadas ao serem gravadas no Box.

> [!TIP] 
> Você também pode optar por habilitar o Logon único baseado em SAML para o Box seguindo as instruções fornecidas no [Portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-account-provisioning"></a>Para configurar o provisionamento automático de conta de usuário:

O objetivo desta seção é descrever como habilitar o provisionamento de contas de usuário do Active Directory no Box.

1. No [Portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory > Aplicativos Empresariais > Todos os aplicativos**.

2. Se já tiver configurado o Box para logon único, pesquise sua instância do Box usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e pesquise o **Box** na galeria de aplicativos. Selecione o Box nos resultados da pesquisa e adicione-o à lista de aplicativos.

3. Selecione sua instância do Box e selecione a guia **Provisionamento**.

4. Defina o **Modo de Provisionamento** como **Automático**. 

    ![provisionamento](./media/active-directory-saas-box-userprovisioning-tutorial/provisioning.png)

5. Na seção **Credenciais de Administrador**, clique em **Autorizar** para abrir uma caixa de diálogo de logon do Box em uma nova janela do navegador.

6. Na página **Fazer logon para conceder acesso ao Box**, forneça as credenciais necessárias e clique em **Autorizar**. 
   
    ![Habilitar o provisionamento automático de usuário](./media/active-directory-saas-box-userprovisioning-tutorial/IC769546.png "Habilitar o provisionamento de usuário automático")

7. Clique em **Conceder acesso ao Box** para autorizar essa operação e retornar ao portal do Azure. 
   
    ![Habilitar o provisionamento automático de usuário](./media/active-directory-saas-box-userprovisioning-tutorial/IC769549.png "Habilitar o provisionamento de usuário automático")

8. No portal do Azure, clique em **Testar conectividade** para garantir que o Azure AD possa se conectar ao aplicativo Box. Se a conexão falhar, verifique se sua conta do Box tem permissões de Administrador de Equipe e repita a etapa **"Autorizar"**.

9. Insira o endereço de email de uma pessoa ou um grupo que deve receber notificações de erro de provisionamento no campo **Email de Notificação** e marque a caixa de seleção.

10. Clique em **Salvar.**

11. Na seção Mapeamentos, selecione **Sincronizar usuários do Azure Active Directory com o Box**.

12. Na seção **Mapeamentos de Atributo**, revise os atributos de usuário que serão sincronizados do Azure AD com o Box. Os atributos selecionados como propriedades **Correspondentes** serão usados para fazer a correspondência entre as contas de usuário no Box para operações de atualização. Selecione o botão Salvar para confirmar as alterações.

13. Para habilitar o serviço de provisionamento do Azure AD para o Box, altere o **Status de Provisionamento** para **Ativado** na seção Configurações

14. Clique em **Salvar.**

Isso inicia a sincronização inicial de todos os usuários e/ou grupos atribuídos ao Box na seção Usuários e Grupos. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 20 minutos, desde que o serviço esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para os relatórios de atividade de provisionamento, que descrevem todas as ações executadas pelo serviço de provisionamento no aplicativo Box.

Agora você pode criar uma conta de teste. Aguarde até 20 minutos para confirmar se a conta foi sincronizada com o Box.

Em seu locatário do Box, os usuários sincronizados estão listados em **Usuários Gerenciados** no **Console do Administrador**.

![Status da integração](./media/active-directory-saas-box-userprovisioning-tutorial/IC769556.png "Status da integração")


## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configurar Logon Único](active-directory-saas-box-tutorial.md)