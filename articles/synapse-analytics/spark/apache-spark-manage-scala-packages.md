---
title: Gerenciar escalabilidade & bibliotecas Java para Apache Spark
description: Saiba como adicionar e gerenciar bibliotecas escalares e Java no Azure Synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: c70ecc4fc5469d728bc12d47024585ccf00ff98e
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098699"
---
# <a name="manage-scala-and-java-packages-for-apache-spark-in-azure-synapse-analytics"></a>Gerenciar pacotes escalares e Java para Apache Spark no Azure Synapse Analytics

As bibliotecas fornecem código reutilizável que você talvez queira incluir em seus programas ou projetos. 

Talvez seja necessário atualizar seu ambiente de pool de Apache Spark sem servidor por vários motivos. Por exemplo, você pode achar que:
- uma de suas dependências principais lançou uma nova versão.
- Você precisa de um pacote extra para treinar seu modelo de aprendizado de máquina ou preparar seus dados.
- Você encontrou um pacote melhor e não precisa mais do pacote mais antigo.

Para disponibilizar código de terceiros ou compilado localmente para seus aplicativos, você pode instalar uma biblioteca em um dos pools de Apache Spark sem servidor ou na sessão do bloco de anotações. Neste artigo, abordaremos como você pode gerenciar os pacotes escalares e Java.

## <a name="default-installation"></a>Instalação padrão
Apache Spark no Azure Synapse Analytics tem um conjunto completo de bibliotecas para engenharia de dados comum, preparação de dados, aprendizado de máquina e tarefas de visualização de dados. A lista de bibliotecas completa pode ser encontrada em [suporte à versão Apache Spark](apache-spark-version-support.md). 

Quando uma instância do Spark é iniciada, essas bibliotecas serão incluídas automaticamente. Pacotes escalares/Java extras podem ser adicionados no nível do pool e da sessão do Spark.

## <a name="workspace-packages"></a>Pacotes de espaço de trabalho
Os pacotes de espaço de trabalho podem ser arquivos jar personalizados ou privados. Você pode carregar esses pacotes em seu espaço de trabalho e, posteriormente, atribuí-los a um pool do Spark específico.

Para adicionar pacotes de espaço de trabalho:
1. Navegue até a guia **gerenciar**  >  **pacotes de espaço de trabalho** .
2. Carregue os arquivos jar usando o seletor de arquivo.
3. Depois que os arquivos tiverem sido carregados no espaço de trabalho Synapse do Azure, você poderá adicionar esses arquivos jar a um determinado pool de Apache Spark.

![Captura de tela que realça os pacotes de espaço de trabalho.](./media/apache-spark-azure-portal-add-libraries/studio-add-workspace-package.png "Exibir pacotes de espaço de trabalho")

## <a name="pool-libraries"></a>Bibliotecas de pool
Depois de identificar os pacotes escalares e Java que você gostaria de usar para o aplicativo Spark, você pode instalá-los em um pool do Spark. As bibliotecas de nível de pool estão disponíveis para todos os blocos de anotações e trabalhos em execução no pool.

Você pode atualizar as bibliotecas do pool do Spark navegando até o Azure Synapse Studio ou portal do Azure. Aqui, você pode selecionar as bibliotecas de espaço de trabalho a serem instaladas. 

Depois que as alterações forem salvas, um trabalho do Spark executará a instalação e armazenará em cache o ambiente resultante para reutilização posterior. Quando o trabalho for concluído, novos trabalhos do Spark ou sessões do bloco de anotações usarão as bibliotecas de pools atualizadas. 

> [!IMPORTANT]
> - Se o pacote que você está instalando for grande ou demorar muito para ser instalado, isso afetará o tempo de inicialização da instância do Spark.
> - Não há suporte para a alteração da versão PySpark, Python, escala/Java, .NET ou Spark.

#### <a name="manage-packages-from-azure-synapse-studio-or-azure-portal"></a>Gerenciar pacotes do Azure Synapse Studio ou portal do Azure
As bibliotecas do pool do Spark podem ser gerenciadas no Azure Synapse Studio ou portal do Azure. 

Para atualizar ou adicionar bibliotecas a um pool do Spark:
1. Navegue até o espaço de trabalho do Azure Synapse Analytics no portal do Azure.

    Se você estiver atualizando do **portal do Azure**:

    - Na seção **recursos do Synapse** , selecione a guia **pools de Apache Spark** e selecione um pool do Spark na lista.
     
    - Selecione os **pacotes** na seção **configurações** do pool do Spark.
  
    ![Captura de tela que realça o botão carregar arquivo de configuração do ambiente.](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Adicionar bibliotecas do Python")
   
    Se você estiver atualizando do **Synapse Studio**:
    - Selecione **gerenciar** no painel de navegação principal e, em seguida, selecione **pools de Apache Spark**.

    - Selecione a seção **pacotes** para um pool do Spark específico.
    ![Captura de tela que realça a opção de configuração do ambiente de carregamento do estúdio.](./media/apache-spark-azure-portal-add-libraries/studio-update-libraries.png "Adicionar bibliotecas Python do estúdio")
   
2. Para adicionar arquivos jar, navegue até a seção **pacotes de espaço de trabalho** para adicionar ao pool. 
3. Depois de salvar as alterações, um trabalho do sistema será disparado para instalar e armazenar em cache as bibliotecas especificadas. Esse processo ajuda a reduzir o tempo de inicialização geral da sessão. 
4. Depois que o trabalho for concluído com êxito, todas as novas sessões irão pegar as bibliotecas de pools atualizadas.

> [!IMPORTANT]
> Ao selecionar a opção para **forçar novas configurações**, você encerrará todas as sessões atuais para o pool do Spark selecionado. Depois que as sessões forem encerradas, você precisará aguardar até que o pool seja reiniciado. 
>
> Se essa configuração estiver desmarcada, você precisará aguardar a sessão atual do Spark terminar ou interrompê-la manualmente. Depois que a sessão for encerrada, você precisará permitir que o pool seja reiniciado.

#### <a name="track-installation-progress-preview"></a>Acompanhar o progresso da instalação (versão prévia)
Um trabalho do Spark reservado pelo sistema é iniciado cada vez que um pool é atualizado com um novo conjunto de bibliotecas. Esse trabalho do Spark ajuda a monitorar o status da instalação da biblioteca. Se a instalação falhar devido a conflitos de biblioteca ou outros problemas, o pool do Spark será revertido para seu estado anterior ou padrão. 

Além disso, os usuários também podem inspecionar os logs de instalação para identificar conflitos de dependência ou ver quais bibliotecas foram instaladas durante a atualização do pool.

Para exibir esses logs:
1. Navegue até a lista de aplicativos Spark na guia **monitorar** . 
2. Selecione o trabalho do aplicativo Spark do sistema que corresponde à sua atualização de pool. Esses trabalhos do sistema são executados sob o título *SystemReservedJob-LibraryManagement* .
   ![Captura de tela que realça o trabalho da biblioteca reservada do sistema.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Exibir trabalho da biblioteca do sistema")
3. Alterne para exibir os logs de **Driver** e **stdout** . 
4. Nos resultados, você verá os logs relacionados à instalação de seus pacotes.
    ![Captura de tela que realça os resultados do trabalho da biblioteca reservada do sistema.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Exibir o progresso do trabalho da biblioteca do sistema")

## <a name="session-scoped-libraries"></a>Bibliotecas com escopo de sessão 
Além das bibliotecas de nível de pool, você também pode especificar bibliotecas no escopo da sessão no início de uma sessão do bloco de anotações.  Bibliotecas com escopo de sessão permitem especificar e usar pacotes JAR exclusivamente dentro de uma sessão de bloco de anotações. 

Ao usar bibliotecas com escopo de sessão, é importante ter os seguintes pontos em mente:
   - Quando você instala bibliotecas com escopo de sessão, somente o notebook atual tem acesso às bibliotecas especificadas. 
   - Essas bibliotecas não afetarão outras sessões ou trabalhos usando o mesmo pool do Spark. 
   - Essas bibliotecas são instaladas na parte superior das bibliotecas de tempo de execução base e de pool. 
   - As bibliotecas de notebook terão a precedência mais alta.

Para especificar pacotes Java ou escalares no escopo da sessão, você pode usar a ```%%configure``` opção:

```scala
%%configure -f
{
    "conf": {
        "spark.jars": "abfss://<<file system>>@<<storage account>.dfs.core.windows.net/<<path to JAR file>>",
    }
}
```

Recomendamos que você execute a configuração%% no início do notebook. Você pode consultar este [documento](https://github.com/cloudera/livy#request-body) para obter a lista completa de parâmetros válidos.

## <a name="next-steps"></a>Próximas etapas
- Exibir as bibliotecas padrão: [suporte à versão Apache Spark](apache-spark-version-support.md)
- Solucionar erros de instalação da biblioteca: [solucionar erros de biblioteca](apache-spark-troubleshoot-library-errors.md)
