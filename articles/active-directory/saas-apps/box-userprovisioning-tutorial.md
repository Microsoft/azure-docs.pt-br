---
title: 'Tutorial: Configurar o Box para provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Box.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: jeedes
ms.openlocfilehash: df4031a590eb4547d4327cebe96ccbe63d21785a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102437801"
---
# <a name="tutorial-configure-box-for-automatic-user-provisioning"></a>Tutorial: Configurar o Box para provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas que precisam ser executadas no Box e no Azure AD para provisionar e desprovisionar automaticamente contas de usuário do Azure AD no Box.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Box, você precisará dos seguintes itens:

- Um locatário do Azure AD
- Um plano de negócios do Box ou melhor

> [!NOTE]
> Ao testar as etapas deste tutorial, recomendamos que você *não* use um ambiente de produção.

> [!NOTE]
> Os aplicativos precisam ser habilitados primeiro no aplicativo Box.

> [!NOTE]
> Essa integração também está disponível para uso do ambiente de Nuvem do Governo dos EUA do Azure AD. Encontre esse aplicativo na Galeria de Aplicativos de Nuvem do Governo dos EUA do Azure AD e configure-o da mesma forma que na nuvem pública.

Para testar as etapas neste tutorial, siga estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assigning-users-to-box"></a>Atribuindo usuários ao Box 

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e os grupos que foram "atribuídos" a um aplicativo no Azure AD serão sincronizados.

Antes de configurar e habilitar o serviço de provisionamento, você precisa decidir quais usuários e/ou grupos do Azure AD representam os usuários que precisam de acesso ao aplicativo Box. Depois de decidir, você pode atribuir esses usuários ao aplicativo Box seguindo estas instruções:

[Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="assign-users-and-groups"></a>Atribuir usuários e grupos
A guia **Box > Usuários e Grupos** no portal do Azure permite especificar quais usuários e grupos devem ter acesso ao Box. A atribuição de um usuário ou grupo faz com que as seguintes ações ocorram:

* O Azure AD permite que o usuário atribuído (seja por atribuição direta ou associação de grupo) realize a autenticação no Box. Se um usuário não for atribuído, o Azure AD não permitirá que ele entre no Box e retornará um erro na página de entrada do Azure AD.
* Um bloco de aplicativo do Box é adicionado ao [iniciador do aplicativo](../manage-apps/end-user-experiences.md)do usuário.
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

    ![Captura de tela da guia Provisionamento para Box no portal do Azure. O Modo de Provisionamento é definido como Automático e o botão Autorizar é realçado em Credenciais de Administrador.](./media/box-userprovisioning-tutorial/provisioning.png)

5. Na seção **Credenciais de Administrador**, clique em **Autorizar** para abrir uma caixa de diálogo de logon do Box em uma nova janela do navegador.

6. Na página **Fazer logon para conceder acesso ao Box**, forneça as credenciais necessárias e clique em **Autorizar**. 
   
    ![Captura de tela Fazer logon para conceder acesso ao Box, mostrando a entrada para E-mail e Senha e o botão Autorizar.](./media/box-userprovisioning-tutorial/IC769546.png "Habilitar o provisionamento automático de usuários.")

7. Clique em **Conceder acesso ao Box** para autorizar essa operação e retornar ao portal do Azure. 
   
    ![Captura de tela Autorizar acesso no Box, mostrando uma mensagem explicativa e o botão Conceder acesso ao Box.](./media/box-userprovisioning-tutorial/IC769549.png "Habilitar o provisionamento automático de usuários.")

8. No portal do Azure, clique em **Testar conectividade** para garantir que o Azure AD possa se conectar ao aplicativo Box. Se a conexão falhar, verifique se sua conta do Box tem permissões de Administrador de Equipe e repita a etapa **"Autorizar"**.

9. Insira o endereço de email de uma pessoa ou um grupo que deve receber notificações de erro de provisionamento no campo **Email de Notificação** e marque a caixa de seleção.

10. Clique em **Salvar.**

11. Na seção Mapeamentos, selecione **Sincronizar usuários do Azure Active Directory com o Box**.

12. Na seção **Mapeamentos de Atributo**, revise os atributos de usuário que serão sincronizados do Azure AD com o Box. Os atributos selecionados como propriedades **Correspondentes** serão usados para fazer a correspondência entre as contas de usuário no Box para operações de atualização. Selecione o botão Salvar para confirmar as alterações.

13. Para habilitar o serviço de provisionamento do Azure AD para o Box, altere o **Status de Provisionamento** para **Ativado** na seção Configurações

14. Clique em **Salvar.**

Isso inicia a sincronização inicial de todos os usuários e/ou grupos atribuídos ao Box na seção Usuários e Grupos. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para os logs de atividade de provisionamento, que descrevem todas as ações executadas pelo serviço de provisionamento no aplicativo Box.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

No seu locatário do Box, os usuários sincronizados estão listados em **Usuários gerenciados** no **Console de Admin**.

![Status da integração](./media/box-userprovisioning-tutorial/IC769556.png "Status da integração")


## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Configurar Logon Único](box-tutorial.md)