---
title: Analisar dados do dispositivo em seu aplicativo de IoT Central do Azure | Microsoft Docs
description: Analise os dados do dispositivo em seu aplicativo IoT Central do Azure.
author: ankitscribbles
ms.author: ankitgup
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 8f329b6377915a3df6e3f3e212cbd8d41f9ed80d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90017653"
---
# <a name="how-to-use-analytics-to-analyze-device-data"></a>Como usar a análise para analisar dados do dispositivo

*Este artigo aplica-se a operadores, construtores e administradores.*

O Azure IoT Central fornece recursos de análise avançados para analisar tendências históricas e correlacionar vários telemetrias de seus dispositivos. Para começar, visite **análise** no painel esquerdo.

## <a name="understanding-the-analytics-ui"></a>Noções básicas sobre a interface do usuário do Analytics
A interface do usuário do Analytics é composta por três componentes principais:
- **Painel de configuração de dados:** No painel de configuração, comece selecionando o grupo de dispositivos para o qual você deseja analisar os dados. Em seguida, selecione a telemetria que você deseja analisar e selecione o método de agregação para cada telemetria. A **divisão por** controle ajuda a agrupar os dados usando as propriedades do dispositivo como dimensões.

- **Controle de tempo:** O controle de tempo é usado para selecionar a duração para a qual você deseja analisar os dados. Você pode arrastar uma das extremidades do controle deslizante de tempo para selecionar o período de tempo. O controle de tempo também tem um controle deslizante de **tamanho de intervalo** que controla o Bucket ou o tamanho do intervalo usado para agregar os dados. 

- **Controle de gráfico:** O controle de gráfico visualiza os dados como um gráfico de linhas. Você pode alternar a visibilidade de linhas específicas interagindo com a legenda do gráfico. 


  ![Visão geral da interface do usuário do Analytics](media/howto-create-analytics/analyticsui.png)


## <a name="querying-your-data"></a>Consultando seus dados

Você precisará começar escolhendo um grupo de **dispositivos** e a telemetria que deseja analisar. Depois de terminar, selecione **analisar** para começar a visualizar seus dados.

- **Grupo de dispositivos:** Um [grupo de dispositivos](tutorial-use-device-groups.md) é um grupo definido pelo usuário de seus dispositivos. Por exemplo, todos os refrigeradores em Oakland ou todas as versões de vento 2,0.

- **Telemetria:** Selecione a telemetria que você deseja analisar e explorar. Você pode selecionar vários telemetrias para analisar juntos. O método de agregação padrão é definido como média para numérico e contagem para o tipo de dados de cadeia de caracteres, respectivamente. Os métodos de agregação com suporte para tipos de dados numéricos são média, máximo, mínimo, contagem e soma.  Os métodos de agregação com suporte para o tipo de dados String são Count.

- **Dividir por:** O controle ' dividir por ' ajuda a agrupar os dados usando as propriedades do dispositivo como dimensões. Os valores das propriedades de dispositivo e de nuvem são Unidos junto com a telemetria como e quando ele é enviado pelo dispositivo. Se a propriedade de nuvem ou dispositivo tiver sido atualizada, você verá a telemetria agrupada por valores diferentes no gráfico.

    > [!TIP]
    > Para exibir dados de cada dispositivo separadamente, selecione ID do dispositivo no controle ' dividir por '.

## <a name="interacting-with-your-data"></a>Interagindo com seus dados

Depois de consultar seus dados, você pode começar a Visualizar no gráfico de linhas. Você pode mostrar/ocultar telemetria, alterar a duração da hora, exibir a telemetria em uma grade de dados.

- **Painel do editor de tempo:** Por padrão, recuperaremos dados do passado um dia. Você pode arrastar uma das extremidades do controle deslizante de tempo para alterar a duração do tempo. Você também pode usar o controle Calendar para selecionar um dos buckets de tempo predefinidos ou selecionar um intervalo de tempo personalizado. O controle de tempo também tem um controle deslizante de **tamanho de intervalo** que controla o Bucket ou o tamanho do intervalo usado para agregar os dados.

    ![Editor de hora](media/howto-create-analytics/timeeditorpanel.png)

    - **Ferramenta de controle deslizante de intervalo de datas internas**: Use os dois controles de ponto de extremidade arrastando-os sobre o período de tempo desejado. Esse intervalo de datas interna é restrito pelo controle deslizante de intervalo de datas externa.
    
   
    - **Controle deslizante de intervalo de datas externas**: Use os controles de ponto de extremidade para selecionar o intervalo de datas externas, que estará disponível para o controle de intervalo de datas interna.

    - **Botões aumentar e diminuir intervalo de datas**: aumente ou diminua seu período selecionando um dos botões para o intervalo desejado.

    - **Controle deslizante de tamanho do intervalo**: Use-o para ampliar e reduzir intervalos no mesmo período de tempo. Essa ação fornece controle mais preciso dos movimentos entre fatias de tempo grandes. Você pode usá-lo para ver exibições granulares e de alta resolução de seus dados, mesmo abaixo de milissegundos. O ponto de partida padrão do controle deslizante é definido como a exibição ideal dos dados de sua seleção, que equilibra a resolução, a velocidade da consulta e a granularidade.
    
    - **Seletor de intervalo de datas**: com esse controle da Web, você pode selecionar facilmente os intervalos de data e hora desejados. Você também pode usar o controle para alternar entre fusos horários diferentes. Depois de fazer as alterações a serem aplicadas ao seu espaço de trabalho atual, selecione salvar.

    > [!TIP]
    > O tamanho do intervalo é determinado dinamicamente com base no período de tempo selecionado. Os intervalos de tempo menores habilitarão a agregação dos dados em um intervalo muito granular de até alguns segundos.


- **Legenda do gráfico:** Legenda do gráfico mostra a telemetria selecionada no gráfico. Você pode focalizar cada item na legenda para colocá-lo em foco no gráfico. Ao usar ' dividir por ', a telemetria é agrupada pelos respectivos valores da dimensão selecionada. Você pode alternar a visibilidade de cada telemetria específica ou de todo o grupo clicando no nome do grupo.  


- **Controle de formato do eixo y:** o modo do eixo y percorre as opções de exibição do eixo y disponíveis. Esse controle está disponível somente quando diferentes telemetrias estão sendo visualizados. Você pode definir o eixo y escolhendo um dos três modos:

    - **Empilhado:** Um grafo para cada telemetria é empilhado e cada um dos grafos tem seu próprio eixo y. Esse modo é definido como padrão.
    - **Compartilhado:** Um grafo para cada telemetria é plotado em relação ao mesmo eixo y.
    - **Sobreposição:** Use-o para empilhar várias linhas no mesmo eixo y, com os dados do eixo y mudando com base na linha selecionada.

  ![Organizar dados ao longo do eixo y com diferentes modos de visualização](media/howto-create-analytics/yaxiscontrol.png)

- **Controle de zoom:** O zoom permite que você faça uma busca detalhada de seus dados. Se você encontrar um período de tempo em que deseja se concentrar em seu conjunto de resultados, use o ponteiro do mouse para pegar a área e arraste-a para o ponto de extremidade de sua escolha. Em seguida, clique com o botão direito do mouse na área selecionada e clique em zoom.

  ![Ampliar os dados](media/howto-create-analytics/zoom.png)

Sob as reticências, há mais controles de gráfico para interagir com os dados:

- **Exibir grade:** Os resultados estão disponíveis em um formato de tabela, permitindo que você exiba o valor específico para cada ponto de dados.

- **Baixar como CSV:** Os resultados estão disponíveis para exportação como um arquivo CSV (valores separados por vírgula). O arquivo CSV contém dados para cada dispositivo. Os resultados são exportados usando o intervalo e o período de tempo especificados. 

- **Descartar um marcador:** O controle ' soltar marcador ' fornece uma maneira de ancorar determinados pontos de dados no gráfico. É útil quando você está tentando comparar dados para várias linhas entre períodos de tempo diferentes.

  ![Mostrando a exibição em grade da sua análise](media/howto-create-analytics/additionalchartcontrols.png)
