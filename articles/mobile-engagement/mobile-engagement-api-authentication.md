---
title: Autenticar com APIs REST do Mobile Engagement
description: Descreve como autenticar com APIs REST do Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: da82cb36-957a-4e19-a805-b44733cf6597
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 10/05/2016
ms.author: wesmc;ricksal
ms.openlocfilehash: b05181d9252c0a804648e01b4058019278ae5abe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="authenticate-with-mobile-engagement-rest-apis"></a>Autenticar com APIs REST do Mobile Engagement
## <a name="overview"></a>Visão geral
Este documento descreve como obter um token Oauth válido do AAD para autenticar com as APIs REST do Mobile Engagement. 

Supõe-se que você tenha uma assinatura válida do Azure e criou um aplicativo do Mobile Engagement usando um dos nossos [Tutoriais de Desenvolvedor](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="authentication"></a>Autenticação
Um token OAuth baseado no Microsoft Azure Active Directory é usado para autenticação. 

Para autenticar a solicitação de uma API, um cabeçalho de autorização deve ser adicionado a cada solicitação com a seguinte forma:

    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

> [!NOTE]
> Tokens de Azure Active Directory expiram em 1 hora.
> 
> 

Há várias maneiras de obter um token. Como as APIs geralmente são chamadas de um serviço de nuvem, você desejará usar uma chave de API. Uma chave de API na terminologia do Azure é chamada de senha de uma entidade de serviço. O procedimento a seguir descreve uma maneira de configurá-lo manualmente.

### <a name="one-time-setup-using-script"></a>Configuração única (usando script)
Você deve seguir o conjunto de instruções abaixo para executar a configuração usando um script do PowerShell, que leva o tempo mínimo para a configuração e usa os padrões mais permissivos. Você também pode seguir as instruções de [configuração manual](mobile-engagement-api-authentication-manual.md) para fazer isso diretamente do portal do Azure e chegar a uma configuração mais sofisticada. 

1. Obtenha a versão mais recente do Azure PowerShell [aqui](http://aka.ms/webpi-azps). Para obter mais informações sobre as instruções de download, consulte este [link](/powershell/azure/overview).  
2. Depois de instalar o Azure PowerShell, use os seguintes comandos para garantir que o **módulo do Azure** esteja instalado:
   
    a. Verifique se o módulo do Azure PowerShell está disponível na lista de módulos disponíveis. 
   
        Get-Module –ListAvailable 
   
    ![Módulos do Azure disponíveis][1]
   
    b. Se não encontrar o módulo do Azure PowerShell na lista acima, faça o seguinte:
   
        Import-Module Azure 
3. Faça logon no Azure Resource Manager do PowerShell executando o seguinte comando e fornecendo o nome de usuário e senha de sua conta do Azure: 
   
        Login-AzureRmAccount
4. Se tiver várias assinaturas, faça o seguinte:
   
    a. Obtenha uma lista com todas as suas assinaturas e copie a SubscriptionId da assinatura que deseja usar. Verifique se essa assinatura é o mesma que tem o aplicativo do Mobile Engagement com que você pretende interagir usando as APIs. 
   
        Get-AzureRmSubscription
   
    b. Execute o seguinte comando fornecendo a SubscriptionId para configurar a assinatura a ser usada.
   
        Select-AzureRmSubscription –SubscriptionId <subscriptionId>
5. Copie o texto do script [New-AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) para seu computador local e salve-o como um cmdlet do PowerShell (por exemplo, `APIAuth.ps1`) e execute-o `.\APIAuth.ps1`. 
6. O script solicitará que você forneça uma entrada para **principalName**. Aqui, forneça um nome adequado que você deseja usar para criar seu aplicativo do Active Directory (por exemplo, APIAuth). 
7. Depois que for concluído, o script exibirá os quatro valores a seguir, que você precisará autenticar de forma programática com o AD. Sendo assim, não se esqueça de copiá-los. 
   
    **TenantId**, **SubscriptionId**, **ApplicationId** e **Secret**.
   
    Use o TenantId como `{TENANT_ID}`, o ApplicationId como `{CLIENT_ID}` e o Secret como `{CLIENT_SECRET}`.
   
   > [!NOTE]
   > A política de segurança padrão pode impedir a execução de scripts do PowerShell. Nesse caso, configure temporariamente a política de execução para permitir a execução de script usando o seguinte comando:
   > 
   > Set-ExecutionPolicy RemoteSigned
   > 
   > 
8. O conjunto de cmdlets do PS seria semelhante a este. 
   
    ![][3]
9. Verifique no Portal de Gerenciamento do Azure se um novo aplicativo do AD foi criado com o nome fornecido para o script chamado **principalName** em **Mostrar aplicativos que minha empresa possui**.
   
    ![][4]

#### <a name="steps-to-get-a-valid-token"></a>Etapas para obter um token válido
1. Chame a API com os seguintes parâmetros e certifique-se de substituir TENANT\_ID, CLIENT\_ID and CLIENT\_SECRET:
   
   * **URL da solicitação** como *https://login.microsoftonline.com/{TENANT\_ID}/oauth2/token*
   * **Cabeçalho HTTP Content-Type** : *application/x-www-form-urlencoded*
   * **Corpo da Solicitação HTTP** como *grant\_type=client\_credentials&client_id={CLIENT\_ID}&client_secret={CLIENT\_SECRET}&resource=https%3A%2F%2Fmanagement.core.windows.net%2F*
     
     Segue um exemplo de solicitação:
     
       POST /{TENANT_ID}/oauth2/token HTTP/1.1   Host: login.microsoftonline.com   Content-Type: application/x-www-form-urlencoded   grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso   urce=https%3A%2F%2Fmanagement.core.windows.net%2F
     
     Aqui está um exemplo de resposta:
     
       HTTP/1.1 200 OK   Content-Type: application/json; charset=utf-8   Content-Length: 1234
     
       {"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144   5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_TOKEN}}
     
     Este exemplo incluiu a codificação de URL dos parâmetros de POST; o valor `resource` é, na verdade, `https://management.core.windows.net/`. Tenha cuidado para também codificar a URL `{CLIENT_SECRET}` , pois ela pode conter caracteres especiais.
     
     > [!NOTE]
     > Para testar, é possível usar uma ferramenta de cliente HTTP como o [Fiddler](http://www.telerik.com/fiddler) ou a [extensão Chrome Postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) 
     > 
     > 
2. Agora, em todas as chamadas à API, inclua o cabeçalho de solicitação de autorização:
   
        Authorization: Bearer {ACCESS_TOKEN}
   
    Se você receber um código de status 401 retornado, verifique o corpo da resposta, ele pode informar se o token tiver expirado. Nesse caso, obtenha um novo token.

## <a name="using-the-apis"></a>Usando as APIs
Agora que você tem um token válido, você está pronto para fazer as chamadas à API.

1. Em cada solicitação de API, você precisará passar um token válido e não expirado que você obteve na seção anterior.
2. Você precisará do plug-in em alguns parâmetros na solicitação URI que identifica seu aplicativo. A URI de solicitação é semelhante ao seguinte
   
        https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/
        providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/
   
    Para obter os parâmetros, clique no nome do aplicativo e clique em Painel e você verá uma página semelhante à seguinte com todos os 3 parâmetros.
   
   * **1** `{subscription-id}`
   * **2** `{app-collection}`
   * **3** `{app-resource-name}`
   * **4** O nome do seu Grupo de recursos será **MobileEngagement**, a menos que você tenha criado um novo. 
     
     ![Parâmetros de URI da API do Mobile Engagement][2]

> [!NOTE]
> <br/>
> 
> 1. Ignore o Endereço Raiz da API, pois ele servia apenas para as APIs anteriores.<br/>
> 2. Se você criou o aplicativo usando o portal Clássico do Azure, é necessário usar o nome do Recurso de Aplicativo, que é diferente do nome do Aplicativo em si. Se você criou o aplicativo no Portal do Azure, você deverá usar o Nome do Aplicativo em si (não há nenhuma diferenciação entre o Nome do Recurso do Aplicativo e o Nome do Aplicativo para os aplicativos criados no novo portal).  
> 
> 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication/azure-module.png
[2]: ./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png
[3]: ./media/mobile-engagement-api-authentication/ps-cmdlets.png
[4]: ./media/mobile-engagement-api-authentication/ad-app-creation.png



