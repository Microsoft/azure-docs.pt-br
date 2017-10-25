---
title: "Implantar um modelo para os serviços do Azure Machine Learning (versão prévia) | Microsoft Docs"
description: "Este tutorial completo mostra como usar os serviços do Microsoft Azure Machine Learning (versão prévia) de ponta a ponta. Esta é a parte 3, sobre a implantação de modelos."
services: machine-learning
author: raymondl
ms.author: raymondl, aashishb
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: hero-article
ms.date: 09/27/2017
ms.openlocfilehash: 2325d0ffd369d85b9a21e2274a98dcb673d240e7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="classifying-iris-part-3-deploy-a-model"></a>Parte 3 de Classificando a Íris: implantar um modelo
Os serviços do Azure Machine Learning (versão prévia) são uma solução integrada de análise avançada e de ciência de dados de ponta a ponta para cientistas de dados profissionais prepararem dados, desenvolverem testes e implantarem modelos em escala de nuvem.

Este tutorial é a terceira e última parte de uma série. Nessa parte do tutorial, você usará os serviços do Azure Machine Learning (versão prévia) para aprender como:

> [!div class="checklist"]
> * Localizar o arquivo de modelo
> * Gerar um script de pontuação e um arquivo de esquema
> * Preparar o ambiente
> * Criar um serviço Web em tempo real
> * Executar o serviço Web em tempo real
> * Examinar os dados de blob de saída 

 Este tutorial usa o [Conjunto de dados de flor de íris](https://en.wikipedia.org/wiki/iris_flower_data_set) atemporal para manter as coisas simples. As capturas de tela são específicos do Windows, mas a experiência no macOS é quase idêntica.

## <a name="prerequisites"></a>Pré-requisitos
Conclua as primeiras duas partes desta série de tutoriais:

1. Siga o [tutorial Preparar dados](tutorial-classifying-iris-part-1.md) para criar recursos do Azure Machine Learning e instalar o aplicativo Azure Machine Learning Workbench.

2. Siga o [tutorial Criar um modelo](tutorial-classifying-iris-part-2.md) para criar um modelo de regressão logística no Azure Machine Learning.

3. Você precisa do mecanismo do Docker instalado e em execução localmente. Como alternativa, você pode implantar em um cluster do Serviço de Contêiner do Azure no Azure.

## <a name="download-the-model-pickle-file"></a>Baixe o arquivo de pickle do modelo
Na parte anterior do tutorial, o script **iris_sklearn.py** foi executado localmente no Azure Machine Learning Workbench. Essa ação serializou o modelo de regressão logística usando o popular pacote de serialização de objeto do Python **[pickle](https://docs.python.org/2/library/pickle.html)**. 

1. Inicie o aplicativo **Azure Machine Learning Workbench** e abra o projeto **myIris** criado na parte anterior da série de tutorial.

2. Depois que o projeto estiver aberto, clique no botão **Arquivos** (ícone de pasta) na barra de ferramentas à esquerda no Azure Machine Learning Workbench para abrir a lista de arquivos na pasta do projeto.

3. Selecione o arquivo **iris_sklearn.py** e o código Python abrirá em uma nova guia do editor de texto dentro do Workbench.

4. Examine o arquivo **iris_sklearn.py** para ver onde o arquivo pickle foi gerado. Use Control + F para abrir a caixa de diálogo Localizar e, em seguida, localize a palavra **pickle** no código Python.

   Este trecho de código mostra como o arquivo de saída processado pelo pickle foi gerado. Observe que o arquivo pickle de saída é denominado **model.pkl** no disco. 

   ```python
   print("Export the model to model.pkl")
   f = open('./outputs/model.pkl', 'wb')
   pickle.dump(clf1, f)
   f.close()
   ```

5. Localize o arquivo de modelo pickle nos arquivos de saída de uma execução anterior.
   
   Quando você executou o script **iris_sklearn.py**, o arquivo de modelo foi gravado na pasta **outputs** com o nome **model.pkl**. Essa pasta existe no ambiente de execução em que você opta por executar o script e não na pasta local do projeto. 
   
   - Para localizar o arquivo, use o aplicativo Azure Machine Learning Workbench e clique no botão **Execuções** (ícone do relógio) na barra de ferramentas à esquerda para abrir a lista de **Todas as Execuções**.  
   - A guia **Todas as Execuções** será aberta. Na tabela de Execuções, selecione uma das execuções recentes em que o destino era **local** e o nome do script era **iris_sklearn.py**. 
   - A página **Propriedades da Execução** será aberta. No canto superior direito da página, observe a seção **Saídas**. 
   - Baixe o arquivo pickle selecionando a caixa de seleção ao lado do arquivo **model.pkl** e clicando no botão **Baixar**. Salve-o na raiz da pasta do projeto. Ela é necessária em etapas futuras.

   ![Baixar o arquivo pickle](media/tutorial-classifying-iris/download_model.png)

   Leia mais sobre a pasta **outputs** no artigo [Como ler e gravar artigos de dados grandes](how-to-read-write-files.md).

## <a name="get-scoring-and-schema-files"></a>Obter arquivos de esquema e de pontuação
Para implantar o serviço Web você precisará, além do arquivo de modelo, também de um script de pontuação e, opcionalmente, de um esquema para os dados de entrada do serviço Web. O script de pontuação carrega o arquivo **model.pkl** da pasta atual e usa-o para gerar uma classe Íris recentemente prevista.  

1. Inicie o aplicativo **Azure Machine Learning Workbench** e abra o projeto **myIris** criado na parte anterior da série de tutorial.

2. Depois que o projeto estiver aberto, clique no botão **Arquivos** (ícone de pasta) na barra de ferramentas à esquerda no Azure Machine Learning Workbench para abrir a lista de arquivos na pasta do projeto.

3. Selecione o arquivo **iris_score.py**. O script de Python é aberto. Esse arquivo é usado como o arquivo de pontuação.

   ![Arquivo de Pontuação](media/tutorial-classifying-iris/model_data_collection.png)

4. Para obter o arquivo de esquema, execute o script. Escolha o ambiente **local** e o script **iris_score.py** na barra de comandos e, em seguida, clique no botão **Executar**. 

5. Esse script cria um arquivo JSON na pasta **outputs**, que captura o esquema de dados de entrada exigido pelo modelo.

6. Observe o painel Trabalhos à direita da janela do Machine Learning Workbench. Aguarde a versão mais recente do trabalho **iris\_score.py** para exibir status o verde **concluído**. Em seguida, clique no hiperlink **iris\_score.py [1]** da execução de trabalho mais recente para ver os detalhes da execução de **iris_score.py**. 

7. Na página Propriedades de Execução, na seção **Saídas**, selecione o arquivo recém-criado **service_schema.json**. **Verifique** o arquivo e clique em **Baixar**. Salve o arquivo na pasta raiz do projeto.

8. Retorne à guia anterior em que você abriu o script **iris_score.py**. 

   Observe que o uso de coleta de dados que permite que você capture previsões e entradas de modelo do serviço Web. Os pontos a seguir são de especial interesse para a coleta de dados:

9. Examine o código na parte superior da classe de importações de arquivo ModelDataCollector que contém a funcionalidade de coleta de dados de modelo:

   ```python
   from azureml.datacollector import ModelDataCollector
   ```

10. Examine as seguintes linhas de código na função **init()**, que instancia ModelDataCollector:

   ```python
   global inputs_dc, prediction_dc
   inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
   prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")`
   ```

11. Examine as seguintes linhas de código na função **run(input_df)**, que coleta dados de entrada e de previsão:

   ```python
   global clf2, inputs_dc, prediction_dc
   inputs_dc.collect(input_df)
   prediction_dc.collect(pred)
   ```

Agora você está pronto para preparar o ambiente para operacionalizar o modelo.

## <a name="prepare-to-operationalize-locally"></a>Preparar para operacionalizar localmente
Use a implantação de _modo local_ para executar em contêineres do Docker no computador local.

Você pode usar o _modo local_ para desenvolvimento e teste. O mecanismo do Docker deve ser executado localmente para concluir as etapas a seguir para operacionalizar o modelo. Você pode usar o sinalizador `-h` no final dos comandos para obter ajuda sobre o comando.

>[!NOTE]
>Se você não tem o mecanismo do Docker localmente, você ainda pode prosseguir criando um cluster no Azure para implantação. Apenas certifique-se de excluir o cluster após o tutorial, para que você não incorra em encargos contínuos.

1. Abra a interface de linha de comando no Azure Machine Learning Workbench e, no menu Arquivo, clique em **Abrir o Prompt de Comando**.

   O prompt de linha de comando é aberto em sua localização atual da pasta do projeto **c:\temp\myIris>**.

2. Verifique se o provedor de recursos do Azure **Microsoft.ContainerRegistry** está registrado em sua assinatura. Registre este provedor de recursos antes de criar um ambiente na Etapa 3. Você pode verificar se ele já está registrado usando o seguinte comando:
   ``` 
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table 
   ``` 

   Você deve ver uma saída semelhante a: 
   ```
   Provider                                  Status 
   --------                                  ------
   Microsoft.Authorization                   Registered 
   Microsoft.ContainerRegistry               Registered 
   microsoft.insights                        Registered 
   Microsoft.MachineLearningExperimentation  Registered 
   ... 
   ```
   
   Se o **Microsoft.ContainerRegistry** não estiver registrado, você pode registrá-lo usando o seguinte comando:
   ``` 
   az provider register --namespace Microsoft.ContainerRegistry 
   ```
   O registro pode levar alguns minutos e você pode verificar o status dele usando o comando **az provider list** acima ou o comando a seguir:
   ``` 
   az provider show -n Microsoft.ContainerRegistry 
   ``` 

   A terceira linha da saída exibe **"registrationState": "Registrar"**. Aguarde alguns minutos e repita o comando show até que a saída exiba **"registrationState": "Registrado"**.

3. Crie o ambiente. Esta etapa é necessária para ser executada uma vez por ambiente (por exemplo, uma vez para o ambiente de desenvolvimento e uma vez para a produção). Use o _modo local_ para esse primeiro ambiente. (Você pode tentar usar a opção `-c` ou `--cluster` no comando a seguir para configurar um ambiente no _modo de cluster_ posteriormente.)

   ```azurecli
   az ml env setup -n <new deployment environment name> --location <e.g. eastus2>
   ```
   
   Siga as instruções na tela para provisionar uma conta de armazenamento para armazenar imagens do Docker, um ACR (Registro de Contêiner do Azure) para listar as imagens do Docker e uma conta de AppInsight para coletar telemetria. Se você usar a opção `-c`, ele também criará um cluster do ACS (Serviço de Contêiner do Azure).
   
   O nome do cluster é uma maneira de identificar o ambiente. O local deve ser o mesmo que o local da conta de gerenciamento de Modelos criada no Portal do Azure.

4. Criar uma conta de Gerenciamento de Modelos (essa configuração é realizada apenas uma vez)  
   ```azurecli
   az ml account modelmanagement create --location <e.g. eastus2> -n <new model management account name> -g <existing resource group name> --sku-name S1
   ```
   
5. Criar a conta de Gerenciamento de Modelos  
   ```azurecli
   az ml account modelmanagement set -n <youracctname> -g <yourresourcegroupname>
   ```

6. Defina o ambiente.
Depois que a configuração for concluída, defina as variáveis de ambiente necessárias para operacionalizar usando o comando a seguir. Use o mesmo nome de ambiente que você usou anteriormente na etapa 4. Use o mesmo nome de grupo de recursos que foi enviado na janela de comando quando o processo de instalação foi concluído.
   ```azurecli
   az ml env set -n <deployment environment name> -g <existing resource group name>
   ```

7. Para verificar se você configurou corretamente seu ambiente operacionalizado para a implantação de serviço Web local, digite o seguinte comando:

   ```azurecli
   az ml env show
   ```

Agora você está pronto para criar o serviço Web em tempo real.

## <a name="create-a-real-time-web-service-in-one-command"></a>Criar um serviço Web em tempo real em um comando
1. Use o comando a seguir para criar um serviço Web em tempo real:

   ```azurecli
   az ml service create realtime -f iris_score.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
   ```
   Isso gera uma ID de serviço Web que você pode usar mais tarde.

   As opções a seguir são usadas com o comando **az ml service create realtime**:
   * -n: nome do aplicativo, deve ser composto somente de letras minúsculas.
   * -f: nome do arquivo de script de pontuação
   * --model-file: arquivo de modelo, nesse caso é o arquivo processado pelo pickle model.pkl
   * -r: tipo de modelo, nesse caso é um modelo de Python
   * --collect-model-data true: habilita a coleta de dados

   >[!IMPORTANT]
   >O nome do serviço (que também é o novo nome de imagem do Docker) deverá ser composto somente de letras minúsculas, caso contrário, você obterá um erro. 

2. Quando você executar o comando, o modelo e o arquivo de pontuação serão carregados para a conta de armazenamento que você criou como parte da configuração do ambiente. O processo de implantação cria uma imagem do Docker com o modelo, o esquema e o arquivo de pontuação e envia-a por push para o registro do ACR: **\<ACR_name\>.azureacr.io/\<imagename\>:\<version\>**. 

   Em seguida, ele efetua pull dessa imagem localmente para o seu computador e inicia um contêiner do Docker com base nessa imagem. Se seu ambiente estiver configurado no modo de cluster, o contêiner do Docker será implantado no cluster Kubernetes ACS em vez disso.

   Como parte da implantação, um ponto de extremidade REST HTTP para o serviço Web é criado no computador local. Depois de alguns minutos, o comando deverá terminar com uma mensagem de êxito e o serviço Web estará pronto para a ação!

3. Você pode ver o contêiner do Docker em execução usando o comando **docker ps**:
   ```azurecli
   docker ps
   ```

## <a name="create-a-real-time-web-service-using-separate-commands"></a>Criar um serviço Web em tempo real usando comandos separados
Como uma alternativa para o comando **az ml service create realtime** mostrado acima, você também pode executar as etapas separadamente. Primeiro, faça o registro do modelo, em seguida, gere o manifesto, compile a imagem do Docker e crie o serviço Web. Essa abordagem passo a passo fornece mais flexibilidade em cada etapa. Além disso, você pode reutilizar entidades geradas na etapa anterior e recompilar entidades apenas quando necessário.

1. Registre o modelo, fornecendo o nome do arquivo pickle.

   ```azurecli
   az ml model register --model model.pkl --name model.pkl
   ```
   Este comando gera uma ID de modelo.

2. Criar manifesto

   Para criar um manifesto, use este comando e forneça a saída de ID de modelo da etapa anterior:

   ```azurecli
   az ml manifest create --manifest-name <new manifest name> -f iris_score.py -r python -i <model ID> -s service_schema.json
   ```
   Este comando gera uma ID de manifesto.

3. Criar uma imagem do Docker

   Para criar uma imagem do Docker, use este comando e forneça a saída do valor da ID de manifesto da etapa anterior:

   ```azurecli
   az ml image create -n irisimage --manifest-id <manifest ID>
   ```
   Este comando gera uma ID de imagem do Docker.
   
4. Criar o serviço

   Para criar um serviço, use o comando listado e forneça a saída do valor da ID de imagem da etapa anterior:

   ```azurecli
   az ml service create realtime --image-id <image ID> -n irisapp --collect-model-data true
   ```
   Este comando gera uma ID de serviço Web.

Agora você está pronto para executar o serviço Web.

## <a name="run-the-real-time-web-service"></a>Executar o serviço Web em tempo real

Teste o serviço Web **irisapp** em execução alimentando-o com um registro codificado por JSON contendo uma matriz de quatro números aleatórios.

1. A criação de serviço Web incluiu dados de exemplo. Ao executar em modo local, você pode chamar o comando **az ml service show realtime**. Essa chamada recupera um exemplo de comando de execução útil para testar o serviço. Isso também recupera a URL de pontuação que você poderá usar para incorporar o serviço em seu próprio aplicativo personalizado:

   ```azurecli
   az ml service show realtime -i <web service ID>
   ```

2. Para testar o serviço, execute o comando de execução de serviço retornado.

   ```azurecli
   az ml service run realtime -i irisapp -d "{\"input_df\": [{\"petal width\": 0.25, \"sepal length\": 3.0, \"sepal width\": 3.6, \"petal length\": 1.3}]}"
   ```
   A saída é **"2",**, que é a classe prevista. (O seu resultado pode ser diferente.) 

3. Se você quiser executar o serviço de fora da CLI, você precisará obter as chaves para autenticação:

   ```azurecli
   az ml service keys realtime -i <web service ID>
   ```

## <a name="view-the-collected-data-in-azure-blob-storage"></a>Exiba os dados coletados no Armazenamento de Blobs do Azure

1. Faça logon no [Portal do Azure](https://portal.azure.com).

2. Localize todas as suas contas de armazenamento. Para fazer isso, clique em **Mais Serviços**

3. Na caixa de pesquisa, digite **Contas de armazenamento** e pressione **Enter**

4. Da página de pesquisa **Contas de armazenamento**, selecione o recurso de **Conta de armazenamento** correspondente ao seu ambiente. 

   > [!TIP]
   > Para determinar qual conta de armazenamento é usada: abra o Azure Machine Learning Workbench, selecione o projeto em que você está trabalhando e abra o prompt de linha de comando do menu **Arquivo**. No prompt de linha de comando, digite `az ml env show -v` e verifique o valor *storage_account*. Esse é o nome da sua conta de armazenamento

5. Uma vez que a página **Conta de armazenamento** se abrir, clique no item **Contêineres** na listagem à esquerda. Localize o contêiner nomeado **modeldata**. 
 
   Se você não vir todos os dados, você precisará aguardar até 10 minutos após a primeira solicitação de serviço Web para ver o início da propagação dos dados para a conta de armazenamento.

   Os dados fluem para blobs com o seguinte caminho de contêiner:

   ```
   /modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv
   ```

6. Você pode consumir esses dados nos blobs do Azure. Há uma variedade de ferramentas usando tanto o software da Microsoft quanto ferramentas de código-fonte aberto, como:

   - Azure ML Workbench: abra o arquivo csv no Azure ML Workbench, adicionando o arquivo csv como uma fonte de dados. 
   - O Excel: abra os arquivos csv diários como uma planilha.
   - [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/): crie gráficos contendo dados extraídos por pull de dados csv em blobs.
   - [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started): carregue dados csv em uma tabela de Hive e execute consultas SQL diretamente no blob.
   - [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview): crie um dataframe com uma grande porção de dados csv.

      ```python
      var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
      ```


## <a name="next-steps"></a>Próximas etapas
Nessa terceira parte da série de tutoriais de três partes, você aprendeu a usar os serviços do Azure Machine Learning para:
> [!div class="checklist"]
> * Localizar o arquivo de modelo
> * Gerar um script de pontuação e um arquivo de esquema
> * Preparar o ambiente
> * Criar um serviço Web em tempo real
> * Executar o serviço Web em tempo real
> * Examinar os dados de blob de saída 

Você executou com êxito um script de treinamento em vários ambientes de computação, criou um modelo, serializou esse modelo e operacionalizou-o por meio de um serviço Web baseado em Docker. 

Você está pronto para realizar preparação de dados avançada:
> [!div class="nextstepaction"]
> [Preparação de dados avançada](tutorial-bikeshare-dataprep.md)

