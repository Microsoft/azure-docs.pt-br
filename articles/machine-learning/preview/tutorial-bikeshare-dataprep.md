---
title: "Tutorial de compartilhamento de bicicleta – preparação de dados avançada com o Azure Machine Learning Workbench"
description: "Tutorial de preparação de dados de ponta a ponta usando o Azure Machine Learning Workbench"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial, azure
ms.topic: article
ms.date: 09/21/2017
ms.openlocfilehash: 722657c9bbae23a051a63972a8800d3cc40e7e40
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="bike-share-tutorial-advanced-data-preparation-with-azure-machine-learning-workbench"></a>Tutorial de compartilhamento de bicicleta: preparação de dados avançada com o Azure Machine Learning Workbench
Os serviços do Azure Machine Learning (versão prévia) são uma solução integrada de análise avançada e de ciência de dados de ponta a ponta para cientistas profissionais prepararem dados, desenvolverem experiências e implantarem modelos em escala de nuvem.

Neste tutorial, você usará os serviços do Azure Machine Learning (versão prévia) para aprender como:
> [!div class="checklist"]
> * Preparar dados interativamente com a ferramenta de preparação de dados do Azure Machine Learning
> * Importar, transformar e criar um conjunto de dados de teste
> * Gerar um pacote de preparação de dados
> * Execute o pacote de preparação de dados com Python
> * Gerar um conjunto de dados de treinamento reutilizando o pacote de preparação de dados para arquivos de entrada adicionais

> [!IMPORTANT]
> Este tutorial apenas prepara os dados, ele não cria o modelo de previsão.
>
> Você pode usar os dados preparados para treinar seus próprios modelos de previsão. Por exemplo, você pode criar um modelo para prever a demanda de bicicleta durante um período de 2 horas.

## <a name="prerequisites"></a>Pré-requisitos
* O Azure Machine Learning Workbench precisa ser instalado localmente. Para obter mais informações, siga o [Guia de início rápido de instalação](quickstart-installation.md).
* Familiaridade com a criação de um novo projeto no Workbench.

## <a name="data-acquisition"></a>Aquisição de dados
Este tutorial usa o [conjunto de dados Boston Hubway](https://s3.amazonaws.com/hubway-data/index.html) e dados de clima de Boston do [NOAA](http://www.noaa.gov/).

1. Baixe os arquivos de dados nos links a seguir para o seu ambiente de desenvolvimento local. 
   * [Dados de clima de Boston](https://azuremluxcdnprod001.blob.core.windows.net/docs/azureml/bikeshare/BostonWeather.csv). 
   * Dados de trajeto do Hubway do site Hubway.

      - [201501-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201501-hubway-tripdata.zip)
      - [201504-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201504-hubway-tripdata.zip)
      - [201510-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201510-hubway-tripdata.zip)
      - [201601-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201601-hubway-tripdata.zip)
      - [201604-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201604-hubway-tripdata.zip)
      - [201610-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201610-hubway-tripdata.zip)
      - [201701-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201701-hubway-tripdata.zip)

2. Descompacte cada arquivo .zip após o download.

## <a name="learn-about-the-datasets"></a>Saiba mais sobre conjuntos de dados
1. O arquivo __Clima de Boston__ contém os seguintes campos relacionados ao clima, relatados de hora em hora:
   * DATE
   * REPORTTPYE
   * HOURLYDRYBULBTEMPF
   * HOURLYRelativeHumidity
   * HOURLYWindSpeed

2. Os dados do __Hubway__ são organizados em arquivos por ano e por mês. Por exemplo, o arquivo chamado `201501-hubway-tripdata.zip` contém um arquivo .csv que contém dados de janeiro de 2015. Os dados contém os campos a seguir, cada linha representando um trajeto de bicicleta:

   * Duração do trajeto (em segundos)
   * Data e hora de início
   * Data e hora de parada
   * Nome e ID da estação inicial
   * Nome e ID da estação final
   * ID da bicicleta
   * Tipo de usuário (Casual = usuário com permissão de 24 horas ou 72 horas; Membro = membro mensal ou anual)
   * CEP (se o usuário for membro)
   * Sexo (declarado pelo próprio membro)

## <a name="create-a-new-project"></a>Criar um novo projeto
1. Inicie o **Azure Machine Learning Workbench** no menu Iniciar ou usando o iniciador.

2. Crie um novo projeto do Azure Machine Learning.  Clique no botão **+** na página **Projetos** ou em **Arquivo** > **Novo**.
   - Use o modelo **Projeto em Branco**.
   - Nomeie o projeto **BikeShare**. 

## <a id="newdatasource"></a>Criar uma nova fonte de dados

1. Crie uma nova fonte de dados. Clique no botão **Dados** (ícone de cilindro) na barra de ferramentas à esquerda. A **Exibição de Dados** é mostrada.

   ![Imagem da guia de exibição de dados](media/tutorial-bikeshare-dataprep/navigatetodatatab.png)

2. Adicione uma fonte de dados. Selecione o ícone **+** e, em seguida, selecione **Adicionar Fonte de Dados**.

   ![Imagem da entrada para Adicionar Fonte de Dados](media/tutorial-bikeshare-dataprep/newdatasource.png)

## <a name="add-weather-data"></a>Adicionar dados de clima

1. **Armazenamento de Dados**: selecione o armazenamento de dados que contém os dados. Como você está usando arquivos, selecione **Arquivo(s)/Diretório**. Selecione **Avançar** para continuar.

   ![Imagem dos arquivos/Entrada de diretório](media/tutorial-bikeshare-dataprep/datasources.png)

2. **Seleção de Arquivo**: adicione os dados de clima. Procure e selecione o arquivo `BostonWeather.csv` que você baixou anteriormente. Clique em **Avançar**.

   ![Imagem da seleção de arquivo com BostonWeater.csv selecionado](media/tutorial-bikeshare-dataprep/pickweatherdatafile.png)

3. **Detalhes do Arquivo**: verifique o esquema de arquivo detectado. O Azure Machine Learning Workbench analisa os dados no arquivo e deduz o esquema a ser usado.

   ![Imagem dos detalhes do arquivo](media/tutorial-bikeshare-dataprep/fileparameters.png)

   > [!IMPORTANT]
   > O Workbench pode não detectar o esquema correto em alguns casos. Você sempre deve verificar se os parâmetros estão corretos para o seu conjunto de dados. Para os dados de clima, verifique se eles estão configurados com os seguintes valores:
   >
   > * __Tipo de Arquivo__: arquivo delimitado (csv, tsv, txt, etc.)
   > * __Separador__: vírgula [,]
   > * __Caractere de Linha de Comentário__: nenhum valor definido.
   > * __Modo Ignorar Linhas__: não ignorar
   > * __Codificação de Arquivo__: UTF-8
   > * __Modo Promover Cabeçalhos__: usar cabeçalhos do primeiro arquivo

   A visualização dos dados deve exibir as seguintes colunas:
   * **Caminho**
   * **DATE**
   * **REPORTTYPE**
   * **HOURLYDRYBULBTEMPF**
   * **HOURLYRelativeHumidity**
   * **HOURLYWindSpeed**

   Para continuar, selecione **Avançar**.

4. **Tipos de Dados**: examine os tipos de dados que são detectados automaticamente. O Azure Machine Learning Workbench analisa os dados no arquivo e deduz os tipos de dados a serem usados.

   Para esses dados, altere o `DATA TYPE` de todas as colunas para `String`.

   > [!NOTE]
   > `String` será usado para realçar os recursos do Workbench posteriormente neste tutorial. 

   ![Imagem dos tipos de dados](media/tutorial-bikeshare-dataprep/datatypedetection.png)

   Para continuar, selecione __Avançar__. 

5. **Amostragem**: para criar um esquema de amostragem, selecione o botão **+ Novo**. Selecione a nova linha __10000 Principais__ que foi adicionada e, em seguida, selecione __Editar__. Defina a __Estratégia de Exemplo__ como **Arquivo Completo** e, em seguida, selecione **Aplicar**.

   ![Imagem da adição de uma nova estratégia de amostragem](media/tutorial-bikeshare-dataprep/weatherdatasampling.png)

   Para usar a estratégia __Arquivo Completo__, selecione a entrada __Arquivo Completo__ e, em seguida, selecione __Definido como Ativo__. Um asterisco é exibido ao lado de __Arquivo Completo__ para indicar que esta é a estratégia ativa.

   ![Imagem do Arquivo Completo que é a estratégia de ativa](media/tutorial-bikeshare-dataprep/fullfileactive.png)

   Para continuar, selecione **Avançar**.

6. **Coluna de Caminho**: a seção __Coluna de Caminho__ permite que você inclua o caminho de arquivo completo como uma coluna nos dados importados. Selecione __Não Incluir Coluna de Caminho__.

   > [!TIP]
   > Incluir o caminho como uma coluna será útil se você estiver importando uma pasta de muitos arquivos com nomes de arquivo diferentes. Isso também será útil se os nomes de arquivo contiverem informações que você deseja extrair mais tarde.

   ![Imagem de Coluna de Caminho definida para não incluir](media/tutorial-bikeshare-dataprep/pathcolumn.png)

7. **Concluir**: para concluir a criação da fonte de dados, selecione o botão **Concluir**.

    Uma nova guia de fonte de dados chamada __BostonWeather__ é aberta. Um exemplo de dados é exibido em uma exibição em grade. O exemplo é baseado no esquema de amostragem ativo especificado anteriormente.

    Observe que o painel **Etapas** no lado direito da tela exibe as ações individuais executadas durante a criação desta fonte de dados.

   ![Imagem exibindo a fonte de dados, o exemplo e as etapas](media/tutorial-bikeshare-dataprep/weatherdataloaded.png)

### <a name="view-data-source-metrics"></a>Exibir métricas de fonte de dados

Selecione o botão __Métricas__ na parte superior esquerda da exibição em grade da guia. Essa exibição mostra a distribuição e outras estatísticas agregadas dos dados de amostra.

![Imagem das exibições de métricas](media/tutorial-bikeshare-dataprep/weathermetrics.png)

> [!NOTE]
> Para configurar a visibilidade das estatísticas, use a lista suspensa **Escolher Métrica**. Marque e desmarque as métricas nesse local para alterar a exibição em grade.

Para retornar a __Exibição de Dados__, selecione __Dados__ no canto superior esquerdo da página.

## <a name="add-data-source-to-data-preparation-package"></a>Adicionar fonte de dados ao pacote de preparação de dados

1. Selecione __Preparar__ para começar a preparar os dados. 

2. Quando solicitado, insira um nome para o pacote de Preparação de Dados, como `BikeShare Data Prep`. 

3. Selecione __OK__ para continuar.

   ![Imagem da caixa de diálogo de preparação](media/tutorial-bikeshare-dataprep/dataprepdialog.png)

4. Um novo pacote chamado **Preparação de Dados BikeShare** aparece na seção __Preparação de Dados__ da guia __Dados__. 

   Para exibir o pacote, selecione essa entrada. 

5. Selecione o botão **>>** para expandir e exibir o __Fluxos de Dados__ contidos no pacote. Neste exemplo, __BostonWeather__ é o único fluxo de dados.

   > [!IMPORTANT]
   > Um pacote pode conter vários fluxos de dados.

   ![Imagem dos fluxos de dados contidos no pacote](media/tutorial-bikeshare-dataprep/weatherdataloadedingrid.png)

## <a name="filter-data-by-value"></a>Filtrar dados por valor
1. Para filtrar os dados, clique com o botão direito do mouse em uma célula com um determinado valor, selecione __Filtrar__ e, em seguida, escolha o tipo de filtro.

2. Para este tutorial, selecione uma célula que contenha o valor `FM-15` e, em seguida, defina o filtro para um filtro **É igual a**.  Agora os dados estão filtrados para retornar apenas as linhas em que __REPORTTYPE__ é `FM-15`.

   ![Imagem da caixa de diálogo de filtro](media/tutorial-bikeshare-dataprep/weatherfilterinfm15.png)

   > [!NOTE]
   > FM-15 é um tipo de METAR (Informe Meteorológico Regular de Aeródromo). Na prática, os relatórios FM-15 são considerados os mais completos, com poucos dados ausentes.

## <a name="remove-a-column"></a>Remover uma coluna

Você não precisa mais da coluna __REPORTTYPE__. Clique com botão direito do mouse no cabeçalho da coluna e selecione **Remover Coluna**.

   ![Imagem da opção para remover coluna](media/tutorial-bikeshare-dataprep/weatherremovereporttype.png)

## <a name="change-datatypes-and-remove-errors"></a>Alterar tipos de dados e remover erros
1. Pressionar __Ctrl__ enquanto seleciona os cabeçalhos de coluna permite que você selecione várias colunas de uma vez. Use isso para selecionar os seguintes cabeçalhos de coluna:
   * **HOURLYDRYBULBTEMPF**
   * **HOURLYRelativeHumidity**
   * **HOURLYWindSpeed**

2. **Clique com botão direito do mouse** em um dos cabeçalhos de coluna selecionados e selecione **Converter o tipo de campo em numérico**. Isso converte o tipo de dados das colunas em numérico.

   ![Convertendo várias colunas para numérico](media/tutorial-bikeshare-dataprep/weatherconverttonumeric.png)

3. Filtre os valores de erro. Algumas colunas têm problemas de conversão de tipo de dados. Esse problema é indicado pela cor vermelha na __Barra de Qualidade de Dados__ da coluna.

   Para remover as linhas com erros, clique com o botão direito do mouse no título de coluna **HOURLYDRYBULBTEMPF**. Selecione **Filtrar Coluna**. Use o **Eu Quero** padrão como **Manter Linhas**. Altere a lista suspensa **Condições** para selecionar **não é erro**. Selecione **OK** para aplicar o filtro.

4. Para eliminar as linhas de erro restantes nas outras colunas, repita esse processo de filtro para as colunas **HOURLYRelativeHumidity** e **HOURLYWindSpeed**.

## <a name="use-by-example-transformations"></a>Usar transformações pelo exemplo

Para usar os dados em uma previsão de blocos de tempo de duas horas, você deve calcular as condições médias de clima em períodos de duas horas. Para fazer isso, você pode usar as ações a seguir:

* Divida a coluna **DATE** nas colunas **Data** e **Hora** separadas. Consulte a próxima seção para obter as etapas detalhadas.

* Derive uma coluna **Intervalo_de_Hora** da coluna **Hora**. Consulte a seção a seguir para obter as etapas detalhadas.

* Derive uma coluna **Intervalo\_de_Data\_e_Hora** das colunas **DATE** e **Intervalo_de_Hora**. Consulte a seção a seguir para obter as etapas detalhadas.

### <a name="split-column-by-example"></a>Dividir colunas por exemplo

1. Divida a coluna **DATE** nas colunas data e hora separadas. Clique com botão direito do mouse no cabeçalho da coluna **DATE** e selecione **Dividir Coluna pelo Exemplo**.

   ![Imagem da entrada de divisão de coluna pelo exemplo](media/tutorial-bikeshare-dataprep/weathersplitcolumnbyexample.png)

2. O Azure Machine Learning Workbench identifica automaticamente um delimitador significativo e cria duas colunas, dividindo os dados em valores de data e hora. 

3. Selecione __OK__ para aceitar os resultados da operação de divisão.

   ![Imagem da divisão de colunas DATE_1 e DATE_2](media/tutorial-bikeshare-dataprep/weatherdatesplitted.png)

### <a name="derive-column-by-example"></a>Derivar colunas por exemplo

1. Para derivar um intervalo de duas horas, clique com o botão direito do mouse no cabeçalho da coluna __DATE\_2__ e selecione **Derivar Coluna pelo Exemplo**.

   ![Imagem da entrada de derivar coluna pelo exemplo](media/tutorial-bikeshare-dataprep/weatherdate2range.png)

   Uma nova coluna vazia é adicionada com valores nulos.

2. Clique na primeira célula vazia na nova coluna. Forneça um exemplo do intervalo de tempo desejado digitando `12AM-2AM` na nova coluna e, em seguida, pressione Enter.

   ![Imagem da nova coluna com o valor 12AM-2AM](media/tutorial-bikeshare-dataprep/weathertimerangeexample.png)

   > [!NOTE]
   > O Azure ML Workbench sintetiza um programa com base nos exemplos fornecidos por você e aplica o mesmo programa às linhas restantes. Todas as outras linhas são preenchidas automaticamente com base no exemplo fornecido. O Workbench também analisa os dados e tenta identificar casos extremos. 

3. O texto **Analisando Dados** acima da grade indica que o Workbench está tentando detectar os casos extremos. Quando ele terminar, o status será alterado para **Examinar a próxima linha sugerida** ou **Nenhuma sugestão**. Neste exemplo, é retornado **Examinar a próxima linha sugerida**.

4. Para examinar as alterações sugeridas, selecione **Revisar a próxima linha sugerida**. A célula que você deve examinar e corrigir (se necessário) está realçada na exibição.

   ![Imagem de examinar linha](media/tutorial-bikeshare-dataprep/weatherreviewnextsuggested.png)

    Selecione __OK__ para aceitar a transformação.
 
5. Você retorna para a exibição em grade dos dados do __BostonWeather__. Agora, a grade contém as três colunas adicionadas anteriormente.

   ![Imagem de exibição em grade com linhas adicionadas](media/tutorial-bikeshare-dataprep/timerangecomputed.png)

   > [!TIP]
   >  Todas as alterações feitas são preservadas no painel **Etapas**. Vá para a etapa que você criou no painel **Etapas**, clique na seta para baixo e selecione **Editar**. A janela avançada de **Derivar Coluna pelo Exemplo** é exibida. Todos os seus exemplos são preservados aqui. Você também pode adicionar exemplos manualmente clicando duas vezes em uma linha na grade abaixo. Selecione **Cancelar** para retornar à grade principal sem aplicar alterações. Você também pode acessar essa exibição selecionando **Modo Avançado** durante a execução de um transformação **Derivar Coluna pelo Exemplo**.

6. Para renomear a coluna, clique duas vezes no cabeçalho da coluna e digite **Intervalo de Hora**. Pressione **Enter** para salvar a alteração.

   ![Renomeando colunas](media/tutorial-bikeshare-dataprep/weatherhourrangecolumnrename.png)

7. Para derivar o intervalo de data e hora, faça a seleção múltipla das colunas **Date\_1** e **Intervalo de Hora**, clique com o botão direito do mouse e selecione **Derivar Coluna pelo Exemplo**.

   ![Derivando colunas pelo exemplo](media/tutorial-bikeshare-dataprep/weatherderivedatehourrange.png)

   Digite `Jan 01, 2015 12AM-2AM` como o exemplo para a primeira linha e pressione **Enter**.

   O Workbench determina a transformação com base no exemplo que você fornece. Neste exemplo, o resultado é que o formato de data é alterado e concatenado com o período de duas horas.

   ![Imagem do exemplo `Jan 01, 2015 12AM-2AM](media/tutorial-bikeshare-dataprep/wetherdatehourrangeexample.png)

8. Espere até que o status seja alterado de **Analisando Dados** para **Examinar a próxima linha sugerida**. Isso pode levar alguns segundos. Selecione o link de status para navegar até a linha sugerida. 

   ![Imagem da linha sugerida para examinar](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguate.png)

   A linha tem um valor nulo, porque o valor da data de origem pode ser dd/mm/aaaa ou mm/dd/aaaa. Digite o valor correto de `Jan 13, 2015 2AM-4AM` e pressione **Enter**. O Workbench usa os dois exemplos para melhorar a derivação das linhas restantes.

   ![Imagem de dados formatados corretamente](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguated.png)

9. Selecione **OK** para aceitar a transformação.

   ![Imagem da grade de transformação concluída](media/tutorial-bikeshare-dataprep/weatherdatehourrangecomputed.png)

   > [!TIP]
   > Você pode usar o modo avançado de **Derivar coluna pelo exemplo** nesta etapa clicando na seta para baixo no painel **Etapas**. Na grade de dados, há caixas de seleção ao lado dos nomes das colunas **DATE\_1** e **Intervalo de Hora**. Desmarque a caixa de seleção ao lado da coluna **Intervalo de Hora** para ver como isso altera a saída. Na ausência da coluna **Intervalo de Hora** como entrada, **12AM-2AM** será tratado como uma constante e será anexado aos valores derivados. Selecione **Cancelar** para retornar à grade principal sem aplicar as alterações.

10. Para renomear a coluna, clique duas vezes no cabeçalho. Altere o nome para **Intervalo de Data e Hora** e, em seguida, pressione **Enter**.

11. Faça a seleção múltipla das colunas **DATE**, **DATE\_1**, **DATE\_2** e **Intervalo de Hora**. Clique com o botão direito do mouse e, em seguida, selecione **Remover coluna**.

## <a name="summarize-data-mean"></a>Resumir dados (média)

A próxima etapa é resumir as condições de clima obtendo a média dos valores, agrupados por intervalo de hora.

1. Selecione a coluna **Intervalo de Data e Hora** e, em seguida, selecione **Resumir** no menu **Transformações**.

    ![Menu Transformações](media/tutorial-bikeshare-dataprep/weathersummarizemenu.png)

2. Para resumir os dados, arraste colunas da grade na parte inferior da página para os painéis esquerdo e direito na parte superior. O painel esquerdo contém o texto **Arrastar colunas aqui para agrupar dados**. O painel direito contém o texto **Arrastar colunas aqui para resumir dados**. 

    Arraste a coluna **Intervalo de Data e Hora** da grade na parte inferior do painel esquerdo. Arraste **HOURLYDRYBULBTEMPF**, **HOURLYRelativeHumidity** e **HOURLYWindSpeed** para o painel direito. 

    No painel direito, selecione **Média** como a medida **Agregação** para cada coluna. Clique em **OK** para concluir o resumo.

   ![Imagem da tela de dados resumidos](media/tutorial-bikeshare-dataprep/weathersummarize.png)

## <a name="transform-dataflow-using-script"></a>Transformar o fluxo de dados usando o script

Alterar os dados nas colunas numéricas para um intervalo de 0 a 1 permite que alguns modelos sejam convergidos rapidamente. Atualmente não há nenhuma transformação interna para fazer essa transformação genericamente, mas um script Python pode ser usado para executar esta operação.

1. No menu **Transformação**, selecione **Transformar Fluxo de Dados**.

2. Insira o código a seguir na caixa de texto que aparece. Se você tiver usado os nomes de coluna, o código deverá funcionar sem modificação. Você está escrevendo uma lógica de normalização de mín./máx. simples em Python.

    > [!WARNING]
    > O script espera os nomes de coluna usados anteriormente neste tutorial. Se você tiver nomes de coluna diferentes, será possível alterar os nomes no script.

   ```python
   maxVal = max(df["HOURLYDRYBULBTEMPF_Mean"])
   maxVal = max(df["HOURLYDRYBULBTEMPF_Mean"])
   minVal = min(df["HOURLYDRYBULBTEMPF_Mean"])
   df["HOURLYDRYBULBTEMPF_Mean"] = (df["HOURLYDRYBULBTEMPF_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYDRYBULBTEMPF_Mean":"N_DryBulbTemp"},inplace=True)

   maxVal = max(df["HOURLYRelativeHumidity_Mean"])
   minVal = min(df["HOURLYRelativeHumidity_Mean"])
   df["HOURLYRelativeHumidity_Mean"] = (df["HOURLYRelativeHumidity_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYRelativeHumidity_Mean":"N_RelativeHumidity"},inplace=True)

   maxVal = max(df["HOURLYWindSpeed_Mean"])
   minVal = min(df["HOURLYWindSpeed_Mean"])
   df["HOURLYWindSpeed_Mean"] = (df["HOURLYWindSpeed_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYWindSpeed_Mean":"N_WindSpeed"},inplace=True)

   df
   ```

    > [!TIP]
    > O script Python deverá retornar `df` no final. Esse valor é usado para popular a grade.
    
    ![Diálogo Transformar script do fluxo de dados](media/tutorial-bikeshare-dataprep/transformdataflowscript.png)

3. Selecione __OK__ para usar o script. Agora, as colunas numéricas na grade contêm valores no intervalo de 0 a 1.

    ![Grade que contém os valores entre 0 e 1](media/tutorial-bikeshare-dataprep/datagridwithdecimals.png)

Você concluiu a preparação dos dados de clima. Em seguida, prepare os dados de percurso.

## <a name="load-trip-data"></a>Carregar dados de percurso

1. Para importar o arquivo `201701-hubway-tripdata.csv`, use as etapas na seção [Criar uma nova fonte de dados](#newdatasource). Use as opções a seguir durante o processo de importação:

    * __Esquema de amostragem__: esquema de amostragem de **Arquivo Completo**, ative o exemplo e 
    * __Tipo de Dados__: aceite os padrões.

2. Depois de importar os dados, selecione o botão __Preparar__ para começar a preparar os dados. Selecione o pacote **BikeShare Data Prep.dprep** existente e, em seguida, selecione __OK__.

    Esse processo adiciona um **Fluxo de Dados** no arquivo **Preparação de Dados** existente em vez de criar um novo.

    ![Imagem da seleção do pacote existente](media/tutorial-bikeshare-dataprep/addjandatatodprep.png)

3. Depois que a grade for carregada, expanda __DATAFLOWS__. Agora, há dois fluxos de dados: **BostonWeather** e **201701-hubway-tripdata**. Selecione a entrada **201701-hubway-tripdata**.

    ![Imagem da entrada 201701-hubway-tripdata](media/tutorial-bikeshare-dataprep/twodfsindprep.png)

## <a name="use-map-inspector"></a>Use o inspetor de mapa

Para a preparação de dados, há inúmeras visualizações úteis chamadas **Inspetores** para dados de cadeia de caracteres, numéricos e geográficos que ajudam a entender melhor os dados e a identificar exceções. Use as etapas a seguir para usar o Inspetor de mapa:

1. Faça uma seleção múltipla das colunas **latitude da estação inicial** e **longitude da estação inicial**. Clique com o botão direito do mouse em uma das colunas e, em seguida, selecione **Mapa**.

    > [!TIP]
    > Para habilitar a seleção múltipla, mantenha a tecla __Ctrl__ pressionada e selecione o cabeçalho de cada coluna.

    ![Imagem de visualização do mapa](media/tutorial-bikeshare-dataprep/launchMapInspector.png)

2. Para maximizar a visualização do mapa, selecione o ícone **Maximizar**. Para ajustar o mapa à janela, selecione o ícone **E** no canto superior esquerdo da visualização.

    ![Imagem maximizada](media/tutorial-bikeshare-dataprep/maximizedmap.png)

3. Clique no botão **Minimizar** para retornar à exibição em grade.

## <a name="use-column-statistics-inspector"></a>Usar o inspetor de estatística de coluna

Para usar o inspetor de estatística de coluna, clique com o botão direito do mouse na coluna __tripduration__ e selecione __Estatística da Coluna__.

![Entrada de estatística da coluna](media/tutorial-bikeshare-dataprep/tripdurationcolstats.png)

Esse processo adiciona uma nova visualização intitulada __Estatística de tripduration__ no painel __INSPECTORS__.

![Imagem do Inspetor de estatística de tripduration](media/tutorial-bikeshare-dataprep/tripdurationcolstatsinwell.png)

> [!IMPORTANT]
> O valor máximo da duração da viagem é **961.814 minutos**, ou seja, cerca de dois anos. Parece que há algumas exceções no conjunto de dados.

## <a name="use-histogram-inspector"></a>Usar o inspetor de histograma

Para tentar identificar as exceções, clique com o botão direito do mouse na coluna __tripduration__ e selecione __Histograma__.

![Inspetor de histograma](media/tutorial-bikeshare-dataprep/tripdurationhistogram.png)

O histograma não é útil, pois as exceções estão distorcendo o gráfico.

## <a name="add-column-using-script"></a>Adicionar coluna usando script

1. Clique com botão direito do mouse na coluna **tripduration** e selecione **Adicionar Coluna (Script)**.

    ![Imagem do menu para adicionar coluna (script)](media/tutorial-bikeshare-dataprep/computecolscript.png)

2. Na caixa de diálogo __Adicionar Coluna (Script)__, use os seguintes valores:

    * __Nome da Nova Coluna__: logtripduration
    * __Inserir essa Nova Coluna Após__: tripduration
    * __Novo Código de Coluna__: `math.log(row.tripduration)`
    * __Tipo de Bloco de Código__: expressão

   ![A caixa de diálogo Adicionar Coluna (script)](media/tutorial-bikeshare-dataprep/computecolscriptdialog.png)

3. Selecione __OK__ para adicionar a coluna **logtripduration**.

4. Clique com o botão direito do mouse na coluna e selecione **Histograma**.

    ![Histograma da coluna logtripduration](media/tutorial-bikeshare-dataprep/logtriphistogram.png)

  Visualmente, esse histograma aparenta ser uma Distribuição Normal com um final anormal.

## <a name="use-advanced-filter"></a>Usar um filtro avançado

Usar um filtro nos dados atualiza os inspetores com a nova distribuição. Clique com botão direito do mouse na coluna **logtripduration** e selecione **Filtrar Coluna**. Na caixa de diálogo __Editar__ use os seguintes valores:

* __Filtrar esta Coluna de Número__: logtripduration
* __Eu Quero__: manter linhas
* __Quando__: qualquer uma das condições abaixo for True (OR lógico)
* __Se esta Coluna__: for menor que
* __O valor__: 9

![Opções de filtro](media/tutorial-bikeshare-dataprep/loftripfilter.png)

Selecione __OK__ para aplicar o filtro.

![Histogramas atualizadas depois que o filtro foi aplicado](media/tutorial-bikeshare-dataprep/loftripfilteredinspector.png)

### <a name="the-halo-effect"></a>O efeito de halo

1. Maximize o histograma **logtripduration**. Há um histograma azul sobreposto em um histograma cinza. Essa exibição é chamada de **Efeito Halo**:

    * O **histograma cinza** representa a distribuição antes da operação (nesse caso, a operação de filtragem).
    * O **histograma azul** representa o histograma após a operação. 

   O efeito de halo ajuda a visualizar o efeito de uma operação nos dados.

   ![Imagem do efeito de halo](media/tutorial-bikeshare-dataprep/loftripfilteredinspectormaximized.png)

    > [!NOTE]
    > Observe que o histograma azul aparece menor em comparação ao anterior. Isso ocorre devido à nova segmentação automática dos dados no novo intervalo.

2. Para remover o halo, selecione __Editar__ e desmarque __Mostrar halo__.

    ![Opções do histograma](media/tutorial-bikeshare-dataprep/uncheckhalo.png)

3. Selecione **OK** para desabilitar o efeito de halo e minimize o histograma.

### <a name="remove-columns"></a>Remover colunas

Nos dados de trajeto, cada linha representa um evento de retirada de bicicleta. Para este tutorial, você só precisa das colunas **starttime** e **estação inicial**. Remova as outras colunas fazendo uma seleção múltipla dessas duas colunas, clicando com o botão direito do mouse no cabeçalho da coluna e, em seguida, selecionando **Manter Coluna**. As outras colunas foram removidas.

![Imagem da opção manter coluna](media/tutorial-bikeshare-dataprep/tripdatakeepcolumn.png)

### <a name="summarize-data-count"></a>Resumir dados (contagem)

Para resumir a demanda de bicicleta por um período de 2 horas, use colunas derivadas.

1. Clique com o botão direito do mouse na coluna **starttime** e selecione **Derivar Coluna pelo Exemplo**

    ![Imagem da opção derivar pelo exemplo](media/tutorial-bikeshare-dataprep/tripdataderivebyexample.png)

2. Por exemplo, insira o valor `Jan 01, 2017 12AM-2AM` para a primeira linha.

    > [!IMPORTANT]
    > No exemplo de derivação de colunas anterior, você usou várias etapas para derivar uma coluna que continha o período de data e hora. Neste exemplo, você pode ver que essa operação pode ser executada como uma única etapa, fornecendo um exemplo de saída final.

    > [!NOTE]
    > Você pode fornecer um exemplo em relação a qualquer uma das linhas. Neste exemplo, o valor `Jan 01, 2017 12AM-2AM` é válido para a primeira linha de dados.

    ![Imagem dos dados de exemplo](media/tutorial-bikeshare-dataprep/tripdataderivebyexamplefirstexample.png)

3. Espere até que o aplicativo calcule os valores em todas as linhas. Isso pode levar vários segundos. Depois que a análise for concluída, use o link __Examinar próxima linha sugerida__ para examinar os dados.

   ![Imagem da análise concluída com link de análise](media/tutorial-bikeshare-dataprep/tripdatabyexanalysiscomplete.png)

    Verifique se os valores calculados estão corretos. Caso contrário, atualize com o valor esperado e pressione Enter. Espere até que a análise seja concluída. Conclua o processo **Examinar próxima linha sugerida** até que aparece **Nenhuma sugestão**. Quando aparecer **Nenhuma sugestão**, o aplicativo já terá analisado os casos extremos e estará satisfeito com o programa sintetizado. Uma prática recomendada é realizar uma inspeção visual dos dados transformados antes de aceitar a transformação. 

4. Selecione **OK** para aceitar a transformação. Renomeie a coluna recém-criada para **Intervalo de Data e Hora**.

    ![Imagem da coluna renomeada](media/tutorial-bikeshare-dataprep/tripdatasummarize.png)

5. Clique com botão direito do mouse no cabeçalho da coluna **starttime** e selecione **Remover coluna**.

6. Para resumir os dados, selecione __Resumir__ no menu __Transformação__. Para criar a transformação, execute as seguintes ações:

    * Arraste __Intervalo de Data e Hora__ e __ID da estação inicial__ para o painel esquerdo (agrupar por).
    * Arraste __ID da estação inicial__ para o painel à direita (resumir dados).

   ![Uma imagem das opções de resumo](media/tutorial-bikeshare-dataprep/tripdatacount.png)

7. Selecione **OK** para aceitar o resultado do resumo.

## <a name="join-dataflows"></a>Unir fluxos de dados

Para unir os dados de clima aos dados de viagem, use as seguintes etapas:

1. Selecione __Unir__ no menu __Transformações__.

2. __Tabelas__: selecione **BostonWeather** como o fluxo de dados à esquerda e **201701-hubway-tripdata** como o fluxo de dados à direita. Para continuar, selecione **Avançar**.

    ![Imagem das seleções das tabelas](media/tutorial-bikeshare-dataprep/jointableselection.png)

3. __Colunas Principais__: selecione a coluna **Intervalo de Data e Hora** nas duas tabelas e, em seguida, selecione __Avançar__.

    ![Imagem da junção das colunas principais](media/tutorial-bikeshare-dataprep/joinkeyselection.png)

4. __Tipo de Junção__: selecione __Linhas Correspondentes__ como o tipo de junção e, em seguida, selecione __Concluir__.

    ![Tipo de junção de linhas correspondentes](media/tutorial-bikeshare-dataprep/joinscreen.png)

    Esse processo cria um novo fluxo de dados denominado __Resultado da Junção__.

## <a name="create-additional-features"></a>Criar recursos adicionais

1. Para criar uma coluna que contenha o dia da semana, clique com o botão direito do mouse na coluna **Intervalo de Data e Hora** e selecione **Derivar coluna pelo exemplo**. Use o valor __Dom__ para uma data ocorrida em um domingo. Por exemplo, **Jan 01, 2017 12AM-2AM**. Pressione **Enter** e, em seguida, selecione **OK**. Renomeie esta coluna como __Dia da semana__.

    ![Imagem da criação de uma nova coluna que contém o dia da semana](media/tutorial-bikeshare-dataprep/featureweekday.png)

2. Para criar uma coluna que contenha o período de tempo de uma linha, clique com o botão direito do mouse na coluna **Intervalo de Data e Hora** e selecione **Derivar coluna pelo exemplo**. Use o valor **12AM-2AM** para a linha que contém **Jan 01, 2017 12AM-2AM**. Pressione **Enter** e, em seguida, selecione **OK**. Renomeie essa coluna para **Período**.

    ![Imagem da coluna de período](media/tutorial-bikeshare-dataprep/featurehourrange.png)

3. Para remover as colunas **Intervalo de Data e Hora** e **Intervalo de Data e Hora**, pressione **Ctrl** e selecione o cabeçalho de cada coluna. Clique com botão direito do mouse e, em seguida, selecione **Remover Coluna**.

## <a name="read-data-from-python"></a>Ler dados do Python

Você pode executar um pacote de preparação de dados do Python ou do PySpark e recuperar o resultado como um **Quadro de Dados**.

Para gerar um script Python de exemplo, clique com o botão direito do mouse em __Preparação de dados do BikeShare__ e selecione __Gerar Arquivo de Código de Acesso a Dados__. O arquivo Python de exemplo é criado na **Pasta do Projeto** e também é carregado em uma guia no Workbench. O script Python a seguir é um exemplo do código gerado:

```python
# Use the Azure Machine Learning data preparation package
from azureml.dataprep import package

# Use the Azure Machine Learning data collector to log various metrics
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()

# This call will load the referenced package and return a DataFrame.
# If run in a PySpark environment, this call returns a
# Spark DataFrame. If not, it will return a Pandas DataFrame.
df = package.run('BikeShare Data Prep.dprep', dataflow_idx=0)

# Remove this line and add code that uses the DataFrame
df.head(10)
```

Para este tutorial, o nome do arquivo é `BikeShare Data Prep.py`. Esse arquivo será usado mais tarde no tutorial.

## <a name="save-test-data-as-a-csv-file"></a>Salvar os dados de teste como um arquivo CSV

Para salvar o fluxo de dados **Resultado da Junção** em um arquivo .CSV, você deve alterar o script `BikeShare Data Prep.py`. Atualize o script Python usando o seguinte código:

```python
from azureml.dataprep.package import run

# dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
df = run('BikeShare Data Prep.dprep', dataflow_idx=2)

# Example file path: C:\\Users\\Jayaram\\BikeDataOut\\BikeShareTest.csv
df.to_csv('Your Test Data File Path here')
```

Selecione **Executar** na parte superior da tela. O script é enviado como um **Trabalho** no computador local. Quando o status do trabalho mudar para __Concluído__, o arquivo estará gravado no local especificado.

## <a name="substitute-data-sources"></a>Substituir as fontes de dados

Nas etapas anteriores, você usou as fontes de dados `201701-hubway-tripdata.csv` e `BostonWeather.csv` para preparar os dados de teste. Para usar o pacote com os outros arquivos de dados de trajeto, use as seguintes etapas:

1. Crie uma nova **Fonte de Dados** usando as etapas descritas anteriormente, com as seguintes alterações no processo:

    * __Seleção de Arquivo__: ao selecionar um arquivo, faça a seleção múltipla dos seis arquivos .CSV de dados de trajeto restantes.

        ![Carregar os seis arquivos restantes](media/tutorial-bikeshare-dataprep/selectsixfiles.png)

        > [!NOTE]
        > A entrada __+ 5__ indica que há cinco arquivos adicionais além do que está listado.

    * __Detalhes do Arquivo__: defina __Modo para Promover Cabeçalhos__ como **Todos os Arquivos Têm os Mesmos Cabeçalhos**. Esse valor indica que cada um dos arquivos contêm o mesmo cabeçalho.

        ![Seleção de detalhes do arquivo](media/tutorial-bikeshare-dataprep/headerfromeachfile.png) 

   Salve o nome desta fonte de dados, pois ela será usada em etapas posteriores.

2. Selecione o ícone de pasta para exibir os arquivos no projeto. Expanda o diretório __aml\_config__ e, em seguida, selecione o arquivo `local.runconfig`.

    ![Imagem do local de local.runconfig](media/tutorial-bikeshare-dataprep/localrunconfig.png) 

3. Adicione as seguintes linhas ao final do arquivo `local.runconfig` e, em seguida, selecione o ícone de disco para salvar o arquivo.

    ```yaml
    DataSourceSubstitutions:
      201701-hubway-tripdata.dsource: 201501-hubway-tripdata.dsource
    ```

    Essa alteração substitui a fonte de dados original pela que contém os seis arquivos de dados de trajeto.

## <a name="save-training-data-as-a-csv-file"></a>Salvar dados de treinamento como um arquivo CSV

Navegue até o arquivo Python `BikeShare Data Prep.py` que você editou anteriormente e forneça um Caminho de Arquivo diferente para salvar os Dados de Treinamento.

```python
from azureml.dataprep.package import run
# dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
df = run('BikeShare Data Prep.dprep', dataflow_idx=2)

# Example file path: C:\\Users\\Jayaram\\BikeDataOut\\BikeShareTrain.csv
df.to_csv('Your Training Data File Path here')
```

Para enviar um novo trabalho, use **Executar** no canto superior da página. Um **Trabalho** é enviado com a nova configuração. A saída desse trabalho é os Dados de Treinamento. Esses dados são criados usando as mesmas etapas de Preparação de Dados que você criou anteriormente. A conclusão do trabalho pode levar alguns minutos.

## <a name="next-steps"></a>Próximas etapas
Você concluiu o tutorial de preparação de dados de compartilhamento de bicicleta. Neste tutorial, você usou os serviços do Azure Machine Learning (versão prévia) para aprender como:
> [!div class="checklist"]
> * Preparar dados interativamente com a ferramenta de preparação de dados do Azure Machine Learning
> * Importar, transformar e criar um conjunto de dados de teste
> * Gerar um pacote de preparação de dados
> * Execute o pacote de preparação de dados com Python
> * Gerar um conjunto de dados de treinamento reutilizando o pacote de preparação de dados para arquivos de entrada adicionais

Em seguida, saiba mais sobre a preparação de dados:
> [!div class="nextstepaction"]
> [Guia do usuário de preparação de dados](data-prep-user-guide.md)
