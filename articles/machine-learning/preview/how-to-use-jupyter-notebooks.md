---
title: "Como usar blocos de anotações do Jupyter no Azure Machine Learning Workbench | Microsoft Docs"
description: "Guia para o uso do recurso de Blocos de Anotações do Jupyter do Azure Machine Learning Workbench"
services: machine-learning
author: jopela
ms.author: jopela
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 07759a590e3a4e061a1c5727428d2c7c2a655f51
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-jupyter-notebook-in-azure-machine-learning-workbench"></a>Como usar blocos de anotações do Jupyter no Azure Machine Learning Workbench

O Azure Machine Learning Workbench tem suporte para experimentação de ciência de dados interativa por meio da integração do bloco de anotações do Jupyter. Este artigo descreve como usar de maneira eficiente esse recurso para aumentar a velocidade e a qualidade da sua experimentação de ciência de dados interativa.

## <a name="prerequisites"></a>Pré-requisitos
- [Instalar e criar o Azure Machine Learning](/machine-learning/preview/quickstart-installation.md).
- Familiarize-se com [bloco de anotações do Jupyter](http://jupyter.org/), uma vez que este artigo não ensina a usar o Jupyter.

## <a name="jupyter-notebook-architecture"></a>Arquitetura do bloco de anotações do Jupyter
De um modo geral, a arquitetura do bloco de anotações do Jupyter inclui três componentes, sendo que cada um pode ser executado em ambientes de computação diferentes:

- **Cliente**: recebe entrada do usuário e exibe a saída renderizada
- **Servidor**: servidor Web que hospeda os arquivos de bloc de anotações (arquivos .ipynb)
- **Kernel**: o ambiente de tempo de execução em que ocorre a execução real das células do bloco de anotações

Para obter mais detalhes, consulte a [documentação oficial do Jupyter](http://jupyter.readthedocs.io/en/latest/architecture/how_jupyter_ipython_work.html). A seguir está um diagrama que ilustra como esse cliente, servidor e arquitetura de kernel são mapeados para os componentes no Azure ML.

![arquitetura do bloco de anotações](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-architecture.png)

## <a name="kernels-in-azure-ml-workbench-notebook"></a>Kernels no bloco de anotações do Azure ML Workbench
Você pode acessar muitos kernels diferentes no Azure ML Workbench simplesmente definindo configurações de execução e destinos de computação na pasta `aml_config` do seu projeto. Adicionando um novo destino de computação emitindo o comando `az ml computetarget attach` é equivalente a adicionar um novo kernel.

>[!NOTE]
>Examine [Configurar execução](experiment-execution-configuration.md) para obter mais detalhes sobre configurações de execução e destinos de computação.

### <a name="kernel-naming-convention"></a>Convenção de nomenclatura do kernel
Os kernels geralmente são nomeados no formato "\<nome do projeto> \<nome da configuração de execução>". Por exemplo, se você tiver uma configuração de execução chamada _docker python_ em um projeto chamado _myIris_, poderá encontrar um kernel denominado "myIris docker-python" na lista de kernel ao abrir um bloco de anotações do Jupyter.

Atualmente, o Workbench tem suporte para os tipos de kernels a seguir.

### <a name="local-python-kernel"></a>Kernel Python local
Esse kernel Python dá suporte à execução no computador local. Ele é integrado ao suporte do Histórico de Execução do Azure Machine Learning. O nome do kernel normalmente é "my_project_name local".

### <a name="python-kernel-in-docker-local-or-remote"></a>Python Kernel no Docker (local ou remoto)
Esse kernel Python é executado em um contêiner do Docker em seu computador local ou em uma VM do Linux remota. O nome do kernel normalmente é "my_project docker". O arquivo `docker.runconfig` associado tem o campo `Framework` definido como `Python`.

### <a name="pyspark-kernel-in-docker-local-or-remote"></a>Kernel PySpark no Docker (local ou remoto)
Este kernel PySpark executa scripts em um contexto do Spark em execução dentro do contêiner do Docker, seja no computador local ou em uma VM Linux remota. O nome do kernel normalmente é "my_project docker". O arquivo `docker.runconfig` associado tem o campo `Framework` definido como `PySpark`.

### <a name="pyspark-kernel-on-hdinsight-cluster"></a>Kernel PySpark no cluster HDInsight
Esse kernel é executado no cluster do HDInsight remoto conectado como um destino de computação para seu projeto. O nome do kernel normalmente é "my_project my_hdi". 

>[!IMPORTANT]
>No arquivo `.compute` do destino de computação HDI, você deve alterar o campo `yarnDeployMode` para `client` (o valor padrão é `cluster`) para usar este kernel. 

## <a name="start-jupyter-server-from-the-workbench"></a>Iniciar o servidor Jupyter do Workbench
No Azure Machine Learning Workbench, os blocos de anotações podem ser acessados usando a guia **Blocos de Anotações** do Workbench. O exemplo de projeto _Íris de Classificação_ inclui um exemplo de bloco de anotações `iris.ipynb`.

![guia de blocos de anotações](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-01.png)

Quando um bloco de anotações é aberto no Azure Machine Learning Workbench, ele é exibido em sua própria guia do documento no **Modo de Visualização**. É uma exibição somente leitura que não requer um kernel e servidor Jupyter em execução.

![visualização de bloco de anotações](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-02.png)

Clicar no botão **Iniciar Servidor do Bloco de Anotações** inicializa o servidor Jupyter e coloca o bloco de anotações no **Modo de Edição**. A interface do usuário do bloco de anotações Jupyter familiar aparece inserida no Workbench. Agora você pode definir um núcleo do menu **Kernel** e iniciar a sessão interativa do bloco de anotações. 

>[!NOTE]
>Observe que, para kernels não locais, pode levar um minuto ou dois para iniciar se você o estiver usando pela primeira vez. Você pode executar o comando `az ml experiment prepare` na janela CLI para preparar o destino de computação para que o kernel possa iniciar muito mais rapidamente depois que o destino de computação estiver preparado.

![modo de edição](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-04.png)

Esta é uma experiência de bloco de anotações Jupyter totalmente interativa. Há suporte para todas as operações do bloco de notas e atalhos de teclado normais nesta janela, com exceção de algumas operações de arquivo, uma vez que podem ser executadas por meio da guia **Blocos de Notas** e da guia **Arquivo** do Workbench.

## <a name="start-jupyter-server-from-command-line"></a>Iniciar o Servidor Jupyter da linha de comando
Você também pode inicializar uma sessão do bloco de anotações emitindo um `az ml notebook start` da janela da linha de comando:
```
$ az ml notebook start
[I 10:14:25.455 NotebookApp] The port 8888 is already in use, trying another port.
[I 10:14:25.464 NotebookApp] Serving notebooks from local directory: /Users/johnpelak/Desktop/IrisDemo
[I 10:14:25.465 NotebookApp] 0 active kernels 
[I 10:14:25.465 NotebookApp] The Jupyter Notebook is running at: http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.465 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[C 10:14:25.466 NotebookApp] 
    
    Copy/paste this URL into your browser when you connect for the first time,
    to login with a token:
        http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.759 NotebookApp] Accepting one-time-token-authenticated connection from ::1
[I 10:16:52.970 NotebookApp] Kernel started: 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
[I 10:16:53.854 NotebookApp] Adapting to protocol v5.1 for kernel 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
```
Seu navegador padrão é inicializado automaticamente com o servidor Jupyter apontando para o diretório inicial do projeto. Você também pode usar a URL e o token exibidos na janela da CLI para inicializar outras janelas do navegador localmente. 

![painel do projeto](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-07.png)

Agora você pode clicar em um arquivo do bloco de anotações `.ipynb`, abri-lo, definir o kernel (se não tiver sido definido) e iniciar a sessão interativa.

![painel do projeto](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-08.png)

## <a name="next-steps"></a>Próximas etapas
- Para saber como usar o bloco de anotações Jupyter, acesse a [documentação oficial do Jupyter](http://jupyter-notebook.readthedocs.io/en/latest/).    
- Para obter uma compreensão mais profunda do ambiente de execução de experimentação do Azure ML, examine [Visão geral do serviço de execução de experimento do Azure Machine Learning](experiment-execution-configuration.md)

