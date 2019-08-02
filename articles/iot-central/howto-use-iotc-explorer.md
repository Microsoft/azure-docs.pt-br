---
title: Monitorar a conectividade de dispositivo usando o Azure IoT Central Explorer
description: Monitore as mensagens do dispositivo e observe as alterações do dispositivo gêmeo por meio da CLI do IoT Central Explorer.
author: viv-liu
ms.author: viviali
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 4d17f0e5273c7397bd9c6a71d14b7992d8652768
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165873"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>Monitorar a conectividade de dispositivo usando o Azure IoT Central Explorer

*Este tópico aplica-se a construtores e administradores.*

Use a CLI do IoT Central Explorer para ver as mensagens que os dispositivos estão enviando para o IoT Central e observe as alterações no dispositivo gêmeo do Hub IoT. É possível usar essa ferramenta de software livre para obter informações mais detalhadas sobre o estado da conectividade de dispositivo e diagnosticar problemas de mensagens do dispositivo que não alcançam a nuvem ou dispositivos que não estão respondendo a alterações do dispositivo gêmeo.

[Visite o repositório iotc explorer no GitHub.](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>Pré-requisitos

+ Node.js versão 8.x ou superior - https://nodejs.org
+ Um administrador do seu aplicativo deve gerar um token de acesso para uso no Gerenciador de iotc

## <a name="install-iotc-explorer"></a>Instale o Gerenciador de iotc

Para instalar, execute o comando a seguir na linha de comando:

```cmd/sh
npm install -g iotc-explorer
```

> [!NOTE]
> Normalmente, você precisará executar o comando de instalação com `sudo` em ambientes Unix-like.

## <a name="run-iotc-explorer"></a>Executar o Gerenciador de iotc

As seções a seguir descrevem comum de comandos e opções que você pode usar ao executar `iotc-explorer`. Para exibir o conjunto completo de comandos e opções, passe `--help` para `iotc-explorer` ou qualquer um dos seus subcomandos.

### <a name="login"></a>Logon

Antes de começar, é necessário ter um administrador do aplicativo IoT Central para obter um token de acesso para você usar. O administrador executa as etapas a seguir:

1. Navegue até **Administration** , em seguida, **Tokens de acesso**.
1. Selecione **gerar Token**.
    ![Captura de tela da página de token de acesso](media/howto-use-iotc-explorer/accesstokenspage.png)

1. Insira um nome de Token, selecione **próxima**e então **cópia**.
    > [!NOTE]
    > O valor do token é mostrado apenas uma vez, portanto, ele deverá ser copiado antes de fechar a caixa de diálogo. Depois de fechar a caixa de diálogo, ele nunca é mostrado novamente.

    ![Copiar captura de tela do token de acesso](media/howto-use-iotc-explorer/copyaccesstoken.png)

Você pode usar o token para fazer logon na CLI da seguinte maneira:

```cmd/sh
iotc-explorer login "<Token value>"
```

Se você preferir não ter o token persistentes em seu histórico de shell, você pode deixar o token de saída e em vez disso, forneça-a quando solicitado:

```cmd/sh
iotc-explorer login
```

### <a name="monitor-device-messages"></a>Monitorar mensagens de dispositivo

É possível inspecionar as mensagens provenientes de um dispositivo específico ou de todos os dispositivos no aplicativo usando o comando `monitor-messages`. Esse comando inicia um inspetor que gera continuamente novas mensagens assim que chegarem:

Para inspecionar todos os dispositivos no aplicativo, execute o comando a seguir:

```cmd/sh
iotc-explorer monitor-messages
```

Saída:

![monitor-messages command output](media/howto-use-iotc-explorer/monitormessages.png)

Para assistir a um dispositivo específico, basta adicione a id do dispositivo para o final do comando:

```cmd/sh
iotc-explorer monitor-messages <your-device-id>
```

Você também poderá gerar um formato mais amigável para o computador adicionando as `--raw` opção para o comando:

```
iotc-explorer monitor-messages --raw
```

### <a name="get-device-twin"></a>Obter dispositivo gêmeo

É possível usar o comando `get-twin` para obter o conteúdo do dispositivo gêmeo de um dispositivo IoT Central. Para fazer isso, execute o comando a seguir:

```cmd/sh
iotc-explorer get-twin <your-device-id>
```

Saída:

![get-twin command output](media/howto-use-iotc-explorer/getdevicetwin.png)

Tal como em `monitor-messages`, você pode obter uma saída amigável do computador, passando a opção `--raw`:

```cmd/sh
iotc-explorer get-twin <your-device-id> --raw
```

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como usar o Gerenciador de IoT Central, a próxima etapa sugerida é explorar [gerenciamento de dispositivos IoT Central](howto-manage-devices.md).
