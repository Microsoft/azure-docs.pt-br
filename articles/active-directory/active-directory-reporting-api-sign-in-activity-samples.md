---
title: "Exemplos de API de relatório de atividade de entrada do Azure Active Directory | Microsoft Docs"
description: "Como começar a usar a API de relatório do Active Directory do Azure"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: c41c1489-726b-4d3f-81d6-83beb932df9c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/18/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.translationtype: HT
ms.sourcegitcommit: c999eb5d6b8e191d4268f44d10fb23ab951804e7
ms.openlocfilehash: 7fc2b59fe37ed2ffe85925c457300ef8fd83c3c7
ms.contentlocale: pt-br
ms.lasthandoff: 07/17/2017

---
# <a name="azure-active-directory-sign-in-activity-report-api-samples"></a>Exemplos de API de relatório de atividade de entrada do Azure Active Directory
Este tópico faz parte de uma coleção de tópicos sobre a API de relatório do Azure Active Directory.  
Os relatórios do Azure AD fornecem uma API que permite a você acessar dados de atividade de entrada usando código ou ferramentas relacionadas.  
O escopo deste tópico é fornecer código de exemplo para a **API de atividade de entrada**.

Consulte:

* [Logs de auditoria](active-directory-reporting-azure-portal.md#activity-reports) para obter mais informações conceituais
* [Introdução à API de relatório do Azure Active Directory](active-directory-reporting-api-getting-started.md) para saber mais sobre a API de relatório.


## <a name="prerequisites"></a>Pré-requisitos
Antes de usar os exemplos deste tópico, você precisará atender os [pré-requisitos para acessar a API de relatório do Azure AD](active-directory-reporting-api-prerequisites.md).  

## <a name="powershell-script"></a>Script do PowerShell
    # This script will require the Web Application and permissions setup in Azure Active Directory
    $ClientID       = "<clientId>"             # Should be a ~35 character string insert your info here
    $ClientSecret   = "<clientSecret>"         # Should be a ~44 character string insert your info here
    $loginURL       = "https://login.microsoftonline.com/"
    $tenantdomain   = "<tenantDomain>"
    $ daterange            # For example, contoso.onmicrosoft.com

    $7daysago = "{0:s}" -f (get-date).AddDays(-7) + "Z"
    # or, AddMinutes(-5)

    Write-Output $7daysago

    # Get an Oauth 2 access token based on client id, secret and tenant domain
    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}

    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    $url = "https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&`$filter=signinDateTime ge $7daysago"

    $i=0

    Do{
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        Write-Output "Save the output to a file SigninActivities$i.json"
        Write-Output "---------------------------------------------"
        $myReport.Content | Out-File -FilePath SigninActivities$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)

    } else {

        Write-Host "ERROR: No Access Token"
    }




## <a name="executing-the-script"></a>Executando o script
Quando você terminar de editar o script, execute-o e verifique se os dados esperados do relatório Logs de auditoria são retornados.

O script retorna a saída do relatório de entradas no formato JSON. Ele também cria um arquivo `SigninActivities.json` com a mesma saída. Você pode experimentar ao modificar o script para retornar dados de outros relatórios, além de comentar os formatos de saída de que você não precisa.

## <a name="next-steps"></a>Próximas etapas
* Você gostaria de personalizar os exemplos deste tópico? Confira a [referência da API de atividade de entrada do Azure Active Directory](active-directory-reporting-api-sign-in-activity-reference.md). 
* Se você quiser uma visão geral de como usar a API de relatório do Azure Active Directory, confira [Introdução à API de relatório do Azure Active Directory](active-directory-reporting-api-getting-started.md).
* Se você quiser saber mais sobre os relatórios do Azure Active Directory, confira o [Guia de relatórios do Azure Active Directory](active-directory-reporting-guide.md).  


