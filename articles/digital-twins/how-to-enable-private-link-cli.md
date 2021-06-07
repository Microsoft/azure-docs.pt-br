---
title: Habilitar o acesso privado com o Link Privado (versão prévia) – CLI
titleSuffix: Azure Digital Twins
description: Veja como habilitar o acesso privado para soluções dos Gêmeos Digitais do Azure com link privado usando a CLI do Azure.
author: baanders
ms.author: baanders
ms.date: 02/09/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 4dab08983fc1348ca49e728a65d48aa65fe19a47
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105707"
---
# <a name="enable-private-access-with-private-link-preview-azure-cli"></a>Habilitar o acesso privado com o link privado (versão prévia): CLI do Azure

[!INCLUDE [digital-twins-private-link-selector.md](../../includes/digital-twins-private-link-selector.md)]

Este artigo descreve as diferentes maneiras de [habilitar o link privado com um ponto de extremidade privado para uma instância dos Gêmeos Digitais do Azure](concepts-security.md#private-network-access-with-azure-private-link-preview) (no momento em versão prévia). Configurar um ponto de extremidade privado para sua instância dos Gêmeos Digitais do Azure permite proteger sua instância dos Gêmeos Digitais do Azure e eliminar a exposição pública, assim como evitar a exfiltração dos dados da sua [VNet (Rede Virtual do Azure)](../virtual-network/virtual-networks-overview.md).

Esse artigo explica o processo usando a [**CLI do Azure**](/cli/azure/what-is-azure-cli).

Aqui estão as etapas abordadas em detalhes nesse artigo: 
1. Ative o Link Privado e configure um ponto de extremidade privado para uma instância dos Gêmeos Digitais do Azure.
1. Desabilite ou habilite os sinalizadores de acesso à rede pública para restringir o acesso à API somente para conexões de Link Privado.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar um ponto de extremidade privado, você precisará de uma [**VNet (Rede Virtual do Azure)** ](../virtual-network/virtual-networks-overview.md) em que o ponto de extremidade pode ser implantado. Se você ainda não tiver uma VNet, poderá seguir um dos [guias de início rápido](../virtual-network/quick-create-portal.md) da Rede Virtual do Azure para configurá-la.

## <a name="manage-private-endpoints-for-an-azure-digital-twins-instance"></a>Gerenciar pontos de extremidade privados para uma instância dos Gêmeos Digitais do Azure 

Ao usar a [CLI do Azure](/cli/azure/what-is-azure-cli), você pode configurar pontos de extremidade privados com o Link Privado em uma instância dos Gêmeos Digitais do Azure que já existe (ela não pode ser adicionada como parte da criação da instância). Em seguida, você pode continuar a ver e gerenciá-los por meio de comandos adicionais da CLI. 

>[!TIP]
> Você também pode configurar um ponto de extremidade de Link Privado por meio do serviço de Link Privado, em vez de por meio de sua instância dos Gêmeos Digitais do Azure. Isso também fornece as mesmas opções de configuração e o mesmo resultado final.
>
> Para obter mais detalhes sobre como configurar recursos de Link Privado, confira a documentação do Link Privado para o [portal do Azure](../private-link/create-private-endpoint-portal.md), a [CLI do Azure](../private-link/create-private-endpoint-cli.md), os [modelos do ARM](../private-link/create-private-endpoint-template.md) ou o [PowerShell](../private-link/create-private-endpoint-powershell.md).

### <a name="add-a-private-endpoint-to-an-existing-instance"></a>Adicionar um ponto de extremidade privado a uma instância existente

Para criar um ponto de extremidade privado e vinculá-lo a uma instância dos Gêmeos Digitais do Azure, use o comando [**az network private-endpoint create**](/cli/azure/network/private-endpoint#az_network_private_endpoint_create). Identifique a instância dos Gêmeos Digitais do Azure usando sua ID totalmente qualificada no parâmetro `--private-connection-resource-id`.

Veja um exemplo que usa o comando para criar um ponto de extremidade privado, com apenas os parâmetros necessários.

```azurecli-interactive
az network private-endpoint create --connection-name {private_link_service_connection} -n {name_for_private_endpoint} -g {resource_group} --subnet {subnet_ID} --private-connection-resource-id "/subscriptions/{subscription_ID}/resourceGroups/{resource_group}/providers/Microsoft.DigitalTwins/digitalTwinsInstances/{Azure_Digital_Twins_instance_name}" 
```

Para obter uma lista completa dos parâmetros obrigatórios e opcionais, bem como exemplos de criação de pontos de extremidade privados, confira a [documentação de referência de **az network private-endpoint create**](/cli/azure/network/private-endpoint#az_network_private_endpoint_create).

### <a name="manage-private-endpoint-connections-on-the-instance"></a>Gerenciar conexões de ponto de extremidade privado na instância

Depois que um ponto de extremidade privado tiver sido criado para sua instância dos Gêmeos Digitais do Azure, você poderá usar os comandos de [**az dt network private-endpoint connection**](/cli/azure/dt/network/private-endpoint/connection) para continuar gerenciando **conexões** de ponto de extremidade privado com relação à instância. As operações incluem:
* Mostrar uma conexão de ponto de extremidade privado
* Definir o estado da conexão de ponto de extremidade privado
* Excluir a conexão de ponto de extremidade privado
* Listar todas as conexões de ponto de extremidade privado para uma instância

Para obter mais informações e exemplos, confira a [documentação de referência de **az dt network private-endpoint**](/cli/azure/dt/network/private-endpoint).

### <a name="manage-other-private-link-information-on-an-azure-digital-twins-instance"></a>Gerenciar outras informações de Link Privado em uma instância dos Gêmeos Digitais do Azure

Você pode obter informações adicionais sobre o status do Link Privado de sua instância com os comandos [**az dt network private-link**](/cli/azure/dt/network/private-link). As operações incluem:
* Listar links privados associados a uma instância dos Gêmeos Digitais do Azure
* Mostrar um link privado associado à instância

Para obter mais informações e exemplos, confira a [documentação de referência de **az dt network private-link**](/cli/azure/dt/network/private-link).

## <a name="disable--enable-public-network-access-flags"></a>Desabilitar/habilitar sinalizadores de acesso à rede pública

Você pode configurar sua instância dos Gêmeos Digitais do Azure para negar todas as conexões públicas e permitir somente conexões por meio de pontos de extremidade privados para aprimorar a segurança de rede. Essa ação é feita com um **sinalizador de acesso à rede pública**. 

Essa política permite restringir o acesso à API somente para conexões de Link Privado. Quando o sinalizador de acesso à rede pública é definido como *desabilitado*, todas as chamadas à API REST para o plano de dados da instância dos Gêmeos Digitais do Azure da nuvem pública retornarão `403, Unauthorized`. Como alternativa, quando a política está definida como *desabilitada* e uma solicitação é feita por meio de um ponto de extremidade privado, a chamada à API terá êxito.

Este artigo mostra como atualizar o valor do sinalizador de rede usando a [CLI do Azure](/cli/azure/) ou a [ferramenta de comando ARMClient](https://github.com/projectkudu/ARMClient). Para obter instruções sobre como fazer isso com o portal do Azure, confira a [versão do portal](how-to-enable-private-link-portal.md) desse artigo.

### <a name="use-the-azure-cli"></a>Usar a CLI do Azure

Na CLI do Azure, você pode desabilitar ou habilitar o acesso à rede pública adicionando um parâmetro `--public-network-access` ao comando `az dt create`. Embora esse comando também possa ser usado para criar uma instância, você pode usá-lo para editar as propriedades de uma instância existente fornecendo a ela o nome de uma instância que já existe. (Para obter mais informações sobre esse comando, confira a [documentação de referência](/cli/azure/dt#az_dt_create) ou as [instruções gerais para configurar uma instância dos Gêmeos Digitais do Azure](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)).

Para **desabilitar** o acesso à rede pública para uma instância dos Gêmeos Digitais do Azure, use o parâmetro `--public-network-access` como esse:

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --public-network-access Disabled
```

Para **habilitar** o acesso à rede pública em uma instância em que ela está desabilitada no momento, use o seguinte comando semelhante:

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

Saiba mais sobre o Link Privado para Azure: 
* [*O que é o serviço de Link Privado do Azure?* ](../private-link/private-link-service-overview.md)