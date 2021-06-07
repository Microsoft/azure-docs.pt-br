---
title: Solucionar problemas referentes a erros de instalação de bibliotecas
description: Este tutorial fornece uma visão geral sobre como solucionar erros de instalação da biblioteca.
services: synapse-analytics
author: midesa
ms.author: midesa
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: conceptual
ms.date: 01/04/2021
ms.openlocfilehash: 57e9d0c584600a8fac90499d72cfac1620052603
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694913"
---
# <a name="troubleshoot-library-installation-errors"></a>Solucionar problemas referentes a erros de instalação de bibliotecas 
Para disponibilizar código de terceiros ou localmente compilado para seus aplicativos, você pode instalar uma biblioteca em um dos pools de Apache Spark sem servidor. Os pacotes listados no arquivo de requirements.txt são baixados de PyPi no momento da inicialização do pool. Esse arquivo de requisitos é usado toda vez que uma instância do Spark é criada a partir desse pool do Spark. Depois que uma biblioteca é instalada para um pool do Spark, ela está disponível para todas as sessões que usam o mesmo pool. 

Em alguns casos, você pode descobrir que uma biblioteca não está aparecendo no pool de Apache Spark. Esse caso geralmente ocorre quando há um erro no requirements.txt fornecido ou em bibliotecas especificadas. Quando ocorrer um erro no processo de instalação da biblioteca, o pool de Apache Spark será revertido para bibliotecas especificadas no tempo de execução de base do Synapse.

O objetivo deste documento é fornecer problemas comuns e ajudá-lo a depurar erros de instalação da biblioteca.

## <a name="force-update-your-apache-spark-pool"></a>Forçar atualização do pool de Apache Spark
Quando você atualizar as bibliotecas em seu pool de Apache Spark, essas alterações serão coletadas depois que o pool for reiniciado. Se você tiver trabalhos ativos, esses trabalhos continuarão a ser executados na versão original do pool do Spark.

Você pode forçar as alterações a serem aplicadas selecionando a opção para **forçar novas configurações**. Essa configuração encerrará todas as sessões atuais para o pool do Spark selecionado. Depois que as sessões forem encerradas, você precisará aguardar até que o pool seja reiniciado. 

![Adicionar bibliotecas do Python](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "Adicionar bibliotecas do Python")

## <a name="track-installation-progress"></a>Acompanhar o progresso da instalação
Um trabalho do Spark reservado pelo sistema é iniciado cada vez que um pool é atualizado com um novo conjunto de bibliotecas. Esse trabalho do Spark ajuda a monitorar o status da instalação da biblioteca. Se a instalação falhar devido a conflitos de biblioteca ou outros problemas, o pool do Spark será revertido para seu estado anterior ou padrão. 

Além disso, os usuários também podem inspecionar os logs de instalação para identificar conflitos de dependência ou ver quais bibliotecas foram instaladas durante a atualização do pool.

Para exibir esses logs:
1. Navegue até a lista de aplicativos Spark na guia **monitorar** . 
2. Selecione o trabalho do aplicativo Spark do sistema que corresponde à sua atualização de pool. Esses trabalhos do sistema são executados sob o título *SystemReservedJob-LibraryManagement* .
   ![Captura de tela que realça o trabalho da biblioteca reservada do sistema.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Exibir trabalho da biblioteca do sistema")
3. Alterne para exibir os logs de **Driver** e **stdout** . 
4. Nos resultados, você verá os logs relacionados à instalação de seus pacotes.
    ![Captura de tela que realça os resultados do trabalho da biblioteca reservada do sistema.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Exibir o progresso do trabalho da biblioteca do sistema")

## <a name="validate-your-permissions"></a>Validar suas permissões
Para instalar e atualizar bibliotecas, você deve ter as permissões de proprietário de dados de armazenamento de **blob de armazenamento** ou de propriedade de **blob do Storage** na conta de armazenamento de Azure data Lake Storage Gen2 primária que está vinculada ao espaço de trabalho do Azure Synapse Analytics.

Para validar que você tem essas permissões, você pode executar o seguinte código:

```python
from pyspark.sql.types import StructType,StructField, StringType, IntegerType
data2 = [("James","Smith","Joe","4355","M",3000),
    ("Michael","Rose","Edward","40288","F",4000)
  ]

schema = StructType([ \
    StructField("firstname",StringType(),True), \
    StructField("middlename",StringType(),True), \
    StructField("lastname",StringType(),True), \
    StructField("id", StringType(), True), \
    StructField("gender", StringType(), True), \
    StructField("salary", IntegerType(), True) \
  ])
 
df = spark.createDataFrame(data=data2,schema=schema)

df.write.csv("abfss://<<ENTER NAME OF FILE SYSTEM>>@<<ENTER NAME OF PRIMARY STORAGE ACCOUNT>>.dfs.core.windows.net/validate_permissions.csv")

```
Se você receber um erro, provavelmente você não tem as permissões necessárias. Para saber como obter as permissões necessárias, visite este documento: [atribuir permissões de proprietário de dados de blob de armazenamento ou de proprietários de dados de blob de armazenamento](../../storage/common/storage-auth-aad-rbac-portal.md#assign-an-azure-built-in-role).

Além disso, se você estiver executando um pipeline, o MSI do espaço de trabalho deverá ter também permissões de colaborador de armazenamento de dados do BLOB ou armazenamento de dados de BLOB. Para saber como conceder a identidade do seu espaço de trabalho essa permissão, visite: [conceder permissões para identidade gerenciada do espaço de trabalho](../security/how-to-grant-workspace-managed-identity-permissions.md).

## <a name="check-the-environment-configuration-file"></a>Verificar o arquivo de configuração do ambiente
Um arquivo de configuração de ambiente pode ser usado para atualizar o ambiente Conda. Esses formatos de arquivo aceitáveis para o gerenciamento de pools do Python estão listados [aqui](./apache-spark-manage-python-packages.md).

É importante observar as seguintes restrições:
   -  O conteúdo do arquivo de requisitos não deve incluir linhas ou caracteres em branco extras. 
   -  O [tempo de execução do Synapse](apache-spark-version-support.md) inclui um conjunto de bibliotecas que são pré-instaladas em todos os pools de Apache Spark sem servidor. Os pacotes que vêm pré-instalados no tempo de execução de base não podem ser removidos ou desinstalados.
   -  Não há suporte para a alteração da versão PySpark, Python, escala/Java, .NET ou Spark.
   -  As bibliotecas no escopo da sessão do Python só aceitam arquivos com uma extensão YML.

## <a name="validate-wheel-files"></a>Validar arquivos de roda
Os pools de Apache Spark sem servidor Synapse se baseiam na distribuição do Linux. Ao baixar e instalar arquivos de roda diretamente do PyPI, certifique-se de selecionar a versão criada no Linux e executada na mesma versão do Python que o pool do Spark.

>[!IMPORTANT]
>Os pacotes personalizados podem ser adicionados ou modificados entre sessões. No entanto, você precisará aguardar a reinicialização do pool e da sessão para ver o pacote atualizado.

## <a name="check-for-dependency-conflicts"></a>Verificar conflitos de dependência
 Em geral, a resolução de dependência do Python pode ser difícil de gerenciar. Para ajudar a depurar conflitos de dependência localmente, você pode criar seu próprio ambiente virtual com base no tempo de execução do Synapse e validar suas alterações.

Para recriar o ambiente e validar suas atualizações:
 1. [Baixe](https://github.com/Azure-Samples/Synapse/blob/main/Spark/Python/base_environment.yml) o modelo para recriar localmente o tempo de execução do Synapse. Pode haver pequenas diferenças entre o modelo e o ambiente Synapse real.
   
 2. Crie um ambiente virtual usando as [instruções a seguir](https://docs.conda.io/projects/conda/latest/user-guide/tasks/manage-environments.html). Esse ambiente permite que você crie uma instalação isolada do Python com a lista de bibliotecas especificada. 
    
    ```
    conda myenv create -f environment.yml
    conda activate myenv
    ```
   
 3. Use ``pip install -r <provide your req.txt file>`` para atualizar o ambiente virtual com os pacotes especificados. Se a instalação resultar em um erro, poderá haver um conflito entre o que é pré-instalado no tempo de execução de Synapse base e o que é especificado no arquivo de requisitos fornecido. Esses conflitos de dependência devem ser resolvidos para obter as bibliotecas atualizadas em seu pool de Apache Spark sem servidor.

>[!IMPORTANT]
>Problemas podem arrise ao usar Pip e Conda juntos. Ao combinar Pip e Conda, é melhor seguir essas [práticas](https://docs.conda.io/projects/conda/latest/user-guide/tasks/manage-environments.html#using-pip-in-an-environment)recomendadas.

## <a name="next-steps"></a>Próximas etapas
- Exibir as bibliotecas padrão: [suporte à versão Apache Spark](apache-spark-version-support.md)