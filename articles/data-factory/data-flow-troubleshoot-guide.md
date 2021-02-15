---
title: Solucionar problemas de fluxos de dados de mapeamento
description: Saiba como solucionar problemas de fluxos de dados no Azure Data Factory.
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/11/2020
ms.openlocfilehash: a95cacafc5b1d00b1e4d04fd84cdda2de72b6a59
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522998"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Solucionar problemas de fluxos de dados de mapeamento em Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo explora métodos comuns de solução de problemas para mapeamento de fluxos de dados em Azure Data Factory.

## <a name="common-error-codes-and-messages"></a>Mensagens e códigos de erro comuns 

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Código de erro: DF-Executor-SourceInvalidPayload
- **Mensagem**: a execução de fluxo de dados de pipeline, depuração e visualização de dados falhou porque o contêiner não existe
- **Causas**: quando o conjunto de dados contém um contêiner que não existe no armazenamento
- **Recomendação**: verifique se o contêiner referenciado no conjunto de dados existe ou está acessível.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Código de erro: DF-Executor-SystemImplicitCartesian

- **Mensagem**: o produto cartesiano implícito para junção interna não é compatível, use a junção cruzada em vez disso. As colunas usadas na junção devem criar uma chave exclusiva para as linhas.
- **Causas**: o produto cartesiano implícito para junção interna entre planos lógicos não é compatível. Se as colunas forem usadas na junção, a chave exclusiva com pelo menos uma coluna de ambos os lados da relação será necessária.
- **Recomendação**: para junções não baseadas em igualdade, você precisa optar por uma junção cruzada personalizada.

### <a name="error-code-df-executor-systeminvalidjson"></a>Código de erro: DF-Executor-SystemInvalidJson

- **Mensagem**: erro de análise de JSON, multilinha ou codificação incompatível
- **Causas**: possíveis problemas com o arquivo JSON: codificação incompatível, bytes corrompidos ou uso de origem JSON como um único documento em várias linhas aninhadas
- **Recomendação**: verifique se a codificação do arquivo JSON é compatível. Na transformação da origem que está usando um conjunto de dados JSON, expanda 'Configurações de JSON' e ative 'Documento Único'.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Código de erro: DF-Executor-BroadcastTimeout

- **Mensagem**: erro de tempo limite de junção de difusão, verifique se o fluxo de difusão produz dados em 60 segundos em execuções de depuração e em 300 segundos em execuções de trabalho
- **Causas**: a difusão tem um tempo limite padrão de 60 segundos em execuções de depuração e 300 segundos em execuções de trabalho. O fluxo escolhido para difusão parece muito grande para produzir dados dentro desse limite.
- **Recomendação**: marque a guia Otimizar em suas transformações de fluxo de dados para Junção, Existe e Pesquisa. A opção padrão para Difusão é "Auto". Se "auto" estiver definido ou se você estiver configurando manualmente o lado esquerdo ou direito para difundir em "fixed", poderá definir uma configuração de Azure Integration Runtime maior ou desativar a difusão. A abordagem recomendada para o melhor desempenho em fluxos de dados é permitir que o Spark transmita usando "Auto" e use um Azure IR otimizado para memória. Se você estiver executando o fluxo de dados em uma execução de teste de depuração de uma execução de pipeline de depuração, poderá encontrar essa condição com mais frequência. Isso ocorre porque o ADF acelera o tempo limite de difusão para 60 segundos a fim de manter uma experiência de depuração mais rápida. Se você quiser estender isso para o tempo limite de 300 segundos de uma execução disparada, você pode usar a opção debug > use Activity Runtime para utilizar a Azure IR definida em sua atividade executar pipeline de fluxo de dados.

- **Mensagem**: erro de tempo limite de junção de difusão, você pode escolher ' Desativado ' da opção de difusão na transformação Join/Exists/Lookup para evitar esse problema. Se você pretende difundir a opção de junção para melhorar o desempenho, verifique se o fluxo de difusão pode produzir dados em 60 segundos em execuções de depuração e 300 segundos em execuções de trabalho.
- **Causas**: a difusão tem um tempo limite padrão de 60 segundos em execuções de depuração e de 300 segundos em execuções de trabalho. Na junção de difusão, o fluxo escolhido para difusão parece muito grande para produzir dados dentro desse limite. Se uma junção de difusão não for usada, a difusão padrão feita pelo fluxo de trabalho poderá atingir o mesmo limite
- **Recomendação**: Desative a opção de difusão ou Evite transmitir grandes fluxos de dados em que o processamento pode levar mais de 60 segundos. Em vez disso, escolha um fluxo menor para difusão. Grandes tabelas do SQL/DW e arquivos de origem normalmente são candidatos inválidos. Na ausência de uma junção de difusão, use um cluster maior se o erro ocorrer.

### <a name="error-code-df-executor-conversion"></a>Código de erro: DF-Executor-Conversion

- **Mensagem**: falha ao converter em uma data ou hora devido a um caractere inválido
- **Causas**: os dados não estão no formato esperado
- **Recomendação**: usar o tipo de dados correto

### <a name="error-code-df-executor-invalidcolumn"></a>Código de erro: DF-Executor-InvalidColumn
- **Mensagem**: o nome da coluna precisa ser especificado na consulta, defina um alias se você estiver usando uma função SQL
- **Causas**: nenhum nome de coluna foi especificado
- **Recomendação**: defina um alias se você estiver usando uma função SQL como min()/max() etc.

### <a name="error-code-df-executor-drivererror"></a>Código de erro: DF-executor-DriverError
- **Mensagem**: INT96 é um tipo de carimbo de data/hora herdado que não tem suporte pelo fluxo de mensagens do ADF Considere atualizar o tipo de coluna para os tipos mais recentes.
- **Causas**: erro de driver
- **Recomendação**: INT96 é o tipo de carimbo de data/hora herdado, que não tem suporte do fluxo de texto do ADF. Considere atualizar o tipo de coluna para os tipos mais recentes.

### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Código de erro: DF-executor-BlockCountExceedsLimitError
- **Mensagem**: a contagem de blocos não confirmados não pode exceder o limite máximo de 100.000 blocos. Verifique a configuração do blob.
- **Causas**: pode haver no máximo 100.000 blocos não confirmados em um blob.
- **Recomendação**: entre em contato com a equipe de produto da Microsoft sobre esse problema para obter mais detalhes

### <a name="error-code-df-executor-partitiondirectoryerror"></a>Código de erro: DF-executor-PartitionDirectoryError
- **Mensagem**: o caminho de origem especificado tem vários diretórios particionados (por exemplo, <Source Path> /<diretório raiz de partição 1>/a = 10/b = 20, <Source Path> /<diretório raiz da partição 2>/c = 10/d = 30) ou diretório particionado com outro arquivo ou diretório não particionado (por exemplo <Source Path> /diretório raiz da partição de <1>/a = 10/b = 20, <Source Path> /diretório 2/arquivo1), remova o diretório raiz da partição do caminho de origem e leia-o por meio de transformação de origem separada.
- **Causas**: o caminho de origem tem vários diretórios particionados ou diretório particionado com outro arquivo ou diretório não particionado.
- **Recomendação**: Remova o diretório raiz particionado do caminho de origem e leia-o por meio de transformação de origem separada.

### <a name="error-code-df-executor-outofmemoryerror"></a>Código de erro: DF-executor-OutOfMemoryError
- **Mensagem**: o cluster encontrou um problema de memória insuficiente durante a execução. tente novamente usando um Integration Runtime com maior contagem de núcleos e/ou tipo de computação com otimização de memória
- **Causas**: o cluster está ficando sem memória
- **Recomendação**: os clusters de depuração destinam-se a fins de desenvolvimento. Aproveite a amostragem de dados, o tipo de computação apropriado e o tamanho para executar a carga. Consulte o [Guia de desempenho do fluxo de dados de mapeamento](concepts-data-flow-performance.md) para ajuste para obter o melhor desempenho.

### <a name="error-code-df-executor-invalidtype"></a>Código de erro: DF-executor-invalidatype
- **Mensagem**: Verifique se o tipo de parâmetro corresponde ao tipo de valor passado. A passagem de parâmetros float de pipelines não tem suporte no momento.
- **Causas**: tipos de dados incompatíveis entre o tipo declarado e o valor real do parâmetro
- **Recomendação**: Verifique se os valores de parâmetro passados para um fluxo de dados correspondem ao tipo declarado.

### <a name="error-code-df-executor-columnunavailable"></a>Código de erro: DF-executor-ColumnUnavailable
- **Mensagem**: o nome da coluna usado na expressão está indisponível ou é inválido
- **Causas**: nome de coluna inválido ou indisponível usado em expressões
- **Recomendação**: Verifique os nomes de coluna usados em expressões

### <a name="error-code-df-executor-parseerror"></a>Código de erro: DF-executor-ParseError
- **Mensagem**: a expressão não pode ser analisada
- **Causas**: a expressão está analisando erros devido à formatação
- **Recomendação**: Verifique a formatação na expressão.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Código de erro: DF-Executor-SystemImplicitCartesian
- **Mensagem**: o produto cartesiano implícito para junção interna não é compatível, use a junção cruzada em vez disso. As colunas usadas na junção devem criar uma chave exclusiva para as linhas.
- **Causas**: o produto cartesiano implícito para junção interna entre planos lógicos não é compatível. Se as colunas usadas na junção criarem a chave exclusiva
- **Recomendação**: para junções não baseadas em igualdade, você precisa optar por junção cruzada.

### <a name="error-code-df-executor-systeminvalidjson"></a>Código de erro: DF-Executor-SystemInvalidJson
- **Mensagem**: erro de análise de JSON, multilinha ou codificação incompatível
- **Causas**: possíveis problemas com o arquivo JSON: codificação incompatível, bytes corrompidos ou uso de origem JSON como um único documento em várias linhas aninhadas
- **Recomendação**: verifique se a codificação do arquivo JSON é compatível. Na transformação da origem que está usando um conjunto de dados JSON, expanda 'Configurações de JSON' e ative 'Documento Único'.



### <a name="error-code-df-executor-conversion"></a>Código de erro: DF-Executor-Conversion
- **Mensagem**: falha ao converter em uma data ou hora devido a um caractere inválido
- **Causas**: os dados não estão no formato esperado
- **Recomendação**: Use o tipo de dados correto.


### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Código de erro: DF-executor-BlockCountExceedsLimitError
- **Mensagem**: a contagem de blocos não confirmados não pode exceder o limite máximo de 100.000 blocos. Verifique a configuração do blob.
- **Causas**: pode haver no máximo 100.000 blocos não confirmados em um blob.
- **Recomendação**: entre em contato com a equipe de produto da Microsoft sobre esse problema para obter mais detalhes

### <a name="error-code-df-executor-partitiondirectoryerror"></a>Código de erro: DF-executor-PartitionDirectoryError
- **Mensagem**: o caminho de origem especificado tem vários diretórios particionados (por exemplo, *<Source Path> /<diretório raiz de partição 1>/a = 10/b = 20 <Source Path> ,/<diretório raiz da partição 2>/c = 10/d = 30*) ou diretório particionado com outro arquivo ou diretório não particionado (por exemplo, *<Source Path> /<diretório raiz de partição 1>/A = 10/b = 20, <Source Path> /diretório 2/arquivo1*), remova o diretório raiz da partição do caminho de origem e leia-o por meio de transformação de origem separada.
- **Causas**: o caminho de origem tem vários diretórios particionados ou diretório particionado com outro arquivo ou diretório não particionado.
- **Recomendação**: Remova o diretório raiz particionado do caminho de origem e leia-o por meio de transformação de origem separada.

### <a name="error-code-getcommand-outputasync-failed"></a>Código de erro: falha de GetCommand OutputAsync
- **Mensagem**: durante a depuração do Fluxo de Dados e a visualização dos dados: GetCommand OutputAsync falhou com...
- **Causas**: este é um erro de serviço de back-end. Você pode repetir a operação e também reiniciar a sessão de depuração.
- **Recomendação**: se tentar novamente e reiniciar não resolver o problema, entre em contato com o atendimento ao cliente. 

### <a name="error-code-df-executor-outofmemoryerror"></a>Código de erro: DF-executor-OutOfMemoryError
 
- **Mensagem**: o cluster encontrou um problema de memória insuficiente durante a execução. tente novamente usando um Integration Runtime com maior contagem de núcleos e/ou tipo de computação com otimização de memória
- **Causas**: o cluster está ficando sem memória.
- **Recomendação**: os clusters de depuração destinam-se a fins de desenvolvimento. Aproveite a amostragem de dados tipo e tamanho de computação apropriados para executar a carga. Consulte o [Guia de desempenho de fluxo](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-performance) de os para ajustar os fluxos de fluxo de os para obter o melhor desempenho.

### <a name="error-code-df-executor-illegalargument"></a>Código de erro: DF-executor-illegalArgument
- **Mensagem**: Verifique se a chave de acesso no serviço vinculado está correta.
- **Causas**: o nome da conta ou a chave de acesso está incorreta.
- **Recomendação**: forneça o nome da conta ou a chave de acesso correta.

- **Mensagem**: Verifique se a chave de acesso em seu serviço vinculado está correta
- **Causas**: nome da conta ou chave de acesso incorretos
- **Recomendação**: Verifique se o nome da conta ou a chave de acesso especificada no serviço vinculado está correta. 

### <a name="error-code-df-executor-invalidtype"></a>Código de erro: DF-executor-invalidatype
- **Mensagem**: Verifique se o tipo de parâmetro corresponde ao tipo de valor passado. A passagem de parâmetros float de pipelines não tem suporte no momento.
- **Causas**: tipos de dados incompatíveis entre o tipo declarado e o valor real do parâmetro
- **Recomendação**: forneça os tipos de dados corretos.

### <a name="error-code-df-executor-columnunavailable"></a>Código de erro: DF-executor-ColumnUnavailable
- **Mensagem**: o nome da coluna usado na expressão está indisponível ou é inválido.
- **Causas**: nome de coluna inválido ou indisponível é usado em expressões.
- **Recomendação**: Verifique os nomes de coluna usados em expressões.


### <a name="error-code-df-executor-parseerror"></a>Código de erro: DF-executor-ParseError
- **Mensagem**: a expressão não pode ser analisada.
- **Causas**: a expressão está analisando erros devido à formatação.
- **Recomendação**: Verifique a formatação na expressão.


 ### <a name="error-code-df-executor-outofdiskspaceerror"></a>Código de erro: DF-executor-OutOfDiskSpaceError
- **Mensagem**: erro interno do servidor
- **Causas**: o cluster está ficando sem espaço em disco.
- **Recomendação**: repita o pipeline. Se o problema persistir, entre em contato com o atendimento ao cliente.


 ### <a name="error-code-df-executor-storeisnotdefined"></a>Código de erro: DF-executor-StoreIsNotDefined
- **Mensagem**: a configuração do repositório não está definida. Esse erro é potencialmente causado por uma atribuição de parâmetro inválida no pipeline.
- **Causas**: indeterminadas
- **Recomendação**: Verifique a atribuição de valor de parâmetro no pipeline. A expressão de parâmetro pode conter caracteres inválidos.

### <a name="error-code-df-excel-invalidconfiguration"></a>Código de erro: DF-Excel-InvalidConfiguration
- **Mensagem**: o nome ou índice da planilha do Excel é necessário.
- **Causas**: indeterminadas
- **Recomendação**: Verifique o valor do parâmetro e especifique o nome ou o índice da planilha para ler os dados do Excel.

- **Mensagem**: o nome e o índice da planilha do Excel não podem existir ao mesmo tempo.
- **Causas**: indeterminadas
- **Recomendação**: Verifique o valor do parâmetro e especifique o nome ou o índice da planilha para ler os dados do Excel.

- **Mensagem**: intervalo inválido fornecido.
- **Causas**: indeterminadas
- **Recomendação**: Verifique o valor do parâmetro e especifique um intervalo válido por referência: [Propriedades do Excel](https://docs.microsoft.com/azure/data-factory/format-excel#dataset-properties).

- **Mensagem**: o arquivo do Excel inválido é fornecido enquanto houver suporte apenas para. xlsx e. xls
- **Causas**: indeterminadas
- **Recomendação**: Verifique se a extensão de arquivo do Excel é. xlsx ou. xls.


 ### <a name="error-code-df-excel-invaliddata"></a>Código de erro: DF-Excel-InvalidData
- **Mensagem**: a planilha do Excel não existe.
- **Causas**: indeterminadas
- **Recomendação**: Verifique o valor do parâmetro e especifique um índice ou nome de planilha válido para ler os dados do Excel.

- **Mensagem**: não há suporte para a leitura de arquivos do Excel com esquema diferente agora.
- **Causas**: indeterminadas
- **Recomendação**: Use o arquivo correto do Excel.

- **Mensagem**: não há suporte para o tipo de dados.
- **Causas**: indeterminadas
- **Recomendação**: Use os tipos de dados Right de arquivo do Excel.

### <a name="error-code-4502"></a>Código de erro: 4502
- **Mensagem**: há execuções MappingDataflow simultâneas substanciais que estão causando falhas devido à limitação em Integration Runtime.
- **Causas**: muitas execuções de atividade de Dataflow estão acontecendo simultaneamente no Integration Runtime. Saiba mais sobre os [limites de Azure data Factory](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#data-factory-limits).
- **Recomendação**: caso você esteja procurando executar mais atividades de fluxo de dados em paralelo, distribua-as em vários tempos de execução de integração.


### <a name="error-code-invalidtemplate"></a>Código de erro: invalidatemplate
- **Mensagem**: a expressão de pipeline não pode ser avaliada.
- **Causas**: a expressão de pipeline passada na atividade de Dataflow não está sendo processada corretamente devido a um erro de sintaxe.
- **Recomendação**: Verifique sua atividade no monitoramento de atividades para verificar a expressão.

### <a name="error-code-2011"></a>Código de erro: 2011
- **Mensagem**: a atividade estava em execução no Azure Integration Runtime e falhou ao descriptografar a credencial do armazenamento de dados ou da computação conectada por meio de um Integration Runtime hospedado internamente. Verifique a configuração dos serviços vinculados associados a essa atividade e certifique-se de usar o tipo de tempo de execução de integração adequado.
- **Causas**: o fluxo de dados não oferece suporte aos serviços vinculados com o tempo de execução de integração auto-hospedado.
- **Recomendação**: Configure o fluxo de dados para executar no Integration Runtime com "rede virtual gerenciada".

## <a name="miscellaneous-troubleshooting-tips"></a>Diversas dicas de solução de problemas
- **Problema**: clique em exceção inesperada e a execução falhou
    - **Mensagem**: durante a execução da atividade do Fluxo de Dados: ocorreu uma exceção inesperada e a execução falhou.
    - **Causas**: este é um erro de serviço de back-end. Você pode repetir a operação e também reiniciar a sessão de depuração.
    - **Recomendação**: se tentar novamente e reiniciar não resolver o problema, entre em contato com o atendimento ao cliente.

-  **Problema**: visualização de dados de depuração não há dados de saída em junção
    - **Mensagem**: há um grande número de valores nulos ou valores ausentes que podem ser causados por ter poucas linhas amostradas. Tente atualizar o limite de linha de depuração e atualizar os dados.
    - **Causas**: a condição de junção não correspondeu a nenhuma linha ou resultou em um número alto de nulos durante a visualização de dados.
    - **Recomendação**: Vá para configurações de depuração e aumente o número de linhas no limite de linha de origem. Verifique se você selecionou um Azure IR com um cluster de fluxo de dados grande o suficiente para lidar com mais dados.
    
- **Problema**: erro de validação na origem com arquivos CSV de várias linhas 
    - **Mensagem**: você pode ver uma das seguintes mensagens de erro:
        - A última coluna é nula ou está ausente.
        - A validação de esquema na origem falha.
        - A importação de esquema não é exibida corretamente na UX e a última coluna tem um caractere de nova linha no nome.
    - **Causas**: no fluxo de dados de mapeamento, atualmente, a origem do CSV de várias linhas não funciona com o delimitador de linha \r\n como. Às vezes, as linhas extras em carro retornam valores de origem de quebra. 
    - **Recomendação**: gere o arquivo na fonte com \n como delimitador de linha em vez de \r\n. Ou use a atividade de cópia para converter o arquivo CSV com \r\n para \n como um delimitador de linha.

## <a name="general-troubleshooting-guidance"></a>Diretrizes gerais de solução de problemas
1. Verifique o status das suas conexões de conjunto de dados. Em cada transformação de origem e coletor, visite o serviço vinculado para cada conjunto de dados que você estiver usando e teste as conexões.
2. Verifique o status das conexões de arquivo e de tabela do designer de fluxo de dados. Ative a Depuração e clique em Visualização de Dados em suas Transformações de origem para garantir que você possa acessar seus dados.
3. Se tudo estiver correto na visualização de dados, vá para o Designer de pipeline e coloque seu fluxo de dados em uma atividade de pipeline. Depure o pipeline para um teste de ponta a ponta.

## <a name="next-steps"></a>Próximas etapas

Para obter mais ajuda com a solução de problemas, experimente os seguintes recursos:

*  [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Solicitações de recurso do Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Fórum do Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre o Data Factory](https://twitter.com/hashtag/DataFactory)
