---
title: Gerenciar política de gravação-Azure
description: Este tópico explica como gerenciar a política de gravação.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: ec72f28496c1392b9d95134c343e1892998a0c28
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99224982"
---
# <a name="manage-recording-policy"></a>Gerenciar política de gravação

Você pode usar a análise de vídeo ao vivo em IoT Edge para [gravação contínua de vídeo](continuous-video-recording-concept.md), na qual você pode gravar vídeo na nuvem por semanas ou meses. Você pode gerenciar o tamanho (em dias) desse arquivo de nuvem usando as [ferramentas de gerenciamento de ciclo de vida](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal) internas no armazenamento do Azure.  

Sua conta de serviço de mídia está vinculada a uma conta de armazenamento do Azure e, ao gravar vídeo na nuvem, o conteúdo é gravado em um [ativo](../latest/assets-concept.md)de serviço de mídia. Cada ativo é mapeado para um contêiner na conta de armazenamento. O gerenciamento do ciclo de vida permite que você defina uma [política](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#policy) para uma conta de armazenamento, onde você pode especificar uma [regra](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#rules) como a seguinte.

```
{
  "rules": [
    {
      "name": "NinetyDayRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "delete": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

A regra acima:

* Aplica-se a todos os blobs de blocos na conta de armazenamento.
* Especifica que, quando os BLOBs envelhecem além de 30 dias, eles são movidos da [camada de acesso quente para fria](../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal).
* E quando os BLOBs envelhecem além de 90 dias, eles devem ser excluídos.

Quando você usa a análise de vídeo ao vivo para gravar em um ativo, você especifica uma `segmentLength` propriedade que informa ao módulo para agregar uma duração mínima do vídeo (em segundos) antes que ele seja gravado na nuvem. Seu ativo conterá uma série de segmentos, cada um com um carimbo de data/hora de criação `segmentLength` mais recente do que o anterior. Quando a política de gerenciamento do ciclo de vida é iniciada, ela exclui segmentos anteriores ao limite especificado. No entanto, você continuará a ser capaz de acessar e reproduzir os segmentos restantes por meio de APIs de serviço de mídia. Para obter mais informações, consulte [reproduzir gravações](playback-recordings-how-to.md). 

## <a name="limitations"></a>Limitações

A seguir estão algumas limitações conhecidas do gerenciamento do ciclo de vida:

* Você pode ter no máximo 100 regras dentro da política e cada regra pode especificar até 10 contêineres. Portanto, se precisar ter diferentes políticas de gravação (por exemplo, arquivo de três dias para a câmera voltada para o estacionamento, 30 dias para a câmera no encaixe de carga e 180 dias para a câmera por trás do contador de check-out), com uma conta de serviço de mídia, você poderá personalizar as regras para no máximo 1000 câmeras.
* As atualizações de política de gerenciamento de ciclo de vida não são imediatas. Consulte [esta seção de perguntas frequentes](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#faq) para obter mais detalhes.
* Se você optar por aplicar uma política em que os BLOBs são movidos para a camada fria, a reprodução dessa parte do arquivo pode ser afetada. Você pode ver latências adicionais ou erros esporádicos. Os serviços de mídia não oferecem suporte à reprodução de conteúdo na camada de arquivo morto.

## <a name="next-steps"></a>Próximas etapas

[Reprodução de gravações](playback-recordings-how-to.md)
