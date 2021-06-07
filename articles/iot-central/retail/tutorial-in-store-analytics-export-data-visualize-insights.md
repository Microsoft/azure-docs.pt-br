---
title: Tutorial – Exportar dados e visualizar insights no Azure IoT Central
description: Neste tutorial, saiba como exportar dados do IoT Central e visualizar informações em um painel de Power BI.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: dobett
author: dominicbetts
ms.date: 11/12/2019
ms.openlocfilehash: 2695b34745ff02d55f18cebbe87a468f807ca77a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831681"
---
# <a name="tutorial-export-data-from-azure-iot-central-and-visualize-insights-in-power-bi"></a>Tutorial: Exportar dados do Azure IoT Central e visualizar informações no Power BI


Nos dois tutoriais anteriores, você criou e personalizou um aplicativo IoT Central usando o modelo de aplicativo **análise no repositório - check-out**. Neste tutorial, você configura seu aplicativo IoT Central para exportar a telemetria coletada dos dispositivos. Em seguida, você usa Power BI para criar um painel personalizado para o gerenciador de loja para visualizar as informações derivadas da telemetria.

Neste tutorial, você aprenderá a:
> [!div class="checklist"]
> * Configure um aplicativo IoT Central para exportar telemetria para um hub de eventos.
> * Use os Aplicativos Lógicos do Azure para enviar dados de um hub de eventos para um conjunto de dados do Power BI.
> * Crie um painel de Power BI para visualizar os dados no conjunto de dados de streaming.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

* Para concluir os dois tutoriais anteriores, [Crie um aplicativo de análise no repositório no Azure IoT Central](./tutorial-in-store-analytics-create-app.md) e [Personalize o painel do operador e gerencie dispositivos no Azure IOT Central](./tutorial-in-store-analytics-customize-dashboard.md).
* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* Uma conta do Power BI. Se você não tiver uma conta de Power BI, inscreva-se para uma [avaliação gratuita Power BI Pro](https://app.powerbi.com/signupredirect?pbi_source=web) antes de começar.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de criar o hub de eventos e o aplicativo lógico, você precisa criar um grupo de recursos para gerenciá-los. O grupo de recursos deve estar no mesmo local que seu aplicativo **Análise do repositório - Fazer check-out** do IoT Central. Para criar um grupo de recursos:

1. Entre no [portal do Azure](https://portal.azure.com).
1. No painel de navegação esquerdo, selecione **Grupos de recursos**. Em seguida, selecione **Adicionar**.
1. Para **Assinatura**, selecione o nome da assinatura do Azure na qual você deseja criar o aplicativo do IOT Central.
1. Para o nome do **Grupo de recursos**, insira _de análise do repositório_*.
1. Para a **Região**, selecione a mesma região que você escolheu para o aplicativo IoT Central.
1. Selecione **Examinar + criar**.
1. Na página **Examinar + Criar**, selecione **Criar**.

Agora você tem um grupo de recursos chamado **retail-store-analysis** em sua assinatura.

## <a name="create-an-event-hub"></a>Criar um Hub de Evento

Antes de configurar o aplicativo de monitoramento de varejo para exportar a telemetria, você precisa criar um hub de eventos para receber os dados exportados. As etapas a seguir mostram como criar o hub de eventos:

1. No portal do Azure selecione **Criar um recurso** na parte superior esquerda da tela.
1. Em **Pesquisar no Marketplace**, insira _Hubs de Eventos_ e, em seguida, pressione **Enter**.
1. Na página **Hubs de Eventos**, selecione **Criar**.
1. Na página **Criar Namespace**, execute as seguintes etapas:
    * Insira um nome exclusivo para o namespace, como _yourname-retail-store-analysis_. O sistema verifica se o nome está disponível.
    * Escolher o tipo de preço **Básico**.
    * Selecione a mesma **Assinatura** usada para criar o aplicativo do IoT Central.
    * Selecione o grupo de recursos **retail-store-analysis**.
    * Selecione o mesmo local usado para o aplicativo do IoT Central.
    * Selecione **Criar**. Talvez você precise aguardar alguns minutos para o sistema provisionar os recursos.
1. No portal, navegue até o grupo de recursos **retail-store-analysis**. Aguarde até que a implantação seja concluída. Talvez seja necessário selecionar **Atualizar** para atualizar o status da implantação. Você também pode verificar o status da criação do namespace do hub de eventos em **Notificações**.
1. No grupo de recursos **retail-store-analysis**, selecione o **Namespace de Hubs de Eventos**. Você verá a página inicial do seu **Namespace de Hubs de Eventos** no portal.

Agora você tem um **Namespace de Hubs de Eventos**, você pode criar um **Hub de Eventos** para usar com seu aplicativo IoT Central:

1. Na página inicial do seu **Namespace de Hubs de Eventos** no portal, selecione **+ Hub de Eventos**.
1. Na página **Criar Hub de Eventos**, insira _store-telemetry_ como o nome e, em seguida, selecione **Criar**.

Agora você tem um hub de eventos que pode ser usado ao configurar a exportação de dados do seu aplicativo IoT Central:

![Hub de Eventos](./media/tutorial-in-store-analytics-visualize-insights/event-hub.png)

## <a name="configure-data-export"></a>Configurar exportação de dados

Agora que tem um hub de eventos, você pode configurar seu aplicativo de **Análise de repositório – fazer check-out** para exportar a telemetria dos dispositivos conectados. As etapas a seguir mostram como configurar a exportação:

1. Entre em seu aplicativo de **Análise de repositório - Fazer check-out** do IoT Central.
1. Selecione **Exportação de dados** no painel esquerdo.
1. Selecione **Novo > Hubs de Eventos do Azure**.
1. Insira _Exportação de telemetria_ como o **Nome de exibição**.
1. Selecione o **Namespace de Hubs de Eventos**.
1. Selecione o Hub de Eventos **store-telemetry**.
1. Desative **Dispositivos** e **Modelos de dispositivo** na seção **Dados a serem exportados**.
1. Clique em **Salvar**.

A exportação de dados pode levar alguns minutos para começar a enviar telemetria para o Hub de eventos. Você pode ver o status da exportação na página **Exportações de dados**:

![Configuração contínua de exportação de dados](./media/tutorial-in-store-analytics-visualize-insights/export-configuration.png)

## <a name="create-the-power-bi-datasets"></a>Criar os conjuntos de dados do Power BI

Seu painel de Power BI exibirá dados do seu aplicativo de monitoramento de varejo. Nesta solução, você usa conjuntos de dados de streaming de Power BI como a fonte de dados para o painel de Power BI. Nesta seção, você define o esquema dos conjuntos de dados de streaming para que o aplicativo lógico possa encaminhar dados do hub de eventos. As etapas a seguir mostram como criar dois conjuntos de dados de streaming para os sensores ambientais e um conjunto de dados de streaming para o sensor de ocupação:

1. Entre na sua conta do **Power BI**.
1. Selecione **Espaços de trabalho** e, em seguida, selecione **Criar um de espaço de trabalho**.
1. Na página **Criar um espaço de trabalho**, insira _Análise de repositório - Fazer check-out_ como o **Nome do espaço de trabalho**.
1. Role até a parte inferior da página **Bem-vindo à Análise de repositório - Fazer check-out do espaço de trabalho** e selecione **Ignorar**.
1. Na página do espaço de trabalho, selecione **Criar > Conjunto de dados de streaming**.
1. Na página **Novo conjunto de dados de streaming**, escolha **API** e, em seguida, selecione **Próximo**.
1. Insira _Sensor de Zona 1_ como o **Nome do conjunto de dados**.
1. Insira os três **Valores de stream** na tabela a seguir:

    | Nome do valor  | Tipo de valor |
    | ----------- | ---------- |
    | Timestamp   | Datetime   |
    | Umidade    | Número     |
    | Temperatura | Número     |

1. Ative a **Análise de dados histórica**.
1. Selecione **Criar**, depois **Concluído**.
1. Crie outro conjunto de dados de streaming chamado **Sensor de Zona 2** com o mesmo esquema e configurações que o conjunto de dados de streaming do **Sensor de Zona 1**.

Agora você tem dois conjuntos de dados de streaming. O aplicativo lógico roteará a telemetria dos dois sensores ambientais conectados a seu aplicativo de **Análise de repositório - Fazer check-out** para esses dois conjuntos de dados de valores:

![Conjuntos de dados de zona](./media/tutorial-in-store-analytics-visualize-insights/dataset-1.png)

Essa solução usa um conjunto de dados de streaming para cada sensor porque não é possível aplicar filtros para transmitir dados em Power BI.

Você também precisa de um conjunto de dados de streaming para a telemetria de ocupação:

1. Na página do espaço de trabalho, selecione **Criar > Conjunto de dados de streaming**.
1. Na página **Novo conjunto de dados de streaming**, escolha **API** e, em seguida, selecione **Próximo**.
1. Insira _Sensor de ocupação_ como o **Nome do conjunto de dados**.
1. Insira os cinco **Valores de stream** na tabela a seguir:

    | Nome do valor     | Tipo de valor |
    | -------------- | ---------- |
    | Timestamp      | Datetime   |
    | Comprimento da fila 1 | Número     |
    | Comprimento da fila 2 | Número     |
    | Tempo de espera 1   | Número     |
    | Tempo de espera 2   | Número     |

1. Ative a **Análise de dados histórica**.
1. Selecione **Criar**, depois **Concluído**.

Agora você tem um terceiro conjunto de dados de streaming que armazena valores do sensor de ocupação simulado. Esse sensor informa o comprimento da fila nos dois check-outs no repositório e por quanto tempo os clientes estão aguardando nessas filas:

![Conjunto de uma ocupação](./media/tutorial-in-store-analytics-visualize-insights/dataset-2.png)

## <a name="create-a-logic-app"></a>Criar um aplicativo lógico

Nessa solução, o aplicativo lógico lê a telemetria do hub de eventos, analisa os dados e, em seguida, envia-os para os conjuntos de dados de streaming de Power BI que você criou.

Antes de criar o aplicativo lógico, você precisará das IDs de dispositivo dos dois sensores RuuviTag conectados ao seu aplicativo IoT Central no tutorial [Criar um aplicativo de análise na loja no Azure IoT Central](./tutorial-in-store-analytics-create-app.md):

1. Entre em seu aplicativo de **Análise de repositório - Fazer check-out** do IoT Central.
1. Selecione **Dispositivos** no painel à esquerda. Em seguida, selecione **RuuviTag**.
1. Anote as **IDs do dispositivo**. Na captura de tela a seguir, as IDs são **f5dcf4ac32e8** e **e29ffc8d5326**:

    ![IDs de dispositivo](./media/tutorial-in-store-analytics-visualize-insights/device-ids.png)

As etapas a seguir mostram como criar o aplicativo lógico no portal do Azure:

1. Entre no [portal do Azure](https://portal.azure.com) e selecione **Criar um recurso** na parte superior esquerda da tela.
1. Em **Pesquisar no Marketplace**, insira _Aplicativo Lógico_ e, em seguida, pressione **Enter**.
1. Na página **Aplicativo lógico**, selecione **Criar**.
1. Na página de criação de **Aplicativo lógico**:
    * Insira um nome único para seu aplicativo lógico, como _yourname-retail-store-analysis_.
    * Selecione a mesma **Assinatura** usada para criar o aplicativo do IoT Central.
    * Selecione o grupo de recursos **retail-store-analysis**.
    * Selecione o mesmo local usado para o aplicativo do IoT Central.
    * Selecione **Criar**. Talvez você precise aguardar alguns minutos para o sistema provisionar os recursos.
1. No Portal do Azure, navegue até o novo aplicativo lógico.
1. Na página **Designer de Aplicativos Lógicos**, role para baixo e selecione **Aplicativo Lógico em Branco**.
1. Em **Pesquisar conectores e gatilhos**, insira _Hubs de Eventos_.
1. Em **Gatilhos**, selecione **Quando eventos estiverem disponíveis no Hub de eventos**.
1. Insira _Armazenar telemetria_ como o **Nome da conexão** e selecione o **Namespace dos Hubs de Eventos**.
1. Selecione a política **RootManageSharedAccess** e selecione **Criar**.
1. Na ação **Quando os eventos estiverem disponíveis no Hub de Eventos**:
    * Em **Nome do Hub de Eventos**, selecione **store-telemetry**.
    * Em **Tipo de conteúdo**, selecione **aplicativo/json**.
    * Defina o **Intervalo** para três e a **Frequência** para segundos
1. Selecione **Salvar** para salvar seu aplicativo lógico.

Para adicionar a lógica ao design do aplicativo lógico, selecione **Modo de exibição de código**:

1. Substitua o `"actions": {},` pelo seguinte JSON. Substitua os dois espaços reservados `[YOUR RUUVITAG DEVICE ID 1]` e `[YOUR RUUVITAG DEVICE ID 2]` pelas IDs que você anotou de seus dois dispositivos RuuviTag:

    ```json
    "actions": {
        "Initialize_Device_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "DeviceID",
                        "type": "String"
                    }
                ]
            },
            "runAfter": {},
            "type": "InitializeVariable"
        },
        "Initialize_Interface_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "InterfaceID",
                        "type": "String",
                        "value": "Other"
                    }
                ]
            },
            "runAfter": {
                "Initialize_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "InitializeVariable"
        },
        "Parse_Properties": {
            "inputs": {
                "content": "@triggerBody()?['Properties']",
                "schema": {
                    "properties": {
                        "iothub-connection-auth-generation-id": {
                            "type": "string"
                        },
                        "iothub-connection-auth-method": {
                            "type": "string"
                        },
                        "iothub-connection-device-id": {
                            "type": "string"
                        },
                        "iothub-enqueuedtime": {
                            "type": "string"
                        },
                        "iothub-interface-name": {
                            "type": "string"
                        },
                        "iothub-message-source": {
                            "type": "string"
                        },
                        "x-opt-enqueued-time": {
                            "type": "string"
                        },
                        "x-opt-offset": {
                            "type": "string"
                        },
                        "x-opt-sequence-number": {
                            "type": "integer"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Parse_Telemetry": {
            "inputs": {
                "content": "@triggerBody()?['ContentData']",
                "schema": {
                    "properties": {
                        "DwellTime1": {
                            "type": "number"
                        },
                        "DwellTime2": {
                            "type": "number"
                        },
                        "count1": {
                            "type": "number"
                        },
                        "count2": {
                            "type": "number"
                        },
                        "humidity": {
                            "type": "number"
                        },
                        "temperature": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Set_Device_ID_variable": {
            "inputs": {
                "name": "DeviceID",
                "value": "@body('Parse_Properties')?['iothub-connection-device-id']"
            },
            "runAfter": {
                "Parse_Properties": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Set_Interface_ID_variable": {
            "inputs": {
                "name": "InterfaceID",
                "value": "@body('Parse_Properties')?['iothub-interface-name']"
            },
            "runAfter": {
                "Set_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Switch_by_DeviceID": {
            "cases": {
                "Occupancy": {
                    "actions": {
                        "Switch_by_InterfaceID": {
                            "cases": {
                                "Dwell_Time_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_v2_1l0"
                                },
                                "People_Count_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_iv"
                                }
                            },
                            "default": {
                                "actions": {}
                            },
                            "expression": "@variables('InterfaceID')",
                            "runAfter": {},
                            "type": "Switch"
                        }
                    },
                    "case": "Occupancy"
                },
                "Zone 2 environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 2]"
                },
                "Zone_1_environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 1]"
                }
            },
            "default": {
                "actions": {}
            },
            "expression": "@variables('DeviceID')",
            "runAfter": {
                "Parse_Telemetry": [
                    "Succeeded"
                ],
                "Set_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "Switch"
        }
    },
    ```

1. Selecione **Salvar** e, em seguida, selecione **Designer** para ver a versão visual da lógica que você adicionou:

    ![Design de aplicativos lógicos](./media/tutorial-in-store-analytics-visualize-insights/logic-app.png)

1. Selecione **Alternar por DeviceID** para expandir a ação. Em seguida, selecione **Ambiente de Zona 1** e selecione **Adicionar uma ação**.
1. Em **Pesquisar conectores e ações**, insira **Power BI** e pressione **Enter**.
1. Selecione a ação **Adicionar linhas a uma ação de conjunto de dados (visualização)** .
1. Selecione **Entrar** e siga os prompts para entrar em sua conta do Power BI.
1. Após a conclusão do processo de entrada, na ação **Adicionar linhas a um conjunto de dados**:
    * Selecione **Análise do repositório - Fazer check-out** como o espaço de trabalho.
    * Insira **Sensor de Zona 1** como o conjunto de dados.
    * Selecione **RealTimeData** como a tabela.
    * Selecione **Adicionar novo parâmetro** e, em seguida, selecione os campos **Carimbo de data/hora**, **Umidade** e **Temperatura**.
    * Selecione o campo **Carimbo de data/hora** e, em seguida, selecione **x-opt-enqueuedtime** na lista de **Conteúdo dinâmico**.
    * Selecione o campo **Umidade** e, em seguida, selecione **Ver mais** ao lado de **Analisar telemetria**. Selecione **umidade**.
    * Selecione o campo de **Temperatura** e, em seguida, selecione **Ver mais** ao lado de **Analisar telemetria**. Em seguida, selecione **temperatura**.
    * Selecione **Salvar** para salvar as alterações. A ação **Ambiente de Zona 1** é semelhante à captura de tela a seguir: ![Ambiente de Zona 1](./media/tutorial-in-store-analytics-visualize-insights/zone-1-action.png)
1. Selecione a ação **Ambiente de Zona 2** e selecione **Adicionar uma ação**.
1. Em **Pesquisar conectores e ações**, insira **Power BI** e pressione **Enter**.
1. Selecione a ação **Adicionar linhas a uma ação de conjunto de dados (visualização)** .
1. Na ação **Adicionar linhas a um conjunto de dados 2**:
    * Selecione **Análise do repositório - Fazer check-out** como o espaço de trabalho.
    * Selecione **Sensor de Zona 2** como o conjunto de dados.
    * Selecione **RealTimeData** como a tabela.
    * Selecione **Adicionar novo parâmetro** e, em seguida, selecione os campos **Carimbo de data/hora**, **Umidade** e **Temperatura**.
    * Selecione o campo **Carimbo de data/hora** e, em seguida, selecione **x-opt-enqueuedtime** na lista de **Conteúdo dinâmico**.
    * Selecione o campo **Umidade** e, em seguida, selecione **Ver mais** ao lado de **Analisar telemetria**. Selecione **umidade**.
    * Selecione o campo de **Temperatura** e, em seguida, selecione **Ver mais** ao lado de **Analisar telemetria**. Em seguida, selecione **temperatura**.
    Selecione **Salvar** para salvar as alterações.  A ação **Ambiente de Zona 2** é semelhante à captura de tela a seguir: ![Ambiente de Zona 2](./media/tutorial-in-store-analytics-visualize-insights/zone-2-action.png)
1. Selecione a ação **Ocupação** e, em seguida, selecione a ação **Alternar por ID de interface**.
1. Selecione a ação **Interface de tempo de espera** e selecione **Adicionar uma ação**.
1. Em **Pesquisar conectores e ações**, insira **Power BI** e pressione **Enter**.
1. Selecione a ação **Adicionar linhas a uma ação de conjunto de dados (visualização)** .
1. Na ação **Adicionar linhas a um conjunto de dados**:
    * Selecione **Análise do repositório - Fazer check-out** como o espaço de trabalho.
    * Selecione **Sensor de ocupação** como o conjunto de dados.
    * Selecione **RealTimeData** como a tabela.
    * Selecione **Adicionar novo parâmetro** e, em seguida, selecione os campos **Carimbo de data/hora**, **Tempo de espera 1** e **Tempo de espera 2**.
    * Selecione o campo **Carimbo de data/hora** e, em seguida, selecione **x-opt-enqueuedtime** na lista de **Conteúdo dinâmico**.
    * Selecione o campo **Tempo de espera 1** e, em seguida, selecione **Ver mais** ao lado de **Analisar telemetria**. Em seguida, selecione **Tempo de espera 1**.
    * Selecione o campo **Tempo de espera 2** e, em seguida, selecione **Ver mais** ao lado de **Analisar telemetria**. Em seguida, selecione **Tempo de espera 2**.
    * Selecione **Salvar** para salvar as alterações. A ação **Interface de tempo de espera** é semelhante à captura de tela a seguir: ![Captura de tela que mostra a ação "Interface de tempo de espera".](./media/tutorial-in-store-analytics-visualize-insights/occupancy-action-1.png)
1. Selecione a ação **Interface de contagem de pessoas** e selecione **Adicionar uma ação**.
1. Em **Pesquisar conectores e ações**, insira **Power BI** e pressione **Enter**.
1. Selecione a ação **Adicionar linhas a uma ação de conjunto de dados (visualização)** .
1. Na ação **Adicionar linhas a um conjunto de dados**:
    * Selecione **Análise do repositório - Fazer check-out** como o espaço de trabalho.
    * Selecione **Sensor de ocupação** como o conjunto de dados.
    * Selecione **RealTimeData** como a tabela.
    * Selecione **Adicionar novo parâmetro** e, em seguida, selecione os campos **Carimbo de data/hora**, **Comprimento da fila 1** e **Tamanho da fila 2**.
    * Selecione o campo **Carimbo de data/hora** e, em seguida, selecione **x-opt-enqueuedtime** na lista de **Conteúdo dinâmico**.
    * Selecione o campo **Comprimento da fila 1** e, em seguida, selecione **Ver mais** ao lado de **Analisar telemetria**. Em seguida, selecione **count1**.
    * Selecione o campo **Comprimento da fila 2** e, em seguida, selecione **Ver mais** ao lado de **Analisar telemetria**. Em seguida, selecione **count2**.
    * Selecione **Salvar** para salvar as alterações. A ação **Interface de contagem de pessoas** é semelhante à captura de tela a seguir: ![Ação de ocupação](./media/tutorial-in-store-analytics-visualize-insights/occupancy-action-2.png)

O aplicativo lógico é executado automaticamente. Para ver o status de cada execução, navegue até a página de **Visão geral** do aplicativo lógico no portal do Azure:

## <a name="create-a-power-bi-dashboard"></a>Criar um painel de Power BI

Agora você tem telemetria fluindo do seu aplicativo IoT Central por meio de seu hub de eventos. Em seguida, seu aplicativo lógico analisa as mensagens do hub de eventos e as adiciona a um conjunto de dados de streaming do Power BI. Agora, você pode criar um painel de Power BI para visualizar a telemetria:

1. Entre na sua conta do **Power BI**.
1. Selecione **Espaços de trabalho > Análise do repositório - Fazer check-out**.
1. Selecione **Criar > Painel**.
1. Insira **Análise do repositório** como o nome do painel e selecione **Criar**.

### <a name="add-line-charts"></a>Adicionar gráficos de linhas

Adicione quatro blocos de gráfico de linhas para mostrar a temperatura e a umidade dos dois sensores ambientais. Use as informações na tabela a seguir para criar os blocos. Para adicionar cada bloco, comece selecionando **... (Mais opções) > Adicionar bloco**. Selecione **Dados de streaming personalizados** e, em seguida, selecione **Próximo**:

| Configuração | Gráfico nº 1 | Gráfico nº 2 | Gráfico nº 3 | Gráfico nº 4 |
| ------- | -------- | -------- | -------- | -------- |
| Dataset | Sensor de Zona 1 | Sensor de Zona 1 | Sensor de Zona 2 | Sensor de Zona 2 |
| Tipo de visualização | Gráfico de Linhas | Gráfico de Linhas | Gráfico de Linhas | Gráfico de Linhas |
| Axis | Timestamp | Timestamp | Timestamp | Timestamp |
| Valores | Temperatura | Umidade | Temperatura | Umidade |
| Janela de tempo | 60 minutos | 60 minutos | 60 minutos | 60 minutos |
| Title | Temperatura (1 hora) | Umidade (1 hora) | Temperatura (1 hora) | Umidade (1 hora) |
| Subtítulo | Zona 1 | Zona 1 | Zona 2 | Zona 2 |

A captura de tela a seguir mostra as configurações do primeiro gráfico:

![Configurações do gráfico de linhas](./media/tutorial-in-store-analytics-visualize-insights/line-chart.png)

### <a name="add-cards-to-show-environmental-data"></a>Adicionar cartões para mostrar dados ambientais

Adicione quatro blocos de cartão para mostrar a temperatura e a umidade mais recente dos dois sensores ambientais. Use as informações na tabela a seguir para criar os blocos. Para adicionar cada bloco, comece selecionando **... (Mais opções) > Adicionar bloco**. Selecione **Dados de streaming personalizados** e, em seguida, selecione **Próximo**:

| Configuração | Cartão nº 1 | Cartão nº 2 | Cartão nº 3 | Cartão nº 4 |
| ------- | ------- | ------- | ------- | ------- |
| Dataset | Sensor de Zona 1 | Sensor de Zona 1 | Sensor de Zona 2 | Sensor de Zona 2 |
| Tipo de visualização | Card | Card | Card | Card |
| Campos | Temperatura | Umidade | Temperatura | Umidade |
| Title | Temperatura (F) | Umidade (%) | Temperatura (F) | Umidade (%) |
| Subtítulo | Zona 1 | Zona 1 | Zona 2 | Zona 2 |

A captura de tela a seguir mostra as configurações do primeiro cartão:

![Configurações do cartão](./media/tutorial-in-store-analytics-visualize-insights/card-settings.png)

### <a name="add-tiles-to-show-checkout-occupancy-data"></a>Adicionar blocos para mostrar dados de ocupação de check-out

Adicione quatro blocos de cartão para mostrar o comprimento da fila e o tempo de espera para os dois check-outs no repositório. Use as informações na tabela a seguir para criar os blocos. Para adicionar cada bloco, comece selecionando **... (Mais opções) > Adicionar bloco**. Selecione **Dados de streaming personalizados** e, em seguida, selecione **Próximo**:

| Configuração | Cartão nº 1 | Cartão nº 2 | Cartão nº 3 | Cartão nº 4 |
| ------- | ------- | ------- | ------- | ------- |
| Dataset | Sensor de ocupação | Sensor de ocupação | Sensor de ocupação | Sensor de ocupação |
| Tipo de visualização | Gráfico de colunas clusterizado | Gráfico de colunas clusterizado | Medidor | Medidor |
| Axis    | Timestamp | Timestamp | N/D | N/D |
| Valor | Tempo de espera 1 | Tempo de espera 2 | Comprimento da fila 1 | Comprimento da fila 2 |
| Janela de tempo | 60 minutos | 60 minutos |  N/D | N/D |
| Title | Tempo de espera | Tempo de espera | Comprimento da fila | Comprimento da fila |
| Subtítulo | Fazer check-out 1 | Fazer check-out 2 | Fazer check-out 1 | Fazer check-out 2 |

Redimensione e reorganize os blocos no painel para se parecer com a captura de tela a seguir:

![Captura de tela que mostra o dashboard do Power BI com blocos redimensionados e reorganizados.](./media/tutorial-in-store-analytics-visualize-insights/pbi-dashboard.png)

Você pode adicionar alguns recursos gráficos adicionais para personalizar ainda mais o painel:

![Painel do Power BI](./media/tutorial-in-store-analytics-visualize-insights/pbi-dashboard-graphics.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se tiver concluído o aplicativo IoT Central, você poderá excluí-lo entrando no aplicativo e navegando até a página **Configurações do aplicativo** na seção **Administração**.

Se você quiser manter o aplicativo, mas reduzir os custos associados a ele, desabilite a exportação de dados que está enviando telemetria para o hub de eventos.

Você pode excluir o Hub de eventos e o aplicativo lógico no portal do Azure excluindo o grupo de recursos chamado **retail-store-analysis**.

Você pode excluir seus conjuntos de dados e painel de Power BI excluindo o espaço de trabalho da página de configurações de Power BI do espaço de trabalho.

## <a name="next-steps"></a>Próximas etapas

Esses três tutoriais mostraram uma solução de ponta a ponta que usa o modelo de aplicativo **Análise de repositório - Fazer check-out** do IoT Central. Você conectou dispositivos ao aplicativo, usou IoT Central para monitorar os dispositivos e usou Power BI para criar um painel para exibir informações da telemetria do dispositivo. Uma próxima etapa recomendada é explorar um dos outros modelos de aplicativo IoT Central:

> [!div class="nextstepaction"]
> [Criar soluções de energia com o IoT Central](../energy/overview-iot-central-energy.md)
