---
title: SKUs do Registro de Contêiner do Azure
description: Compare as diferentes camadas de serviço disponíveis no Registro de Contêiner do Azure.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 05/06/2019
ms.author: danlep
ms.openlocfilehash: bf620178a0c10661126b3e52c7b908ccc9a90d89
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311896"
---
# <a name="azure-container-registry-skus"></a>SKUs do Registro de Contêiner do Azure

O ACR (Registro de Contêiner do Azure) está disponível em várias camadas de serviço, conhecidas como SKUs. Essas SKUs fornecem preços previsíveis e várias opções de alinhamento aos padrões de capacidade e uso de seu registro de Docker privado no Azure.

| SKU | Gerenciada | DESCRIÇÃO |
| --- | :-------: | ----------- |
| **Básico** | Sim | Um ponto de entrada de otimização de custo para desenvolvedores aprendendo sobre o Registro de Contêiner do Azure. Os registros básicos têm os mesmos recursos de programação que o Standard e o Premium (como Azure Active Directory [integração de autenticação](container-registry-authentication.md#individual-login-with-azure-ad), [exclusão][container-registry-delete], and [webhooks][container-registry-webhook]de imagem). No entanto, o armazenamento incluído e a taxa de transferência de imagem são mais apropriados para cenários de uso mais baixos. |
| **Standard** | Sim | Os registros Standard oferecem os mesmos recursos do Básico, com maior armazenamento incluído e taxa de transferência de imagem. Registros Standard devem atender às necessidades da maioria dos cenários de produção. |
| **Premium** | Sim | Os registros Premium fornecem a maior quantidade de armazenamento incluído e operações simultâneas, permitindo cenários de alto volume. Além da taxa de transferência de imagem mais alta, o Premium adiciona recursos, incluindo [replicação geográfica][container-registry-geo-replication] para gerenciar um único registro em várias regiões, [confiança de conteúdo](container-registry-content-trust.md) para assinatura de marca de imagem e [firewalls e redes virtuais (visualização)](container-registry-vnet.md) para restringir o acesso ao registro. |
|  Clássico (*não disponível após abril de 2019*) | Não | Esta SKU permitiu a versão inicial do serviço Registro de Contêiner do Azure no Azure. Os registros Clássicos contam com uma conta de armazenamento que o Azure cria em sua assinatura, o que limita a capacidade do ACR de fornecer recursos de níveis mais altos, como uma maior taxa de transferência e a replicação geográfica. |

> [!IMPORTANT]
> O SKU do registro clássico está sendo preterido e não estará disponível depois de **abril de 2019**. É recomendável usar Basic, Standard ou Premium para todos os novos registros. Todos os registros clássicos existentes devem ser atualizados antes de 2019 de abril. Para obter informações de atualização, consulte [atualizar um registro clássico][container-registry-upgrade].

Os SKUs básico, Standard e Premium (coletivamente chamados de *registros gerenciados*) fornecem os mesmos recursos de programação. Todos eles também se beneficiam do [armazenamento de imagem][container-registry-storage] gerenciado inteiramente pelo Azure. A escolha de uma SKU de nível mais alto oferece mais desempenho e escala. Com várias camadas de serviço, pode começar com o Básico e depois mudar para o Padrão e o Premium à medida que crescer o uso do registro.

## <a name="sku-feature-matrix"></a>Matriz de recurso de SKU

A tabela a seguir fornece detalhes sobre os recursos e os limites das camadas de serviço Básico, Standard e Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Alterando SKUs

Você pode alterar a SKU do registro com o CLI do Azure ou no portal do Azure. Você pode trocar livremente os SKUs gerenciados, contanto que o SKU para o qual muda tenha a capacidade de armazenamento máxima necessária. Quando você alterna para um dos SKUs gerenciados do clássico, não pode voltar para o clássico--é uma conversão unidirecional.

### <a name="azure-cli"></a>CLI do Azure

Para mover entre SKUs no CLI do Azure, use o comando [AZ ACR Update][az-acr-update] . Por exemplo, para alternar para o Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Portal do Azure

Na **Visão geral** do registro de contêiner no portal do Azure, selecione **Atualizar** e selecione uma nova **SKU** na lista suspensa SKU.

![Atualizar SKU do registro de contêiner no portal do Azure][update-registry-sku]

Se você tiver um registro clássico, não poderá selecionar uma SKU gerenciada no portal do Azure. Em vez disso, você deve primeiro [Atualizar][container-registry-upgrade] para um registro gerenciado.

## <a name="pricing"></a>Preços

Para saber mais sobre os preços de cada SKU de Registro de Contêiner do Azure, confira [Preços do registro de contêiner][container-registry-pricing].

Para obter detalhes sobre os preços das transferências de dados, confira [Detalhes de Preços de Largura de Banda](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="next-steps"></a>Próximas etapas

**Roteiro do Registro de Contêiner do Azure**

Visite o [Roteiro do ACR][acr-roadmap] no GitHub para encontrar informações sobre os próximos recursos do serviço.

**UserVoice do Registro de Contêiner do Azure**

Envie e vote em sugestões de novos recursos no [UserVoice do ACR][container-registry-uservoice].

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-upgrade]: container-registry-upgrade.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md
