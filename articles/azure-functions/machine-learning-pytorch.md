---
title: Implantar um modelo do PyTorch como um aplicativo do Azure Functions
description: Use uma rede neural profunda ResNet 18 pré-treinada no PyTorch com o Azure Functions para atribuir 1 de 1.000 rótulos de ImageNet a uma imagem.
author: gvashishtha
ms.topic: tutorial
ms.date: 02/28/2020
ms.author: gopalv
ms.custom: devx-track-python, devx-track-azurepowershell
ms.openlocfilehash: 8891c29e5d8d06df6292d06ec06e5e57fb9880e7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93422834"
---
# <a name="tutorial-deploy-a-pre-trained-image-classification-model-to-azure-functions-with-pytorch"></a>Tutorial: Implantar um modelo de classificação de imagem pré-treinado no Azure Functions com o PyTorch

Neste artigo, você aprenderá a usar o Python, o PyTorch e o Azure Functions para carregar um modelo pré-treinado, a fim de classificar uma imagem com base no conteúdo. Como você faz todo o trabalho localmente e cria recursos do Azure na nuvem, não há nenhum custo para concluir este tutorial.

> [!div class="checklist"]
> * Inicialize um ambiente local para o desenvolvimento do Azure Functions no Python.
> * Importe um modelo de machine learning pré-treinado do PyTorch para um aplicativo de funções.
> * Crie uma API HTTP sem servidor para classificar uma imagem como uma das mil [classes](https://gist.github.com/yrevar/942d3a0ac09ec9e5eb3a) do ImageNet.
> * Consuma a API em um aplicativo Web.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Python 3.7.4 ou superior](https://www.python.org/downloads/release/python-374/). (O Python 3.8.x e o Python 3.6.x também são verificados com o Azure Functions.)
- O [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools)
- Um editor de códigos como o [Visual Studio Code](https://code.visualstudio.com/)

### <a name="prerequisite-check"></a>Verificação de pré-requisitos

1. Em um terminal ou uma janela Comando, execute `func --version` para verificar se a versão do Azure Functions Core Tools é 2.7.1846 ou posterior.
1. Execute `python --version` (Linux/macOS) ou `py --version` (Windows) para verificar se a versão do Python é 3.7.x.

## <a name="clone-the-tutorial-repository"></a>Clonar o repositório do tutorial

1. Em um terminal ou uma janela Comando, clone o seguinte repositório usando o Git:

    ```
    git clone https://github.com/Azure-Samples/functions-python-pytorch-tutorial.git
    ```

1. Navegue até a pasta e examine o conteúdo.

    ```
    cd functions-python-pytorch-tutorial
    ```

    - *start* é a pasta de trabalho do tutorial.
    - *end* é o resultado final e a implementação completa para sua referência.
    - *resources* contém o modelo de machine learning e as bibliotecas auxiliares.
    - *frontend* é um site que chama o aplicativo de funções.

## <a name="create-and-activate-a-python-virtual-environment"></a>Criar e ativar um ambiente virtual do Python

Navegue até a pasta *start* e execute os comandos a seguir para criar e ativar um ambiente virtual chamado `.venv`.


# <a name="bash"></a>[Bash](#tab/bash)

```bash
cd start
python -m venv .venv
source .venv/bin/activate
```

Se o Python não instalou o pacote venv na distribuição do Linux, execute o seguinte comando:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
cd start
py -m venv .venv
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
cd start
py -m venv .venv
.venv\scripts\activate
```

---

Você executará todos os comandos posteriores neste ambiente virtual ativado. (Para sair do ambiente virtual, execute `deactivate`.)


## <a name="create-a-local-functions-project"></a>Criar um projeto local do Functions

No Azure Functions, um projeto de função é um contêiner para uma ou mais funções individuais que respondem, cada uma, a um gatilho específico. Todas as funções em um projeto compartilham as configurações locais e de hospedagem. Nesta seção, você criará um projeto de função que contém uma só função clichê chamada `classify` que fornece um ponto de extremidade HTTP. Você adicionará um código mais específico em uma seção posterior.

1. Na pasta *start*, use o Azure Functions Core Tools para inicializar um aplicativo de funções do Python:

    ```
    func init --worker-runtime python
    ```

    Após a inicialização, a pasta *start* contém vários arquivos para o projeto, incluindo arquivos de configuração chamados [local.settings.json](functions-run-local.md#local-settings-file) e [host.json](functions-host-json.md). Como *local.settings.json* pode conter segredos baixados do Azure, o arquivo é excluído do controle do código-fonte por padrão no arquivo *.gitignore*.

    > [!TIP]
    > Como um projeto de função está vinculado a um runtime específico, todas as funções no projeto precisam ser escritas com a mesma linguagem.

1. Adicione uma função ao projeto usando o comando a seguir, em que o argumento `--name` é o nome exclusivo da função e o argumento `--template` especifica o gatilho da função. `func new` cria uma subpasta correspondente ao nome da função que contém um arquivo de código apropriado para a linguagem escolhida do projeto, bem como um arquivo de configuração chamado *function.json*.

    ```
    func new --name classify --template "HTTP trigger"
    ```

    Esse comando cria uma pasta correspondente ao nome da função, *classify*. Nessa pasta, há dois arquivos: *\_\_init\_\_.py*, que contém o código da função, e *function.json*, que descreve o gatilho da função e as associações de entrada e saída. Para obter detalhes sobre o conteúdo desses arquivos, confira [Examinar o conteúdo do arquivo](./create-first-function-cli-python.md#optional-examine-the-file-contents) no início rápido do Python.


## <a name="run-the-function-locally"></a>Executar a função localmente

1. Inicie a função iniciando o host de runtime local do Azure Functions na pasta *start*:

    ```
    func start
    ```

1. Depois de ver o ponto de extremidade `classify` ser exibido na saída, navegue até a URL, ```http://localhost:7071/api/classify?name=Azure```. A mensagem "Olá, Azure!" deverá ser exibida na saída.

1. Use **CTRL**-**C** para interromper o host.


## <a name="import-the-pytorch-model-and-add-helper-code"></a>Importar o modelo do PyTorch e adicionar o código auxiliar

Para modificar a função `classify` para classificar uma imagem com base no conteúdo, use um modelo do [ResNet](https://arxiv.org/abs/1512.03385) pré-treinado. O modelo pré-treinado, proveniente do [PyTorch](https://pytorch.org/hub/pytorch_vision_resnet/), classifica uma imagem em 1 de 1.000 [classes ImageNet](https://gist.github.com/yrevar/942d3a0ac09ec9e5eb3a). Em seguida, você adicionará um código auxiliar e as dependências ao projeto.

1. Na pasta *start*, execute o comando a seguir para copiar o código de previsão e os rótulos na pasta *classify*.

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    cp ../resources/predict.py classify
    cp ../resources/labels.txt classify
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    copy ..\resources\predict.py classify
    copy ..\resources\labels.txt classify
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    copy ..\resources\predict.py classify
    copy ..\resources\labels.txt classify
    ```

    ---

1. Verifique se a pasta *classify* contém arquivos chamados *predict.py* e *labels.txt*. Caso contrário, verifique se você executou o comando na pasta *start*.

1. Abra *start/requirements.txt* em um editor de texto e adicione as dependências necessárias para o código auxiliar, que deve ter a seguinte aparência:

    ```txt
    azure-functions
    requests
    -f https://download.pytorch.org/whl/torch_stable.html
    torch==1.5.0+cpu
    torchvision==0.6.0+cpu
    ```

1. Salve *requirements.txt* e, em seguida, execute o comando a seguir na pasta *start* para instalar as dependências.


    ```
    pip install --no-cache-dir -r requirements.txt
    ```

A instalação poderá levar alguns minutos, durante os quais você poderá prosseguir com a modificação da função na próxima seção.
> [!TIP]
> >No Windows, você poderá receber o erro "Não foi possível instalar pacotes devido a um EnvironmentError: [Errno 2] Esse arquivo ou esse diretório não existe:" seguido por um nome de caminho longo para um arquivo como *sharded_mutable_dense_hashtable.cpython-37.pyc*. Normalmente, esse erro ocorre porque a profundidade do caminho da pasta se torna muito longa. Nesse caso, defina a chave do Registro `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem@LongPathsEnabled` como `1` para habilitar caminhos longos. Como alternativa, verifique a localização em que o interpretador do Python está instalado. Se essa localização tiver um caminho longo, tente fazer a reinstalação em uma pasta com um caminho mais curto.

## <a name="update-the-function-to-run-predictions"></a>Atualizar a função para executar previsões

1. Abra *classify/\_\_init\_\_.py* em um editor de texto e adicione as seguintes linhas após as instruções `import` existentes para importar a biblioteca JSON padrão e os auxiliares de *previsão*:

    :::code language="python" source="~/functions-pytorch/end/classify/__init__.py" range="1-6" highlight="5-6":::

1. Substitua todo o conteúdo da função `main` pelo seguinte código:

    :::code language="python" source="~/functions-pytorch/end/classify/__init__.py" range="8-19":::

    Essa função recebe uma URL de imagem em um parâmetro de cadeia de caracteres de consulta chamado `img`. Em seguida, ela chama `predict_image_from_url` na biblioteca auxiliar para baixar e classificar a imagem usando o modelo do PyTorch. Em seguida, a função retorna uma resposta HTTP com os resultados.

    > [!IMPORTANT]
    > Como o ponto de extremidade HTTP é chamado por uma página da Web hospedada em outro domínio, a resposta inclui um cabeçalho `Access-Control-Allow-Origin` para atender aos requisitos do CORS (compartilhamento de recursos entre origens) do navegador.
    >
    > Em um aplicativo de produção, altere `*` para a origem específica da página da Web para obter segurança adicional.

1. Salve as alterações e, em seguida, supondo que as dependências tenham concluído a instalação, inicie o host da função local novamente com `func start`. Lembre-se de executar o host na pasta *start* com o ambiente virtual ativado. Caso contrário, o host será iniciado, mas você verá erros ao invocar a função.

    ```
    func start
    ```

1. Em um navegador, abra a URL a seguir para invocar a função com a URL de uma imagem de um bernese e confirme se o JSON retornado classifica a imagem como um bernese.

    ```
    http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/Bernese-Mountain-Dog-Temperament-long.jpg
    ```

1. Mantenha o host em execução porque você o usará na próxima etapa.

### <a name="run-the-local-web-app-front-end-to-test-the-function"></a>Executar o front-end do aplicativo Web local para testar a função

Para testar a invocação do ponto de extremidade de função em outro aplicativo Web, há um aplicativo simples na pasta *frontend* do repositório.

1. Abra um novo terminal ou um prompt de comando e ative o ambiente virtual (conforme descrito anteriormente em [Criar e ativar um ambiente virtual do Python](#create-and-activate-a-python-virtual-environment)).

1. Navegue até a pasta *frontend* do repositório.

1. Inicie um servidor HTTP com o Python:

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    python -m http.server
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    py -m http.server
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    py -m http.server
    ```

1. Em um navegador, navegue até `localhost:8000` e, em seguida, insira uma das URLs de foto a seguir na caixa de texto ou use a URL de qualquer imagem acessível publicamente.

    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/Bernese-Mountain-Dog-Temperament-long.jpg`
    - `https://github.com/Azure-Samples/functions-python-pytorch-tutorial/blob/master/resources/assets/bald-eagle.jpg?raw=true`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/penguin.jpg`

1. Selecione **Enviar** para invocar o ponto de extremidade da função para classificar a imagem.

    ![Captura de tela do projeto concluído](media/machine-learning-pytorch/screenshot.png)

    Se o navegador relatar um erro quando você enviar a URL da imagem, verifique o terminal no qual você está executando o aplicativo de funções. Caso receba um erro como "Nenhum módulo encontrou a 'PIL'", você poderá ter iniciado o aplicativo de funções na pasta *start* sem primeiro ativar o ambiente virtual criado anteriormente. Se você ainda receber erros, execute `pip install -r requirements.txt` novamente com o ambiente virtual ativado e procure erros.

## <a name="clean-up-resources"></a>Limpar os recursos

Como todo este tutorial é executado localmente no computador, não há recursos nem serviços do Azure a serem limpos.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a criar e personalizar um ponto de extremidade de API HTTP com o Azure Functions para classificar imagens usando um modelo do PyTorch. Você também aprendeu a chamar a API em um aplicativo Web. É possível usar as técnicas deste tutorial para criar APIs de qualquer complexidade, fazendo-o simultaneamente à execução no modelo de computação sem servidor fornecido pelo Azure Functions.

Consulte também:

- [Implantar a função no Azure usando o Visual Studio Code](https://code.visualstudio.com/docs/python/tutorial-azure-functions).
- [Guia do desenvolvedor do Python para o Azure Functions](./functions-reference-python.md)


> [!div class="nextstepaction"]
> [Implantar a função no Azure Functions usando o guia da CLI do Azure](./functions-run-local.md#publish)
