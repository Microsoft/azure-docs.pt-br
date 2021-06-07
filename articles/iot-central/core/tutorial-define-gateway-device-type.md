---
title: Tutorial – definir um novo tipo de dispositivo de gateway no Azure IoT Central | Microsoft Docs
description: Este tutorial mostra a você, como um construtor, como definir um novo tipo de dispositivo de gateway de IoT no aplicativo Azure IoT Central.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: cdace22072e4122150c878d43b99765cd6fd59e1
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490770"
---
# <a name="tutorial---define-a-new-iot-gateway-device-type-in-your-azure-iot-central-application"></a>Tutorial – definir um novo tipo de dispositivo de gateway de IoT no aplicativo do Azure IoT Central

*Este artigo se aplica a construtores de solução e desenvolvedores de dispositivos.*

Este tutorial mostra a você, como construtor de solução, como usar um modelo de dispositivo de gateway para definir um dispositivo de gateway no aplicativo IoT Central. Em seguida, configure vários dispositivos downstream que se conectam ao aplicativo IoT Central por meio do dispositivo de gateway. 

Neste tutorial, você criará um modelo de dispositivo de gateway de **Compilação Inteligente**. Um dispositivo de gateway de **Compilação inteligente** tem relações com outros dispositivos downstream.

![Diagrama de relação entre o dispositivo de gateway e os dispositivos downstream](./media/tutorial-define-gateway-device-type/gatewaypattern.png)

Além de permitir que dispositivos downstream se comuniquem com seu aplicativo IoT Central, um dispositivo de gateway também pode:

* Enviar sua telemetria, como a temperatura.
* Responder a atualizações de propriedade gravável feitas por um operador. Por exemplo, um operador pode alterar o intervalo de envio de telemetria.
* Responder a comandos, como reinicialização do dispositivo.

> [!div class="checklist"]
> Criar modelos de dispositivo downstream Criar um modelo de dispositivo de gateway Publicar o modelo de dispositivo Criar os dispositivos simulados

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa [Criar um aplicativo Azure IoT Central](./quick-deploy-iot-central.md).

## <a name="create-downstream-device-templates"></a>Criar modelos de dispositivo downstream

Este tutorial usa modelos de dispositivo para um **Sensor S1** dispositivo e um dispositivo **Sensor de ocupação RS40** para gerar dispositivos downstream simulados.

Para criar um modelo de dispositivo para um dispositivo **Sensor S1**:

1. No painel esquerdo, selecione **Modelos de Dispositivo**. Em seguida, selecione **+** para começar a adicionar o modelo.

1. Role a tela para baixo até ver o bloco do dispositivo **Sensor S1**. Selecione o bloco e, em seguida, **Avançar: Personalizar**.

1. Na página **Examinar**, selecione **Criar** para adicionar o modelo de dispositivo ao seu aplicativo. 

Para criar um modelo de dispositivo para um dispositivo ***Sensor de ocupação RS40**:

1. No painel esquerdo, selecione **Modelos de Dispositivo**. Em seguida, selecione **+** para começar a adicionar o modelo.

1. Role a tela para baixo até ver o bloco do dispositivo ***Sensor de ocupação RS40**. Selecione o bloco e, em seguida, **Avançar: Personalizar**.

1. Na página **Examinar**, selecione **Criar** para adicionar o modelo de dispositivo ao seu aplicativo. 

Agora você tem modelos de dispositivo para os dois tipos de dispositivo downstream:

![Modelos de dispositivo para dispositivos downstream](./media/tutorial-define-gateway-device-type/downstream-device-types.png)


## <a name="create-a-gateway-device-template"></a>Criar um modelo de dispositivo de gateway

Neste tutorial, você criará um modelo de dispositivo para um dispositivo de gateway do zero. Você usará esse modelo posteriormente para criar um dispositivo de gateway simulado em seu aplicativo.

Para adicionar um novo modelo de dispositivo de gateway ao seu aplicativo:

1. No painel esquerdo, selecione **Modelos de Dispositivo**. Em seguida, selecione **+** para começar a adicionar o modelo.

1. Na página **Selecionar tipo de modelo**, selecione o bloco **Dispositivo IoT** e selecione **Avançar: Personalizar**.

1. Na página **Personalizar o dispositivo**, marque a caixa de seleção **Dispositivo de gateway**.

1. Na página **Examinar**, selecione **Criar**. 

1. Insira **Dispositivo de gateway de Compilação Inteligente** como o nome do modelo.

1. Na página **Criar um modelo de capacidade**, selecione o bloco **Personalizado**.

1. Selecione **+** para adicionar uma interface.  Escolha a interface padrão **Informações do dispositivo**.

### <a name="add-relationships"></a>Adicionar relações

Em seguida, adicione relações aos modelos para os modelos de dispositivo downstream:

1. No modelo de **Dispositivo de gateway de Compilação Inteligente**, selecione **Relações**.

1. Selecione **+ Adicionar relação**. Insira **Sensor de ambiente** como o nome de exibição e selecione **Sensor S1** como o destino.

1. Selecione **+ Adicionar relação** novamente. Insira **Sensor de ocupação** como o nome de exibição e selecione **Sensor de ocupação RS40** como o destino.

1. Clique em **Salvar**.

![Modelo de ispositivo de gateway de Compilação Inteligente, mostrando relações](./media/tutorial-define-gateway-device-type/relationships.png)

### <a name="add-cloud-properties"></a>Adicionar propriedades da nuvem

Um modelo de dispositivo de gateway pode incluir propriedades de nuvem. As propriedades de nuvem existem somente no aplicativo IoT Central e nunca são enviadas nem recebidas de um dispositivo.

Para adicionar propriedades de nuvem ao modelo de **Dispositivo de gateway de Compilação Inteligente**.

1. No modelo de **Dispositivo de gateway de Compilação Inteligente**, selecione **Propriedades de nuvem**.

1.  Use as informações na tabela a seguir para adicionar duas propriedades de nuvem ao seu modelo de dispositivo de gateway.

    | Nome de exibição      | Tipo semântico | Esquema |
    | ----------------- | ------------- | ------ |
    | Data do Último Serviço | Nenhum          | Data   |
    | Nome do Cliente     | Nenhum          | String |

2. Clique em **Salvar**.

### <a name="create-views"></a>Criar exibições

Como um construtor, você pode personalizar o aplicativo para exibir informações relevantes sobre o dispositivo de sensor ambiental para um operador. Suas personalizações habilitam o operador a gerenciar os dispositivos de sensor ambiental conectados ao aplicativo. Você pode criar dois tipos de modos de exibição para que um operador use para interagir com dispositivos:

* Formulários para exibir e editar propriedades de dispositivo e de nuvem.
* Painéis para visualizar dispositivos.

Para gerar as exibições padrão do modelo de **Dispositivo de gateway de Compilação Inteligente**:

1. No modelo de **Dispositivo de gateway de Compilação Inteligente**, selecione **Exibições**.

1. Selecione o bloco **Gerar exibições padrão** e confira se todas as opções estão selecionadas.

1. Selecione **Gerar exibições padrão do painel**.

## <a name="publish-the-device-template"></a>Publicar o modelo de dispositivo

Antes de criar um dispositivo de gateway simulado ou conectar um dispositivo de gateway real, você precisa publicar o modelo do dispositivo.

Para publicar o modelo de dispositivo de gateway:

1. Selecione o modelo **Dispositivo de gateway de Compilação Inteligente** na página **Modelos de dispositivo**.

2. Selecione **Publicar**.

3. Na caixa de diálogo **Publicar um Modelo de Dispositivo**, escolha **Publicar**.

Depois que um modelo de dispositivo é publicado, ele fica visível na página **Dispositivos** e para o operador. Em um modelo de dispositivo publicado, não é possível editar um modelo do dispositivo sem criar uma versão. No entanto, você pode fazer atualizações nas propriedades de nuvem, nas personalizações e nos modos de exibição, em um modelo de dispositivo publicado. Essas atualizações não fazem com que uma nova versão seja criada. Depois de fazer qualquer alteração, selecione **Publicar** para enviar por push essas alterações para o operador.

## <a name="create-the-simulated-devices"></a>Criar os dispositivos simulados

Este tutorial usa dispositivos downstream simulados e um dispositivo de gateway simulado.

Para criar um dispositivo de gateway simulado:

1. Na página **Dispositivos**, selecione **Dispositivo de gateway de Compilação Inteligente** na lista de modelos de dispositivo.

1. Selecione **+** para começar a adicionar um novo dispositivo.

1. Mantenha a **ID do dispositivo** e o **Nome do dispositivo** gerados. A opção **Simulado** deve estar **Ativada**. Selecione **Criar**.

Para criar dispositivos downstream simulados:

1. Na página **Dispositivos**, selecione **Sensor de ocupação RS40** na lista de modelos de dispositivo.

1. Selecione **+** para começar a adicionar um novo dispositivo.

1. Mantenha a **ID do dispositivo** e o **Nome do dispositivo** gerados. A opção **Simulado** deve estar **Ativada**. Selecione **Criar**.

1. Na página **Dispositivos**, selecione **Sensor S1** na lista de modelos de dispositivo.

1. Selecione **+** para começar a adicionar um novo dispositivo.

1. Mantenha a **ID do dispositivo** e o **Nome do dispositivo** gerados. A opção **Simulado** deve estar **Ativada**. Selecione **Criar**.

![Dispositivos simulados no aplicativo](./media/tutorial-define-gateway-device-type/simulated-devices.png)

### <a name="add-downstream-device-relationships-to-a-gateway-device"></a>Adicionar relações de dispositivo downstream a um dispositivo de gateway

Agora que você tem os dispositivos simulados em seu aplicativo, pode criar as relações entre os dispositivos downstream e o dispositivo de gateway:

1. Na página **Dispositivos**, selecione **Sensor S1** na lista de modelos de dispositivo e, em seguida, selecione o dispositivo simulado **Sensor S1**.

1. Selecione **Conectar ao gateway**.

1. Na caixa de diálogo **Conectar a um gateway**, selecione o modelo de **Dispositivo de gateway de Compilação Inteligente** e, em seguida, selecione a instância simulada que você criou anteriormente.

1. Selecione **Ingressar**.

1. Na página **Dispositivos**, selecione **Sensor de ocupação RS40** na lista de modelos de dispositivo e, em seguida, selecione o dispositivo simulado **Sensor de ocupação RS40**.

1. Selecione **Conectar ao gateway**.

1. Na caixa de diálogo **Conectar a um gateway**, selecione o modelo de **Dispositivo de gateway de Compilação Inteligente** e, em seguida, selecione a instância simulada que você criou anteriormente.

1. Selecione **Ingressar**.

Agora, os dois dispositivos downstream simulados estão conectados ao dispositivo de gateway simulado. Se você navegar até a exibição **Dispositivos downstream** para seu dispositivo de gateway, poderá ver os dispositivos downstream relacionados:

![Exibição de dispositivos downstream](./media/tutorial-define-gateway-device-type/downstream-device-view.png)

Selecione um modelo de dispositivo de gateway e uma instância de dispositivo de gateway e selecione **Unir**.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

* Criar um gateway IoT como um modelo de dispositivo.
* Criar propriedades da nuvem.
* Criar personalizações.
* Definir uma visualização para a telemetria do dispositivo.
* Adicionar relacionamentos.
* Publicar seu modelo de dispositivo.

Em seguida, como um desenvolvedor do dispositivo, você pode aprender como:

> [!div class="nextstepaction"]
> [Adicionar um dispositivo Azure IoT Edge ao aplicativo do Azure IoT Central](tutorial-add-edge-as-leaf-device.md)
