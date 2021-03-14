---
title: Habilitar o acesso privado com o link privado (visualização)-CLI
titleSuffix: Azure Digital Twins
description: Consulte Como habilitar o acesso privado para soluções de gêmeos digitais do Azure com o link privado, usando o CLI do Azure.
author: baanders
ms.author: baanders
ms.date: 02/09/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5bd7ffda508980a9a56d86037887fc53a0fed640
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102202936"
---
# <a name="enable-private-access-with-private-link-preview-azure-cli"></a>Habilitar o acesso privado com o link privado (visualização): CLI do Azure

[!INCLUDE [digital-twins-private-link-selector.md](../../includes/digital-twins-private-link-selector.md)]

Este artigo descreve as diferentes maneiras de [habilitar o link privado com um ponto de extremidade privado para uma instância do gêmeos digital do Azure](concepts-security.md#private-network-access-with-azure-private-link-preview) (atualmente em visualização). Configurar um ponto de extremidade privado para sua instância do gêmeos digital do Azure permite proteger sua instância do gêmeos digital do Azure e eliminar a exposição pública, bem como evitar dados vazamento de sua [rede virtual do Azure (VNet)](../virtual-network/virtual-networks-overview.md).

Este artigo percorre o processo usando o [**CLI do Azure**](/cli/azure/what-is-azure-cli).

Aqui estão as etapas abordadas neste artigo: 
1. Ative o link privado e configure um ponto de extremidade privado para uma instância de gêmeos digital do Azure.
1. Desabilitar ou habilitar sinalizadores de acesso à rede pública para restringir o acesso à API somente para conexões de vínculo privado.

## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar um ponto de extremidade privado, você precisará de uma [**rede virtual do Azure (VNet)**](../virtual-network/virtual-networks-overview.md) em que o ponto de extremidade pode ser implantado. Se você ainda não tiver uma VNet, poderá seguir um dos [guias de início rápido](../virtual-network/quick-create-portal.md) da rede virtual do Azure para configurá-la.

## <a name="manage-private-endpoints-for-an-azure-digital-twins-instance"></a>Gerenciar pontos de extremidade privados para uma instância de gêmeos digital do Azure 

Ao usar o [CLI do Azure](/cli/azure/what-is-azure-cli), você pode configurar pontos de extremidade privados com o link privado em uma instância do gêmeos digital do Azure que já existe (ele não pode ser adicionado como parte da criação da instância). Em seguida, você pode continuar a exibir e gerenciá-los por meio de comandos adicionais da CLI. 

>[!TIP]
> Você também pode configurar um ponto de extremidade de link privado por meio do serviço de link privado, em vez de por meio de sua instância de gêmeos digital do Azure. Isso também fornece as mesmas opções de configuração e o mesmo resultado final.
>
> Para obter mais detalhes sobre como configurar recursos de link privado, consulte a documentação de link privado para o [portal do Azure](../private-link/create-private-endpoint-portal.md), [CLI do Azure](../private-link/create-private-endpoint-cli.md), [modelos de ARM](../private-link/create-private-endpoint-template.md)ou [PowerShell](../private-link/create-private-endpoint-powershell.md).

### <a name="add-a-private-endpoint-to-an-existing-instance"></a>Adicionar um ponto de extremidade privado a uma instância existente

Para criar um ponto de extremidade privado e vinculá-lo a uma instância do gêmeos digital do Azure, use o comando [**AZ Network Private-Endpoint Create**](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) . Identifique a instância do gêmeos digital do Azure usando sua ID totalmente qualificada no `--private-connection-resource-id` parâmetro.

Aqui está um exemplo que usa o comando para criar um ponto de extremidade privado, com apenas os parâmetros necessários.

```azurecli-interactive
az network private-endpoint create --connection-name {private_link_service_connection} -n {name_for_private_endpoint} -g {resource_group} --subnet {subnet_ID} --private-connection-resource-id "/subscriptions/{subscription_ID}/resourceGroups/{resource_group}/providers/Microsoft.DigitalTwins/digitalTwinsInstances/{Azure_Digital_Twins_instance_name}" 
```

Para obter uma lista completa dos parâmetros obrigatórios e opcionais, bem como exemplos de criação de ponto de extremidade privados, consulte a [documentação **AZ Network Private-Endpoint Create** Reference](/cli/azure/network/private-endpoint#az_network_private_endpoint_create).

### <a name="manage-private-endpoint-connections-on-the-instance"></a>Gerenciar conexões de ponto de extremidade privado na instância

Depois que um ponto de extremidade privado tiver sido criado para sua instância de gêmeos digital do Azure, você poderá usar os comandos de [**conexão AZ DT Network Private-Endpoint**](/cli/azure/ext/azure-iot/dt/network/private-endpoint/connection) para continuar Gerenciando **conexões** de ponto de extremidade privado com relação à instância. As operações incluem:
* Mostrar uma conexão de ponto de extremidade particular
* Definir o estado da conexão de ponto de extremidade privado
* Excluir a conexão de ponto de extremidade privado
* Listar todas as conexões de ponto de extremidade privado para uma instância

Para obter mais informações e exemplos, consulte a documentação de referência do [ **ponto de extremidade AZ DT Network Private-Endpoint**](/cli/azure/ext/azure-iot/dt/network/private-endpoint).

### <a name="manage-other-private-link-information-on-an-azure-digital-twins-instance"></a>Gerenciar outras informações de link privado em uma instância do gêmeos digital do Azure

Você pode obter informações adicionais sobre o status do link privado de sua instância com os comandos [**AZ DT Network Private-link**](/cli/azure/ext/azure-iot/dt/network/private-link) . As operações incluem:
* Listar links privados associados a uma instância do gêmeos digital do Azure
* Mostrar um link privado associado à instância

Para obter mais informações e exemplos, consulte a [documentação **AZ DT Network Private-link** Reference](/cli/azure/ext/azure-iot/dt/network/private-link).

## <a name="disable--enable-public-network-access-flags"></a>Desabilitar/habilitar sinalizadores de acesso à rede pública

Você pode configurar sua instância de gêmeos digital do Azure para negar todas as conexões públicas e permitir somente conexões por meio de pontos de extremidade privados para aprimorar a segurança de rede. Essa ação é feita com um **sinalizador de acesso à rede pública**. 

Essa política permite restringir o acesso à API somente para conexões de link privado. Quando o sinalizador de acesso à rede pública é definido como *desabilitado*, todas as chamadas à API REST para o plano de dados da instância do gêmeos digital do Azure da nuvem pública retornarão `403, Unauthorized` . Como alternativa, quando a política está definida como *desabilitada* e uma solicitação é feita por meio de um ponto de extremidade privado, a chamada à API terá sucesso.

Este artigo mostra como atualizar o valor do sinalizador de rede usando o [CLI do Azure](/cli/azure/) ou a ferramenta de [comando ARMClient](https://github.com/projectkudu/ARMClient). Para obter instruções sobre como fazer isso com o portal do Azure, consulte a [versão do portal](how-to-enable-private-link-portal.md) deste artigo.

### <a name="use-the-azure-cli"></a>Usar a CLI do Azure

No CLI do Azure, você pode desabilitar ou habilitar o acesso à rede pública adicionando um `--public-network-access` parâmetro ao `az dt create` comando. Embora esse comando também possa ser usado para criar uma nova instância, você pode usá-lo para editar as propriedades de uma instância existente, fornecendo a ela o nome de uma instância que já existe. (Para obter mais informações sobre esse comando, consulte sua [documentação de referência](/cli/azure/ext/azure-iot/dt#ext_azure_iot_az_dt_create) ou as [instruções gerais para configurar uma instância de gêmeos digital do Azure](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)).

Para **desabilitar** o acesso à rede pública para uma instância do gêmeos digital do Azure, use o `--public-network-access` parâmetro como este:

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --public-network-access Disabled
```

Para **habilitar** o acesso à rede pública em uma instância em que ela está desabilitada, use o seguinte comando semelhante:

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --public-network-access Enabled
```

### <a name="usethe-armclientcommand-tool"></a>Usar a ferramenta de comando ARMClient 

Com a [ferramenta de comando ARMClient](https://github.com/projectkudu/ARMClient), o acesso à rede pública é habilitado ou desabilitado usando os comandos a seguir. 

Para **desabilitar** o acesso à rede pública:
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'disabled' } }"  
```

Para **habilitar** o acesso à rede pública:  
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'enabled' } }"  
``` 

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o link privado para o Azure: 
* [*O que é o serviço de vínculo privado do Azure?*](../private-link/private-link-service-overview.md)