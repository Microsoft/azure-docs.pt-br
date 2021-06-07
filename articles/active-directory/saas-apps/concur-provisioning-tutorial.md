---
title: 'Tutorial: Configurar Concur para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Concur.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: edb21287b30f8ba77d6312ec6b456e20aa260598
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358204"
---
# <a name="tutorial-configure-concur-for-automatic-user-provisioning"></a>Tutorial: Configurar Concur para provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas que precisam ser executadas no Concur e no Azure AD para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o Concur.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

*   Um locatário do Azure Active Directory.
*   Uma assinatura habilitada para logon único do Concur.
*   Uma conta de usuário no Concur com permissões de Administrador de Equipe.

## <a name="assigning-users-to-concur"></a>Como atribuir usuários ao Concur

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e os grupos que foram "atribuídos" a um aplicativo no Azure AD serão sincronizados.

Antes de configurar e habilitar o serviço de provisionamento, você precisa decidir quais usuários e/ou grupos no Azure AD representam os usuários que precisam de acesso ao seu aplicativo Concur. Depois de decidir, atribua esses usuários ao seu aplicativo Concur seguindo estas instruções:

[Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-concur"></a>Dicas importantes para atribuir usuários ao Concur

*   Recomendamos a atribuição de um único usuário do Azure AD ao Concur para testar a configuração de provisionamento. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um usuário ao Concur, você deve selecionar uma função de usuário válida. A função de "Acesso Padrão" não funciona para provisionamento.

## <a name="enable-user-provisioning"></a>Permitir provisionamento do usuário

Esta seção orienta você pela conexão do Azure AD com a API de provisionamento de conta de usuário do Concur e pela configuração do serviço de provisionamento, a fim de criar, atualizar e desabilitar contas de usuário atribuídas no Concur com base na atribuição de usuário e de grupo do Azure AD.

> [!Tip] 
> Você também pode optar por habilitar o logon único baseado em SAML para o Concur, seguindo as instruções fornecidas no [Portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos sejam complementares.

### <a name="to-configure-user-account-provisioning"></a>Para configurar o provisionamento de conta de usuário:

O objetivo desta seção é descrever como habilitar o provisionamento de contas de usuário do Active Directory no Concur.

Para habilitar os aplicativos no serviço de Despesas, é necessário configurar e usar um perfil de Administrador de Serviço Web. Não adicione a função de Administrador de WS ao seu perfil de administrador existente usado para as funções administrativas de T&E.

O consultor do Concur ou o administrador do cliente deve criar um perfil de Administrador de Serviços Web distinto e o administrador do cliente deve usar esse perfil para as funções de Administrador de Serviços Web (por exemplo, habilitação de aplicativos). Esses perfis devem ser mantidos separados do perfil de administração diária de T&E do administrador do cliente (o perfil de administração de T&E não deve ter a função de Administrador de Serviço Web).

Ao criar o perfil que será usado para habilitar o aplicativo, insira o nome do administrador do cliente nos campos de perfil do usuário. Isso atribuirá propriedade ao perfil. Depois que um ou mais perfis forem criados, o cliente deverá fazer logon com esse perfil para clicar no botão “*Habilitar*” de um Aplicativo do Parceiro no menu dos Serviços Web.

Essa ação não deve ser feita com o perfil usado para a administração normal de T&E pelos seguintes motivos.

* O cliente tem de ser aquele que clica em "*Sim*" na janela de diálogo que é exibida depois que um aplicativo é habilitado. Esse clique confirma que o cliente está disposto a permitir que o Aplicativo parceiro acesse seus dados, portanto você ou o Parceiro não pode clicar no botão Sim.

* Se um administrador de cliente que habilitou um aplicativo usando o perfil de administrador de T&E deixar a empresa (resultando na inativação do perfil), qualquer aplicativo habilitado com esse perfil não funcionará até que seja habilitado com outro perfil ativo de Administrador de WS. É por isso que você deve criar perfis de Administrador de Serviços Web distintos.

* Se um administrador deixar a empresa, o nome associado ao perfil de Administrador de WS pode ser alterado para o administrador substituto, se isso for desejado, sem afetar o aplicativo habilitado, porque esse perfil não ficará inativado.

**Para configurar o provisionamento de usuários, execute as seguintes etapas:**

1. Faça logon em seu locatário do **Concur**.

2. No menu **Administração**, selecione **Serviços Web**.
   
    ![Locatário do Concur](./media/concur-provisioning-tutorial/IC721729.png "Locatário do Concur")

3. Do lado esquerdo, no painel **Serviços Web**, selecione **Habilitar Aplicativo Parceiro**.
   
    ![Habilitar Aplicativo do Parceiro](./media/concur-provisioning-tutorial/ic721730.png "Habilitar Aplicativo do Parceiro")

4. Na lista **Habilitar Aplicativo**, selecione **Azure Active Directory** e, em seguida, clique em **Habilitar**.
   
    ![Active Directory do Microsoft Azure](./media/concur-provisioning-tutorial/ic721731.png "Active Directory do Microsoft Azure")

5. Clique em **Sim** para fechar a caixa de diálogo **Confirmar Ação**.
   
    ![Confirmar Ação](./media/concur-provisioning-tutorial/ic721732.png "Confirmar Ação")

6. No [Portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory > Aplicativos Empresariais > Todos os aplicativos**.

7. Se você já tiver configurado o Concur para logon único, pesquise sua instância do Concur usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e pesquise **Concur** na galeria de aplicativos. Selecione o Concur nos resultados da pesquisa e adicione-o à lista de aplicativos.

8. Selecione sua instância do Concur e selecione a guia **Provisionamento**.

9. Defina o **Modo de Provisionamento** como **Automático**. 
 
    ![Captura de tela da guia Provisionamento para o Concur no portal do Azure. O Modo de Provisionamento é definido como Automático e o botão Testar Conectividade é realçado.](./media/concur-provisioning-tutorial/provisioning.png)

10. Na seção **Credenciais do Administrador**, insira o **nome de usuário** e a **senha** do administrador do Concur.

11. No Portal do Azure, clique em **Testar Conexão** para garantir que o Azure AD possa se conectar ao seu aplicativo Concur. Se a conexão falhar, verifique se a sua conta do Concur tem permissões de Administrador de Equipe.

12. Insira o endereço de email de uma pessoa ou um grupo que deve receber notificações de erro de provisionamento no campo **Email de Notificação** e marque a caixa de seleção.

13. Clique em **Salvar.**

14. Na seção Mapeamentos, selecione **Sincronizar Usuários do Azure Active Directory com o Concur.**

15. Na seção **Mapeamentos de Atributo**, revise os atributos de usuário que serão sincronizados do Azure AD para o Concur. Os atributos selecionados como propriedades **Correspondentes** serão usados para corresponder as contas de usuário no Concur para operações de atualização. Selecione o botão Salvar para confirmar as alterações.

16. Para habilitar o serviço de provisionamento do Azure AD para o Concur, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**

17. Clique em **Salvar.**

Agora você pode criar uma conta de teste. Aguarde 20 minutos para verificar se a conta foi sincronizada com o Concur.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Configurar Logon Único](concur-tutorial.md)