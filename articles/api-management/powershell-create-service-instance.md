---
title: Início Rápido – Criar uma instância de Gerenciamento de API do Azure usando o PowerShell | Microsoft Docs
description: Crie uma instância de Gerenciamento de API do Azure usando o Azure PowerShell.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/14/2020
ms.author: apimpm
ms.openlocfilehash: eb2c42d26a85a07518a018ba5b8817f13d3cd17f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90707068"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-powershell"></a>'Início Rápido: Criar uma instância de serviço do Gerenciamento de API do Azure usando o PowerShell

O APIM (Gerenciamento de API) do Azure ajuda as organizações a publicar APIs para parceiros externos e desenvolvedores internos a fim de liberar o potencial de seus dados e serviços. O Gerenciamento de API fornece as competências essenciais para garantir um programa de API de sucesso através do envolvimento do desenvolvedor, ideias de negócios, análises, segurança e proteção. O APIM permite que você crie e gerencie gateways de API modernos para serviços de back-end hospedados em qualquer lugar. Para obter mais informações, confira a [Visão geral](api-management-key-concepts.md).

Este início rápido descreve as etapas para criar uma instância de Gerenciamento de API usando cmdlets do Azure PowerShell.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você escolher instalar e usar o PowerShell localmente, este tutorial exigirá o módulo do Azure PowerShell versão 1.0 ou posterior. Execute `Get-Module -ListAvailable Az` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos do Azure com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. 

O seguinte comando cria um grupo de recursos chamado *myResourceGroup* na localização Oeste dos EUA:

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location WestUS
```

## <a name="create-an-api-management-service"></a>Criar um serviço de Gerenciamento de API

Agora que você tem um grupo de recursos, você pode criar uma instância de serviço do Gerenciamento de API. Crie uma usando [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) e forneça um nome de serviço e detalhes do editor. O nome do serviço precisa ser único no Azure.

No exemplo a seguir, *myapim* é usado para o nome do serviço. Atualize o nome para um valor exclusivo. Também atualize o nome da organização do editor da API e o endereço de email do administrador para receber notificações.

Por padrão, o comando cria a instância na camada Desenvolvedor, uma opção econômica para avaliar o Gerenciamento de API do Azure. Essa camada não é para uso em produção. Para obter mais informações sobre a colocação em escala das camadas de Gerenciamento de API, confira [atualizar e dimensionar](upgrade-and-scale.md).

> [!NOTE]
> Essa é uma operação de execução prolongada. A criação e a ativação de um serviço de Gerenciamento de API nessa camada leva de 30 a 40 minutos.

```azurepowershell-interactive
New-AzApiManagement -Name "myapim" -ResourceGroupName "myResourceGroup" `
  -Location "West US" -Organization "Contoso" -AdminEmail "admin@contoso.com" 
```

Quando o comando retornar, execute [Get-AzApiManagement](/powershell/module/az.apimanagement/get-azapimanagement) para exibir as propriedades do serviço de Gerenciamento de API do Azure. Após a ativação, o status de provisionamento será Bem-sucedido e a instância do serviço terá várias URLs associadas. Por exemplo:

```azurepowershell-interactive
Get-AzApiManagement -Name "myapim" -ResourceGroupName "myResourceGroup" 
```

Saída de exemplo:

```console
PublicIPAddresses                     : {203.0.113.1}
PrivateIPAddresses                    :
Id                                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.ApiManagement/service/myapim
Name                                  : myapim
Location                              : West US
Sku                                   : Developer
Capacity                              : 1
CreatedTimeUtc                        : 9/9/2020 9:07:43 PM
ProvisioningState                     : Succeeded
RuntimeUrl                            : https://myapim.azure-api.net
RuntimeRegionalUrl                    : https://myapi-westus-01.regional.azure-api.net
PortalUrl                             : https://myapim.portal.azure-api.net
DeveloperPortalUrl                    : https://myapim.developer.azure-api.net
ManagementApiUrl                      : https://myapim.management.azure-api.net
ScmUrl                                : https://myapim.scm.azure-api.net
PublisherEmail                        : admin@contoso.com
OrganizationName                      : Contoso
NotificationSenderEmail               : apimgmt-noreply@mail.windowsazure.com
VirtualNetwork                        :
VpnType                               : None
PortalCustomHostnameConfiguration     :
ProxyCustomHostnameConfiguration      : {myapim.azure-api.net}
ManagementCustomHostnameConfiguration :
ScmCustomHostnameConfiguration        :
DeveloperPortalHostnameConfiguration  :
SystemCertificates                    :
Tags                                  : {}
AdditionalRegions                     : {}
SslSetting                            : Microsoft.Azure.Commands.ApiManagement.Models.PsApiManagementSslSetting
Identity                              :
EnableClientCertificate               :
ResourceGroupName                     : myResourceGroup

```

Depois que sua instância de serviço de Gerenciamento de API tiver sido implantada, você estará pronto para usá-la. Comece com o tutorial para [importar e publicar sua primeira API](import-and-publish.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, você poderá usar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Importar e publicar sua primeira API](import-and-publish.md)
