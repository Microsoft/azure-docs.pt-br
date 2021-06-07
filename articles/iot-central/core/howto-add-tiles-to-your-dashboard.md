---
title: Configurar para o painel de IoT Central do Azure | Microsoft Docs
description: Como um construtor, saiba como configurar o painel de aplicativo padrão do Azure IoT Central com blocos.
author: TheJasonAndrew
ms.author: v-anjaso
ms.date: 12/19/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: 73c78f514f974b97eb178177f12dc4ceff7ede63
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97797002"
---
# <a name="configure-the-application-dashboard"></a>Configurar o painel de aplicativo

O **painel** é a primeira página que você vê quando se conecta a um aplicativo IOT central. Se você criar seu aplicativo de um dos [modelos de aplicativo](./concepts-app-templates.md)focados no setor, seu aplicativo terá um painel predefinido para iniciar. Se você criar seu aplicativo a partir de um [modelo de aplicativo](./concepts-app-templates.md)personalizado, seu painel mostrará algumas dicas para começar.

> [!TIP]
> Os usuários podem [criar vários painéis](howto-create-personal-dashboards.md) além do painel de aplicativo padrão. Esses painéis podem ser pessoais somente para o usuário ou compartilhados entre todos os usuários do aplicativo.  

## <a name="add-tiles"></a>Adicionar blocos

A captura de tela a seguir mostra o painel em um aplicativo criado a partir do modelo de **aplicativo personalizado** . Para personalizar o painel atual, selecione **Editar**, para adicionar um painel pessoal ou compartilhado personalizado, selecione **novo**:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png" alt-text="Painel para aplicativos com base no modelo de aplicativo personalizado":::

Depois de selecionar **Editar** ou **novo**, o painel estará no modo de *edição* . Você pode usar as ferramentas no painel **Editar painel** para adicionar blocos ao painel e personalizar e remover blocos no próprio Dashboard. Por exemplo, para adicionar um bloco de **telemetria** para mostrar a temperatura atual relatada por um ou mais dispositivos:

1. Selecione um **grupo de dispositivos** e, em seguida, escolha seus dispositivos no menu suspenso **dispositivos** para mostrar no bloco. Agora você verá a telemetria, as propriedades e os comandos disponíveis dos dispositivos.

1. Se necessário, use a lista suspensa para selecionar um valor de telemetria para mostrar no bloco. Você pode adicionar mais itens ao bloco outro selecionando **+ telemetria**, **+ Propriedade** ou **+ propriedade de nuvem**.

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/device-details.png" alt-text="Adicionar um bloco de telemetria de temperatura ao painel":::

Quando você tiver selecionado todos os valores para mostrar no bloco, clique em **Adicionar bloco.** O bloco agora aparece no painel em que você pode alterar a visualização, redimensioná-la, movê-la e configurá-la.

Quando terminar de adicionar e personalizar blocos no painel, selecione **salvar** para salvar as alterações no painel, o que o levará do modo de edição.

## <a name="customize-tiles"></a>Personalizar blocos

Para editar um bloco, você deve estar no modo de edição.  As opções de personalização disponíveis dependem do [tipo de bloco](#tile-types):

* O ícone de régua em um bloco permite que você altere a visualização. As visualizações incluem gráficos de linhas, gráficos de barras, gráficos de pizza, últimos valores conhecidos, indicadores chave de desempenho (ou KPIs), calor e mapas.

* O ícone quadrado permite redimensionar o bloco.

* O ícone de engrenagem permite que você configure a visualização. Por exemplo, para uma visualização de gráfico de linhas, você pode optar por mostrar a legenda e os eixos e escolher o intervalo de tempo para plotar.


## <a name="tile-types"></a>Tipos de bloco

A tabela a seguir descreve os diferentes tipos de bloco que você pode adicionar a um painel:

| Tile             | Descrição |
| ---------------- | ----------- |
| Markdown         | Blocos de redução são blocos clicáveis que exibem um texto de título e descrição formatado usando a redução. A URL pode ser um link relativo para outra página no aplicativo ou um link absoluto para um site externo.|
| Imagem            | Os blocos de imagem exibem uma imagem personalizada e podem ser clicáveis. A URL pode ser um link relativo para outra página no aplicativo ou um link absoluto para um site externo.|
| Label            | Os blocos de rótulo exibem um texto personalizado em um painel. Você pode escolher o tamanho do texto. Use um bloco de rótulo para adicionar informações relevantes ao painel, como descrições, detalhes de contato ou ajuda.|
| Contagem            | Os blocos de contagem exibem o número de dispositivos em um grupo de dispositivos.|
| Map              | Os blocos de mapa exibem o local de um ou mais dispositivos em um mapa. Você também pode exibir até 100 pontos do histórico de localização de um dispositivo. Por exemplo, você pode exibir uma rota de amostra de onde um dispositivo esteve na última semana.|
| KPI              |  Os blocos de KPI exibem valores de telemetria agregados para um ou mais dispositivos durante um período de tempo. Por exemplo, você pode usá-lo para mostrar a temperatura máxima e a pressão atingida para um ou mais dispositivos durante a última hora.|
| Gráfico de Linhas       | Os blocos de gráfico de linhas plotam um ou mais valores de telemetria agregados para um ou mais dispositivos por um período de tempo. Por exemplo, você pode exibir um gráfico de linhas para plotar a temperatura média e a pressão de um ou mais dispositivos na última hora.|
| Gráfico de barras        | Os blocos de gráfico de barras plotam um ou mais valores de telemetria agregados para um ou mais dispositivos por um período de tempo. Por exemplo, você pode exibir um gráfico de barras para mostrar a temperatura média e a pressão de um ou mais dispositivos na última hora.|
| Gráfico de pizza        | Os blocos de gráfico de pizza exibem um ou mais valores agregados de telemetria para um ou mais dispositivos por um período de tempo.|
| Mapa de calor         | Os blocos de mapa de calor exibem informações sobre um ou mais dispositivos, representados como cores.|
| Último valor conhecido | Os últimos blocos de valor conhecidos exibem os valores de telemetria mais recentes para um ou mais dispositivos. Por exemplo, você pode usar esse bloco para exibir os valores de temperatura, pressão e umidade mais recentes para um ou mais dispositivos. |
| Histórico de eventos    | Os blocos de histórico de eventos exibem os eventos de um dispositivo durante um período de tempo. Por exemplo, você pode usá-lo para mostrar todos os eventos de válvula abrir e fechar para um ou mais dispositivos durante a última hora.|
| Propriedade         |  Os blocos de propriedades exibem o valor atual para propriedades e propriedades de nuvem de um ou mais dispositivos. Por exemplo, você pode usar esse bloco para exibir as propriedades do dispositivo, como a versão do fabricante ou do firmware de um dispositivo. |

No momento, você pode adicionar até 10 dispositivos a blocos que dão suporte a vários dispositivos.

### <a name="customizing-visualizations"></a>Personalizar visualizações

Por padrão, os gráficos de linhas mostram dados em um intervalo de tempo. O intervalo de tempo selecionado é dividido em 50 buckets de tamanho igual e os dados do dispositivo são agregados por Bucket para fornecer 50 pontos de dados no intervalo de tempo selecionado. Se você quiser exibir dados brutos, poderá alterar sua seleção para exibir os últimos valores 100. Para alterar o intervalo de tempo ou selecionar a visualização de dados brutos, use a lista suspensa intervalo de exibição no painel **Configurar gráfico** .

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/display-range.png" alt-text="Alterar o intervalo de exibição de um gráfico de linhas":::

Para blocos que exibem valores de agregação, selecione o ícone de engrenagem ao lado do tipo de telemetria no painel **Configurar gráfico** para escolher a agregação. Você pode escolher entre média, soma, máximo, mínimo e contagem.

Para gráficos de linhas, gráficos de barras e gráficos de pizza, você pode personalizar a cor dos diferentes valores de telemetria. Selecione o ícone de paleta ao lado da telemetria que você deseja personalizar:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/color-customization.png" alt-text="Alterar a cor de um valor de telemetria":::

Para blocos que mostram Propriedades de cadeia de caracteres ou valores de telemetria, você pode escolher como exibir o texto. Por exemplo, se o dispositivo armazenar uma URL em uma propriedade de cadeia de caracteres, você poderá exibi-la como um link clicável. Se a URL fizer referência a uma imagem, você poderá renderizar a imagem em um último valor conhecido ou bloco de propriedade. Para alterar a forma como uma cadeia de caracteres é exibida, na configuração de bloco, selecione o ícone de engrenagem ao lado da propriedade ou tipo de telemetria:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/string-customization.png" alt-text="Alterar como uma cadeia de caracteres é exibida em um bloco":::

Para o **KPI** numérico, o **último valor conhecido** e os blocos de **Propriedade** , você pode usar a formatação condicional para personalizar a cor do bloco com base em seu valor atual. Para adicionar formatação condicional, selecione **Configurar** no bloco e, em seguida, selecione o ícone de **formatação condicional** ao lado do valor a ser personalizado:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-1.png" alt-text="Captura de tela mostrando como localizar a opção configurar para um bloco e, em seguida, o ícone de formatação condicional":::

Adicione suas regras de formatação condicional:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-2.png" alt-text="Captura de tela mostrando regras de formatação condicional para o fluxo médio. Há três regras – menos de 20 é verde, menos de 50 é amarelo e qualquer coisa acima de 50 é vermelho":::
   
A captura de tela a seguir mostra o efeito da regra de formatação condicional:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-3.png" alt-text="Captura de tela mostrando a cor vermelha do plano de fundo no bloco média de fluxo de água. O número no bloco é 50,54":::

### <a name="tile-formatting"></a>formatação de "bloco"
Esse recurso, disponível nos blocos KPI, LKV e propriedade, permite aos usuários ajustar o tamanho da fonte, escolher precisão decimal, abreviar valores numéricos (por exemplo, o formato 1.700 como 1.7 K) ou encapsular valores de cadeia de caracteres em seus blocos.

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/tile-format.png" alt-text="Formato do bloco":::

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como configurar seu painel de aplicativo padrão do Azure IoT Central, você pode [aprender a criar um painel pessoal](howto-create-personal-dashboards.md).
