---
title: 'Tutorial: Configurar o Cisco Webex para o provisionamento automático de usuários com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e descontinuar automaticamente as contas de usuário para o Cisco Webex.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: bc05e83ac6c7f0f7c5e9a571c1fa7397af858f44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96179989"
---
# <a name="tutorial-configure-cisco-webex-for-automatic-user-provisioning"></a>Tutorial: Configurar o Cisco Webex para provisionamento automático de usuários

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Cisco Webex e no Microsoft Azure AD (Azure Active Directory) para configurar o Azure AD para provisionar e desprovisionar usuários automaticamente para o Cisco Webex.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD.
* [Um locatário do Cisco Webex](https://www.webex.com/pricing/index.html).
* Uma conta de usuário no Cisco Webex com permissões de administrador.

## <a name="adding-cisco-webex-from-the-gallery"></a>Adicionando o Cisco Webex da galeria

Antes de configurar o Cisco Webex para o provisionamento automático de usuários com o Microsoft Azure Active Directory, você precisa adicionar o Cisco Webex da galeria de aplicativos do Microsoft Azure Active Directory à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Cisco Webex da galeria de aplicativos do Microsoft Azure Active Directory, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Cisco Webex**, selecione **Cisco Webex** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Cisco Webex na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-cisco-webex"></a>Atribuindo usuários ao Cisco Webex

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Microsoft Azure AD são sincronizados.

Antes de configurar e ativar o provisionamento automático de usuários, você deve decidir quais usuários do Microsoft Azure Active Directory precisam acessar o Cisco Webex. Uma vez decidido, você pode atribuir esses usuários ao Cisco Webex seguindo as instruções aqui:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-webex"></a>Dicas importantes para atribuir usuários ao Cisco Webex

* Recomenda-se que um único usuário do Microsoft Azure Active Directory seja atribuído ao Cisco Webex para testar a configuração automática de provisionamento de usuários. Outros usuários podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Cisco Webex, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-cisco-webex"></a>Configurando o Fornecimento Automático de Usuários para o Cisco Webex

Esta seção o guia pelas etapas para configurar o serviço de provisionamento do Microsoft Azure Active Directory para criar, atualizar e desabilitar usuários no Cisco Webex com base nas atribuições de usuários no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-cisco-webex-in-azure-ad"></a>Para configurar o provisionamento automático de usuários para o Cisco Webex no Microsoft Azure Active Directory:

1. Entre no [portal do Azure](https://portal.azure.com), selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Cisco Webex**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **Cisco Webex**.

    ![O link do Cisco Webex na lista de Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    :::image type="content" source="common/provisioning.png" alt-text="Captura de tela de um menu no portal do Azure. Em Gerenciar, o Provisionamento é realçado." border="false":::

4. Defina o **Modo de Provisionamento** como **Automático**.

    :::image type="content" source="common/provisioning-automatic.png" alt-text="Captura de tela da caixa de listagem do Modo de provisionamento, com Automático realçado." border="false":::

5. Na seção **Credenciais de administrador**, insira o **URL do locatário** e **Token secreto** da conta do seu Cisco Webex.

    :::image type="content" source="./media/cisco-webex-provisioning-tutorial/secrettoken1.png" alt-text="Captura de tela da seção Credenciais de Administrador. As caixas URL do Locatário e Token Secreto estão realçadas, mas estão vazias." border="false":::

6.  No campo **URL do Locatário**, insira um valor na forma de `https://api.ciscospark.com/v1/scim/[OrgId]`. Para obter `[OrgId]`, entre em seu [Hub de Controle do Cisco Webex](https://admin.webex.com/login). Clique no nome de sua organização na parte inferior esquerda e copie o valor de **ID da Organização**. 

    * Para obter o valor do **Token Secreto**, navegue até esta [URL](https://idbroker.webex.com/idb/saml2/jsp/doSSO.jsp?type=login&goto=https%3A%2F%2Fidbroker.webex.com%2Fidb%2Foauth2%2Fv1%2Fauthorize%3Fresponse_type%3Dtoken%26client_id%3DC4ca14fe00b0e51efb414ebd45aa88c1858c3bfb949b2405dba10b0ca4bc37402%26redirect_uri%3Dhttp%253A%2f%2flocalhost%253A3000%2fauth%2fcode%26scope%3Dspark%253Apeople_read%2520spark%253Apeople_write%2520Identity%253ASCIM%26state%3Dthis-should-be-a-random-string-for-security-purpose). Na página de entrada do WebEx que aparece, entre com a conta do administrador completa do Cisco WebEx para sua organização. Uma página de erro aparece dizendo que o site não pode ser acessado, mas isso é normal.

        :::image type="content" source="./media/cisco-webex-provisioning-tutorial/test.png" alt-text="Captura de tela de uma página exibindo uma mensagem de erro. A mensagem informa que o site não pode ser acessado e inclui algumas dicas de solução de problemas." border="false":::
 
    * Copie o valor do token de portador gerado da URL, conforme realçado abaixo. Esse token é válido por 365 dias.
        
        :::image type="content" source="./media/cisco-webex-provisioning-tutorial/test1.png" alt-text="Captura de tela mostrando uma URL longa. Parte do endereço é indecifrável, mas está realçado e rotulado como Token de portador." border="false":::

7. Ao preencher os campos mostrados na Etapa 5, clique em **Testar Conexão** para garantir que o Microsoft Azure Active Directory possa se conectar ao Cisco Webex. Se a conexão falhar, certifique-se de que sua conta do Cisco Webex tenha permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)
   
8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

9. Clique em **Save** (Salvar).

10. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Microsoft Azure Active Directory para o Cisco Webex**.

    :::image type="content" source="./media/cisco-webex-provisioning-tutorial/usermapping.png" alt-text="Captura de tela da seção Mapeamentos no portal do Azure. Em Nome, Sincronizar Usuários do Azure Active Directory com o CiscoSpark está realçado." border="false":::

11. Revise os atributos do usuário sincronizados do Microsoft Azure Active Directory para o Cisco Webex na seção **Mapeamento de Atributos**. Os atributos selecionados como **Matching** são usados para corresponder às contas de usuário no Cisco Webex para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    :::image type="content" source="./media/cisco-webex-provisioning-tutorial/usermappingattributes.png" alt-text="Captura de tela da seção Mapeamentos de Atributo mostrando os atributos do Azure Active Directory, os atributos CiscoSpark correspondentes e o status correspondente." border="false":::

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Microsoft Azure Active Directory para o Cisco Webex, altere o **Status de provisionamento** para **ativar** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e / ou grupos que você deseja provisionar para o Cisco Webex escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **Detalhes da Sincronização** para monitorar o andamento e seguir os links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Microsoft Azure Active Directory no Cisco Webex.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* O Cisco Webex está atualmente na fase Early Field Testing (EFT) da Cisco. Para obter mais informações, entre em contato com [equipe de suporte do Cisco](https://www.webex.co.in/support/support-overview.html). 
* Para obter mais informações sobre a configuração do Cisco WebEx, veja a documentação da Cisco [aqui](https://help.webex.com/en-us/aumpbz/Synchronize-Azure-Active-Directory-Users-into-cisco-webex-Control-Hub).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
