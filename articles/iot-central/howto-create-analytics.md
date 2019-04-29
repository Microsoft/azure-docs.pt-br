---
title: Analisar dados do dispositivo em seu aplicativo do Azure IoT Central | Microsoft Docs
description: Analise os dados do dispositivo no aplicativo Central do IoT do Azure.
author: lmasieri
ms.author: lmasieri
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 05915e2059e45c002a9dfa71a538ed6391caa157
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60886432"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>Como usar análise para analisar os dados do dispositivo

*Este artigo aplica-se a operadores, construtores e administradores.*

A Central de IoT do Azure fornece recursos avançados de análise para compreender grandes quantidades de dados de seus dispositivos. Para começar, visite **Análise** no menu de navegação à esquerda.

## <a name="querying-your-data"></a>Consultando seus dados

Você precisará escolher um **conjunto de dispositivos**, adicionar um **filtro** (opcional) e selecionar um **período de tempo** para começar. Quando você terminar, selecione **Mostrar resultados** para começar a visualizar seus dados.

* **Conjuntos de dispositivos:** Um [conjunto de dispositivos](howto-use-device-sets.md) é um grupo de dispositivos definido pelo usuário. Por exemplo, todos os refrigeradores em Oakland ou todas as turbinas eólicas rev 2.0.

* **Filtros:** Opcionalmente, é possível adicionar filtros à pesquisa para aprimorar os dados. Você pode adicionar até 10 filtros por vez. Por exemplo, em todos os refrigeradores em Oakland, encontre aqueles que apresentaram temperatura acima de 60 graus.
* **Período de tempo:** Por padrão, recuperaremos os dados dos últimos 10 minutos. Você pode alterar esse valor para um dos intervalos de tempo predefinidos ou selecionar um período de tempo personalizado.

  ![Consulta de análise](media/howto-create-analytics/analytics-query.png)

## <a name="visualizing-your-data"></a>Visualizando seus dados

Depois de consultar seus dados, você poderá começar a visualizá-los. Você pode mostrar/ocultar medidas, alterar a maneira como os dados são agregados e dividir os dados ainda mais pelas diferentes propriedades do dispositivo.  

* **Dividir por:** Dividir os dados por propriedades de dispositivo permite fazer busca mais detalhada nos seus dados. Por exemplo, você pode dividir os resultados por ID ou local do dispositivo.

* **Medidas:** Você pode optar por mostrar/ocultar até 10 itens de telemetria diferentes sendo informados por seus dispositivos por vez. As medidas são itens como temperatura e umidade.

* **Agregação:** Por padrão, agregamos dados pela média, mas é possível optar por alterar a agregação de dados para algo que atenda às suas necessidades.

   ![Visualização de análise - dividir por](media/howto-create-analytics/analytics-splitby.png)

## <a name="interacting-with-your-data"></a>Interagindo com seus dados

Você tem várias maneiras de alterar os resultados da consulta para atender às suas necessidades de visualização. Alternar entre um modo de exibição de gráfico e uma exibição de grade, ampliar e reduzir, atualize seu conjunto de dados e alterar como as linhas são mostradas.

* **Mostrar grade:** Os resultados estão disponíveis em um formato de tabela, permitindo que você exibir o valor específico para cada ponto de dados. Essa exibição também atende aos padrões de acessibilidade.
* **Mostrar gráfico:** Os resultados são exibidos em um formato de linha para ajudar a identificá-lo para cima ou para baixo tendências e anomalias.

  ![Mostrando a exibição em grade da sua análise](media/howto-create-analytics/analytics-showgrid.png)

Zoom permite que você à página inicial em seus dados. Se você encontrar um período de tempo em que gostaria de se concentrar no conjunto de resultados, use o cursor para capturar a área que deseja ampliar e use os controles disponíveis para executar uma das seguintes ações:

* **Ampliar:** Depois de selecionar um período de tempo, ampliar está habilitado e permite que você amplie seus dados.
* **Reduzir:** Esse controle permite que você reduza um nível em relação à última ampliação. Por exemplo, se você tiver o zoom aos seus dados três vezes, leva de redução você faça uma etapa por vez.
* **Redefinir Zoom:** Após realizar vários níveis de zoom, você poderá usar o controle redefinir zoom para retornar ao conjunto de resultados original.

  ![Executando zoom em seus dados](media/howto-create-analytics/analytics-zoom.png)

Você pode alterar o estilo da linha para atender às suas necessidades. Você tem quatro opções:

* **Linha:** Uma linha reta entre cada um dos pontos de dados.
* **Suave:** Uma linha curva entre cada ponto.
* **Etapa:** Linha entre cada ponto no gráfico é uma etapa.
* **Dispersão:** Todos os pontos são plotados no gráfico sem linhas conectando-os.

  ![Diferentes tipos de linha disponíveis na análise](media/howto-create-analytics/analytics-linetypes.png)

Por fim, você pode organizar seus dados entre o eixo y, escolhendo um dos três modos:

* **Empilhado:** Um gráfico para cada medida é empilhado e cada um dos gráficos tem seu próprio eixo Y. Os gráficos empilhados são úteis quando você tem várias medidas selecionadas e deseja ter uma exibição distinta dessas medidas.
* **Fora da pilha:** Um gráfico para cada medida é plotado em relação a um eixo Y, mas os valores para o eixo Y são alterados com base na medida realçada. Gráficos não empilhados são úteis quando você deseja sobrepor várias medidas e deseja ver padrões nessas medidas para o mesmo intervalo de tempo.
* **Eixo compartilhado Y:** Todos os gráficos compartilham o mesmo eixo Y e os valores para o eixo não são alterados. Os gráficos compartilhados do eixo Y são úteis quando você deseja examinar uma única medida ao fatiar os dados com a opção dividir por.

  ![Organizar dados ao longo do eixo y com diferentes modos de visualização](media/howto-create-analytics/analytics-yaxis.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a criar análises personalizadas para o aplicativo Azure IoT Central, a próxima etapa sugerida é apresentada:

> [!div class="nextstepaction"]
> [Preparar e conectar um aplicativo Node.js](howto-connect-nodejs.md)