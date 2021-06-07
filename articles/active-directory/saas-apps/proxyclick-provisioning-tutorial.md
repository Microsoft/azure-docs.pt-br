---
title: 'Tutorial: Configurar o Proxyclick para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar contas de usuário automaticamente para o Proxyclick.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: f7d2a6f01e891a7fb1c14cde552d66679e474139
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94359126"
---
# <a name="tutorial-configure-proxyclick-for-automatic-user-provisioning"></a>Tutorial: Configurar o Proxyclick para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Proxyclick e no Azure AD (Active Directory) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no Proxyclick.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do Proxyclick](https://www.proxyclick.com/pricing)
* Uma conta de usuário no Proxyclick com permissões de administrador.

## <a name="add-proxyclick-from-the-gallery"></a>Adicionar o Proxyclick por meio da galeria

Antes de configurar o Proxyclick para o provisionamento automático de usuário com o Azure AD, é necessário adicioná-lo por meio da galeria de aplicativos do Azure AD à lista de aplicativos SaaS gerenciados.

**Para adicionar o Proxyclick por meio da galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Proxyclick**, selecione o **Proxyclick** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Proxyclick na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-proxyclick"></a>Atribuir usuários ao Proxyclick

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou os grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Para configurar e habilitar o provisionamento automático de usuário, decida quais usuários e/ou grupos no Azure AD precisam de acesso ao Proxyclick. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao Proxyclick seguindo estas instruções:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-proxyclick"></a>Dicas importantes para atribuir usuários ao Proxyclick

* É recomendável que somente um usuário do Azure AD seja atribuído ao Proxyclick para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Proxyclick, é necessário selecionar qualquer função específica ao aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-proxyclick"></a>Configurando o provisionamento automático de usuário no Proxyclick 

Esta seção orienta você pelas etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Proxyclick com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o Proxyclick, seguindo as instruções fornecidas no [Tutorial do logon único do Proxyclick](proxyclick-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-proxyclick-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Proxyclick no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **Proxyclick**.

    ![O link Proxyclick na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções Gerenciar com a opção Provisionamento destacada.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa Modo de Provisionamento com a opção Automático destacada.](common/provisioning-automatic.png)

5. Para recuperar a **URL do Locatário** e o **Token Secreto** de sua conta do Proxyclick, siga o passo a passo descrito na Etapa 6.

6. Entre no [Console de Administração do Proxyclick](https://app.proxyclick.com/login//?destination=%2Fdefault). Navegue até **Configurações** > **Integrações** > **Procurar no Marketplace**.

    ![Configurações do Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick09.png)

    ![Integrações do Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick01.png)

    ![Proxyclick no Marketplace](media/proxyclick-provisioning-tutorial/proxyclick02.png)

    Selecione **Microsoft Azure Active Directory**. Clique em **Instalar agora**.

    ![Proxyclick no Azure AD](media/proxyclick-provisioning-tutorial/proxyclick03.png)

    ![Instalação do Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick04.png)

    Selecione o **Provisionamento de Usuário** e clique em **Iniciar integração**. 

    ![Provisionamento de Usuário do Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick05.png)

    A interface do usuário com as definições de configuração apropriadas deve aparecer em **Configurações** > **Integrações**. Selecione **Configurações** em **Azure AD (Provisionamento de Usuário)** .

    ![Criar no Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick06.png)

    Você pode encontrar a **URL do Locatário** e o **Token Secreto** aqui.

    ![Criar Token no Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick07.png)

7. Ao popular os campos mostrados na Etapa 5, clique em **Testar Conectividade** para garantir que o Azure AD pode se conectar ao Proxyclick. Se a conexão falhar, verifique se a sua conta do Proxyclick tem permissões de Administrador e tente novamente.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

9. Clique em **Save** (Salvar).

10. Na seção **Mapeamentos**, selecione **Sincronizar usuários do Azure Active Directory com o Proxyclick**.

    ![Mapeamentos de Usuário no Proxyclick](media/proxyclick-provisioning-tutorial/Proxyclick-user-mappings.png)

11. Examine os atributos de usuário que serão sincronizados do Azure AD para o Proxyclick na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** serão usados para corresponder as contas de usuário no Proxyclick para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de Usuário do Proxyclick](media/proxyclick-provisioning-tutorial/Proxyclick-user-attribute.png)

13. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Para habilitar o serviço de provisionamento do Azure AD para o Proxyclick, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

15. Defina os usuários e/ou os grupos que deseja provisionar no Proxyclick escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

16. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Proxyclick.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* O Proxyclick requer que **emails** e **userName** tenham o mesmo valor de origem. Todas as atualizações de um dos atributos modificarão o outro valor.
* O Proxyclick não dá suporte ao provisionamento de grupos.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

