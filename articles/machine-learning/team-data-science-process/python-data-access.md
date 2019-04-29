---
title: Acesso a conjuntos de dados com a biblioteca de clientes do Python – Processo de ciência de dados de equipe
description: Instalar e usar a biblioteca de cliente do Python para acessar e gerenciar dados de Azure Machine Learning com segurança em um ambiente local do Python.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: bf0e679ab46752d71ba4f5ef2b014e0cb2b4c6ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60593796"
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Acessar os conjuntos de dados com o Python usando a biblioteca de cliente do Python de Azure Machine Learning
A visualização da biblioteca de cliente do Python de Microsoft Azure Machine Learning pode habilitar o acesso seguro a seus conjuntos de dados de Azure Machine Learning em um ambiente Python local e habilita a criação e o gerenciamento de conjuntos de dados no workspace.

Este tópico fornece instruções sobre como:

* instalar a biblioteca de cliente do Python de Machine Learning
* acessar e carregar conjuntos de dados, incluindo instruções sobre como obter autorização para acessar conjuntos de dados de Azure Machine Learning no seu ambiente local do Python
* acessar conjuntos de dados intermediários por meio de testes
* usar a biblioteca de cliente do Python para enumerar os conjuntos de dados, acessar metadados, ler o conteúdo de um conjunto de dados, criar novos conjuntos de dados e atualizar conjuntos de dados existentes

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="prerequisites"></a>Pré-requisitos
A biblioteca de cliente do Python foi testada nos ambientes a seguir:

* Windows, Mac e Linux
* Python 2.7, 3.3 e 3.4

Ela tem uma dependência nos seguintes pacotes:

* solicitações
* python-dateutil
* pandas

É recomendável usar uma distribuição do Python como [Anaconda](http://continuum.io/downloads#all) ou [Canopy](https://store.enthought.com/downloads/), que acompanham o Python, IPython e os três pacotes listados acima e instalados. Embora o IPython não seja estritamente necessário, é um ótimo ambiente para manipular e visualizar dados interativamente.

### <a name="installation"></a>Como instalar a biblioteca de cliente do Python de Azure Machine Learning
A biblioteca de cliente do Python de Azure Machine Learning também deve ser instalada para concluir as tarefas descritas neste tópico. Ela está disponível no [Índice de Pacote do Python](https://pypi.python.org/pypi/azureml). Para instalá-la em seu ambiente Python, execute o seguinte comando no seu ambiente local Python:

    pip install azureml

Como alternativa, você pode fazer o download e instalar as origens no [GitHub](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python).

    python setup.py install

Se tiver o git instalado em seu computador, você pode usar pip para instalar diretamente por meio do repositório git:

    pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


## <a name="datasetAccess"></a>Usar snippets de código do Estúdio para acessar conjuntos de dados
A biblioteca de cliente do Python fornece acesso programático aos conjuntos de dados existentes de testes que foram executados.

Na interface da Web do Studio, você pode gerar snippets de código que incluem todas as informações necessárias para baixar e desserializar conjuntos de dados como objetos DataFrame de pandas em seu computador local.

### <a name="security"></a>Segurança para acesso a dados
Os snippets de código fornecidos pelo Estúdio para uso com a biblioteca de cliente do Python incluem a ID do workspace e a autorização de token. Eles fornecem acesso completo ao workspace e devem ser protegidos, com uma senha, por exemplo.

Por motivos de segurança, a funcionalidade do snippet de código está disponível somente para usuários que tenham a função definida como **Proprietário** no workspace. Sua função é exibida no Estúdio de Machine Learning do Azure na página **USUÁRIOS** em **Configurações**.

![Segurança][security]

Se a sua função não estiver definida como **Proprietário**, você pode solicitar a ser convidado novamente como um proprietário ou pedir ao proprietário do workspace para fornecer o snippet de código.

Para obter o token de autorização, você pode fazer o seguinte:

* Solicitar um token de um proprietário. Os proprietários podem acessar seus tokens de autorização na página Configurações do seu workspace no Estúdio. Selecione **Configurações** no painel esquerdo e clique em **TOKENS DE AUTORIZAÇÃO** para ver os tokens primários e secundários. Embora os tokens de autorização primários ou secundários possam ser usados no snippet de código, é recomendável que os proprietários compartilham somente os tokens de autorização secundários.

![Tokens de autorização](./media/python-data-access/ml-python-access-settings-tokens.png)

* Peça para ser promovido à função do proprietário. Para fazer isso, um proprietário atual do workspace precisa primeiro remover você do workspace e depois convidá-lo novamente como um proprietário.

Depois que os desenvolvedores tiverem obtido a ID do workspace e o token de autorização, eles poderão acessar o workspace usando o snippet de código, independentemente de sua função.

Os tokens de autorização são gerenciados na página **TOKENS DE AUTORIZAÇÃO** em **CONFIGURAÇÕES**. Você pode gerá-los novamente, mas esse procedimento revogará o acesso ao token anterior.

### <a name="accessingDatasets"></a>Conjuntos de dados de acesso de um aplicativo Python local
1. No Machine Learning Studio, clique em **CONJUNTOS DE DADOS** na barra de navegação à esquerda.
2. Selecione o conjunto de dados que você deseja acessar. Você pode selecionar qualquer um dos conjuntos de dados da lista **MEUS CONJUNTOS DE DADOS** ou da lista **EXEMPLOS**.
3. Na barra de ferramentas inferior, clique em **Gerar código de acesso a dados**. Esse botão será desabilitado se os dados estiverem em um formato incompatível com a biblioteca de cliente do Python.
   
    ![Conjunto de dados][datasets]
4. Selecione o snippet de código na janela que aparece e copie-o para a área de transferência.
   
    ![Botão Gerar código de acesso a dados][dataset-access-code]
5. Cole o código no bloco de notas do seu aplicativo Python local.
   
    ![Cole o código no notebook][ipython-dataset]

## <a name="accessingIntermediateDatasets"></a>Acesse os conjuntos intermediários de testes de Machine Learning
Depois de um teste ser executado no Machine Learning Studio, é possível acessar os conjuntos de dados intermediários de nós de saída dos módulos. Os conjuntos de dados intermediários são dados que foram criados e usados para etapas intermediárias quando uma ferramenta de modelo tiver sido executada.

Os conjuntos de dados intermediários podem ser acessados conforme o formato de dados for compatível com a biblioteca de cliente do Python.

Há suporte para os seguintes formatos (as constantes para eles estão na classe `azureml.DataTypeIds` ):

* Texto sem formatação
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

Você pode determinar o formato passando o mouse sobre um nó de saída do módulo. Ele é exibido com o nome do nó, em uma dica de ferramenta.

Alguns módulos, tal como o módulo [Divisão][split], resultam em um formato chamado `Dataset`, que não tem suporte na biblioteca de cliente do Python.

![Formato de conjunto de dados][dataset-format]

Você precisa usar um módulo de conversão, como [Converter para CSV][convert-to-csv], para obter uma saída em um formato com suporte.

![Formato de GenericCSV][csv-format]

As etapas a seguir mostram um exemplo que cria um teste, executa-o e acessa o conjunto de dados intermediário.

1. Criar um novo teste.
2. Inserir um módulo **Conjunto de dados de Classificação Binária de Renda de Censo de Adulto** .
3. Inserir um módulo [Divisão][split] e conectá-lo à sua entrada para a saída de módulo do conjunto de dados.
4. Inserir um módulo [Converter para CSV][convert-to-csv] e conectá-lo à sua entrada em uma das saídas do módulo [Divisão][split].
5. Salve o teste, execute-o e aguarde o término da execução.
6. Clique no nó de saída no módulo [Converter para CSV][convert-to-csv].
7. Quando o menu de contexto for exibido, selecione **Gerar Código de Acesso a Dados**.
   
    ![Menu de contexto][experiment]
8. Selecione o snippet de código e copie-o na área de transferência a partir da janela exibida.
   
    ![Gerar código de acesso no menu de contexto][intermediate-dataset-access-code]
9. Cole o código no bloco de notas.
   
    ![Cole o código no notebook][ipython-intermediate-dataset]
10. É possível visualizar os dados usando matplotlib. Ele é exibido em um histograma para a coluna Idade:
    
    ![Histograma][ipython-histogram]

## <a name="clientApis"></a>Use a biblioteca de cliente do Python de Machine Learning para acessar, ler, criar e gerenciar conjuntos de dados
### <a name="workspace"></a>Workspace
O workspace é o ponto de entrada para a biblioteca de cliente do Python. Forneça a classe `Workspace` com a sua ID de workspace e token de autorização para criar uma instância:

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### <a name="enumerate-datasets"></a>Enumerar conjuntos de dados
Para enumerar todos os conjuntos de dados em um determinado workspace:

    for ds in ws.datasets:
        print(ds.name)

Para enumerar somente os conjuntos de dados criados pelo usuário:

    for ds in ws.user_datasets:
        print(ds.name)

Para enumerar apenas os conjuntos de dados de exemplo:

    for ds in ws.example_datasets:
        print(ds.name)

Você pode acessar um conjunto de dados por nome (que diferencia maiúsculas de minúsculas):

    ds = ws.datasets['my dataset name']

Ou você pode acessá-lo pelo índice:

    ds = ws.datasets[0]


### <a name="metadata"></a>Metadados
Conjuntos de dados têm metadados, além de conteúdo. (Os conjuntos de dados intermediários são uma exceção a essa regra e não têm nenhum metadado.)

Alguns valores de metadados são atribuídos pelo usuário no momento da criação:

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

Outros são valores atribuídos pelo Azure ML:

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

Consulte a classe `SourceDataset` para saber mais sobre os metadados disponíveis.

### <a name="read-contents"></a>Ler conteúdo
Os trechos de código fornecidos pelo Machine Learning Studio baixam e desserializam automaticamente o conjunto de dados para um objeto DataFrame do pandas. Isso é feito com o método `to_dataframe` :

    frame = ds.to_dataframe()

Se você preferir baixar os dados brutos e executar você mesmo a desserialização, essa é uma opção. No momento, esta é a única opção para formatos como 'ARFF', que a biblioteca de cliente do Python não pode desserializar.

Para ler o conteúdo como texto:

    text_data = ds.read_as_text()

Para ler o conteúdo como binário:

    binary_data = ds.read_as_binary()

Você também pode abrir um fluxo para o conteúdo:

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### <a name="create-a-new-dataset"></a>Criar um novo conjunto de dados
A biblioteca de cliente do Python permite carregar conjuntos de dados de seu programa Python. Esses conjuntos de dados ficarão disponíveis para uso em seu workspace.

Se você tiver seus dados em um DataFrame pandas, use o seguinte código:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Se os seus dados já estiverem serializados, você pode usar:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

A biblioteca cliente Python é capaz de serializar um DataFrame pandas para os seguintes formatos (constantes para eles estão na classe `azureml.DataTypeIds`):

* Texto sem formatação
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

### <a name="update-an-existing-dataset"></a>Atualizar um conjunto de dados existente
Se você tentar carregar um novo conjunto de dados com um nome que corresponda a um conjunto de dados existente, você deverá obter um erro de conflito.

Para atualizar um conjunto de dados existente, primeiro você precisa obter uma referência ao conjunto de dados existente:

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Em seguida, use `update_from_dataframe` para serializar e substituir o conteúdo do conjunto de dados no Azure:

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Se desejar serializar os dados em um formato diferente, especifique um valor para o parâmetro opcional `data_type_id` .

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Opcionalmente, você pode definir uma nova descrição especificando um valor para o parâmetro `description` .

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

Opcionalmente, você pode definir um novo nome especificando um valor para o parâmetro `name` . De agora em diante, você recuperará o conjunto de dados usando o novo nome. O código a seguir atualiza os dados, o nome e a descrição.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        name='existing dataset v2',
        description='data up to feb 2015',
    )

    print(dataset.data_type_id)                    # 'GenericCSV'
    print(dataset.name)                            # 'existing dataset v2'
    print(dataset.description)                     # 'data up to feb 2015'

    print(ws.datasets['existing dataset v2'].name) # 'existing dataset v2'
    print(ws.datasets['existing dataset'].name)    # IndexError

Os parâmetros `data_type_id`, `name` e `description` são opcionais e padrão para seu valor anterior. O parâmetro `dataframe` sempre é necessário.

Se os seus dados já estiverem serializados, use `update_from_raw_data` em vez de `update_from_dataframe`. Se você simplesmente passar `raw_data` em vez de `dataframe`, ele funcionará da mesma forma.

<!-- Images -->
[security]:./media/python-data-access/security.png
[dataset-format]:./media/python-data-access/dataset-format.png
[csv-format]:./media/python-data-access/csv-format.png
[datasets]:./media/python-data-access/datasets.png
[dataset-access-code]:./media/python-data-access/dataset-access-code.png
[ipython-dataset]:./media/python-data-access/ipython-dataset.png
[experiment]:./media/python-data-access/experiment.png
[intermediate-dataset-access-code]:./media/python-data-access/intermediate-dataset-access-code.png
[ipython-intermediate-dataset]:./media/python-data-access/ipython-intermediate-dataset.png
[ipython-histogram]:./media/python-data-access/ipython-histogram.png


<!-- Module References -->
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

