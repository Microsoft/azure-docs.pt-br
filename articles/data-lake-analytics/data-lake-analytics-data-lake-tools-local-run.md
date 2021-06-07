---
title: Executar scripts do U-SQL do Azure Data Lake em seu computador local
description: Saiba como usar as Ferramentas do Azure Data Lake para Visual Studio para executar trabalhos de U-SQL no computador local.
author: liudan66
ms.author: liud
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 07/03/2018
ms.openlocfilehash: 5f9410cc91174420662bb5efc67c8904b5d5e647
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018980"
---
# <a name="run-u-sql-scripts-on-your-local-machine"></a>Execute scripts do U-SQL em sua máquina local

Ao desenvolver scripts de U-SQL, você pode economizar tempo e despesas executando os scripts localmente. O Azure Data Lake Tools para Visual Studio oferece suporte à execução de scripts U-SQL em sua máquina local. 

## <a name="basic-concepts-for-local-runs"></a>Conceitos básicos para execuções locais

O gráfico a seguir mostra os componentes para execução local e como esses componentes são mapeados para a execução da nuvem.

|Componente|Execução local|Execução de nuvem|
|---------|---------|---------|
|Armazenamento|Pasta raiz de dados local|Conta padrão do Azure Data Lake Store|
|Computação|Mecanismo de execução local de U-SQL|Serviço do Azure Data Lake Analytics|
|Ambiente de execução|Diretório de trabalho no computador local|Cluster do Azure Data Lake Analytics|

As seções a seguir fornecem mais informações sobre componentes de execução locais.

### <a name="local-data-root-folders"></a>Pastas raiz de dados local

Uma pasta raiz de dados local é um **armazenamento local** para a conta do computador local. Qualquer pasta no sistema de arquivos local em seu computador local pode ser uma pasta raiz de dados local. É o mesmo que a conta padrão do Azure Data Lake Store de uma conta do Data Lake Analytics. Mudar para uma pasta raiz de dados diferente é como mudar para uma conta de loja padrão diferente. 

A pasta raiz de dados é usada da seguinte maneira:
- Metadados de Store. Exemplos são bancos de dados, tabelas, funções com valor de tabela e assemblies.
- Procure os caminhos de entrada e saída que são definidos como caminhos relativos em scripts U-SQL. Usando caminhos relativos, é mais fácil implantar seus scripts de U-SQL no Azure.

### <a name="u-sql-local-run-engines"></a>Mecanismos de execução local do U-SQL

Um mecanismo de execução local do U-SQL um **conta de computador local** para trabalhos de U-SQL. Os usuários podem executar trabalhos de U-SQL localmente usando as Ferramentas do Azure Data Lake para Visual Studio. Também há suporte para execuções locais por meio das interfaces de programação e linha de comando do SDK do Azure Data Lake U-SQL. Saiba mais sobre o [SDK do Azure Data Lake U-SQL](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/).

### <a name="working-directories"></a>Diretórios de trabalho

Quando você executa um script U-SQL, uma pasta de diretório de trabalho é necessário para armazenar em cache os resultados de compilação, logs de execução e executar outras funções. No Azure Data Lake Tools para Visual Studio, o diretório de trabalho é o diretório de trabalho do projeto U-SQL. Ele está localizado sob `<U-SQL project root path>/bin/debug>`. O diretório de trabalho é limpo toda vez que uma nova execução é acionada.

## <a name="local-runs-in-microsoft-visual-studio"></a>Execuções locais no Microsoft Visual Studio

As Ferramentas do Azure Data Lake para Visual Studio têm um mecanismo de execução local integrado. As ferramentas exibem o mecanismo como uma conta de computação local. Para executar um script U-SQL localmente, selecione a conta **Máquina local** ou **Projeto local** no menu suspenso da margem do editor do script. Em seguida, selecione **Enviar**.

![Envie um script U-SQL para uma conta local](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-submit-script-to-local-account.png) 
 
## <a name="local-runs-with-a-local-machine-account"></a>Execuções locais com uma conta de computador Local

Uma conta **Máquina local** é uma conta de computação local compartilhada com uma única pasta raiz de dados local como a conta de loja local. Por padrão, a pasta raiz de dados está localizada em **C:\Users \<username> \AppData\Local\USQLDataRoot**. Ele também pode ser configurado por meio de **ferramentas**  >  **Data Lake**  >  **Opções e configurações**.

![Configurar uma pasta raiz de dados local](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-local-data-root.png)
  
Um projeto U-SQL é necessário para uma execução local. O diretório de trabalho do projeto U-SQL é usado para o diretório de trabalho de execução local do U-SQL. Os resultados da compilação, os logs de execução e outros arquivos relacionados à execução da tarefa são gerados e armazenados na pasta do diretório de trabalho durante a execução local. Toda vez que você reexecuta o script, todos os arquivos no diretório de trabalho são limpos e regenerados.

## <a name="local-runs-with-a-local-project-account"></a>Execuções locais com uma conta de projeto local

Uma conta do **projeto local** é uma conta de cálculo local isolada do projeto para cada projeto com uma pasta raiz de dados local isolada. Todo projeto U-SQL ativo aberto no Gerenciador de Soluções no Visual Studio tem uma conta `(Local-project: <project name>)` correspondente. As contas são listadas no Server Explorer no Visual Studio e na margem do editor de scripts do U-SQL.  

A conta de **projeto local** fornece um ambiente de desenvolvimento limpo e isolado. Um **Local-machine** terá uma pasta raiz local de dados compartilhada que armazena dados de metadados e de entrada e saída para todos os trabalhos locais. Mas uma conta de **projeto local** cria uma pasta raiz de dados local temporária sob um diretório de trabalho do projeto U-SQL sempre que um script U-SQL é executado. Essa pasta raiz temporária de dados é limpa quando uma recriação ou nova execução ocorre. 

Um projeto U-SQL gerencia o ambiente de execução local isolado por meio de uma referência e propriedade do projeto. Você pode configurar as origens de dados de entrada para scripts U-SQL nos ambientes de projeto e de banco de dados referenciado.

### <a name="manage-the-input-data-source-for-a-local-project-account"></a>Gerenciar a fonte de dados de entrada para uma conta de projeto local 

Um projeto U-SQL cria uma pasta raiz de dados local e configura dados para uma conta de **projeto local**. Uma pasta raiz temporária de dados é limpa e recriada no diretório de trabalho do projeto U-SQL toda vez que uma reconstrução e uma execução local ocorrem. Todas as fontes de dados configuradas pelo projeto U-SQL são copiadas para essa pasta raiz de dados local temporária antes da execução da tarefa local. 

Você pode configurar a pasta raiz das suas fontes de dados. Clique com o botão direito do mouse em **U-SQL**  >  **Propriedade** do projeto  >  **teste de fonte de dados**. Quando você executa um script U-SQL uma **projeto Local** conta, todos os arquivos e subpastas na **fonte de dados de teste** pasta são copiados para a pasta raiz de dados local temporário. Arquivos em subpastas são incluídos. Depois que uma tarefa local é executada, os resultados de saída também podem ser encontrados na pasta raiz de dados locais temporários no diretório de trabalho do projeto. Toda essa saída é excluída e limpa quando o projeto é reconstruído e limpo. 

![Configurar a fonte de dados de teste de um projeto](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-a-referenced-database-environment-for-a-local-project-account"></a>Gerenciar um ambiente de banco de dados referenciado para um **Local-projeto** conta 

Se uma consulta U-SQL usar ou consultar objetos de banco de dados U-SQL, você deverá deixar os ambientes de banco de dados prontos localmente antes de executar o script U-SQL localmente. Para uma conta de **projeto local**, as dependências do banco de dados U-SQL podem ser gerenciadas por referências de projeto U-SQL. Você pode adicionar referências de projeto de banco de dados U-SQL ao seu projeto de U-SQL. Antes de executar scripts U-SQL em uma conta de **projeto local**, todos os bancos de dados de referência são implantados na pasta raiz de dados local temporária. E para cada execução, a pasta raiz temporária de dados é limpa como um novo ambiente isolado.

Consulte o artigo relacionado:
* Saiba como gerenciar definições de banco de dados U-SQL e referências na [projetos de banco de dados U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="the-difference-between-local-machine-and-local-project-accounts"></a>A diferença entre **máquina Local** e contas **projeto Local**

Uma conta de **máquina local** simula uma conta do Azure Data Lake Analytics em computadores locais dos usuários. Ele compartilha a mesma experiência com uma conta do Azure Data Lake Analytics. Uma conta de **projeto local** fornece um ambiente de desenvolvimento local fácil de usar. Esse ambiente ajuda os usuários a implantar referências de banco de dados e dados de entrada antes de executar scripts localmente. Uma conta **Máquina local** fornece um ambiente permanente compartilhado que pode ser acessado por todos os projetos. Uma conta de **projeto local** fornece um ambiente de desenvolvimento isolado para cada projeto. Ele é atualizado para cada execução. Uma conta **projeto local** oferece uma experiência de desenvolvimento mais rápida por meio da aplicação rapidamente novas alterações.

Mais diferenças entre as contas **Máquina local** e **Projeto local** são mostradas na tabela a seguir:

|Ângulo de diferença|Máquina local|Projeto local|
|----------------|---------------|---------------|
|Acesso local|Pode ser acessado por todos os projetos.|Apenas o projeto correspondente pode acessar essa conta.|
|Pasta raiz de dados local|Uma pasta local permanente. Configurado por meio de **ferramentas**  >  **Data Lake**  >  **Opções e configurações**.|Uma pasta temporária criada para cada execução local no diretório de trabalho do projeto U-SQL. A pasta é limpa quando uma reconstrução ou reexecução acontece.|
|Dados de entrada para um script U-SQL|O caminho relativo na pasta raiz de dados locais permanentes.|Defina por meio da fonte **de dados de teste de Propriedade do projeto U-SQL**  >  . Todos os arquivos e subpastas são copiados para a pasta raiz temporária de dados antes de uma execução local.|
|Dados de saída para um script U-SQL|Caminho relativo sob a pasta raiz de dados locais permanentes.|Saída para a pasta raiz de dados temporários. Os resultados são limpos quando uma reconstrução ou reexecução acontece.|
|Implantação de banco de dados de referência|Os bancos de dados referenciados não são implantados automaticamente quando executados em uma conta **Máquina local**. É o mesmo para envio para uma conta do Azure Data Lake Analytics.|Os bancos de dados referenciados são implantados na conta **Projeto local** automaticamente antes de uma execução local. Todos os ambientes de banco de dados são limpos e reimplantados, quando uma recompilação ou execute novamente acontece.|

## <a name="a-local-run-with-the-u-sql-sdk"></a>Uma execução local com o SDK do U-SQL

Você pode executar scripts U-SQL localmente no Visual Studio e também usar o SDK do U-SQL do Azure Data Lake para executar scripts U-SQL localmente com interfaces de linha de comando e programação. Por meio dessas interfaces, você pode automatizar execuções e testes locais do U-SQL.

Saiba mais sobre o [SDK do Azure Data Lake U-SQL](data-lake-analytics-u-sql-sdk.md).

## <a name="next-steps"></a>Próximas etapas

- [Como configurar um pipeline de IC / CD para o Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md).
- [Como testar seu código do Azure Data Lake Analytics](data-lake-analytics-cicd-test.md).
