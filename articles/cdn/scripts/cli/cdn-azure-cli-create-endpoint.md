---
title: Criar um perfil e um ponto de extremidade da CDN (Rede de Distribuição de Conteúdo) do Azure usando a CLI do Azure
description: Scripts de exemplo da CLI do Azure para criar um perfil, um ponto de extremidade, um grupo de origem, uma origem e um domínio personalizado da CDN do Azure.
author: asudbring
ms.author: allensu
manager: danielgi
ms.date: 03/09/2021
ms.topic: sample
ms.service: azure-cdn
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: ce01c1f851a5dbaedc85d848b12bf0b0831a16ef
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104723427"
---
# <a name="create-an-azure-cdn-profile-and-endpoint-using-the-azure-cli"></a>Criar um perfil e um ponto de extremidade da CDN do Azure usando a CLI do Azure

Como alternativa ao portal do Azure, você pode usar estes scripts de exemplo da CLI do Azure para gerenciar as seguintes operações da CDN:

- Criar um perfil da CDN.
- Criar um novo ponto de extremidade do CDN.
- Criar um grupo de origem da CDN e torná-lo o grupo padrão.
- Criar uma origem da CDN.
- Criar um domínio personalizado e habilitar o HTTPS.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="sample-scripts"></a>Scripts de exemplo

Caso ainda não tenha um grupo de recursos para seu perfil da CDN, crie-o com o comando `az group create`:

```azurecli
# Create a resource group to use for the CDN.
az group create --name MyResourceGroup --location eastus

```

O seguinte script da CLI do Azure cria um perfil e um ponto de extremidade da CDN:

```azurecli
# Create a CDN profile.
az cdn profile create --resource-group MyResourceGroup --name MyCDNProfile --sku Standard_Microsoft

# Create a CDN endpoint.
az cdn endpoint create --resource-group MyResourceGroup --name MyCDNEndpoint --profile-name MyCDNProfile --origin www.contoso.com

```

O seguinte script da CLI do Azure cria um grupo de origem da CDN, define o grupo de origem padrão para um ponto de extremidade e cria uma origem:

```azurecli
# Create an origin group.
az cdn origin-group create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyOriginGroup --origins origin-0

# Make the origin group the default group of an endpoint.
az cdn endpoint update --resource-group MyResourceGroup --name MyCDNEndpoint --profile-name MyCDNProfile --default-origin-group MyOriginGroup
                           
# Create another origin for an endpoint.
az cdn origin create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name origin-1 --host-name example.contoso.com

```

O script da CLI do Azure a seguir cria um domínio personalizado da CDN e habilita o HTTPS. Para associar um domínio personalizado a um ponto de extremidade da CDN do Azure, crie um registro CNAME (nome canônico) com o DNS do Azure ou seu provedor DNS para apontá-lo para o ponto de extremidade da CDN. Para obter mais informações, consulte [criar um registro DNS CNAME](../../../cdn/cdn-map-content-to-custom-domain.md#create-a-cname-dns-record).

```azurecli
# Associate a custom domain with an endpoint.
az cdn custom-domain create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyCustomDomain --hostname www.example.com

# Enable HTTPS on the custom domain.
az cdn custom-domain enable-https --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyCustomDomain

```

## <a name="clean-up-resources"></a>Limpar recursos

Depois de concluir a execução dos scripts de exemplo, use o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele.

```azurecli
# Delete the resource group.
az group delete --name MyResourceGroup

```

## <a name="azure-cli-commands-used-in-this-article"></a>Comandos da CLI do Azure usados neste artigo

- [az cdn endpoint create](/cli/azure/cdn/endpoint#az_cdn_endpoint_create)
- [az cdn endpoint update](/cli/azure/cdn/endpoint#az_cdn_endpoint_update)
- [az cdn origin create](/cli/azure/cdn/origin#az_cdn_origin_create)
- [az cdn origin-group create](/cli/azure/cdn/origin-group#az_cdn_origin_group_create)
- [az cdn profile create](/cli/azure/cdn/profile#az_cdn_profile_create)
- [az group create](/cli/azure/group#az_group_create)
- [az group delete](/cli/azure/group#az_group_delete)
- [az cdn custom-domain create](/cli/azure/cdn/custom-domain#az_cdn_custom_domain_create)
- [az cdn custom-domain enable-https](/cli/azure/cdn/custom-domain#az_cdn_custom_domain_enable_https)
