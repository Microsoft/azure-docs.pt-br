---
title: Instalação silenciosa do conector de Proxy do Aplicativo Azure AD | Microsoft Docs
description: Aborda como realizar a instalação autônoma do Conector de roxy de Aplicativo do Azure AD para fornecer acesso remoto seguro aos seus aplicativos locais.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/24/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ac442e895850de04bedf7673ffe267ac8697d26
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258143"
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>Criar um script de instalação autônoma para o conector de Proxy de Aplicativo do Azure AD

Este tópico ajuda você a criar um script do Windows PowerShell que permite a instalação autônoma e registro para o conector de Proxy de Aplicativo do Azure AD.

Esse recurso é útil quando você deseja:

* Instale o conector em servidores Windows que não têm uma interface do usuário habilitada ou que você não pode acessar com a Área de Trabalho Remota.
* Instalar e registrar vários conectores ao mesmo tempo.
* Integrar a instalação e o registro do conector como parte de outro procedimento.
* Criar uma imagem de servidor padrão que contém bits do conector, mas que não está registrada.

Para que o [conector de proxy de aplicativo](application-proxy-connectors.md) funcione, ele precisa ser registrado com seu diretório do AD do Azure usando um administrador de aplicativos e uma senha. Normalmente, essa informação é inserida durante a instalação do Conector em uma caixa de diálogo pop-up, mas em vez disso, você pode usar o PowerShell para automatizar esse processo.

Há duas etapas para uma instalação autônoma. Primeiro, instale o conector. Segundo, registre o conector com o Azure AD.

> [!IMPORTANT]
> Se você estiver instalando o conector para a nuvem do Azure governamental, examine os [pré-requisitos](../hybrid/reference-connect-government-cloud.md#allow-access-to-urls) e [as etapas de instalação](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud). Isso requer a habilitação do acesso a um conjunto diferente de URLs e um parâmetro adicional para executar a instalação.

## <a name="install-the-connector"></a>Instalar o conector
Use as seguintes etapas para instalar o conector sem registrá-lo:

1. Abra um prompt de comando.
2. Execute o comando a seguir, no qual o /q significa instalação silenciosa. Uma instalação silenciona não solicita que você aceite os termos de licença.

   ```
   AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
   ```

## <a name="register-the-connector-with-azure-ad"></a>Registrar o conector com o Azure AD
Há dois métodos que você pode usar para registrar o conector:

* Registrar o conector usando um objeto de credencial do Windows PowerShell
* Registrar o conector usando um token criado offline

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Registrar o conector usando um objeto de credencial do Windows PowerShell
1. Crie um objeto de credenciais do Windows PowerShell `$cred` que contém um nome de usuário administrativo e uma senha para seu diretório. Execute o seguinte comando, substituindo *\<username\>* e *\<password\>* :

   ```powershell
   $User = "<username>"
   $PlainPassword = '<password>'
   $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
   $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
   ```
2. Vá para **C:\Program Files\Microsoft AAD App Proxy Connector** e execute o script a seguir usando o objeto `$cred` que você criou:

   ```powershell
   .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy
   ```

### <a name="register-the-connector-using-a-token-created-offline"></a>Registrar o conector usando um token criado offline
1. Crie um token offline usando a classe AuthenticationContext, usando os valores nesse snippet de código ou cmdlets do PowerShell abaixo:

   **Usando C#:**

   ```csharp
   using System;
   using System.Linq;
   using System.Collections.Generic;
   using Microsoft.Identity.Client;

   class Program
   {
   #region constants
   /// <summary>
   /// The AAD authentication endpoint uri
   /// </summary>
   static readonly string AadAuthenticationEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/authorize";

   /// <summary>
   /// The application ID of the connector in AAD
   /// </summary>
   static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";
 
   /// <summary>
   /// The AppIdUri of the registration service in AAD
   /// </summary>
   static readonly string RegistrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp/user_impersonation";

   #endregion

   #region private members
   private string token;
   private string tenantID;
   #endregion

   public void GetAuthenticationToken()
   {
    
   IPublicClientApplication clientApp = PublicClientApplicationBuilder
      .Create(ConnectorAppId)
      .WithDefaultRedirectUri() // will automatically use the default Uri for native app
      .WithAuthority(AadAuthenticationEndpoint)
      .Build();

      AuthenticationResult authResult = null;
            
      IAccount account = null;

      IEnumerable<string> scopes = new string[] { RegistrationServiceAppIdUri };

      try
      {
       authResult = await clientApp.AcquireTokenSilent(scopes, account).ExecuteAsync();
      }
       catch (MsalUiRequiredException ex)
      {
       authResult = await clientApp.AcquireTokenInteractive(scopes).ExecuteAsync();
      }


      if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
      {
       Trace.TraceError("Authentication result, token or tenant id returned are null");
       throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
      }

      token = authResult.AccessToken;
      tenantID = authResult.TenantId;
      }
      ```

   **Usando o PowerShell:**

   ```powershell
   # Load MSAL (Tested with version 4.7.1) 

   Add-Type -Path "..\MSAL\Microsoft.Identity.Client.dll" 
        
   # The AAD authentication endpoint uri
        
   $authority = "https://login.microsoftonline.com/common/oauth2/v2.0/authorize"

   #The application ID of the connector in AAD

   $connectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

   #The AppIdUri of the registration service in AAD
   $registrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp/user_impersonation"

   # Define the resources and scopes you want to call 

   $scopes = New-Object System.Collections.ObjectModel.Collection["string"] 

   $scopes.Add($registrationServiceAppIdUri)

   $app = [Microsoft.Identity.Client.PublicClientApplicationBuilder]::Create($connectorAppId).WithAuthority($authority).WithDefaultRedirectUri().Build()

   [Microsoft.Identity.Client.IAccount] $account = $null

   # Acquiring the token 

   $authResult = $null

   $authResult = $app.AcquireTokenInteractive($scopes).WithAccount($account).ExecuteAsync().ConfigureAwait($false).GetAwaiter().GetResult()

   # Check AuthN result
   If (($authResult) -and ($authResult.AccessToken) -and ($authResult.TenantId)) {
        
   $token = $authResult.AccessToken
   $tenantId = $authResult.TenantId

   Write-Output "Success: Authentication result returned."
        
   }
   Else {
         
   Write-Output "Error: Authentication result, token or tenant id returned with null."
        
   } 
   ```

2. Quando tiver o token, crie uma SecureString usando o token:

   ```powershell
   $SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force
   ```

3. Execute o seguinte comando do Windows PowerShell, substituindo \<tenant GUID\> pela sua ID de diretório:

   ```powershell
   .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy
   ```

## <a name="next-steps"></a>Próximas etapas
* [Publicar aplicativos usando seu próprio nome de domínio](application-proxy-configure-custom-domain.md)
* [Habilitar o logon único](application-proxy-configure-single-sign-on-with-kcd.md)
* [Solucionar problemas que surgirem com o Proxy de Aplicativo](application-proxy-troubleshoot.md)