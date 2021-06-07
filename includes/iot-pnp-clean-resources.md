---
author: dominicbetts
ms.author: dominicbetts
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: 647226091c3e15a19bf8262c23e62d95018488b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831189"
---
Se planejar continuar com os artigos adicionais de IoT Plug and Play, guarde e use novamente os recursos usados neste artigo. Caso contrário, exclua os recursos criados neste artigo para evitar encargos adicionais.

Você pode excluir o hub e o dispositivo registrado ao mesmo tempo excluindo o grupo de recursos inteiro com o comando da CLI do Azure a seguir. Não use esse comando se esses recursos estiverem compartilhando um grupo de recursos com outros recursos que você desejar manter.

```azurecli-interactive
az group delete --name <YourResourceGroupName>
```

Para excluir apenas o hub IoT, execute o seguinte comando usando a CLI do Azure:

```azurecli-interactive
az iot hub delete --name <YourIoTHubName>
```

Para excluir apenas a identidade do dispositivo registrada no hub IoT, execute o seguinte comando usando a CLI do Azure:

```azurecli-interactive
az iot hub device-identity delete --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Talvez você também queira remover os arquivos de exemplo clonados do seu computador de desenvolvimento.
