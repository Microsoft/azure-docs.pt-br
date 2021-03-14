---
title: Solucionar problemas de fluxos de dados de mapeamento
description: Saiba como solucionar problemas de fluxo de dados no Azure Data Factory.
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/11/2020
ms.openlocfilehash: f8a852a8c4197169061a9c7633f4f363ad057337
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102505793"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Solucionar problemas de fluxos de dados de mapeamento em Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo explora métodos comuns de solução de problemas para mapeamento de fluxos de dados em Azure Data Factory.

## <a name="common-error-codes-and-messages"></a>Mensagens e códigos de erro comuns 

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Código de erro: DF-Executor-SourceInvalidPayload
- **Mensagem**: a execução de fluxo de dados de pipeline, depuração e visualização de dados falhou porque o contêiner não existe
- **Causa**: um DataSet contém um contêiner que não existe no armazenamento.
- **Recomendação**: Certifique-se de que o contêiner referenciado no conjunto de seus conjuntos de seus exista e possa ser acessado.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Código de erro: DF-Executor-SystemImplicitCartesian

- **Mensagem**: o produto cartesiano implícito para junção interna não é compatível, use a junção cruzada em vez disso. As colunas usadas na junção devem criar uma chave exclusiva para as linhas.
- **Causa**: não há suporte para produtos cartesianos implícitos para junções internas entre planos lógicos. Se você estiver usando colunas na junção, crie uma chave exclusiva com pelo menos uma coluna de ambos os lados da relação.
- **Recomendação**: para junções não baseadas em igualdade, use junção cruzada personalizada.

### <a name="error-code-df-executor-systeminvalidjson"></a>Código de erro: DF-Executor-SystemInvalidJson

- **Mensagem**: erro de análise de JSON, multilinha ou codificação incompatível
- **Causa**: possíveis problemas com o arquivo JSON: codificação sem suporte, bytes corrompidos ou uso de origem JSON como um único documento em várias linhas aninhadas.
- **Recomendação**: Verifique se há suporte para a codificação do arquivo JSON. Na transformação origem que está usando um conjunto de uma JSON, expanda **configurações JSON** e ative um **único documento**.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Código de erro: DF-Executor-BroadcastTimeout

- **Mensagem**: erro de tempo limite de junção de difusão, verifique se o fluxo de difusão produz dados em 60 segundos em execuções de depuração e em 300 segundos em execuções de trabalho
- **Causa**: a difusão tem um tempo limite padrão de 60 segundos em execuções de depuração e 300 segundos em execuções de trabalho. O fluxo escolhido para difusão é muito grande para produzir dados dentro desse limite.
- **Recomendação**: marque a guia **otimizar** em suas transformações de fluxo de dados para Join, Exists e Lookup. A opção padrão para difusão é **automática**. Se **auto** estiver definido, ou se você estiver definindo manualmente o lado esquerdo ou direito para difundir em **Fixed**, você poderá definir uma configuração maior de ir (Integration Runtime) do Azure ou desativar a difusão. Para obter o melhor desempenho em fluxos de dados, recomendamos que você permita que o Spark transmita usando **auto** e use um Azure ir com otimização de memória. 
 
  Se você estiver executando o fluxo de dados em uma execução de teste de depuração de uma execução de pipeline de depuração, poderá encontrar essa condição com mais frequência. Isso ocorre porque Azure Data Factory limita o tempo limite de difusão a 60 segundos para manter uma experiência de depuração mais rápida. Você pode estender o tempo limite para o tempo limite de 300 segundos de uma execução disparada. Para fazer isso, você pode usar a opção de tempo de execução de atividade de uso de **depuração**  >   para usar o Azure ir definido em sua atividade executar pipeline de fluxo de dados.

- **Mensagem**: erro de tempo limite de junção de difusão, você pode escolher ' Desativado ' da opção de difusão na transformação Join/Exists/Lookup para evitar esse problema. Se você pretende difundir a opção de junção para melhorar o desempenho, verifique se o fluxo de difusão pode produzir dados em 60 segundos em execuções de depuração e 300 segundos em execuções de trabalho.
- **Causa**: a difusão tem um tempo limite padrão de 60 segundos em execuções de depuração e 300 segundos em execuções de trabalho. Na junção de difusão, o fluxo escolhido para a transmissão é muito grande para produzir dados dentro desse limite. Se uma junção de difusão não for usada, a difusão padrão por fluxo de transmissão poderá alcançar o mesmo limite.
- **Recomendação**: Desative a opção de difusão ou Evite transmitir grandes fluxos de dados para os quais o processamento pode levar mais de 60 segundos. Escolha um fluxo menor para difundir. Grandes tabelas de SQL Data Warehouse do Azure e arquivos de origem não são normalmente boas opções. Na ausência de uma junção de difusão, use um cluster maior se esse erro ocorrer.

### <a name="error-code-df-executor-conversion"></a>Código de erro: DF-Executor-Conversion

- **Mensagem**: falha ao converter em uma data ou hora devido a um caractere inválido
- **Causa**: os dados não estão no formato esperado.
- **Recomendação**: Use o tipo de dados correto.

### <a name="error-code-df-executor-invalidcolumn"></a>Código de erro: DF-Executor-InvalidColumn
- **Mensagem**: o nome da coluna precisa ser especificado na consulta, defina um alias se você estiver usando uma função SQL
- **Causa**: nenhum nome de coluna foi especificado.
- **Recomendação**: defina um alias se você estiver usando uma função SQL como min () ou Max ().

### <a name="error-code-df-executor-drivererror"></a>Código de erro: DF-executor-DriverError
- **Mensagem**: INT96 é um tipo de carimbo de data/hora herdado que não tem suporte pelo fluxo de mensagens do ADF Considere atualizar o tipo de coluna para os tipos mais recentes.
- **Causa**: erro de driver.
- **Recomendação**: INT96 é um tipo de carimbo de data/hora herdado que não tem suporte pelo fluxo de dados do Azure data Factory. Considere atualizar o tipo de coluna para o tipo mais recente.

### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Código de erro: DF-executor-BlockCountExceedsLimitError
- **Mensagem**: a contagem de blocos não confirmados não pode exceder o limite máximo de 100.000 blocos. Verifique a configuração do blob.
- **Causa**: o número máximo de blocos não confirmados em um blob é 100.000.
- **Recomendação**: entre em contato com a equipe de produtos da Microsoft para obter mais detalhes sobre esse problema.

### <a name="error-code-df-executor-partitiondirectoryerror"></a>Código de erro: DF-executor-PartitionDirectoryError
- **Mensagem**: o caminho de origem especificado tem vários diretórios particionados (por exemplo, <Source Path> /<diretório raiz de partição 1>/a = 10/b = 20, <Source Path> /<diretório raiz da partição 2>/c = 10/d = 30) ou diretório particionado com outro arquivo ou diretório não particionado (por exemplo <Source Path> /diretório raiz da partição de <1>/a = 10/b = 20, <Source Path> /diretório 2/arquivo1), remova o diretório raiz da partição do caminho de origem e leia-o por meio de transformação de origem separada.
- **Causa**: o caminho de origem tem vários diretórios particionados ou um diretório particionado que tem outro arquivo ou diretório não particionado.
- **Recomendação**: Remova o diretório raiz particionado do caminho de origem e leia-o por meio de transformação de origem separada.

### <a name="error-code-df-executor-invalidtype"></a>Código de erro: DF-executor-invalidatype
- **Mensagem**: Verifique se o tipo de parâmetro corresponde ao tipo de valor passado. A passagem de parâmetros float de pipelines não tem suporte no momento.
- **Causa**: o tipo de dados para o tipo declarado não é compatível com o valor do parâmetro real.
- **Recomendação**: Verifique se os valores de parâmetro passados para o fluxo de dados correspondem ao tipo declarado.

### <a name="error-code-df-executor-columnunavailable"></a>Código de erro: DF-executor-ColumnUnavailable
- **Mensagem**: o nome da coluna usado na expressão está indisponível ou é inválido
- **Causa**: um nome de coluna inválido ou indisponível usado em uma expressão.
- **Recomendação**: Verifique os nomes de coluna em expressões.

### <a name="error-code-df-executor-parseerror"></a>Código de erro: DF-executor-ParseError
- **Mensagem**: a expressão não pode ser analisada
- **Causa**: uma expressão gerou erros de análise devido à formatação incorreta.
- **Recomendação**: Verifique a formatação na expressão.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Código de erro: DF-Executor-SystemImplicitCartesian
- **Mensagem**: o produto cartesiano implícito para junção interna não é compatível, use a junção cruzada em vez disso. As colunas usadas na junção devem criar uma chave exclusiva para as linhas.
- **Causa**: não há suporte para produtos cartesianos implícitos para junções internas entre planos lógicos. Se você estiver usando colunas na junção, crie uma chave exclusiva.
- **Recomendação**: para junções não baseadas em igualdade, use junção cruzada.

### <a name="error-code-df-executor-systeminvalidjson"></a>Código de erro: DF-Executor-SystemInvalidJson
- **Mensagem**: erro de análise de JSON, multilinha ou codificação incompatível
- **Causa**: possíveis problemas com o arquivo JSON: codificação sem suporte, bytes corrompidos ou uso de origem JSON como um único documento em várias linhas aninhadas.
- **Recomendação**: Verifique se há suporte para a codificação do arquivo JSON. Na transformação origem que está usando um conjunto de uma JSON, expanda **configurações JSON** e ative um **único documento**.



### <a name="error-code-df-executor-conversion"></a>Código de erro: DF-Executor-Conversion
- **Mensagem**: falha ao converter em uma data ou hora devido a um caractere inválido
- **Causa**: os dados não estão no formato esperado.
- **Recomendação**: Use o tipo de dados correto.


### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Código de erro: DF-executor-BlockCountExceedsLimitError
- **Mensagem**: a contagem de blocos não confirmados não pode exceder o limite máximo de 100.000 blocos. Verifique a configuração do blob.
- **Causa**: o número máximo de blocos não confirmados em um blob é 100.000.
- **Recomendação**: entre em contato com a equipe de produtos da Microsoft para obter mais detalhes sobre esse problema.

### <a name="error-code-df-executor-partitiondirectoryerror"></a>Código de erro: DF-executor-PartitionDirectoryError
- **Mensagem**: o caminho de origem especificado tem vários diretórios particionados (por exemplo, *<Source Path> /<diretório raiz de partição 1>/a = 10/b = 20 <Source Path> ,/<diretório raiz da partição 2>/c = 10/d = 30*) ou diretório particionado com outro arquivo ou diretório não particionado (por exemplo, *<Source Path> /<diretório raiz de partição 1>/A = 10/b = 20, <Source Path> /diretório 2/arquivo1*), remova o diretório raiz da partição do caminho de origem e leia-o por meio de transformação de origem separada.
- **Causa**: o caminho de origem tem vários diretórios particionados ou um diretório particionado que tem outro arquivo ou diretório não particionado. 
- **Recomendação**: Remova o diretório raiz particionado do caminho de origem e leia-o por meio de transformação de origem separada.

### <a name="error-code-getcommand-outputasync-failed"></a>Código de erro: falha de GetCommand OutputAsync
- **Mensagem**: durante a depuração do Fluxo de Dados e a visualização dos dados: GetCommand OutputAsync falhou com...
- **Causa**: esse erro é um erro de serviço de back-end. 
- **Recomendação**: repita a operação e reinicie a sessão de depuração. Se a repetição e a reinicialização não resolverem o problema, contate o atendimento ao cliente. 

### <a name="error-code-df-executor-outofmemoryerror"></a>Código de erro: DF-executor-OutOfMemoryError
 
- **Mensagem**: o cluster encontrou um problema de memória insuficiente durante a execução. tente novamente usando um Integration Runtime com maior contagem de núcleos e/ou tipo de computação com otimização de memória
- **Causa**: o cluster está ficando sem memória.
- **Recomendação**: os clusters de depuração destinam-se ao desenvolvimento. Use a amostragem de dados e um tipo e tamanho de computação apropriados para executar a carga. Para obter dicas de desempenho, consulte [mapeando o guia de desempenho do fluxo de dados](concepts-data-flow-performance.md).

### <a name="error-code-df-executor-illegalargument"></a>Código de erro: DF-executor-illegalArgument

- **Mensagem**: Verifique se a chave de acesso em seu serviço vinculado está correta
- **Causa**: o nome da conta ou a chave de acesso está incorreta.
- **Recomendação**: Verifique se o nome da conta ou a chave de acesso especificada no serviço vinculado está correta. 

### <a name="error-code-df-executor-invalidtype"></a>Código de erro: DF-executor-invalidatype
- **Mensagem**: Verifique se o tipo de parâmetro corresponde ao tipo de valor passado. A passagem de parâmetros float de pipelines não tem suporte no momento.
- **Causa**: o tipo de dados para o tipo declarado não é compatível com o valor do parâmetro real. 
- **Recomendação**: forneça os tipos de dados corretos.

### <a name="error-code-df-executor-columnunavailable"></a>Código de erro: DF-executor-ColumnUnavailable
- **Mensagem**: o nome da coluna usado na expressão está indisponível ou é inválido.
- **Causa**: um nome de coluna inválido ou indisponível é usado em uma expressão.
- **Recomendação**: Verifique os nomes de coluna usados em expressões.


### <a name="error-code-df-executor-parseerror"></a>Código de erro: DF-executor-ParseError
- **Mensagem**: a expressão não pode ser analisada.
- **Causa**: uma expressão gerou erros de análise devido à formatação incorreta. 
- **Recomendação**: Verifique a formatação na expressão.


 ### <a name="error-code-df-executor-outofdiskspaceerror"></a>Código de erro: DF-executor-OutOfDiskSpaceError
- **Mensagem**: erro interno do servidor
- **Causa**: o cluster está ficando sem espaço em disco.
- **Recomendação**: repita o pipeline. Se isso não resolver o problema, entre em contato com o atendimento ao cliente.


 ### <a name="error-code-df-executor-storeisnotdefined"></a>Código de erro: DF-executor-StoreIsNotDefined
- **Mensagem**: a configuração do repositório não está definida. Esse erro é potencialmente causado por uma atribuição de parâmetro inválida no pipeline.
- **Causa**: não determinado.
- **Recomendação**: Verifique a atribuição de valor de parâmetro no pipeline. Uma expressão de parâmetro pode conter caracteres inválidos.

### <a name="error-code-df-excel-invalidconfiguration"></a>Código de erro: DF-Excel-InvalidConfiguration
- **Mensagem**: o nome ou índice da planilha do Excel é necessário.
- **Causa**: não determinado.
- **Recomendação**: Verifique o valor do parâmetro. Especifique o nome da planilha ou o índice para ler os dados do Excel.

- **Mensagem**: o nome e o índice da planilha do Excel não podem existir ao mesmo tempo.
- **Causa**: não determinado.
- **Recomendação**: Verifique o valor do parâmetro. Especifique o nome da planilha ou o índice para ler os dados do Excel.

- **Mensagem**: intervalo inválido fornecido.
- **Causa**: não determinado.
- **Recomendação**: Verifique o valor do parâmetro. Especifique um intervalo válido por referência. Para obter mais informações, consulte [Propriedades do Excel](./format-excel.md#dataset-properties).

- **Mensagem**: o arquivo do Excel inválido é fornecido enquanto houver suporte apenas para. xlsx e. xls
- **Causa**: não determinado.
- **Recomendação**: Verifique se a extensão de arquivo do Excel é. xlsx ou. xls.


 ### <a name="error-code-df-excel-invaliddata"></a>Código de erro: DF-Excel-InvalidData
- **Mensagem**: a planilha do Excel não existe.
- **Causa**: não determinado.
- **Recomendação**: Verifique o valor do parâmetro. Especifique um nome de planilha ou índice válido para ler dados do Excel.

- **Mensagem**: não há suporte para a leitura de arquivos do Excel com esquema diferente agora.
- **Causa**: não determinado.
- **Recomendação**: Use um arquivo do Excel com suporte.

- **Mensagem**: não há suporte para o tipo de dados.
- **Causa**: não determinado.
- **Recomendação**: use tipos de dados de arquivo do Excel com suporte.

### <a name="error-code-4502"></a>Código de erro: 4502
- **Mensagem**: há execuções MappingDataflow simultâneas substanciais que estão causando falhas devido à limitação em Integration Runtime.
- **Causa**: um grande número de execuções de atividade de fluxo de dados está ocorrendo simultaneamente no Integration Runtime. Para obter mais informações, consulte [limites de Azure data Factory](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits).
- **Recomendação**: se você quiser executar mais atividades de fluxo de dados em paralelo, distribua-as entre vários tempos de execução de integração.


### <a name="error-code-invalidtemplate"></a>Código de erro: invalidatemplate
- **Mensagem**: a expressão de pipeline não pode ser avaliada.
- **Causa**: a expressão de pipeline passada na atividade de fluxo de dados não está sendo processada corretamente devido a um erro de sintaxe.
- **Recomendação**: Verifique sua atividade no monitoramento de atividades para verificar a expressão.

### <a name="error-code-2011"></a>Código de erro: 2011
- **Mensagem**: a atividade estava em execução no Azure Integration Runtime e falhou ao descriptografar a credencial do armazenamento de dados ou da computação conectada por meio de um Integration Runtime hospedado internamente. Verifique a configuração dos serviços vinculados associados a essa atividade e certifique-se de usar o tipo de tempo de execução de integração adequado.
- **Causa**: o fluxo de dados não dá suporte a serviços vinculados em tempos de execução de integração hospedados internamente.
- **Recomendação**: Configure o fluxo de dados para ser executado em um tempo de execução de integração de rede virtual gerenciada.

## <a name="miscellaneous-troubleshooting-tips"></a>Diversas dicas de solução de problemas
- **Problema**: ocorreu uma exceção inesperada e a execução falhou.
    - **Mensagem**: durante a execução da atividade do Fluxo de Dados: ocorreu uma exceção inesperada e a execução falhou.
    - **Causa**: esse erro é um erro de serviço de back-end. Repita a operação e reinicie a sessão de depuração.
    - **Recomendação**: se a repetição e a reinicialização não resolverem o problema, contate o atendimento ao cliente.

-  **Problema**: não há dados de saída na junção durante a visualização de dados de depuração.
    - **Mensagem**: há um grande número de valores nulos ou valores ausentes que podem ser causados por ter poucas linhas amostradas. Tente atualizar o limite de linha de depuração e atualizar os dados.
    - **Causa**: a condição de junção não corresponde a nenhuma linha ou resultou em um grande número de valores nulos durante a visualização de dados.
    - **Recomendação**: em **configurações de depuração**, aumente o número de linhas no limite de linha de origem. Certifique-se de selecionar um Azure IR que tenha um cluster de fluxo de dados grande o suficiente para lidar com mais dados.
    
- **Problema**: erro de validação na origem com arquivos CSV de várias linhas. 
    - **Mensagem**: você pode ver uma destas mensagens de erro:
        - A última coluna é nula ou está ausente.
        - A validação de esquema na origem falha.
        - A importação de esquema não é exibida corretamente na UX e a última coluna tem um caractere de nova linha no nome.
    - **Causa**: no fluxo de dados de mapeamento, os arquivos de origem CSV de várias linhas não funcionam atualmente quando \r\n é usado como o delimitador de linha. Às vezes, as linhas extras em retornos de carro podem causar erros. 
    - **Recomendação**: gere o arquivo na origem usando \n como o delimitador de linha em vez de \r\n. Ou use a atividade de cópia para converter o arquivo CSV para usar \n como um delimitador de linha.

## <a name="general-troubleshooting-guidance"></a>Diretrizes gerais de solução de problemas
1. Verifique o status das suas conexões de conjunto de dados. Em cada transformação de origem e coletor, vá para o serviço vinculado para cada conjunto de um que você estiver usando e teste as conexões.
2. Verifique o status de suas conexões de arquivo e tabela no designer de fluxo de dados. No modo de depuração, selecione **visualização de dados** em suas transformações de origem para garantir que você possa acessar seus dados.
3. Se tudo parecer correto na visualização de dados, vá para o designer de pipeline e coloque seu fluxo de dados em uma atividade de pipeline. Depure o pipeline para um teste de ponta a ponta.

## <a name="next-steps"></a>Próximas etapas

Para obter mais ajuda com a solução de problemas, consulte estes recursos:

*  [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Solicitações de recurso do Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Fórum do Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre o Data Factory](https://twitter.com/hashtag/DataFactory)

