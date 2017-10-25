---
title: "Azure Active Directory B2C: definições e exemplos de API de relatório de uso | Microsoft Docs"
description: "Guia e exemplos de como obter relatórios sobre usuários de locatário, autenticações e autenticações multifator do Azure AD B2C"
services: active-directory-b2c
documentationcenter: dev-center-name
author: rojasja
manager: mbaldwin
ms.service: active-directory-b2c
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/04/2017
ms.author: joroja
ms.openlocfilehash: 52180b760046d273c5a75720df0a73532d0343ad
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="accessing-usage-reports-in-azure-ad-b2c-via-the-reporting-api"></a>Acessando os relatórios de uso do Azure AD B2C por meio da API de geração de relatórios

O Azure AD B2C (Azure Active Directory B2C) fornece autenticação com base na entrada do usuário e na Autenticação Multifator do Azure. A autenticação é fornecida para usuários finais da sua família de aplicativos em provedores de identidade. Quando você sabe o número de usuários registrados no locatário, os provedores que eles usaram para registrar-se e o número de autenticações por tipo, você pode responder a perguntas como:
* Quantos usuários de cada tipo de provedor de identidade (por exemplo, uma conta da Microsoft ou do LinkedIn) se registraram nos últimos 10 dias?
* Quantas autenticações usando a Autenticação Multifator foram concluídas com êxito no último mês?
* Quantas autenticações baseadas em início de sessão foram concluídas neste mês? Por dia? Por aplicativo?
* Como posso estimar o custo mensal esperado da minha atividade de locatário do Azure AD B2C?

Este artigo se concentra em relatórios ligados à atividade de cobrança, que é baseada no número de usuários, nas autenticações faturáveis baseadas em início de sessão e nas autenticações multifator.


## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, você precisa concluir as etapas em [Pré-requisitos para acessar as APIs de relatório do Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/). Criar um aplicativo, obter um segredo para ele e conceder acesso direitos para relatórios do locatário B2C do AD do Azure. Também são fornecidos aqui os exemplos de *script Bash* e de *script Python*. 

## <a name="powershell-script"></a>Script do PowerShell
Este script demonstra a criação de quatro relatórios de uso por meio do parâmetro `TimeStamp` e do filtro `ApplicationId`.

```powershell
# This script will require the Web Application and permissions setup in Azure Active Directory

# Constants
$ClientID      = "your-client-application-id-here"  
$ClientSecret  = "your-client-application-secret-here"
$loginURL      = "https://login.microsoftonline.com"
$tenantdomain  = "your-b2c-tenant-domain.onmicrosoft.com"  
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body          = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth         = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
if ($oauth.access_token -ne $null) {
    $headerParams  = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    Write-host Data from the tenantUserCount report
    Write-host ====================================================
     # Returns a JSON document for the report
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the tenantUserCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?%24filter=TimeStamp+gt+2016-10-15&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCountSummary report
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=TimeStamp+gt+2016-09-20+and+TimeStamp+lt+2016-10-03&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with ApplicationId filter
    Write-host ====================================================
    # Returns a JSON document for the " " report
        $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCountSummary
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCount?%24filter=TimeStamp+gt+2016-09-10+and+TimeStamp+lt+2016-10-04&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with ApplicationId filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
     Write-host $myReport.Content

} else {
    Write-Host "ERROR: No Access Token"
    }
```


## <a name="usage-report-definitions"></a>Definições de relatório de uso
* **tenantUserCount**: o número de usuários no locatário por tipo de provedor de identidade, por dia, nos últimos 30 dias. (opcionalmente, um filtro `TimeStamp` fornece contas de usuário de uma data especificada até a data atual). O relatório fornece:
  * **TotalUserCount**: o número de todos os objetos de usuário.
  * **OtherUserCount**: o número de usuários do Azure Active Directory (não os usuários do Azure AD B2C).
  * **LocalUserCount**: o número de contas de usuário do Azure AD B2C criadas com credenciais locais ao locatário do Azure AD B2C.

* **AlternateIdUserCount**: o número de usuários do Azure AD B2C registrados com provedores de identidade externa (por exemplo, Facebook, uma conta da Microsoft ou outro locatário do Azure Active Directory, também conhecido como uma `OrgId`).

* **b2cAuthenticationCountSummary**: resumo do número diário de autenticações faturáveis nos últimos 30 dias, por dia e tipo de fluxo de autenticação.

* **b2cAuthenticationCount**: o número de autenticações em um período de tempo. O padrão é nos últimos 30 dias.  (Opcional: os parâmetros `TimeStamp` inicial e final definem um período de tempo específico). A saída inclui `StartTimeStamp` (data mais antiga de atividade para este locatário) e `EndTimeStamp` (atualização mais recente).

* **b2cMfaRequestCountSummary**: resumo do número diário de autenticações multifator, por dia e por tipo (SMS ou voz).


## <a name="limitations"></a>Limitações
Dados de contagem de usuário são atualizados a cada 24 a 48 horas. As autenticações são atualizadas várias vezes ao dia. Ao usar o filtro `ApplicationId`, uma resposta de relatório em branco poderá ser devida a uma das seguintes condições:
  * A ID do aplicativo não existe no locatário. Verifique se que ele está correto.
  * A ID do aplicativo existe, mas não foi encontrado nenhum dado no período do relatório. Examine seus parâmetros de data/hora.


## <a name="next-steps"></a>Próximas etapas
### <a name="monthly-bill-estimates-for-azure-ad"></a>Estimativas de cobrança mensal do Azure AD
Quando combinado com [a mais atual do Azure AD B2C preços disponíveis](https://azure.microsoft.com/pricing/details/active-directory-b2c/), você pode estimar diários, semana e mensal de consumo do Azure.  Uma estimativa é especialmente útil ao planejar alterações no comportamento do locatário que possam afetar o custo geral. Você pode examinar os custos reais na sua [assinatura vinculada do Azure](active-directory-b2c-how-to-enable-billing.md).

### <a name="options-for-other-output-formats"></a>Opções para outros formatos de saída
O código a seguir mostra exemplos do envio da saída para JSON, para uma lista de nome-valor e para XML:
```powershell
# to output to JSON use following line in the PowerShell sample
$myReport.Content | Out-File -FilePath b2cUserJourneySummaryEvents.json -Force

# to output the content to a name value list
($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath name-your-file.txt -Force

# to output the content in XML use the following line
(($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath name-your-file.xml -Force
```
