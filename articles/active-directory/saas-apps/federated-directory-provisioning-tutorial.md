---
title: 'Tutorial: Configurar o diretório federado para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o diretório federado.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: bc19a64fc72fef6407416f65cdc3b60a4c4f9464
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68296084"
---
# <a name="tutorial-configure-federated-directory-for-automatic-user-provisioning"></a>Tutorial: Configurar o diretório federado para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no diretório federado e Azure Active Directory (AD do Azure) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no diretório federado.

> [!NOTE]
>  Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os termos de uso geral de Microsoft Azure para recursos de visualização, consulte [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD.
* [Um diretório federado](https://www.federated.directory/pricing).
* Uma conta de usuário no diretório federado com permissões de administrador.

## <a name="assign-users-to-federated-directory"></a>Atribuir usuários ao diretório federado
Azure Active Directory usa um conceito chamado atribuições para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao diretório federado. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao diretório federado seguindo as instruções aqui:

 * [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md) 
 
 ## <a name="important-tips-for-assigning-users-to-federated-directory"></a>Dicas importantes para atribuir usuários ao diretório federado
 * É recomendável que um único usuário do Azure AD seja atribuído ao diretório federado para testar a configuração automática de provisionamento de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao diretório federado, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Os usuários com a função de acesso padrão são excluídos do provisionamento.
    
 ## <a name="set-up-federated-directory-for-provisioning"></a>Configurar o diretório federado para provisionamento

Antes de configurar o diretório federado para o provisionamento automático de usuário com o Azure AD, será necessário habilitar o provisionamento do SCIM no diretório federado.

1. Entre no console do [administrador do diretório federado](https://federated.directory/of)

    ![Tutorial de diretório federado](media/federated-directory-provisioning-tutorial/companyname.png)

2. Navegue até **diretórios > diretórios de usuário** e selecione seu locatário. 

    ![diretório federado](media/federated-directory-provisioning-tutorial/ad-user-directories.png)

3.  Para gerar um token de portador permanente, navegue até **chaves de diretório > criar nova chave.** 

    ![diretório federado](media/federated-directory-provisioning-tutorial/federated01.png)

4. Crie uma chave de diretório. 

    ![diretório federado](media/federated-directory-provisioning-tutorial/federated02.png)
    

5. Copie o valor de **Token de Acesso**. Esse valor será inserido no campo **token secreto** na guia provisionamento do seu aplicativo de diretório federado no portal do Azure. 

    ![diretório federado](media/federated-directory-provisioning-tutorial/federated03.png)
    
## <a name="add-federated-directory-from-the-gallery"></a>Adicionar diretório federado da Galeria

Para configurar o diretório federado para o provisionamento automático de usuário com o Azure AD, você precisa adicionar o diretório federado da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o diretório federado da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **diretório federado**, selecione **diretório federado** no painel de resultados.

    ![Diretório federado na lista de resultados](common/search-new-app.png)

5. Navegue até a **URL** realçada abaixo em um navegador separado. 

    ![diretório federado](media/federated-directory-provisioning-tutorial/loginpage1.png)

6. Clique em **fazer logon**.

    ![diretório federado](media/federated-directory-provisioning-tutorial/federated04.png)

7.  Como o diretório federado é um aplicativo OpenIDConnect, opte por fazer logon no diretório federado usando sua conta corporativa da Microsoft.
    
    ![diretório federado](media/federated-directory-provisioning-tutorial/loginpage3.png)
 
8. Após uma autenticação bem-sucedida, aceite a solicitação de consentimento para a página de consentimento. O aplicativo será adicionado automaticamente ao seu locatário e você será redirecionado para sua conta de diretório federado.

    ![Adicionar SCIM do diretório federado](media/federated-directory-provisioning-tutorial/premission.png)



## <a name="configuring-automatic-user-provisioning-to-federated-directory"></a>Configurando o provisionamento automático de usuário para o diretório federado 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no diretório federado com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-federated-directory-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o diretório federado no Azure AD:

1. Entre no [Portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **diretório federado**.

    ![O link do diretório federado na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira `https://api.federated.directory/v2/` a URL de locatário. Insira o valor que você recuperou e salvou anteriormente do diretório federado no **token secreto**. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao diretório federado. Se a conexão falhar, verifique se sua conta de diretório federado tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

9. Clique em **Salvar**.

10. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para o diretório federado**.

    ![Tutorial de diretório federado](media/federated-directory-provisioning-tutorial/user-mappings.png)
    
    
11. Examine os atributos de usuário que são sincronizados do Azure AD para o diretório federado na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no diretório federado para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Tutorial de diretório federado](media/federated-directory-provisioning-tutorial/user-attributes.png)
    

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD para o diretório federado, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você deseja provisionar para o diretório federado escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no diretório federado.

Para obter mais informações sobre como ler os logs de provisionamento do Azure AD, consulte [relatórios sobre o provisionamento automático de conta de usuário](../manage-apps/check-status-user-account-provisioning.md)
## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
