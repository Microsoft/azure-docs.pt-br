---
title: Autenticação do Azure Active Directory (AD do Azure)
titleSuffix: Azure Cognitive Services
description: Referência para o SDK do leitor de imersão
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: de6a29e1c4c102aa7d4038185c1635544ba9dfe2
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688786"
---
# <a name="use-azure-active-directory-azure-ad-authentication-with-the-immersive-reader-service"></a>Usar a autenticação do Azure Active Directory (AD do Azure) com o serviço de leitura de imersão

Nas seções a seguir, você usará o ambiente de Azure Cloud Shell ou o CLI do Azure para criar um novo recurso de leitor de imersão com um subdomínio personalizado e, em seguida, configurar o Azure AD em seu locatário do Azure. Depois de concluir essa configuração inicial, você chamará o Azure AD para obter um token de acesso, semelhante a como ele será feito ao usar o SDK do leitor de imersão. Se você ficar preso, os links serão fornecidos em cada seção com todas as opções disponíveis para cada um dos comandos CLI do Azure.

## <a name="create-an-immersive-reader-resource-with-a-custom-subdomain"></a>Criar um recurso de leitor de imersão com um subdomínio personalizado

1. Comece abrindo a [Azure cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Em seguida, [Selecione uma assinatura](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0#description):

   ```azurecli-interactive
   Select-AzSubscription -SubscriptionName <YOUR_SUBSCRIPTION>
   ```

2. Em seguida, [crie um recurso de leitor de imersão](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0) com um subdomínio personalizado.

   >[!NOTE]
   > O nome do subdomínio é usado no SDK do leitor de imersão ao iniciar o leitor com a função launchAsync.

   -SkuName pode ser F0 (camada gratuita) ou S0 (camada Standard, também gratuita durante a visualização pública). A camada S0 tem um limite de taxa de chamada mais alto e nenhuma cota mensal no número de chamadas.

   -O local pode ser qualquer um dos seguintes `eastus`: `westus`, `australiaeast`, `centralindia` `japaneast` `northeurope`,,,,`westeurope`

   -CustomSubdomainName precisa ser globalmente exclusivo e não pode incluir caracteres especiais, como: ".", "!", ",".


   ```azurecli-interactive
   $resource = New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <RESOURCE_NAME> -Type ImmersiveReader -SkuName S0 -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>

   // Display the Resource info
   $resource
   ```

   Se for bem-sucedido, o **ponto de extremidade** do recurso deverá mostrar o nome do subdomínio exclusivo para seu recurso.

   Aqui estamos capturando o objeto de recurso recém-criado em uma variável **$Resource** , pois ele será usado posteriormente ao conceder acesso a esse recurso.


   >[!NOTE]
   > Se você criar um recurso no portal do Azure, o recurso ' nome ' será usado como o subdomínio personalizado. Você pode verificar o nome do subdomínio no portal acessando a página Visão geral do recurso e localizando o subdomínio no ponto de extremidade listado lá `https://[SUBDOMAIN].cognitiveservices.azure.com/`, por exemplo,. Você também pode verificar aqui mais tarde quando precisar obter o subdomínio para integração com o SDK.

   Se o recurso foi criado no portal, você também pode [obter um recurso existente](https://docs.microsoft.com/powershell/module/az.cognitiveservices/get-azcognitiveservicesaccount?view=azps-1.8.0) agora.

   ```azurecli-interactive
   $resource = Get-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <RESOURCE_NAME>

   // Display the Resource info
   $resource
   ```

## <a name="assign-a-role-to-a-service-principal"></a>Atribuir uma função a uma entidade de serviço

Agora que você tem um subdomínio personalizado associado ao recurso, é necessário atribuir uma função a uma entidade de serviço.

1. Primeiro, vamos [criar um aplicativo do Azure ad](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0).

   >[!NOTE]
   > A senha, também conhecida como ' segredo do cliente ', será usada ao obter tokens de autenticação.

   ```azurecli-interactive
   $password = "<YOUR_PASSWORD>"
   $secureStringPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
   $aadApp = New-AzADApplication -DisplayName ImmersiveReaderAAD -IdentifierUris http://ImmersiveReaderAAD -Password $secureStringPassword

   // Display the Azure AD app info
   $aadApp
   ```

   Aqui estamos capturando o objeto de aplicativo do Azure AD recém-criado em uma variável **$aadApp** para uso na próxima etapa.

2. Em seguida, você precisa [criar uma entidade de serviço](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0) para o aplicativo do Azure AD.

   ```azurecli-interactive
   $principal = New-AzADServicePrincipal -ApplicationId $aadApp.ApplicationId

   // Display the service principal info
   $principal
   ```

   Aqui estamos capturando o objeto de entidade de serviço recém-criado em uma variável **$principal** para uso na próxima etapa.


3. A última etapa é [atribuir a função "usuário de serviços cognitivas"](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0) à entidade de serviço (com escopo para o recurso). Ao atribuir uma função, você está concedendo acesso à entidade de serviço a esse recurso. Você pode conceder o mesmo acesso de entidade de serviço a vários recursos em sua assinatura.

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId $principal.Id -Scope $resource.Id -RoleDefinitionName "Cognitive Services User"
   ```

   >[!NOTE]
   > Essa etapa precisa ser executada para cada recurso de leitor de imersão que você criar. Por exemplo, se você criar vários recursos para regiões globais diferentes, será necessário executar essa etapa para cada um desses recursos regionais para que a autenticação do Azure AD funcione para todos eles. Ou, se você criar um novo recurso em um momento posterior, será necessário executar essa etapa de atribuição de função para esse novo recurso também.


## <a name="obtain-an-azure-ad-token"></a>Obter um token do Azure AD

Neste exemplo, sua senha é usada para autenticar a entidade de serviço para obter um token do Azure AD.

1. Obtenha sua **tenantid**:
   ```azurecli-interactive
   $context = Get-AzContext
   $context.Tenant.Id
   ```

2. Obter um token:
   ```azurecli-interactive
   $authority = "https://login.windows.net/" + $context.Tenant.Id
   $resource = "https://cognitiveservices.azure.com/"
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $aadApp.ApplicationId, $password
   $token = $authContext.AcquireTokenAsync($resource, $clientCredential).Result
   $token
   ```

   >[!NOTE]
   > O SDK do leitor de imersão usa a propriedade AccessToken do token, por exemplo, $token. AccessToken. Consulte a [referência](reference.md) do SDK e os [exemplos](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples) de código para obter detalhes.

Como alternativa, a entidade de serviço pode ser autenticada com um certificado. Além de uma entidade de serviço, as entidades de usuário também têm suporte ao ter permissões delegadas por meio de outro aplicativo do Azure AD. Nesse caso, em vez de senhas ou certificados, os usuários seriam solicitados a fornecer autenticação de dois fatores ao adquirir tokens.

## <a name="next-steps"></a>Próximas etapas

* Confira o [tutorial](./tutorial.md) para ver o que mais você pode fazer com o SDK da Leitura Avançada
* Explore o [SDK da Leitura Avançada](https://github.com/microsoft/immersive-reader-sdk) e a [Referência de SDK da Leitura Avançada](./reference.md)