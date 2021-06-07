---
title: Usar Python e TensorFlow para Machine Learning no Azure
description: Use o Python, o TensorFlow e o Azure Functions com um modelo de machine learning para classificar uma imagem com base no conteúdo.
author: anthonychu
ms.topic: tutorial
ms.date: 01/15/2020
ms.author: antchu
ms.custom: mvc, devx-track-python, devx-track-azurepowershell
ms.openlocfilehash: ca9ce27583168dfee1a597fce559afad38a3a8c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95994597"
---
# <a name="tutorial-apply-machine-learning-models-in-azure-functions-with-python-and-tensorflow"></a>Tutorial: Aplicar modelos de machine learning no Azure Functions com o Python e o TensorFlow

Neste artigo, você aprenderá a usar o Python, o TensorFlow e o Azure Functions com um modelo de machine learning para classificar uma imagem com base no conteúdo. Como você faz todo o trabalho localmente e cria recursos do Azure na nuvem, não há nenhum custo para concluir este tutorial.

> [!div class="checklist"]
> * Inicialize um ambiente local para o desenvolvimento do Azure Functions no Python.
> * Importe um modelo de machine learning personalizado do TensorFlow para um aplicativo de funções.
> * Crie uma API HTTP sem servidor para classificar uma imagem como contendo um cachorro ou um gato.
> * Consuma a API em um aplicativo Web.

## <a name="prerequisites"></a>Pré-requisitos 

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Python 3.7.4](https://www.python.org/downloads/release/python-374/). (Python 3.7.4 e Python 3.6.x são verificados com o Azure Functions; ainda não há suporte para o Python 3.8 e versões posteriores.)
- O [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools)
- Um editor de códigos como o [Visual Studio Code](https://code.visualstudio.com/)

### <a name="prerequisite-check"></a>Verificação de pré-requisitos

1. Em um terminal ou uma janela Comando, execute `func --version` para verificar se a versão do Azure Functions Core Tools é 2.7.1846 ou posterior.
1. Execute `python --version` (Linux/macOS) ou `py --version` (Windows) para verificar se a versão do Python é 3.7.x.

## <a name="clone-the-tutorial-repository"></a>Clonar o repositório do tutorial

1. Em um terminal ou uma janela Comando, clone o seguinte repositório usando o Git:

    ```
    git clone https://github.com/Azure-Samples/functions-python-tensorflow-tutorial.git
    ```

1. Navegue até a pasta e examine o conteúdo.

    ```
    cd functions-python-tensorflow-tutorial
    ```

    - *start* é a pasta de trabalho do tutorial.
    - *end* é o resultado final e a implementação completa para sua referência.
    - *resources* contém o modelo de machine learning e as bibliotecas auxiliares.
    - *frontend* é um site que chama o aplicativo de funções.
    
## <a name="create-and-activate-a-python-virtual-environment"></a>Criar e ativar um ambiente virtual do Python

Navegue até a pasta *start* e execute os comandos a seguir para criar e ativar um ambiente virtual chamado `.venv`. Lembre-se de usar o Python 3.7, que é compatível com o Azure Functions.


# <a name="bash"></a>[Bash](#tab/bash)

```bash
cd start
```

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Se o Python não instalou o pacote venv na distribuição do Linux, execute o seguinte comando:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
cd start
```

```powershell
py -3.7 -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
cd start
```

```cmd
py -3.7 -m venv .venv
```

```cmd
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


## <a name="import-the-tensorflow-model-and-add-helper-code"></a>Importar o modelo do TensorFlow e adicionar o código auxiliar

Para modificar a função `classify` para classificar uma imagem com base no conteúdo, você usará um modelo predefinido do TensorFlow que foi treinado com o Serviço de Visão Personalizada do Azure e exportado dele. O modelo, que está contido na pasta *resources* da amostra clonada anteriormente, classifica uma imagem com base em se ela contém um cachorro ou um gato. Em seguida, você adicionará um código auxiliar e as dependências ao projeto.

Para criar o próprio modelo usando a camada gratuita do Serviço de Visão Personalizada, siga as instruções no [repositório do projeto de exemplo](https://github.com/Azure-Samples/functions-python-tensorflow-tutorial/blob/master/train-custom-vision-model.md).

> [!TIP]
> Se desejar hospedar seu modelo TensorFlow independente do aplicativo de funções, poderá montar um compartilhamento de arquivo que contenha seu modelo para seu aplicativo de funções do Linux. Para saber mais, confira [Montar um compartilhamento de arquivo para um aplicativo de funções Python usando a CLI do Azure](./scripts/functions-cli-mount-files-storage-linux.md).

1. Na pasta *start*, execute o comando a seguir para copiar os arquivos de modelo para a pasta *classify*. Lembre-se de incluir `\*` no comando. 

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    cp ../resources/model/* classify
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    copy ..\resources\model\* classify
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    copy ..\resources\model\* classify
    ```
    
    ---
    
1. Verifique se a pasta *classify* contém arquivos chamados *model.pb* e *labels.txt*. Caso contrário, verifique se você executou o comando na pasta *start*.

1. Na pasta *start*, execute o seguinte comando para copiar um arquivo com o código auxiliar para a pasta *classify*:

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    cp ../resources/predict.py classify
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    copy ..\resources\predict.py classify
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    copy ..\resources\predict.py classify
    ```
    
    ---

1. Verifique se a pasta *classify* agora contém um arquivo chamado *predict.py*.

1. Abra *start/requirements.txt* em um editor de texto e adicione as seguintes dependências necessárias para o código auxiliar:

    ```txt
    tensorflow==1.14
    Pillow
    requests
    ```
    
1. Salve *requirements.txt*.

1. Instale as dependências executando o comando a seguir na pasta *start*. A instalação poderá levar alguns minutos, durante os quais você poderá prosseguir com a modificação da função na próxima seção.

    ```
    pip install --no-cache-dir -r requirements.txt
    ```
    
    No Windows, você poderá receber o erro "Não foi possível instalar pacotes devido a um EnvironmentError: [Errno 2] Esse arquivo ou esse diretório não existe:" seguido por um nome de caminho longo para um arquivo como *sharded_mutable_dense_hashtable.cpython-37.pyc*. Normalmente, esse erro ocorre porque a profundidade do caminho da pasta se torna muito longa. Nesse caso, defina a chave do Registro `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem@LongPathsEnabled` como `1` para habilitar caminhos longos. Como alternativa, verifique a localização em que o interpretador do Python está instalado. Se essa localização tiver um caminho longo, tente fazer a reinstalação em uma pasta com um caminho mais curto.

> [!TIP]
> Ao chamar *predict.py* para fazer a primeira previsão, uma função chamada `_initialize` carrega o modelo do TensorFlow do disco e o armazena em cache em variáveis globais. Esse cache acelera as previsões posteriores. Para obter mais informações sobre como usar variáveis globais, veja o [guia do desenvolvedor do Python para o Azure Functions](functions-reference-python.md#global-variables).

## <a name="update-the-function-to-run-predictions"></a>Atualizar a função para executar previsões

1. Abra *classify/\_\_init\_\_.py* em um editor de texto e adicione as seguintes linhas após as instruções `import` existentes para importar a biblioteca JSON padrão e os auxiliares de *previsão*:

    :::code language="python" source="~/functions-python-tensorflow-tutorial/end/classify/__init__.py" range="1-6" highlight="5-6":::

1. Substitua todo o conteúdo da função `main` pelo seguinte código:

    :::code language="python" source="~/functions-python-tensorflow-tutorial/end/classify/__init__.py" range="8-19":::

    Essa função recebe uma URL de imagem em um parâmetro de cadeia de caracteres de consulta chamado `img`. Em seguida, ela chama `predict_image_from_url` na biblioteca auxiliar para baixar e classificar a imagem usando o modelo do TensorFlow. Em seguida, a função retorna uma resposta HTTP com os resultados. 

    > [!IMPORTANT]
    > Como o ponto de extremidade HTTP é chamado por uma página da Web hospedada em outro domínio, a resposta inclui um cabeçalho `Access-Control-Allow-Origin` para atender aos requisitos do CORS (compartilhamento de recursos entre origens) do navegador.
    >
    > Em um aplicativo de produção, altere `*` para a origem específica da página da Web para obter segurança adicional.

1. Salve as alterações e, em seguida, supondo que as dependências tenham concluído a instalação, inicie o host da função local novamente com `func start`. Lembre-se de executar o host na pasta *start* com o ambiente virtual ativado. Caso contrário, o host será iniciado, mas você verá erros ao invocar a função.

    ```
    func start
    ```

1. Em um navegador, abra a URL a seguir para invocar a função com a URL da imagem de um gato e confirme se o JSON retornado classifica a imagem como um gato.

    ```
    http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png
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

    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat2.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog1.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog2.png`
    
1. Selecione **Enviar** para invocar o ponto de extremidade da função para classificar a imagem.

    ![Captura de tela do projeto concluído](media/functions-machine-learning-tensorflow/functions-machine-learning-tensorflow-screenshot.png)

    Se o navegador relatar um erro quando você enviar a URL da imagem, verifique o terminal no qual você está executando o aplicativo de funções. Caso receba um erro como "Nenhum módulo encontrou a 'PIL'", você poderá ter iniciado o aplicativo de funções na pasta *start* sem primeiro ativar o ambiente virtual criado anteriormente. Se você ainda receber erros, execute `pip install -r requirements.txt` novamente com o ambiente virtual ativado e procure erros.

> [!NOTE]
> O modelo sempre classifica o conteúdo da imagem como um gato ou um cachorro, independentemente de a imagem conter um dos dois, usando cachorro como padrão. As imagens de tigres e panteras, por exemplo, normalmente são classificadas como gatos, mas as imagens de elefantes, cenouras ou aviões são classificadas como cachorros.

## <a name="clean-up-resources"></a>Limpar os recursos

Como todo este tutorial é executado localmente no computador, não há recursos nem serviços do Azure a serem limpos.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a criar e personalizar um ponto de extremidade de API HTTP com o Azure Functions para classificar imagens usando um modelo do TensorFlow. Você também aprendeu a chamar a API em um aplicativo Web. É possível usar as técnicas deste tutorial para criar APIs de qualquer complexidade, fazendo-o simultaneamente à execução no modelo de computação sem servidor fornecido pelo Azure Functions.

> [!div class="nextstepaction"]
> [Implantar a função no Azure Functions usando o guia da CLI do Azure](./functions-run-local.md#publish)

Consulte também:

- [Implantar a função no Azure usando o Visual Studio Code](https://code.visualstudio.com/docs/python/tutorial-azure-functions).
- [Guia do desenvolvedor do Python para o Azure Functions](./functions-reference-python.md)
- [Montar um compartilhamento de arquivo para um aplicativo de funções Python usando a CLI do Azure](./scripts/functions-cli-mount-files-storage-linux.md)
