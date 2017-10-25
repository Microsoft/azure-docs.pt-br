---
title: "Criar um modelo para os serviços do Azure Machine Learning (versão prévia) | Microsoft Docs"
description: "Este tutorial completo mostra como usar os serviços do Microsoft Azure Machine Learning (versão prévia) de ponta a ponta. Esta é a parte 2 em experimentação."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: hero-article
ms.date: 09/25/2017
ms.openlocfilehash: 273b94e9a53e36d7e8c699d3b7fa4305b881a001
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="classifying-iris-part-2-build-a-model"></a>Parte 2 de Classificando a Íris: criar um modelo
Os serviços do Azure Machine Learning (versão prévia) são uma solução integrada de análise avançada e de ciência de dados de ponta a ponta para cientistas de dados profissionais prepararem dados, desenvolverem testes e implantarem modelos em escala de nuvem.

Este tutorial é a parte dois de uma série de três partes. Nessa parte do tutorial, você usará os serviços do Azure Machine Learning (versão prévia) para aprender como:

> [!div class="checklist"]
> * Trabalhar no Azure Machine Learning Workbench
> * Abrir os scripts e examinar o código
> * Executar scripts em um ambiente local
> * Examinar o histórico de execuções
> * Executar scripts em um ambiente do Docker local
> * Executar scripts em uma janela de CLI do Azure local
> * Executar scripts em um ambiente do Docker remoto
> * Executar scripts em um ambiente do HDInsight na nuvem

Este tutorial usa o [Conjunto de dados de flor de íris](https://en.wikipedia.org/wiki/Iris_flower_data_set) atemporal para manter as coisas simples. As capturas de tela são específicos do Windows, mas a experiência no macOS é quase idêntica.

## <a name="prerequisites"></a>Pré-requisitos
Você deve concluir a primeira parte desta série de tutoriais. Siga o [tutorial Preparar dados](tutorial-classifying-iris-part-1.md) para criar recursos do Azure Machine Learning e instalar o aplicativo Azure Machine Learning Workbench antes de iniciar as etapas neste tutorial.

Opcionalmente, você pode fazer experiências com a execução de scripts em um contêiner do Docker local. Para fazer isso, você precisará de um engine do Docker (o Community Edition é suficiente) instalado e iniciado localmente no seu computador Windows ou macOS. Leia mais sobre a [instrução de instalação do Docker](https://docs.docker.com/engine/installation/).

Se você quiser fazer experiências com o script de expedição a ser executado em um contêiner do Docker em uma VM do Azure remota, ou em um cluster Spark do HDInsight, siga as [instruções para criar uma Máquina Virtual de Ciência de Dados do Azure ou um Cluster HDI baseado em Ubuntu](how-to-create-dsvm-hdi.md).

## <a name="review-irissklearnpy-and-configuration-files"></a>Examine os arquivos de configuração e iris_sklearn.py
1. Inicie o aplicativo **Azure Machine Learning Workbench** e abra o projeto **myIris** criado na parte anterior da série de tutorial.

2. Depois que o projeto estiver aberto, clique no botão **Arquivos** (ícone de pasta) na barra de ferramentas à esquerda no Azure Machine Learning Workbench para abrir a lista de arquivos na pasta do projeto.

3. Selecione o arquivo **iris_sklearn.py** e o código Python abrirá em uma nova guia do editor de texto dentro do Workbench.

   ![abrir arquivo](media/tutorial-classifying-iris/open_iris_sklearn.png)

   >[!NOTE]
   >O código que você vê pode não ser exatamente o mesmo que o código anterior, já que este projeto de exemplo é atualizado com frequência.

4. Examine o código de script de Python para se familiarizar com o estilo de codificação. Observe que esse script executa as seguintes tarefas:

   - Carrega o pacote de preparação de dados **iris.dprep** para criar um [DataFrame pandas](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html). 

        >[!NOTE]
        >Estamos usando o pacote de preparação de dados `iris.dprep` que é fornecido com o projeto de exemplo, que deve ser o mesmo que o arquivo `iris-1.dprep` criado na parte 1 deste tutorial.

   - Adiciona recursos aleatórios para tornar o problema mais difícil de resolver. (A aleatoriedade é necessária porque o Íris é um pequeno conjunto de dados que pode ser facilmente classificado com quase 100% de precisão.)

   - Usa a biblioteca de aprendizado de máquina [scikit-learn](http://scikit-learn.org/stable/index.html) para criar um modelo de regressão logística simples. 

   - Serializa o modelo usando a biblioteca [pickle](https://docs.python.org/2/library/pickle.html) em um arquivo na pasta `outputs`, então carrega-o e desserializa-o de volta para a memória

   - Usa o modelo desserializado para fazer a previsão em um novo registro. 

   - Plota dois gráficos – matriz de confusão e curva ROC multiclasse – usando a biblioteca [matplotlib](https://matplotlib.org/) e salva-os na pasta `outputs`.

   - O objeto `run_logger` é usado durante todo esse processo para registrar a taxa de regularização e a precisão do modelo, sendo que os logs são plotados automaticamente no histórico de execuções.


## <a name="execute-irissklearnpy-script-in-local-environment"></a>Executar o script iris_sklearn.py no ambiente local

Nos prepararemos para executar o script **iris_sklearn.py** pela primeira vez. Este script requer os pacotes scikit-learn e matplotlib. O **scikit-learn** já está instalado pelo Azure ML Workbench. No entanto, precisamos instalar o **matplotlib**. 

1. No Azure Machine Learning Workbench, clique no menu **Arquivo** e escolha **Abrir o Prompt de Comando** para iniciar o prompt de comando. Nos referimos a esta janela de interface de linha de comando como a janela de CLI de Azure Machine Learning Workbench ou, abreviando, janela de CLI.

2. Na janela de CLI, digite o seguinte comando para instalar o pacote **matplotlib** do Python. Isso deve ser concluído em menos de um minuto.

   ```azurecli
   pip install matplotlib
   ```

   >[!NOTE]
   >Se você ignorar o comando `pip install` acima, o código em `iris_sklearn.py` será executado com êxito, mas não produzirá os gráficos da saída de matriz de confusão e da curva ROC multiclasse conforme mostrado nas visualizações de histórico.

3. Retorne para a janela de aplicativo do Workbench. 

4. No canto superior esquerdo da guia **iris_sklearn.py**, ao lado do ícone salvar, clique na lista suspensa para escolher a seleção **Configuração de Execução**.  Escolha **local** como o ambiente de execução e `iris_sklearn.py` como o script a executar.

5. Em seguida, indo para a direita da mesma guia, preencha o campo **argumentos** com um valor de `0.01`. 

   ![img](media/tutorial-classifying-iris/run_control.png)

6. Clique no botão **Executar**. Um trabalho é agendado imediatamente. O trabalho é listado no painel **Trabalhos** no lado direito da janela do Workbench. 

7. Após alguns instantes, o status do trabalho transiciona de **Enviando** para **Executando** e, finalmente, para **Concluído** logo depois.

   ![executar sklearn](media/tutorial-classifying-iris/run_sklearn.png)

8. Clique na palavra **Concluído** no texto de status do trabalho no painel Trabalhos. Uma janela pop-up é aberta e exibe o texto de saída padrão (stdout) do script em execução. Para fechar o texto stdout, clique no botão **X** no canto superior direito do pop-up.

9. No mesmo status de trabalho no painel de tarefas, clique no texto azul **iris_sklearn.py [n]** (_n_ é o número da execução) imediatamente acima do status **Concluído** e da hora de início. A página **Propriedades da Execução** é aberta e mostra as informações de Propriedades da Execução, arquivos de **Saídas**, quaisquer eventuais **Visualizações** e **Logs** dessa execução específica. 

   Quando a execução é concluída, a janela pop-up mostra os seguintes resultados:

   >[!NOTE]
   >Já que introduzimos algum caráter aleatório ao conjunto de treinamento definido anteriormente, os resultados exatos variam um pouco.

   ```text
   Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  5 2016, 11:41:13) [MSC v.1900 64 bit (AMD64)]
   
   Iris dataset shape: (150, 5)
   Regularization rate is 0.01
   LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
          intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
          penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
          verbose=0, warm_start=False)
   Accuracy is 0.6792452830188679
   
   ==========================================
   Serialize and deserialize using the outputs folder.
   
   Export the model to model.pkl
   Import the model from model.pkl
   New sample: [[3.0, 3.6, 1.3, 0.25]]
   Predicted class is ['Iris-setosa']
   Plotting confusion matrix...
   Confusion matrix in text:
   [[50  0  0]
    [ 1 37 12]
    [ 0  4 46]]
   Confusion matrix plotted.
   Plotting ROC curve....
   ROC curve plotted.
   Confusion matrix and ROC curve plotted. See them in Run History details page.
   ```

10. Feche a guia **Propriedades da Execução** e retorne para a guia **iris_sklearn.py**. 

11. Repita as execuções adicionais. 

    Insira uma série de valores numéricos diferentes no campo **Argumentos** desde `0.001` até `10`. Clique em **Executar** para executar o código mais algumas vezes. O valor do argumento que você alterar a cada vez será alimentado no algoritmo de regressão logística no código, resultando em resultados diferentes a cada vez.

## <a name="review-run-history-in-detail"></a>Examinar o histórico de execuções com detalhes
No Azure Machine Learning Workbench, cada execução do script é capturada como um registro do histórico de execuções. Você pode exibir o histórico de execuções de um script específico abrindo a exibição **Execuções**.

1. Clique no botão **Execuções** (ícone de relógio) na barra de ferramentas à esquerda para abrir a listagem de **Execuções**. Em seguida, clique em **iris_sklearn.py** para mostrar o **Painel de Execução** de `iris_sklearn.py`.

   ![img](media/tutorial-classifying-iris/run_view.png)

2. A guia **Painel de Execução** se abrirá. Examine as estatísticas capturadas em várias execuções. Os gráficos são renderizados na parte superior da guia e cada execução numerada com detalhes é listada na tabela na parte inferior da página.

   ![img](media/tutorial-classifying-iris/run_dashboard.png)

3. Filtre a tabela e, em seguida, clique nos gráficos interativamente para exibir o status, a duração, a precisão e a taxa de regularização de cada execução. 

4. Selecione duas ou três execuções na tabela **Execuções** e, em seguida, clique no botão **Comparar** para abrir uma página de comparação detalhada. Examine a comparação lado a lado. Clique no botão voltar da **Lista de execuções** no canto superior esquerdo da página de comparação para retornar para o **Painel de Execução**.

5. Clique em uma execução individual para ver a exibição de detalhes de execução. Observe que as estatísticas para a execução selecionada são listadas na seção _Propriedades da Execução_. Os arquivos criados na pasta de saída são listados na seção **Saída** e podem ser baixados.

   ![img](media/tutorial-classifying-iris/run_details.png)

   A dois gráficos, a matriz de confusão e a curva ROC multiclasse são renderizados na seção **Visualizações**. Todos os arquivos de log também podem ser encontrados na seção **Logs**.

## <a name="execute-scripts-in-the-local-docker-environment"></a>Executar scripts no ambiente do Docker local

O Azure ML permite configurar ambientes de execução adicionais como o Docker e executar o script nesses ambientes com facilidade. 

>[!IMPORTANT]
>Para realizar esta etapa, você deve ter o mecanismo do Docker localmente instalado e iniciado. Consulte o guia de instalação para obter mais detalhes.

1. Na barra de ferramentas à esquerda, selecione o ícone de pasta para abrir a listagem **Arquivos** para seu projeto. Expanda a pasta `aml_config`. 

2. Observe que há vários ambientes pré-configurados como **docker-python**, **docker-spark** e **local**. 

   Cada ambiente tem dois arquivos, assim como `docker-python.compute` e `docker-python.runconfig`. Abra cada tipo de arquivo, observe que algumas opções são configuráveis no editor de texto.  

   Feche (X) as guias para quaisquer editores de texto abertos para limpar.

3. Execute o script **iris_sklearn.py** usando o ambiente **docker-python**. 

   - Na barra de ferramentas à esquerda, clique no ícone de relógio para abrir o painel **Execuções**. Clique em **Todas as Execuções**. 
   - Na parte superior da guia **Todas as Execuções**, escolha **docker-python** como o ambiente de destino em vez do **local** padrão. 
   - Em seguida, movendo para a direita, escolha **iris_sklearn.py** como o script a executar. 
   - Deixe o campo **Argumentos** em branco, pois o script especifica um valor padrão. 
   - Clique no botão **Executar**.

4. Observe que um novo trabalho inicia conforme o painel **Trabalhos** à direita da janela do workbench.

   Quando você está executando no Docker pela primeira vez, ele leva mais alguns minutos extra para ser concluído. 

   Nos bastidores, o Azure Machine Learning Workbench cria um novo arquivo de docker referenciando a imagem base do Docker especificada no arquivo `docker.compute`, bem como os pacotes do Python de dependência especificados no arquivo `conda_dependencies.yml`. O mecanismo do Docker, em seguida, baixa a imagem base do Azure, instala os pacotes do Python especificados no arquivo `conda_dependencies.yml` e, em seguida, inicia um contêiner do Docker. Em seguida, ele copia (ou referencia, dependendo da configuração de execução) a cópia local da pasta do projeto, então executa o script `iris_sklearn.py`. No final, você deve ver exatamente o mesmo resultado que veria ao usar **local** como destino.

5. Agora experimentaremos o Spark. A imagem base do Docker contém uma instância do Spark previamente instalada e configurada. Por isso, você pode executar um script PySpark nele. Essa é uma maneira simples para desenvolver e testar seu programa Spark sem precisar gastar tempo instalando e configurando o Spark por conta própria. 

   Abra o arquivo `iris_pyspark.py` . Este script carrega o arquivo de dados `iris.csv` e usa o algoritmo de Regressão Logística da biblioteca de ML do Spark para classificar o conjunto de dados Íris. Agora, altere o ambiente de execução para **docker-spark** e o script para **iris_pyspark.py** e execute novamente. Isso leva um pouco mais de tempo, uma vez que uma sessão do Spark deve ser criada e iniciada dentro do contêiner do Docker. Você também pode ver que o stdout é diferente de stdout do `iris_pyspark.py`.

6. Realize mais algumas execuções e experimente argumentos diferentes. 

7. Abra o arquivo `iris_pyspark.py` para ver o modelo de regressão logística simples criado usando a biblioteca de ML do Spark. 

8. Interaja com o painel **Trabalhos**, a exibição de lista de histórico de execuções e a exibição de detalhes de execução das suas execuções em diferentes ambientes de execução.

## <a name="execute-scripts-in-the-azure-ml-cli-window"></a>Executar scripts em uma janela de CLI do Azure ML

1. Usando o Azure Machine Learning Workbench, inicie a janela de linha de comando clicando no menu **Arquivo**, depois em **Abrir o Prompt de Comando**. O seu prompt de comando inicia na pasta do projeto com o prompt `C:\Temp\myIris\>`.

   >[!Important]
   >Você deve usar a janela de linha de comando (iniciada por meio do Workbench) para realizar as seguintes etapas:

2. Use o prompt de comando (CLI) para fazer logon no Azure. 

   O aplicativo workbench e a CLI usam caches de credenciais independentes ao autenticar para recursos do Azure. Você só precisa fazer isso vez, até que o token em cache expire. O comando **az account list** retorna a lista de assinaturas disponíveis para seu logon. Se houver mais de um, use o valor da ID da assinatura desejada e defina-a como a conta padrão para usar com o comando **az set account -s**, fornecendo o valor da ID da assinatura. Em seguida, confirme a configuração usando o comando account show.

   ```azurecli
   REM login using aka.ms/devicelogin site.
   az login
   
   REM list all Azure subscriptions you have access to. 
   az account list -o table
   
   REM set the current Azure subscription to the one you want to use.
   az set account -s <subscriptionId>
   
   REM verify your current subscription is set correctly
   az account show
   ```

3. Depois que você estiver autenticado e o contexto atual da assinatura do Azure estiver definido, digite os comandos a seguir na janela de CLI para instalar matplotlib e enviar o script Python como um experimento a ser executado.

   ```azurecli
   REM You don't need to do this if you have installed matplotlib locally from the previous steps.
   pip install matplotlib
   
   REM Kick off an execution of the iris_sklearn.py file against local compute context
   az ml experiment submit -c local .\iris_sklearn.py
   ```

4. Examine a saída. Observe a mesma saída e o resultado que você executou anteriormente neste tutorial usando o Workbench para executar o script. 

5. Execute o mesmo script usando o ambiente de execução do Docker se você tem o Docker instalado em seu computador.

   ```azurecli
   REM Execute iris_sklearn.py in local Docker container Python environment.
   az ml experiment submit -c docker-python .\iris_sklearn.py 0.01
   
   REM Execute iris_pyspark.py in local Docker container Spark environment.
   az ml experiment submit -c docker-spark .\iris_pyspark.py 0.1
   ```
6. No Azure Machine Learning Workbench, clique no ícone de pasta na barra de ferramentas à esquerda para listar os arquivos de projeto e abra o script Python chamado **run.py**. 

   Esse script é útil para fazer loop por várias taxas de regularização e executar o experimento várias vezes com essas taxas. Esse script inicia um trabalho `iris_sklearn.py` com uma taxa de regularização de `10.0` (um número absurdamente grande) e reduz a taxa à metade na execução seguinte e assim por diante, até que a taxa não seja menor do que `0.005`. 

   ```python
   # run.py
   import os
   
   reg = 10
   while reg > 0.005:
       os.system('az ml experiment submit -c local ./iris_sklearn.py {}'.format(reg))
       reg = reg / 2
   ```

   Para iniciar o script **run.py** da linha de comando, execute os seguintes comandos:

   ```cmd
   REM Submit iris_sklearn.py multiple times with different regularization rates
   python run.py
   ```

   Quando `run.py` for concluído, você verá um gráfico em sua exibição de lista de histórico de execuções no Azure Machine Learning Workbench.

## <a name="execute-in-a-docker-container-on-a-remote-machine"></a>Execute em um contêiner de Docker em um computador remoto
Para executar o script em um contêiner do Docker em um computador Linux remoto, você precisa ter acesso SSH (nome de usuário e senha) para esse computador remoto. E o computador remoto deve ter o mecanismo do Docker instalado e em execução. É a maneira mais fácil de obter um computador Linux desse tipo é criar uma [DSVM (Máquina Virtual de Ciência de Dados) com base no Ubuntu](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) no Azure. (Observe que NÃO há suporte para o DSVM com base em CentOS.) 

1. Depois que a VM é criada, você pode anexar a VM como um ambiente de execução, gerando um par de arquivos `.runconfig` e `.compute` usando o comando abaixo. Nomearemos o novo ambiente `myvm`.
 
   ```azurecli
   REM create myvm compute target
   az ml computetarget attach --name myvm --address <IP address> --username <username> --password <password> --type remotedocker
   ```
   
   >[!NOTE]
   >A área de endereço IP também pode ser um FQDN (nome de domínio totalmente qualificado) publicamente endereçável, tal como `vm-name.southcentralus.cloudapp.azure.com`. É uma melhor prática adicionar o FQDN ao DSVM e usá-lo ali em lugar do endereço IP, pois você pode desligar a VM em algum momento para economizar no custo. Além disso, na próxima vez que você iniciar a VM, o endereço IP poderá ter sido alterado.

   Em seguida, execute o comando a seguir no constructo na imagem do Docker na VM para prepará-la para executar os scripts.
   
   ```azurecli
   REM prepare the myvm compute target
   az ml experiment prepare -c myvm
   ```
   >[!NOTE]
   >Você também pode alterar o valor de `PrepareEnvironment` na `myvm.runconfig` do `false` padrão para `true`. Isso preparará automaticamente o contêiner de Docker na primeira execução.

2. Edite o arquivo `myvm.runconfig` gerado sob `aml_config` e altere a Estrutura do `PySpark` padrão para `Python`:

   ```yaml
   "Framework": "Python"
   ```
   >[!NOTE]
   >Deixar a configuração de estrutura para PySpark também deve funcionar corretamente. No entanto, essa configuração é um pouco ineficiente se você não precisar realmente que uma sessão do Spark execute o script Python.

3. Envie o mesmo comando que você enviou na janela da CLI, mas desta vez usaremos _myvm_ como destino:
   ```azurecli
   REM execute iris_sklearn.py in remote Docker container
   az ml experiment submit -c myvm .\iris_sklearn.py
   ```
   O comando é executado como se você estivesse usando um ambiente `docker-python`, exceto pelo fato de que a execução ocorre na VM Linux remota. A janela CLI exibe as mesmas informações de saída.

4. Agora experimentaremos o Spark no contêiner. Abra o Explorador de arquivos (você também poderá fazer isso na janela de CLI se você estiver familiarizado com os comandos básicos de manipulação de arquivos). Faça uma cópia do arquivo `myvm.runconfig` e nomeie-o `myvm-spark.runconfig`. Edite o novo arquivo para alterar a configuração `Framework` de `Python` para `PySpark`:
   ```yaml
   "Framework": "PySpark"
   ```
   Não faça nenhuma alteração ao arquivo `myvm.compute`. A mesma imagem do Docker na mesma VM é usada para a execução do Spark. No novo `myvy-spark.runconfig`, o campo `target` aponta para o mesmo arquivo `myvm.compute` por meio de seu nome `myvm`.

5. Digite o comando a seguir para executá-lo na instância do Spark no contêiner do Docker remoto:
   ```azureli
   REM execute iris_pyspark.py in Spark instance on remote Docker container
   az ml experiment submit -c myvm-spark .\iris_pyspark.py
   ```

## <a name="execute-script-in-an-hdinsight-cluster"></a>Execute o script em um cluster HDInsight
Você também pode executar esse script em um cluster Spark real. 

1. Se você tiver acesso a um cluster Spark para o Azure HDInsight, gere um comando de configuração de execução HDI, conforme mostrado. Forneça o nome do cluster HDInsight, seu nome de usuário HDInsight e a senha como parâmetros. Use o seguinte comando:

   ```azurecli
   REM create a compute target that points to a HDI cluster
   az ml computetarget attach --name myhdi --address <cluster head node FQDN> --username <username> --password <password> --type cluster

   REM prepare the HDI cluster
   az ml experiment prepare -c myhdi
   ```

   O FQDN (nome de domínio totalmente qualificado) do nó de cabeçalho do cluster geralmente é `<cluster_name>-ssh.azurehdinsight.net`.

   >[!NOTE]
   >O `username` é o nome de usuário SSH do cluster. O valor padrão é `sshuser` se você não alterá-lo durante o provisionamento de HDI. Ele não é `admin`, que é o outro usuário criado durante o provisionamento para habilitar o acesso ao site de administração do cluster. 

2. Execute o comando a seguir e o script será executado no cluster HDInsight:

   ```azurecli
   REM execute iris_pyspark on the HDI cluster
   az ml experiment submit -c myhdi .\iris_pyspark.py
   ```

   >[!NOTE]
   >Quando você executar em um cluster HDI remoto, você também poderá exibir os detalhes de execução do trabalho YARN em `https://<cluster_name>.azurehdinsight.net/yarnui` usando a conta de usuário `admin`.


## <a name="next-steps"></a>Próximas etapas
Nessa segunda parte da série de tutoriais de três partes, você aprendeu a usar os serviços do Azure Machine Learning para:
> [!div class="checklist"]
> * Trabalhar no Azure Machine Learning Workbench
> * Abrir os scripts e examinar o código
> * Executar scripts em um ambiente local
> * Examinar o histórico de execuções
> * Executar scripts em um ambiente do Docker local
> * Executar scripts em uma janela de CLI do Azure local
> * Executar scripts em um ambiente do Docker remoto
> * Executar scripts em um ambiente do HDInsight na nuvem

Você está pronto para passar para a terceira parte da série. Agora que você criou o modelo de regressão logística, nós o implantaremos como um serviço Web em tempo real.

> [!div class="nextstepaction"]
> [Implantar um modelo](tutorial-classifying-iris-part-3.md)
