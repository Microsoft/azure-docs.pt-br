---
ms.openlocfilehash: c2b1e1d87600e83733eb8bdbf5bc6e2e63376cf4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993066"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Localizar uma âncora espacial de nuvem

Ser capaz de localizar uma âncora espacial de nuvem salva anteriormente é um dos principais motivos para usar as Âncoras Espaciais do Azure. Há várias maneiras de localizar uma âncora espacial de nuvem. Você pode usar uma estratégia em um observador por vez.
- Localizar âncoras por identificador.
- Localizar âncoras conectadas a uma âncora localizada anteriormente. Saiba mais sobre as relações entre âncoras [aqui](../articles/spatial-anchors/concepts/anchor-relationships-way-finding.md).
- Localizar âncora usando a [Relocalização grosseira](../articles/spatial-anchors/concepts/coarse-reloc.md).

> [!NOTE]
> Cada vez que você localizar uma âncora, as Âncoras Espaciais do Azure tentarão usar os dados do ambiente coletados para aumentar as informações visuais na âncora. Se você tiver problemas para localizar uma âncora, poderá ser útil criar uma âncora e, em seguida, localizá-la várias vezes de diferentes ângulos e condições de iluminação.

Se estiver localizando âncoras espaciais de nuvem pelo identificador, você desejará armazenar o identificador da âncora espacial de nuvem no serviço de back-end do aplicativo e torná-lo acessível para todos os dispositivos que puderem se autenticar nele corretamente. Para ver um exemplo disso, confira [Tutorial: Compartilhar âncoras espaciais entre dispositivos](../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md).

Crie uma instância de um objeto `AnchorLocateCriteria`, defina os identificadores que está procurando e invoque o método `CreateWatcher` na sessão fornecendo seu `AnchorLocateCriteria`.