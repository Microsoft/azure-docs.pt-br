---
title: Personalizar os clusters de HDInsight do Azure por meio de ações de script
description: Adicione componentes personalizados a clusters do HDInsight baseados em Linux usando ações de script. As ações de script são scripts de bash que podem ser usados para personalizar a configuração do cluster ou adicionar mais serviços e utilitários, como Hue, Solr ou R.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 7885b03e9f92fc8e8c5b2c78049760cbed8d4dc7
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67703969"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>Personalizar os clusters de HDInsight do Azure por meio de ações de script

O Azure HDInsight fornece um método de configuração chamado **ações de script** que chama scripts personalizados para personalizar o cluster. Esses scripts são usados para instalar componentes adicionais e alterar definições de configuração. Ações de script podem ser usadas durante ou após a criação do cluster.

Ações de script também podem ser publicadas no Azure Marketplace como um aplicativo do HDInsight. Para saber mais sobre aplicativos do HDInsight, confira [Publicar um aplicativo do HDInsight no Azure Marketplace](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>Permissões

Para um cluster do HDInsight associado a domínio, há duas permissões Apache Ambari necessárias ao usar ações de script com o cluster:

* **AMBARI.RUN\_CUSTOM\_COMMAND**. Função de administrador de Ambari tem essa permissão por padrão.
* **CLUSTER.RUN\_CUSTOM\_COMMAND**. O administrador de Cluster HDInsight e administrador de Ambari têm essa permissão por padrão.

Para saber mais sobre como trabalhar com permissões com o HDInsight associado a um domínio, confira [Gerenciar clusters do HDInsight com o Enterprise Security Package](./domain-joined/apache-domain-joined-manage.md).

## <a name="access-control"></a>Controle de acesso

Se você não é o proprietário ou administrador da sua assinatura do Azure, sua conta deve ter pelo menos acesso de Colaborador ao grupo de recursos que contém o cluster do HDInsight.

Se você criar um cluster do HDInsight, alguém com acesso mínimo de Colaborador à assinatura do Azure deve ter registrado anteriormente o provedor para o HDInsight. O registro do provedor acontece quando um usuário com acesso de Colaborador à assinatura cria um recurso nela pela primeira vez. Isso também pode ser feito sem criar um recurso se você [registrar um provedor usando REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Saiba mais sobre como trabalhar com o gerenciamento de acesso:

* [Introdução ao gerenciamento de acesso no portal do Azure](../role-based-access-control/overview.md)
* [Usar as atribuições de função para gerenciar o acesso aos recursos de assinatura do Azure](../role-based-access-control/role-assignments-portal.md)

## <a name="understand-script-actions"></a>Noções básicas sobre ações de script

Uma ação de script é Bash script executado em nós em um cluster HDInsight. Estas são as características e os recursos das ações de script:

* Devem estar armazenados em um URI que possa ser acessado do cluster do HDInsight. Estes são os possíveis locais de armazenamento:
    
    * Para clusters regulares:
    
      * Gen1 ADLS: A entidade de serviço que HDInsight usa para acessar o Data Lake Storage deve ter acesso de leitura para o script. O formato de URI dos scripts armazenados no Data Lake Storage Gen1 é `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`.
      
      * Um blob em uma conta do Armazenamento do Azure que seja a conta de armazenamento principal ou adicional para o cluster do HDInsight. O HDInsight recebe acesso a ambos esses tipos de contas de armazenamento durante a criação do cluster.

        > [!IMPORTANT]  
        > Não gire a chave de armazenamento nessa conta de armazenamento do Azure, como fará com que as ações de script subsequentes com scripts armazenados ali para falhar.

      * Um compartilhamento de arquivos serviço público acessível por meio de caminhos de http://. Os exemplos são BLOBs do Azure, GitHub, OneDrive.

        Para obter exemplos de URIs, confira [Exemplos de ações de script](#example-script-action-scripts).

     * Para clusters com ESP:
         
         * O wasb: / / ou wasbs: / / ou http [s] :// URIs são suportados.
            
* Isso pode estar restrito à execução somente em determinados tipos de nó. Por exemplo, nós de cabeçalho ou nós de trabalho.

* Podem ser persistentes ou ad hoc.

    Os scripts persistentes são usados para personalizar novos nós de trabalho adicionados ao cluster por meio de operações de escalonamento. Um script persistente também pode aplicar alterações a outro tipo de nó quando ocorrem operações de escalonamento. Um exemplo é um nó de cabeçalho.

  > [!IMPORTANT]  
  > As ações de script persistentes devem ter um nome exclusivo.

    Scripts ad hoc não são persistentes. Eles não são aplicados a nós de trabalho adicionados ao cluster após o script ter sido executado. É possível promover posteriormente um script ad hoc a um script persistente ou rebaixar um script persistente a um script ad hoc.

  > [!IMPORTANT]  
  > As ações de script usadas durante a criação do cluster são mantidas automaticamente.
  >
  > Os scripts que falham não são persistentes, mesmo que você indique especificamente que eles devem ser.

* Eles podem aceitar parâmetros usados pelo script durante a execução.

* São executados com os privilégios no nível de raiz nos nós de cluster.

* Podem ser usados por meio do Portal do Azure, Azure PowerShell, CLI clássica do Azure ou HDInsight .NET SDK.

O cluster mantém um histórico de todos os scripts que foram executados. O histórico é útil quando você precisa localizar a ID de um script para operações de promoção ou rebaixamento.

> [!IMPORTANT]  
> Não há nenhuma forma automática de desfazer as alterações feitas por uma ação de script. Reverta manualmente as alterações ou forneça um script que as reverta.

### <a name="script-action-in-the-cluster-creation-process"></a>Ação de script no processo de criação do cluster

As ações de script usadas durante a criação do cluster são ligeiramente diferentes das ações de script executadas em um cluster existente:

* O script é automaticamente persistente.

* Uma falha no script pode causar falha no processo de criação do cluster.

O seguinte diagrama ilustra quando a ação de script é executada durante o processo de criação:

![Personalização do cluster HDInsight e estágios durante a criação de cluster][img-hdi-cluster-states]

O script é executado enquanto o HDInsight está sendo configurado. O script é executado em paralelo com todos os nós especificados no cluster. Ele é executado com privilégios de raiz nos nós.

> [!NOTE]  
> Você pode executar operações como parar e iniciar serviços, incluindo serviços relacionados ao Apache Hadoop. Se você interromper quaisquer serviços, deve garantir que o serviço do Ambari e outros serviços relacionados ao Hadoop estejam funcionando antes da conclusão do script. Esses serviços são necessários para determinar com êxito a integridade e o estado do cluster enquanto ele está sendo criado.


Durante a criação do cluster, você pode usar várias ações de script simultaneamente. Esses scripts são invocados na ordem em que eles foram especificados.

> [!IMPORTANT]  
> Ações de script devem ser concluídas em 60 minutos ou atingirão o tempo limite. Durante o provisionamento do cluster, o script é executado simultaneamente com outros processos de instalação e configuração. A competição por recursos, como tempo de CPU ou largura de banda da rede, pode fazer com que o script demore mais para concluir em comparação ao tempo de conclusão no seu ambiente de desenvolvimento.
>
> Para minimizar o tempo necessário de execução do script, evite tarefas como baixar e compilar aplicativos da fonte. Pré-compile aplicativos e armazene o binário no Armazenamento do Azure.


### <a name="script-action-on-a-running-cluster"></a>Ação de script em um cluster em execução

Uma falha em um script executado em um cluster já em execução não faz com que o cluster mude automaticamente para um estado de falha. Quando um script é concluído, o cluster deve retornar a um estado em execução.

> [!IMPORTANT]  
> Mesmo que o cluster esteja em um estado em execução, o script com falha pode apresentar problemas. Por exemplo, um script pode excluir arquivos necessários para o cluster.
>
> Ações de scripts executados com privilégios de raiz. Tenha certeza de que entendeu a função de um script antes de aplicá-lo ao seu cluster.

Quando você aplica um script a um cluster, o estado do cluster muda de **Em execução** para **Aceito**. Em seguida, ele é alterado para **Configuração do HDInsight** e, por fim, de volta para **Em execução**, no caso de scripts bem-sucedidos. O status do script é registrado no histórico de ação de script. Essa informação indica se o script teve êxito ou falha. Por exemplo, o cmdlet `Get-AzHDInsightScriptActionHistory` do PowerShell exibe o status de um script. Isso retorna informações semelhantes ao seguinte texto:

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!IMPORTANT]  
> Se você alterar a senha do usuário (admin) do cluster após sua criação, as ações de script executadas nesse cluster podem falhar. Se houver ações de script persistente direcionadas para nós de trabalho, esses scripts poderão falhar quando você dimensionar o cluster.

## <a name="example-script-action-scripts"></a>Scripts de ação do de exemplo de script

Scripts de ação de script podem ser usados por meio dos utilitários a seguir:

* O Portal do Azure
* Azure PowerShell
* A CLI clássica do Azure
* SDK .NET do HDInsight

O HDInsight fornece scripts para instalar os seguintes componentes nos clusters do HDInsight:

| Nome | script |
| --- | --- |
| Adicionar uma conta de Armazenamento do Azure |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. Confira [Adicionar outras contas de armazenamento ao HDInsight](hdinsight-hadoop-add-storage.md). |
| Instalar o Hue |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. Confira [Instalar e usar o Hue em clusters Hadoop do HDInsight](hdinsight-hadoop-hue-linux.md). |
| Instalar o Giraph |`https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh`. Confira [Instalar o Apache Giraph em clusters Hadoop do HDInsight](hdinsight-hadoop-giraph-install-linux.md). |
| Pré-carregar bibliotecas Hive |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. Confira [Adicionar bibliotecas Apache Hive personalizadas ao criar seu cluster do HDInsight](hdinsight-hadoop-add-hive-libraries.md). |

## <a name="use-a-script-action-during-cluster-creation"></a>Usar uma ação de script durante a criação do cluster

Esta seção explica as diferentes maneiras de usar ações de script ao criar um cluster do HDInsight.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Usar uma ação de script durante a criação do cluster no portal do Azure

1. Comece a criar um cluster conforme descrito em [Configurar clusters no HDInsight com Apache Hadoop, Apache Spark, Apache Kafka e muito mais](hdinsight-hadoop-provision-linux-clusters.md). Durante a criação do cluster, você chega a uma página __Resumo do cluster__. Na página __Resumo do cluster__, selecione o link __Editar__ para __Configurações avançadas__.

    ![Link Configurações avançadas](./media/hdinsight-hadoop-customize-cluster-linux/advanced-settings-link.png)

3. Na seção __Configurações avançadas__, selecione __Ações de script__. Na seção __Ações de script__, selecione __+ Enviar novo__.

    ![Enviar uma nova ação de script](./media/hdinsight-hadoop-customize-cluster-linux/add-script-action.png)

4. Use a entrada __Selecionar um script__ para escolher um script criado previamente. Para usar um script personalizado, selecione __Personalizado__. Em seguida, forneça o __Nome__ e o __URI do script de bash__ para o seu script.

    ![Adicionar um script no formulário selecionar script](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    A tabela a seguir descreve os elementos no formulário:

    | Propriedade | Valor |
    | --- | --- |
    | Selecionar um script | Para usar seu próprio script, selecione __Personalizado__. Caso contrário, selecione um dos scripts fornecidos. |
    | NOME |Especifique um nome para a ação de script. |
    | URI do script Bash |Especificar o URI do script. |
    | Head/Worker/ZooKeeper |Especifique os nós em que o script deve ser executado: **Cabeçalho**, **Trabalho** ou **ZooKeeper**. |
    | parâmetros |Especifique os parâmetros, se exigido pelo script. |

    Use a entrada __Persista essa ação de script__ para garantir que o script seja aplicado durante operações de escalonamento.

5. Selecione __Criar__ para salvar o script. Posteriormente, você pode usar __+ Enviar novo__ para adicionar outro script.

    ![Várias ações de script](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts.png)

    Ao terminar de adicionar scripts, use o botão __Selecionar__ e, em seguida, o botão __Avançar__ para retornar à seção __Resumo do cluster__.

3. Para criar o cluster, selecione __Criar__ na seleção __Resumo do cluster__.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Usar uma ação de script em modelos do Azure Resource Manager

Ações de script podem ser usadas com modelos do Azure Resource Manager. Por exemplo, confira [Criar cluster Linux do HDInsight e executar uma ação de script](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/).

Neste exemplo, a ação de script é adicionada usando o seguinte código:

```json
"scriptActions": [
    {
        "name": "setenvironmentvariable",
        "uri": "[parameters('scriptActionUri')]",
        "parameters": "headnode"
    }
]
```

Saiba mais sobre como implantar um modelo:

* [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

* [Implantar recursos com modelos do Resource Manager e a CLI do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Usar uma ação de script durante a criação do cluster no Azure PowerShell

Nesta seção, você deve usar o [AzHDInsightScriptAction adicionar](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) cmdlet invocar scripts para personalizar um cluster. Antes de começar, instale e configure o Azure PowerShell. Para usar esses comandos do PowerShell, você precisa de [AZ módulo](https://docs.microsoft.com/powershell/azure/overview).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O seguinte script mostra como aplicar uma ação de script ao criar um cluster usando o PowerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Pode levar alguns minutos até que o cluster seja criado.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Usar uma ação de script durante a criação do cluster no SDK do .NET do HDInsight

O SDK .NET do HDInsight fornece bibliotecas de cliente que facilitam o trabalho com o HDInsight por meio de um aplicativo .NET. Para ver um exemplo de código, confira [Criar clusters baseados em Linux no HDInsight usando o SDK .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action).

## <a name="apply-a-script-action-to-a-running-cluster"></a>Aplicar uma ação de script a um cluster em execução

Esta seção explica como aplicar ações de script a um cluster em execução.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Aplicar uma ação de script em um cluster em execução no portal do Azure

Vá para o [Portal do Azure](https://portal.azure.com):

1. No menu esquerdo, selecione **Todos os serviços**.

1. Em **ANÁLISES**, selecione **Clusters do HDInsight**.

1. Selecione seu cluster na lista para abrir a exibição padrão.

1. Na exibição padrão, sob **Configurações**, selecione **Ações de script**.

1. Na parte superior da página **Ações de script**, selecione **+ Enviar novas**.

    ![Adicionar um script a um cluster em execução](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

4. Use a entrada __Selecionar um script__ para escolher um script criado previamente. Para usar um script personalizado, selecione __Personalizado__. Em seguida, forneça o __Nome__ e o __URI do script de bash__ para o seu script.

    ![Adicionar um script no formulário selecionar script](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    A tabela a seguir descreve os elementos no formulário:

    | Propriedade | Valor |
    | --- | --- |
    | Selecionar um script | Para usar seu próprio script, selecione __personalizado__. Caso contrário, selecione um script fornecido. |
    | Nome |Especifique um nome para a ação de script. |
    | URI do script Bash |Especificar o URI do script. |
    | Cabeçalho/Trabalho/Zookeeper |Especifique os nós em que o script deve ser executado: **Cabeçalho**, **Trabalho** ou **ZooKeeper**. |
    | parâmetros |Especifique os parâmetros, se exigido pelo script. |

    Use a entrada __Persistir essa ação de script__ para garantir que o script seja aplicado durante operações de colocação em escala.

5. Por fim, selecione o botão **Criar** para aplicar o script ao cluster.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Aplicar uma ação de script em um cluster em execução no Azure PowerShell

Para usar esses comandos do PowerShell, você precisa de [AZ módulo](https://docs.microsoft.com/powershell/azure/overview).

O seguinte exemplo mostra como aplicar uma ação de script a um cluster em execução:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

Após a conclusão da operação, você receberá informações semelhantes ao seguinte texto:

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Aplicar uma ação de script em um cluster em execução da CLI do Azure

Antes de começar, instale e configure a CLI do Azure. Para saber mais, confira [Instalar a CLI clássica do Azure](https://docs.microsoft.com/cli/azure/install-classic-cli?view=azure-cli-latest).

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

1. Mude para o modo do Azure Resource Manager:

    ```bash
    azure config mode arm
    ```

2. Autentique-se na assinatura do Azure:

    ```bash
    azure login
    ```

3. Aplique uma ação de script a um cluster em execução:

    ```bash
    azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>
    ```

    Se você omitir parâmetros para esse comando, será solicitado a fornecê-los. Caso o script especificado com `-u` aceite parâmetros, especifique-os usando o parâmetro `-p`.

    Os tipos de nós válidos são `headnode`, `workernode` e `zookeeper`. Caso o script deva ser aplicado a vários tipos de nós, especifique os tipos separados por um ponto-e-vírgula `;`. Por exemplo, `-n headnode;workernode`.

    Para persistir o script, adicione `--persistOnSuccess`. Também é possível persistir o script posteriormente usando `azure hdinsight script-action persisted set`.

    Após a conclusão do trabalho, você verá um resultado semelhante a este:

        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>Aplicar uma ação de script a um cluster em execução usando a API REST

Confira [API REST de cluster no Azure HDInsight](https://msdn.microsoft.com/library/azure/mt668441.aspx).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Aplicar uma ação de script a um cluster em execução no SDK do .NET do HDInsight

Para obter um exemplo de como usar o SDK .NET para aplicar scripts a um cluster, confira [Aplicar uma ação de script a um cluster do HDInsight baseado em Linux em execução](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

## <a name="view-history-and-promote-and-demote-script-actions"></a>Exibir o histórico, promover e rebaixar ações de script

### <a name="the-azure-portal"></a>O Portal do Azure

1. Entre no [Portal do Azure](https://portal.azure.com).

1. No menu esquerdo, selecione **Todos os serviços**.

1. Em **ANÁLISES**, selecione **Clusters do HDInsight**.

1. Selecione seu cluster na lista para abrir a exibição padrão.

1. Na exibição padrão, sob **Configurações**, selecione **Ações de script**.

4. Um histórico de scripts para esse cluster é exibido na seção de ações de script. Essas informações incluem uma lista de scripts persistentes. A captura de tela a seguir mostra que o script Solr foi executado nesse cluster. A captura de tela não mostra scripts persistentes.

    ![Ações de script](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

5. Selecione um script do histórico para exibir a seção **Propriedades** desse script. Na parte superior da tela, é possível executar novamente o script ou promovê-lo.

    ![Propriedades de ações de script](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

6. Também é possível usar as reticências ( **…** ) à direita das entradas na seção de ações de script para executar ações.

    ![Ações de script, reticências](./media/hdinsight-hadoop-customize-cluster-linux/deletepromoted.png)

### <a name="azure-powershell"></a>Azure PowerShell

| cmdlet | Função |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |Recuperar informações sobre as ações de script persistentes. |
| `Get-AzHDInsightScriptActionHistory` |Recuperar um histórico das ações de script aplicadas no cluster ou detalhes de um script específico. |
| `Set-AzHDInsightPersistedScriptAction` |Promover uma ação de script ad hoc para uma ação de script persistente. |
| `Remove-AzHDInsightPersistedScriptAction` |Rebaixar uma ação de script persistente a uma ação ad hoc. |

> [!IMPORTANT]  
> `Remove-AzHDInsightPersistedScriptAction` não desfaz as ações realizadas por um script. Esse cmdlet remove apenas o sinalizador persistente.

O exemplo de script a seguir demonstra como usar os cmdlets para promover e depois rebaixar um script.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="the-azure-classic-cli"></a>A CLI clássica do Azure

| cmdlet | Função |
| --- | --- |
| `azure hdinsight script-action persisted list <clustername>` |Recuperar uma lista de ações de script persistente. |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` |Recuperar informações sobre uma ação de script persistente específica. |
| `azure hdinsight script-action history list <clustername>` |Recuperar um histórico das ações de script aplicadas ao cluster. |
| `azure hdinsight script-action history show <clustername> <scriptname>` |Recuperar informações sobre uma ação de script específica. |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` |Promover uma ação de script ad hoc para uma ação de script persistente. |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` |Rebaixar uma ação de script persistente a uma ação ad hoc. |

> [!IMPORTANT]  
> `azure hdinsight script-action persisted delete` não desfaz as ações realizadas por um script. Esse cmdlet remove apenas o sinalizador persistente.

### <a name="the-hdinsight-net-sdk"></a>O SDK .NET do HDInsight

Para obter um exemplo de como usar o SDK .NET para recuperar o histórico de scripts de um cluster, promover ou rebaixar scripts, confira [Aplicar uma ação de script em um cluster do HDInsight baseado em Linux em execução](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [!NOTE]  
> Este exemplo também demonstra como instalar um aplicativo do HDInsight usando o SDK .NET.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Suporte para software livre utilizado em clusters do HDInsight

O serviço Microsoft Azure HDInsight usa um ecossistema de tecnologias de código aberto formado em torno do Apache Hadoop. O Microsoft Azure fornece um nível geral de suporte para tecnologias de software livre. Para saber mais, confira a seção **Escopo do Suporte** nas [Perguntas frequentes sobre o Suporte do Azure](https://azure.microsoft.com/support/faq/). O serviço HDInsight fornece um nível adicional de suporte a componentes internos.

Dois tipos de componentes de software livre estão disponíveis no serviço do HDInsight:

* **Componentes internos**. Esses componentes estão pré-instalados em clusters do HDInsight e fornecem os recursos básicos do cluster. Os seguintes componentes pertencem a essa categoria:

  * [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) ResourceManager.
  * A linguagem de consulta do Hive, [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).
  * [Apache Mahout](https://mahout.apache.org/). 
    
    Uma lista completa de componentes de cluster está disponível em [Quais são os componentes e versões do Apache Hadoop disponíveis com o HDInsight?](hdinsight-component-versioning.md)

* **Componentes personalizados**. Como usuário do cluster, você pode instalar ou usar em sua carga de trabalho qualquer componente disponível na comunidade ou criado por você.

> [!WARNING]  
> Componentes fornecidos com o cluster HDInsight contam com suporte total. O Suporte da Microsoft ajuda a isolar e resolver problemas relacionados a esses componentes.
>
> Componentes personalizados recebem suporte comercial razoável para ajudar você a solucionar o problema. O Suporte da Microsoft pode conseguir resolver o problema. Ou eles podem solicitar que você busque nos canais disponíveis as tecnologias de código-fonte aberto, onde é possível encontrar conhecimento aprofundado sobre essa tecnologia. Muitos sites de comunidades podem ser usados. Por exemplo, o [Fórum do MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) e o [Stack Overflow](https://stackoverflow.com). 
>
> Os projetos do Apache têm sites de projeto no [site do Apache](https://apache.org). Um exemplo é o [Hadoop](https://hadoop.apache.org/).

O serviço HDInsight possibilita o uso de componentes personalizados de várias formas. O mesmo nível de suporte se aplica, independentemente de como um componente é usado ou instalado no cluster. A lista a seguir descreve as formas mais comuns de usar os componentes personalizados em clusters HDInsight:

1. **Envio de trabalhos**. Trabalhos do Hadoop ou de outros tipos que executam ou usam componentes personalizados podem ser enviados para o cluster.

2. **Personalização de clusters**. Durante a criação de clusters, você pode especificar configurações adicionais e componentes personalizados para instalação nos nós de cluster.

3. **Exemplos**. Para componentes personalizados populares, a Microsoft e outras empresas podem fornecer exemplos de uso desses componentes em clusters HDInsight. Esses exemplos são fornecidos sem suporte.

## <a name="troubleshooting"></a>Solução de problemas

Você pode usar a interface de usuário Web do Ambari para exibir as informações registradas pelas ações de script. Se o script falhar durante a criação do cluster, os logs também estarão disponíveis na conta de armazenamento padrão associada ao cluster. Esta seção fornece informações sobre como recuperar os logs usando ambas as opções.

### <a name="the-apache-ambari-web-ui"></a>A interface de usuário Web do Apache Ambari

1. No navegador, acesse https://CLUSTERNAME.azurehdinsight.net. Substitua **CLUSTERNAME** pelo nome do seu cluster HDInsight.

    Quando solicitado, insira o nome de conta de administrador, **admin**, e a senha correspondente para o cluster. Você precisará reinserir as credenciais de administrador em um formulário da Web.

2. Na barra na parte superior da página, selecione a entrada **ops**. Uma lista exibe as operações atuais e anteriores realizadas no cluster por meio do Ambari.

    ![Barra de interface do usuário da Web do Ambari com o item "ops" selecionado](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. Localize as entradas com **run\_customscriptaction** na coluna **Operações**. Essas entradas são criadas quando as ações de script são executadas.

    ![Captura de tela de operações](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

    Para exibir as saídas **STDOUT** e **STDERR**, selecione a entrada **run\customscriptaction** e faça drill down pelos links. Essa saída é gerada ao executar o script e pode conter informações úteis.

### <a name="access-logs-from-the-default-storage-account"></a>Acessar logs na conta de armazenamento padrão

Se a criação do cluster falhar devido a um erro de script, os logs serão mantidos na conta de armazenamento de cluster.

* Os logs de armazenamento estão disponíveis em `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`.

    ![Captura de tela de operações](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

    Nesse diretório, os logs são organizados separadamente em **nó de cabeçalho**, **nó de trabalho** e **nó do zookeeper**. Veja os exemplos a seguir:

    * **Nó de cabeçalho**: `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`

    * **Nó de trabalho**: `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`

    * **Nó do Zookeeper**: `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* Todos os **stdout** e **stderr** do host correspondente são carregados na conta de armazenamento. Há um **output-\*.txt** e um **errors-\*.txt** para cada ação de script. O arquivo **output-*.txt** contém informações sobre o URI do script que foi executado no host. O texto a seguir é um exemplo destas informações:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* É possível criar repetidamente um cluster de ação de script com o mesmo nome. Nesse caso, você pode distinguir os logs relevantes com base no nome da pasta **DATE**. Por exemplo, a estrutura de pastas de um cluster, **mycluster**, criado em datas diferentes será semelhante às seguintes entradas de log:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Se você criar um cluster de ação de script com o mesmo nome no mesmo dia, você poderá usar o prefixo exclusivo para identificar os arquivos de log relevantes.

* Se você criar um cluster perto de 00h00, meia-noite, é possível que os arquivos de log incluam dois dias. Nesses casos, você verá duas pastas com datas diferentes para o mesmo cluster.

* Carregar arquivos de log no contêiner padrão pode levar até cinco minutos, especialmente para clusters grandes. Portanto, caso queira acessar os logs, você não deve excluir imediatamente o cluster se uma ação de script falhar.

### <a name="ambari-watchdog"></a>Ambari Watchdog

> [!WARNING]  
> Não altere a senha do watchdog Ambari, hdinsightwatchdog, no seu cluster do HDInsight baseado em Linux. A alteração da senha para essa conta interrompe a capacidade de executar novas ações de script no cluster HDInsight.

### <a name="cant-import-name-blobservice"></a>Não é possível importar o BlobService de nome

__Sintomas__. Falha ao executar a ação de script. Um texto semelhante ao seguinte erro é exibido quando você visualiza a operação no Ambari:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Causa__. Esse erro ocorre se você atualizar o cliente de Armazenamento do Microsoft Azure do Python incluído com o cluster do HDInsight. O HDInsight espera o cliente de Armazenamento do Azure 0.20.0.

__Resolução__. Para resolver esse erro, conecte-se manualmente a cada nó de cluster usando `ssh`. Execute o seguinte comando para reinstalar a versão de cliente de armazenamento correta:

```bash
sudo pip install azure-storage==0.20.0
```

Para saber mais sobre como se conectar ao cluster com SSH, confira [Conectar ao HDInsight (Apache Hadoop) usando SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>O histórico não mostra os scripts usados durante a criação do cluster

Se o cluster tiver sido criado antes de 15 de março de 2016, talvez você não veja uma entrada no histórico de ação de script. Redimensionar o cluster faz com que os scripts apareçam em um histórico de ação de script.

Há duas exceções:

* O cluster foi criado antes de 1º de setembro de 2015. Esta data é quando as ações de script foram introduzidas. Qualquer cluster criado antes dessa data não poderia ter usado as ações de script para a criação do cluster.

* Você usou várias ações de script durante a criação do cluster. Ou usou o mesmo nome para vários scripts, ou o mesmo nome e o mesmo URI, mas parâmetros diferentes para vários scripts. Nesses casos, você verá o seguinte erro:

    Nenhuma nova ação de script pode ser executada neste cluster devido aos nomes de script conflitantes nos scripts existentes. Os nomes de script fornecidos na criação do cluster devem ser exclusivos. Os scripts existentes são executados após redimensionamento.

## <a name="next-steps"></a>Próximas etapas

* [Desenvolver scripts de ação de script para o HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Instalar e usar o Apache Giraph em clusters do HDInsight](hdinsight-hadoop-giraph-install-linux.md)
* [Acrescentar armazenamento adicional a um cluster HDInsight](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Estágios durante a criação de cluster"
