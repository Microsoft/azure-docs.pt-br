---
title: Iniciar, monitorar e cancelar execuções de treinamento em Python
titleSuffix: Azure Machine Learning
description: Saiba como iniciar, status e gerenciar seu experimento de aprendizado de máquina é executado com o SDK Azure Machine Learning Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 977498abb17fe592cef344f407a662d3b79749b7
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "102634747"
---
# <a name="start-monitor-and-track-runs"></a>Iniciar, monitorar e rastrear execuções 

O [SDK do Azure Machine Learning para Python](/python/api/overview/azure/ml/intro), [Machine Learning CLI](reference-azure-machine-learning-cli.md)e [Azure Machine Learning Studio](https://ml.azure.com) fornecem vários métodos para monitorar, organizar e gerenciar suas execuções para treinamento e experimentação.

Este artigo mostra exemplos das seguintes tarefas:

* Monitorar desempenho de execução.
* Monitore o status de execução por notificação por email.
* Marcar e localizar execuções.
* Adicione uma descrição de execução. 
* Executar pesquisa. 
* As execuções de cancelamento ou falha.
* Criar execuções filhas.
 

> [!TIP]
> Se você estiver procurando informações sobre como monitorar o serviço de Azure Machine Learning e os serviços do Azure associados, consulte [como monitorar Azure Machine Learning](monitor-azure-machine-learning.md).
> Se você estiver procurando informações sobre modelos de monitoramento implantados como serviços Web ou módulos IoT Edge, consulte [coletar dados de modelo](how-to-enable-data-collection.md) e [monitorar com Application insights](how-to-enable-app-insights.md).

## <a name="prerequisites"></a>Pré-requisitos

Você precisará dos seguintes itens:

* Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente hoje mesmo a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

* Um [espaço de trabalho Azure Machine Learning](how-to-manage-workspace.md).

* O SDK do Azure Machine Learning para Python (versão 1.0.21 ou posterior). Para instalar ou atualizar para a versão mais recente do SDK, consulte [instalar ou atualizar o SDK](/python/api/overview/azure/ml/install).

    Para verificar sua versão do SDK do Azure Machine Learning, use o seguinte código:

    ```python
    print(azureml.core.VERSION)
    ```

* A [CLI do Azure](/cli/azure/?preserve-view=true&view=azure-cli-latest) e a [extensão da CLI para Azure Machine Learning](reference-azure-machine-learning-cli.md).


## <a name="monitor-run-performance"></a>Monitorar desempenho de execução

* Iniciar uma execução e seu processo de registro em log

    # <a name="python"></a>[Python](#tab/python)
    
    1. Configure seu experimento importando as classes [Workspace](/python/api/azureml-core/azureml.core.workspace.workspace), [experimento](/python/api/azureml-core/azureml.core.experiment.experiment), [Run](/python/api/azureml-core/azureml.core.run%28class%29)e [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) do pacote [azureml. Core](/python/api/azureml-core/azureml.core) .
    
        ```python
        import azureml.core
        from azureml.core import Workspace, Experiment, Run
        from azureml.core import ScriptRunConfig
        
        ws = Workspace.from_config()
        exp = Experiment(workspace=ws, name="explore-runs")
        ```
    
    1. Inicie uma execução e seu processo de registro em log com o [`start_logging()`](/python/api/azureml-core/azureml.core.experiment%28class%29#start-logging--args----kwargs-) método.
    
        ```python
        notebook_run = exp.start_logging()
        notebook_run.log(name="message", value="Hello from run!")
        ```
        
    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
    
    Para iniciar uma execução do seu experimento, use as seguintes etapas:
    
    1. Em um shell ou prompt de comando, use o CLI do Azure para autenticar a sua assinatura do Azure:
    
        ```azurecli-interactive
        az login
        ```
        
        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 
    
    1. Anexe uma configuração de espaço de trabalho à pasta que contém o script de treinamento. Substitua `myworkspace` por seu espaço de trabalho do Azure Machine Learning. Substitua `myresourcegroup` pelo grupo de recursos do Azure que contém seu espaço de trabalho:
    
        ```azurecli-interactive
        az ml folder attach -w myworkspace -g myresourcegroup
        ```
    
        Este comando cria um subdiretório `.azureml` que contém os arquivos de ambiente runconfig e conda de exemplo. Ele também contém um arquivo `config.json` que é usado para se comunicar com seu workspace do Azure Machine Learning.
    
        Para obter mais informações, consulte [az ml folder attach](/cli/azure/ext/azure-cli-ml/ml/folder?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).
    
    2. Para iniciar a execução, use o comando a seguir. Ao usar esse comando, especifique o nome do arquivo runconfig (o texto antes de \*.runconfig se você estiver olhando seu sistema de arquivos) em relação ao parâmetro -c.
    
        ```azurecli-interactive
        az ml run submit-script -c sklearn -e testexperiment train.py
        ```
    
        > [!TIP]
        > O `az ml folder attach` comando criou um `.azureml` subdiretório, que contém dois arquivos de exemplo runconfig.
        >
        > Se você tiver um script Python que cria um objeto de configuração de execução programaticamente, poderá usar [RunConfig.save()](/python/api/azureml-core/azureml.core.runconfiguration#save-path-none--name-none--separate-environment-yaml-false-) para salvá-lo como um arquivo runconfig.
        >
        > Para obter mais exemplos de arquivos runconfig, consulte [https://github.com/MicrosoftDocs/pipelines-azureml/](https://github.com/MicrosoftDocs/pipelines-azureml/) .
    
        Para obter mais informações, consulte [az ml run submit-script](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

    # <a name="studio"></a>[Estúdio](#tab/azure-studio)

    Para obter um exemplo de treinamento de um modelo no designer de Azure Machine Learning, consulte [tutorial: prever o preço do automóvel com o designer](tutorial-designer-automobile-price-train-score.md).

    ---

* Monitorar o status de uma execução

    # <a name="python"></a>[Python](#tab/python)
    
    * Obtenha o status de uma execução com o [`get_status()`](/python/api/azureml-core/azureml.core.run%28class%29#get-status--) método.
    
        ```python
        print(notebook_run.get_status())
        ```
    
    * Para obter a ID de execução, o tempo de execução e os detalhes adicionais sobre a execução, use o [`get_details()`](/python/api/azureml-core/azureml.core.workspace.workspace#get-details--) método.
    
        ```python
        print(notebook_run.get_details())
        ```
    
    * Quando sua execução for concluída com êxito, use o [`complete()`](/python/api/azureml-core/azureml.core.run%28class%29#complete--set-status-true-) método para marcá-la como concluída.
    
        ```python
        notebook_run.complete()
        print(notebook_run.get_status())
        ```
    
    * Se você usar o padrão de design do Python `with...as` , a execução se marcará automaticamente como concluída quando a execução estiver fora do escopo. Você não precisa marcar manualmente a execução como concluída.
        
        ```python
        with exp.start_logging() as notebook_run:
            notebook_run.log(name="message", value="Hello from run!")
            print(notebook_run.get_status())
        
        print(notebook_run.get_status())
        ```
    
    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
    
    * Para exibir uma lista de execuções para o experimento, use o comando a seguir. Substitua `experiment` pelo nome do seu experimento:
    
        ```azurecli-interactive
        az ml run list --experiment-name experiment
        ```
    
        Esse comando retorna um documento JSON que lista informações sobre execuções para este experimento.
    
        Para obter mais informações, consulte [az ml experiment list](/cli/azure/ext/azure-cli-ml/ml/experiment?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).
    
    * Para exibir informações sobre uma execução específica, use o comando a seguir. Substituir `runid` pela ID da execução:
    
        ```azurecli-interactive
        az ml run show -r runid
        ```
    
        Esse comando retorna um documento JSON que lista informações sobre a execução.
    
        Para obter mais informações, consulte [AZ ml Run show](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show).
    
    
    # <a name="studio"></a>[Estúdio](#tab/azure-studio)
    
    Para exibir suas execuções no estúdio: 
    
    1. Navegue até a guia **experimentos** .
    
    1. Selecione **todos os experimentos** para exibir todas as execuções em um experimento ou selecione **todas as execuções** para exibir todas as execuções enviadas no espaço de trabalho.
    
        Na página **todas as execuções** , você pode filtrar a lista de execuções por marcas, experimentos, destino de computação e muito mais para organizar e fazer o escopo de seu trabalho melhor.  
    
    1. Faça personalizações na página selecionando execuções para comparar, adicionando gráficos ou aplicando filtros. Essas alterações podem ser salvas como uma **exibição personalizada** para que você possa retornar facilmente ao seu trabalho. Os usuários com permissões de espaço de trabalho podem editar ou exibir o modo de exibição personalizado. Além disso, compartilhe a exibição personalizada com membros da equipe para colaboração aprimorada selecionando **exibição de compartilhamento**.   
    
        :::image type="content" source="media/how-to-manage-runs/custom-views.gif" alt-text="Captura de tela: criar uma exibição personalizada":::
    
    1. Para exibir os logs de execução, selecione uma execução específica e, na guia **saídas + logs** , você pode encontrar logs de diagnóstico e de erros para sua execução.
    
    ---

## <a name="monitor-the-run-status-by-email-notification"></a>Monitorar o status de execução por notificação por email

1. Na [portal do Azure](https://ms.portal.azure.com/), na barra de navegação à esquerda, selecione a guia **Monitor** . 

1. Selecione **configurações de diagnóstico** e, em seguida, selecione **+ Adicionar configuração de diagnóstico**.

    ![Captura de tela das configurações de diagnóstico para notificação por email](./media/how-to-manage-runs/diagnostic-setting.png)

1. Na configuração de diagnóstico, 
    1. nos **detalhes da categoria**, selecione o **AmlRunStatusChangedEvent**. 
    1. Nos **detalhes de destino**, selecione o **espaço de trabalho enviar para log Analytics**  e especifique a **assinatura** e o **espaço de trabalho log Analytics**. 

    > [!NOTE]
    > O **espaço de trabalho do azure log Analytics** é um tipo diferente de recurso do Azure do que o **espaço de trabalho do Azure Machine Learning Service**. Se não houver nenhuma opção nessa lista, você poderá [criar um log Analytics espaço de trabalho](https://docs.microsoft.com/azure/azure-monitor/logs/quick-create-workspace). 
    
    ![Onde salvar a notificação por email](./media/how-to-manage-runs/log-location.png)

1. Na guia **logs** , adicione uma **nova regra de alerta**. 

    ![Nova regra de alerta](./media/how-to-manage-runs/new-alert-rule.png)

1. Consulte [como criar e gerenciar alertas de log usando o Azure monitor](https://docs.microsoft.com/azure/azure-monitor/alerts/alerts-log).

## <a name="run-description"></a>Descrição da execução 

Uma descrição de execução pode ser adicionada a uma execução para fornecer mais contexto e informações para a execução. Você também pode pesquisar nessas descrições na lista execuções e adicionar a descrição de execução como uma coluna na lista execuções. 

Navegue até a página de **detalhes da execução** da sua execução e selecione o ícone Editar ou lápis para adicionar, editar ou excluir descrições para sua execução. Para manter as alterações na lista de execuções, salve as alterações em sua exibição personalizada existente ou em uma nova exibição personalizada. O formato de redução tem suporte para descrições de execução, o que permite que as imagens sejam incorporadas e vinculação profunda, conforme mostrado abaixo.

:::image type="content" source="media/how-to-manage-runs/run-description.gif" alt-text="Captura de tela: criar uma descrição de execução"::: 

## <a name="tag-and-find-runs"></a>Marcar e localizar execuções

No Azure Machine Learning, você pode usar as propriedades e marcas para ajudar a organizar e consultar suas execuções para obter informações importantes.

* Adicionar propriedades e marcas

    # <a name="python"></a>[Python](#tab/python)
    
    Para adicionar metadados pesquisáveis às suas execuções, use o [`add_properties()`](/python/api/azureml-core/azureml.core.run%28class%29#add-properties-properties-) método. Por exemplo, o código a seguir adiciona a `"author"` Propriedade à execução:
    
    ```Python
    local_run.add_properties({"author":"azureml-user"})
    print(local_run.get_properties())
    ```
    
    As propriedades são imutáveis e, portanto, criam um registro permanente para fins de auditoria. O exemplo de código a seguir resulta em um erro, porque já adicionamos `"azureml-user"` como o `"author"` valor da propriedade no código anterior:
    
    ```Python
    try:
        local_run.add_properties({"author":"different-user"})
    except Exception as e:
        print(e)
    ```
    
    Ao contrário das propriedades, as marcas são mutáveis. Para adicionar informações de pesquisa e significativas para os consumidores de seu experimento, use o [`tag()`](/python/api/azureml-core/azureml.core.run%28class%29#tag-key--value-none-) método.
    
    ```Python
    local_run.tag("quality", "great run")
    print(local_run.get_tags())
    
    local_run.tag("quality", "fantastic run")
    print(local_run.get_tags())
    ```
    
    Você também pode adicionar marcas de cadeia de caracteres simples. Quando essas marcas aparecem no dicionário de marcas como chaves, elas têm um valor de `None` .
    
    ```Python
    local_run.tag("worth another look")
    print(local_run.get_tags())
    ```
    
    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
    
    > [!NOTE]
    > Usando a CLI, você só pode adicionar ou atualizar marcas.
    
    Para adicionar ou atualizar uma marca, use o seguinte comando:
    
    ```azurecli-interactive
    az ml run update -r runid --add-tag quality='fantastic run'
    ```
    
    Para obter mais informações, consulte [AZ ml Run Update](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update).
    
    # <a name="studio"></a>[Estúdio](#tab/azure-studio)
    
    Você pode adicionar, editar ou excluir marcas de execução do estúdio. Navegue até a página de **detalhes da execução** da sua execução e selecione o ícone Editar ou lápis para adicionar, editar ou excluir marcas para suas execuções. Você também pode pesquisar e filtrar essas marcas na página de lista de execuções.
    
    :::image type="content" source="media/how-to-manage-runs/run-tags.gif" alt-text="Captura de tela: Adicionar, editar ou excluir marcas de execução":::
    
    ---

* Propriedades e marcas de consulta

    Você pode consultar execuções em um experimento para retornar uma lista de execuções que correspondem a propriedades e marcas específicas.

    # <a name="python"></a>[Python](#tab/python)
    
    ```Python
    list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
    list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
    ```
    
    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
    
    O CLI do Azure dá suporte a consultas [JMESPath](http://jmespath.org) , que podem ser usadas para filtrar execuções com base em Propriedades e marcas. Para usar uma consulta JMESPath com o CLI do Azure, especifique-o com o `--query` parâmetro. Os exemplos a seguir mostram algumas consultas usando propriedades e marcas:
    
    ```azurecli-interactive
    # list runs where the author property = 'azureml-user'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user']
    # list runs where the tag contains a key that starts with 'worth another look'
    az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
    # list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
    ```
    
    Para obter mais informações sobre como consultar resultados de CLI do Azure, consulte [consultar CLI do Azure comando de saída](/cli/azure/query-azure-cli?preserve-view=true&view=azure-cli-latest).
    
    # <a name="studio"></a>[Estúdio](#tab/azure-studio)
    
    Para pesquisar execuções específicas, navegue até a lista  **todas as execuções** . A partir daí, você tem duas opções:
    
    1. Use o botão **Adicionar filtro** e selecione filtrar nas marcas para filtrar suas execuções por marca que foram atribuídas às execuções. <br><br>
    OU
    
    1. Use a barra de pesquisa para encontrar execuções rapidamente pesquisando nos metadados de execução, como o status de execução, descrições, nomes de experimento e nome do emissor. 
    
## <a name="cancel-or-fail-runs"></a>Cancelar ou falhar execuções

Se você notar um erro ou se sua execução estiver demorando muito para ser concluída, você poderá cancelar a execução.

# <a name="python"></a>[Python](#tab/python)

Para cancelar uma execução usando o SDK, use o [`cancel()`](/python/api/azureml-core/azureml.core.run%28class%29#cancel--) método:

```python
src = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_run = exp.submit(src)
print(local_run.get_status())

local_run.cancel()
print(local_run.get_status())
```

Se sua execução for concluída, mas contiver um erro (por exemplo, o script de treinamento incorreto foi usado), você poderá usar o [`fail()`](/python/api/azureml-core/azureml.core.run%28class%29#fail-error-details-none--error-code-none---set-status-true-) método para marcá-lo como com falha.

```python
local_run = exp.submit(src)
local_run.fail()
print(local_run.get_status())
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para cancelar uma execução usando a CLI, use o comando a seguir. Substituir `runid` pela ID da execução

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

Para obter mais informações, consulte [AZ ml Run Cancel](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel).

# <a name="studio"></a>[Estúdio](#tab/azure-studio)

Para cancelar uma execução no estúdio, usando as seguintes etapas:

1. Vá para o pipeline em execução na seção **experimentos** ou **pipelines** . 

1. Selecione o número de execução de pipeline que você deseja cancelar.

1. Na barra de ferramentas, selecione **Cancelar**

---

## <a name="create-child-runs"></a>Criar execuções filhas

Crie execuções filhas para agrupar execuções relacionadas, como para diferentes iterações de ajuste de hiperparâmetro.

> [!NOTE]
> As execuções filhas só podem ser criadas usando o SDK.

Este exemplo de código usa o `hello_with_children.py` script para criar um lote de cinco execuções filhas de dentro de uma execução enviada usando o [`child_run()`](/python/api/azureml-core/azureml.core.run%28class%29#child-run-name-none--run-id-none--outputs-none-) método:

```python
!more hello_with_children.py
src = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_run = exp.submit(src)
local_run.wait_for_completion(show_output=True)
print(local_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> À medida que eles saem do escopo, as execuções filhas são automaticamente marcadas como concluídas.

Para criar várias execuções filhas com eficiência, use o [`create_children()`](/python/api/azureml-core/azureml.core.run.run#create-children-count-none--tag-key-none--tag-values-none-) método. Como cada criação resulta em uma chamada de rede, a criação de um lote de execuções é mais eficiente do que criá-los um a um.

### <a name="submit-child-runs"></a>Enviar execuções filhas

As execuções filhas também podem ser enviadas de uma execução pai. Isso permite que você crie hierarquias de execuções pai e filho. Não é possível criar uma execução filho sem pai: mesmo que a execução pai não faça nada, mas inicie execuções filhas, ainda é necessário criar a hierarquia. Os status de todas as execuções são independentes: um pai pode estar no `"Completed"` estado bem-sucedido mesmo que uma ou mais execuções filhas tenham sido canceladas ou com falha.  

Você pode desejar que seu filho seja executado para usar uma configuração de execução diferente da execução do pai. Por exemplo, você pode usar uma configuração menos eficiente baseada em CPU para o pai, ao usar configurações baseadas em GPU para seus filhos. Outro desejo comum é passar cada um dos diferentes argumentos e dados filho. Para personalizar uma execução filho, crie um `ScriptRunConfig` objeto para a execução do filho. O código abaixo faz o seguinte:

- Recuperar um recurso de computação chamado `"gpu-cluster"` a partir do espaço de trabalho `ws`
- Itera sobre valores de argumento diferentes a serem passados para os `ScriptRunConfig` objetos filhos
- Cria e envia uma nova execução de filho, usando o recurso de computação personalizado e o argumento
- Bloqueia até que todas as execuções do filho sejam concluídas

```python
# parent.py
# This script controls the launching of child scripts
from azureml.core import Run, ScriptRunConfig

compute_target = ws.compute_targets["gpu-cluster"]

run = Run.get_context()

child_args = ['Apple', 'Banana', 'Orange']
for arg in child_args: 
    run.log('Status', f'Launching {arg}')
    child_config = ScriptRunConfig(source_directory=".", script='child.py', arguments=['--fruit', arg], compute_target=compute_target)
    # Starts the run asynchronously
    run.submit_child(child_config)

# Experiment will "complete" successfully at this point. 
# Instead of returning immediately, block until child runs complete

for child in run.get_children():
    child.wait_for_completion()
```

Para criar várias execuções filhas com configurações, argumentos e entradas idênticas com eficiência, use o [`create_children()`](/python/api/azureml-core/azureml.core.run.run#create-children-count-none--tag-key-none--tag-values-none-) método. Como cada criação resulta em uma chamada de rede, a criação de um lote de execuções é mais eficiente do que criá-los um a um.

Em uma execução de filho, você pode exibir a ID de execução pai:

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>Consultar execuções filhas

Para consultar as execuções filhas de um pai específico, use o [`get_children()`](/python/api/azureml-core/azureml.core.run%28class%29#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) método. O ``recursive = True`` argumento permite consultar uma árvore aninhada de Children e netos.

```python
print(parent_run.get_children())
```

### <a name="log-to-parent-or-root-run"></a>Log para a execução de pai ou raiz

Você pode usar o `Run.parent` campo para acessar a execução que iniciou a execução do filho atual. Um caso de uso comum para isso é quando você deseja consolidar os resultados de log em um único lugar. Observe que o filho é executado de forma assíncrona e não há nenhuma garantia de ordenação ou sincronização além da capacidade do pai de aguardar a conclusão de suas execuções filhas.

```python
# in child (or even grandchild) run

def root_run(self : Run) -> Run :
    if self.parent is None : 
        return self
    return root_run(self.parent)

current_child_run = Run.get_context()
root_run(current_child_run).log("MyMetric", f"Data from child run {current_child_run.id}")

```

## <a name="example-notebooks"></a>Blocos de anotações de exemplo

Os blocos de anotações a seguir demonstram os conceitos neste artigo:

* Para saber mais sobre as APIs de log, consulte o [bloco de anotações da API de log](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb).

* Para obter mais informações sobre o gerenciamento de execuções com o SDK do Azure Machine Learning, consulte o [bloco de anotações gerenciar execuções](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb).

## <a name="next-steps"></a>Próximas etapas

* Para saber como registrar métricas para seus experimentos, consulte [métricas de log durante execuções de treinamento](how-to-track-experiments.md).
* Para saber como monitorar recursos e logs de Azure Machine Learning, consulte [Azure Machine Learning de monitoramento](monitor-azure-machine-learning.md).
