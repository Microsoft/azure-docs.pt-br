---
title: Monitorar a conectividade de dispositivo usando o Azure IoT Central Explorer
description: Monitore as mensagens do dispositivo e observe as alterações do dispositivo gêmeo por meio da CLI do IoT Central Explorer.
author: viv-liu
ms.author: viviali
ms.date: 03/27/2020
ms.topic: how-to
ms.service: iot-central
ms.custom: devx-track-azurecli, device-developer
services: iot-central
manager: corywink
ms.openlocfilehash: b266def0dbe81542714fc6d47986f3cde8235dd0
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92122953"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>Monitorar a conectividade do dispositivo usando a CLI do Azure

*Este tópico se aplica a desenvolvedores de dispositivos e construtores de solução.*

Use a extensão CLI do Azure IoT para ver as mensagens que seus dispositivos estão enviando para IoT Central e observar alterações no dispositivo. Você pode usar essa ferramenta para depurar e observar a conectividade do dispositivo e diagnosticar problemas de mensagens do dispositivo que não estão atingindo a nuvem ou os dispositivos que não estão respondendo a alterações de conexão.

[Visite a referência de extensões de CLI do Azure para obter mais detalhes](/cli/azure/ext/azure-iot/iot/central?view=azure-cli-latest)

## <a name="prerequisites"></a>Pré-requisitos

+ CLI do Azure instalado e é a versão 2.7.0 ou superior. Verifique a versão do seu CLI do Azure executando `az --version` . Saiba como instalar e atualizar a partir do [CLI do Azure docs](/cli/azure/install-azure-cli)
+ Uma conta corporativa ou de estudante no Azure, adicionada como um usuário em um aplicativo IoT Central.

## <a name="install-the-iot-central-extension"></a>Instalar a extensão de IoT Central

Para instalar, execute o comando a seguir na linha de comando:

```azurecli
az extension add --name azure-iot
```

Verifique a versão da extensão executando:

```azurecli
az --version
```

Você deve ver que a extensão Azure-IOT é 0.9.9 ou superior. Se não estiver, execute:

```azurecli
az extension update --name azure-iot
```

## <a name="using-the-extension"></a>Usar a extensão

As seções a seguir descrevem comandos e opções comuns que você pode usar ao executar o `az iot central` . Para exibir o conjunto completo de comandos e opções, passe `--help` para `az iot central` ou qualquer um de seus subcomandos.

### <a name="login"></a>Logon

Comece entrando no CLI do Azure. 

```azurecli
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>Obter a ID do aplicativo IoT Central aplicativo
Em **Administração/configurações do aplicativo**, copie a **ID do aplicativo**. Você usará esse valor em etapas posteriores.

### <a name="monitor-messages"></a>Monitorar mensagens
Monitore as mensagens que estão sendo enviadas para seu aplicativo IoT Central de seus dispositivos. A saída inclui todos os cabeçalhos e anotações.

```azurecli
az iot central diagnostics monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>Exibir propriedades do dispositivo
Exibir as propriedades do dispositivo atual de leitura e leitura/gravação para um determinado dispositivo.

```azurecli
az iot central device twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>Próximas etapas

Se você for um desenvolvedor de dispositivos, uma próxima etapa sugerida é ler sobre a [conectividade do dispositivo no Azure IOT central](./concepts-get-connected.md).