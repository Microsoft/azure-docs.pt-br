---
title: 'Tutorial: configurar o Playvox para o provisionamento automático de usuário usando Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o Playvox.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: c31c20ab-f6cd-40e1-90ad-fa253ecbc0f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2020
ms.author: Zhchia
ms.openlocfilehash: 3c7efca5e052c2d0680aa7ca3e1b6d27bfdd7d11
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862472"
---
# <a name="tutorial-configure-playvox-for-automatic-user-provisioning"></a>Tutorial: configurar o Playvox para o provisionamento automático de usuário

Este tutorial descreve as etapas a serem seguidas no Playvox e no Azure Active Directory (AD do Azure) para configurar o provisionamento automático de usuário. Quando configurado, o Azure AD provisiona e desprovisiona automaticamente usuários ou grupos para [Playvox](https://www.playvox.com) usando o serviço de provisionamento do Azure AD. Para obter detalhes importantes sobre o que esse serviço faz e como ele funciona, e para perguntas frequentes, consulte [automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Funcionalidades com suporte
> [!div class="checklist"]
> * Crie usuários no Playvox.
> * Remova os usuários no Playvox quando eles não precisam mais de acesso.
> * Mantenha os atributos de usuário sincronizados entre o Azure AD e o Playvox.

## <a name="prerequisites"></a>Pré-requisitos

O cenário neste tutorial pressupõe que você já tenha os seguintes pré-requisitos:

* [Um locatário do Azure AD](../develop/quickstart-create-new-tenant.md).
* Uma conta de usuário no Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento. Por exemplo, uma conta pode ter o administrador do aplicativo, o administrador do aplicativo de nuvem, o proprietário do aplicativo ou a função de administrador global.
* Uma conta de usuário no [Playvox](https://www.playvox.com) com permissões de superadministrador.

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1: planejar sua implantação de provisionamento

1. Saiba [como o serviço de provisionamento funciona](../app-provisioning/user-provisioning.md).

2. Determine quem estará [no escopo do provisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

3. Determine quais dados [mapeados entre o Azure AD e o Playvox](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-playvox-to-support-provisioning-by-using-azure-ad"></a>Etapa 2: configurar o Playvox para dar suporte ao provisionamento usando o Azure AD

1. Faça logon no console de administração do Playvox e vá para **configurações > chaves de API**.

2. Selecione **criar chave de API**.

    ![Captura de tela parcial mostrando o local do botão Criar chave de API na interface do usuário do Playvox.](media/playvox-provisioning-tutorial/create.png)

3. Insira um nome significativo para a chave de API e, em seguida, selecione **salvar**. Depois que a chave de API for gerada, selecione **fechar**.

4. Na chave de API que você criou, selecione o ícone de **detalhes** .

    ![Captura de tela parcial mostrando o local do ícone de detalhes, que é uma lupa, na interface do usuário do Playvox.](media/playvox-provisioning-tutorial/api.png)

5. Copie e salve o valor da **chave Base64** . Posteriormente, no portal do Azure, você inserirá esse valor na caixa de texto **token secreto** na guia **provisionamento** do aplicativo Playvox.

    ![Captura de tela da caixa de mensagem detalhes da chave de API, com o valor de chave BASE64 realçado.](media/playvox-provisioning-tutorial/token.png)

## <a name="step-3-add-playvox-from-the-azure-ad-application-gallery"></a>Etapa 3: Adicionar o Playvox da Galeria de aplicativos do Azure AD

Para começar a gerenciar o provisionamento para o Playvox, adicione o Playvox ao seu locatário do Azure AD por meio da Galeria de aplicativos. Para saber mais, consulte [início rápido: adicionar um aplicativo ao seu locatário do Azure Active Directory (Azure AD)](../manage-apps/add-application-portal.md).

Se você tiver configurado anteriormente o Playvox para SSO (logon único), poderá usar o mesmo aplicativo. No entanto, recomendamos que você crie um aplicativo separado ao testar a integração inicialmente.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4: definir quem estará no escopo para provisionamento

Você usa o serviço de provisionamento do Azure AD para o escopo que será provisionado, com base na atribuição para o aplicativo ou em atributos do usuário ou grupo. Para definir o escopo que será provisionado em seu aplicativo com base na atribuição, consulte [gerenciar a atribuição de usuário para um aplicativo no Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md) para saber como atribuir usuários ou grupos ao aplicativo. Para o escopo que será provisionado com base apenas em atributos do usuário ou grupo, use um filtro de escopo conforme descrito em [provisionamento de aplicativo baseado em atributo com filtros de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

Lembre-se destes pontos:

* Ao atribuir usuários ao Playvox, você deve selecionar uma função diferente de acesso padrão. Os usuários com a função Acesso Padrão são excluídos do provisionamento e serão marcados como "Não qualificado efetivamente" nos logs de provisionamento. Se o acesso padrão for a única função disponível no aplicativo, você poderá [atualizar o manifesto do aplicativo](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar outras funções.

* Comece pequeno. Teste com um pequeno conjunto de usuários ou grupos antes de distribuir para todos. Quando o escopo de provisionamento é baseado em usuários ou grupos atribuídos, você pode controlar o tamanho do conjunto atribuindo apenas um ou dois usuários ou grupos ao aplicativo. Quando o escopo de provisionamento inclui todos os usuários e grupos, você pode especificar um [filtro de escopo baseado em atributo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) para limitar o tamanho do seu conjunto de testes.

## <a name="step-5-configure-automatic-user-provisioning-to-playvox"></a>Etapa 5: configurar o provisionamento automático de usuário para o Playvox

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários ou grupos com base em atribuições de usuário ou de grupo no Azure AD.

Para configurar o provisionamento automático de usuário para Playvox no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.

    ![Captura de tela parcial do portal do Azure, com aplicativos empresariais e todos os itens de aplicativos realçados](common/enterprise-applications.png)

2. Na lista de aplicativos, procure e selecione **Playvox**.

    ![Captura de tela parcial da lista de aplicativos, com a caixa de pesquisa de aplicativo realçada.](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela parcial mostrando o item de menu de provisionamento.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela parcial da guia provisionamento, mostrando a opção automática selecionada na caixa de listagem suspensa modo de provisionamento.](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira sua **URL de locatário** do Playvox como:

    `https://{tenant}.playvox.com/scim/v1`

    Insira o **token secreto** que você copiou anteriormente na etapa 2. Em seguida, selecione **testar conexão** para garantir que o Azure ad possa se conectar ao Playvox. Se a conexão falhar, verifique se sua conta do Playvox tem permissões de administrador e tente novamente.

    ![Captura de tela parcial mostrando a seção de credenciais de administrador, incluindo as caixas de texto URL do locatário e token secreto, e com o link testar conexão realçado.](common/provisioning-testconnection-tenanturltoken.png)

6. Na caixa de texto **email de notificação** , insira o endereço de email de uma pessoa ou grupo que receberá as notificações de erro de provisionamento. Depois, marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Captura de tela parcial mostrando a caixa de texto email de notificação e a caixa de seleção notificação por email.](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para Playvox**.

9. Examine os atributos de usuário que são sincronizados do Azure AD para o Playvox na seção de **mapeamento de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no Playvox para operações de atualização. Se você optar por alterar o [atributo de destino correspondente](../app-provisioning/customize-application-attributes.md), verifique se a API Playvox dá suporte à filtragem de usuários com base nesse atributo. Para confirmar eventuais alterações, selecione **Salvar**.

   |Atributo|Type|Com suporte para filtragem|
   |---|---|---|
   |userName|String|&check;|
   |ativo|Boolean|
   |displayName|String|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |externalId|String|

10. Para configurar filtros de escopo, confira as instruções fornecidas no [tutorial sobre filtros de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento do Azure AD para o Playvox, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Captura de tela parcial da seção Configurações, mostrando o status de provisionamento definido como ativado.](common/provisioning-toggle-on.png)

12. Ainda em **configurações**, defina os usuários ou grupos a serem provisionados para o Playvox escolhendo os valores desejados no **escopo**.

    ![Captura de tela parcial da seção Configurações, mostrando a caixa de listagem suspensa escopo.](common/provisioning-scope.png)

13. Quando estiver pronto para fazer o provisionamento, selecione **Salvar**.

    ![Captura de tela parcial mostrando opções de salvar e descartar.](common/provisioning-configuration-save.png)

Essa operação começa o ciclo de sincronização inicial de todos os usuários e grupos definidos no **Escopo** na seção **Configurações**. O ciclo inicial leva mais tempo do que os ciclos posteriores. Os ciclos posteriores ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução.

## <a name="step-6-monitor-your-deployment"></a>Etapa 6: monitorar sua implantação

Depois de configurar o provisionamento, use os seguintes recursos para monitorar sua implantação:

* Use os [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais usuários foram provisionados com êxito ou não.
* Confira a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o status do ciclo de provisionamento e saber como fechá-la para concluir.
* Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Para saber mais sobre os estados de quarentena, confira [Provisionamento de aplicativo no status de quarentena](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para aplicativos empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)