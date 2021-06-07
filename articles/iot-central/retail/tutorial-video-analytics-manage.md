---
title: Tutorial – Monitorar um vídeo usando o modelo de aplicativo de detecção de objetos e movimentos e análise de vídeo do Azure IoT Central
description: Este tutorial mostra como usar os painéis do modelo de aplicativo de detecção de objetos e movimentos e análise de vídeo para gerenciar suas câmeras e monitorar o vídeo.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: fbfef094cd062e437f2a28369162de96631ef41b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99832599"
---
# <a name="tutorial-monitor-and-manage-a-video-analytics---object-and-motion-detection-application"></a>Tutorial: Monitorar e gerenciar um aplicativo de detecção de objetos e movimentos e análise de vídeo

Neste tutorial, você aprende a:
> [!div class="checklist"]
> * Adicionar câmeras de detecção de objetos e movimentos ao seu aplicativo do IoT Central.
> * Gerenciar seus fluxos de vídeo e reproduzi-los quando forem detectados eventos interessantes.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você deverá concluir:

* Um dos tutoriais anteriores [Criar um aplicativo da Análise Dinâmica de Vídeo no Azure IoT Central](./tutorial-video-analytics-create-app-yolo-v3.md) ou [Criar uma análise de vídeo no Azure IoT Central (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md).
* Um dos tutoriais anteriores [Criar uma instância do IoT Edge da Análise Dinâmica de Vídeo (VM do Linux)](tutorial-video-analytics-iot-edge-vm.md) ou [Criar uma instância do IoT Edge da Análise Dinâmica de Vídeo (Intel NUC)](tutorial-video-analytics-iot-edge-nuc.md).

Você deverá ter o [Docker](https://www.docker.com/products/docker-desktop) instalado no computador local para executar o aplicativo de exibição de vídeo.

## <a name="add-an-object-detection-camera"></a>Adicionar uma câmera de detecção de objetos

No seu aplicativo do IoT Central, procure o dispositivo **Gateway de LVA 001** criado anteriormente. Em seguida, selecione a guia **Comandos**.

Use os valores da tabela a seguir como os valores de parâmetro para o comando **Adicionar Solicitação da Câmera**. Os valores mostrados na tabela pressupõem que você esteja usando a câmera simulada na VM do Azure; ajuste os valores de acordo se estiver usando uma câmera real:

| Campo| Descrição| Valor de exemplo|
|---------|---------|---------|
| ID da Câmera      | Identificação do Dispositivo para provisionamento | camera-003 |
| Nome da Câmera    | Nome amigável           | Câmera de detecção de objetos |
| URL do RTSP       | Endereço do fluxo   | RTSP: //10.0.0.4:554/media/camera-300s.mkv|
| Nome de Usuário do RTSP  |                         | usuário    |
| Senha do RTSP  |                         | password    |
| Tipo de detecção | Lista suspensa                | Detecção de objetos       |

Selecione **Executar** para adicionar o dispositivo de câmera:

:::image type="content" source="media/tutorial-video-analytics-manage/add-camera.png" alt-text="Adicionar Câmera":::

> [!NOTE]
> O modelo de dispositivo **Detector de Objetos de Borda de LVA** já existe no aplicativo.

## <a name="add-a-motion-detection-camera-optional"></a>Adicionar uma câmera de detecção de movimentos (opcional)

Se você tiver duas câmeras conectadas ao dispositivo de gateway do IoT Edge, repita as etapas anteriores para adicionar uma câmera de detecção de movimentos ao aplicativo. Use valores diferentes para os parâmetros **ID da Câmera**, **Nome da Câmera** e **URL do RTSP**.

## <a name="view-the-downstream-devices"></a>Exibir os dispositivos downstream

Selecione a guia **Dispositivos Downstream** do dispositivo **Gateway de LVA 001** para ver os dispositivos de câmera recém-adicionados:

:::image type="content" source="media/tutorial-video-analytics-manage/inspect-downstream.png" alt-text="Inspecionar":::

Os dispositivos de câmera também são exibidos na lista da página **Dispositivos** no aplicativo.

## <a name="configure-and-manage-the-camera"></a>Configurar e gerenciar a câmera

Procure o dispositivo **camera-003** e selecione a guia **Gerenciar**.

Use os valores padrão ou modifique-os se precisar personalizar as propriedades do dispositivo:

**Camera Settings**

| Propriedade | Descrição | Valor sugerido |
|-|-|-|
| Host de Reprodução de Vídeo | Host do visualizador do Player de Mídia do Azure | http://localhost:8094 |

**Operações e Diagnósticos da LVA**

| Propriedade | Descrição | Valor sugerido |
|-|-|-|
| Inicialização automática | Iniciar a detecção de objetos quando o Gateway de LVA for reiniciado | Verificado |
| Depurar Telemetria | Rastreamento de Evento | Opcional |
|Tempo Limite de Inferência (s)| O tempo usado para determinar se as inferências foram interrompidas | 20 |

**Detecção de Objetos de IA**

| Propriedade | Descrição | Valor sugerido |
|-|-|-|
| Tempo Máximo de Captura de Inferência (s) | Tempo máximo de captura | 15 |
| Classes de Detecção | Cadeias de caracteres, delimitadas por vírgulas, com as marcas de detecção. Para obter mais informações, confira a [lista de marcas compatíveis](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/yolov3-onnx/tags.txt) | caminhão, ônibus |
| Limite de Confiança | Percentual de qualificação para determinar se a detecção de objetos é válida | 70 |
| Taxa de Amostragem de Quadro de Inferência (Fps) | [Descrição Aqui] | 2 |

Clique em **Salvar**.

Após alguns segundos, você verá a mensagem de confirmação **Aceito** para cada configuração:

:::image type="content" source="media/tutorial-video-analytics-manage/object-detection.png" alt-text="Detecção de Objetos":::

## <a name="start-lva-processing"></a>Iniciar processamento de LVA

Procure o dispositivo **camera-003** e selecione a guia **Comandos**.

Execute o comando **Iniciar Processamento de LVA**.

Quando o comando for concluído, exiba o histórico de comandos para verificar se não há erros:

:::image type="content" source="media/tutorial-video-analytics-manage/start-processing.png" alt-text="comando Iniciar processamento de LVA":::

## <a name="monitor-the-cameras"></a>Monitorar as câmeras

Procure o dispositivo **camera-003** e selecione a guia **Painel**:

:::image type="content" source="media/tutorial-video-analytics-manage/camera-dashboard.png" alt-text="Painel da câmera":::

O bloco **Contagem de Detecções** mostra a contagem média de detecções de cada um dos objetos de classes de detecção selecionados durante um intervalo de detecção de um segundo.

O gráfico de pizza **Classes de Detecção** mostra o percentual de detecções de cada tipo de classe.

O **Vídeo de Evento de Inferência** é uma lista de links para os ativos nos Serviços de Mídia do Azure que contêm as detecções. O link usa o player de host descrito na seção a seguir.

## <a name="start-the-streaming-endpoint"></a>Iniciar o ponto de extremidade de streaming

Inicie o ponto de extremidade dos Serviços de Mídia para habilitar o aplicativo cliente de Player de Mídia a transmitir o vídeo gravado:

* No portal do Azure, procure o grupo de recursos **lva-rg**.
* Abra o recurso **Ponto de Extremidade de Streaming**.
* Na página **Detalhes do ponto de extremidade de streaming**, selecione **Iniciar**. Você verá um aviso informando que a cobrança começa quando o ponto de extremidade é iniciado.

## <a name="view-stored-video"></a>Exibir o vídeo armazenado

Acabaram os dias de observar as câmeras e executar ações em relação a imagens suspeitas. Com a marcação automática de eventos e links diretos para o vídeo armazenado com a detecção inferida, os operadores de segurança podem encontrar eventos de interesse em uma lista e seguir o link para assistir ao vídeo.

Use o [player de vídeo do AMP](https://github.com/Azure/live-video-analytics/tree/master/utilities/amp-viewer) para assistir ao vídeo armazenado na sua conta dos Serviços de Mídia do Azure.

O aplicativo do IoT Central armazena o vídeo nos Serviços de Mídia do Azure, em que você poderá transmiti-lo. Você precisará ter um player de vídeo para reproduzir o vídeo armazenado nos Serviços de Mídia do Azure.

Verifique se o **Docker** está em execução no computador local.

Abra um prompt de comando e use o comando a seguir para executar o player de vídeo em um contêiner do Docker no computador local. Substitua os espaços reservados no comando pelos valores que você anotou anteriormente do arquivo *scratchpad.txt*. Você anotou a `amsAadClientId`, o `amsAadSecret`, a `amsAadTenantId`, a `amsSubscriptionId` e o `amsAccountName` quando criou a entidade de serviço para a sua conta dos Serviços de Mídia:

```bash
docker run -it --rm -e amsAadClientId="<FROM_AZURE_PORTAL>" -e amsAadSecret="<FROM_AZURE_PORTAL>" -e amsAadTenantId="<FROM_AZURE_PORTAL>" -e amsArmAadAudience="https://management.core.windows.net" -e amsArmEndpoint="https://management.azure.com" -e amsAadEndpoint="https://login.microsoftonline.com" -e amsSubscriptionId="<FROM_AZURE_PORTAL>" -e amsResourceGroup="<FROM_AZURE_PORTAL>" -e amsAccountName="<FROM_AZURE_PORTAL>" -p 8094:8094 mcr.microsoft.com/lva-utilities/amp-viewer:1.0-amd64
```

|Parâmetro do Docker | Acesso à API do AMS (JSON)|
|-|-|
|amsAadClientId| AadClientId|
|amsAadSecret| AadSecret |
|amsAadTenantId| AadTenantId |
|amsSubscriptionId| SubscriptionId|
|amsResourceGroup| ResourceGroup |
|amsAccountName| AccountName|

Procure o dispositivo **camera-003** e selecione a guia **Painel**. Em seguida, clique em um dos hiperlinks de detecção de objetos capturados no bloco **Vídeo de Evento de Inferência**. O vídeo é exibido em uma página mostrada pelo player de vídeo local:

:::image type="content" source="media/tutorial-video-analytics-manage/video-snippet.png" alt-text="Trecho do vídeo":::

## <a name="change-the-simulated-devices-in-application-dashboards"></a>Alterar os dispositivos simulados nos painéis do aplicativo

Os painéis de aplicativos são originalmente preenchidos com a telemetria e as propriedades geradas por meio dos dispositivos simulados do IoT Central. Para configurar os blocos para telemetria em câmeras reais ou no simulador Live555, siga estas etapas:

1. Navegue até o painel do aplicativo **(Amostra) Monitor de câmera real**.
1. Selecione **Editar**.
1. Selecione o bloco **Observação** e exclua-o.
1. Altere o título do painel para *Monitor de câmera real*.
1. No bloco **Contagem de Inferências**, selecione o ícone Configurar.
1. Na seção **Configurar Gráfico**, selecione uma ou mais câmeras reais no grupo de dispositivos do **Detector de Objetos de Borda de LVA**.
1. Selecione o campo de telemetria `AI Inference Interface/Inference Count`.
1. Selecione **Atualizar**.

1. Repita as etapas para os seguintes blocos:
    1. O gráfico de pizza **Detecção** usa o tipo de telemetria `AI Inference Interface/Inference/entity/tag/value`.
    1. **Inferência** usa o último valor conhecido de `AI Inference Interface/Inference/entity/tag/value`.
    1. **% de Confiança** usa o último valor conhecido de `AI Inference Interface/Inference/entity/tag/confidence`.
    1. **Instantâneo** usa `AI Inference Interface/Inference Image` mostrado como uma imagem.
    1. **Vídeo de Evento de Inferência** usa `AI Inference Interface/Inference Event Video` mostrado como um link.
1. Clique em **Salvar**.

O painel **Monitor de Câmera Real** agora mostra valores do seu dispositivo de câmera real:

:::image type="content" source="media/tutorial-video-analytics-manage/update-real-cameras.png" alt-text="Painel do aplicativo Câmeras Reais":::

## <a name="pause-processing"></a>Pausar o processamento

Pause o processamento da Análise Dinâmica de Vídeo no aplicativo:

1. No aplicativo, procure a página **Comandos** da câmera de detecção de objetos. Em seguida, execute o comando **Parar Processamento de LVA**.

1. Pare o ponto de extremidade de streaming da sua conta dos Serviços de Mídia:
    * No portal do Azure, procure o grupo de recursos **lva-rg**.
    * Clique no recurso **Ponto de Extremidade de Streaming**.
    * Na página **Detalhes do ponto de extremidade de streaming**, selecione **Parar**.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você tiver concluído o aplicativo, remova todos os recursos criados da seguinte maneira:

1. No aplicativo do IoT Central, procure a página **Seu aplicativo** na seção **Administração**. Em seguida, selecione **Excluir**.
1. No portal do Azure, exclua o grupo de recursos **lva-rg**.
1. No computador local, pare o contêiner **amp-viewer** do Docker.

## <a name="next-steps"></a>Próximas etapas

Você acabou de aprender a adicionar câmeras ao aplicativo do IoT Central e configurá-las para a detecção de objetos e movimentos.

Para saber como personalizar o código-fonte dos módulos do IoT Edge:

> [!div class="nextstepaction"]
> [Modificar e criar os módulos de gateway da Análise Dinâmica de Vídeo](./tutorial-video-analytics-build-module.md)
