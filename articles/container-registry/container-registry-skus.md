---
title: SKUs do Registro de Contêiner do Azure
description: Compare as diferentes camadas de serviço disponíveis no Registro de Contêiner do Azure.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/26/2019
ms.author: danlep
ms.openlocfilehash: 7fdc1417ac524b422a12a087cf1661040efb9f8a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60582783"
---
# <a name="azure-container-registry-skus"></a>SKUs do Registro de Contêiner do Azure

O ACR (Registro de Contêiner do Azure) está disponível em várias camadas de serviço, conhecidas como SKUs. Essas SKUs fornecem preços previsíveis e várias opções de alinhamento aos padrões de capacidade e uso de seu registro de Docker privado no Azure.

| SKU | Gerenciada | DESCRIÇÃO |
| --- | :-------: | ----------- |
| **Básico** | Sim | Um ponto de entrada de otimização de custo para desenvolvedores aprendendo sobre o Registro de Contêiner do Azure. Registros básicos têm os mesmos recursos de programação que Standard e Premium (como o Azure Active Directory [integração de autenticação](container-registry-authentication.md#individual-login-with-azure-ad), [exclusão de imagem][container-registry-delete], e [webhooks][container-registry-webhook]). No entanto, o armazenamento incluído e a taxa de transferência de imagem são mais apropriados para cenários de uso mais baixos. |
| **Standard** | Sim | Os registros Standard oferecem os mesmos recursos do Básico, com maior armazenamento incluído e taxa de transferência de imagem. Registros Standard devem atender às necessidades da maioria dos cenários de produção. |
| **Premium** | Sim | Os registros Premium fornecem a maior quantidade de armazenamento incluído e operações simultâneas, permitindo cenários de alto volume. Além de maior taxa de transferência de imagem, o Premium adiciona recursos, incluindo [replicação geográfica] [ container-registry-geo-replication] para gerenciar um único registro em várias regiões, [confiança (visualização)deconteúdo](container-registry-content-trust.md) para a assinatura de marca de imagem, e [firewalls e redes virtuais (versão prévia)](container-registry-vnet.md) para restringir o acesso ao registro. |
|  Clássico (*não está disponível depois de abril de 2019*) | Não  | Esta SKU permitiu a versão inicial do serviço Registro de Contêiner do Azure no Azure. Os registros Clássicos contam com uma conta de armazenamento que o Azure cria em sua assinatura, o que limita a capacidade do ACR de fornecer recursos de níveis mais altos, como uma maior taxa de transferência e a replicação geográfica. |

> [!IMPORTANT]
> O registro clássico SKU está sendo **preterido**e não estará disponível após **abril de 2019**. É recomendável usar Basic, Standard ou Premium para todos os registros novos. Todos os registros clássicos existentes devem ser atualizados antes de abril de 2019. Para obter informações de atualização, consulte [atualizar um registro clássico][container-registry-upgrade].

Basic, Standard e Premium SKUs (chamados coletivamente *registros gerenciados*) fornecem os mesmos recursos de programação. Eles também todos se beneficiar [armazenamento de imagens] [ container-registry-storage] totalmente gerenciadas pelo Azure. A escolha de uma SKU de nível mais alto oferece mais desempenho e escala. Com várias camadas de serviço, pode começar com o Básico e depois mudar para o Padrão e o Premium à medida que crescer o uso do registro.

## <a name="sku-feature-matrix"></a>Matriz de recurso de SKU

A tabela a seguir fornece detalhes sobre os recursos e os limites das camadas de serviço Básico, Standard e Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Alterando SKUs

Você pode alterar a SKU do registro com o CLI do Azure ou no portal do Azure. Você pode trocar livremente os SKUs gerenciados, contanto que o SKU para o qual muda tenha a capacidade de armazenamento máxima necessária. Quando você alterna para um dos SKUs gerenciados do clássico, você não poderá voltar para o clássico é uma conversão unidirecional.

### <a name="azure-cli"></a>CLI do Azure

Para mover entre os SKUs do CLI do Azure, use o comando [az acr update][az-acr-update]. Por exemplo, para alternar para o Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Portal do Azure

Na **Visão geral** do registro de contêiner no portal do Azure, selecione **Atualizar** e selecione uma nova **SKU** na lista suspensa SKU.

![Atualizar SKU do registro de contêiner no portal do Azure][update-registry-sku]

Se você tiver um registro clássico, não poderá selecionar uma SKU gerenciada no portal do Azure. Em vez disso, você deve primeiro [upgrade] [ container-registry-upgrade] para um registro gerenciado.

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
