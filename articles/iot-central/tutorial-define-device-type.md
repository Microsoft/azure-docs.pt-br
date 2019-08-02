---
title: Definir um novo tipo de dispositivo no Azure IoT Central | Microsoft Docs
description: Este tutorial mostra a você, como um construtor, como definir um novo tipo de dispositivo no seu aplicativo Azure IoT Central. Você define a telemetria, estado, propriedades e configurações para o seu tipo.
author: dominicbetts
ms.author: dobett
ms.date: 06/07/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 54884728533aeff0472ed99660be00478227fbcd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056771"
---
# <a name="tutorial-define-a-new-device-type-in-your-azure-iot-central-application"></a>Tutorial: Definir um novo tipo de dispositivo em seu aplicativo do Azure IoT Central

Este tutorial mostra a você, como um construtor, como usar um modelo de dispositivo para definir um novo tipo de dispositivo no seu aplicativo Microsoft Azure IoT Central. Um modelo de dispositivo define a telemetria, estado, propriedades e configurações para o seu tipo de dispositivo.

Para permitir que você teste o seu aplicativo antes de conectá-lo a um dispositivo real, o IoT Central gera um dispositivo simulado a partir do modelo de dispositivo quando você o cria.

Neste tutorial, você criará um modelo de dispositivo de **Ar-condicionado conectado**. Um dispositivo de ar-condicionado conectado:

* Envia a telemetria como temperatura e umidade.
* Relata o estado, como se ele está ligado ou desligado.
* Tem propriedades de dispositivo como versão de firmware e número de série.
* Tem configurações como temperatura de destino.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um novo modelo de dispositivo
> * Adicionar telemetria ao seu dispositivo
> * Exibir telemetria simulada
> * Definir uma medida de evento
> * Exibir eventos simulados
> * Definir medida de estado
> * Exibir estado simulado
> * Usar configurações e propriedades
> * Usar comandos
> * Exibir seu dispositivo simulado no painel

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa de um aplicativo Azure IoT Central. Se você concluiu o início rápido [Criar um aplicativo Azure IoT Central](quick-deploy-iot-central.md), você pode reutilizar o aplicativo criado no início rápido. Caso contrário, conclua as seguintes etapas para criar um aplicativo Azure IoT Central vazio:

1. Navegue até a página do [Gerenciador de aplicativos](https://aka.ms/iotcentral) do Azure IoT Central.

2. Digite o endereço de email e a senha que você usa para acessar sua assinatura do Azure:

    ![Insira a conta da sua organização](./media/tutorial-define-device-type/sign-in.png)

3. Para começar a criar um novo aplicativo Azure IoT Central, selecione **Novo Aplicativo**:

    ![Página do Gerenciador de aplicativos do Azure IoT Central](./media/tutorial-define-device-type/iotcentralhome.png)

4. Para criar um novo aplicativo Azure IoT Central:
    
   * Escolha **Versão de avaliação**. Você não precisa de uma assinatura do Azure para criar um aplicativo de Versão de avaliação.
    
      Para obter mais informações sobre assinaturas e diretórios, consulte o [criar um início rápido de aplicativo](quick-deploy-iot-central.md).
    
   * Escolha **Aplicativo personalizado**.
    
   * Opcionalmente, você pode escolher um nome amigável do aplicativo, como **Ar-condicionados Contoso**. O Azure IoT Central gera um prefixo de URL exclusivo para você. Você pode alterar esse prefixo de URL para algo mais fácil de lembrar.
    
   * Selecione **Criar**.

     ![Página Criar aplicativo do Azure IoT Central](./media/tutorial-define-device-type/iotcentralcreate.png)

     Para obter mais informações, consulte o [Criar um início rápido de aplicativo](quick-deploy-iot-central.md).

## <a name="create-a-device-template"></a>Crie um modelo de dispositivo

Como um construtor, você pode criar e editar os modelos de dispositivo no seu aplicativo. Quando você cria um modelo de dispositivo, o Azure IoT Central gera um dispositivo simulado a partir do modelo. O dispositivo simulado gera telemetria que permite que você teste o comportamento do seu aplicativo antes de você conectar um dispositivo real.

Para adicionar um novo modelo de dispositivo ao aplicativo, você precisará acessar a página **Modelos de Aplicativo**. Para fazer isso, selecione os **Modelos de Dispositivo** no menu de navegação esquerdo.

![Página Modelos de Dispositivo](./media/tutorial-define-device-type/devicetemplates.png)

## <a name="add-a-device-template"></a>Adicionar um modelo de dispositivo

As etapas a seguir mostram como criar um novo modelo de dispositivo de **Ar-condicionado conectado** para dispositivos que enviam telemetria de temperatura para o seu aplicativo:

1. Na página **Modelos de Dispositivo**, selecione **+ Novo**:

    ![Página Modelos de Dispositivo, Criar Modelo de Dispositivo](./media/tutorial-define-device-type/newtemplate.png)

2. A página mostra os modelos que você pode escolher.

    ![Biblioteca de modelos de dispositivos](./media/tutorial-define-device-type/devicetemplatelibrary.png)

3. Selecione **Personalizado**, insira **Ar Condicionado Conectado** como o nome do modelo de dispositivo e, em seguida, selecione **Criar**. Você também pode carregar uma imagem do seu dispositivo que é visível para os operadores no Device Explorer:

    ![Dispositivo personalizado](./media/tutorial-define-device-type/createcustomdevice.png)

4. No modelo de dispositivo **Ar-condicionado Conectado**, verifique se você está na guia **Medidas**, na qual a telemetria é definida. Cada modelo de dispositivo definido tem guias separadas para você:

   * Especificar as _medidas_, como telemetria, evento e estado, enviadas pelo dispositivo.

   * Definir as _configurações_ usadas para controlar o dispositivo.

   * Definir as _propriedades_ que são os metadados do dispositivo.

   * Definir os _comandos_ a serem executados diretamente no dispositivo.

   * Definir as _regras_ associadas ao dispositivo.

   * Personalizar o _painel_ do dispositivo para os operadores.

     ![Medidas do ar-condicionado](./media/tutorial-define-device-type/airconmeasurements.png)

     > [!NOTE]
     > Para alterar o nome do modelo de dispositivo, selecione o nome do modelo na parte superior da página.

5. Para adicionar a medida de telemetria de temperatura, selecione **+ Nova Medida**. Em seguida, escolha **Telemetria** como o tipo de medida:

    ![Medidas do ar-condicionado conectado](./media/tutorial-define-device-type/airconmeasurementsnew.png)

6. Cada tipo de telemetria que você define para um modelo de dispositivo inclui [opções de configuração](howto-set-up-template.md) como:

   * Opções de exibição.

   * Detalhes da telemetria.

   * Parâmetros de simulação.

     Para configurar sua telemetria de **Temperatura**, use as informações na tabela a seguir:

     | Configuração              | Valor         |
     | -------------------- | -----------   |
     | Nome de exibição         | Temperatura   |
     | Nome do campo           | temperatura   |
     | Unidades                | F             |
     | Min                  | 60            |
     | max                  | 110           |
     | Casas decimais       | 0             |

     Você também pode escolher uma cor para a exibição da telemetria. Para salvar a definição de telemetria, selecione **Salvar**:

     ![Configurar a simulação de temperatura](./media/tutorial-define-device-type/temperaturesimulation.png)

7. Após alguns instantes, a guia **Medidas** mostrará um gráfico da telemetria de temperatura do dispositivo simulado de ar-condicionado conectado. Use os controles para gerenciar a visibilidade, agregação, ou para editar a definição de telemetria:
 
    > [!NOTE]
    > Para telemetria, **Média** é definida como a agregação padrão. 

    ![Exibir a simulação de temperatura](./media/tutorial-define-device-type/viewsimulation.png)

8. Você também pode personalizar o gráfico usando os controles **Linha**, **Empilhado** e **Editar intervalo de tempo**:

    ![Personalizar o gráfico](./media/tutorial-define-device-type/customizechart.png)

## <a name="add-an-event-measurement"></a>Adicionar uma medida de evento

Use eventos para definir dados pontuais enviados pelo dispositivo quando houver um evento, como um erro ou uma falha de componente. O Azure IoT Central pode simular eventos do dispositivo para permitir que você teste o comportamento do aplicativo antes de conectar um dispositivo real. Defina medidas de evento para o modelo de dispositivo na exibição **Medidas**.

1. Para adicionar a medida de evento **Erro do Motor de Ventilador**, selecione **+ Nova Medida**. Em seguida, escolha **Evento** como o tipo de medida:

    ![Medidas do ar-condicionado conectado](./media/tutorial-define-device-type/eventnew.png)

2. Cada tipo de Evento que você define para um modelo de dispositivo inclui [opções de configuração](howto-set-up-template.md) como:

   * Nome de exibição.

   * Nome do campo.

   * Gravidade.

     Para configurar seu evento de **Erro do motor do ventilador**, use as informações na tabela a seguir:

     | Configuração              | Valor             |
     | -------------------- | -----------       |
     | Nome de exibição         | Erro do motor do ventilador   |
     | Nome do campo           | fanmotorerr       |
     | Severity             | Erro             |

     Para salvar a definição de evento, selecione **Salvar**:

     ![Configurar uma medida de evento](./media/tutorial-define-device-type/eventconfiguration.png)

3. Após alguns instantes, a guia **Medidas** mostrará um gráfico dos eventos gerados aleatoriamente com base no dispositivo simulado de ar-condicionado conectado. Use os controles para gerenciar a visibilidade ou para editar a definição de evento:

    ![Exibir evento simulado](./media/tutorial-define-device-type/eventview.png)

1. Para ver detalhes adicionais sobre o evento, selecione o evento no gráfico:

    ![Exibir detalhes do evento](./media/tutorial-define-device-type/eventviewdetail.png)

## <a name="define-a-state-measurement"></a>Definir uma medida de estado

Use o estado para definir e visualizar o estado do dispositivo ou de seu componente ao longo de um período. O Azure IoT Central pode simular o estado do dispositivo para permitir que você teste o comportamento do aplicativo antes de conectar um dispositivo real. Você define as medidas de estado para o seu tipo de dispositivo na exibição **Medidas**.

1. Para adicionar uma medida de estado **Modo Ventilador**, selecione **+ Nova Medida**. Em seguida, escolha **Estado** como o tipo de medida:

    ![Medidas de estado do ar-condicionado conectado](./media/tutorial-define-device-type/statenew.png)

2. Cada tipo de estado definido para um modelo de dispositivo inclui [opções de configuração](howto-set-up-template.md) como:

   * Nome de exibição.

   * Nome do campo.

   * Valores com rótulos de exibição opcionais.

   * Cor de cada valor.

     Para configurar seu estado de **Modo ventilação**, use as informações na tabela a seguir:

     | Configuração              | Valor             |
     | -------------------- | -----------       |
     | Nome de exibição         | Modo ventilação          |
     | Nome do campo           | fanmode           |
     | Valor                | 1                 |
     | Rótulo de exibição        | Operacional         |
     | Valor                | 0                 |
     | Rótulo de exibição        | Parado           |

     Para salvar a definição de medida de estado, selecione **Salvar**:

     ![Configurar medida de Estado](./media/tutorial-define-device-type/stateconfiguration.png)

3. Após alguns instantes, a guia **Medidas** mostrará um gráfico dos estados gerados aleatoriamente com base no dispositivo simulado de ar-condicionado conectado. Use os controles para gerenciar a visibilidade ou para editar a definição de estado:

    ![Exibir simulação de estado](./media/tutorial-define-device-type/stateview.png)

4. Se houver muitos pontos de dados enviados pelo dispositivo durante um curto período, a medida de estado será mostrada com um visual diferente. Selecione o gráfico para ver todos os pontos de dados dentro desse período exibidos em ordem cronológica. Também é possível reduzir o intervalo de tempo para ver as medidas com mais detalhes.

## <a name="settings-properties-and-commands"></a>Configurações, propriedades e comandos

Configurações, propriedades e comandos são valores diferentes definidos em um modelo de dispositivo e associados a cada dispositivo individual:

* Você usa _configurações_ para enviar dados de configuração para um dispositivo a partir de seu aplicativo. Por exemplo, um operador pode usar uma configuração para alterar o intervalo de telemetria do dispositivo de dois segundos para cinco segundos. Quando um operador altera uma configuração, a configuração é marcada como pendente na interface do usuário até que o dispositivo responda com uma confirmação.

* Você usa _propriedades_ para definir metadados associados ao seu dispositivo. Existem duas categorias de propriedades:
    
  * Você usa _propriedades de aplicativo_ para registrar informações sobre seu dispositivo no seu aplicativo. Por exemplo, você pode usar as propriedades do aplicativo para registrar o local de um dispositivo e sua última data de serviço. Essas propriedades são armazenadas no aplicativo e não são sincronizadas com o dispositivo. Um operador pode atribuir valores às propriedades.

  * Você usa _propriedades do dispositivo_ para habilitar um dispositivo para envio de valores de propriedade para o seu aplicativo. Essas propriedades só podem ser alteradas pelo dispositivo. Para um operador, as propriedades do dispositivo são somente leitura. Nesse cenário de um ar-condicionado conectado, o número de série do dispositivo e a versão de firmware são propriedades do dispositivo relatadas pelo dispositivo.
    
    Para obter mais informações, confira [Propriedades](howto-set-up-template.md#properties) no guia de instruções sobre como configurar um modelo de dispositivo.

* Você usa _comandos_ para gerenciar remotamente seu dispositivo a partir de seu aplicativo. Você pode executar comandos diretamente no dispositivo da nuvem para controlar os dispositivos. Por exemplo, um operador pode executar comandos como reinicialização para reinicializar o dispositivo instantaneamente.

## <a name="use-settings"></a>Use as configurações

Você usa *configurações* para permitir que um operador envie dados de configuração para um dispositivo. Nesta seção, você adiciona uma configuração para o seu modelo de dispositivo de **Ar-condicionado conectado** que permite que um operador defina a temperatura de destino do ar-condicionado conectado.

1. Navegue para a guia **Configurações** do modelo de dispositivo **Ar-condicionado Conectado**.

2. Você pode criar configurações de tipos diferentes, como números ou texto. Selecione **Número** para adicionar uma configuração de número ao dispositivo.

3. Para definir sua configuração de **Temperatura definida**, use as informações na tabela a seguir:

    | Campo                | Valor           |
    | -------------------- | -----------     |
    | Nome de exibição         | Temperatura definida |
    | Nome do campo           | setTemperature  |
    | Unidade de medida      | F               |
    | Casas Decimais       | 1               |
    | Valor mínimo        | 20              |
    | Valor máximo        | 200             |
    | Valor inicial        | 80              |
    | DESCRIÇÃO          | Defina a temperatura de destino para o ar-condicionado |

    Em seguida, selecione **Salvar**:

    ![Definir configuração de Temperatura definida](./media/tutorial-define-device-type/configuresetting.png)

    > [!NOTE]
    > Quando o dispositivo confirmar a alteração da configuração, o status da configuração mudará para **sincronizado**.

4. Personalize o layout da guia **Configurações** movendo e redimensionando os blocos de configurações:

    ![Personalizar layout das configurações](./media/tutorial-define-device-type/settingslayout.png)

## <a name="use-properties"></a>Usar propriedades

Você usa *propriedades do aplicativo* para registrar informações sobre seu dispositivo no seu aplicativo. Nesta seção, você adiciona as propriedades do aplicativo para seu modelo de dispositivo **Ar-Condicionado Conectado** para armazenar o local do dispositivo e a última data de serviço. Essas propriedades são editáveis no aplicativo. O dispositivo também relata propriedades, como número de série e versão de firmware, que são somente leitura no aplicativo.

1. Navegue para a guia **Propriedades** do modelo de dispositivo **Ar-condicionado Conectado**.

1. Você pode criar propriedades de dispositivo de tipos diferentes, como números ou texto. Para adicionar uma propriedade local para seu modelo de dispositivo, escolha **Local**. Para configurar sua propriedade de local, use as informações na tabela a seguir:

    | Campo                | Valor                |
    | -------------------- | -------------------- |
    | Nome de exibição         | Local padrão             |
    | Nome do campo           | location             |
    | Valor inicial        | Seattle, WA          |
    | DESCRIÇÃO          | Localização do dispositivo      |

    Deixe os outros campos com os seus respectivos valores padrão.

    ![Configurar as propriedades do dispositivo](./media/tutorial-define-device-type/configureproperties.png)

    Clique em **Salvar**.

1. Para adicionar uma propriedade de última data de serviço ao seu modelo de dispositivo, escolha **Data**.

1. Para configurar a sua última propriedade data de serviço, use as informações na tabela a seguir:

    | Campo                | Valor                   |
    | -------------------- | ----------------------- |
    | Nome de exibição         | Data do Último Serviço       |
    | Nome do campo           | serviceDate             |
    | Valor inicial        | 1/1/2019                |
    | DESCRIÇÃO          | Atendido por último           |

    ![Configurar as propriedades do dispositivo](./media/tutorial-define-device-type/configureproperties2.png)

    Clique em **Salvar**.

1. Personalize o layout da guia **Propriedades** movendo e redimensionando os blocos de propriedade.

1. Para adicionar uma propriedade do dispositivo como versão de firmware para o seu modelo de dispositivo, escolha **Propriedade do dispositivo**.

1. Para configurar sua versão de firmware, use as informações na tabela a seguir:

    | Campo                | Valor                   |
    | -------------------- | ----------------------- |
    | Nome de exibição         | Versão do firmware        |
    | Nome do campo           | firmwareVersion         |
    | Tipo de Dados            | text                    |
    | DESCRIÇÃO          | A versão de firmware de ar-condicionado |

    ![Configurar versão de firmware](./media/tutorial-define-device-type/configureproperties3.png)

    Clique em **Salvar**.

1. Para adicionar uma propriedade do dispositivo como número de série para o seu modelo de dispositivo, escolha **Propriedade do dispositivo**.

1. Para configurar o número de série, use as informações na tabela a seguir:

    | Campo                | Valor                   |
    | -------------------- | ----------------------- |
    | Nome de exibição         | Número de série           |
    | Nome do campo           | serialNumber            |
    | Tipo de Dados            | text                    |
    | DESCRIÇÃO          | O número de série do ar-condicionado  |

    ![Configurar número de série](./media/tutorial-define-device-type/configureproperties4.png)

    Clique em **Salvar**.

    > [!NOTE]
    > A propriedade do dispositivo é enviada do dispositivo para o aplicativo. Os valores de versão de firmware e do número de série serão atualizados quando o dispositivo real se conectar ao IoT Central.

## <a name="use-commands"></a>Usar comandos

Use _comandos_ para habilitar um operador a executar comandos diretamente no dispositivo. Nesta seção, você adicionará um comando ao modelo de dispositivo de **Ar Condicionado Conectado** que permite que um operador ecoe determinada mensagem sobre a exibição do ar condicionado conectado.

1. Navegue para a guia **Comandos** do modelo de dispositivo **Ar-condicionado Conectado** para editar o modelo.

1. Selecione **+ Novo Comando** para adicionar um comando ao dispositivo e começar a configurar o novo comando.

1. Para configurar seu novo comando, use as informações da tabela a seguir:

    | Campo                | Valor           |
    | -------------------- | -----------     |
    | Nome de exibição         | Comando echo    |
    | Nome do campo           | echo            |
    | Tempo limite padrão      | 30              |
    | Exibir tipo         | text            |
    | DESCRIÇÃO          | Comando do dispositivo  |  

    É possível adicionar entradas adicionais ao comando, selecionando **+** para **Campos de Entrada**.

    ![Preparar para adicionar uma configuração](./media/tutorial-define-device-type/commandsecho1.png)

     Clique em **Salvar**.

1. Personalize o layout da guia **Comandos** movendo e redimensionando os blocos de comando.

## <a name="view-your-simulated-device"></a>Exibir seu dispositivo simulado

Agora que você definiu o modelo de dispositivo **Ar-condicionado Conectado**, personalize o **Painel** para incluir as medidas, as configurações e as propriedades definidas. Em seguida, você pode visualizar o painel como um operador:

1. Escolha a guia **Painel** do modelo de dispositivo **Ar-condicionado Conectado**.

1. Selecione **Gráfico de Linhas** para adicionar o componente ao **Painel**.

1. Configure o componente **Gráfico de linhas** usando as informações na tabela a seguir:

    | Configuração      | Valor       |
    | ------------ | ----------- |
    | Title        | Temperatura |
    | Intervalo de tempo   | Últimos 30 minutos |
    | Medidas     | Temperatura (selecione **Visibilidade** ao lado de **Temperatura**) |

    ![Configurações do gráfico de linhas](./media/tutorial-define-device-type/linechartsettings.png)

    Em seguida, selecione **Salvar**.

1. Selecione o componente **Histórico de Eventos** usando as informações da seguinte tabela:

    | Configuração      | Valor       |
    | ------------ | ----------- |
    | Title        | Eventos do Motor do Ventilador |
    | Intervalo de tempo   | Últimos 30 minutos |
    | Medidas     | Erro do Motor do Ventilador (selecione **Visibilidade** ao lado de **Erro do Motor do Ventilador**) |

    ![Configurações do gráfico do evento](./media/tutorial-define-device-type/dashboardeventchartsetting.png)

    Em seguida, selecione **Salvar**.

1. Configure o componente **Histórico de Estado** usando as informações na tabela a seguir:

    | Configuração      | Valor       |
    | ------------ | ----------- |
    | Title        | Modo ventilação |
    | Intervalo de tempo   | Últimos 30 minutos |
    | Medidas | Modo Ventilador (selecione **Visibilidade** ao lado de **Modo Ventilador**) |

    ![Configurações do gráfico de linhas](./media/tutorial-define-device-type/dashboardstatechartsetting.png)

    Em seguida, selecione **Salvar**.

1. Para adicionar as configurações e as propriedades do dispositivo ao painel, escolha **Configurações e Propriedades**. Selecione **Adicionar/Remover** para adicionar as configurações ou propriedades que você gostaria de ver no painel.

1. Configure o componente **Configurações e propriedades** usando as informações na tabela a seguir:

    | Configuração                 | Valor         |
    | ----------------------- | ------------- |
    | Title                   | Propriedades do dispositivo |
    | Configurações e propriedades | Temperatura definida<br/>Número de série<br/>Versão do firmware |

    As configurações e as propriedades definidas anteriormente nas páginas **Configurações e Propriedades** são mostradas em **Colunas Disponíveis**.

    ![Definir as configurações de propriedade de temperatura](./media/tutorial-define-device-type/propertysettings4.png)

    Em seguida, selecione **Salvar**.

1. Agora você poderá ver dados simulados para o Ar-condicionado Conectado no painel. Edite os blocos e o layout do painel:

    ![Exibir painel](./media/tutorial-define-device-type/dashboard.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Criar um novo modelo de dispositivo
> * Adicionar telemetria ao seu dispositivo
> * Exibir telemetria simulada
> * Definir eventos de dispositivo
> * Exibir eventos simulados
> * Definir seu estado
> * Exibir estado simulado
> * Usar configurações e propriedades
> * Usar comandos
> * Exibir seu dispositivo simulado no painel

Agora que você definiu um modelo de dispositivo no aplicativo do Azure IoT Central, estas são as próximas etapas sugeridas:

* [Configurar regras e ações para o seu dispositivo](tutorial-configure-rules.md)
* [Personalizar os modos de exibição do operador](tutorial-customize-operator.md)