---
title: 'Executar script Python: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo executar script Python no Azure Machine Learning designer para executar o código Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.custom: devx-track-python
author: likebupt
ms.author: keli19
ms.date: 01/02/2021
ms.openlocfilehash: 6003ca9156d8553604d7ebbf94c5c3373d077f0f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102500692"
---
# <a name="execute-python-script-module"></a>Executar módulo de script Python

Este artigo descreve o módulo executar script Python no designer de Azure Machine Learning.

Use este módulo para executar o código Python. Para obter mais informações sobre os princípios de arquitetura e design do Python, consulte [como executar código Python no designer de Azure Machine Learning](../how-to-designer-python.md).

Com o Python, você pode executar tarefas para as quais os módulos existentes não oferecem suporte, como:

+ Visualizando dados usando o `matplotlib` .
+ Usando bibliotecas do Python para enumerar conjuntos de valores e modelos em seu espaço de trabalho.
+ Leitura, carregamento e manipulação de dados de fontes para as quais o módulo [importar dados](./import-data.md) não dá suporte.
+ Execute seu próprio código de aprendizado profundo. 

## <a name="supported-python-packages"></a>Pacotes Python com suporte

Azure Machine Learning usa a distribuição Anaconda do Python, que inclui muitos utilitários comuns para processamento de dados. Atualizaremos a versão do Anaconda automaticamente. A versão atual é:
 -  Anaconda 4.5 + distribuição para Python 3,6 

Para obter uma lista completa, consulte a seção [pacotes python pré-instalados](#preinstalled-python-packages).

Para instalar pacotes que não estão na lista pré-instalada (por exemplo, *scikit-misc*), adicione o seguinte código ao seu script: 

```python
import os
os.system(f"pip install scikit-misc")
```

Use o código a seguir para instalar pacotes para melhorar o desempenho, especialmente para inferência:
```python
import importlib.util
package_name = 'scikit-misc'
spec = importlib.util.find_spec(package_name)
if spec is None:
    import os
    os.system(f"pip install scikit-misc")
```

> [!NOTE]
> Se o pipeline contiver vários módulos executar script Python que precisam de pacotes que não estão na lista pré-instalada, instale os pacotes em cada módulo.

> [!WARNING]
> O módulo script excute Python não dá suporte à instalação de pacotes que dependem de bibliotecas nativas extras com comando como "apt-get", como Java, PyODBC e etc. Isso ocorre porque esse módulo é executado em um ambiente simples com Python pré-instalado apenas e com permissão de não administrador.  

## <a name="access-to-current-workspace-and-registered-datasets"></a>Acesso ao espaço de trabalho atual e conjuntos de valores registrados

Você pode consultar o seguinte código de exemplo para acessar os [conjuntos de valores registrados](../how-to-create-register-datasets.md) em seu espaço de trabalho:

```Python
def azureml_main(dataframe1 = None, dataframe2 = None):

    # Execution logic goes here
    print(f'Input pandas.DataFrame #1: {dataframe1}')
    from azureml.core import Run
    run = Run.get_context(allow_offline=True)
    #access to current workspace
    ws = run.experiment.workspace

    #access to registered dataset of current workspace
    from azureml.core import Dataset
    dataset = Dataset.get_by_name(ws, name='test-register-tabular-in-designer')
    dataframe1 = dataset.to_pandas_dataframe()
     
    # If a zip file is connected to the third input port,
    # it is unzipped under "./Script Bundle". This directory is added
    # to sys.path. Therefore, if your zip file contains a Python file
    # mymodule.py you can import it using:
    # import mymodule

    # Return value must be of a sequence of pandas.DataFrame
    # E.g.
    #   -  Single return value: return dataframe1,
    #   -  Two return values: return dataframe1, dataframe2
    return dataframe1,
```

## <a name="upload-files"></a>Carregar arquivos
O módulo executar script Python dá suporte ao carregamento de arquivos usando o [SDK Azure Machine Learning Python](/python/api/azureml-core/azureml.core.run%28class%29#upload-file-name--path-or-stream-).

O exemplo a seguir mostra como carregar um arquivo de imagem no módulo executar script Python:

```Python

# The script MUST contain a function named azureml_main,
# which is the entry point for this module.

# Imports up here can be used to
import pandas as pd

# The entry point function must have two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):

    # Execution logic goes here
    print(f'Input pandas.DataFrame #1: {dataframe1}')

    from matplotlib import pyplot as plt
    plt.plot([1, 2, 3, 4])
    plt.ylabel('some numbers')
    img_file = "line.png"
    plt.savefig(img_file)

    from azureml.core import Run
    run = Run.get_context(allow_offline=True)
    run.upload_file(f"graphics/{img_file}", img_file)

    # Return value must be of a sequence of pandas.DataFrame
    # For example:
    #   -  Single return value: return dataframe1,
    #   -  Two return values: return dataframe1, dataframe2
    return dataframe1,
}
```

Depois que a execução do pipeline for concluída, você poderá visualizar a imagem no painel direito do módulo.

> [!div class="mx-imgBorder"]
> ![Visualização da imagem carregada](media/module/upload-image-in-python-script.png)

## <a name="how-to-configure-execute-python-script"></a>Como configurar o script Python de execução

O módulo executar script Python contém o código Python de exemplo que você pode usar como ponto de partida. Para configurar o módulo executar script Python, forneça um conjunto de entradas e código Python para execução na caixa de texto **script Python** .

1. Adicione o módulo **Executar script Python** ao seu pipeline.

2. Adicione e conecte-se em **dataSet1** quaisquer conjuntos de dados do designer que você deseja usar para entrada. Referencie este conjunto de DataSet em seu script Python como **DataFrame1**.

    O uso de um conjunto de um DataSet é opcional. Use-o se você quiser gerar dados usando o Python ou usar o código Python para importar os dados diretamente para o módulo.

    Este módulo dá suporte à adição de um segundo conjunto de uma em **Dataset2**. Referencie o segundo conjunto de DataSet em seu script Python como **DataFrame2**.

    Os conjuntos de dados armazenados em Azure Machine Learning são convertidos automaticamente em quadros de dado do pandas quando carregados com esse módulo.

    ![Executar mapa de entrada do Python](media/module/python-module.png)

4. Para incluir novos pacotes ou códigos do Python, conecte o arquivo compactado que contém esses recursos personalizados à porta do **pacote de script** . Ou, se o seu script tiver mais de 16 KB, use a porta de **pacote de script** para evitar erros como *CommandLine excede o limite de 16597 caracteres*. 

    
    1. Agrupe o script e outros recursos personalizados em um arquivo zip.
    1. Carregue o arquivo zip como um **conjunto** de um arquivo para o estúdio. 
    1. Arraste o módulo DataSet da lista *DataSets* no painel do módulo à esquerda na página criação do designer. 
    1. Conecte o módulo DataSet à porta do **pacote de script** do módulo **Executar script Python** .
    
    Qualquer arquivo contido no arquivo compactado carregado pode ser usado durante a execução do pipeline. Se o arquivo incluir uma estrutura de diretório, a estrutura será preservada.
 
    > [!WARNING]
    > **Não** use o **aplicativo** como o nome da pasta ou do seu script, pois o **aplicativo** é uma palavra reservada para serviços internos. Mas você pode usar outros namespaces como o `app123` .
   
    Veja a seguir um exemplo de pacote de script, que contém um arquivo de script Python e um arquivo txt:
      
    > [!div class="mx-imgBorder"]
    > ![Exemplo de pacote de script](media/module/python-script-bundle.png)  

    Veja a seguir o conteúdo de `my_script.py` :

    ```python
    def my_func(dataframe1):
    return dataframe1
    ```
    Veja a seguir um código de exemplo que mostra como consumir os arquivos no pacote de script:    

    ```python
    import pandas as pd
    from my_script import my_func
 
    def azureml_main(dataframe1 = None, dataframe2 = None):
 
        # Execution logic goes here
        print(f'Input pandas.DataFrame #1: {dataframe1}')
 
        # Test the custom defined python function
        dataframe1 = my_func(dataframe1)
 
        # Test to read custom uploaded files by relative path
        with open('./Script Bundle/my_sample.txt', 'r') as text_file:
            sample = text_file.read()
    
        return dataframe1, pd.DataFrame(columns=["Sample"], data=[[sample]])
    ```

5. Na caixa de texto **script do Python** , digite ou cole script Python válido.

    > [!NOTE]
    >  Tenha cuidado ao escrever seu script. Verifique se não há erros de sintaxe, como o uso de variáveis não declaradas ou de módulos ou funções não importadas. Preste atenção extra à lista de módulos pré-instalados. Para importar módulos que não estão listados, instale os pacotes correspondentes em seu script, como:
    >  ``` Python
    > import os
    > os.system(f"pip install scikit-misc")
    > ```
    
    A caixa de texto **script do Python** é preenchida previamente com algumas instruções em comentários e o código de exemplo para acesso a dados e saída. Você deve editar ou substituir este código. Siga as convenções Python para recuo e capitalização:

    + O script deve conter uma função chamada `azureml_main` como ponto de entrada para este módulo.
    + A função de ponto de entrada deve ter dois argumentos de entrada `Param<dataframe1>` e `Param<dataframe2>` , mesmo quando esses argumentos não são usados em seu script.
    + Os arquivos compactados conectados à terceira porta de entrada são descompactados e armazenados no diretório `.\Script Bundle` , que também é adicionado ao Python `sys.path` . 

    Se o arquivo. zip contiver `mymodule.py` , importe-o `import mymodule` usando.

    Dois conjuntos de valores podem ser retornados para o designer, que deve ser uma sequência do tipo `pandas.DataFrame` . Você pode criar outras saídas em seu código Python e gravá-las diretamente no armazenamento do Azure.

    > [!WARNING]
    > **Não** é recomendável conectar-se a um banco de dados ou a outros armazenamentos externos no **módulo executar script Python**. Você pode usar [módulo importar dados](./import-data.md) e [Exportar módulo de dados](./export-data.md)     

6. Envie o pipeline.

    Se o módulo estiver concluído, verifique a saída se for o esperado.

    Se o módulo falhar, você precisará fazer algumas soluções de problemas. Selecione o módulo e abra **saídas + logs** no painel direito. Abra **70_driver_log.txt** e pesquise **no azureml_main**, em seguida, você pode descobrir qual linha causou o erro. Por exemplo, "File"/tmp/tmp01_ID/user_script. py ", line 17, in azureml_main" indica que o erro ocorreu na linha 17 do seu script Python.

## <a name="results"></a>Resultados

Os resultados de qualquer computação pelo código Python inserido devem ser fornecidos como `pandas.DataFrame` , que é automaticamente convertido para o formato de conjunto de Azure Machine Learning DataSet. Em seguida, você pode usar os resultados com outros módulos no pipeline.

O módulo retorna dois conjuntos de valores:  
  
+ **Results DataSet 1**, definido pelo primeiro quadro de dados pandas retornado em um script Python.

+ O conjunto de dados de **resultado 2**, definido pelo segundo, retornou o quadro de dado pandas em um script Python.

## <a name="preinstalled-python-packages"></a>Pacotes python pré-instalados
Os pacotes pré-instalados são:
-    adal==1.2.2
-    applicationinsights==0.11.9
-    attrs = = 19.3.0
-    Azure-comum = = 1.1.25
-    Azure-Core = = 1.3.0
-    azure-graphrbac==0.61.1
-    Azure-Identity = = 1.3.0
-    azure-mgmt-authorization==0.60.0
-    azure-mgmt-containerregistry==2.8.0
-    Azure-MGMT-keyvault = = 2.2.0
-    Azure-MGMT-Resource = = 8.0.1
-    Azure-MGMT-Storage = = 8.0.0
-    Azure-Storage-blob = = 1.5.0
-    Azure-Storage-comum = = 1.4.2
-    azureml-Core = = 1.1.5.5
-    azureml-dataprep-nativo = = 14.1.0
-    azureml-dataprep = = 1.3.5
-    azureml-padrões = = 1.1.5.1
-    azureml-designer-clássico-modules = = 0.0.118
-    azureml-designer-Core = = 0.0.31
-    azureml-designer-interno = = 0.0.18
-    azureml-Model-Management-SDK = = 1.0.1 B6. post1
-    azureml-pipeline-Core = = 1.1.5
-    azureml-telemetria = = 1.1.5.3
-    backports.tempfile==1.0
-    backports.weakref==1.0.post1
-    boto3 = = 1.12.29
-    botocore = = 1.15.29
-    cachetools = = 4.0.0
-    Certificates = = 2019.11.28
-    cffi==1.12.3
-    chardet==3.0.4
-    clique em = = 7.1.1
-    cloudpickle = = 1.3.0
-    ConfigParser = = 3.7.4
-    contextlib2 = = 0.6.0. post1
-    criptografia = = 2.8
-    cycler==0.10.0
-    dill==0.3.1.1
-    distro==1.4.0
-    Docker = = 4.2.0
-    docutils==0.15.2
-    dotnetcore2 = = 2.1.13
-    Flask = = 1.0.3
-    fusepy==3.0.1
-    gensim==3.8.1
-    Google-API-Core = = 1.16.0
-    Google-auth = = 1.12.0
-    Google-Cloud-Core = = 1.3.0
-    Google-Cloud-Storage = = 1.26.0
-    Google-retomável-mídia = = 0.5.0
-    googleapis-Common-Protos = = 1.51.0
-    gunicorn = = 19.9.0
-    IDNA = = 2.9
-    desbalanceado-Learn = = 0.4.3
-    isodate==0.6.0
-    itsdangerous = = 1.1.0
-    jeepney = = 0.4.3
-    jinja2 = = 2.11.1
-    jmespath = = 0.9.5
-    joblib = = 0.14.0
-    JSON-Logging-py = = 0.2
-    jsonpickle = = 1.3
-    jsonschema = = 3.0.1
-    kiwisolver==1.1.0
-    Liac-ARFF = = 2.4.0
-    lightgbm==2.2.3
-    markupsafe = = 1.1.1
-    matplotlib = = 3.1.3
-    mais-itertools = = 6.0.0
-    MSAL-Extensions = = 0.1.3
-    MSAL = = 1.1.0
-    msrest = = 0.6.11
-    msrestazure = = 0.6.3
-    ndg-httpsclient==0.5.1
-    nimbusml = = 1.6.1
-    numpy = = 1.18.2
-    oauthlib==3.1.0
-    pandas = = 0.25.3
-    pathspec = = 0.7.0
-    Pip = = 20.0.2
-    portalocker = = 1.6.0
-    protobuf = = 3.11.3
-    pyarrow = = 0.16.0
-    pyasn1-modules = = 0.2.8
-    pyasn1 = = 0.4.8
-    pycparser = = 2,20
-    pycryptodomex = = 3.7.3
-    pyjwt = = 1.7.1
-    pyopenssl = = 19.1.0
-    pyparsing = = 2.4.6
-    pyrsistent = = 0.16.0
-    Python-DateUtil = = 2.8.1
-    pytz==2019.3
-    solicitações-oauthlib = = 1.3.0
-    solicitações = = 2.23.0
-    RSA = = 4.0
-    ruamel.yaml==0.15.89
-    s3transfer = = 0.3.3
-    scikit-Learn = = 0.22.2
-    SciPy = = 1.4.1
-    secretstorage = = 3.1.2
-    setuptools = = 46.1.1. post20200323
-    seis = = 1.14.0
-    Smart-Open = = 1.10.0
-    urllib3 = = 1.25.8
-    WebSocket-cliente = = 0.57.0
-    Werkzeug = = 0.16.1
-    Wheel = = 0.34.2

## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning.