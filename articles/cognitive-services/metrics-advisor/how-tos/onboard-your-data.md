---
title: Integre seu feed de dados ao assistente de métricas
titleSuffix: Azure Cognitive Services
description: Como começar a integrar seus feeds de dados ao assistente de métricas.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: fe3b87c733f54d8bd52c4d973977e3c8cbfefe19
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043182"
---
# <a name="how-to-onboard-your-metric-data-to-metrics-advisor"></a>Como: carregar seus dados de métrica para o assistente de métricas

Use este artigo para saber mais sobre a integração de seus dados ao assistente de métricas. 

## <a name="data-schema-requirements-and-configuration"></a>Requisitos e configuração do esquema de dados

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]

## <a name="avoid-loading-partial-data"></a>Evite carregar dados parciais

Os dados parciais são causados por inconsistências entre os dados armazenados no Metrics Advisor e a fonte de dados. Isso pode acontecer quando a fonte de dados é atualizada após a conclusão da obtenção de dados pelo Orientador de métricas. O assistente de métricas só efetua pull de dados de uma determinada fonte de dados uma vez.

Por exemplo, se uma métrica tiver sido integrada ao orientador de métricas para monitoramento. O assistente de métricas captura com êxito os dados de métrica no carimbo de data/hora A e executa a detecção de anomalias nele. No entanto, se os dados de métrica desse carimbo de data/hora específico tiverem sido atualizados após os dados serem ingeridos. Novo valor de dados não será recuperado.

Você pode tentar [aterrar](manage-data-feeds.md#backfill-your-data-feed) os dados históricos (descritos posteriormente) para atenuar inconsistências, mas isso não disparará novos alertas de anomalia, se alertas para esses pontos de tempo já tiverem sido disparados. Esse processo pode adicionar mais carga de trabalho ao sistema e não é automático.

Para evitar o carregamento de dados parciais, recomendamos duas abordagens:

* Gerar dados em uma transação:

    Verifique se os valores de métrica para todas as combinações de dimensão no mesmo carimbo de data/hora são armazenados na fonte de dados em uma transação. No exemplo acima, aguarde até que os dados de todas as fontes de dados estejam prontos e, em seguida, carregue-os no Orientador de métricas em uma transação. O revisor de métricas pode sondar o feed de dados regularmente até que os dados sejam recuperados com êxito (ou parcialmente).

* Atrase a ingestão de dados definindo um valor adequado para o parâmetro de **deslocamento de tempo de ingestão** :

    Defina o parâmetro de **deslocamento de tempo de ingestão** para o feed de dados para atrasar a ingestão até que os dados estejam totalmente preparados. Isso pode ser útil para algumas fontes de dados que não dão suporte a transações como o armazenamento de tabelas do Azure. Consulte [Configurações avançadas](manage-data-feeds.md#advanced-settings) para obter detalhes.

## <a name="add-a-data-feed-using-the-web-based-workspace"></a>Adicionar um feed de dados usando o espaço de trabalho baseado na Web

Depois de entrar no portal do supervisor de métrica e escolher seu espaço de trabalho, clique em **introdução**. Em seguida, na página principal do espaço de trabalho, clique em **Adicionar feed de dados** no menu à esquerda.

### <a name="add-connection-settings"></a>Adicionar configurações de conexão

Em seguida, você inserirá um conjunto de parâmetros para conectar sua fonte de dados de série temporal. 
* **Tipo de fonte**: o tipo de fonte de dados em que os dados de série temporal ficam armazenados.
* **Granularidade**: o intervalo entre pontos de dados consecutivos em seus dados de série temporal. Atualmente, o Orientador de métricas dá suporte a: anual, mensal, semanal, diário, por hora e personalizado. O intervalo mais baixo para o qual a opção de personalização oferece suporte é de 60 segundos.
  * **Segundos**: o número de segundos quando *granularityName* é definido para *Personalizar*.
* **Ingerir dados desde (UTC)**: a hora de início da linha de base para a ingestão de dados. *startOffsetInSeconds* é geralmente usado para adicionar um deslocamento para ajudar com a consistência dos dados.

Em seguida, você precisará especificar as informações de conexão para a fonte de dados e as consultas personalizadas usadas para converter os dados no esquema necessário. Para obter detalhes sobre os outros campos e como conectar diferentes tipos de fontes de dados, consulte [Adicionar feeds de dados de diferentes fontes de dados](../data-feeds-from-different-sources.md).

[!INCLUDE [query requirements](../includes/query-requirements.md)]

### <a name="verify-and-get-schema"></a>Verificar e obter esquema

Depois que a cadeia de conexão e a cadeia de caracteres de consulta forem definidas, selecione **verificar e obter esquema** para verificar a conexão e executar a consulta para obter o esquema de dados da fonte de dados. Normalmente, são necessários alguns segundos dependendo da conexão com a fonte de dados. Se ocorrer um erro nesta etapa, confirme se:

* A cadeia de conexão e a consulta estão corretas.
* Sua instância do Assistente de Métricas poderá se conectar à fonte de dados caso haja configurações de firewall.

### <a name="schema-configuration"></a>Configuração de esquema

Depois que o esquema de dados for carregado, selecione os campos apropriados.

Se o carimbo de data/hora de um ponto de dados for omitido, o supervisor de métricas usará o carimbo de data/hora quando o ponto de dados for ingerido Para cada feed de dados, você pode especificar no máximo uma coluna como um carimbo de data/hora. Se você receber uma mensagem informando que uma coluna não pode ser especificada como um carimbo de data/hora, verifique sua consulta ou fonte de dados e se há vários carimbos de data/hora no resultado da consulta – não apenas nos dados de visualização. Ao executar a ingestão de dados, o assistente de métricas só pode consumir apenas uma parte (por exemplo, um dia, uma hora, de acordo com a granularidade) de dados de série temporal da origem determinada a cada vez.

|Seleção  |Descrição  |Observações  |
|---------|---------|---------|
| **Nome de Exibição** | Nome a ser exibido em seu espaço de trabalho em vez do nome da coluna original. | |
|**Timestamp**     | O carimbo de data/hora de um ponto de dados. Se omitido, o Assistente de Métricas usará o carimbo de data/hora de quando o ponto de dados for ingerido. Para cada feed de dados, você pode especificar no máximo uma coluna como carimbo de data/hora.        | Opcional. Deve ser especificado com, no máximo, uma coluna. Se você receber uma **coluna não pode ser especificada como** erro de carimbo de hora, verifique sua consulta ou fonte de dados para obter carimbos de data/hora duplicados.      |
|**Medida**     |  Os valores numéricos no feed de dados. Para cada feed de dados, você pode especificar várias medidas, mas pelo menos uma coluna deve ser selecionada como medida.        | Deve ser especificado com, pelo menos, uma coluna.        |
|**Dimensão**     | Valores categóricos. Uma combinação de diferentes valores identifica uma série temporal de dimensão única em particular, por exemplo: país, idioma, locatário. Você pode selecionar zero ou mais colunas como dimensões. Observação: tenha cuidado ao selecionar uma coluna que não seja de cadeia de caracteres como uma dimensão. | Opcional.        |
|**Ignorar**     | Ignorar a coluna selecionada.        | Opcional. Consulte o texto abaixo.       |

Se você quiser ignorar colunas, é recomendável atualizar sua consulta ou fonte de dados para excluir essas colunas. Você também pode ignorar colunas usando **ignorar colunas** e, em seguida, **ignorar** nas colunas específicas. Se uma coluna deve ser uma dimensão e é definida erroneamente como *ignorada*, o Orientador de métrica pode acabar ingerindo dados parciais. Por exemplo, suponha que os dados da consulta sejam os seguintes:

| ID da linha | Timestamp | País | Idioma | Receita |
| --- | --- | --- | --- | --- |
| 1 | 2019/11/10 | China | ZH-CN | 10000 |
| 2 | 2019/11/10 | China | EN-US | 1000 |
| 3 | 2019/11/10 | EUA | ZH-CN | 12000 |
| 4 | 2019/11/11 | EUA | EN-US | 23000 |
| ... | ...| ... | ... | ... |

Se *Country* for uma dimensão e o *idioma* for definido como *ignorado*, a primeira e a segunda linhas terão as mesmas dimensões. O Orientador de métricas usará arbitrariamente um valor das duas linhas. O Orientador de métricas não agregará as linhas nesse caso.

### <a name="automatic-roll-up-settings"></a>Configurações de acúmulo automático

> [!IMPORTANT]
> Se você quiser habilitar a análise da causa raiz e outros recursos de diagnóstico, as **configurações de acúmulo automático** precisam ser configuradas. Uma vez habilitadas, as configurações de acumulação automática não podem ser alteradas.

O assistente de métricas pode executar automaticamente a agregação (por exemplo, SUM, MAX, MIN) em cada dimensão durante a ingestão e cria uma hierarquia que será usada na análise de caso raiz e outros recursos de diagnóstico. 

Considere os seguintes cenário:

* *Não preciso incluir a análise de acúmulo para meus dados.*

    Você não precisa usar a acumulação do revisor de métricas.

* *Meus dados já foram acumulados e o valor da dimensão é representado por: nulo ou vazio (padrão), somente nulo, outros.*

    Essa opção significa que o Orientador de métricas não precisa acumular os dados porque as linhas já estão somadas. Por exemplo, se você selecionar *somente nulo*, a segunda linha de dados no exemplo abaixo será vista como uma agregação de todos os países e idiomas *en-US*; a quarta linha de dados que tem um valor vazio para *Country* , no entanto, será vista como uma linha comum que pode indicar dados incompletos.
    
    | País | Idioma | Receita |
    |---------|----------|--------|
    | China   | ZH-CN    | 10000  |
    | NULO  | EN-US    | 999999 |
    | EUA      | EN-US    | 12000  |
    |         | EN-US    | 5.000   |

* *Preciso do assistente de métricas para acumular meus dados calculando Sum/Max/min/AVG/Count e representá-los por <some string>*

    Algumas fontes de dados como o Cosmos DB ou o armazenamento de BLOBs do Azure não dão suporte a determinados cálculos como *Group by* ou *Cube*. O assistente de métricas fornece a opção de acumulação para gerar automaticamente um cubo de dados durante a ingestão.
    Essa opção significa que você precisa do Orientador de métricas para calcular o acúmulo usando o algoritmo que você selecionou e usar a cadeia de caracteres especificada para representar o acúmulo no assistente de métricas. Isso não alterará nenhum dado na fonte de dados.
    Por exemplo, suponha que você tenha um conjunto de séries temporais que representa as métricas de vendas com a dimensão (país, região). Para um determinado carimbo de data/hora, ele pode ser semelhante ao seguinte:


    | País       | Região           | Sales |
    |---------------|------------------|-------|
    | Canada        | Alberta          | 100   |
    | Canada        | Colúmbia Britânica | 500   |
    | Estados Unidos | Montana          | 100   |


    Depois de habilitar o acúmulo automático com *sum*, o revisor de métricas calculará as combinações de dimensão e somará as métricas durante a ingestão de dados. O resultado pode ser:

    | País       | Região           | Sales |
    | ------------ | --------------- | ---- |
    | Canada        | Alberta          | 100   |
    | NULO          | Alberta          | 100   |
    | Canada        | Colúmbia Britânica | 500   |
    | NULO          | Colúmbia Britânica | 500   |
    | Estados Unidos | Montana          | 100   |
    | NULO          | Montana          | 100   |
    | NULO          | NULO             | 700   |
    | Canada        | NULO             | 600   |
    | Estados Unidos | NULO             | 100   |

    `(Country=Canada, Region=NULL, Sales=600)` significa que a soma das vendas no Canadá (todas as regiões) é 600.

    A seguir está a transformação na linguagem SQL.

    ```mssql
    SELECT
        dimension_1,
        dimension_2,
        ...
        dimension_n,
        sum (metrics_1) AS metrics_1,
        sum (metrics_2) AS metrics_2,
        ...
        sum (metrics_n) AS metrics_n
    FROM
        each_timestamp_data
    GROUP BY
        CUBE (dimension_1, dimension_2, ..., dimension_n);
    ```

    Considere o seguinte antes de usar o recurso de acumulação automática:

    * Se você quiser usar *sum* para agregar seus dados, verifique se suas métricas são aditivas em cada dimensão. Aqui estão alguns exemplos de métricas *não aditivas* :
      * Métricas baseadas em frações. Isso inclui a taxa, a porcentagem, etc. Por exemplo, você não deve adicionar a taxa de desemprego de cada Estado para calcular a taxa de desemprego de todo o país.
      * Sobreposição na dimensão. Por exemplo, você não deve adicionar o número de pessoas em cada esporte para calcular o número de pessoas que gostam de esportes, porque há uma sobreposição entre elas, uma pessoa pode gostar de vários esportes.
    * Para garantir a integridade do sistema inteiro, o tamanho do cubo é limitado. Atualmente, o limite é 1 milhão. Se os dados excederem esse limite, a ingestão falhará para esse carimbo de data/hora.

## <a name="advanced-settings"></a>Configurações avançadas

Há várias configurações avançadas para habilitar os dados ingeridos de forma personalizada, como especificar o deslocamento de ingestão ou a simultaneidade. Para obter mais informações, consulte a seção [Configurações avançadas](manage-data-feeds.md#advanced-settings) no artigo gerenciamento de feed de dados.

## <a name="specify-a-name-for-the-data-feed-and-check-the-ingestion-progress"></a>Especifique um nome para o feed de dados e verifique o andamento da ingestão
 
Dê um nome personalizado para o feed de dados, que será exibido no workspace. Em seguida, clique em **Enviar**. Na página detalhes do feed de dados, você pode usar a barra de progresso de ingestão para exibir informações de status.

:::image type="content" source="../media/datafeeds/ingestion-progress.png" alt-text="Barra de progresso da ingestão" lightbox="../media/datafeeds/ingestion-progress.png":::


Para verificar os detalhes da falha de ingestão: 

1. Clique em **Mostrar detalhes**.
2. Clique em **status** e escolha **falha** ou **erro**.
3. Focalize uma falha de ingestão e exiba a mensagem de detalhes que aparece.

:::image type="content" source="../media/datafeeds/check-failed-ingestion.png" alt-text="Barra de progresso da ingestão":::

Um status de *falha* indica que a ingestão dessa fonte de dados será repetida mais tarde.
Um status de *erro* indica que o supervisor de métrica não tentará novamente a fonte de dados. Para recarregar os dados, você precisa disparar um aterramento/recarregamento manualmente.

Você também pode recarregar o progresso de uma ingestão clicando em **Atualizar andamento**. Após a conclusão da ingestão de dados, você fica livre para clicar em métricas e verificar os resultados da detecção de anomalias.

## <a name="next-steps"></a>Próximas etapas
- [Gerenciar seus feeds de dados](manage-data-feeds.md)
- [Configurações para diferentes fontes de dados](../data-feeds-from-different-sources.md)
- [Configurar métricas e ajustar a configuração de detecção](configure-metrics.md)
