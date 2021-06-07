---
title: Criar um locatário na Área de Trabalho Virtual do Windows (clássica) – Azure
description: Descreve como configurar locatários da Área de Trabalho Virtual do Windows (clássica) no Azure Active Directory.
author: Heidilohr
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: b2421c8b8c2d74649084f1cc8b75160857f40fc1
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444319"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop-classic"></a>Tutorial: Criar um locatário na Área de Trabalho Virtual do Windows (clássica)

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows (clássica), que não é compatível com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager.

A criação de um locatário na Área de Trabalho Virtual do Windows é a primeira etapa para a criação de sua solução de virtualização de área de trabalho. Um locatário é um grupo de um ou mais pools de host. Cada pool de hosts consiste em vários hosts de sessão, em execução como máquinas virtuais no Azure e registrados no serviço da Área de Trabalho Virtual do Windows. Cada pool de hosts também consiste em um ou mais grupos de aplicativos que são usados para publicar recursos de aplicativo remoto e área de trabalho remota para usuários. Com um locatário, é possível criar pools de hosts, criar grupos de aplicativos, atribuir usuários e fazer conexões por meio do serviço.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Conceda permissões do Azure Active Directory ao serviço da Área de Trabalho Virtual do Windows.
> * Atribua a função de aplicativo TenantCreator a um usuário em seu locatário do Azure Active Directory.
> * Crie um locatário da Área de Trabalho Virtual do Windows.

## <a name="what-you-need-to-set-up-a-tenant"></a>O que é necessário para configurar um locatário

Antes de começar a configurar seu locatário da Área de Trabalho Virtual do Windows, verifique se você tem estes itens:

* A ID de locatário do [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) para os usuários da Área de Trabalho Virtual do Windows.
* Uma conta de administrador global no locatário do Azure Active Directory.
   * Isso também se aplica às organizações CSP (Provedor de Soluções na Nuvem) que estão criando um locatário da Área de Trabalho Virtual do Windows para seus clientes. Se você é uma organização CSP, deve conseguir entrar como administrador global da instância do Azure Active Directory do cliente.
   * A conta de administrador deve se originar de locatário do Azure Active Directory no qual você está tentando criar o locatário de Área de Trabalho Virtual do Windows. Esse processo não dá suporte a contas do Azure Active Directory B2B (convidado).
   * A conta de administrador deve ser uma conta corporativa ou de estudante.
* Uma assinatura do Azure.

Você precisa ter a ID de locatário, a conta de administrador global e a assinatura do Azure prontas para que o processo descrito neste tutorial possa funcionar corretamente.

## <a name="grant-permissions-to-windows-virtual-desktop"></a>Conceder permissões à Área de Trabalho Virtual do Windows

Se você já concedeu permissões à Área de Trabalho Virtual do Windows para essa instância do Azure Active Directory, ignore esta seção.

A concessão de permissões ao serviço da Área de Trabalho Virtual do Windows permite que ele confira tarefas administrativas e do usuário final no Azure Active Directory.

Para conceder as permissões de serviço:

1. Abra um navegador e inicie o fluxo de consentimento do administrador para o [aplicativo do servidor de Área de Trabalho Virtual do Windows](https://login.microsoftonline.com/common/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback).
   > [!NOTE]
   > Se você gerenciar um cliente e precisar conceder consentimento do administrador para o diretório do cliente, insira a seguinte URL no navegador e substitua {tenant} pelo nome de domínio do Azure AD do cliente. Por exemplo, se a organização do cliente tiver registrado o nome de domínio do Azure AD de contoso.onmicrosoft.com, substitua {tenant} por contoso.onmicrosoft.com.
   >```
   >https://login.microsoftonline.com/{tenant}/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

2. Entre na página de consentimento da Área de Trabalho Virtual do Windows com uma conta de administrador global. Por exemplo, se você fosse entrar com a organização Contoso, sua conta poderia ser admin@contoso.com ou admin@contoso.onmicrosoft.com.
3. Selecione **Aceitar**.
4. Aguarde um minuto para que o Azure AD possa registrar o consentimento.
5. Abra um navegador e inicie o fluxo de consentimento do administrador para o [aplicativo do cliente de Área de Trabalho Virtual do Windows](https://login.microsoftonline.com/common/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback).
   >[!NOTE]
   > Se você gerenciar um cliente e precisar conceder consentimento do administrador para o diretório do cliente, insira a seguinte URL no navegador e substitua {tenant} pelo nome de domínio do Azure AD do cliente. Por exemplo, se a organização do cliente tiver registrado o nome de domínio do Azure AD de contoso.onmicrosoft.com, substitua {tenant} por contoso.onmicrosoft.com.
   >```
   > https://login.microsoftonline.com/{tenant}/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

6. Entre na página de consentimento da Área de Trabalho Virtual do Windows como administrador global, como fez na etapa 2.
7. Selecione **Aceitar**.

## <a name="assign-the-tenantcreator-application-role"></a>Atribuir a função de aplicativo TenantCreator

A atribuição de um usuário do Azure Active Directory à função de aplicativo TenantCreator permite que o usuário crie um locatário da Área de Trabalho Virtual do Windows associado à instância do Azure Active Directory. Você precisará usar sua conta de administrador global para atribuir a função TenantCreator.

Para atribuir a função de aplicativo TenantCreator:

1. Vá para o [portal do Azure](https://portal.azure.com) para gerenciar a função de aplicativo TenantCreator. Pesquise por **Aplicativos empresariais** e selecione essa opção. Se você está trabalhando com vários locatários do Azure Active Directory, é uma prática recomendada abrir uma sessão privada do navegador e copiar e colar as URLs na barra de endereços.

   > [!div class="mx-imgBorder"]
   > ![Captura de tela da pesquisa por Aplicativos empresariais no portal do Azure](../media/azure-portal-enterprise-applications.png)

2. Em **Aplicativos Empresariais**, pesquise **Área de Trabalho Virtual do Windows**. Você verá os dois aplicativos para os quais forneceu consentimento na seção anterior. Desses dois aplicativos, selecione **Área de Trabalho Virtual do Windows**.

   > [!div class="mx-imgBorder"]
   > ![Uma captura de tela dos resultados da pesquisa ao pesquisar "Área de Trabalho Virtual do Windows" em "Aplicativos empresariais". O aplicativo chamado "Área de Trabalho Virtual do Windows" está realçado.](../media/tenant-enterprise-app.png)

3. Selecione **Usuários e grupos**. Você pode ver que o administrador que deu consentimento ao aplicativo já está listado com a função **Acesso Padrão** atribuída. Isso não é suficiente para criar um locatário da Área de Trabalho Virtual do Windows. Continue a seguir estas instruções para adicionar a função **TenantCreator** a um usuário.

   > [!div class="mx-imgBorder"]
   > ![Captura de tela dos usuários e grupos designados para gerenciar o aplicativo corporativo "Área de Trabalho Virtual do Windows". A captura de tela mostra apenas uma atribuição, que é de "Acesso Padrão".](../media/tenant-default-access.png)

4. Selecione **+ Adicionar usuário** e, em seguida, selecione **Usuários e grupos** na guia **Adicionar Atribuição**.
5. Pesquise uma conta de usuário que criará o locatário da Área de Trabalho Virtual do Windows. Para simplificar, isso pode ser a conta de administrador global.
   - Se você estiver usando um provedor de identidade da Microsoft, como contosoadmin@live.com ou contosoadmin@outlook.com, talvez não consiga entrar na Área de Trabalho Virtual do Windows. É recomendável usar uma conta específica do domínio, como admin@contoso.com ou admin@contoso.onmicrosoft.com.

   > [!div class="mx-imgBorder"]
   > ![Captura de tela da seleção de um usuário para adicionar como "TenantCreator".](../media/tenant-assign-user.png)

   > [!NOTE]
   > É preciso selecionar um usuário (ou um grupo que contém um usuário) originário desta instância do Azure Active Directory. Não é possível escolher um usuário convidado (B2B) ou uma entidade de serviço.

6. Selecione a conta de usuário, escolha o botão **Selecionar** e, em seguida, selecione **Atribuir**.
7. Na página **Área de Trabalho Virtual do Windows – Usuários e Grupos**, verifique se há uma nova entrada com a função **TenantCreator** atribuída ao usuário que criará o locatário da Área de Trabalho Virtual do Windows.

   > [!div class="mx-imgBorder"]
   > ![Captura de tela dos usuários e grupos designados para gerenciar o aplicativo corporativo "Área de Trabalho Virtual do Windows". A captura de tela agora inclui uma segunda entrada de um usuário atribuído à função "TenantCreator".](../media/tenant-tenant-creator-added.png)

Antes de continuar a criar seu locatário da Área de Trabalho Virtual do Windows, você precisará de duas informações:

   - ID de locatário do Azure Active Directory (ou **ID de diretório**)
   - ID da assinatura do Azure

Para localizar sua ID de locatário do Azure Active Directory (ou **ID de diretório**):
1. Na mesma sessão do [portal do Azure](https://portal.azure.com), procure pelo **Azure Active Directory** e selecione-o.

   > [!div class="mx-imgBorder"]
   > ![Captura de tela dos resultados da pesquisa para "Azure Active Directory" no portal do Azure. O resultado da pesquisa em "Serviços" está destacado.](../media/tenant-search-azure-active-directory.png)

2. Role para baixo até encontrar **Propriedades** e, em seguida, selecione essa opção.
3. Procure a **ID de diretório** e selecione o ícone da área de transferência. Cole-o em um local prático para poder usá-lo posteriormente como o valor **AadTenantId**.

   > [!div class="mx-imgBorder"]
   > ![Captura de tela das propriedades do Azure Active Directory. O mouse passando sobre o ícone da área de transferência para o "ID de diretório" para copiar e colar.](../media/tenant-directory-id.png)

Para localizar sua ID da assinatura do Azure:
1. Na mesma sessão do [portal do Azure](https://portal.azure.com), procure por **Assinaturas** e selecione essa opção.

   > [!div class="mx-imgBorder"]
   > ![Captura de tela dos resultados da pesquisa para "Azure Active Directory" no portal do Azure. O resultado da pesquisa por "Serviços" está destacado.](../media/tenant-search-subscription.png)

2. Selecione a assinatura do Azure que você quer usar para receber as notificações de serviço da Área de Trabalho Virtual do Windows.
3. Procure **ID da assinatura** e passe o mouse sobre o valor até que um ícone da área de transferência seja exibido. Selecione o ícone da área de transferência e cole-o em um local prático para poder usá-lo posteriormente como o valor **AzureSubscriptionId**.

   > [!div class="mx-imgBorder"]
   > ![Captura de tela das propriedades da assinatura do Azure. O mouse passando sobre o ícone da área de transferência para o "ID da assinatura" para copiar e colar.](../media/tenant-subscription-id.png)

## <a name="create-a-windows-virtual-desktop-tenant"></a>Criar um locatário da Área de Trabalho Virtual do Windows

Agora que você recebeu as permissões de serviço da Área de Trabalho Virtual do Windows para consultar o Azure Active Directory e atribuiu a função TenantCreator a uma conta de usuário, crie um locatário da Área de Trabalho Virtual do Windows.

Primeiro, [baixe e importe o módulo da Área de Trabalho Virtual do Windows](/powershell/windows-virtual-desktop/overview/) para usá-lo na sessão do PowerShell, caso ainda não tenha feito isso.

Entre na Área de Trabalho Virtual do Windows usando a conta de usuário TenantCreator com este cmdlet:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Depois disso, crie um locatário da Área de Trabalho Virtual do Windows associado ao locatário do Azure Active Directory:

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

Substitua os valores entre colchetes pelos valores relevantes à sua organização e ao locatário. O nome escolhido para o novo locatário da Área de Trabalho Virtual do Windows deve ser globalmente exclusivo. Por exemplo, digamos que você seja o TenantCreator da Área de Trabalho Virtual do Windows para a organização Contoso. O cmdlet que você executaria seria semelhante a este:

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

É uma boa ideia atribuir o acesso administrativo a um segundo usuário no caso de você ser bloqueado de sua conta ou estar de férias e precisar de alguém para atuar como administrador de locatários em sua ausência. Para atribuir o acesso de administrador a um segundo usuário, execute o cmdlet a seguir com `<TenantName>` e `<Upn>` substituído pelo nome do locatário e pelo UPN do segundo usuário.

```powershell
New-RdsRoleAssignment -TenantName <TenantName> -SignInName <Upn> -RoleDefinitionName "RDS Owner"
```

## <a name="next-steps"></a>Próximas etapas
Depois de criar seu locatário, você precisará criar uma entidade de serviço no Azure Active Directory e atribuir a ela uma função dentro da Área de Trabalho Virtual do Windows. A entidade de serviço permitirá que você implante com êxito a oferta do Azure Marketplace da Área de Trabalho Virtual do Windows para criar um pool de host. Para saber mais sobre os pools de host, continue com o tutorial para criar um pool de hosts na Área de Trabalho Virtual do Windows.

> [!div class="nextstepaction"]
> [Criar entidades de serviço e atribuições de função com o PowerShell](create-service-principal-role-powershell.md)
