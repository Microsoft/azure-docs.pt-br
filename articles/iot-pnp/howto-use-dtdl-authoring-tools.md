---
title: Usar uma ferramenta para criar e validar modelos DTDL | Microsoft Docs
description: Instale o editor de DTDL para Visual Studio Code ou o Visual Studio 2019 e use-o para criar modelos de IoT Plug and Play.
author: dominicbetts
ms.author: dobett
ms.date: 09/14/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 40d1ae4da07e159c24970c065d1c39e22b89a29a
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280200"
---
# <a name="install-and-use-the-dtdl-authoring-tools"></a>Instalar e usar as ferramentas de criação do DTDL

Os modelos [DTDL (digital gêmeos Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) são arquivos JSON. Você pode usar uma extensão para o Visual Studio Code ou o Visual Studio 2019 para criar e validar esses arquivos de modelo.

## <a name="install-and-use-the-vs-code-extension"></a>Instalar e usar a extensão de VS Code

A extensão DTDL para VS Code adiciona os seguintes recursos de criação de DTDL:

- Validação de sintaxe DTDL v2.
- O IntelliSense, incluindo o preenchimento automático, para ajudá-lo com a sintaxe da linguagem.
- A capacidade de criar interfaces na paleta de comandos.

Para instalar a extensão DTDL, vá para o [Editor DTDL para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl). Você também pode pesquisar por **DTDL** no modo de exibição de extensões no vs Code.

Quando você tiver instalado a extensão, use-a para ajudá-lo a criar arquivos de modelo DTDL no VS Code:

- A extensão fornece validação de sintaxe em arquivos de modelo DTDL, realçando erros conforme mostrado na seguinte captura de tela:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-validation.png" alt-text="Validação de modelo no VS Code":::

- Use o IntelliSense e o preenchimento automático quando estiver editando modelos DTDL:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-intellisense.png" alt-text="Usar o IntelliSense para modelos DTDL no VS Code":::

- Crie uma nova interface DTDL. O comando **DTDL: Create interface** cria um arquivo JSON com uma nova interface. A interface inclui definições de telemetria, propriedade e comando de exemplo.

## <a name="install-and-use-the-visual-studio-extension"></a>Instalar e usar a extensão do Visual Studio

A extensão DTDL para o Visual Studio 2019 adiciona os seguintes recursos de criação de DTDL:

- Validação de sintaxe DTDL v2.
- O IntelliSense, incluindo o preenchimento automático, para ajudá-lo com a sintaxe da linguagem.

Para instalar a extensão DTDL, acesse [suporte do idioma DTDL para VS 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16dtdllanguagesupport). Você também pode pesquisar por **DTDL** em **gerenciar extensões** no Visual Studio.

Quando você tiver instalado a extensão, use-a para ajudá-lo a criar arquivos de modelo DTDL no Visual Studio:

- A extensão fornece validação de sintaxe em arquivos de modelo DTDL, realçando erros conforme mostrado na seguinte captura de tela:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-validation-2.png" alt-text="Validação de modelo no Visual Studio":::

- Use o IntelliSense e o preenchimento automático quando estiver editando modelos DTDL:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-intellisense-2.png" alt-text="Usar o IntelliSense para modelos DTDL no Visual Studio":::

## <a name="next-steps"></a>Próximas etapas

Neste artigo de instruções, você aprendeu a usar as extensões DTDL para Visual Studio Code e o Visual Studio 2019 para criar e validar arquivos de modelo DTDL. Uma próxima etapa sugerida é aprender a usar o [Azure IOT Explorer com seus modelos e dispositivos](./howto-use-iot-explorer.md).
