---
title: Usar dados de referência para pesquisas no Azure Stream Analytics
description: Este artigo descreve como usar dados de referência para pesquisar ou correlacionar dados no design de consulta de um trabalho do Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: ed50dfd7e3c423c1c26a7dc19ae60dcb319f1850
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621609"
---
# <a name="using-reference-data-for-lookups-in-stream-analytics"></a>Usar dados de referência para pesquisas no Stream Analytics

Dados de referência (também conhecido como uma tabela de pesquisa) são um conjunto de dados finito estático ou de alteração lenta por natureza, usado para executar uma pesquisa ou para ampliar seus fluxos de dados. Por exemplo, em um cenário de IoT, você pode armazenar metadados sobre sensores (que não são alterados com frequência) em dados de referência e uni-los a fluxos de dados de IoT em tempo real. O Azure Stream Analytics carrega dados de referência na memória para obter um processamento de fluxo de baixa latência. Para usar dados de referência no seu trabalho do Azure Stream Analytics geralmente é preciso usar uma [União de dados de referência](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics) em sua consulta. 

O Stream Analytics dá suporte ao Armazenamento de Blobs do Azure e ao Banco de Dados SQL do Azure como a camada de armazenamento para dados de referência. Você também pode transformar e/ou copiar os dados de referência para o Armazenamento de Blobs do Azure Data Factory para usar [qualquer número de armazenamentos de dados locais e baseados em nuvem](../data-factory/copy-activity-overview.md).

## <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

Dados de referência são modelados como uma sequência de blobs (definidos na configuração de entrada) em ordem crescente segundo a data/hora especificada no nome do blob. Ele **somente** dá suporte à adição ao final da sequência usando um valor de data/hora **maior** que aquele especificado pelo último blob na sequência.

### <a name="configure-blob-reference-data"></a>Configurar dados de referência de blob

Para configurar os dados de referência, você primeiro precisa criar uma entrada que seja do tipo **Dados de Referência**. A tabela a seguir explica cada propriedade que você precisará fornecer ao criar os entrada de dados de referência com sua descrição:

|**Nome da propriedade**  |**Descrição**  |
|---------|---------|
|Alias de entrada   | Um nome amigável que será usado na consulta de trabalho para fazer referência a essa entrada.   |
|Conta de armazenamento   | O nome da conta de armazenamento onde estão localizados os blobs. Se estiver na mesma assinatura que o trabalho do Stream Analytics, você pode selecioná-lo na lista suspensa.   |
|Chave da conta de armazenamento   | A chave secreta associada à conta de armazenamento. É preenchida automaticamente se a conta de armazenamento estiver na mesma assinatura que o trabalho do Stream Analytics.   |
|Contêiner de armazenamento   | Os contêineres fornecem um agrupamento lógico de blobs armazenados no serviço Blob do Microsoft Azure. Quando você carrega um blob no serviço Blob, você deve especificar um contêiner para aquele blob.   |
|Padrão de caminho   | O caminho usado para localizar seus blobs no contêiner especificado. No caminho, você pode optar por especificar uma ou mais instâncias das duas variáveis a seguir:<BR>{data}, {hora}<BR>Exemplo 1: products/{data}/{hora}/product-list.csv<BR>Exemplo 2: products/{data}/product-list.csv<BR>Exemplo 3: product-list.csv<BR><br> Se o blob não existir no caminho especificado, o trabalho do Stream Analytics aguardará indefinidamente que o blob fique disponível.   |
|Formato de data [opcional]   | Se você tiver usado {data} no padrão de caminho que você especificou, você pode selecionar o formato de data no qual os blobs são organizados na lista suspensa de formatos com suporte.<BR>Exemplo: AAAA/MM/DD, MM/DD/AAAA etc.   |
|Formato de hora [opcional]   | Se você tiver usado {hora} no padrão de caminho que você especificou, você pode selecionar o formato de hora no qual os blobs são organizados na lista suspensa de formatos com suporte.<BR>Exemplo: HH, HH/mm ou HH-mm.  |
|Formato de serialização do evento   | Para verificar se as consultas funcionam da maneira esperada, o Stream Analytics precisa saber qual formato de serialização você está usando para os fluxos de dados de entrada. Para dados de referência, os formatos com suporte são CSV e JSON.  |
|Codificando   | UTF-8 é o único formato de codificação com suporte no momento.  |

### <a name="static-reference-data"></a>Dados de referência estática

Se os dados de referência não forem alterados, em seguida, então o suporte para dados são habilitados especificando um caminho estático na configuração de entrada de referência estática. O Azure Stream Analytics pega o blob do caminho especificado. substituição de {data} e {hora} não é necessária. Como os dados de referência são imutáveis no Stream Analytics, não é recomendável substituir um blob de dados de referência estática.

### <a name="generate-reference-data-on-a-schedule"></a>Gerar dados de referência de acordo com um agendamento

Se os seus dados de referência são um conjunto de dados de alteração lenta, o suporte para atualização de dados de referência é habilitado especificando, na configuração de entrada, um padrão de caminho usando os tokens de substituição {date} e {time}. O Stream Analytics separa as definições de dados de referência baseadas nesse padrão de caminho. Por exemplo, um padrão de `sample/{date}/{time}/products.csv` com um formato de data de **"AAAA-MM-DD"** e um formato de hora de **"HH-mm"** instrui o Stream Analytics a captar o atualizou o blob `sample/2015-04-16/17-30/products.csv` às 17h30 do dia 16 de abril de 2015, fuso horário UTC.

O Azure Stream Analytics verifica automaticamente os blobs de dados de referência atualizados em um intervalo de um minuto. Se um blob com carimbo de hora 10:30:00 é carregado com um pequeno atraso (por exemplo, 10:30:30), você observará um pequeno atraso no trabalho de Stream Analytics fazer referência a esse blob. Para evitar esses cenários, é recomendável para carregar o blob anterior ao horário de destino em vigor (10: 30:00 neste exemplo) para permitir que o trabalho do Stream Analytics tempo suficiente para descobrir e carregá-lo na memória e executar operações. 

> [!NOTE]
> Atualmente os trabalhos do Stream Analytics procuram pela atualização de blob somente quando a hora do computador avança até a hora codificada no nome do blob. Por exemplo o trabalho irá procurar `sample/2015-04-16/17-30/products.csv` assim que possível, mas não antes das 17:30 UTC do dia 16 de abril de 2015. Ele *nunca* procurará um blob com uma hora codificada anterior ao último que foi descoberto.
> 
> Por exemplo, depois que o trabalho localiza o blob `sample/2015-04-16/17-30/products.csv` ele ignora todos os arquivos com uma data codificada anterior às 17:30 de 16 de abril de 2015 portanto, se um atrasado `sample/2015-04-16/17-25/products.csv` blob é criado no mesmo contêiner o trabalho não o usará.
> 
> Da mesma forma, se `sample/2015-04-16/17-30/products.csv` só é produzido às 22:03 do dia 16 de abril de 2015, mas nenhum blob com uma data anterior está presente no contêiner, o trabalho usa esse arquivo começando às 22:03 do dia 16 de abril de 2015 e usando os dados de referência anteriores.
> 
> Uma exceção a isso é iniciada quando o trabalho precisa reprocessar dados de volta no tempo ou quando o trabalho é o primeiro a iniciar. Na hora de início, o trabalho está procurando o blob mais recente produzido antes da hora especificada de início do trabalho. Isso é feito para garantir que haja um conjunto de dados de referência **não vazio** quando o trabalho é iniciado. Se um não for encontrado, o trabalho exibirá o seguinte diagnóstico: `Initializing input without a valid reference data blob for UTC time <start time>`.

[Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) pode ser utilizada para orquestrar a tarefa de criar os blobs atualizados exigidos pelo Stream Analytics para atualizar as definições de dados de referência. O Data Factory é um serviço de integração de dados baseado em nuvem que automatiza a movimentação e a transformação dos dados. O Data Factory dá suporte [à conexão de um grande número de armazenamentos de dados local baseados em nuvem](../data-factory/copy-activity-overview.md) e mover dados facilmente em um agendamento regular que você especificar. Para obter mais informações e orientações passo a passo sobre como configurar um pipeline de Data Factory para gerar dados de referência para o Stream Analytics que é atualizado em um cronograma predefinido, confira este [exemplo de GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs).

### <a name="tips-on-refreshing-blob-reference-data"></a>Dicas sobre como atualizar os dados de referência de blob

1. Não substitua os blobs de dados de referência uma vez que são imutáveis.
2. A maneira recomendada para atualizar os dados de referência é:
    * Use {date} / {hora} no padrão de caminho
    * Adicione um novo blob usando o mesmo padrão de contêiner e o caminho definido na entrada do trabalho
    * Use um valor de data/hora **maior** daquele especificado pelo último blob na sequência.
3. Blobs de dados de referência são **não** ordenados pela hora "Última modificação" do blob, mas apenas pela hora e pela data especificadas no nome do blob usando as substituições {date} e {time}.
3. Para evitar a necessidade de listar um grande número de blobs, considere excluir blobs muito antigos para os quais o processamento não será mais feito. Observe que o ASA talvez tenha que reprocessar uma pequena quantidade em alguns cenários, como um reinício.

## <a name="azure-sql-database"></a>Banco de Dados SQL do Azure

Os dados de referência do Banco de Dados SQL do Azure são recuperados pelo trabalho do Stream Analytics e armazenados como um instantâneo na memória para processamento. O instantâneo dos dados de referência também é armazenado em um contêiner em uma conta de armazenamento especificada nas definições de configuração. O contêiner é criado automaticamente quando o trabalho é iniciado. Se o trabalho for interrompido ou entrar em um estado de falha, os contêineres criados automaticamente serão excluídos quando o trabalho for reiniciado.  

Se os dados de referência forem um conjunto de dados com alteração lenta, você precisará atualizar periodicamente o instantâneo que é usado no trabalho. O Stream Analytics permite que você defina uma taxa de atualização ao configurar a conexão de entrada do Banco de Dados SQL do Azure. O tempo de execução do Stream Analytics consultará o Banco de Dados SQL do Azure no intervalo especificado pela taxa de atualização. A taxa de atualização mais rápida compatível é uma vez por minuto. Para cada atualização, o Stream Analytics armazena um novo instantâneo na conta de armazenamento fornecida.

O Stream Analytics oferece duas opções para consulta do Banco de Dados SQL do Azure. Uma consulta de instantâneo é obrigatória e precisa ser incluída em cada trabalho. O Stream Analytics executa a consulta de instantâneo periodicamente com base no intervalo de atualização e usa o resultado da consulta (o instantâneo) como o conjunto de dados de referência. A consulta de instantâneo deverá se ajustar à maioria dos cenários, mas se você enfrentar problemas de desempenho com conjuntos de dados grandes e taxas de atualização rápidas, use a opção de consulta delta. Consultas que levam mais de 60 segundos para retornar o conjunto de dados de referência resultará em um tempo limite.

Com a opção de consulta delta, o Stream Analytics executa a consulta de instantâneo inicialmente para obter um conjunto de dados de referência de linha de base. Em seguida, o Stream Analytics executa a consulta delta periodicamente com base no intervalo de atualização para recuperar alterações incrementais. Essas alterações incrementais são aplicadas continuamente ao conjunto de dados de referência para mantê-lo atualizado. O uso da consulta delta pode ajudar a reduzir o custo de armazenamento e as operações de E/S de rede.

### <a name="configure-sql-database-reference"></a>Configurar a referência do Banco de Dados SQL

Para configurar os dados de referência do Banco de Dados SQL, primeiro você precisará criar a entrada **Dados de Referência**. A tabela abaixo explica cada propriedade que você precisará fornecer ao criar a entrada de dados de referência com sua descrição. Para obter mais informações, confira [Usar dados de referência de um Banco de Dados SQL para um trabalho do Azure Stream Analytics](sql-reference-data.md).

|**Nome da propriedade**|**Descrição**  |
|---------|---------|
|Alias de entrada|Um nome amigável que será usado na consulta de trabalho para fazer referência a essa entrada.|
|Assinatura|Escolha sua assinatura|
|Banco de dados|O Banco de Dados SQL do Azure que contém os dados de referência.|
|Nome de usuário|O nome de usuário associado ao Banco de Dados SQL do Azure.|
|Senha|A senha associada ao Banco de Dados SQL do Azure.|
|Atualizar periodicamente|Essa opção permite que você escolha uma taxa de atualização. A escolha da opção "Ativado" permitirá que você especifique a taxa de atualização em DD:HH:MM.|
|Consulta de instantâneo|Essa é a opção de consulta padrão que recupera os dados de referência do Banco de Dados SQL.|
|Consulta delta|Para cenários avançados com conjuntos de dados grandes e uma taxa de atualização curta, opte por adicionar uma consulta delta.|

## <a name="size-limitation"></a>Limitação de tamanho

O Stream Analytics suporta dados de referência com  **tamanho máximo de 300 MB**. O limite de 300 MB de tamanho máximo de dados de referência é alcançável apenas com consultas simples. À medida que a complexidade da consulta aumenta para incluir o processamento com estado, como agregações de janela, junções temporais e funções analíticas temporais, é esperado que o tamanho máximo compatível dos dados de referência diminua. Se o Azure Stream Analytics não pode carregar os dados de referência e realize operações complexas, o trabalho executará fora da memória e falhará. Nesses casos, a % de SU Métrica de utilização alcançará 100%    

|**Número de unidades de streaming**  |**Tamanho máximo aprox. suportado (em MB)**  |
|---------|---------|
|1   |50   |
|3   |150   |
|6 e além   |300   |

O aumento do número de Unidades de Streaming contínuo de um trabalho além de 6 não aumenta o tamanho máximo suportado dos dados de referência.

O suporte para a compactação não está disponível para os dados de referência. 

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Início Rápido: Criar um trabalho do Stream Analytics usando o portal do Azure](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
