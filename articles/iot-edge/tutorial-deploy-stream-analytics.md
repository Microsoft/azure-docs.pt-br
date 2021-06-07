---
title: Tutorial – Stream Analytics na borda usando o Azure IoT Edge
description: Neste tutorial, você implantará o Azure Stream Analytics como um módulo em um dispositivo IoT Edge
author: kgremban
ms.author: kgremban
ms.date: 07/29/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 323973b7646acee07a0c4dbc59834e0aceca75ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462041"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module"></a>Tutorial: Implantar o Azure Stream Analytics como um módulo do IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Muitas soluções de IoT usam serviços de análise para obter insights sobre dados quando estes chegam na nuvem vindos de dispositivos de IoT. Com o Azure IoT Edge, você pode usar a lógica do [Azure Stream Analytics](../stream-analytics/index.yml) e movê-la para o próprio dispositivo. Ao processar fluxos de telemetria na borda, você pode reduzir a quantidade de dados carregados e reduzir o tempo necessário para reagir a ideias acionáveis.

O Azure IoT Edge e o Azure Stream Analytics são integrados para simplificar seu desenvolvimento de carga de trabalho. Você pode criar um trabalho do Azure Stream Analytics no portal do Azure e implantá-lo como um módulo do IoT Edge sem nenhum código adicional.  

O Azure Stream Analytics fornece uma sintaxe de consulta estruturada de modo sofisticado para a análise de dados na nuvem e em dispositivos do IoT Edge. Para obter mais informações, confira a [documentação do Azure Stream Analytics](../stream-analytics/stream-analytics-edge.md).

O módulo do Stream Analytics neste tutorial calcula a temperatura média em uma janela ininterrupta de 30 segundos. Quando essa média atinge 70, o módulo envia um alerta para o dispositivo tomar medidas. Nesse caso, a medida é redefinir o sensor de temperatura simulado. Em um ambiente de produção, você pode usar essa funcionalidade para desligar uma máquina ou tomar medidas preventivas quando a temperatura alcançar níveis perigosos.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
>
> * Criar um trabalho do Azure Stream Analytics para processar dados no edge.
> * Conectar o novo trabalho do Azure Stream Analytics a outros módulos do IoT Edge.
> * Implante o trabalho do Azure Stream Analytics em um dispositivo IoT Edge no portal do Azure.

<center>

![Diagrama – Arquitetura do tutorial: preparar e implantar trabalho do ASA](./media/tutorial-deploy-stream-analytics/asa-architecture.png)
</center>

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo do Azure IoT Edge:

* Você pode usar uma máquina virtual do Azure como um dispositivo do IoT Edge seguindo as etapas no início rápido para dispositivos do [Linux](quickstart-linux.md) ou do [Windows](quickstart.md).

Recursos de nuvem:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na camada padrão ou gratuito no Azure.

## <a name="create-an-azure-stream-analytics-job"></a>Criar um trabalho do Azure Stream Analytics

Nesta seção, você cria um trabalho do Azure Stream Analytics que executará as seguintes etapas:

* Receber dados de seu dispositivo IoT Edge.
* Consultar os dados de telemetria para obter valores fora de um intervalo definido.
* Agir com relação ao dispositivo IoT Edge com base nos resultados da consulta.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Quando você cria um trabalho do Azure Stream Analytics para execução em um dispositivo IoT Edge, ele precisa ser armazenado de uma forma que possa ser chamado a partir do dispositivo. Você pode usar uma conta de Armazenamento do Azure existente ou criar uma agora.

1. No portal do Azure, acesse **Criar um recurso** > **Armazenamento** > **Conta de armazenamento**.

1. Forneça os seguintes valores para criar sua conta de armazenamento:

   | Campo | Valor |
   | ----- | ----- |
   | Subscription | Escolha a mesma assinatura que o Hub IoT. |
   | Resource group | É recomendável usar o mesmo grupo de recursos para todos os recursos de teste para tutoriais e inícios rápidos do IoT Edge. Por exemplo, **IoTEdgeResources**. |
   | Nome | Forneça um nome exclusivo para sua conta de armazenamento. |
   | Location | Escolha um local perto de você. |

1. Use os valores padrão para os outros campos e selecione **Examinar + Criar**.

1. Examine as configurações e selecione **Criar**.

### <a name="create-a-new-job"></a>Criar um novo trabalho

1. No portal do Azure, vá até **Criar um recurso** > **Internet das Coisas** > **Trabalho do Stream Analytics**.

1. Forneça os seguintes valores para criar seu trabalho:

   | Campo | Valor |
   | ----- | ----- |
   | Nome do trabalho | Forneça um nome para o seu trabalho. Por exemplo, **IoTEdgeJob** |
   | Subscription | Escolha a mesma assinatura que o Hub IoT. |
   | Resource group | É recomendável que você use o mesmo grupo de recursos para todos os recursos de teste que foram criados durante os tutoriais e guias de início rápido do IoT Edge. Por exemplo, **IoTEdgeResources**. |
   | Location | Escolha um local perto de você. |
   | Ambiente de hospedagem | Selecione **Edge**. |

1. Selecione **Criar**.

### <a name="configure-your-job"></a>Configurar seu trabalho

Depois que o trabalho do Stream Analytics for criado no portal do Azure, você pode configurá-lo com uma entrada, uma saída e uma consulta para execução nos dados que passam por ele.

Usando os três elementos de entrada, saída e de consulta, esta seção cria um trabalho que recebe dados de temperatura do dispositivo do IoT Edge. Ele analisa esses dados em uma janela móvel de 30 segundos. Se a temperatura média nessa janela ficar acima de 70 graus, um alerta será enviado ao dispositivo do IoT Edge. Você especificará exatamente de onde os dados são provenientes e avançará para a próxima seção quando implantar o trabalho.  

1. Navegue até o seu trabalho do Stream Analytics no portal do Azure.

1. Em **Topologia do trabalho**, selecione **Entradas** e, em seguida, **Adicionar entrada do fluxo**.

   ![Azure Stream Analytics – adicionar entrada](./media/tutorial-deploy-stream-analytics/asa-input.png)

1. Escolha **Hub do Edge** na lista suspensa.

1. No painel **Nova entrada**, insira **temperatura** como o alias de entrada.

1. Use os valores padrão para os outros campos e selecione **Salvar**.

1. Em **Topologia do Trabalho**, abra **Saídas** e, em seguida, selecione **Adicionar**.

   ![Azure Stream Analytics – adicionar saída](./media/tutorial-deploy-stream-analytics/asa-output.png)

1. Escolha **Hub do Edge** na lista suspensa.

1. No painel **Nova saída**, insira **alerta** como o alias de saída.

1. Use os valores padrão para os outros campos e selecione **Salvar**.

1. Em **Topologia do trabalho**, selecione **Consulta**.

1. Substitua o texto padrão pela consulta a seguir. O código SQL envia um comando de reinicialização para a saída do alerta se a temperatura média da máquina em uma janela de 30 segundos atingir 70 graus. O comando de reinicialização foi pré-programado no sensor como uma ação que pode ser executada.

    ```sql
    SELECT  
        'reset' AS command
    INTO
       alert
    FROM
       temperature TIMESTAMP BY timeCreated
    GROUP BY TumblingWindow(second,30)
    HAVING Avg(machine.temperature) > 70
    ```

1. Selecione **Salvar consulta**.

### <a name="configure-iot-edge-settings"></a>Definição das configurações do IoT Edge

Para preparar seu trabalho do Stream Analytics a ser implantado em um dispositivo do IoT Edge, você precisa associar o trabalho a um contêiner em uma conta de armazenamento. Ao implantar seu trabalho, a definição deste é exportada para o contêiner de armazenamento.

1. Em **Configurar**, selecione **Configurações da conta de armazenamento** e então **Adicionar conta de armazenamento**.

   ![Azure Stream Analytics – adicionar conta de armazenamento](./media/tutorial-deploy-stream-analytics/add-storage-account.png)

1. Selecione a **Conta de armazenamento** que você criou no início deste tutorial no menu suspenso.

1. No campo **Contêiner**, selecione **Criar novo** e forneça um nome para o contêiner de armazenamento.

1. Clique em **Salvar**.

## <a name="deploy-the-job"></a>Implantar o trabalho

Agora você está pronto para implantar o trabalho do Azure Stream Analytics em seu dispositivo do IoT Edge.

Nesta seção, você usa o assistente **Definir módulos** no portal do Azure para criar um *manifesto de implantação*. Um manifesto de implantação é um arquivo JSON que descreve todos os módulos que serão implantados em um dispositivo, os registros de contêiner que armazenam as imagens do módulo, como os módulos devem ser gerenciados e como os módulos podem se comunicar entre si. Seu dispositivo IoT Edge recupera o manifesto de implantação do Hub IoT e, em seguida, usa as informações contidas nele para implantar e configurar todos os seus módulos atribuídos.

Para este tutorial, você deve implantar dois módulos. O primeiro é **SimulatedTemperatureSensor**, um módulo que simula um sensor de temperatura e umidade. O segundo é o seu trabalho do Stream Analytics. O módulo do sensor fornece o fluxo de dados que a consulta do trabalho analisará.

1. No portal do Azure, navegue para o hub IoT.

1. Vá para **IOT Edge** e, em seguida, abra a página de detalhes de seu dispositivo IoT Edge.

1. Selecione **Definir módulos**.  

1. Se você implantou o módulo SimulatedTemperatureSensor neste dispositivo, talvez ele seja automaticamente preenchido. Se não existir, adicione o módulo fazendo o seguinte:

   1. Clique em **Adicionar** e selecione **Módulo do IoT Edge**.
   1. Para o nome, digite **SimulatedTemperatureSensor**.
   1. Como URI da imagem, insira **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0**.
   1. Deixe as outras configurações inalteradas e selecione **Adicionar**.

1. Adicione o trabalho de Edge do Azure Stream Analytics com as seguintes etapas:

   1. Clique em **Adicionar** e selecione **Módulo do Azure Stream Analytics**.
   1. Selecione sua assinatura e o trabalho de Edge do Azure Stream Analytics que você criou.
   1. Clique em **Salvar**.

   Depois de salvar as alterações, os detalhes de seu trabalho do Stream Analytics serão publicados no contêiner de armazenamento que você criou.

1. Quando o módulo do Stream Analytics for adicionado à lista de módulos, selecione o nome dele para ver como ele está estruturado e atualize as configurações na página **Atualizar Módulo do IoT Edge**.

   A guia **Configurações do Módulo** tem o **URI da imagem** que aponta para uma imagem padrão do Azure Stream Analytics. Essa imagem é usada para cada módulo do Stream Analytics implantado em um dispositivo IoT Edge.

   A guia **Configurações do Módulo Gêmeo** mostra o JSON que define a propriedade do ASA (Azure Stream Analytics) chamada **ASAJobInfo**. O valor dessa propriedade aponta para a definição de trabalho em seu contêiner de armazenamento. Esta propriedade é como a imagem do Stream Analytics é configurada com os detalhes de seu trabalho específico.

   Por padrão, o módulo Stream Analytics usa o mesmo nome que o trabalho no qual ele se baseia. Você pode alterar o nome do módulo nesta página, se desejar, mas isso não é necessário.

1. Selecione **Atualizar** ou **Cancelar**.

1. Tome nota do nome do módulo do Stream Analytics, pois você precisará dele na próxima etapa. Em seguida, selecione **Avançar: Rotas** para continuar.

1. Na guia **Rotas**, defina como as mensagens são transmitidas entre os módulos e o Hub IoT. As mensagens são construídas com pares nome/valor. Substitua o nome padrão de `route` e `upstream` e os valores pelos pares mostrados na tabela a seguir, os pares nome/valor a seguir, substituindo as instâncias de _{moduleName}_ pelo nome do módulo do Azure Stream Analytics.

    | Nome | Valor |
    | --- | --- |
    | `telemetryToCloud` | `FROM /messages/modules/SimulatedTemperatureSensor/* INTO $upstream` |
    | `alertsToCloud` | `FROM /messages/modules/{moduleName}/* INTO $upstream` |
    | `alertsToReset` | `FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint("/modules/SimulatedTemperatureSensor/inputs/control")` |
    | `telemetryToAsa` | `FROM /messages/modules/SimulatedTemperatureSensor/* INTO BrokeredEndpoint("/modules/{moduleName}/inputs/temperature")`|

    As rotas que você declara aqui definem o fluxo de dados no dispositivo do IoT Edge. Os dados de telemetria do SimulatedTemperatureSensor são enviados para o Hub IoT e para a entrada de **temperatura** que foi configurada no trabalho do Stream Analytics. As mensagens de saída do **alerta** são enviadas para o Hub IoT e para o módulo SimulatedTemperatureSensor para disparar o comando de reinicialização.

1. Selecione **Avançar: Examinar + Criar**.

1. Na guia **Examinar + Criar**, você poderá ver como as informações fornecidas no assistente são convertidas em um manifesto de implantação JSON. Quando terminar de examinar o manifesto, selecione **Criar**.

1. Você retornará à página de detalhes do dispositivo. Selecione **Atualizar**.  

    Você deverá ver o novo módulo do Stream Analytics em execução, juntamente com os módulos agente do IoT Edge e hub do IoT Edge. Podem ser necessários alguns minutos para que as informações cheguem ao dispositivo IoT Edge e então para que os novos módulos serem iniciados. Se você não vir os módulos que estão em execução imediatamente, continue a atualizar a página.

    ![Módulos SimulatedTemperatureSensor e ASA relatados pelo dispositivo](./media/tutorial-deploy-stream-analytics/module-output2.png)

## <a name="view-data"></a>Exibir dados

Agora você pode acessar o dispositivo do IoT Edge para verificar a interação entre o módulo do Azure Stream Analytics e o módulo SimulatedTemperatureSensor.

1. Verifique se todos os módulos estão em execução no Docker:

   ```cmd/sh
   iotedge list  
   ```

1. Exibir todos os logs do sistema e dados de métricas. Use o nome do módulo do Stream Analytics:

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

1. Veja como o comando de redefinição afeta o SimulatedTemperatureSensor vendo os logs de sensor:

   ```cmd/sh
   iotedge logs SimulatedTemperatureSensor
   ```

   Você pode observar a temperatura do computador aumentar gradualmente até atingir 70 graus por 30 segundos. Em seguida, o módulo do Stream Analytics dispara uma redefinição e a temperatura da máquina cai para 21.

   ![Redefinir a saída do comando nos logs de módulo](./media/tutorial-deploy-stream-analytics/docker_log.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se você pretende continuar no próximo artigo recomendado, pode manter os recursos e as configurações já criados e reutilizá-los. Você também pode continuar usando o mesmo dispositivo IoT Edge como um dispositivo de teste.

Caso contrário, é possível excluir as configurações locais e os recursos do Azure usados neste artigo para evitar encargos.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou um trabalho do Azure Stream Analytics para analisar dados de seu dispositivo IoT Edge. Em seguida, carregou o módulo do Azure Stream Analytics em seu dispositivo IoT Edge para processar e reagir ao aumento de temperatura localmente, além de enviar o fluxo de dados agregados para a nuvem. Continue com os outros tutoriais para saber como o Azure IoT Edge pode criar outras soluções para seus negócios.

> [!div class="nextstepaction"]
> [Implantar um modelo do Azure Machine Learning como um módulo](tutorial-deploy-machine-learning.md)