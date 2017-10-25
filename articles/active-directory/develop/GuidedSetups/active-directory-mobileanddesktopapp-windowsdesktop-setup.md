---
title: "Introdução à Área de Trabalho do Windows no Azure AD v2 – Instalação | Microsoft Docs"
description: "Como os aplicativos .NET da Área de Trabalho do Windows (XAML) podem chamar uma API que exige tokens de acesso pelo ponto de extremidade do Azure Active Directory v2"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 4065727aef04d7969d438c6ef79127bb44568be1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
## <a name="set-up-your-project"></a>Configurar o seu projeto

Esta seção fornece instruções passo a passo sobre como criar um novo projeto para demonstrar como integrar um aplicativo .NET da Área de Trabalho do Windows (XAML) com a opção *Entrar com uma Conta da Microsoft*, de forma que ele possa consultar APIs Web que exigem um token.

O aplicativo criado por este guia expõe um botão para o gráfico e mostra os resultados na tela e um botão de saída.

> Prefere baixar este projeto do Visual Studio de exemplo? [Baixe um projeto](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/master.zip) e vá para a [Etapa de configuração](#create-an-application-express) para configurar o exemplo de código antes de executá-lo.


### <a name="create-your-application"></a>Criar o aplicativo
1. No Visual Studio: `File` > `New` > `Project`<br/>
2. Em *Modelos*, selecione `Visual C#`
3. Selecione `WPF App` (ou *Aplicativo WPF* dependendo da versão do Visual Studio)

## <a name="add-the-microsoft-authentication-library-msal-to-your-project"></a>Adicionar a MSAL (Biblioteca de Autenticação da Microsoft) ao projeto
1. No Visual Studio: `Tools` > `Nuget Package Manager` > `Package Manager Console`
2. Copie e cole o seguinte na janela do Console do Gerenciador de Pacotes:

```powershell
Install-Package Microsoft.Identity.Client -Pre
```

> O pacote acima instala a MSAL (Biblioteca de Autenticação da Microsoft). A MSAL manipula a aquisição, o armazenamento em cache e a atualização de tokens de usuário usados para acessar APIs protegidas pelo Azure Active Directory v2.

## <a name="add-the-code-to-initialize-msal"></a>Adicionar o código para inicializar a MSAL
Esta etapa ajudará você a criar uma classe para manipular a interação com a Biblioteca MSAL, como a manipulação de tokens.

1. Abra o arquivo `App.xaml.cs` e adicione a referência da biblioteca MSAL à classe:

```csharp
using Microsoft.Identity.Client;
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Atualize a classe App para o seguinte:
</li>
</ol>

```csharp
public partial class App : Application
{
    //Below is the clientId of your app registration. 
    //You have to replace the below with the Application Id for your app registration
    private static string ClientId = "your_client_id_here";

    public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);

}
```

## <a name="create-your-applications-ui"></a>Criar a interface do usuário do aplicativo
A seção abaixo mostra como um aplicativo pode consultar um servidor de back-end protegido como o Microsoft Graph. Um arquivo MainWindow.xaml deve ser criado automaticamente como parte do modelo de projeto. Abra esse arquivo e, em seguida, siga as instruções abaixo:

Substitua o `<Grid>` do aplicativo pelo seguinte:

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
