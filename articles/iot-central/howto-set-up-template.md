---
title: Configurar um modelo de dispositivo em um aplicativo Azure IoT Central | Microsoft Docs
description: Saiba como configurar um modelo de dispositivo com medidas, configurações, propriedades, regras e um painel.
author: viv-liu
ms.author: viviali
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: d27fd9460685c08a2b13936415935f5aaf893797
ms.sourcegitcommit: dda9fc615db84e6849963b20e1dce74c9fe51821
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67622403"
---
# <a name="set-up-a-device-template"></a>Configurar um modelo de dispositivo

Um modelo de dispositivo é um blueprint que define as características e os comportamentos de um tipo de dispositivo que conecta a um aplicativo do Azure IoT Central.

Por exemplo, um construtor pode criar um modelo de dispositivo para um ventilador conectado que tem as seguintes características:

- Medição de telemetria de temperatura
- Medição de local
- Medição de evento de erro de motor da ventoinha
- Medição do estado operacional da ventoinha
- Ajuste de velocidade da ventoinha
- Regras que enviam alertas
- Painel que oferece uma visão geral do dispositivo

A partir desse modelo de dispositivo, um operador pode criar e conectar dispositivos de ventoinhas reais com nomes como **ventoinha-1** e **ventoinha-2**. Todos esses fãs possuem medidas, configurações, propriedades, regras e um painel que os usuários do seu aplicativo podem monitorar e gerenciar.

> [!NOTE]
> Somente construtores e administradores podem criar, editar e excluir modelos de dispositivo. Qualquer usuário pode criar dispositivos na página **Device Explorer** a partir de modelos de dispositivos existentes.

## <a name="create-a-device-template"></a>Crie um modelo de dispositivo

1. Navegue para a página **Modelos de Dispositivo**.

2. Para criar um modelo, comece selecionando **+ novo**.

3. Para começar rapidamente, escolha entre os modelos predefinidos existentes. Caso contrário, selecione **personalizado**, insira um nome e clique em **criar** para criar seu próprio modelo do zero.

   ![Biblioteca de modelos de dispositivo](./media/howto-set-up-template/newtemplate.png)

4. Quando você cria um modelo personalizado, você vê o **detalhes do dispositivo** página para o novo modelo de dispositivo. O IoT Central cria automaticamente um dispositivo simulado quando você cria um modelo de dispositivo. Um dispositivo simulado permite testar o comportamento do aplicativo antes de você conectar um dispositivo real.

As seções a seguir descrevem cada uma das guias da página **Modelo de Dispositivo**.

## <a name="measurements"></a>Medidas

Medidas são os dados que vêm do seu dispositivo. É possível adicionar várias medidas ao modelo de dispositivo para corresponder aos recursos do dispositivo.

- **Telemetria** são os pontos de dados numéricos que o seu dispositivo coleta ao longo do tempo. Eles são representados como um fluxo contínuo. Um exemplo é a temperatura.
- **As medições do evento** são dados point-in-time que representam algo significativo no dispositivo. Um nível de severidade representa a importância de um evento. Um exemplo é um erro no motor do ventilador.
- Medidas de **Estado** representam o estado do dispositivo ou os respectivos componentes durante um período de tempo. Por exemplo, um modo de ventilador pode ser definido como tendo **Operating** e **Stopped** como os dois estados possíveis.
- **Local** medidas são as coordenadas de longitude e latitude do dispositivo em um período de tempo em. Por exemplo, um ventilador pode ser movido de um local para outro.

### <a name="create-a-telemetry-measurement"></a>Criar uma medição de telemetria

Para adicionar uma nova medida de telemetria, selecione **+ nova medição**, escolha **telemetria** como a medida de tipo e insira os detalhes no formulário.

> [!NOTE]
> Os nomes de campo no modelo de dispositivo precisarão corresponder aos nomes de propriedade no código do dispositivo correspondente para que a medida de telemetria seja exibida no aplicativo quando um dispositivo real estiver conectado. Faça o mesmo ao definir configurações, propriedades do dispositivo e comandos enquanto você continua a definir o modelo de dispositivo nas seções a seguir.
. png, por exemplo, você pode adicionar uma nova medida de telemetria de temperatura:

| Nome de exibição        | Nome do campo    |  Unidades    | Min   |max|
| --------------------| ------------- |-----------|-------|---|
| Temperatura         | temp          |  degC     |  0    |100|

![Formulário "Criar Telemetria" com detalhes para medição de temperatura](./media/howto-set-up-template/measurementsform.png)

Depois de selecionar **Salvar**, a medição **Temperatura** aparece na lista de medições. Em alguns instantes, você verá a visualização dos dados de temperatura do dispositivo simulado.

Ao exibir a telemetria, você pode escolher entre as seguintes opções de agregação: Média, mínimo, máximo, soma e contagem. **Médio** é selecionado como a agregação padrão no gráfico.

> [!NOTE]
> O tipo de dados da medição de telemetria é um número de ponto flutuante.

### <a name="create-an-event-measurement"></a>Crie uma medição de evento

Para adicionar uma nova medida de evento, selecione **+ nova medição** e selecione **evento** como o tipo de medida. Insira os detalhes no formulário **Criar Evento**.

Forneça os detalhes **Nome para exibição**, **Nome do campo** e **Gravidade** para o evento. Escolha um entre os três níveis de gravidade disponíveis: **Erro**, **Aviso** e **Informações**.

Por exemplo, você pode adicionar um novo evento **Fan Motor Error**.

| Nome de exibição        | Nome do campo    |  Gravidade padrão |
| --------------------| ------------- |-----------|
| Erro do motor do ventilador     | fanmotorerror |  Erro    |

![Formulário "Criar Evento" com detalhes para um evento de motor de ventilador](./media/howto-set-up-template/eventmeasurementsform.png)

Depois de selecionar **Gravar**, a medição **Erro do Motor do Ventilador** aparece na lista de medições. Em alguns instantes, você verá a visualização dos dados de evento do dispositivo simulado.

Para exibir mais detalhes sobre um evento, selecione o ícone de evento no gráfico:

![Detalhes para o evento "Fan Motor Error"](./media/howto-set-up-template/eventmeasurementsdetail.png)

> [!NOTE]
> O tipo de dados da medida de evento é cadeia de caracteres.

### <a name="create-a-state-measurement"></a>Crie uma medida de estado

Para adicionar uma nova avaliação de estado, selecione o **+ nova medição** botão e selecione **estado** como o tipo de medida. Insira os detalhes no formulário **Criar Estado**.

Forneça os detalhes para **Nome de exibição**, **Nome do campo** e **Valores** do estado. Cada valor também pode ter um nome de exibição que será usado quando o valor aparecer em gráficos e tabelas.

Por exemplo, você pode adicionar um novo **estado do Modo de fãs** que tenha dois valores possíveis que o dispositivo pode enviar, **Em funcionamento** e **Parado**.

| Nome de exibição | Nome do campo    |  Valor 1   | Nome de exibição | Valor 2    |Nome de exibição  | 
| -------------| ------------- |----------- | -------------| -----------| -------------|
| Modo ventilação     | fanmode       |  1         | Operacional    |     0      | Parado      |

![Formulário "Editar estado" com detalhes para o modo de ventilador](./media/howto-set-up-template/statemeasurementsform.png)

Depois de selecionar **Gravar**, a **medição do estado do Fan Mode** aparece na lista de medições. Em alguns instantes, você verá a visualização dos dados de estado do dispositivo simulado.

Se o dispositivo enviar muitos pontos de dados durante um curto período, a medida de estado será exibida com um visual diferente. Selecione o gráfico para exibir todos os pontos de dados dentro desse período de tempo em ordem cronológica. Também é possível reduzir o intervalo de tempo para ver a medida plotada no gráfico.

> [!NOTE]
> O tipo de dados da medida de estado é cadeia de caracteres.

### <a name="create-a-location-measurement"></a>Criar uma medida de local

Para adicionar uma nova medida local, selecione **+ nova medição**, escolha **local** como a medida de tipo e insira os detalhes o **criar medida** formulário.

Por exemplo, você pode adicionar uma nova medida de telemetria local:

| Nome de exibição        | Nome do campo    |
| --------------------| ------------- |
| Localização do ativo      |  assetloc     |

![Formulário de "Criar local" com detalhes de medição de local](./media/howto-set-up-template/locationmeasurementsform.png)

Depois de selecionar **salve**, o **local** medida aparece na lista de medidas. Em pouco tempo, você deve ver a visualização dos dados de localização do dispositivo simulado.

Ao exibir o local, você pode escolher entre as seguintes opções: local mais recente e o histórico de localização. **Histórico de localização** só é aplicada ao longo do intervalo de tempo selecionado.

O tipo de dados da medida local é um objeto que contém uma altitude opcional, latitude e longitude. O trecho a seguir mostra a estrutura do JavaScript:

```javascript
assetloc: {
  lon: floating point number,
  lat: floating point number,
  alt?: floating point number
}
```

## <a name="settings"></a>Configurações

As configurações controlam um dispositivo. Elas permitem que os operadores forneçam entradas ao dispositivo. É possível adicionar várias configurações ao modelo de dispositivo que aparecem como blocos na guia **Configurações** para os operadores usarem. Você pode adicionar vários tipos de configurações: texto, número, data, alternância e rótulo de seção.

As configurações podem estar em um dos três estados. O dispositivo informa esses estados.

- **Sincronizado**: o dispositivo foi alterado para refletir o valor da configuração.

- **Pendente**: o dispositivo está sendo alterado para o valor de configuração.

- **Erro**: o dispositivo retornou um erro.

Por exemplo, você pode adicionar uma nova configuração de velocidade do ventilador, selecionando **as configurações** e inserindo na nova **número** configuração:

| Nome de exibição  | Nome do campo    |  Unidades  | Decimais |Initial|
| --------------| ------------- |---------| ---------|---- |
| Velocidade da ventoinha     | fanSpeed      | RPM     | 2        | 0   |

![Formulário "Configurar Número" com detalhes para configurações de velocidade](./media/howto-set-up-template/settingsform.png)

Depois que você selecionar **Salvar**, a configuração **Velocidade do Ventilador** será exibida como um bloco. Um operador pode usar a configuração na página **Device Explorer** para alterar a velocidade do ventilador do dispositivo.

## <a name="properties"></a>Propriedades

As propriedades são metadados que está associada com o dispositivo, como um local fixo de dispositivo e o número de série. Adicione várias propriedades ao modelo de dispositivo que são exibidas como blocos na guia **Propriedades**. Uma propriedade tem um tipo, como número, texto, data, ativar/desativar, propriedade do dispositivo, rótulo ou um local fixo. Um operador especifica os valores para propriedades quando eles criam um dispositivo, e eles podem editar esses valores a qualquer momento. As propriedades de dispositivo são somente leitura e são enviadas do dispositivo para o aplicativo. Um operador não é possível alterar as propriedades do dispositivo. Quando um dispositivo real se conecta, as atualizações de bloco de propriedade do dispositivo no aplicativo.

Existem duas categorias de propriedades:

- _Propriedades de dispositivo_ que o dispositivo relata ao aplicativo do IoT Central. As propriedades de dispositivo são valores somente leitura relatados pelo dispositivo e são atualizadas no aplicativo quando um dispositivo real é conectado.
- _Propriedades do aplicativo_ que são armazenadas no aplicativo e que podem ser editadas pelo operador. Propriedades do aplicativo são armazenadas apenas no aplicativo e nunca são vistas por um dispositivo.

Por exemplo, adicione a última data de manutenção do dispositivo como uma nova propriedade **Data** (uma propriedade do aplicativo) à guia **Propriedades**:

| Nome de exibição  | Nome do campo | Valor inicial   |
| --------------| -----------|-----------------|
| Atendido por último      | lastServiced        | 29/01/2019     |

![Formulário "Configurar a Última Manutenção" na guia "Propriedades"](./media/howto-set-up-template/propertiesform.png)

Depois que você selecionar **Salvar**, a última data de manutenção do dispositivo será exibida como um bloco.

Depois de criar o bloco, você poderá alterar o valor da propriedade do aplicativo no **Device Explorer**.

### <a name="create-a-location-property"></a>Criar uma propriedade local

Você pode fornecer contexto geográfico a seus dados locais no Azure IoT Central e mapear qualquer latitude e longitude coordenadas ou um endereço de rua. O Azure Maps habilita esse recurso na IoT Central.

Você pode adicionar dois tipos de propriedades de localização:

- **Localização como uma propriedade do aplicativo**, que é armazenada no aplicativo. Propriedades do aplicativo são armazenadas apenas no aplicativo e nunca são vistas por um dispositivo.
- **Localização como uma propriedade de dispositivo**, relatada pelo dispositivo ao aplicativo. Esse tipo de propriedade é melhor usado para um local estático.

> [!NOTE]
> Local como uma propriedade não registrará um histórico. Se o histórico for desejado, use uma medida de local.

#### <a name="add-location-as-an-application-property"></a>Adicionar local como uma propriedade do aplicativo

Você pode criar uma propriedade de local como uma propriedade de aplicativo usando o Azure Maps no seu aplicativo IoT Central. Por exemplo, adicione o endereço de instalação do dispositivo:

1. Navegue para a guia **Propriedades**.

2. Na biblioteca, selecione **Local**.

3. Configure **Nome de exibição**, **Nome do campo** e (opcionalmente) **Valor inicial** para o local.

    | Nome de exibição  | Nome do campo | Valor inicial |
    | --------------| -----------|---------|
    | Endereço de instalação | installAddress | Microsoft, 1 Microsoft Way, Redmond, WA 98052   |

   ![Formulário "Configurar localização" com detalhes para localização](./media/howto-set-up-template/locationcloudproperty2.png)

   Há dois formatos com suporte para adicionar um local:
   - **Local como um endereço**
   - **Local como coordenadas**

4. Clique em **Salvar**. Um operador pode atualizar o valor de localização no **Device Explorer**.

#### <a name="add-location-as-a-device-property"></a>Adicionar local como uma propriedade de dispositivo

Você pode criar uma propriedade de localização como uma propriedade de dispositivo que o dispositivo reporta. Por exemplo, se você quiser rastrear a localização do dispositivo:

1. Navegue para a guia **Propriedades**.

2. Selecione **Propriedade do Dispositivo** na biblioteca.

3. Configure o nome de exibição e o nome de campo e selecione **Localização** como o tipo de dados:

    | Nome de exibição  | Nome do campo | Tipo de dados |
    | --------------| -----------|-----------|
    | Localização do dispositivo | deviceLocation | location  |

   > [!NOTE]
   > Os nomes de campo devem corresponder aos nomes de propriedade no código do dispositivo correspondente

   ![Formulário "Configurar propriedades do dispositivo" com detalhes para localização](./media/howto-set-up-template/locationdeviceproperty2.png)

Depois que o dispositivo real estiver conectado, o local adicionado como uma propriedade de dispositivo é atualizada com o valor enviado pelo dispositivo. Depois de configurar sua propriedade de local, você pode [adicionar um mapa para visualizar o local no painel do dispositivo](#add-a-location-in-the-dashboard).

## <a name="commands"></a>Comandos

Os comandos são usados para gerenciar remotamente um dispositivo. Eles permitem que os operadores executem comandos no dispositivo. Você pode adicionar vários comandos ao seu modelo de dispositivo que aparecem como blocos na guia **Comandos** para os operadores usarem. Como construtor do dispositivo, você tem a flexibilidade de definir comandos de acordo com seus requisitos.

Como um comando é diferente de um cenário?

* **Configuração**: Uma configuração que você deseja aplicar a um dispositivo. Você deseja que o dispositivo persista essa configuração até você alterá-la. Por exemplo, você deseja definir a temperatura do seu freezer e desejar essa configuração mesmo quando o freezer reiniciar.

* **Comando**: use comandos para executar de forma instantânea um comando no dispositivo remotamente por meio do IoT Central. Se um dispositivo não estiver conectado, o comando atingirá o tempo limite e falhará. Por exemplo, você deseja reiniciar um dispositivo.

Por exemplo, você pode adicionar um novo **Echo** comando selecionando o **comandos** guia, em seguida, selecionando **+ novo comando**e inserindo os detalhes do novo comando:

| Nome de exibição  | Nome do campo | Tempo limite padrão | Tipo de dados |
| --------------| -----------|---------------- | --------- |
| Comando echo  | echo       |  30             | text      |

![Formulário "Configurar Comando" com detalhes para o echo](./media/howto-set-up-template/commandsecho1.png)

Depois que você selecionar **Salvar**, o comando **Eco** será exibido como um bloco e estará pronto para ser usado no **Device Explorer** quando o dispositivo real for conectado. Os nomes de campo de seu comando devem corresponder aos nomes de propriedade no código do dispositivo correspondente na ordem de comandos para serem executados com êxito.

## <a name="rules"></a>Regras

As regras permitem que os operadores monitorem os dispositivos quase em tempo real. As regras invocam automaticamente ações como o envio de um email quando a regra é acionada. Um tipo de regra está disponível hoje:

- **Regra de telemetria**, que é acionada quando a telemetria do dispositivo selecionado ultrapassa um limite especificado. [Saiba mais sobre as regras de telemetria](howto-create-telemetry-rules.md).

## <a name="dashboard"></a>painel

O painel é onde um operador vai para ver informações sobre um dispositivo. Como um construtor, você pode adicionar blocos a esta página para ajudar a entender como o dispositivo está se comportando de operadores. Você pode adicionar muitos tipos de blocos do painel, como imagem, gráfico de linhas, gráfico de barras, KPI (indicador chave de desempenho), configurações, propriedades e rótulo.

Por exemplo, adicione um bloco **Configurações e Propriedades** para mostrar uma seleção dos valores atuais de configurações e propriedades selecionando a guia **Painel** e o bloco na Biblioteca:

![Formulário "Configurar detalhes do dispositivo" com detalhes para configurações e propriedades](./media/howto-set-up-template/dashboardsettingsandpropertiesform1.png)

Agora, quando um operador exibir o painel no **Device Explorer**, ele poderá ver o bloco.

### <a name="add-a-location-in-the-dashboard"></a>Adicionar um local no painel de controle

Se você tiver configurado uma medida de local, você pode visualizar o local com um mapa no painel do dispositivo.

1. Navegue para a guia **Painel**.

1. No painel do dispositivo, selecione **Mapa** da biblioteca.

1. Dê um título ao mapa. O exemplo a seguir tem um título **local atual do dispositivo**. Em seguida, escolha a medida de local que você configurou anteriormente na **medidas** guia. No exemplo a seguir, o **localização do ativo** medida está selecionada:

   ![Formulário "Configurar Mapa" com detalhes para título e propriedades](./media/howto-set-up-template/locationcloudproperty5map.png)

1. Clique em **Salvar**. O bloco do mapa agora exibe o local selecionado.

Você pode redimensionar o bloco de mapa. Quando um operador de exibições no painel a **Device Explorer**, o painel todos os blocos que você configurou, incluindo um mapa do local são visíveis.

Para saber mais sobre como usar blocos no Azure IoT Central, consulte [usar blocos de dashboard](howto-use-tiles.md).

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a configurar um modelo de dispositivo no aplicativo Central do IoT do Azure, é possível:

> [!div class="nextstepaction"]
> [Criar uma nova versão do modelo de dispositivo](howto-version-device-template.md)
> [conectar um dispositivo MXChip IoT DevKit ao seu aplicativo do Azure IoT Central](howto-connect-devkit.md)
> [se conectar a um aplicativo cliente genérico para o Azure Aplicativo de Central da IoT (Node. js)](howto-connect-nodejs.md)