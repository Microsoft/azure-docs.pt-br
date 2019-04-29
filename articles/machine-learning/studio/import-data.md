---
title: Importar dados de várias fontes de dados
titleSuffix: Azure Machine Learning Studio
description: Como importar os dados para o Azure Machine Learning Studio de diferentes fontes de dados. Saiba quais tipos e formatos de dados têm suporte.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/01/2019
ms.openlocfilehash: 41cc1d6638871f26ae942e724a402e17f52150fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60811022"
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-from-various-data-sources"></a>Importar os dados de treinamento para o Azure Machine Learning Studio de diferentes fontes de dados

Para usar seus próprios dados no Machine Learning Studio para desenvolver e treinar uma solução de análise preditiva, você pode usar dados de: 

* **Arquivo local** – carregue dados locais antecipadamente do disco rígido para criar um módulo de conjunto de dados em seu workspace
* **Fontes de dados online** – use o módulo [Importar Dados][import-data] para acessar dados de uma das várias fontes online durante a execução do experimento
* **Experimento do Machine Learning Studio** – use os dados que foram salvos como um conjunto de dados no Machine Learning Studio
* [**Banco de dados do SQL Server local**](use-data-from-an-on-premises-sql-server.md) - Use dados de um banco de dados do SQL Server local sem ter que copiar dados manualmente

> [!NOTE]
> Há diversos conjuntos de dados de exemplo disponíveis no Machine Learning Studio que você pode usar para treinamento de dados. Para obter informações sobre eles, confira [Usar os conjuntos de dados de exemplo no Azure Machine Learning Studio](use-sample-datasets.md).

## <a name="prepare-data"></a>Preparar dados

O Machine Learning Studio foi criado para trabalhar com dados tabulares ou retangulares, como dados de texto delimitados ou dados estruturados de um banco de dados, embora em algumas circunstâncias seja possível usar dados não retangulares.

Será melhor se os dados estiverem relativamente limpos antes de serem importados para o Studio. Por exemplo, você desejará resolver problemas como cadeias de caracteres sem aspas.

No entanto, há módulos disponíveis no Studio que permitem fazer alguma manipulação de dados no experimento após a importação dos dados. Dependendo dos algoritmos de aprendizado de máquina que for usar, talvez seja necessário decidir como você lidará com problemas estruturais dos dados, como valores ausentes e dados esparsos, e há módulos que podem ajudar com isso. Veja a seção **Transformação de Dados** da paleta de módulos para ver os módulos que executam essas funções.

A qualquer altura do experimento, você poderá exibir ou baixar os dados produzidos por um módulo clicando na porta de saída. Dependendo do módulo, poderá haver opções de download diferentes disponíveis ou a possibilidade de visualizar os dados no navegador da Web no Studio.

## <a name="supported-data-formats-and-data-types"></a>Formatos e tipos de dados compatíveis

Você pode importar vários tipos de dados para seu experimento, dependendo de qual mecanismo usar para importar os dados e de onde eles estão vindo:

* Texto sem formatação (.txt)
* Valores separados por vírgulas (CSV) com cabeçalho (.csv) ou sem (.nh.csv)
* Valores separados por tabulação (TSV) com cabeçalho (.tsv) ou sem (.nh.tsv)
* Arquivo do Excel
* Tabela do Azure
* Tabela do Hive
* Tabela de Banco de Dados SQL
* Valores de OData
* Dados SVMLight (.svmlight) (consulte a [definição de SVMLight](http://svmlight.joachims.org/) para obter informações sobre o formato)
* Attribute Relation File Format (ARFF) (.arff) (consulte a [definição de ARFF](https://weka.wikispaces.com/ARFF) para obter informações sobre o formato)
* Arquivo zip (.zip)
* Arquivo de workspace ou objeto R (.RData)

Se você importar os dados em um formato como ARFF, que inclui metadados, o Studio usará esses metadados para definir o cabeçalho e o tipo de dados de cada coluna.

Se você importar os dados em um formato como TSV ou CSV, que não incluem esses metadados, o Studio inferirá o tipo de dados de cada coluna por amostragem dos dados. Se os dados também não tiverem títulos de coluna, o Studio fornecerá nomes padrão.

Você pode especificar explicitamente ou alterar os cabeçalhos e tipos de dados das colunas usando o módulo [Editar Metadados][edit-metadata].

Os seguintes tipos de dados são reconhecidos pelo Studio:

* Cadeia de caracteres
* Número inteiro
* Double
* Boolean
* DateTime
* TimeSpan

O Studio usa um tipo de dados interno chamado ***tabela de dados*** para passar dados entre módulos. Você pode converter explicitamente os dados no formato de tabela de dados usando o módulo [Converter em Conjunto de Dados][convert-to-dataset].

Qualquer módulo que aceite formatos que não sejam a tabela de dados converterá os dados para a tabela de dados silenciosamente antes de passá-los para o próximo módulo.

Se necessário, você poderá converter o formato de tabela de dados novamente no formato CSV, TSV, ARFF ou SVMLight usando outros módulos de conversão.
Veja a seção **Conversões de Formato de Dados** da paleta de módulos para ver os módulos que executam essas funções.

## <a name="data-capacities"></a>Capacidades de dados

Os módulos do Machine Learning Studio dão suporte a conjuntos de dados com até 10 GB de dados numéricos densos para casos de uso comuns. Se um módulo receber mais de uma entrada, o valor de 10 GB será o total dos tamanhos das entradas. Você pode conjuntos de dados de exemplo maiores usando consultas do Hive ou de Banco de Dados SQL do Azure ou pode usar o Learning por meio contagens de pré-processamento antes de importar os dados.  

Os seguintes tipos de dados podem ser expandidos para conjuntos de dados maiores durante a normalização de recursos e são limitados a menos de 10 GB:

* Esparsos
* Categóricos
* Cadeias de caracteres
* Dados binários

Os seguintes módulos são limitados a conjuntos de dados com menos de 10 GB:

* Módulos de recomendação
* Módulo SMOTE (Técnica de Sobreamostragem Minoritária Sintética)
* Módulos de script: R, Python, SQL
* Módulos em que o tamanho dos dados de saída pode ser maior que o tamanho dos dados de entrada, como Join ou Feature Hashing
* Validação cruzada, Hiperparâmetros de Modelo de Ajuste, Regressão Ordinal e Classes Múltiplas, um versos todos, quando o número de iterações é muito grande

Para conjuntos de dados com mais do que alguns GB, carregue os dados no Armazenamento do Azure ou no Banco de Dados SQL do Azure ou use o Azure HDInsight, em vez de carregar diretamente de um arquivo local.

Você pode encontrar informações sobre dados de imagem na referência de módulo [Importar imagens](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/import-images#bkmk_Notes).

## <a name="import-from-a-local-file"></a>Importação de um arquivo local

Carregue um arquivo de dados do disco rígido para usá-lo como dados de treinamento no Studio. Ao importar um arquivo de dados, você cria um módulo de conjunto de dados pronto para uso nos experimentos em seu workspace.

Para importar dados de um disco rígido local, faça o seguinte:

1. Clique em **+NOVO** na parte inferior da janela do Studio.
2. Selecione **CONJUNTO DE DADOS** e **DO ARQUIVO LOCAL**.
3. Na caixa de diálogo **Carregar um novo conjunto de dados**, procure o arquivo que deseja carregar.
4. Digite um nome, identifique o tipo de dados e, opcionalmente, insira uma descrição. Uma descrição é recomendada – ela permite registrar características sobre os dados que você deseja lembrar ao usar os dados no futuro.
5. A caixa de seleção **Esta é a nova versão de um conjunto de dados existente** permite que você atualize um conjunto de dados existente com novos dados. Para fazer isso, clique nessa caixa de seleção e insira o nome de um conjunto de dados existente.

![Carregar um novo conjunto de dados](./media/import-data/upload-dataset-from-local-file.png)

O tempo de upload depende do tamanho dos dados e da velocidade da conexão com o serviço. Se você souber que o arquivo levará muito tempo para ser carregado, você poderá realizar outras tarefas no Studio enquanto aguarda. No entanto, o fechamento do navegador antes da conclusão do upload de dados causará uma falha no upload.

Uma vez carregados, seus dados são armazenados em um módulo de conjunto de dados e ficam disponíveis para qualquer experimento em seu workspace.

Quando estiver editando um experimento, você poderá encontrar os conjuntos de dados carregados na lista **Meus Conjuntos de Dados** da lista **Conjuntos de Dados Salvos** na paleta de módulos. É possível arrastar e soltar o conjunto de dados na tela de teste quando você desejar usá-lo para outras análises e aprendizado de máquina.

## <a name="import-from-online-data-sources"></a>Importação de fontes de dados online

Usando o módulo [Importar Dados][import-data] módulo, o experimento poderá importar dados de várias fontes de dados online durante a execução.

> [!NOTE]
> Este artigo fornece informações gerais sobre o módulo [Importar Dados][import-data]. Para obter informações mais detalhadas sobre os tipos de dados que você pode acessar, formatos, parâmetros e respostas a perguntas comuns, consulte o tópico de referência do módulo [Importar Dados][import-data].

Usando o módulo [Importar Dados][import-data], você poderá acessar dados de uma das várias fontes de dados online durante a execução do experimento:

* Uma URL da Web usando HTTP
* Hadoop usando HiveQL
* Armazenamento do blob do Azure
* Tabela do Azure
* Banco de dados SQL do Azure ou SQL Server na VM do Azure
* Banco de dados local do SQL Server
* Um provedor de feed de dados, atualmente OData
* Azure Cosmos DB

Como esses dados de treinamento são acessados durante a execução do experimento, eles só ficam disponíveis nesse experimento. Por comparação, os dados que foram armazenados em um módulo do conjunto de dados ficam disponíveis para todos os experimentos em seu workspace.

Para acessar fontes de dados online no experimento do Studio, adicione o módulo [Importar Dados][import-data] ao experimento. Em seguida, selecione **Iniciar Assistente para Importação de Dados** em **Propriedades** para obter instruções guiadas passo a passo para selecionar e configurar a fonte de dados. Como alternativa, você pode selecionar **Fonte de dados** manualmente em **Propriedades** e fornecer os parâmetros necessários para acessar os dados.

As fontes de dados online com suporte são detalhadas na tabela a seguir. Esta tabela também resume os formatos de arquivo e os parâmetros com suporte usados para acessar os dados.

> [!IMPORTANT]
> Atualmente, os módulos [Importar Dados][import-data] e [Exportar Dados][export-data] podem ler e gravar dados somente no armazenamento do Azure criado usando o modelo de implantação Clássico. Em outras palavras, o novo tipo de conta de Armazenamento de Blobs do Azure que oferece uma camada de acesso de armazenamento dinâmica ou uma camada de acesso de armazenamento estática ainda não é compatível.
>
> De modo geral, as contas de armazenamento do Azure que você possa ter criado antes de essa opção se tornar disponível não deverão ser afetadas.
> Se precisar criar uma nova conta, escolha **Clássico** para o modelo de Implantação, ou use o Gerenciador de Recursos e selecione **Objetivo Geral**, em vez de **Armazenamento de Blobs**, para **Tipo de conta**.
>
> Para obter mais informações, confira [Armazenamento de Blobs do Azure: camadas de armazenamento frequente e esporádico](../../storage/blobs/storage-blob-storage-tiers.md).

### <a name="supported-online-data-sources"></a>Fontes de dados online com suporte
O módulo **Importar Dados** do Azure Machine Learning Studio é compatível com as seguintes fontes de dados:

| Fonte de dados | DESCRIÇÃO | parâmetros |
| --- | --- | --- |
| URL da Web via HTTP |Lê dados nos formatos de valores separados por vírgula (CSV), de valores separados por tabulação (TSV), de arquivo de relação de atributo (ARFF) e de Máquinas de Vetores de Suporte (SVM-light), de qualquer URL da Web que use HTTP |<b>URL</b>: especifica o nome completo do arquivo, incluindo a URL do site e o nome do arquivo com qualquer extensão. <br/><br/><b>Formato de dados</b>: especifica um dos formatos de dados compatíveis: CSV, TSV, ARFF ou SVM-light. Se os dados tiverem uma linha de cabeçalho, serão usados para atribuir nomes de coluna. |
| Hadoop/HDFS |Lê dados do armazenamento distribuído no Hadoop. Você especifica os dados desejados usando o HiveQL, uma linguagem de consulta do tipo SQL. O HiveQL também pode ser usado para agregar dados e executar a filtragem deles antes de serem adicionados ao Studio. |<b>Consulta do banco de dados Hive</b>: especifica a consulta do Hive usada para gerar os dados.<br/><br/><b>URI do servidor HCatalog</b>: especifica o nome do cluster usando o formato *&lt;nome do seu cluster&gt;.azurehdinsight.net.*<br/><br/><b>Nome da conta de usuário do Hadoop</b>: especifica o nome da conta de usuário do Hadoop usada para provisionar o cluster.<br/><br/><b>Senha da conta de usuário do Hadoop</b>: especifica as credenciais usadas para provisionar o cluster. Para obter mais informações, veja [Criar clusters Hadoop no HDInsight](/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters).<br/><br/><b>Local dos dados de saída</b>: especifica se os dados são armazenados em um HDFS (Sistema de Arquivos Distribuído Hadoop) ou no Azure. <br/><ul>Se você armazenar dados de saída no HDFS, especifique o URI do servidor HDFS. (Use o nome do cluster HDInsight sem o prefixo HTTPS://). <br/><br/>Se você armazenar os dados de saída no Azure, deverá especificar o nome da conta de armazenamento do Azure, a chave de acesso de Armazenamento e o nome do contêiner do Armazenamento.</ul> |
| Banco de dados SQL |Lê os dados armazenados em um banco de dados SQL do Azure ou em um banco de dados do SQL Server em execução em uma máquina virtual do Azure. |<b>Nome do servidor de banco de dados</b>: especifica o nome do servidor no qual o banco de dados está em execução.<br/><ul>No caso do Banco de Dados SQL do Azure, insira o nome do servidor gerado. Geralmente, tem o formato *&lt;identificador_gerado&gt;.database.windows.net.* <br/><br/>No caso de um SQL server hospedado em uma máquina Virtual do Azure, insira *tcp:&lt;nome da máquina Virtual DNS&gt;, 1433*</ul><br/><b>Nome do banco de dados</b>: especifica o nome do banco de dados no servidor. <br/><br/><b>Nome de conta de usuário do servidor</b>: especifica um nome de usuário para uma conta que tenha permissões de acesso para o banco de dados. <br/><br/><b>Senha da conta de usuário do servidor</b>: especifica a senha da conta de usuário.<br/><br/><b>Consulta de banco de dados</b>: insira uma instrução SQL que descreve os dados que você deseja ler. |
| Banco de dados SQL local |Lê dados armazenados em um banco de dados SQL local. |<b>Gateway de dados</b>: especifica o nome do gateway de gerenciamento de dados instalado em um computador em que ele possa acessar seu banco de dados do SQL Server. Para saber mais sobre como configurar o gateway, confira [Executar análises avançadas com o Azure Machine Learning Studio usando os dados de um SQL server local](use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Nome do servidor de banco de dados</b>: especifica o nome do servidor no qual o banco de dados está em execução.<br/><br/><b>Nome do banco de dados</b>: especifica o nome do banco de dados no servidor. <br/><br/><b>Nome de conta de usuário do servidor</b>: especifica um nome de usuário para uma conta que tenha permissões de acesso para o banco de dados. <br/><br/><b>Nome de usuário e senha</b>: Clique em <b>Inserir valores</b> para inserir suas credenciais de banco de dados. Você pode usar a Autenticação Integrada do Windows ou Autenticação do SQL Server dependendo de como o SQL Server local está configurado.<br/><br/><b>Consulta de banco de dados</b>: insira uma instrução SQL que descreve os dados que você deseja ler. |
| tabela do Azure |Lê os dados do serviço Tabela no Armazenamento do Azure.<br/><br/>Caso você leia grandes quantidades de dados com pouca frequência, use o serviço Tabela do Azure. Ele fornece uma solução de armazenamento flexível, não relacional (NoSQL), massivamente escalonável, barata e de alta disponibilidade. |As opções em **Importar Dados** variam de acordo com o que você está acessando: informações públicas ou uma conta de armazenamento privada que exija credenciais de logon. Isso é determinado pelo <b>Tipo de Autenticação</b>, que pode ter um valor "PublicOrSAS" ou "Account", cada um deles com seu próprio conjunto de parâmetros. <br/><br/><b>URI pública ou de SAS (Assinatura de Acesso Compartilhado)</b>: Os parâmetros são:<br/><br/><ul><b>URI da tabela</b>: especifica a URL de SAS ou pública da tabela.<br/><br/><b>Especifica as linhas a serem examinadas quanto a nomes de propriedades</b>: os valores são <i>TopN</i> para verificar o número especificado de linhas ou <i>ScanAll</i> para obter todas as linhas na tabela. <br/><br/>Se os dados forem homogêneos e previsíveis, é recomendável que você selecione *TopN* e insira um número para N. No caso de tabelas grandes, isso pode resultar em tempos de leitura mais rápidos.<br/><br/>Se os dados são estruturados com conjuntos de propriedades que variam de acordo com a profundidade e a posição na tabela, escolha a opção *ScanAll* para verificar todas as linhas. Isso garante a integridade da propriedade resultante e da conversão dos metadados.<br/><br/></ul><b>Conta de armazenamento privada</b>: Os parâmetros são: <br/><br/><ul><b>Nome da conta</b>: especifica o nome da conta que contém a tabela a ser lida.<br/><br/><b>Chave de conta</b>: especifica a chave de armazenamento associada à conta.<br/><br/><b>Nome da tabela</b>: especifica o nome da tabela que contém os dados a serem lidos.<br/><br/><b>Linhas a serem examinadas quanto a nomes de propriedades</b>: os valores são <i>TopN</i> para verificar o número especificado de linhas ou <i>ScanAll</i> para obter todas as linhas na tabela.<br/><br/>Se os dados forem homogêneos e previsíveis, recomendamos que você selecione *TopN* e insira um número para N. No caso de tabelas grandes, isso pode resultar em tempos de leitura mais rápidos.<br/><br/>Se os dados são estruturados com conjuntos de propriedades que variam de acordo com a profundidade e a posição na tabela, escolha a opção *ScanAll* para verificar todas as linhas. Isso garante a integridade da propriedade resultante e da conversão dos metadados.<br/><br/> |
| Armazenamento do Blobs do Azure |Lê os dados armazenados no serviço Blob no Armazenamento do Azure, incluindo imagens, texto não estruturado ou dados binários.<br/><br/>Você pode usar o serviço Blob para expor dados publicamente ou para armazenar dados de aplicativo de forma privada. Você pode acessar seus dados de qualquer lugar usando as conexões HTTP ou HTTPS. |As opções no módulo **Import Data** (Importar Dados) variam de acordo com o que você está acessando: informações públicas ou uma conta de armazenamento privada que exija credenciais de logon. Isso é determinado pelo <b>Tipo de Autenticação</b>, que pode ter um valor "PublicOrSAS" ou "Conta".<br/><br/><b>URI pública ou de SAS (Assinatura de Acesso Compartilhado)</b>: Os parâmetros são:<br/><br/><ul><b>URI</b>: especifica a URL de SAS ou Pública do blob de armazenamento.<br/><br/><b>Formato de arquivo</b>: especifica o formato dos dados no serviço Blob. Os formatos com suporte são CSV, TSV e ARFF.<br/><br/></ul><b>Conta de armazenamento privada</b>: Os parâmetros são: <br/><br/><ul><b>Nome da conta</b>: especifica o nome da conta que contém o blob que você deseja ler.<br/><br/><b>Chave de conta</b>: especifica a chave de armazenamento associada à conta.<br/><br/><b>Caminho para o contêiner, diretório ou blob</b>: especifica o nome do blob que contém os dados a serem lidos.<br/><br/><b>Formato de arquivo do Blob</b>: especifica o formato dos dados no serviço Blob. Os formatos de dados com suporte são CSV, TSV, ARFF, CSV com uma codificação especificada e o Excel. <br/><br/><ul>Se o formato for CSV ou TSV, não deixe de indicar se o arquivo contém uma linha de cabeçalho.<br/><br/>Você pode usar a opção do Excel para ler dados de pastas de trabalho do Excel. Na opção <i>formato de dados do Excel</i>, indique se os dados estão em um intervalo de planilha do Excel ou em uma tabela do Excel. Na opção <i>planilha do Excel ou tabela inserida</i>, especifique o nome da planilha ou da tabela que você deseja ler.</ul><br/> |
| Provedor de feed de dados |Lê dados de um provedor de feeds com suporte. Atualmente, somente o formato OData (Open Data Protocol) tem suporte. |<b>Tipo de conteúdo de dados</b>: especifica o formato OData.<br/><br/><b>URL de origem</b>: especifica a URL completa dos feed de dados. <br/>Por exemplo, a URL a seguir lê a partir do banco de dados de exemplo Northwind: https://services.odata.org/northwind/northwind.svc/ |

## <a name="import-from-another-experiment"></a>Importação de outro experimento

Haverá momentos em que você desejará usar o resultado intermediário de um teste como parte de outro teste. Para fazer isso, salve o módulo como um conjunto de dados:

1. Clique na saída do módulo que você deseja salvar como um conjunto de dados.
2. Clique em **Salvar como Conjunto de Dados**.
3. Quando solicitado, insira um nome e uma descrição que permitiriam identificar o conjunto de dados facilmente.
4. Clique na marca de seleção **OK** .

Quando o salvamento for concluído, o conjunto de dados ficará disponível para uso em qualquer experimento do seu workspace. Você pode encontrá-lo na lista **Conjuntos de Dados Salvos** na paleta de módulos.

## <a name="next-steps"></a>Próximas etapas

[Implantando os serviços Web do Azure Machine Learning Studio que usam os módulos Importar Dados e Exportar Dados](web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/


<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
