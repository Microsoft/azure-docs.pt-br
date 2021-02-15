---
title: Acessar e examinar os logs de auditoria
titleSuffix: Azure AD B2C
description: Como acessar Azure AD B2C logs de auditoria programaticamente e no portal do Azure.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 02/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 04d5a989fcaf3cef3eea3b782842fb3255d4b317
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98661095"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Acessando os logs de auditoria do Microsoft Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) emite logs de auditoria contendo informações de atividade sobre recursos B2C, tokens emitidos e acesso de administrador. Este artigo fornece uma breve visão geral das informações disponíveis em logs de auditoria e instruções sobre como acessar esses dados para seu locatário de Azure AD B2C.

Os eventos do log de auditoria são mantidos apenas por **sete dias**. Planeje realizar o download e armazenar seus logs usando um dos métodos abaixo se precisar de um período de retenção mais longo.

> [!NOTE]
> Você não pode ver entradas de usuário para aplicativos de Azure AD B2C individuais na seção **usuários** das páginas **Azure Active Directory** ou **Azure ad B2C** no portal do Azure. Os eventos de entrada mostram a atividade do usuário, mas não podem ser correlacionados de volta ao aplicativo B2C no qual o usuário se conectou. Você deve usar os logs de auditoria para isso, conforme explicado mais adiante neste artigo.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Visão geral das atividades disponíveis na categoria B2C dos logs de auditoria

A categoria **B2C** nos logs de auditoria contém os seguintes tipos de atividades:

|Tipo de atividade |Descrição  |
|---------|---------|
|Autorização |Atividades relacionadas à autorização de um usuário para acessar os recursos do B2C (por exemplo, um administrador acessando uma lista de políticas do B2C).         |
|Diretório |Atividades relacionadas aos atributos de diretório recuperados quando um administrador entra usando o portal do Azure. |
|Aplicativo | Operações CRUD (criar, ler, atualizar e excluir) em aplicativos B2C. |
|Chave |Operações CRUD em chaves armazenadas em um contêiner de chave B2C. |
|Recurso |Operações CRUD em recursos do B2C. Por exemplo, políticas e provedores de identidade.
|Autenticação |Validação de credenciais de usuário e emissão de tokens.|

Para atividades CRUD do objeto de usuário, consulte a categoria **Diretório Principal**.

## <a name="example-activity"></a>Atividade de exemplo

Esta imagem de exemplo da portal do Azure mostra os dados capturados quando um usuário entra com um provedor de identidade externo, neste caso, o Facebook:

![Exemplo de página de detalhes da atividade de log de auditoria no portal do Azure](./media/view-audit-logs/audit-logs-example.png)

O painel detalhes da atividade contém as seguintes informações relevantes:

|Seção|Campo|Descrição|
|-------|-----|-----------|
| Atividade | Nome | Qual atividade ocorreu. Por exemplo, *emita um id_token para o aplicativo*, que conclui a entrada do usuário real. |
| Iniciado por (ator) | ObjectId | A **ID de objeto** do aplicativo B2C ao qual o usuário está se conectando. Esse identificador não é visível no portal do Azure, mas pode ser acessado por meio da API do Microsoft Graph. |
| Iniciado por (ator) | SPNs | A **ID do aplicativo** B2C para o qual o usuário está entrando. |
| Destino(s) | ObjectId | A **ID de objeto** do usuário que está entrando. |
| Additional Details | TenantId | A **ID do locatário** do locatário do Azure ad B2C. |
| Additional Details | PolicyId | A **ID de política** do fluxo do usuário (política) que está sendo usada para conectar o usuário. |
| Additional Details | ApplicationId | A **ID do aplicativo** B2C para o qual o usuário está entrando. |

## <a name="view-audit-logs-in-the-azure-portal"></a>Exibir logs de auditoria no portal do Azure

O portal do Azure fornece acesso aos eventos do log de auditoria em seu locatário do Azure AD B2C.

1. Entre no [Portal do Azure](https://portal.azure.com)
1. Alterne para o diretório que contém seu locatário Azure AD B2C e, em seguida, navegue até **Azure ad B2C**.
1. Em **atividades** no menu à esquerda, selecione **logs de auditoria**.

Uma lista de eventos de atividade registrados nos últimos sete dias é exibida.

![Filtro de exemplo com dois eventos de atividade no portal do Azure](./media/view-audit-logs/audit-logs-example-filter.png)

Várias opções de filtragem estão disponíveis, incluindo:

* **Tipo de recurso de atividade** – filtre pelos tipos de atividade mostrados na tabela na seção [visão geral da atividades disponíveis](#overview-of-activities-available-in-the-b2c-category-of-audit-logs) .
* **Data** – filtre o intervalo de datas das atividades mostradas.

Se você selecionar uma linha na lista, os detalhes da atividade do evento serão exibidos.

Para baixar a lista de eventos de atividade em um arquivo CSV (valores separados por vírgula), selecione **baixar**.

## <a name="get-audit-logs-with-the-azure-ad-reporting-api"></a>Obter logs de auditoria com a API de relatórios do Azure AD

Os logs de auditoria são publicados para o mesmo pipeline como outras atividades do Azure Active Directory, para que possam ser acessados por meio da [API de relatórios do Microsoft Azure Active Directory](/graph/api/directoryaudit-list). Para obter mais informações, consulte Introdução [à API de relatório do Azure Active Directory](../active-directory/reports-monitoring/concept-reporting-api.md).

### <a name="enable-reporting-api-access"></a>Habilitar o acesso à API de relatórios

Para permitir o acesso baseado em script ou aplicativo à API de relatórios do Azure AD, você precisa de um aplicativo registrado em seu locatário Azure AD B2C com as seguintes permissões de API. Você pode habilitar essas permissões em um registro de aplicativo existente em seu locatário B2C ou criar um novo especificamente para uso com a automação do log de auditoria.

* Microsoft Graph permissões de aplicativo > > AuditLog > AuditLog. Read. All

Siga as etapas no seguinte artigo para registrar um aplicativo com as permissões necessárias:

[Gerenciar Azure AD B2C com Microsoft Graph](microsoft-graph-get-started.md)

Depois de registrar um aplicativo com as permissões apropriadas, consulte a seção script do PowerShell mais adiante neste artigo para obter um exemplo de como você pode obter eventos de atividade com um script.

### <a name="access-the-api"></a>Acessar a API

Para baixar Azure AD B2C eventos do log de auditoria por meio da API, filtre os logs na `B2C` categoria. Para filtrar por categoria, use o `filter` parâmetro de cadeia de caracteres de consulta ao chamar o ponto de extremidade da API de relatórios do Azure AD.

```http
https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?$filter=loggedByService eq 'B2C' and activityDateTime gt 2019-09-10T02:28:17Z
```

### <a name="powershell-script"></a>Script do PowerShell

O script do PowerShell a seguir mostra um exemplo de como consultar a API de relatórios do Azure AD. Depois de consultar a API, ela imprime os eventos registrados para a saída padrão e grava a saída JSON em um arquivo.

Você pode experimentar esse script na [Azure cloud Shell](overview.md). Certifique-se de atualizá-lo com a ID do aplicativo, o segredo do cliente e o nome do seu locatário de Azure AD B2C.

```powershell
# This script requires an application registration that's granted Microsoft Graph API permission
# https://docs.microsoft.com/azure/active-directory-b2c/microsoft-graph-get-started

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's client ID, a GUID
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's client secret
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # Insert your Azure AD B2C tenant domain name

$loginURL       = "https://login.microsoftonline.com"
$resource       = "https://graph.microsoft.com"           # Microsoft Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 through n for number of nextLink pages
if ($oauth.access_token -ne $null) {
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = "https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?`$filter=loggedByService eq 'B2C' and activityDateTime gt  " + $7daysago

    # loop through each query page (1 through n)
    Do {
        # display each event on the console window
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output ($event | ConvertTo-Json)
        }

        # save the query page to an output file
        Write-Output "Save the output to a file audit$i.json"
        $myReport.Content | Out-File -FilePath audit$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)
} else {
    Write-Host "ERROR: No Access Token"
}
```

Aqui está a representação JSON do evento de atividade de exemplo mostrado anteriormente neste artigo:

```json
{
    "id": "B2C_DQO3J_4984536",
    "category": "Authentication",
    "correlationId": "00000000-0000-0000-0000-000000000000",
    "result": "success",
    "resultReason": "N/A",
    "activityDisplayName": "Issue an id_token to the application",
    "activityDateTime": "2019-09-14T18:13:17.0618117Z",
    "loggedByService": "B2C",
    "operationType": "",
    "initiatedBy": {
        "user": null,
        "app": {
            "appId": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "servicePrincipalId": null,
            "servicePrincipalName": "00000000-0000-0000-0000-000000000000"
        }
    },
    "targetResources": [
        {
            "id": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "type": "User",
            "userPrincipalName": null,
            "groupType": null,
            "modifiedProperties": []
        }
    ],
    "additionalDetails": [
        {
            "key": "TenantId",
            "value": "test.onmicrosoft.com"
        },
        {
            "key": "PolicyId",
            "value": "B2C_1A_signup_signin"
        },
        {
            "key": "ApplicationId",
            "value": "00000000-0000-0000-0000-000000000000"
        },
        {
            "key": "Client",
            "value": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.132 Safari/537.36"
        },
        {
            "key": "IdentityProviderName",
            "value": "facebook"
        },
        {
            "key": "IdentityProviderApplicationId",
            "value": "0000000000000000"
        },
        {
            "key": "ClientIpAddress",
            "value": "127.0.0.1"
        }
    ]
}
```

## <a name="next-steps"></a>Próximas etapas

Você pode automatizar outras tarefas de administração, por exemplo, [gerenciar Azure ad B2C contas de usuário com Microsoft Graph](microsoft-graph-operations.md).