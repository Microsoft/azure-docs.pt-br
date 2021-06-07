---
title: Início rápido – Adicionar um dispositivo simulado ao Azure IoT Central
description: Este início rápido mostra como criar um modelo de dispositivo e adicionar um dispositivo simulado ao seu aplicativo IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 4c7925c1bc397e15d8b6e0f2f21d5e9147109b83
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106064844"
---
# <a name="quickstart-add-a-simulated-device-to-your-iot-central-application"></a>Início Rápido: Adicionar um dispositivo simulado ao seu aplicativo IoT Central

*Este artigo aplica-se a operadores, construtores e administradores.*

Um modelo de dispositivo define as funcionalidades de um dispositivo que se conecta ao seu aplicativo do IoT Central. Os recursos incluem telemetria que o dispositivo envia, as propriedades do dispositivo e os comandos aos quais um dispositivo responde. Em um modelo de dispositivo, um compilador ou operador pode adicionar dispositivos reais e simulados a um aplicativo. Dispositivos simulados são úteis para testar o comportamento do seu aplicativo do IoT Central antes de conectar dispositivos reais.

Neste início rápido, você adicionará um modelo de dispositivo para uma placa de desenvolvimento ESP32-Azure IoT Kit e criará um dispositivo simulado. Para concluir este início rápido, você não precisa de um dispositivo real, pois trabalhará com uma simulação do dispositivo. Um dispositivo ESP32:

* Envia telemetria como a temperatura.
* Informa propriedades específicas do dispositivo, como a temperatura máxima desde que o dispositivo foi reinicializado.
* Responde a comandos como reinicialização.
* Relata propriedades genéricas de dispositivo como a versão de firmware e o número de série.

## <a name="prerequisites"></a>Pré-requisitos

Conclua o início rápido [Criar um aplicativo do Azure IoT Central](./quick-deploy-iot-central.md) para criar um aplicativo do IoT Central usando o modelo de **App personalizado > Aplicativo personalizado**.

## <a name="create-a-device-template"></a>criar um modelo de dispositivo;

Como um construtor, você pode criar e editar os modelos de dispositivo no seu aplicativo IoT Central. Depois de publicar um modelo de dispositivo, você pode gerar um dispositivo simulado ou conectar dispositivos reais do modelo de dispositivo. Dispositivos simulados permitem testar o comportamento do aplicativo antes de você conectar um dispositivo real.

Para adicionar um novo modelo de dispositivo a seu aplicativo, selecione a guia **Modelos de dispositivo** no painel esquerdo.

:::image type="content" source="media/quick-create-simulated-device/device-definitions.png" alt-text="Captura de tela mostrando uma lista vazia de modelos de dispositivo":::

Um modelo de dispositivo inclui um modelo de dispositivo que define:

* A telemetria enviada pelo dispositivo.
* As propriedades do dispositivo.
* Os comandos aos quais o dispositivo responde.

### <a name="add-a-device-template"></a>Adicionar um modelo de dispositivo

Há várias opções para adicionar um modelo de dispositivo no aplicativo IoT Central. Você pode criar um modelo do zero, importar um modelo de um arquivo ou selecionar um dispositivo no catálogo de dispositivos. O IoT Central também dá suporte a uma abordagem de *dispositivo primeiro*, em que o aplicativo importa automaticamente um modelo de um repositório quando um dispositivo real se conecta pela primeira vez.

Neste início rápido, você escolherá um dispositivo do catálogo de dispositivos para criar um modelo de dispositivo.

As etapas a seguir mostram como usar o catálogo de dispositivos para importar o modelo para um dispositivo **ESP32**. Esses dispositivos enviam a telemetria, como a temperatura, para seu aplicativo:

1. Para adicionar um novo modelo de dispositivo, selecione **+ Novo** na página **Modelos de dispositivo**.

1. Na página **Selecionar tipo**, role para baixo até encontrar o bloco **ESP32-Azure IoT Kit** na seção **Usar um modelo de dispositivo pré-configurado**.

1. Selecione o bloco **ESP32-Azure IoT Kit** e, em seguida, **Avançar: Análise**.

1. Na página **Examinar**, selecione **Criar**.

1. Depois de alguns segundos, você pode ver o novo modelo de dispositivo:

    :::image type="content" source="media/quick-create-simulated-device/devkit-template.png" alt-text="Captura de tela mostrando o modelo de dispositivo para o dispositivo ESP32":::

    O nome do modelo é **Controlador de Sensor**. O modelo inclui componentes como **Controlador de Sensor**, **SensorTemp** e **Interface de Informações do Dispositivo**. Os componentes definem as funcionalidades de um dispositivo ESP32. As funcionalidades incluem a telemetria, as propriedades e os comandos.

### <a name="add-cloud-properties"></a>Adicionar propriedades da nuvem

Um modelo de dispositivo pode incluir propriedades de nuvem. As propriedades de nuvem existem somente no aplicativo IoT Central e nunca são enviadas ou recebidas de um dispositivo. Para adicionar duas propriedades de nuvem:

1. Selecione **Propriedades da Nuvem** e, em seguida, **+ Adicionar propriedade de nuvem**. Use as informações da seguinte tabela para adicionar duas propriedades de nuvem ao seu modelo de dispositivo:

    | Nome de exibição      | Tipo Semântico | Esquema |
    | ----------------- | ------------- | ------ |
    | Data do Último Serviço | Nenhum          | Data   |
    | Nome do Cliente     | Nenhum          | String |

1. Selecione **Salvar** para salvar as alterações:

    :::image type="content" source="media/quick-create-simulated-device/cloud-properties.png" alt-text="Captura de tela mostrando duas propriedades de nuvem":::

## <a name="views"></a>Exibições

Como um construtor, você pode personalizar o aplicativo para exibir informações relevantes sobre o dispositivo para um operador. Suas personalizações permitem que o operador gerencie os dispositivos conectados ao aplicativo. Você pode criar dois tipos de modos de exibição para que um operador use para interagir com dispositivos:

* Formulários para exibir e editar propriedades de dispositivo e de nuvem.
* Painéis para visualizar dispositivos que incluem a telemetria que enviam.

### <a name="default-views"></a>Exibições padrão

As exibições padrão são uma maneira rápida de começar a visualizar as informações importantes do dispositivo. Você pode ter até três modos de exibição padrão geradas para seu modelo de dispositivo:

* O modo de exibição **Comandos** permite que o operador despache comandos para seu dispositivo.
* O modo de exibição **Visão Geral** usa gráficos e métricas para exibir a telemetria do dispositivo.
* O modo de exibição **Sobre** exibe as propriedades do dispositivo.

Selecione o nó **Exibições** no modelo de dispositivo. Você pode ver que o IoT Central gerou uma **Visão geral** e uma exibição **Sobre** para você quando adicionou o modelo.

Para adicionar um novo formulário **Gerenciar dispositivo** que um operador pode usar para gerenciar o dispositivo:

1. Selecione o nó **Exibições** e, em seguida, selecione o bloco **Editando dados de dispositivo e de nuvem** para adicionar uma nova exibição.

1. Altere o nome do formulário para **Gerenciar dispositivo**.

1. Selecione as propriedades de nuvem **Nome do Cliente** e **Data do Último Serviço**, bem como a propriedade **Temperatura de Destino**. Em seguida, selecione **Adicionar Seção**:

    :::image type="content" source="media/quick-create-simulated-device/new-form.png" alt-text="Captura de tela mostrando o novo formulário adicionado ao modelo de dispositivo":::

1. Selecione **Salvar** para salvar seu novo formulário.

## <a name="publish-device-template"></a>Publicar um modelo de dispositivo

Antes de criar um dispositivo simulado ou conectar um dispositivo real, você precisa publicar o modelo de dispositivo. Embora o IoT Central tenha publicado o modelo quando você o criou pela primeira vez, é necessário publicar a versão atualizada.

Para publicar um modelo de dispositivo:

1. Navegue até o modelo de dispositivo **Controlador de Sensor** da página **Modelos de dispositivo**.

1. Selecione **Publicar**:

    :::image type="content" source="media/quick-create-simulated-device/published-model.png" alt-text="Captura de tela mostrando o local do ícone de publicação":::

1. Na caixa de diálogo **Publicar este modelo de dispositivo no aplicativo**, selecione **Publicar**.

Depois de publicar um modelo de dispositivo, ele fica visível na página **Dispositivos**. Em um modelo de dispositivo publicado, não é possível editar um modelo do dispositivo sem criar uma versão. No entanto, você pode modificar propriedades de nuvem, personalizações e exibições em um modelo de dispositivo publicado sem controle de versão. Depois de fazer qualquer alteração, selecione **Publicar** para enviar por push essas alterações para o operador.

## <a name="add-a-simulated-device"></a>Adicionar um dispositivo simulado

Para adicionar um dispositivo simulado ao seu aplicativo, use o modelo de dispositivo **ESP32** que você criou.

1. Para adicionar um novo dispositivo como um operador, escolha **Dispositivos** no painel esquerdo. A guia **Dispositivos** mostra **Todos os dispositivos** e o modelo de dispositivo **Controlador de Sensor** para o dispositivo ESP32. Selecione **Controlador de Sensor**.

1. Para adicionar um dispositivo DevKit simulado, selecione **+ Novo**. Use a **Identificação do Dispositivo** sugerida ou insira a sua. Uma identificação do dispositivo pode conter letras, números e o caractere `-`. Você também pode inserir um nome para o novo dispositivo. Verifique se a opção **Simular este dispositivo** foi definida como **Sim** e selecione **Criar**.

    :::image type="content" source="media/quick-create-simulated-device/simulated-device.png" alt-text="Captura de tela que mostra o dispositivo de Controlador de Sensor simulado":::

Agora você pode interagir com as exibições que foram criadas pelo construtor para o modelo de dispositivo usando dados simulados:

1. Selecione o dispositivo simulado na página **Dispositivos**

    * A exibição **Visão geral** mostra um gráfico da telemetria simulada:

        :::image type="content" source="media/quick-create-simulated-device/simulated-telemetry.png" alt-text="Captura de tela que mostra a página de visão geral para o dispositivo simulado":::

    * A exibição **Sobre** mostra os valores da propriedade.

    * A exibição **Comandos** permite que você execute comandos, como **reboot**, no dispositivo.

    * A exibição **Gerenciar dispositivos** é o formulário que você criou para o operador gerenciar o dispositivo.

    * A exibição **Dados brutos** permite que você veja a telemetria bruta e os valores de propriedade enviados pelo dispositivo. Essa exibição é útil para a depuração de dispositivos.

## <a name="use-a-simulated-device-to-improve-views"></a>Usar um dispositivo simulado para melhorar as exibições

Depois de criar um novo dispositivo simulado, o construtor pode usar esse dispositivo para continuar a melhorar e reforçar as exibições para o modelo de dispositivo.

1. Escolha **Modelos de Dispositivo** no painel esquerdo e selecione o modelo **Controlador de Sensor**.

1. Selecione qualquer uma das exibições que você gostaria de editar, como **Visão geral**, ou crie uma. Selecione **Configurar dispositivo de versão prévia** e **Selecionar em um dispositivo em execução**. Aqui, você pode optar por não ter nenhum dispositivo de versão prévia, um dispositivo real que pode ser configurado para teste ou um dispositivo existente que você adicionou ao IoT Central.

1. Escolha seu dispositivo simulado na lista. Em seguida, selecione **Aplicar**. Agora você pode ver o mesmo dispositivo simulado em sua experiência de construção de exibições de modelo de dispositivo. Essa exibição é particularmente útil para gráficos e outras visualizações.

    :::image type="content" source="media/quick-create-simulated-device/configure-preview.png" alt-text="Captura de tela que mostra um dispositivo de visualização configurado":::

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a criar um modelo de dispositivo **Controlador de Sensor** para um dispositivo ESP32 e adicionar um dispositivo simulado ao seu aplicativo.

Para saber mais sobre como monitorar dispositivos conectados ao seu aplicativo, passe para o início rápido:

> [!div class="nextstepaction"]
> [Configurar regras e ações](./quick-configure-rules.md)
