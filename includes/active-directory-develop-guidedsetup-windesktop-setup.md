---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: ae6d590cdada24638ec2d24c83609b8e6addfaf0
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172097"
---
## <a name="set-up-your-project"></a>Configurar o seu projeto

Nesta seção, você cria um novo projeto para demonstrar como integrar um aplicativo .NET da Área de Trabalho do Windows (XAML) com a opção *Entrar com uma Conta da Microsoft*, de forma que o aplicativo possa consultar APIs Web que exigem um token.

O aplicativo que você criar com este guia exibe um botão que é usado para chamar um gráfico, uma área para mostrar os resultados na tela e um botão de saída.

> [!NOTE]
> Prefere baixar este projeto do Visual Studio de exemplo? [Baixe um projeto](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip) e vá para a [Etapa de configuração](#register-your-application) para configurar o exemplo de código antes de executá-lo.
>

Para criar seu aplicativo, faça o seguinte:

1. No Visual Studio, selecione **Arquivo** > **Novo** > **Projeto**.
2. Em **Modelos**, selecione **Visual C#** .
3. Selecione **aplicativo WPF (.NET Framework)** , dependendo da versão do Visual Studio que você estiver usando.

## <a name="add-msal-to-your-project"></a>Adicionar MSAL ao seu projeto

1. No Visual Studio, selecione **Ferramentas** > **Gerenciador de Pacotes do NuGet**> **Console do Gerenciador de Pacotes**.
2. Na janela do Console do Gerenciador de Pacotes, cole o seguinte comando do Azure PowerShell:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE] 
    > Esse comando instala a Biblioteca de Autenticação da Microsoft. A MSAL lida com a aquisição, o armazenamento em cache e a atualização de tokens de usuário usados para acessar APIs protegidas pelo Azure Active Directory v2.0
    >

## <a name="add-the-code-to-initialize-msal"></a>Adicionar o código para inicializar a MSAL

Nesta etapa, você cria uma classe para lidar com a interação com a MSAL, por exemplo, com a manipulação de tokens.

1. Abra o arquivo *App.xaml.cs* e adicione a referência para a MSAL à classe:

    ```csharp
    using Microsoft.Identity.Client;
    ```
   <!-- Workaround for Docs conversion bug -->

2. Atualize a classe app para o seguinte:

    ```csharp
    public partial class App : Application
    {
        static App()
        {
            _clientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AzureCloudInstance.AzurePublic, Tenant)
                .Build();
        }

        // Below are the clientId (Application Id) of your app registration and the tenant information. 
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - the content of Tenant by the information about the accounts allowed to sign-in in your application:
        //   - For Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use `organizations`
        //   - for any Work or School accounts, or Microsoft personal account, use `common`
        //   - for Microsoft Personal account, use consumers
        private static string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";

        private static string Tenant = "common";

        private static IPublicClientApplication _clientApp ;

        public static IPublicClientApplication PublicClientApp { get { return _clientApp; } }
    }
    ```

## <a name="create-the-application-ui"></a>Criar a interface do usuário do aplicativo

Esta seção mostra como um aplicativo pode consultar um servidor de back-end protegido como o Microsoft Graph. 

Um arquivo *MainWindow.xaml* deve ser criado automaticamente como parte de seu modelo de projeto. Abra esse arquivo e, em seguida, substitua o nó *\<Grade>* de seu aplicativo pelo código a seguir:

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <Label Content="API Call Results" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <Label Content="Token Info" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```
