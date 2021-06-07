---
title: Tutorial – Adicionar um dispositivo do Azure IoT Edge ao Azure IoT Central | Microsoft Docs
description: Tutorial – Como operador, adicione um dispositivo do Azure IoT Edge ao aplicativo do Azure IoT Central
author: rangv
ms.author: rangv
ms.date: 05/29/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
- iot-edge
ms.openlocfilehash: 373d144b4df818a075f0088e9cbf31cb5027e747
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101724873"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>Tutorial: Adicionar um dispositivo Azure IoT Edge ao aplicativo do Azure IoT Central

*Este artigo se aplica a operadores, criadores de solução e desenvolvedores de dispositivos.*

Este tutorial mostra como configurar e adicionar um dispositivo Azure IoT Edge para o aplicativo do Azure IoT Central. O tutorial usa uma VM (máquina virtual) do Linux habilitada para IoT Edge para simular um dispositivo do IoT Edge. O dispositivo IoT Edge usa um módulo que gera telemetria de ambiente simulado. Você exibe a telemetria em um painel no aplicativo IoT Central.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um modelo de dispositivo para um dispositivo IoT Edge
> * Criar um dispositivo IoT Edge no IoT Central
> * Implantar um dispositivo IoT Edge simulado em uma VM do Linux

## <a name="prerequisites"></a>Pré-requisitos

Conclua o início rápido [Criar um aplicativo do Azure IoT Central](./quick-deploy-iot-central.md) para criar um aplicativo do IoT Central usando o modelo de **App personalizado > Aplicativo personalizado**.

Para concluir as etapas deste tutorial, você precisa de uma assinatura ativa do Azure.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Baixe o arquivo de manifesto do IoT Edge no GitHub. Clique com o botão direito do mouse no seguinte link e selecione **Salvar link como**: [EnvironmentalSensorManifest.json](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/iotedge/EnvironmentalSensorManifest.json)

## <a name="create-device-template"></a>Criar modelo de dispositivo

Nesta seção, você cria um modelo de dispositivo do IoT Central para um dispositivo IoT Edge. Você importa um manifesto IoT Edge para começar e, em seguida, modifica o modelo para adicionar definições e exibições de telemetria:

### <a name="import-manifest-to-create-template"></a>Importar manifesto para criar modelo

Para criar um modelo de dispositivo com base em um manifesto IoT Edge:

1. No aplicativo IoT Central, navegue até **Modelos de dispositivo** e selecione **+ Novo**.

1. Na página **Selecionar tipo de modelo**, selecione o bloco **Azure IoT Edge**. Em seguida, selecione **Avançar: Personalizar**.

1. Na página **Carregar um manifesto de implantação do Azure IoT Edge**, insira *Dispositivo de Borda do Sensor Ambiental* como o nome do modelo de dispositivo. Em seguida, selecione **Procurar** para carregar o **EnvironmentalSensorManifest.json** baixado anteriormente. Em seguida, selecione **Avançar: Análise**.

1. Na página **Examinar**, selecione **Criar**.

1. Selecione a interface **Gerenciar** no módulo **SimulatedTemperatureSensor** para exibir as duas propriedades definidas no manifesto:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/imported-manifest.png" alt-text="Modelo de dispositivo criado no manifesto IoT Edge":::

> [!TIP]
> Esse manifesto de implantação efetua pull de imagens do repositório do Registro de Contêiner do Azure, que não requer nenhuma credencial para se conectar. Se quiser usar imagens de módulo de um repositório privado, defina as credenciais do registro de contêiner no manifesto.

### <a name="add-telemetry-to-manifest"></a>Adicionar telemetria ao manifesto

Um manifesto IoT Edge não define a telemetria que um módulo envia. Você adiciona as definições de telemetria ao modelo de dispositivo no IoT Central. O módulo **SimulatedTemperatureSensor** envia mensagens de telemetria parecidas com o seguinte JSON:

```json
{
  "machine": {
    "temperature": 75.0,
    "pressure": 40.2
  },
  "ambient": {
    "temperature": 23.0,
    "humidity": 30.0
  },
  "timeCreated": ""
}
```

Para adicionar as definições de telemetria ao modelo de dispositivo:

1. Selecione a interface **Gerenciar** no modelo **Dispositivo de borda do sensor ambiental**.

1. Selecione **+ Adicionar capacidade**. Insira *machine* como o **Nome de exibição**. O **Tipo de capacidade** deve ser **Telemetria**.

1. Selecione **Objeto** como o tipo de esquema e, em seguida, selecione **Definir**. Na página de definição de objeto, adicione *temperature* e *pressure* como atributos do tipo **Duplo** e, em seguida, selecione **Aplicar**.

1. Selecione **+ Adicionar capacidade**. Insira *ambient* como o **Nome de exibição**. O **Tipo de capacidade** deve ser **Telemetria**.

1. Selecione **Objeto** como o tipo de esquema e, em seguida, selecione **Definir**. Na página de definição de objeto, adicione *temperature* e *humidity* como atributos do tipo **Duplo** e, em seguida, selecione **Aplicar**.

1. Selecione **+ Adicionar capacidade**. Insira *timeCreated* como o **Nome de exibição**. O **Tipo de capacidade** deve ser **Telemetria**.

1. Selecione **DateTime** como o tipo de esquema.

1. Selecione **Salvar** para atualizar o modelo.

Agora, a interface **Gerenciar** inclui os tipos de telemetria **machine**, **ambient** e **timeCreated**:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/manage-interface.png" alt-text="Interface com os tipos de telemetria computador e ambiente":::

### <a name="add-views-to-template"></a>Adicionar exibições ao modelo

O modelo de dispositivo ainda não tem uma exibição que permite que um operador veja a telemetria do dispositivo IoT Edge. Para adicionar uma exibição ao modelo de dispositivo:

1. Selecione **Exibições** no modelo **Dispositivo de borda do sensor ambiental**.

1. Na página **Selecione para adicionar uma nova exibição**, selecione o bloco **Visualizando o dispositivo**.

1. Altere o nome da exibição para *Exibir telemetria do dispositivo IoT Edge*.

1. Selecione os tipos de telemetria **ambient** e **machine**. Em seguida, selecione **Adicionar bloco**.

1. Selecione **Salvar** para salvar a exibição **Exibir telemetria do dispositivo IoT Edge**.

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/template-telemetry-view.png" alt-text="Modelo de dispositivo com exibição de telemetria":::

### <a name="publish-the-template"></a>Publicar o modelo

Antes de adicionar um dispositivo que usa o modelo de **Dispositivo de borda do sensor ambiental**, você deve publicar o modelo.

Navegue até o modelo de **Dispositivo de borda do sensor de ambiente** e selecione **Publicar**. No painel **Publicar este modelo de dispositivo no aplicativo**, selecione **Publicar** para publicar o modelo:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/publish-template.png" alt-text="Publicar o modelo de dispositivo":::

## <a name="add-iot-edge-device"></a>Adicionar dispositivo do IoT Edge

Agora que você publicou o modelo de **Dispositivo de borda do sensor ambiental**, pode adicionar um dispositivo ao seu aplicativo IoT Central:

1. No aplicativo IoT Central, navegue até a página **Dispositivos** e selecione **Dispositivo de borda do sensor ambiental** na lista de modelos disponíveis.

1. Selecione **+ Novo** para adicionar um novo dispositivo do modelo. Na página **Criar dispositivo**, selecione **Criar**.

Agora você tem um novo dispositivo com o status **Registrado**:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/new-device.png" alt-text="Novo dispositivo registrado":::

### <a name="get-the-device-credentials"></a>Obter as credenciais do dispositivo

Ao implantar o dispositivo IoT Edge mais tarde neste tutorial, você precisará das credenciais que permitem que o dispositivo se conecte ao aplicativo IoT Central. Para obter as credenciais do dispositivo:

1. Na página **Dispositivo**, selecione o dispositivo que você criou.

1. Selecione **Conectar**.

1. Na página **Conexão do dispositivo**, anote o **Escopo da ID**, a **ID do Dispositivo** e a **Chave Primária**. Esses valores serão usados depois.

1. Selecione **Fechar**.

Agora você concluiu a configuração do aplicativo IoT Central para permitir que um dispositivo IoT Edge se conecte.

## <a name="deploy-an-iot-edge-device"></a>Implantar um dispositivo IoT Edge

Neste tutorial, você usa uma VM do Linux habilitada para Azure IoT Edge, criada no Azure para simular um dispositivo IoT Edge. Para criar a VM habilitada para IoT Edge em sua assinatura do Azure, clique em:

[![Botão Implantar no Azure para iotedge-vm-deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

Na página **Implantação personalizada**:

1. Selecione sua assinatura do Azure.

1. Selecione **Criar** para criar um grupo de recursos chamado *central-edge-rg*.

1. Escolha uma região próxima de você.

1. Adicione um **Prefixo de Rótulo DNS** exclusivo, como *contoso-central-edge*.

1. Escolha um nome de usuário administrador para a máquina virtual.

1. Insira *temp* como a cadeia de conexão. Posteriormente, você vai configurar o dispositivo para se conectar usando o DPS.

1. Aceite os valores padrão para o tamanho da VM, a versão do Ubuntu e o local.

1. Selecione **senha** como o tipo de autenticação.

1. Insira uma senha para a VM.

1. Em seguida, selecione **Examinar + Criar**.

1. Examine suas escolhas e, em seguida, selecione **Criar**:

    :::image type="content" source="media/tutorial-add-edge-as-leaf-device/vm-deployment.png" alt-text="Criar uma VM do IoT Edge":::

Concluir a implantação leva alguns minutos. Quando a implantação for concluída, navegue até o grupo de recursos **central-edge-rg** no portal do Azure.

### <a name="configure-the-iot-edge-vm"></a>Configurar a VM IoT Edge

Para configurar IoT Edge na VM para usar o DPS para registrar-se e conectar-se ao aplicativo IoT Central:

1. No grupo de recursos **contoso-edge-rg**, selecione a instância de máquina virtual.

1. Na seção **Suporte + solução de problemas**, selecione **Console serial**. Se você for solicitado a configurar o diagnóstico de inicialização, siga as instruções no portal.

1. Pressione **Enter** para ver o prompt `login:`. Insira seu nome de usuário e sua senha para entrar.

1. Execute o comando a seguir para verificar a versão do runtime do IoT Edge. No momento da redação deste artigo, a versão era 1.0.9.1:

    ```bash
    sudo iotedge --version
    ```

1. Use o editor `nano` para abrir o arquivo config.yaml do IoT Edge:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Role a tela para baixo até ver `# Manual provisioning configuration`. Comente as próximas três linhas, conforme mostrado no seguinte snippet:

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "temp"
    ```

1. Role a tela para baixo até ver `# DPS symmetric key provisioning configuration`. Remova a marca de comentário das próximas oito linhas, conforme mostrado no seguinte snippet:

    ```yaml
    # DPS symmetric key provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "{scope_id}"
      attestation:
        method: "symmetric_key"
        registration_id: "{registration_id}"
        symmetric_key: "{symmetric_key}"
    ```

    > [!TIP]
    > Não deve haver espaço restante na frente de `provisioning:`

1. Substitua `{scope_id}` pelo **Escopo da ID** que você anotou anteriormente.

1. Substitua `{registration_id}` pela **ID do Dispositivo** que você anotou anteriormente.

1. Substitua `{symmetric_key}` pela **Chave primária** que você anotou anteriormente.

1. Salve as alterações (**Ctrl-O**) e saia (**Ctrl-X**) do editor `nano`.

1. Execute o seguinte comando para reiniciar o daemon do IoT Edge:

    ```bash
    sudo systemctl restart iotedge
    ```

1. Para verificar o status dos módulos do IoT Edge, execute o seguinte comando:

    ```bash
    iotedge list
    ```

    A seguinte saída de exemplo mostra os módulos em execução:

    ```bash
    NAME                        STATUS           DESCRIPTION      CONFIG
    SimulatedTemperatureSensor  running          Up 20 seconds    mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
    edgeAgent                   running          Up 27 seconds    mcr.microsoft.com/azureiotedge-agent:1.0
    edgeHub                     running          Up 22 seconds    mcr.microsoft.com/azureiotedge-hub:1.0
    ```

    > [!TIP]
    > Talvez seja necessário aguardar até que todos os módulos iniciem a execução.

## <a name="view-the-telemetry"></a>Exibir a Telemetria

Agora, o dispositivo IoT Edge simulado está em execução na VM. No aplicativo IoT Central, o status do dispositivo agora é **Provisionado** na página **Dispositivos**:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/provisioned-device.png" alt-text="Dispositivo do IoT Edge provisionado":::

Você pode ver a telemetria do dispositivo na página **Exibir telemetria do dispositivo do IoT Edge**:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/device-telemetry-view.png" alt-text="Telemetria do Dispositivo":::

A página **Módulos** mostra o status dos módulos do IoT Edge no dispositivo:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/edge-module-status.png" alt-text="Status do módulo de dispositivo":::

## <a name="clean-up-resources"></a>Limpar os recursos

Se você planeja continuar trabalhando com a VM IoT Edge, pode manter e reutilizar os recursos usados neste tutorial. Caso contrário, exclua os recursos criados neste tutorial para evitar encargos adicionais:

* Para excluir a VM do IoT Edge e seus recursos associados, exclua o grupo de recursos **contoso-edge-rg** no portal do Azure.
* Para excluir o aplicativo IoT Central, navegue até a página **Seu aplicativo** na seção **Administração** do aplicativo e selecione **Excluir**.

Como um operador ou desenvolvedor de soluções, agora que você aprendeu a trabalhar com dispositivos IoT Edge e gerenciá-los no IoT Central, uma sugestão para a próxima etapa:

> [!div class="nextstepaction"]
> [Usar grupos de dispositivos para analisar a telemetria do dispositivo](./tutorial-use-device-groups.md)

## <a name="next-steps"></a>Próximas etapas

Como um desenvolvedor de dispositivos, agora que você aprendeu a trabalhar com dispositivos IoT Edge e gerenciá-los no IoT Central, uma sugestão para a próxima etapa é ler:

> [!div class="nextstepaction"]
> [Desenvolver módulos do IoT Edge](../../iot-edge/tutorial-develop-for-linux.md)