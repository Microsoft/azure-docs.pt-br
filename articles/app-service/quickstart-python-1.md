---
title: 'Início Rápido: Criar um aplicativo Python no Linux'
description: Comece a usar o Serviço de Aplicativo do Azure implantando um aplicativo Python em um contêiner do Linux no Serviço de Aplicativo.
ms.topic: quickstart
ms.date: 09/22/2020
ms.custom: seo-python-october2019, cli-validate, devx-track-python
zone_pivot_groups: python-frameworks-01
robots: noindex
ms.openlocfilehash: 464583f9e20d55fa351a6ae0df31b608b18dc428
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379529"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Início Rápido: Criar um aplicativo Python no Serviço de Aplicativo do Azure no Linux

Neste Início Rápido, você implantará um aplicativo Web Python no [Serviço de Aplicativo no Linux](overview.md#app-service-on-linux), um serviço de hospedagem Web do Azure que é altamente escalonável e conta com aplicação automática de patch. Use a [CLI (interface de linha de comando) do Azure](/cli/azure/install-azure-cli) local em um computador Mac, Linux ou Windows para implantar um exemplo com as estruturas Flask ou Django. O aplicativo Web que você configura usa uma camada de Serviço de Aplicativo gratuita, portanto, os procedimentos neste artigo não resultam em custos para você.

> [!TIP]
> Se você prefere implantar aplicativos por meio de um IDE, confira **[Implantar aplicativos Python no Serviço de Aplicativo usando o Visual Studio Code](/azure/developer/python/tutorial-deploy-app-service-on-linux-01)** .

## <a name="set-up-your-initial-environment"></a>Configurar o seu ambiente inicial

1. Tenha uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
1. Instale o <a href="https://www.python.org/downloads/" target="_blank">Python 3.6 ou mais recente</a>.
1. Instale a <a href="/cli/azure/install-azure-cli" target="_blank">CLI do Azure</a> 2.0.80 ou superior, com a qual você executa comandos em qualquer shell para provisionar e configurar os recursos do Azure.

Abra uma janela de terminal e verifique se sua versão do Python é 3.6 ou superior:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 --version
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

Verifique se a sua versão da CLI do Azure é 2.0.80 ou superior:

```azurecli
az --version
```

Em seguida, entre no Azure por meio da CLI:

```azurecli
az login
```

Esse comando abre um navegador para coletar suas credenciais. Quando o comando for concluído, ele mostrará a saída JSON que contém informações sobre as suas assinaturas.

Depois de conectado, você poderá executar os comandos do Azure com a CLI do Azure para trabalhar com recursos na sua assinatura.

[Está com problemas? Fale conosco.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="clone-the-sample"></a>Clonar o exemplo

Clone o repositório de exemplo usando o comando a seguir e navegue até a pasta do exemplo. ([Instale o Git](https://git-scm.com/downloads) se você ainda não o tiver.)

::: zone pivot="python-framework-flask"
```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Em seguida, acesse esta pasta:

```terminal
cd python-docs-hello-world
```
::: zone-end

::: zone pivot="python-framework-django"
```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-django
```

Em seguida, acesse esta pasta:

```terminal
cd python-docs-hello-django
```
::: zone-end

O exemplo contém o código específico da estrutura que o Serviço de Aplicativo do Azure reconhece ao iniciar o aplicativo. Para obter mais informações, confira [Processo de inicialização do contêiner](configure-language-python.md#container-startup-process).

[Está com problemas? Fale conosco.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="deploy-the-sample"></a>Implantar o exemplo

Implante o código em sua pasta local (*python-docs-hello-world*) usando o comando `az webapp up`:

```azurecli
az webapp up --sku B1 --name <app-name>
```

- Se o comando `az` não for reconhecido, verifique se você tem a CLI do Azure instalada, conforme descrito em [Configurar seu ambiente inicial](#set-up-your-initial-environment).
- Se o comando `webapp` não for reconhecido, sua versão da CLI do Azure será a 2.0.80 ou superior. Caso contrário, [instale a versão mais recente](/cli/azure/install-azure-cli).
- Substitua `<app_name>` por um nome que seja exclusivo em todo o Azure (*os caracteres válidos são `a-z`, `0-9` e `-`* ). Um bom padrão é usar uma combinação do nome da empresa e um identificador de aplicativo.
- O argumento `--sku B1` cria o aplicativo Web no tipo de preço Básico, gerando um pequeno custo por hora. Omita este argumento para usar uma camada Premium mais rápida.
- Opcionalmente, você pode incluir o argumento `--location <location-name>`, em que `<location_name>` é uma região do Azure disponível. Você pode recuperar uma lista de regiões permitidas para sua conta do Azure executando o comando [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations).
- Caso veja o erro "Não foi possível detectar automaticamente a pilha de runtime do seu aplicativo", verifique se você está executando o comando na pasta *python-docs-hello-world* (Flask) ou na pasta *python-docs-hello-django* (Django), que contém o arquivo *requirements.txt*. (Confira [Solução de problemas na detecção automática com az webapp up](https://github.com/Azure/app-service-linux-docs/blob/master/AzWebAppUP/runtime_detection.md) (GitHub).)

O comando pode demorar um pouco para ser concluído. Durante a execução, ele fornece mensagens sobre como criar o grupo de recursos, o plano do Serviço de Aplicativo e o aplicativo de hospedagem, configurar o registro em log e executar a implantação ZIP. Em seguida, ele fornece a mensagem "Você pode iniciar o aplicativo em http://&lt;nome-do-aplicativo&gt;.azurewebsites.net", que é a URL do aplicativo no Azure.

![Exemplo de saída do comando az webapp up](./media/quickstart-python/az-webapp-up-output.png)

[Está com problemas? Fale conosco.](https://aka.ms/FlaskCLIQuickstartHelp)

[!include [az webapp up command note](../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Navegar até o aplicativo

Navegue até o aplicativo implantado no navegador da Web na URL `http://<app-name>.azurewebsites.net`. Demora alguns instantes para iniciar o aplicativo pela primeira vez.

O código de exemplo Python está executando um contêiner Linux no Serviço de Aplicativo usando uma imagem interna.

![Executar um aplicativo Python de exemplo no Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Parabéns!** Você implantou seu aplicativo Python no Serviço de Aplicativo.

[Está com problemas? Fale conosco.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="run-the-sample"></a>Execute o exemplo 

::: zone pivot="python-framework-flask"
1. Verifique se você está na pasta *python-docs-hello-world*. 

1. Crie um ambiente virtual e instale as dependências:

    [!include [virtual environment setup](../../includes/app-service-quickstart-python-venv.md)]

    Se encontrar o erro "[Errno 2] Esse arquivo ou diretório não existe: 'requirements.txt'.", verifique se você está na pasta *python-docs-hello-world*.

1. Execute o Development Server.

    ```terminal  
    flask run
    ```
    
    Por padrão, o servidor presume que o módulo de entrada do aplicativo esteja em *app.py*, conforme usado no exemplo. (Se usar outro nome de módulo, defina a variável de ambiente `FLASK_APP` com esse nome.)

1. Abra um navegador da Web e vá até o aplicativo de exemplo em `http://localhost:5000/`. O aplicativo exibe a mensagem **Olá, Mundo!** .

    ![Executar um aplicativo Python de exemplo localmente](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)
    
1. Na janela do terminal, pressione **Ctrl**+**C** para sair do Development Server.
::: zone-end

::: zone pivot="python-framework-django"
1. Verifique se você está na pasta *python-docs-hello-django*. 

1. Crie um ambiente virtual e instale as dependências:

    [!include [virtual environment setup](../../includes/app-service-quickstart-python-venv.md)]

    Se encontrar o erro "[Errno 2] Esse arquivo ou diretório não existe: 'requirements.txt'.", verifique se você está na pasta *python-docs-hello-django*.
    
1. Execute o Development Server.

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    python3 manage.py runserver
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    py -3 manage.py runserver
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    py -3 manage.py runserver
    ```

    ---    

1. Abra um navegador da Web e vá até o aplicativo de exemplo em `http://localhost:8000/`. O aplicativo exibe a mensagem **Olá, Mundo!** .

    ![Executar um aplicativo Python de exemplo localmente](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)
    
1. Na janela do terminal, pressione **Ctrl**+**C** para sair do Development Server.
::: zone-end

[Está com problemas? Fale conosco.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="redeploy-updates"></a>Reimplantar atualizações

Nesta seção, você faz uma pequena alteração de código e reimplanta o código no Azure. Essa alteração de código adiciona uma instrução `print` para gerar a saída de log com a qual você trabalha na próxima seção.

::: zone pivot="python-framework-flask"
Abra *app.py* em um editor e atualize a função `hello` para que corresponda ao código a seguir. 

```python
def hello():
    print("Handling request to home page.")
    return "Hello, Azure!"
```
::: zone-end
::: zone pivot="python-framework-django"
Abra *hello/views.py* em um editor e atualize a função `hello` de maneira a corresponder ao código a seguir.

```python
def hello(request):
    print("Handling request to home page.")
    return HttpResponse("Hello, Azure!")
```
::: zone-end
    
Salve suas alterações e reimplante o aplicativo usando o comando `az webapp up` novamente:

```azurecli
az webapp up
```

Esse comando usa valores armazenados em cache localmente no arquivo *.azure/config*, incluindo o nome do aplicativo, o grupo de recursos e o plano do Serviço de Aplicativo.

Depois que a implantação for concluída, volte para a janela do navegador aberta em `http://<app-name>.azurewebsites.net`. Atualize a página, que deve exibir a mensagem modificada:

![Executar um aplicativo Python de exemplo atualizado no Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

[Está com problemas? Fale conosco.](https://aka.ms/FlaskCLIQuickstartHelp)

> [!TIP]
> O Visual Studio Code fornece extensões poderosas para o Python e o Serviço de Aplicativo do Azure, que simplificam o processo de implantação de aplicativos Web do Python no Serviço de Aplicativo. Para obter mais informações, confira [Implantar aplicativos Python no Serviço de Aplicativo por meio do Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="stream-logs"></a>Transmitir logs

Você pode acessar os logs do console gerados de dentro do aplicativo e do contêiner no qual ele é executado. Os logs incluem todas as saídas geradas usando instruções `print`.

Para transmitir logs, execute o comando [az webapp log tail](/cli/azure/webapp/log#az_webapp_log_tail):

```azurecli
az webapp log tail
```

Você também pode incluir o parâmetro `--logs` com o comando `az webapp up` para abrir automaticamente o fluxo de log na implantação.

Atualize o aplicativo no navegador para gerar logs de console, que incluem mensagens que descrevem solicitações HTTP para o aplicativo. Se nenhuma saída for exibida imediatamente, tente novamente em 30 segundos.

Você também pode inspecionar os arquivos de log do navegador em `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Para interromper o streaming de log a qualquer momento, pressione **CTRL**+**C** no terminal.

[Está com problemas? Fale conosco.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="manage-the-azure-app"></a>Gerenciar o aplicativo do Azure

Acesse o <a href="https://portal.azure.com" target="_blank">portal do Azure</a> para gerenciar o aplicativo que você criou. Pesquise e selecione **Serviços de Aplicativos**.

![Navegar para os Serviços de Aplicativos no portal do Azure](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Selecione o nome do seu aplicativo do Azure.

![Navegue até o aplicativo Python nos Serviços de Aplicativos no portal do Azure](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

A seleção do aplicativo abre a página **Visão geral** dele, em que você pode executar tarefas básicas de gerenciamento como procurar, parar, iniciar, reiniciar e excluir.

![Gerencie seu aplicativo Python na página Visão geral no portal do Azure](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

O menu de Serviço de Aplicativo fornece páginas diferentes para configurar seu aplicativo.

[Está com problemas? Fale conosco.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="clean-up-resources"></a>Limpar os recursos

Nas etapas anteriores, você criou os recursos do Azure em um grupo de recursos. O grupo de recursos tem um nome como "appsvc_rg_Linux_CentralUS", dependendo da sua localização. Se você mantiver o aplicativo Web em execução, incorrerá em alguns custos contínuos (confira [Preços do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/linux/)).

Se você achar que não precisará desses recursos no futuro, exclua o grupo de recursos executando o seguinte comando:

```azurecli
az group delete --no-wait
```

O comando usa o nome do grupo de recursos armazenado em cache no arquivo *.azure/config*.

O argumento `--no-wait` permite que o comando seja retornado antes que a operação seja concluída.

[Está com problemas? Fale conosco.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Aplicativo Web Python (Django) com PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Configurar o aplicativo Python](configure-language-python.md)

> [!div class="nextstepaction"]
> [Adicionar entrada do usuário a um aplicativo Web Python](../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Tutorial: Executar o aplicativo Python em um contêiner personalizado](tutorial-custom-container.md)
