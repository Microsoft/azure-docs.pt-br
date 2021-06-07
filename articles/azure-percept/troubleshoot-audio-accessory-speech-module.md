---
title: Solucionar problemas com o áudio Percept do Azure e o módulo de fala
description: Obter dicas de solução de problemas de áudio e azureearspeechclientmodule do Azure Percept
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: c4fc7d7564ecd30326fbec832639b2a81d55e6d5
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605647"
---
# <a name="azure-percept-audio-and-speech-module-troubleshooting"></a>Solução de problemas do módulo de áudio e fala do Azure Percept

Use as diretrizes abaixo para solucionar problemas do aplicativo assistente de voz.

## <a name="collecting-speech-module-logs"></a>Coletando logs do módulo de fala

Para executar esses comandos, use o [ssh no kit de desenvolvimento](./how-to-ssh-into-percept-dk.md) e insira os comandos no prompt do cliente SSH.

Coletar logs do módulo de fala:

```console
sudo iotedge logs azureearspeechclientmodule
```

Para redirecionar a saída para um arquivo. txt para análise adicional, use a seguinte sintaxe:

```console
sudo [command] > [file name].txt
```

Altere as permissões do arquivo. txt para que ele possa ser copiado:

```console
sudo chmod 666 [file name].txt
```

Depois de redirecionar a saída para um arquivo. txt, copie o arquivo para o computador host por meio do SCP:

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

[caminho do arquivo de host local] refere-se ao local no computador host para o qual você gostaria de copiar o arquivo. txt. [nome de usuário remoto] é o nome de usuário SSH escolhido durante a [experiência de instalação](./quickstart-percept-dk-set-up.md).

## <a name="checking-runtime-status-of-the-speech-module"></a>Verificando o status do tempo de execução do módulo de fala

Verifique se o status de tempo de execução de **azureearspeechclientmodule** é mostrado como **em execução**. Para localizar o status de tempo de execução dos seus módulos de dispositivo, abra o [portal do Azure](https://portal.azure.com/) e navegue até **todos os recursos**  ->  **[seu hub IOT]**  ->  **IOT Edge**  ->  **[sua ID de dispositivo]**. Clique na guia **módulos** para ver o status de tempo de execução de todos os módulos instalados.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Página de dispositivo do Edge no portal do Azure.":::

Se o status de tempo de execução de **azureearspeechclientmodule** não estiver listado como **em execução**, clique em **definir módulos**  ->  **azureearspeechclientmodule**. Na página **configurações do módulo** , defina **o status desejado** como **em execução** e clique em **Atualizar**.

## <a name="understanding-ear-som-led-indicators"></a>Entendendo indicadores de LED de SoM Ear

Você pode usar indicadores de LED para entender em qual estado seu dispositivo está. Normalmente, leva cerca de 2 minutos para que o módulo seja inicializado completamente após o dispositivo ligar. À medida que ele passa pelas etapas de inicialização, você verá:

1. LED do centro branco ativado (estático): o dispositivo está ligado.
2. LED de centro-branco aceso (piscando): a autenticação está em andamento.
3. Todos os três LEDs serão alterados para azul quando o dispositivo for autenticado e estiver pronto para uso.

|LED|Estado do LED|Status de SoM Ear|
|---|---------|--------------|
|L02|1x branco, estático em|Ligue |
|L02|1x branco, 0,5 Hz piscando|Autenticação em andamento |
|L01 & L02 & L03|azul-3x, estático ativado|Aguardando palavra-chave|
|L01 & L02 & L03|Matriz de LED piscando, 20fps |Ouvindo ou falando|
|L01 & L02 & L03|Corrida de matriz LED, 20fps|Pensando|
|L01 & L02 & L03|triplo vermelho, estático em |Mute|

## <a name="next-steps"></a>Próximas etapas

Consulte o [Guia de solução de problemas geral](./troubleshoot-dev-kit.md) para obter mais informações sobre como solucionar problemas do Azure Percept DK.
