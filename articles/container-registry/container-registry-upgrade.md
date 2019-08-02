---
title: Atualizar um registro de contêiner do Azure Clássico
description: Aproveite o conjunto expandido de recursos dos registros de contêiner gerenciados Básico, Standard e Premium atualizando seu registro de contêiner Clássico não gerenciado.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 03/26/2019
ms.author: danlep
ms.openlocfilehash: 05c227e7de078c6bb371049f16e191598b9ca4e5
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310370"
---
# <a name="upgrade-a-classic-container-registry"></a>Atualizar um registro de contêiner Clássico

O ACR (Registro de Contêiner do Azure) está disponível em várias camadas de serviço, [conhecidas como SKUs](container-registry-skus.md). A versão inicial do ACR oferecia um único SKU, o Clássico, que não tinha vários recursos inerentes aos SKUs Básico, Standard e Premium (coletivamente conhecidos como registros *gerenciados*).

O SKU clássico está sendo preterido e não estará disponível depois de abril de 2019. Este artigo fornece detalhes sobre como migrar seu registro Clássico não gerenciado para um dos SKUs gerenciados para que você possa tirar proveito do seu conjunto de recursos aprimorado.

## <a name="why-upgrade"></a>Por que atualizar?

O SKU do registro clássico está sendo preterido e não estará disponível depois de **abril de 2019**. Todos os registros clássicos existentes devem ser atualizados antes de 2019 de abril. Os recursos de gerenciamento do portal de registros clássicos serão divididos em fases. A criação de novos registros clássicos será desabilitada após 2019 de abril.

Devido à preterição planejada e aos recursos limitados de registros não gerenciados clássicos, todos os registros clássicos devem ser atualizados para registros gerenciados (Basic, Standard ou Premium). Essas SKUs de nível superior integram o registro aos recursos do Azure mais profundamente. Para obter mais informações sobre os preços e os recursos das diferentes camadas de serviço, consulte [SKUs do registro de contêiner](container-registry-skus.md).

O registro Clássico depende da conta de armazenamento que o Azure provisionou automaticamente na sua assinatura do Azure quando você criou o registro. Por outro lado, SKUs Básico, Standard e Premium aproveitam os [recursos de armazenamento avançados](container-registry-storage.md) do Azure, manipulando de modo transparente o armazenamento das imagens para você. Uma conta de armazenamento separada não é criada em sua própria assinatura.

Um armazenamento de registro gerenciado oferece os seguintes benefícios:

* As imagens de contêiner são [criptografadas em repouso](container-registry-storage.md#encryption-at-rest).
* As imagens são armazenadas usando o [armazenamento com redundância geográfica](container-registry-storage.md#geo-redundant-storage), garantindo o backup de suas imagens com replicação de várias regiões (somente SKU Premium).
* Capacidade de [movimentação entre SKUs](container-registry-skus.md#changing-skus) de modo livre, permitindo uma maior taxa de transferência ao escolher um SKU de nível superior. Com cada SKU, o ACR pode atender a seus requisitos de taxa de transferência conforme a necessidade.
* O modelo de segurança unificada para o registro e seu armazenamento fornece gerenciamento de direitos simplificado. Você pode gerenciar permissões apenas para o registro de contêiner, sem a necessidade de gerenciar também permissões para uma conta de armazenamento separada.

Para obter detalhes adicionais sobre o armazenamento de imagens no ACR, consulte [Armazenamento de imagens de contêiner no Registro de Contêiner do Azure](container-registry-storage.md).

## <a name="migration-considerations"></a>Considerações sobre a migração

Quando você atualiza um registro clássico para um registro gerenciado, o Azure deve copiar todas as imagens de contêiner existentes da conta de armazenamento criada pelo ACR em sua assinatura para uma conta de armazenamento gerenciada pelo Azure. Dependendo do tamanho do registro, esse processo pode demorar de alguns minutos a várias horas. Para fins de estimativa, espere um tempo de migração de aproximadamente 0,5 GiB por minuto.

Durante o processo de conversão `docker push` , as operações são desabilitadas durante os últimos 10% da migração. `docker pull`continua a funcionar normalmente.

Não exclua nem modifique o conteúdo da conta de armazenamento que dá suporte ao seu registro Clássico durante o processo de conversão. Isso pode resultar na corrupção de suas imagens de contêiner.

Depois que a migração for concluída, a conta de armazenamento em sua assinatura que originalmente fez o backup do registro clássico não será mais usada pelo registro de contêiner do Azure. Depois de verificar que a migração foi bem-sucedida, considere a exclusão da conta de armazenamento para ajudar a minimizar o custo.

>[!IMPORTANT]
> A atualização do Clássico para um dos SKUs gerenciados é um **processo unidirecional**. Depois de converter um registro Clássico para Básico, Standard ou Premium, não será possível reverter para o Clássico. É possível, no entanto, mover livremente entre SKUs gerenciados com uma capacidade suficiente para o registro.

## <a name="how-to-upgrade"></a>Como atualizar

É possível atualizar um registro Clássico não gerenciado para um dos SKUs gerenciados de várias maneiras. Nas seções a seguir, descrevemos o processo de uso do [CLI do Azure][azure-cli] and the [Azure portal][azure-portal].

## <a name="upgrade-in-azure-cli"></a>Atualização na CLI do Azure

Para atualizar um registro clássico no CLI do Azure, execute o comando [AZ ACR Update][az-acr-update] e especifique a nova SKU para o registro. No exemplo a seguir, um registro Clássico denominado *myclassicregistry* é atualizado para o SKU Premium:

```azurecli-interactive
az acr update --name myclassicregistry --sku Premium
```

Quando a migração for concluída, você verá uma saída semelhante à seguinte. Observe que o `sku` é "Premium" e o `storageAccount` is `null`, indicando que o Azure agora gerencia o armazenamento de imagem para esse registro.

```JSON
{
  "adminUserEnabled": false,
  "creationDate": "2017-12-12T21:23:29.300547+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "location": "eastus",
  "loginServer": "myregistry.azurecr.io",
  "name": "myregistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "name": "Premium",
    "tier": "Premium"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Se especificar um SKU de registro gerenciado cuja capacidade máxima é menor que o tamanho do registro Clássico, você receberá uma mensagem de erro semelhante à seguinte.

`Cannot update the registry SKU due to reason: Registry size 12936251113 bytes exceeds the quota value 10737418240 bytes for SKU Basic. The suggested SKU is Standard.`

Se você receber um erro semelhante, execute o comando [AZ ACR Update][az-acr-update] novamente e ESPECIFIQUE o SKU sugerido, que é o próximo SKU de nível mais alto que pode acomodar suas imagens.

## <a name="upgrade-in-azure-portal"></a>Atualizar no Portal do Azure

Quando você atualiza um registro clássico usando o portal do Azure, o Azure seleciona automaticamente o SKU Standard ou Premium, dependendo de qual SKU pode acomodar suas imagens. Por exemplo, se o registro contiver menos de 100 GiB em imagens, o Azure selecionará e converterá automaticamente o registro clássico para Standard (máximo de 100 GiB).

Para atualizar o registro Clássico usando o Portal do Azure, navegue até o registro de contêiner **Visão geral** e selecione **Atualizar para o registro gerenciado**.

![Botão de atualização do registro Clássico na interface do usuário do Portal do Azure][update-classic-01-upgrade]

Selecione **OK** para confirmar que você deseja atualizar um registro gerenciado.

Durante a migração, o portal indica que o **estado de provisionamento** do registro é *Atualizando*. Conforme mencionado anteriormente, `docker push` as operações são desabilitadas durante os últimos 10% da migração. Não exclua nem atualize a conta de armazenamento usada pelo registro clássico enquanto a migração estiver em andamento, fazendo isso pode resultar em corrupção de imagem.

![Progresso da atualização do registro Clássico na interface do usuário do Portal do Azure][update-classic-03-updating]

Quando a migração for concluída, o **estado de provisionamento** indicará *êxito*e você poderá retomar as operações normais com o registro.

![Estado de conclusão da atualização do registro Clássico na interface do usuário do Portal do Azure][update-classic-04-updated]

## <a name="next-steps"></a>Próximas etapas

Depois de atualizar um registro clássico para um registro gerenciado, o Azure não usa mais a conta de armazenamento que originalmente apoiava o registro clássico. Para reduzir o custo, considere a possibilidade de excluir a conta de armazenamento ou o contêiner de Blob dentro da conta que contém as imagens do contêiner antigo.

<!-- IMAGES -->
[update-classic-01-upgrade]: ./media/container-registry-upgrade/update-classic-01-upgrade.png
[update-classic-02-confirm]: ./media/container-registry-upgrade/update-classic-02-confirm.png
[update-classic-03-updating]: ./media/container-registry-upgrade/update-classic-03-updating.png
[update-classic-04-updated]: ./media/container-registry-upgrade/update-classic-04-updated.png

<!-- LINKS - internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[azure-cli]: /cli/azure/install-azure-cli
[azure-portal]: https://portal.azure.com