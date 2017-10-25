---
title: "Obter dados usando a API de Relatório do Azure AD com certificados | Microsoft Docs"
description: "Explica como usar a API de Relatório do Azure AD com credenciais de certificado para obter dados de diretórios sem intervenção do usuário."
services: active-directory
documentationcenter: 
author: ramical
writer: v-lorisc
manager: kannar
ms.assetid: 
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/08/2017
ms.author: ramical
ms.openlocfilehash: 38c240ed1608b2e99bde78f3633e722f8e2fa30b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-data-using-the-azure-ad-reporting-api-with-certificates"></a>Obter dados usando a API de Relatório do Azure AD com certificados
Este artigo explica como usar a API de Relatório do Azure AD com credenciais de certificado para obter dados de diretórios sem intervenção do usuário. 

## <a name="use-the-azure-ad-reporting-api"></a>Usar a API de Relatório do Azure AD 
A API de Relatório do Azure AD exige que você conclua as seguintes etapas:
 *  Instalar pré-requisitos
 *  Definir o certificado em seu aplicativo
 *  Obter um token de acesso
 *  Usar o token de acesso para chamar a API do Graph

Para saber mais sobre o código-fonte, confira [Aproveitar o módulo da API de Relatório](https://github.com/AzureAD/azure-activedirectory-powershell/tree/gh-pages/Modules/AzureADUtils). 

### <a name="install-prerequisites"></a>Instalar pré-requisitos
Você precisará ter o Azure AD PowerShell V2 e o módulo AzureADUtils instalados.

1. Baixe e instale o Azure AD Powershell V2, seguindo as instruções em [Azure Active Directory PowerShell](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure AD Cmdlets/AzureAD/index.md).
2. Baixe o módulo Azure AD Utils em [AzureAD/azure-activedirectory-powershell](https://github.com/AzureAD/azure-activedirectory-powershell/blob/gh-pages/Modules/AzureADUtils/AzureADUtils.psm1). 
  Esse módulo fornece vários cmdlets do utilitário, incluindo:
   * A versão mais recente do ADAL usando o Nuget
   * Tokens de acesso do usuário, chaves de aplicativo e certificados usando ADAL
   * Resultados paginados de manipulação da API do Graph

**Para instalar o módulo Azure AD Utils:**

1. Crie um diretório para salvar o módulo de utilitários (por exemplo, c:\azureAD) e baixe o módulo do GitHub.
2. Abra uma sessão do PowerShell e vá para o diretório que você acabou de criar. 
3. Importe o módulo e instale-o no caminho do módulo do PowerShell usando o cmdlet Install-AzureADUtilsModule. 

A sessão deve fica mais ou menos como esta tela:

  ![Windows PowerShell](./media/active-directory-report-api-with-certificates/windows-powershell.png)

### <a name="set-the-certificate-in-your-app"></a>Definir o certificado em seu aplicativo
1. Se você já tiver um aplicativo, obtenha a ID do objeto do Portal do Azure. 

  ![Portal do Azure](./media/active-directory-report-api-with-certificates/azure-portal.png)

2. Abra uma sessão do PowerShell e conecte-se ao Azure AD usando o cmdlet Connect-AzureAD.

  ![Portal do Azure](./media/active-directory-report-api-with-certificates/connect-azuaread-cmdlet.png)

3. Use o cmdlet New-AzureADApplicationCertificateCredential de AzureADUtils para adicionar uma credencial de certificado a ele. 

>[!Note]
>Você precisa fornecer a ID de objeto capturada anteriormente, bem como o objeto de certificado do aplicativo (obter isso usando a unidade Cert:).
>


  ![Portal do Azure](./media/active-directory-report-api-with-certificates/add-certificate-credential.png)
  
### <a name="get-an-access-token"></a>Obter um token de acesso

Para obter um token de acesso, use o cmdlet Get-AzureADGraphAPIAccessTokenFromCert de AzureADUtils. 

>[!NOTE]
>Você precisa usar a ID do aplicativo em vez da ID de objeto que você usou na última seção.
>

 ![Portal do Azure](./media/active-directory-report-api-with-certificates/application-id.png)

### <a name="use-the-access-token-to-call-the-graph-api"></a>Usar o token de acesso para chamar a API do Graph

Agora, você pode criar o script. Abaixo vemos um exemplo usando o cmdlet Invoke-AzureADGraphAPIQuery do AzureADUtils. Esse cmdlet lida com várias páginas de resultados e os envia para o pipeline do PowerShell. 

 ![Portal do Azure](./media/active-directory-report-api-with-certificates/script-completed.png)

Agora você está pronto para exportar para um CSV e salvar em um sistema SIEM. Você pode também encapsular o script em uma tarefa agendada para obter dados do Azure AD do seu locatário periodicamente sem a necessidade de armazenar as chaves de aplicativo no código-fonte. 

## <a name="next-steps"></a>Próximas etapas
[Os fundamentos do gerenciamento de identidades do Azure](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals-identity)<br>



