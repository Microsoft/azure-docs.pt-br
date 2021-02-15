---
title: Noções básicas sobre o controle de versão de modelo de dispositivo para aplicativos Azure IoT Central | Microsoft Docs
description: Iterar modelos de dispositivo criando novas versões e sem afetar os dispositivos conectados em tempo real
author: dominicbetts
ms.author: dobett
ms.date: 11/06/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 93545c63013c95e3db498b079061da3d9b189efd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995753"
---
# <a name="create-a-new-device-template-version"></a>Criar uma nova versão do modelo de dispositivo

*Este artigo se aplica a construtores de solução e desenvolvedores de dispositivos.*

Um modelo de dispositivo inclui um esquema que descreve como um dispositivo interage com IoT Central. Essas interações incluem telemetria, propriedades e comandos. O dispositivo e o aplicativo IoT Central dependem de um entendimento compartilhado desse esquema para trocar informações. Você só pode fazer alterações limitadas no esquema sem quebrar o contrato, é por isso que a maioria das alterações de esquema requer uma nova versão do modelo de dispositivo. O controle de versão do modelo de dispositivo permite que dispositivos mais antigos continuem com a versão do esquema que eles entendem, enquanto dispositivos mais recentes ou atualizados usam uma versão de esquema posterior.

O esquema em um modelo de dispositivo é definido no modelo de dispositivo e suas interfaces. Os modelos de dispositivo incluem outras informações, como propriedades de nuvem, personalizações de exibição e exibições. Se você fizer alterações a essas partes do modelo de dispositivo que não definem como o dispositivo troca dados com IoT Central, você não precisará fazer a versão do modelo.

Você sempre deve publicar um modelo de dispositivo atualizado antes que um operador possa usá-lo. IoT Central impede que você publique alterações significativas em um modelo de dispositivo sem primeiro o controle de versão do modelo.

> [!NOTE]
> Para saber mais sobre como criar um modelo de dispositivo [, consulte Configurar e gerenciar um modelo de dispositivo](howto-set-up-template.md)

## <a name="versioning-rules"></a>Regras de controle de versão

Esta seção resume as regras de controle de versão que se aplicam aos modelos de dispositivo. Os modelos de dispositivo e as interfaces têm números de versão. O trecho a seguir mostra o modelo de dispositivo para um dispositivo termostato. O modelo do dispositivo tem uma única interface. Você pode ver o número de versão no final do `@id` campo.

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    // ...
  ]
}
```

Para exibir essas informações na interface do usuário do IoT Central, selecione **Exibir identidade** no editor de modelos de dispositivo:

:::image type="content" source="media/howto-version-device-template/view-identity.png" alt-text="Exibir a identidade de uma interface para ver o número de versão":::

A lista a seguir mostra as regras que determinam quando você deve criar uma nova versão:

* Depois que um modelo de dispositivo é publicado, não é possível remover nenhuma interface, mesmo em uma nova versão do modelo de dispositivo.
* Depois que um modelo de dispositivo for publicado, você poderá adicionar uma interface se criar uma nova versão do modelo de dispositivo.
* Depois que um modelo de dispositivo for publicado, você poderá substituir uma interface com uma versão mais recente se criar uma nova versão do modelo de dispositivo. Por exemplo, se o modelo de dispositivo do sensor v1 usar a interface termostato v1, você poderá criar um modelo de dispositivo do sensor v2 que usa a interface termostato v2.
* Depois que uma interface é publicada, não é possível remover nenhum conteúdo da interface, mesmo em uma nova versão do modelo de dispositivo.
* Depois que uma interface for publicada, você poderá adicionar itens ao conteúdo de uma interface se criar uma nova versão da interface e do modelo de dispositivo. Os itens que você pode adicionar à interface incluem telemetria, propriedades e comandos.
* Depois que uma interface for publicada, você poderá fazer alterações de não-esquema em itens existentes na interface se criar uma nova versão do modelo de interface e dispositivo. Partes sem esquema de um item de interface incluem o nome de exibição e o tipo semântico. As partes de esquema de um item de interface que você não pode alterar são nome, tipo de recurso e esquema.

As seções a seguir orientam você por alguns exemplos de como modificar modelos de dispositivo no IoT Central.

## <a name="customize-the-device-template-without-versioning"></a>Personalizar o modelo de dispositivo sem controle de versão

Determinados elementos de seus recursos de dispositivo podem ser editados sem a necessidade de versão do modelo e das interfaces do dispositivo. Por exemplo, alguns desses campos incluem nome de exibição, tipo semântico, valor mínimo, valor máximo, casas decimais, cor, unidade, unidade de exibição, comentário e descrição. Para adicionar uma dessas personalizações:

1. Vá para a página **modelos de dispositivo** .
1. Selecione o modelo de dispositivo que você deseja personalizar.
1. Escolha a guia **Personalizar** .
1. Todos os recursos definidos no seu modelo de dispositivo estão listados aqui. Você pode editar, salvar e usar todos esses campos sem a necessidade de versão do seu modelo de dispositivo. Se houver campos que você deseja editar que sejam somente leitura, você deverá fazer a versão do modelo de dispositivo para alterá-los. Selecione um campo que você deseja editar e insira um novo valor.
1. Clique em **Salvar**. Agora, esses valores substituem tudo o que foi salvo inicialmente em seu modelo de dispositivo e são usados em todo o aplicativo.

## <a name="version-a-device-template"></a>Versão um modelo de dispositivo

A criação de uma nova versão do modelo de dispositivo cria uma versão de rascunho do modelo em que o modelo de dispositivo pode ser editado. Todas as interfaces publicadas permanecem publicadas até que sejam com controle de versão individual. Para modificar uma interface publicada, primeiro crie uma nova versão de modelo de dispositivo.

Somente versione o modelo de dispositivo quando você estiver tentando editar uma parte do modelo de dispositivo que você não pode editar na seção personalizações.

Para a versão de um modelo de dispositivo:

1. Vá para a página **modelos de dispositivo** .
1. Selecione o modelo de dispositivo que você está tentando executar na versão.
1. Selecione o botão **versão** na parte superior da página e dê um novo nome ao modelo. IoT Central sugere um novo nome, que você pode editar.
1. Selecione **Criar**.
1. Agora seu modelo de dispositivo está no modo de rascunho. Você pode ver que suas interfaces ainda estão bloqueadas. Versão todas as interfaces que você deseja modificar.

## <a name="version-an-interface"></a>Versão de uma interface

O controle de versão de uma interface permite que você adicione, atualize e remova os recursos dentro da interface que você já criou.

Para a versão de uma interface:

1. Vá para a página **modelos de dispositivo** .
1. Selecione o modelo de dispositivo que você tem em um modo de rascunho.
1. Selecione a interface que está no modo publicado que você deseja versão e edição.
1. Selecione o botão **versão** na parte superior da página da interface.
1. Selecione **Criar**.
1. Agora sua interface está no modo de rascunho. Você pode adicionar ou editar recursos à sua interface sem interromper as exibições e visualizações existentes.

## <a name="migrate-a-device-across-versions"></a>Migrar um dispositivo entre versões

É possível criar várias versões do modelo de dispositivo. Ao longo do tempo, você terá vários dispositivos conectados usando esses modelos de dispositivo. Você pode migrar dispositivos de uma versão do modelo de dispositivo para outra. As etapas a seguir descrevem como migrar um dispositivo:

1. Acesse a página **Dispositivos**.
1. Selecione o dispositivo que você precisa migrar para outra versão.
1. Escolha **migrar**:  :::image type="content" source="media/howto-version-device-template/migrate-device.png" alt-text="escolha a opção para iniciar a migração de um dispositivo":::
1. Selecione o modelo de dispositivo com o número de versão para o qual você deseja migrar o dispositivo e selecione **migrar**.

## <a name="next-steps"></a>Próximas etapas

Se você for um operador ou um construtor de soluções, uma próxima etapa sugerida é aprender a [gerenciar seus dispositivos](./howto-manage-devices.md).

Se você for um desenvolvedor de dispositivos, uma próxima etapa sugerida será ler sobre [Azure IOT Edge dispositivos e IOT central do Azure](./concepts-iot-edge.md).
