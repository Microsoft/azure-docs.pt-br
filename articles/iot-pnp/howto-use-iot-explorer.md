---
title: Instalar e usar o Azure IoT Explorer | Microsoft Docs
description: Instale a ferramenta do Azure IoT Explorer e use-a para interagir com dispositivos IoT Plug and Play conectados ao Hub IoT. Embora este artigo se concentre em trabalhar com dispositivos IoT Plug and Play, você pode usar a ferramenta com qualquer dispositivo conectado ao seu hub.
author: dominicbetts
ms.author: dobett
ms.date: 11/10/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: contperf-fy21q2
ms.openlocfilehash: b0a86a32148086a3b644d0bf147d12be0d812536
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97030308"
---
# <a name="install-and-use-azure-iot-explorer"></a>Instalar e usar o Azure IoT Explorer

O Azure IoT Explorer é uma ferramenta gráfica para interagir com dispositivos conectados ao Hub IoT. Este artigo se concentra em usar a ferramenta para testar seus dispositivos de Plug and Play de IoT. Depois de instalar a ferramenta em seu computador local, você pode usá-la para se conectar a um Hub. Você pode usar a ferramenta para exibir a telemetria que os dispositivos estão enviando, trabalhar com propriedades de dispositivo e invocar comandos.

Este artigo mostra como:

- Instale e configure a ferramenta do Azure IoT Explorer.
- Use a ferramenta para interagir com e testar seus dispositivos IoT Plug and Play.

Para obter mais informações gerais sobre como usar a ferramenta, consulte o [Leiame](https://github.com/Azure/azure-iot-explorer/blob/master/README.md)do github.

Para usar a ferramenta do Azure IoT Explorer, você precisa de:

- Um Hub IoT do Azure. Há várias maneiras de adicionar um hub IoT à sua assinatura do Azure, como [a criação de um hub IOT usando o CLI do Azure](../iot-hub/iot-hub-create-using-cli.md). Você precisa da cadeia de conexão do Hub IoT para executar a ferramenta do Azure IoT Explorer. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Um dispositivo registrado em seu hub IoT. Você pode usar o Gerenciador IoT para criar e gerenciar registros de dispositivos no Hub IoT.

## <a name="install-azure-iot-explorer"></a>Instalar o Azure IoT Explorer

Acesse as [versões do Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases) e expanda a lista de ativos para a versão mais recente. Baixe e instale a versão mais recente do aplicativo.

>[!Important]
> Atualize para a versão 0,13. x para resolver modelos de qualquer repositório com base em [https://github.com/Azure/iot-plugandplay-models](https://github.com/Azure/iot-plugandplay-models)

## <a name="use-azure-iot-explorer"></a>Usar o Azure IoT Explorer

Para um dispositivo, você pode conectar seu próprio dispositivo ou usar um dos dispositivos simulados de exemplo. Para alguns exemplos de dispositivos simulados escritos em linguagens diferentes, consulte o início rápido [conectar um aplicativo de dispositivo de plug and Play IOT para o Hub IOT](quickstart-connect-device.md) .

### <a name="connect-to-your-hub"></a>Conectar-se ao seu hub

Na primeira vez que você executar o Azure IoT Explorer, sua cadeia de conexão do Hub IoT será solicitada. Depois de adicionar a cadeia de conexão, selecione **conectar**. Você pode usar as configurações da ferramenta para alternar para outro hub IoT atualizando a cadeia de conexão.

A definição de modelo para um dispositivo de Plug and Play IoT é armazenada no repositório público, no dispositivo conectado ou em uma pasta local. Por padrão, a ferramenta procura a definição do modelo no repositório público e no dispositivo conectado. Você pode adicionar e remover fontes ou configurar a prioridade das fontes em **configurações**:

Para adicionar uma fonte:

1. Ir para **configurações de plug and Play doméstica/IOT**
2. Selecione **Adicionar** e escolha sua origem, de um repositório ou pasta local.

Para remover uma fonte:

1. Ir para **configurações de plug and Play doméstica/IOT**
2. Localize a origem que você deseja remover.
3. Selecione **X** para removê-lo.

Alterar as prioridades de origem:

Você pode arrastar e soltar uma das fontes de definição de modelo para uma classificação diferente na lista.

### <a name="view-devices"></a>Exibir dispositivos

Depois que a ferramenta se conectar ao Hub IoT, ela exibirá a página de lista de **dispositivos** que lista as identidades de dispositivo registradas com o Hub IOT. Você pode selecionar qualquer entrada na lista para ver mais informações.

Na página lista de **dispositivos** , você pode:

- Selecione **novo** para registrar um novo dispositivo com o Hub. Em seguida, insira uma ID do dispositivo. Use as configurações padrão para gerar automaticamente chaves de autenticação e habilitar a conexão com o Hub.
- Selecione um dispositivo e, em seguida, selecione **excluir** para excluir uma identidade de dispositivo. Examine os detalhes do dispositivo antes de concluir esta ação para certificar-se de que você está excluindo a identidade correta do dispositivo.

## <a name="interact-with-a-device"></a>Interagir com um dispositivo

Na página lista de **dispositivos** , selecione um valor na coluna **ID do dispositivo** para exibir a página de detalhes do dispositivo registrado. Para cada dispositivo, há duas seções: **dispositivo** e e/ou **digital**.

### <a name="device"></a>Dispositivo

Esta seção inclui a **identidade do dispositivo**,  **dispositivos de dispositivo**, **telemetria**, **método direto**, **mensagem da nuvem para o dispositivo**, guias de identidade do **módulo**  .

- Você pode exibir e atualizar as informações de [identidade do dispositivo](../iot-hub/iot-hub-devguide-identity-registry.md) na guia **identidade do dispositivo** .
- Você pode acessar as informações de FileUp do [dispositivo](../iot-hub/iot-hub-devguide-device-twins.md) na guia de **dispositivo de entrelaçamento** .
- Se um dispositivo estiver conectado e enviando dados ativamente, você poderá exibir a [telemetria](../iot-hub/iot-hub-devguide-messages-read-builtin.md) na guia **telemetria** .
- Você pode chamar um [método direto](../iot-hub/iot-hub-devguide-direct-methods.md) no dispositivo na guia **método direto** .
- Você pode enviar uma [mensagem da nuvem para o dispositivo](../iot-hub/iot-hub-devguide-messages-c2d.md) na guia **mensagens da nuvem para o dispositivo** .
- Você pode acessar as informações do [módulo](../iot-hub/iot-hub-devguide-module-twins.md) .

### <a name="iot-plug-and-play-components"></a>Componentes de Plug and Play de IoT

Se o dispositivo estiver conectado ao Hub usando uma **ID de modelo**, a ferramenta mostrará a guia **componentes de plug and Play IOT** , onde você poderá ver a **ID do modelo**.

Se a **ID do modelo** estiver disponível em uma das fontes configuradas-repositório público ou pasta local, a lista de componentes será exibida. A seleção de um componente mostra as propriedades, os comandos e a telemetria disponíveis.

Na página **componente** , você pode exibir as propriedades somente leitura, atualizar propriedades graváveis, invocar comandos e ver as mensagens de telemetria produzidas por esse componente.

:::image type="content" source="media/howto-use-iot-explorer/components-iot-explorer.png" alt-text="Exibir componentes no Azure IoT Explorer":::

#### <a name="properties"></a>Propriedades

:::image type="content" source="media/howto-use-iot-explorer/properties-iot-explorer.png" alt-text="Exibir Propriedades no Azure IoT Explorer":::

Você pode exibir as propriedades somente leitura definidas em uma interface na guia **Propriedades (somente leitura)** . Você pode atualizar as propriedades graváveis definidas em uma interface na guia **Propriedades (gravável)** :

1. Vá para a guia **Propriedades (gravável)** .
1. Clique na propriedade que você deseja atualizar.
1. Insira o novo valor para a propriedade.
1. Visualize o conteúdo a ser enviado para o dispositivo.
1. Envie a alteração.

Depois de enviar uma alteração, você pode acompanhar o status da atualização: **sincronização**, **êxito** ou **erro**. Quando a sincronização estiver concluída, você verá o novo valor da propriedade na coluna **Propriedade relatada** . Se você navegar para outras páginas antes de a sincronização ser concluída, a ferramenta ainda o notificará quando a atualização for concluída. Você também pode usar o centro de notificações da ferramenta para ver o histórico de notificações.

#### <a name="commands"></a>Comandos

Para enviar um comando para um dispositivo, vá para a guia **comandos** :

1. Na lista de comandos, expanda o comando que você deseja disparar.
1. Insira os valores necessários para o comando.
1. Visualize o conteúdo a ser enviado para o dispositivo.
1. Envie o comando.

#### <a name="telemetry"></a>Telemetria

Para exibir a telemetria da interface selecionada, vá para a guia **telemetria** .

#### <a name="known-issues"></a>Problemas conhecidos

Para obter uma lista dos recursos de IoT com suporte da versão mais recente da ferramenta, consulte [lista de recursos](https://github.com/Azure/azure-iot-explorer/wiki).

## <a name="next-steps"></a>Próximas etapas

Neste artigo de instruções, você aprendeu a instalar e usar o Azure IoT Explorer para interagir com seus dispositivos de Plug and Play de IoT. Uma próxima etapa sugerida é aprender a [instalar e usar as ferramentas de criação do DTDL](howto-use-dtdl-authoring-tools.md).
