---
title: Parar ou iniciar os contêineres manualmente nas instâncias de contêiner do Azure
description: Saiba como parar manualmente ou iniciar um grupo de contêineres em instâncias de contêiner do Azure.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: c7d46ad8d935e28b5a24e48c85ac2464b55b2669
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325651"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>Parar ou iniciar os contêineres manualmente nas instâncias de contêiner do Azure

A configuração de [diretiva](container-instances-restart-policy.md) de reinicialização de um grupo de contêineres determina como as instâncias de contêiner iniciam ou param por padrão. Você pode substituir a configuração padrão interrompendo ou iniciando manualmente um grupo de contêineres.

## <a name="stop"></a>Stop

Interrompa manualmente um grupo de contêineres em execução-por exemplo, usando o comando [AZ container Stop][az-container-stop] ou portal do Azure. Para determinadas cargas de trabalho de contêiner, talvez você queira interromper um grupo de contêineres de execução longa após um período definido para economizar nos custos. 

*Quando um grupo de contêineres entra no estado parado, ele termina e recicla todos os contêineres no grupo. Ele não preserva o estado do contêiner.*

Quando os contêineres são reciclados, os [recursos](container-instances-container-groups.md#resource-allocation) são desalocados e a cobrança é interrompida para o grupo de contêineres.

A ação de parada não terá efeito se o grupo de contêineres já tiver sido encerrado (está em um estado de êxito ou falha). Por exemplo, um grupo de contêineres com tarefas de contêiner de execução única que foram executadas com êxito termina no estado com êxito. As tentativas de parar o grupo nesse estado não alteram o estado. 

## <a name="start"></a>Início

Quando um grupo de contêineres é interrompido – porque os contêineres terminam sozinhos ou você interrompeu manualmente o grupo – você pode iniciar os contêineres. Por exemplo, use o comando [AZ container Start][az-container-start] ou portal do Azure para iniciar manualmente os contêineres no grupo. Se a imagem de contêiner de qualquer contêiner for atualizada, uma nova imagem será extraída. 

Iniciar um grupo de contêineres inicia uma nova implantação com a mesma configuração de contêiner. Essa ação pode ajudar você a reutilizar rapidamente uma configuração de grupo de contêineres conhecido que funciona conforme o esperado. Você não precisa criar um novo grupo de contêineres para executar a mesma carga de trabalho.

Todos os contêineres em um grupo de contêineres são iniciados por esta ação. Não é possível iniciar um contêiner específico no grupo.

Após você iniciar ou reiniciar manualmente um grupo de contêineres, o grupo de contêineres é executado de acordo com a política de reinicialização configurada.
  
## <a name="restart"></a>Reiniciar

Você pode reiniciar um grupo de contêineres enquanto ele está em execução-por exemplo, usando o comando [AZ container][az-container-restart] restart. Essa ação reinicia todos os contêineres no grupo de contêineres. Se a imagem de contêiner de qualquer contêiner for atualizada, uma nova imagem será extraída. 

Reiniciar um grupo de contêineres é útil quando você deseja solucionar um problema de implantação. Por exemplo, se uma limitação de recursos temporária impedir que seus contêineres sejam executados com êxito, reiniciar o grupo poderá resolver o problema.

Todos os contêineres em um grupo de contêineres são reiniciados por essa ação. Não é possível reiniciar um contêiner específico no grupo.

Depois de reiniciar manualmente um grupo de contêineres, o grupo de contêineres é executado de acordo com a política de reinicialização configurada.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [as configurações de política](container-instances-restart-policy.md) de reinicialização nas instâncias de contêiner do Azure.

Além de parar e iniciar manualmente um grupo de contêineres com a configuração existente, você pode [atualizar as configurações](container-instances-update.md) de um grupo de contêineres em execução.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-start]: /cli/azure/container?view=azure-cli-latest#az-container-start
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop
