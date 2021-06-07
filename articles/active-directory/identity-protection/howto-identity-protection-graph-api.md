---
title: Microsoft Graph SDK do PowerShell e Azure Active Directory Identity Protection
description: Saiba como consultar Microsoft Graph as detecções de risco e as informações associadas de Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 01/25/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2db8cfe652c0fca4b68b00d846e345c1b60cd05d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880229"
---
# <a name="azure-active-directory-identity-protection-and-the-microsoft-graph-powershell-sdk"></a>Azure Active Directory Identity Protection e o SDK do Microsoft Graph PowerShell

Microsoft Graph é o ponto de extremidade da API unificada da Microsoft e a página inicial das APIs de [Azure Active Directory Identity Protection](./overview-identity-protection.md) . Este artigo mostrará como usar o SDK do [Microsoft Graph PowerShell](/graph/powershell/get-started) para obter detalhes de usuário arriscados usando o PowerShell. As organizações que desejam consultar as APIs de Microsoft Graph diretamente podem usar o artigo [tutorial: identificar e corrigir riscos usando APIs de Microsoft Graph](/graph/tutorial-riskdetection-api) para iniciar essa jornada.


## <a name="connect-to-microsoft-graph"></a>Conectar o Microsoft Graph

Há quatro etapas para acessar dados de Proteção de Identidade por meio do Microsoft Graph:

- [Criar um certificado](#create-a-certificate)
- [Criar um novo registro de aplicativo](#create-a-new-app-registration)
- [Configure as permissões da API](#configure-api-permissions)
- [Configure uma credencial válida](#configure-a-valid-credential)

### <a name="create-a-certificate"></a>Criar um certificado

Em um ambiente de produção, você usaria um certificado de sua autoridade de certificação de produção, mas, neste exemplo, usaremos um certificado autoassinado. Crie e exporte o certificado usando os comandos do PowerShell a seguir.

```powershell
$cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"
```

### <a name="create-a-new-app-registration"></a>Criar um novo registro de aplicativo

1. No portal do Azure, navegue até os registros de aplicativo do **Azure Active Directory** > .
1. Selecione **Novo registro**.
1. Na página **Criar**, siga as seguintes etapas:
   1. Na caixa de texto **Nome**, digite um nome para seu aplicativo (por exemplo: API de detecção de riscos do Azure AD).
   1. Em **Tipos de conta com suporte**, selecione o tipo de contas que usarão as APIs.
   1. Selecione **Registrar**.
1. Anote a ID do **aplicativo (cliente)** e a **ID do diretório (locatário)** , pois você precisará desses itens mais tarde.

### <a name="configure-api-permissions"></a>Configure as permissões da API

Neste exemplo, configuramos permissões de aplicativo, permitindo que este exemplo seja usado de forma autônoma. Se conceder permissões a um usuário que será conectado, escolha permissões delegadas em vez disso. Mais informações sobre tipos de permissão diferentes podem ser encontradas no artigo [permissões e consentimento na plataforma Microsoft Identity](../develop/v2-permissions-and-consent.md#permission-types).

1. No **aplicativo** que você criou, selecione **permissões de API**.
1. Na página **Permissões configuradas**, na barra de ferramentas na parte superior, clique em **Adicionar uma permissão**.
1. Na página **Adicionar acesso à API**, clique em **Selecionar uma API**.
1. Na página **Selecionar uma API**, selecione **Microsoft Graph** e clique em **Selecionar**.
1. Na página **Solicitar permissões de API**: 
   1. Selecione **Permissões de aplicativo**.
   1. Marque as caixas de seleção ao lado de `IdentityRiskEvent.Read.All` e `IdentityRiskyUser.Read.All` .
   1. Escolha **Adicionar permissões**.
1. Selecione **conceder consentimento de administrador para o domínio** 

### <a name="configure-a-valid-credential"></a>Configure uma credencial válida

1. No **Aplicativo** que você criou, selecione **Certificados e segredos**.
1. Em **certificados**, selecione **carregar certificado**.
   1. Selecione o certificado exportado anteriormente na janela que é aberta.
   1. Selecione **Adicionar**.
1. Anote a **impressão digital** do certificado, pois você precisará dessas informações na próxima etapa.

## <a name="list-risky-users-using-powershell"></a>Listar usuários arriscados usando o PowerShell

Para habilitar a capacidade de consultar Microsoft Graph, precisamos instalar o `Microsoft.Graph` módulo em nossa janela do PowerShell, usando o `Install-Module Microsoft.Graph` comando.

Modifique as variáveis a seguir para incluir as informações geradas nas etapas anteriores e, em seguida, execute-as como um todo para obter detalhes de usuário arriscados usando o PowerShell.

```powershell
$ClientID       = "<your client ID here>"        # Application (client) ID gathered when creating the app registration
$tenantdomain   = "<your tenant domain here>"    # Directory (tenant) ID gathered when creating the app registration
$Thumbprint     = "<your client secret here>"    # Certificate thumbprint gathered when configuring your credential

Select-MgProfile -Name "beta"
  
Connect-MgGraph -ClientId $ClientID -TenantId $tenantdomain -CertificateThumbprint $Thumbprint

Get-MgRiskyUser -All
```

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao SDK do Microsoft Graph PowerShell](/graph/powershell/get-started)
- [Tutorial: identificar e corrigir riscos usando APIs de Microsoft Graph](/graph/tutorial-riskdetection-api)
- [Visão geral do Microsoft Graph](https://developer.microsoft.com/graph/docs)
- [Obter acesso sem um usuário](/graph/auth-v2-service)
- [Raiz do Serviço Azure AD Identity Protection](/graph/api/resources/identityprotectionroot)
- [Azure Active Directory Identity Protection](./overview-identity-protection.md)
