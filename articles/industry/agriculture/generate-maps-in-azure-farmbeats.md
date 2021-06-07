---
title: Gerar mapas
description: Este artigo descreve como gerar mapas no Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: d0176fd337abd37f97a6dbe5cf7c68ccf94114d0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102173857"
---
# <a name="generate-maps"></a>Gerar mapas

Usando o Azure FarmBeats, você pode gerar os seguintes mapas usando imagens de satélite e entradas de dados de sensor. Os mapas ajudam você a ver a localização geográfica do seu farm e a identificar o local apropriado para seus dispositivos.

  - **Mapa de posicionamento do sensor**: fornece recomendações sobre quantos sensores usar e onde colocá-los em um farm.
  - **Mapa de índices satélite**: mostra o índice de vegetação e o índice de água para um farm.
  - **Calor de umidade de solo**: mostra a distribuição de umidade de solo ao recusar dados de satélite e dados de sensor.

## <a name="sensor-placement-map"></a>Mapa de posicionamento do sensor

Um mapa de posicionamento de sensor FarmBeats ajuda você com o posicionamento de sensores de umidade de solo. A saída do mapa consiste em uma lista de coordenadas para a implantação do sensor. As entradas desses sensores são usadas juntamente com imagens satélite para gerar o calor de umidade de solo.

Esse mapa é derivado ao segmentar o canopy como visto em várias datas ao longo do ano. Até mesmo Bare-solo e edifícios fazem parte do Canopy. Você pode remover os sensores que não são necessários no local. Este mapa é para orientação, e você pode alterar a posição e os números um pouco com base no seu conhecimento personalizado. A adição de sensores não reresultará em calor de umidade de terra, mas haverá uma possibilidade de deterioração na precisão do calor se o número do sensor for reduzido.

## <a name="before-you-begin"></a>Antes de começar

Atenda aos seguintes pré-requisitos antes de tentar gerar um mapa de posicionamento de sensor:

- O tamanho do farm deve ser maior que um acre.
- O número de cenas de sentinela sem nuvem deve ser maior que seis para o intervalo de datas selecionado.
- Pelo menos seis cenas de sentinela sem nuvem devem ter um NDVI (índice vegetação de diferença normalizado) maior ou igual a 0,3.

    > [!NOTE]
    > O Sentinel permite apenas dois threads simultâneos por usuário. Como resultado, os trabalhos são colocados na fila e podem levar mais tempo para serem concluídos.

### <a name="dependencies-on-sentinel"></a>Dependências no sentinela

As seguintes dependências pertencem ao sentinela:

- Dependemos do desempenho de sentinela para baixar imagens satélite. Verifique o status de desempenho do Sentinela e [as atividades](https://scihub.copernicus.eu/twiki/do/view/SciHubNews/WebHome)de manutenção.
- O Sentinel permite apenas dois [threads de downloads](https://sentinels.copernicus.eu/web/sentinel/sentinel-data-access/typologies-and-services) simultâneos por usuário.
- A geração de mapa de precisão é afetada pela [cobertura do Sentinel e pela frequência de revisita]( https://sentinel.esa.int/web/sentinel/user-guides/sentinel-2-msi/revisit-coverage).

## <a name="create-a-sensor-placement-map"></a>Criar um mapa de posicionamento do sensor
Esta seção detalha os procedimentos para criar mapas de posicionamento de sensor.

> [!NOTE]
> Você pode iniciar um mapa de posicionamento de sensor na página **mapas** ou no menu suspenso **gerar mapas de precisão** na página de **detalhes do farm** .

Siga estas etapas.

1. Na home page, vá para **mapas** no menu de navegação à esquerda.
2. Selecione **criar mapas** e selecione o **posicionamento do sensor** no menu suspenso.

    ![Selecionar o posicionamento do sensor](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-1.png)

3. Depois de selecionar o **posicionamento do sensor**, a janela de posicionamento do **sensor** é exibida.

    ![Janela de posicionamento do sensor](./media/get-sensor-data-from-sensor-partner/sensor-placement-1.png)

4. Selecione um farm no menu suspenso **farm** .
   Para pesquisar e selecionar seu farm, você pode rolar na lista suspensa ou inserir o nome do farm na caixa de texto.
5. Para gerar um mapa para o último ano, selecione **recomendado**.
6. Para gerar um mapa para um intervalo de datas personalizado, selecione a opção **selecionar intervalo de datas**. Insira a data de início e de término para a qual você deseja gerar o mapa de posicionamento do sensor.
7. Selecione **gerar mapas**.
 Uma mensagem de confirmação com detalhes do trabalho é exibida.

  Para obter informações sobre o status do trabalho, consulte a seção **exibir trabalhos**. Se o status do trabalho mostrar *falha*, uma mensagem de erro detalhada aparecerá na dica de ferramenta do status de *falha* . Nesse caso, repita as etapas anteriores e tente novamente.

  Se o problema persistir, consulte a seção [solucionar problemas](troubleshoot-azure-farmbeats.md) ou contate o [Fórum do Azure FarmBeats para obter suporte](https://aka.ms/FarmBeatsMSDN) com os logs relevantes.

### <a name="view-and-download-a-sensor-placement-map"></a>Exibir e baixar um mapa de posicionamento do sensor

Siga estas etapas.

1. Na home page, vá para **mapas** no menu de navegação à esquerda.

    ![Selecione mapas no menu de navegação à esquerda](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Selecione **Filtrar** na navegação à esquerda da janela.
  A janela **filtro** exibe os critérios de pesquisa.

    ![Janela de filtro](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Selecione os valores de **tipo**, **Data** e **nome** nos menus suspensos. Em seguida, selecione **aplicar** para pesquisar o mapa que você deseja exibir.
  A data em que o trabalho foi criado é mostrada no formato type_farmname_YYYY-MM-DD.
4. Percorra a lista de mapas disponíveis usando as barras de navegação no final da página.
5. Selecione o mapa que você deseja exibir. Uma janela pop-up exibe a visualização do mapa selecionado.
6. Selecione **baixar** e baixe o arquivo geojson de coordenadas do sensor.

    ![Visualização do mapa de posicionamento do sensor](./media/get-sensor-data-from-sensor-partner/download-sensor-placement-map-1.png)

## <a name="satellite-indices-map"></a>Mapa de índices satélite

As seções a seguir explicam os procedimentos envolvidos na criação e exibição de um mapa de índices satélite.

> [!NOTE]
> Você pode iniciar um mapa de índices satélite na página **mapas** ou no menu suspenso **gerar mapas de precisão** na página detalhes do **farm** .

O FarmBeats fornece a capacidade de gerar NDVI, índice de vegetação aprimorado (EVI) e mapas de índice de água de diferença normalizada (NDWI) para farms. Esses índices ajudam a determinar como o corte está crescendo no momento ou cresceu no passado e os níveis de água representativos no solo.


> [!NOTE]
> Uma imagem de Sentinela é necessária para os dias para os quais o mapa é gerado.


## <a name="create-a-satellite-indices-map"></a>Criar um mapa de índices satélite

Siga estas etapas.

1. Na home page, vá para **mapas** no menu de navegação à esquerda.
2. Selecione **criar mapas** e selecione **índices satélites** no menu suspenso.

    ![Selecione os índices satélite no menu suspenso](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-satellite-indices-1.png)

3. Depois de selecionar os **índices satélites**, a janela de **índices satélite** é exibida.

    ![Janela de índices satélite](./media/get-sensor-data-from-sensor-partner/satellitte-indices-1.png)

4. Selecione um farm no menu suspenso.
   Para pesquisar e selecionar seu farm, você pode rolar na lista suspensa ou inserir o nome do farm.   
5. Para gerar um mapa para a última semana, selecione **esta semana**.
6. Para gerar um mapa para um intervalo de datas personalizado, selecione a opção **selecionar intervalo de datas**. Insira a data de início e de término para a qual você deseja gerar o mapa de índices satélite.
7. Selecione **gerar mapas**.
    Uma mensagem de confirmação com detalhes do trabalho é exibida.

    ![Mensagem de confirmação do mapa de índices satélite](./media/get-sensor-data-from-sensor-partner/successful-satellitte-indices-1.png)

    Para obter informações sobre o status do trabalho, consulte a seção **exibir trabalhos**. Se o status do trabalho mostrar *falha*, uma mensagem de erro detalhada aparecerá na dica de ferramenta do status de *falha* . Nesse caso, repita as etapas anteriores e tente novamente.

    Se o problema persistir, consulte a seção [solucionar problemas](troubleshoot-azure-farmbeats.md) ou contate o [Fórum do Azure FarmBeats para obter suporte](https://aka.ms/FarmBeatsMSDN) com os logs relevantes.

### <a name="view-and-download-a-map"></a>Exibir e baixar um mapa

Siga estas etapas.

1. Na home page, vá para **mapas** no menu de navegação à esquerda.

    ![Selecionar mapas](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Selecione **Filtrar** na navegação à esquerda da janela. A janela **filtro** exibe os critérios de pesquisa.

    ![Janela de filtro exibe critérios de pesquisa](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Selecione os valores de **tipo**, **Data** e **nome** nos menus suspensos. Em seguida, selecione **aplicar** para pesquisar o mapa que você deseja exibir.
  A data em que o trabalho foi criado é mostrada no formato type_farmname_YYYY-MM-DD.

4. Percorra a lista de mapas disponíveis usando as barras de navegação no final da página.
5. Para cada combinação de **nome** e **Data** do farm, os três mapas a seguir estão disponíveis:
    - NDVI
    - EVI
    - NDWI
6. Selecione o mapa que você deseja exibir. Uma janela pop-up exibe a visualização do mapa selecionado.
7. Selecione **baixar** no menu suspenso para selecionar o formato de download. O mapa é baixado e armazenado em sua pasta local no computador.

    ![Visualização de mapa de índices satélite selecionados](./media/get-sensor-data-from-sensor-partner/download-satellite-indices-map-1.png)

## <a name="soil-moisture-heatmap"></a>Calor de umidade de solo

A umidade de solo é a água que é mantida nos espaços entre as partículas de solo.A calor de umidade de solo ajuda você a entender os dados de umidade de solo em qualquer profundidade, em alta resolução em seu farm. Para gerar um calor de umidade de solo preciso e utilizável, uma implantação uniforme de sensores é necessária. Todos os sensores devem ser do mesmo provedor. Provedores diferentes têm diferenças na maneira como a umidade de solo é medida junto com as diferenças na calibragem. O calor é gerado para uma determinada profundidade usando os sensores implantados nessa profundidade.

### <a name="before-you-begin"></a>Antes de começar

Atenda aos seguintes pré-requisitos antes de tentar gerar um calor de umidade de solo:

- Pelo menos três sensores de umidade de solo devem ser implantados. Não tente criar um calor de umidade de solo antes que os sensores sejam implantados e associados ao farm.
- A geração de um calor de umidade de solo é influenciada pela cobertura do caminho do Sentinel, pela nuvem e pela sombra da nuvem. Pelo menos uma cena de sentinela sem nuvem deve estar disponível nos últimos 120 dias, a partir do dia para o qual a calor de umidade de solo foi solicitada.
- Pelo menos metade dos sensores implantados no farm deve estar online e ter dados transmitidos para o datahub.
- O calor deve ser gerado usando medidas de sensor do mesmo provedor.


## <a name="create-a-soil-moisture-heatmap"></a>Criar um calor de umidade de solo

Siga estas etapas.

1. Na home page, vá para **mapas** no menu de navegação à esquerda para exibir a página **mapas** .
2. Selecione **criar mapas** e selecione **umidade de solo** no menu suspenso.

    ![Selecione umidade de solo no menu suspenso](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-soil-moisture-1.png)

3. Depois de selecionar a **umidade de solo**, a janela de umidade de **solo** é exibida.

    ![Janela de umidade do solo](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)

4. Selecione um farm no menu suspenso **farm** .
   Para pesquisar e selecionar seu farm, você pode rolar a partir da lista suspensa ou inserir o nome do farm no menu suspenso **selecionar Farm** .
5. No menu suspenso **selecionar medida do sensor de umidade de solo** , selecione a medida do sensor de umidade de solo (profundidade) para a qual você deseja gerar o mapa.
Para localizar a medida do sensor, vá para **sensores** e selecione qualquer sensor de umidade do solo. Em seguida, na seção **Propriedades do sensor** , use o valor em nome da **medida**.
6. Para gerar um mapa para **hoje** ou **esta semana**, selecione uma das opções.
7. Para gerar um mapa para um intervalo de datas personalizado, selecione a opção **selecionar intervalo de datas**. Insira a data de início e de término para a qual você deseja gerar o calor de umidade de solo.
8. Selecione **gerar mapas**.
 Uma mensagem de confirmação com detalhes do trabalho é exibida.

   ![Mensagem de confirmação do mapa de umidade do solo](./media/get-sensor-data-from-sensor-partner/successful-soil-moisture-1.png)

    Para obter informações sobre o status do trabalho, consulte a seção **exibir trabalhos**. Se o status do trabalho mostrar *falha*, uma mensagem de erro detalhada aparecerá na dica de ferramenta do status de *falha* . Nesse caso, repita as etapas anteriores e tente novamente.

    Se o problema persistir, consulte a seção [solucionar problemas](troubleshoot-azure-farmbeats.md) ou contate o [Fórum do Azure FarmBeats para obter suporte](https://aka.ms/FarmBeatsMSDN) com os logs relevantes.

### <a name="view-and-download-a-map"></a>Exibir e baixar um mapa

Siga estas etapas.

1. Na home page, vá para **mapas** no menu de navegação à esquerda.

    ![Ir para mapas](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Selecione **Filtrar** na navegação à esquerda da janela. A janela **filtro** é exibida de onde você pode pesquisar mapas.

    ![Selecionar filtro na navegação à esquerda](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3.  Selecione os valores de **tipo**, **Data** e **nome** nos menus suspensos. Em seguida, selecione **aplicar** para pesquisar o mapa que você deseja exibir. A data em que o trabalho foi criado é mostrada no formato type_farmname_YYYY-MM-DD.
4. Selecione o ícone de **classificação** ao lado dos cabeçalhos de tabela para classificar de acordo com o **farm**, **Data**, **criado em**, **ID do trabalho** e **tipo de trabalho**.
5. Percorra a lista de mapas disponíveis usando os botões de navegação no final da página.
6. Selecione o mapa que você deseja exibir. Uma janela pop-up exibe a visualização do mapa selecionado.
7. Selecione **baixar** no menu suspenso para selecionar o formato de download. O mapa é baixado e armazenado na pasta especificada.

    ![Visualização de calor de umidade de solo](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)
