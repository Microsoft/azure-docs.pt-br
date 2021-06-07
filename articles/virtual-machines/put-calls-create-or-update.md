---
title: Chamadas PUT para operações de criação ou atualização
description: COLOCAR chamadas para operações de criação ou atualização em recursos de computação
author: mimckitt
ms.author: mimckitt
ms.reviewer: cynthn
ms.topic: conceptual
ms.service: virtual-machines
ms.date: 08/4/2020
ms.custom: avverma
ms.openlocfilehash: d6ee4179dce905d637e933743ade7452a2484077
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91978553"
---
# <a name="put-calls-for-creation-or-updates-on-compute-resources"></a>COLOCAR chamadas para criação ou atualizações em recursos de computação

`Microsoft.Compute` os recursos não dão suporte à definição convencional da semântica *Put http* . Em vez disso, esses recursos usam a semântica de PATCH para os verbos PUT e PATCH.

As operações de **criação** aplicam valores padrão quando apropriado. No entanto, **as atualizações** de recursos feitas por meio de Put ou patch não aplicam nenhuma propriedade padrão. As operações de **atualização** aplicam-se aplicar semântica de patch estrito.

Por exemplo, a `caching` propriedade de disco de uma máquina virtual usa como padrão `ReadWrite` se o recurso é um disco do sistema operacional.

```json
    "storageProfile": {
      "osDisk": {
        "name": "myVMosdisk",
        "image": {
          "uri": "http://{existing-storage-account-name}.blob.core.windows.net/{existing-container-name}/{existing-generalized-os-image-blob-name}.vhd"
        },
        "osType": "Windows",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "vhd": {
          "uri": "http://{existing-storage-account-name}.blob.core.windows.net/{existing-container-name}/myDisk.vhd"
        }
      }
    },
```

No entanto, para operações de **atualização** quando uma propriedade é deixada para fora ou um valor *nulo* é passado, ela permanecerá inalterada e não haverá valores padrão.

Isso é importante ao enviar operações de atualização para um recurso com a intenção de remover uma associação. Se esse recurso for um `Microsoft.Compute` recurso, a propriedade correspondente que você deseja remover precisará ser explicitamente chamada e um valor atribuído. Para conseguir isso, os usuários podem passar uma cadeia de caracteres vazia, como **""**. Isso instruirá a plataforma a remover essa associação.

> [!IMPORTANT]
> Não há suporte para "aplicação de patch" em um elemento de matriz. Em vez disso, o cliente precisa fazer uma solicitação PUT ou PATCH com todo o conteúdo da matriz atualizada. Por exemplo, para desanexar um disco de dados de uma VM, faça uma solicitação GET para obter o modelo de VM atual, remova o disco a ser desanexado `properties.storageProfile.dataDisks` e faça uma solicitação Put com a entidade VM atualizada.

## <a name="examples"></a>Exemplos

### <a name="correct-payload-to-remove-a-proximity-placement-groups-association"></a>Carga correta para remover uma associação de grupos de posicionamento de proximidade

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20, "proximityPlacementGroup": "" } }
`

### <a name="incorrect-payloads-to-remove-a-proximity-placement-groups-association"></a>Cargas incorretas para remover uma associação de grupos de posicionamento de proximidade

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20, "proximityPlacementGroup": null } }
`

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20 } }
`

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre criar ou atualizar chamadas para [máquinas virtuais](/rest/api/compute/virtualmachines/createorupdate) e [conjuntos de dimensionamento de máquinas virtuais](/rest/api/compute/virtualmachinescalesets/createorupdate)