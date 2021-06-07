---
title: 'Tutorial: Configurar o Federated Directory para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no Federated Directory.
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
ms.openlocfilehash: 8ca7654d930247f70d85cbc20fbbeb961223f05f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95998355"
---
# <a name="tutorial-configure-federated-directory-for-automatic-user-provisioning"></a>Tutorial: Configurar o Federated Directory para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Federated Directory e no Azure AD (Active Directory) a fim de configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no Federated Directory.

> [!NOTE]
>  Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD.
* [Um Federated Directory](https://www.federated.directory/pricing).
* Uma conta de usuário no Federated Directory com permissões de Administrador.

## <a name="assign-users-to-federated-directory"></a>Atribuir usuários ao Federated Directory
O Azure Active Directory usa um conceito chamado atribuições para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou os grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Para configurar e habilitar o provisionamento automático de usuário, decida quais usuários e/ou grupos no Azure AD precisam de acesso ao Federated Directory. Depois de decidir isso, você poderá atribuir esses usuários e/ou grupos ao Federated Directory seguindo estas instruções:

 * [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md) 
 
 ## <a name="important-tips-for-assigning-users-to-federated-directory"></a>Dicas importantes para atribuir usuários ao Federated Directory
 * Recomendamos que um só usuário do Azure AD seja atribuído ao Federated Directory para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Federated Directory, você precisará selecionar qualquer função válida específica do aplicativo (se disponível) na caixa de diálogo de atribuição. Usuários com a função Acesso padrão são excluídos do provisionamento.
    
 ## <a name="set-up-federated-directory-for-provisioning"></a>Configurar o Federated Directory para provisionamento

Antes de configurar o Federated Directory para o provisionamento automático de usuário com o Azure AD, você precisará habilitar o provisionamento do SCIM no Federated Directory.

1. Entre no [Console de Administração do Federated Directory](https://federated.directory/of)

    :::image type="content" source="media/federated-directory-provisioning-tutorial/companyname.png" alt-text="Captura de tela do console de administração do Federated Directory mostrando um campo para inserção de um nome de empresa. Os botões de entrada também estão visíveis." border="false":::

2. Procure **Diretórios > Diretórios de usuário** e selecione seu locatário. 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/ad-user-directories.png" alt-text="Captura de tela do console de administração do Federated Directory, com as opções Diretórios e Teste do Federated Directory e do Azure AD realçadas." border="false":::

3.  Para gerar um token de portador permanente, procure **Chaves de Diretório > Criar Chave.** 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated01.png" alt-text="Captura de tela da página Chaves de diretório do console de administração do Federated Directory. O botão Criar chave está realçado." border="false":::

4. Crie uma chave de diretório. 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated02.png" alt-text="Captura de tela da página Criar chave de diretório do console de administração do Federated Directory, com os campos Nome e Descrição e um botão Criar chave." border="false":::
    

5. Copie o valor de **Token de Acesso**. Esse valor será inserido no campo **Token Secreto** na guia Provisionamento do aplicativo Federated Directory no portal do Azure. 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated03.png" alt-text="Captura de tela de uma página no console de administração do Federated Directory. Um espaço reservado de token de acesso e um nome de chave, uma descrição e um emissor são visíveis." border="false":::
    
## <a name="add-federated-directory-from-the-gallery"></a>Adicionar o Federated Directory por meio da galeria

Para configurar o Federated Directory para o provisionamento automático de usuário com o Azure AD, é necessário adicionar o Federated Directory por meio da galeria de aplicativos do Azure AD à lista de aplicativos SaaS gerenciados.

**Para adicionar o Federated Directory por meio da galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Federated Directory** e selecione **Federated Directory** no painel de resultados.

    ![Federated Directory na lista de resultados](common/search-new-app.png)

5. Navegue até a **URL** realçada abaixo em um navegador separado. 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/loginpage1.png" alt-text="Captura de tela de uma página no portal do Azure que exibe informações sobre o Federated Directory. O valor da URL está realçado." border="false":::

6. Clique em **FAZER LOGON**.

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated04.png" alt-text="Captura de tela do menu principal no site do Federated Directory. O botão Fazer logon está realçado." border="false":::

7.  Como o Federated Directory é um aplicativo OpenIDConnect, opte por fazer logon no Federated Directory usando sua conta corporativa da Microsoft.
    
    :::image type="content" source="media/federated-directory-provisioning-tutorial/loginpage3.png" alt-text="Captura de tela da página de teste do SCIM e do AD no site do Federated Directory. A opção Faça logon com a sua conta Microsoft está realçada." border="false":::
 
8. Após uma autenticação bem-sucedida, aceite a solicitação de consentimento da página de consentimento. Depois, o aplicativo será adicionado automaticamente ao seu locatário e você será redirecionado para sua conta do Federated Directory.

    ![Adicionar o SCIM no Federated Directory](media/federated-directory-provisioning-tutorial/premission.png)



## <a name="configuring-automatic-user-provisioning-to-federated-directory"></a>Como configurar o provisionamento automático de usuário no Federated Directory 

Esta seção descreve as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Federated Directory com base em atribuições de usuário e/ou de grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-federated-directory-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Federated Directory no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Federated Directory**.

    ![O link do Federated Directory na lista Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções Gerenciar com a opção Provisionamento destacada.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa Modo de Provisionamento com a opção Automático destacada.](common/provisioning-automatic.png)

5. Na seção **Credenciais de Administrador**, insira `https://api.federated.directory/v2/` na URL do locatário. Insira o valor recuperado e salvo anteriormente do Federated Directory em **Token Secreto**. Clique em **Testar Conectividade** para verificar se o Azure AD pode se conectar ao Federated Directory. Se a conexão falhar, verifique se a sua conta do Federated Directory tem permissões de Administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

9. Clique em **Save** (Salvar).

10. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Federated Directory**.

    :::image type="content" source="media/federated-directory-provisioning-tutorial/user-mappings.png" alt-text="Captura de tela da seção Mapeamentos. Em Nome, a opção Sincronizar Usuários do Azure Active Directory com o Federated Directory está realçada." border="false":::
    
    
11. Examine os atributos de usuário que serão sincronizados do Azure AD para o Federated Directory na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das contas de usuário no Federated Directory em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    :::image type="content" source="media/federated-directory-provisioning-tutorial/user-attributes.png" alt-text="Captura de tela da página Mapeamentos de Atributos. Uma tabela lista os atributos do Azure Active Directory e do Federated Directory, bem como o status de correspondência." border="false":::
    

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD no Federated Directory, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou os grupos que deseja provisionar no Federated Directory escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para o relatório das atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Federated Directory.

Para obter mais informações sobre como ler os logs de provisionamento do Azure AD, confira [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md)
## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
