---
title: Transformar dados usando o Hive na Rede Virtual do Azure | Microsoft Docs
description: Este tutorial fornece instruções passo a passo para transformar dados usando a Atividade Hive no Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/04/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: b6e57500da0ca863f0c5810f625d6a4b0c56d1bf
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277471"
---
# <a name="transform-data-in-azure-virtual-network-using-hive-activity-in-azure-data-factory"></a>Transformar dados na Rede Virtual do Azure usando a Atividade Hive no Azure Data Factory
Neste tutorial, você pode usar o portal do Azure para criar um pipeline do Data Factory que transforma dados usando a atividade Hive em um cluster HDInsight que está em uma Rede Virtual (VNet) do Azure. Neste tutorial, você realizará os seguintes procedimentos:

> [!div class="checklist"]
> * Criar um data factory. 
> * Criar um Integration Runtime auto-hospedado
> * Criar serviços vinculados do Armazenamento do Azure e do Azure HDInsight
> * Criar um pipeline com atividade do Hive.
> * Dispare uma execução de pipeline.
> * Monitorar a execução de pipeline 
> * Verificar a saída

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Conta de Armazenamento do Azure**. Você cria um script Hive e carrega-o no Armazenamento do Azure. A saída do script Hive é armazenada nessa conta de armazenamento. Nessa amostra, o cluster HDInsight usa essa conta de Armazenamento do Azure como o armazenamento primário. 
- **Rede Virtual do Azure.** Se você não tem uma Rede Virtual do Azure, crie-a seguindo [estas instruções](../virtual-network/quick-create-portal.md). Nessa amostra, o HDInsight está em uma Rede Virtual do Azure. Aqui está uma amostra de configuração de Rede Virtual do Azure. 

    ![Criar rede virtual](media/tutorial-transform-data-using-hive-in-vnet-portal/create-virtual-network.png)
- **Cluster HDInsight.** Crie um cluster HDInsight e ingresse-o na rede virtual criada na etapa anterior seguindo este artigo: [Estender o Azure HDInsight usando uma Rede Virtual do Azure](../hdinsight/hdinsight-extend-hadoop-virtual-network.md). Aqui está uma amostra de configuração do HDInsight em uma Rede Virtual do Azure. 

    ![HDInsight em uma rede virtual](media/tutorial-transform-data-using-hive-in-vnet-portal/hdinsight-virtual-network-settings.png)
- **Azure PowerShell**. Siga as instruções em [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-Az-ps).
- **Uma máquina virtual**. Crie uma máquina virtual do Azure e ingresse-a na mesma rede virtual que contém seu cluster HDInsight. Para obter detalhes, consulte [Como criar máquinas virtuais](../virtual-network/quick-create-portal.md#create-virtual-machines). 

### <a name="upload-hive-script-to-your-blob-storage-account"></a>Carregar o script Hive em sua conta de Armazenamento de Blobs

1. Crie um arquivo Hive SQL chamado **hivescript.hql** com o seguinte conteúdo:

   ```sql
   DROP TABLE IF EXISTS HiveSampleOut; 
   CREATE EXTERNAL TABLE HiveSampleOut (clientid string, market string, devicemodel string, state string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' 
   STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

   INSERT OVERWRITE TABLE HiveSampleOut
   Select 
       clientid,
       market,
       devicemodel,
       state
   FROM hivesampletable
   ```
2. No seu Armazenamento de Blobs do Azure, crie um contêiner denominado **adftutorial** se ele não existir.
3. Crie uma pasta chamada **hivescripts**.
4. Carregar o arquivo **hivescript.hql** na subpasta **hivescripts**.

## <a name="create-a-data-factory"></a>Criar uma data factory

1. Iniciar o navegador da Web **Microsoft Edge** ou **Google Chrome**. Atualmente, a interface do usuário do Data Factory tem suporte apenas nos navegadores da Web Microsoft Edge e Google Chrome.
1. Faça logon no [Portal do Azure](https://portal.azure.com/).    
2. Clique em **Novo** no menu à esquerda, clique em **Dados + Análise** e clique em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-data-factory-menu.png)
3. Na página **Novo data factory**, insira **ADFTutorialHiveFactory** como o **nome**. 
      
     ![Página de novo data factory](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-azure-data-factory.png)
 
   O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se você receber o erro a seguir, altere o nome do data factory (por exemplo, yournameMyAzureSsisDataFactory) e tente criar novamente. Confira o artigo [Data Factory - regras de nomenclatura](naming-rules.md) para ver as regras de nomenclatura para artefatos do Data Factory.
  
       `Data factory name “MyAzureSsisDataFactory” is not available`
3. Selecione a **assinatura** do Azure na qual você deseja criar o data factory. 
4. Para o **Grupo de Recursos**, execute uma das seguintes etapas:
     
   - Selecione **Usar existente**e selecione um grupo de recursos existente na lista suspensa. 
   - Selecione **Criar novo**e insira o nome de um grupo de recursos.   
         
     Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
4. Selecione **V2** para a **versão**.
5. Selecione o **local** do data factory. Somente os locais com suporte para a criação de data factories são mostrados na lista.
6. Selecione **Fixar no painel**.     
7. Clique em **Criar**.
8. No painel, você deve ver o seguinte bloco com status: **Implantando data factory**. 

     ![implantando bloco data factory](media/tutorial-transform-data-using-hive-in-vnet-portal/deploying-data-factory.png)
9. Após a criação, a página do **Data Factory** será exibida conforme mostrado na imagem.
   
    ![Página inicial do data factory](./media/tutorial-transform-data-using-hive-in-vnet-portal/data-factory-home-page.png)
10. Clique em **Criar e Monitorar** para iniciar a interface do usuário (IU) do Azure Data Factory em uma guia separada.
11. Na página **introdução**, acesse a guia **Editar** no painel esquerdo, conforme mostrado na imagem a seguir: 

    ![Guia Editar](./media/tutorial-transform-data-using-hive-in-vnet-portal/get-started-page.png)

## <a name="create-a-self-hosted-integration-runtime"></a>Criar um Integration Runtime auto-hospedado
Como o cluster Hadoop está em uma rede virtual, você precisa instalar um tempo de execução de integração auto-hospedado (IR) na mesma rede virtual. Nesta seção, crie uma nova VM, associe-a à mesma rede virtual e instale o IR auto-hospedado nela. O IR auto-hospedado permite que o serviço Data Factory distribua as solicitações de processamento para um serviço de computação como o HDInsight em uma rede virtual. Ele também permite mover dados para/de armazenamentos de dados de uma rede virtual para o Azure. Você usa um IR auto-hospedado quando a computação ou o armazenamento de dados estão em um ambiente local também. 

1. Na interface do usuário do Azure Data Factory, clique em **Conexões** na parte inferior da janela, alterne para a guia **Tempos de Execução de Integração** e, em seguida, clique em **+ Novo** na barra de ferramentas. 

   ![Menu Novo tempo de execução de integração](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-integration-runtime-menu.png)
2. Na janela **Instalação do Integration Runtime**, selecione a opção **Executar atividades de movimentação e distribuição de dados para cálculos externos** e clique em **Avançar**. 

   ![Selecione executar a opção de atividades de movimentação e a distribuição de dados](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-perform-data-movement-compute-option.png)
3. Selecione **Rede Privada** e clique em **Avançar**.
    
   ![Selecionar rede privada](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-private-network.png)
4. Insira **MySelfHostedIR** como **Nome** e clique em **Avançar**. 

   ![Especificar nome do tempo de execução de integração](./media/tutorial-transform-data-using-hive-in-vnet-portal/integration-runtime-name.png) 
5. Copie a **chave de autenticação** do tempo de execução de integração clicando no botão de cópia e salve-a. Mantenha a janela aberta. Você pode usar essa chave para registrar o IR instalado em uma máquina virtual. 

   ![Copiar chave de autenticação](./media/tutorial-transform-data-using-hive-in-vnet-portal/copy-key.png)

### <a name="install-ir-on-a-virtual-machine"></a>Instalar o IR em uma máquina virtual

1. Na VM do Azure, baixe o [Integration Runtime auto-hospedado](https://www.microsoft.com/download/details.aspx?id=39717). Use a **chave de autenticação** obtida na etapa anterior para registrar manualmente o tempo de execução de integração auto-hospedado. 

    ![Registrar Integration Runtime](media/tutorial-transform-data-using-hive-in-vnet-portal/register-integration-runtime.png)

2. Você verá a seguinte mensagem quando o tempo de execução de integração auto-hospedado for registrado com êxito. 
   
    ![Registrado com êxito](media/tutorial-transform-data-using-hive-in-vnet-portal/registered-successfully.png)
3. Clique em **Iniciar Configuration Manager**. Você verá a página a seguir quando o nó estiver conectado ao serviço de nuvem: 
   
    ![O nó está conectado](media/tutorial-transform-data-using-hive-in-vnet-portal/node-is-connected.png)

### <a name="self-hosted-ir-in-the-azure-data-factory-ui"></a>IR auto-hospedado na interface do usuário do Azure Data Factory

1. Na **interface do usuário do Azure Data Factory**, você deverá ver o nome do nome da VM auto-hospedada e seu status.

   ![Nós auto-hospedados existentes](./media/tutorial-transform-data-using-hive-in-vnet-portal/existing-self-hosted-nodes.png)
2. Clique em **Concluir** para fechar a janela **Configuração do Tempo de Execução de Integração**. Você vê o IR auto-hospedado na lista de tempos de execução de integração.

   ![IR auto-hospedado na lista](./media/tutorial-transform-data-using-hive-in-vnet-portal/self-hosted-ir-in-list.png)


## <a name="create-linked-services"></a>Criar serviços vinculados

Você cria e implanta dois serviços vinculados nesta seção:
- Um **serviço vinculado do Armazenamento do Azure** que vincula uma conta de Armazenamento do Azure ao data factory. Esse armazenamento é o armazenamento primário usado por seu cluster HDInsight. Nesse caso, você também pode usar essa conta do Armazenamento do Azure para manter o script Hive e a saída do script.
- Um **Serviço Vinculado do HDInsight**. O Azure Data Factory envia o script Hive para este cluster do HDInsight para execução.

### <a name="create-azure-storage-linked-service"></a>Criar o serviço vinculado do armazenamento do Azure

1. Alterne para a guia **Serviços Vinculados** e, em seguida, clique em **Novo**.

   ![Botão Novo serviço vinculado](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-linked-service.png)    
2. Na página **Novo Serviço Vinculado**, selecione **Armazenamento de Blobs do Azure** e clique em **Continuar**. 

   ![Selecionar Armazenamento de Blobs do Azure](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-azure-storage.png)
3. Na janela **Novo serviço vinculado** execute as seguintes etapas:

    1. Insira **AzureStorageLinkedService** como o **Nome**.
    2. Selecione **MySelfHostedIR** para **Conectar por meio do tempo de execução de integração**.
    3. Selecione sua conta de armazenamento do Azure como o **Nome da conta de armazenamento**. 
    4. Clique em **Testar conectividade** para testar a conexão à Conta do Armazenamento do Azure.
    5. Clique em **Save** (Salvar).
   
        ![Especificar conta do Armazenamento de Blobs do Azure](./media/tutorial-transform-data-using-hive-in-vnet-portal/specify-azure-storage-account.png)

### <a name="create-hdinsight-linked-service"></a>Criar o serviço vinculado ao HDInsight

1. Clique em **Novo** outra vez para criar outro serviço vinculado. 
    
   ![Botão Novo serviço vinculado](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-linked-service.png)    
2. Alterne para a guia **Computação**, selecione **Azure HDInsight** e clique em **Continuar**.

    ![Selecione Azure HDInsight](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-hdinsight.png)
3. Na janela **Novo serviço vinculado** execute as seguintes etapas:

    1. Insira **AzureHDInsightLinkedService** no **Nome**.
    2. Selecione **Traga seu próprio HDInsight**. 
    3. Selecione o cluster HDInsight para **Cluster Hdi**. 
    4. Insira o **nome de usuário** para o cluster HDInsight.
    5. Insira a **senha** para o usuário. 
    
        ![Configurações do Azure HDInsight](./media/tutorial-transform-data-using-hive-in-vnet-portal/specify-azure-hdinsight.png)

Este artigo pressupõe que você tenha acesso ao cluster via Internet. Por exemplo, que você possa se conectar ao cluster em `https://clustername.azurehdinsight.net`. Esse endereço usa o gateway público, que não estará disponível se você tiver usado NSGs (Grupos de Segurança de Rede) ou UDRs (rotas definidas pelo usuário) para restringir o acesso da Internet. Para que o Data Factory possa enviar trabalhos para o cluster HDInsight na Rede Virtual do Azure, você precisa configurar sua Rede Virtual do Azure de modo que a URL possa ser resolvida para o endereço IP do gateway usado pelo HDInsight.

1. No Portal do Azure, abra a Rede Virtual que contém o HDInsight. Abra o adaptador de rede cujo nome começa com `nic-gateway-0`. Anote o endereço IP privado dela. Por exemplo, 10.6.0.15. 
2. Se sua Rede Virtual do Azure tem um servidor DNS, atualize o registro DNS de modo que a URL do cluster do HDInsight `https://<clustername>.azurehdinsight.net` possa ser resolvida para `10.6.0.15`. Se você não tiver um servidor DNS em sua Rede Virtual do Azure, você poderá usar uma solução alternativa temporária para isso editando o arquivo de hosts (C:\Windows\System32\drivers\etc.) de todas as VMs registradas como nós de tempo de execução auto-hospedado, adicionando uma entrada semelhante a esta: 

    `10.6.0.15 myHDIClusterName.azurehdinsight.net`

## <a name="create-a-pipeline"></a>Criar um pipeline 
Nesta etapa, você cria um pipeline com uma atividade Hive. A atividade executa o script do Hive para retornar dados de uma tabela de exemplo e salvá-los em um caminho que você definiu.

Observe os seguintes pontos:

- **scriptPath** aponta para o caminho para o script Hive na conta de Armazenamento do Azure que você usou para MyStorageLinkedService. O caminho diferencia maiúsculas de minúsculas.
- **Output** é um argumento usado no script Hive. Use o formato de `wasbs://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/` para apontá-lo para uma pasta existente no seu Armazenamento do Azure. O caminho diferencia maiúsculas de minúsculas. 

1. Na interface de usuário do Data Factory, clique em **+ (adição)** no painel esquerdo e clique em **Pipeline**. 

    ![Menu de novo pipeline](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-pipeline-menu.png)
2. Na caixa de ferramentas **Atividades**, expanda **HDInsight**e arraste e solte a atividade **Hive** para a superfície do designer de pipeline. 

    ![atividade de arrastar e soltar do Hive](./media/tutorial-transform-data-using-hive-in-vnet-portal/drag-drop-hive-activity.png)
3. Na janela de propriedades, alterne para a guia **Cluster HDI** e selecione **AzureHDInsightLinkedService** para **Serviço Vinculado do HDInsight**.

    ![Selecionar serviço vinculado do HDInsight](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-hdinsight-linked-service.png)
4. Alterne para a guia **Scripts** e siga estas etapas: 

    1. Selecione **AzureStorageLinkedService** como **Serviço Vinculado de Script**. 
    2. Para **Caminho do Arquivo**, clique em **Procurar Armazenamento**. 
 
        ![Procurar armazenamento](./media/tutorial-transform-data-using-hive-in-vnet-portal/browse-storage-hive-script.png)
    3. Na janela **Escolher um arquivo ou uma pasta**, navegue até a pasta **hivescripts** no contêiner **adftutorial**, selecione o arquivo **hivescript.hql** e clique em **Concluir**.  
        
        ![Escolher um arquivo ou uma pasta](./media/tutorial-transform-data-using-hive-in-vnet-portal/choose-file-folder.png) 
    4. Confirme que você vê **adftutorial/hivescripts/hivescript.hql** em **Caminho do arquivo**.

        ![Configurações de script](./media/tutorial-transform-data-using-hive-in-vnet-portal/confirm-hive-script-settings.png)
    5. Na **guia Script**, expanda a seção **Avançado**. 
    6. Clique em **Preenchimento automático desde o script** para **Parâmetros**. 
    7. Insira o valor para o parâmetro **Output** no seguinte formato: `wasbs://<Blob Container>@<StorageAccount>.blob.core.windows.net/outputfolder/`. Por exemplo: `wasbs://adftutorial@mystorageaccount.blob.core.windows.net/outputfolder/`.
 
        ![Argumentos de script](./media/tutorial-transform-data-using-hive-in-vnet-portal/script-arguments.png)
1. Para publicar artefatos no Data Factory, clique em **Publicar**.

    ![Publicar](./media/tutorial-transform-data-using-hive-in-vnet-portal/publish.png)

## <a name="trigger-a-pipeline-run"></a>Disparar uma execução de pipeline

1. Primeiro, valide o pipeline clicando no botão **Validar** na barra de ferramentas. Feche a janela **Saída da Validação do Pipeline** clicando na **seta para a direita (>>)** . 

    ![Validar o pipeline](./media/tutorial-transform-data-using-hive-in-vnet-portal/validate-pipeline.png) 
2. Para disparar uma execução de pipeline, clique em Disparar na barra de ferramentas e em Disparar Agora. 

    ![Disparar agora](./media/tutorial-transform-data-using-hive-in-vnet-portal/trigger-now-menu.png)

## <a name="monitor-the-pipeline-run"></a>Monitorar a execução de pipeline

1. Alterne para a guia **Monitorar** à esquerda. Você vê uma execução de pipeline na lista **Execuções de Pipeline**. 

    ![Monitorar execuções de pipeline](./media/tutorial-transform-data-using-hive-in-vnet-portal/monitor-pipeline-runs.png)
2. Para atualizar a lista, clique em **Atualizar**.
4. Para exibir execuções de atividade associadas com a execução do pipeline, clique em **Exibir execuções de atividade** na coluna **Ação**. Outros links de ação são para interromper/executar novamente o pipeline. 

    ![Exibir execuções de atividade](./media/tutorial-transform-data-using-hive-in-vnet-portal/view-activity-runs-link.png)
5. Você vê somente as execuções de atividade, já que há apenas uma atividade no pipeline do tipo **HDInsightHive**. Para voltar para o modo de exibição anterior, clique no link **Pipelines** na parte superior.

    ![Execuções de atividade](./media/tutorial-transform-data-using-hive-in-vnet-portal/view-activity-runs.png)
6. Confirme que você vê um arquivo de saída na pasta **outputfolder** do contêiner **adftutorial**. 

    ![Arquivo de saída](./media/tutorial-transform-data-using-hive-in-vnet-portal/output-file.png)

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você realizará os seguintes procedimentos: 

> [!div class="checklist"]
> * Criar um data factory. 
> * Criar um Integration Runtime auto-hospedado
> * Criar serviços vinculados do Armazenamento do Azure e do Azure HDInsight
> * Criar um pipeline com atividade do Hive.
> * Dispare uma execução de pipeline.
> * Monitorar a execução de pipeline 
> * Verificar a saída

Avance para o tutorial a seguir para saber mais sobre como transformar dados usando um cluster Spark no Azure:

> [!div class="nextstepaction"]
>[Ramificação e encadeamento de fluxo de controle de Data Factory](tutorial-control-flow-portal.md)



