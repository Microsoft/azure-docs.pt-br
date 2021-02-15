---
title: Implantar uma ferramenta de gerenciamento para a área de trabalho virtual do Windows (clássica) usando a entidade de serviço-Azure
description: Como implantar a ferramenta de gerenciamento para a área de trabalho virtual do Windows (clássica) usando o PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: lizross
ms.openlocfilehash: d7219751d584eb458cded9f4e30cccb1439dfa1b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89069010"
---
# <a name="deploy-a-windows-virtual-desktop-classic-management-tool-with-powershell"></a>Implantar uma ferramenta de gerenciamento de área de trabalho virtual do Windows (clássico) com o PowerShell

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows (clássica), que não é compatível com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager.

Este artigo mostrará como implantar a ferramenta de gerenciamento usando o PowerShell.

## <a name="important-considerations"></a>Considerações importantes

Cada assinatura de locatário do Azure Active Directory (AD do Azure) precisa de sua própria implantação separada da ferramenta de gerenciamento. Esta ferramenta não dá suporte a cenários B2B (Business-to-Business) do Azure AD.

Esta ferramenta de gerenciamento é uma amostra. A Microsoft fornecerá atualizações de qualidade e de segurança importantes. [O código-fonte está disponível no GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Seja você um cliente ou parceiro, incentivamos a personalização da ferramenta para atender às suas necessidades de negócios.

Os navegadores a seguir são compatíveis com a ferramenta de gerenciamento do:

- Google Chrome 68 ou posterior
- Microsoft Edge 40.15063 ou posterior
- Mozilla Firefox 52.0 ou posterior
- Safari 10 ou posterior (somente macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>O que você precisa para implantar a ferramenta de gerenciamento

Antes de implantar a ferramenta de gerenciamento, você precisará que um usuário do Azure AD (Azure Active Directory) crie um registro de aplicativo e implante a interface do usuário de gerenciamento. Esse usuário precisa:

- Ter permissão para criar recursos em sua assinatura do Azure
- Ter permissão para criar um aplicativo do Azure AD. Siga estas etapas para verificar se o usuário tem as permissões necessárias, seguindo as instruções em [Permissões necessárias](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

Depois de implantar e configurar a ferramenta de gerenciamento, é recomendável solicitar que um usuário inicie a interface do usuário de gerenciamento para verificar se tudo está funcionando. O usuário que inicia a interface do usuário de gerenciamento deve ter uma atribuição de função que permita exibir ou editar o locatário da Área de Trabalho Virtual do Windows.

## <a name="set-up-powershell"></a>Configurar o PowerShell

Comece entrando no módulo do PowerShell AZ e Azure AD. Veja como entrar:

1. Abra o PowerShell como administrador e navegue até o diretório em que você salvou os scripts do PowerShell.
2. Entre no Azure com uma conta que tenha permissões de proprietário ou colaborador na assinatura do Azure que você planeja usar para criar a ferramenta de gerenciamento executando o seguinte cmdlet:

    ```powershell
    Login-AzAccount
    ```

3. Execute o seguinte cmdlet para entrar no Azure AD com a mesma conta usada para o módulo AZ PowerShell:

    ```powershell
    Connect-AzureAD
    ```

4. Depois disso, navegue até a pasta em que você salvou os dois scripts do PowerShell do repositório GitHub RDS-Templates.

Mantenha a janela do PowerShell que você usou para entrar em aberto para executar cmdlets do PowerShell adicionais enquanto estiver conectado.

## <a name="create-an-azure-active-directory-app-registration"></a>Criar um registro de aplicativo Azure Active Directory

Para implantar e configurar com êxito a ferramenta de gerenciamento, primeiro você precisa baixar os seguintes scripts do PowerShell do repositório do [GitHub de modelos de RDS](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy/scripts)

```powershell
Set-Location -Path "c:\temp"
$uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/scripts/createWvdMgmtUxAppRegistration.ps1"
Invoke-WebRequest -Uri $uri -OutFile ".\createWvdMgmtUxAppRegistration.ps1"
$uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/scripts/updateWvdMgmtUxApiUrl.ps1"
Invoke-WebRequest -Uri $uri -OutFile ".\updateWvdMgmtUxApiUrl.ps1"
```

Execute os seguintes comandos para criar o registro do aplicativo com as permissões de API necessárias:

```powershell
$appName = Read-Host -Prompt "Enter a unique name for the management tool's app registration. The name can't contain spaces or special characters."
$azureSubscription = Get-AzSubscription | Out-GridView -PassThru
$subscriptionId = $azureSubscription.Id
Get-AzSubscription -SubscriptionId $subscriptionId | Select-AzSubscription

.\createWvdMgmtUxAppRegistration.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Agora que você concluiu o registro do aplicativo do Azure AD, você pode implantar a ferramenta de gerenciamento.

## <a name="deploy-the-management-tool"></a>Implantar a ferramenta de gerenciamento

Execute os seguintes comandos do PowerShell para implantar a ferramenta de gerenciamento e associá-la à entidade de serviço que você acabou de criar:

```powershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateParameters = @{}
$templateParameters.Add('isServicePrincipal', $true)
$templateParameters.Add('azureAdminUserPrincipalNameOrApplicationId', $ServicePrincipalCredentials.UserName)
$templateParameters.Add('azureAdminPassword', $servicePrincipalCredentials.Password)
$templateParameters.Add('applicationName', $appName)

Get-AzSubscription -SubscriptionId $subscriptionId | Select-AzSubscription
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/mainTemplate.json" `
    -TemplateParameterObject $templateParameters `
    -Verbose
```

Depois de criar o aplicativo Web, você deve adicionar um URI de redirecionamento ao aplicativo do Azure AD para conectar com êxito os usuários.

## <a name="set-the-redirect-uri"></a>Definir o URI de redirecionamento

Execute os comandos do PowerShell a seguir para recuperar a URL do aplicativo Web e defini-la como o URI de redirecionamento de autenticação (também chamado de URL de resposta):

```powershell
$webApp = Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $appName
$redirectUri = "https://" + $webApp.DefaultHostName + "/"
Get-AzureADApplication -All $true | where { $_.AppId -match $servicePrincipalCredentials.UserName } | Set-AzureADApplication -ReplyUrls $redirectUri
```

Agora que você adicionou um URI de redirecionamento, você precisa atualizar a URL da API para que a ferramenta de gerenciamento possa interagir com o serviço de back-end de API.

## <a name="update-the-api-url-for-the-web-application"></a>Atualizar a URL da API para o aplicativo Web

Execute o script a seguir para atualizar a configuração de URL da API no front-end do aplicativo Web:

```powershell
.\updateWvdMgmtUxApiUrl.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Agora que você configurou totalmente o aplicativo Web da ferramenta de gerenciamento, é hora de verificar o aplicativo do Azure AD e fornecer consentimento.

## <a name="verify-the-azure-ad-application-and-provide-consent"></a>Verificar o aplicativo Azure AD e fornecer consentimento

Para verificar a configuração de aplicativo do Azure AD e fornecer consentimento:

1. Abra seu navegador da Internet e entre no [portal do Azure](https://portal.azure.com/) com sua conta administrativa.
2. Na barra de pesquisa na parte superior da portal do Azure, procure **registros de aplicativo** e selecione o item em **Serviços**.
3. Selecione **todos os aplicativos** e pesquise o nome exclusivo do aplicativo fornecido para o script do PowerShell em [criar um Azure Active Directory registro de aplicativo](#create-an-azure-active-directory-app-registration).
4. No painel no lado esquerdo do navegador, selecione **autenticação** e verifique se o URI de redirecionamento é o mesmo da URL do aplicativo Web para a ferramenta de gerenciamento, conforme mostrado na imagem a seguir.

   [![A página de autenticação com o URI ](../media/management-ui-redirect-uri-inline.png) de redirecionamento inserido](../media/management-ui-redirect-uri-expanded.png#lightbox)

5. No painel esquerdo, selecione **permissões de API** para confirmar que as permissões foram adicionadas. Se você for um administrador global, selecione o botão **conceder consentimento de `tenantname` administrador para** e siga os prompts de diálogo para fornecer consentimento de administrador para sua organização.

    [![A página ](../media/management-ui-permissions-inline.png) permissões de API](../media/management-ui-permissions-expanded.png#lightbox)

Agora você pode começar a usar a ferramenta de gerenciamento.

## <a name="use-the-management-tool"></a>Usar a ferramenta de gerenciamento

Agora que você configurou a ferramenta de gerenciamento a qualquer momento, poderá iniciá-la a qualquer momento, em qualquer lugar. Veja como iniciar a ferramenta:

1. Abra a URL do aplicativo Web em um navegador da Web. Se você não se lembrar da URL, poderá entrar no Azure, localizar o serviço de aplicativo implantado para a ferramenta de gerenciamento e, em seguida, selecionar a URL.
2. Entre usando suas credenciais da Área de Trabalho Virtual do Windows.

   > [!NOTE]
   > Se você não concedeu consentimento de administrador ao configurar a ferramenta de gerenciamento, cada usuário que se conectar precisará fornecer seu próprio consentimento de usuário para usar a ferramenta.

3. Quando for solicitado a escolher um grupo de locatários, selecione **grupo de locatários padrão** na lista suspensa.
4. Quando você seleciona **Grupo de Locatários Padrão**, um menu deve ser exibido no lado esquerdo da janela. Nesse menu, encontre o nome do seu grupo de locatários e selecione-o.

   > [!NOTE]
   > Se tiver um grupo de locatários personalizado, digite o nome manualmente em vez de escolher na lista suspensa.

## <a name="report-issues"></a>Relatar problemas

Caso tenha problemas com a ferramenta de gerenciamento ou outras ferramentas da Área de Trabalho Virtual do Windows, siga as instruções em [Modelos do Azure Resource Manager para Serviços de Área de Trabalho Remota](https://github.com/Azure/RDS-Templates/blob/master/README.md) para relatá-los no GitHub.

## <a name="next-steps"></a>Próximas etapas

Agora que aprendeu como implantar a ferramenta de gerenciamento e conectar-se a ela, você pode aprender a usar a Integridade do Serviço do Azure para monitorar problemas de serviço e avisos de integridade. Para saber mais, confira o tutorial [Configurar alertas de serviço](set-up-service-alerts-2019.md).
