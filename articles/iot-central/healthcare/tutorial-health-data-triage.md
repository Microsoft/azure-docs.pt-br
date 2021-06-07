---
title: Tutorial – Criar um painel de triagem de dados de saúde com o Azure IoT Central | Microsoft Docs
description: Tutorial – Aprenda a criar um painel de triagem de dados de saúde usando os modelos de aplicativo do Azure IoT Central.
author: philmea
ms.author: philmea
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: d227d934eedd31342ce419576fffe7cea17efb1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101742757"
---
# <a name="tutorial-build-a-power-bi-provider-dashboard"></a>Tutorial: Criar um painel do provedor do Power BI

Ao criar a sua solução de monitoramento contínuo de pacientes, crie também um dashboard para a equipe de atendimento de um hospital visualizar os dados dos pacientes. Neste tutorial, você aprenderá a criar um dashboard de streaming em tempo real do Power BI com base no modelo de aplicativo de monitoramento contínuo de pacientes do IoT Central. Se o caso de uso não exigir acesso a dados em tempo real, você poderá usar o [Dashboard do Power BI do IoT Central](../core/howto-connect-powerbi.md), que tem um processo de implantação simplificado. 

:::image type="content" source="media/dashboard-gif-3.gif" alt-text="GIF do dashboard":::

A arquitetura básica seguirá esta estrutura:

:::image type="content" source="media/dashboard-architecture.png" alt-text="Dashboard de Triagem do Provedor":::

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Exportar dados do Azure IoT Central para os Hubs de Eventos do Azure
> * Configurar um conjunto de dados de streaming do Power BI
> * Conectar seu Aplicativo Lógico aos Hubs de Eventos do Azure
> * Transmitir dados para o Power BI de seu Aplicativo Lógico
> * Crie um dashboard em tempo real para os sinais vitais dos pacientes


## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Um modelo de aplicativo de monitoramento contínuo de pacientes do Azure IoT Central. Se você ainda não tiver um, poderá seguir as etapas para [Implantar um modelo de aplicativo](overview-iot-central-healthcare.md).

* Um [namespace de Hubs de Eventos do Azure e um Hub de Eventos](../../event-hubs/event-hubs-create.md).

* O Aplicativo Lógico que você deseja que acesse o Hub de Eventos. Para iniciar o Aplicativo Lógico com um gatilho dos Hubs de Eventos do Azure, será necessário um [Aplicativo Lógico em branco](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Uma conta de serviço do Power BI. Se você ainda não tiver uma, poderá [criar uma conta de avaliação gratuita para serviço do Power BI](https://app.powerbi.com/). Se você nunca usou o Power BI, pode ser útil ver a [Introdução ao Power BI](/power-bi/service-get-started).


## <a name="set-up-a-continuous-data-export-to-azure-event-hubs"></a>Configurar uma exportação de dados contínua para os Hubs de Eventos do Azure
Primeiro, você precisará configurar uma exportação de dados contínua de seu modelo de aplicativo Azure IoT Central para o Hub de Eventos do Azure em sua assinatura. Você pode fazer isso seguindo as etapas neste tutorial do Azure IoT Central para [Exportar para Hubs de Eventos](../core/howto-export-data.md). Você só precisará exportar para a telemetria para os fins deste tutorial.


## <a name="create-a-power-bi-streaming-dataset"></a>Criar um conjunto de dados de streaming do Power BI

1. Entre na sua conta do Power BI.

1. Em seu Workspace preferido, crie um novo conjunto de dados de streaming selecionando o botão **+ Criar** no canto superior direito da barra de ferramentas. Você precisará criar um conjunto de dados separado para cada paciente que você gostaria de ter em seu painel.

   :::image type="content" source="media/create-streaming-dataset.png" alt-text="Criar um conjunto de dados de streaming":::


1. Escolha **API** para a origem de seu conjunto de dados.

1. Insira um **nome** (por exemplo, o nome de um paciente) para seu conjunto de dados e preencha os valores de seu fluxo. Você pode ver um exemplo abaixo com base nos valores provenientes dos dispositivos simulados no modelo de aplicativo de monitoramento contínuo de pacientes. O exemplo tem dois pacientes:

   * Teddy Silvers, que têm dados da Faixa de Inteligente de Joelho.
   * Yesenia Sanford, que tem dados do Emplastro de Sinais Vitais Inteligente.

   :::image type="content" source="media/enter-dataset-values.png" alt-text="Inserir valores do conjunto de dados":::

Para saber mais sobre o streaming de conjuntos de dados no Power BI, leia este documento em [streaming em tempo real no Power BI](/power-bi/service-real-time-streaming).


## <a name="connect-your-logic-app-to-azure-event-hubs"></a>Conectar seu Aplicativo Lógico aos Hubs de Eventos do Azure

Para conectar seu Aplicativo Lógico aos hubs de eventos do Azure, siga as instruções descritas neste documento em [Como enviar eventos com os Hubs de Eventos do Azure e os Aplicativos Lógicos do Azure](../../connectors/connectors-create-api-azure-event-hubs.md#add-event-hubs-action). Aqui estão alguns parâmetros sugeridos:

|Parâmetro|Valor|
|---|---|
|Tipo de conteúdo|aplicativo/json|
|Intervalo|3|
|Frequência|Segundo|

No final desta etapa, o Designer de Aplicativo Lógico deve se parecer com isto:

>[!div class="mx-imgBorder"] 
>![Os Aplicativos Lógicos conectam-se aos Hubs de Eventos](media/eh-logic-app.png)

:::image type="content" source="media/enter-dataset-values.png" alt-text="Inserir valores do conjunto de dados":::


## <a name="stream-data-to-power-bi-from-your-logic-app"></a>Transmitir dados para o Power BI de seu Aplicativo Lógico

A próxima etapa será analisar os dados provenientes do seu Hub de Eventos para transmiti-los para os conjuntos de dados do Power BI criados anteriormente.

Antes de fazer isso, você precisará entender o conteúdo JSON que está sendo enviado de seu dispositivo para o Hub de Eventos. Você pode fazer isso examinando este [esquema de exemplo](../core/howto-export-data.md#telemetry-format) e modificando-o para corresponder a seu esquema ou usando o [gerenciador do Barramento de Serviço](https://github.com/paolosalvatori/ServiceBusExplorer) para inspecionar as mensagens. Se você estiver usando os aplicativos de monitoramento contínuo do paciente, suas mensagens serão assim:

**Telemetria de Patch de Sinais Vitais Inteligente**

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```

**Telemetria de Faixa de Inteligente de Joelho**

```json
{
  "Acceleration": {
    "x": 72.73510947763711,
    "y": 72.73510947763711,
    "z": 72.73510947763711
  },
  "RangeOfMotion": 123,
  "KneeBend": 3
}
```

**Propriedades**

```json
{
  "iothub-connection-device-id": "1qsi9p8t5l2",
  "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",  \"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
  "iothub-connection-auth-generation-id": "637063718586331040",
  "iothub-enqueuedtime": 1571681440990,
  "iothub-message-source": "Telemetry",
  "iothub-interface-name": "Patient_health_data_3bk",
  "x-opt-sequence-number": 7,
  "x-opt-offset": "3672",
  "x-opt-enqueued-time": 1571681441317
}
```

1. Agora que você inspecionou suas cargas JSON, volte para o Designer de Aplicativo Lógico e selecione **+ Nova Etapa**. Pesquise e adicione **Inicializar variável** como a próxima etapa e insira os seguintes parâmetros:

   |Parâmetro|Valor|
   |---|---|
   |Nome|Nome da Interface|
   |Type|String|

   Clique em **Salvar**. 

1. Adicione outra variável chamada **Corpo** com o Tipo **Cadeia de Caracteres**. Seu Aplicativo Lógico terá estas ações adicionadas:

   :::image type="content" source="media/initialize-string-variables.png" alt-text="Inicializar variáveis":::
    
1. Selecione **+ Nova Etapa** e adicione uma ação **Analisar JSON**. Altere o nome para **Analisar Propriedades**. Para o Conteúdo, escolha **Propriedades** provenientes do Hub de Eventos. Selecione **Usar conteúdo de exemplo para gerar esquema** na parte inferior e cole o conteúdo de exemplo da seção Propriedades acima.

1. Em seguida, escolha a ação **Definir variável** e atualize sua variável **Nome da Interface** com o **iothub-interface-name** das propriedades JSON analisadas.

1. Adicione um Controle de **Dividir** como a próxima ação e escolha a variável **Nome da Interface** como o parâmetro Ligado. Você usará isso para direcionar os dados para o conjunto de dados correto.

1. No aplicativo Azure IoT Central, localize o Nome da Interface dos dados de integridade do Patch de Sinais Vitais Inteligente e os dados de saúde da Faixa de Inteligente de Joelho na exibição **Modelos de Dispositivo**. Crie dois casos diferentes para o Controle de **Opção** para cada Nome de Interface e renomeie o controle adequadamente. Você pode definir o caso Padrão para usar o Controle **Encerrar** e escolher que status gostaria de mostrar.

   :::image type="content" source="media/split-by-interface.png" alt-text="Controle de divisão":::

1. Para o caso do **Patch de Sinais Vitais Inteligente**, adicione uma ação **Analisar JSON**. Para o Conteúdo, escolha **Conteúdo** proveniente do Hub de Eventos. Copie e cole os conteúdos de exemplo para o Patch de Sinais Vitals Inteligente acima para gerar o esquema.

1. Adicione uma ação **Definir variável** e atualize a variável **Corpo** com o **Corpo** do JSON analisado na Etapa 7.

1. Adicione uma **Condição** controle como sua próxima ação e defina a condição como **Corpo**, **contém**, **Frequência Cardíaca**. Com isso, você tenha o conjunto de dados correto proveniente do Patch de Sinais Vitais Inteligente antes de preencher o conjunto de dados do Power BI. As etapas 7 a 9 serão assim:

   :::image type="content" source="media/smart-vitals-pbi.png" alt-text="Adicionar condição de Sinais Vitais Inteligentes":::

1. Para o caso **Verdadeiro** da condição, adicione uma ação que chame a funcionalidade **Adicionar linhas a um conjunto de dados** do Power BI. Você precisará entrar no Power BI para isso. Seu caso **Falso** pode usar novamente o controle **Encerrar**.

1. Escolha o **Workspace**, o **Conjunto de Dados** e a **Tabela** adequados. Mapeie os parâmetros especificados ao criar o conjunto de fluxos de transmissão no Power BI para os valores JSON analisados que estão vindo de seu Hub de Eventos. Suas ações preenchidas devem ser assim:
 
   :::image type="content" source="media/add-rows-yesenia.png" alt-text="Adicionar linhas ao Power BI":::

1. Para o caso de troca de **Faixa de Inteligente de Joelho**, adicione uma ação **Analisar JSON** para analisar o conteúdo, semelhante à Etapa 7. Em seguida, **Adicione linhas a um conjunto de dados** para atualizar seu conjunto de dados de Teddy Silvers no Power BI.

   :::image type="content" source="media/knee-brace-pbi.png" alt-text="Captura de tela que mostra como adicionar linhas a um conjunto de dados":::

1. Pressione **Salvar** e, em seguida, execute o Aplicativo Lógico.


## <a name="build-a-real-time-dashboard-for-patient-vitals"></a>Crie um dashboard em tempo real para os sinais vitais dos pacientes

Agora, volte para o Power BI e selecione **+ Criar** para criar um **Dashboard**. Dê um nome a seu dashboard e clique em **Criar**.

Selecione os três pontos na barra de navegação superior e, em seguida, selecione **+ Adicionar bloco**.

:::image type="content" source="media/add-tile.png" alt-text="Adicionar bloco ao dashboard":::

Escolha o tipo de bloco que você deseja adicionar e personalize seu aplicativo da maneira que quiser.


## <a name="clean-up-resources"></a>Limpar os recursos

Se você não quiser continuar usando este aplicativo, exclua seus recursos seguindo estas etapas:

1. No portal do Azure, você pode excluir os recursos do Hub de Eventos e dos Aplicativos Lógicos que você criou.

1. Para seu aplicativo IoT Central, vá para a guia Administração e selecione **Excluir**.


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Diretrizes de arquitetura de monitoramento contínuo de pacientes](concept-continuous-patient-monitoring-architecture.md)