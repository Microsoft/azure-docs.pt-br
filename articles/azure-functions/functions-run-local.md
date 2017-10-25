---
title: "Desenvolver e executar funções do Azure localmente | Microsoft Docs"
description: "Saiba como codificar e testar o Azure Functions no computador local antes de executá-las no Azure Functions."
services: functions
documentationcenter: na
author: lindydonna
manager: cfowler
editor: 
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 09/25/2017
ms.author: glenga
ms.openlocfilehash: b6ab081311822abd9c0a24b4cc241291bf56af68
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2017
---
# <a name="code-and-test-azure-functions-locally"></a>Codificar e testar o Azure Functions localmente

Enquanto o [Portal do Azure] fornece um conjunto completo de ferramentas para desenvolvimento e teste do Azure Functions, muitos desenvolvedores preferem uma experiência de desenvolvimento local. O Azure Functions facilita a utilização do seu editor de códigos favorito e das ferramentas de desenvolvimento locais para desenvolver e testar as funções em seu computador local. As funções podem disparar eventos no Azure e você pode depurar o as funções de C# e JavaScript em seu computador local. 

Se você for um desenvolvedor de C# no Visual Studio, o Azure Functions também [se integrará ao Visual Studio de 2017](functions-develop-vs.md).

## <a name="install-the-azure-functions-core-tools"></a>Instalação das ferramentas básicas do Azure Functions

As [Ferramentas básicas do Azure Functions] são uma versão local do tempo de execução do Azure Functions que pode ser executada no computador local de desenvolvimento. Não é um simulador ou emulador. É o mesmo tempo de execução que potencializa as funções no Azure. Há duas versões das ferramentas básicas do Azure Functions, uma para a versão 1.x do tempo de execução e outra para a versão 2.x. Ambas as versões são fornecidas como [pacote npm](https://docs.npmjs.com/getting-started/what-is-npm).

>[!NOTE]  
> Antes de instalar uma das versões, você deve [instalar o NodeJS](https://docs.npmjs.com/getting-started/installing-node), que inclui npm. Para a versão 2.x das ferramentas, somente Node.js 8.5 e versões posteriores têm suporte. 

### <a name="version-1x-runtime"></a>Tempo de execução versão 1.x

A versão original das ferramentas usa o tempo de execução 1.x das funções. Essa versão usa o .NET Framework e só tem suporte em computadores Windows. Use o seguinte comando para instalar as ferramentas versão 1.x:

```bash
npm install -g azure-functions-core-tools
```

### <a name="version-2x-runtime"></a>Tempo de execução versão 2.x

A versão 2.x das ferramentas usa o tempo de execução 2.x do Azure Functions que se baseia em .NET Core. Essa versão tem suporte em todas as plataformas que o .NET Core 2.x dá suporte. Use essa versão para o desenvolvimento de plataforma cruzada e quando o tempo de execução 2.x das funções for necessário. 

>[!IMPORTANT]   
> Antes de instalar as ferramentas básicas do Azure Functions, [instale o .NET Core 2.0](https://www.microsoft.com/net/core).  
>
> O tempo de execução 2.0 do Azure Functions está em versão prévia e, no momento, nem todos os recursos do Azure Functions têm suporte. Para saber mais, confira [problemas conhecidos do tempo de execução 2.0 do Azure Functions](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues) 

 Use o seguinte comando para instalar as ferramentas versão 2.0:

```bash
npm install -g azure-functions-core-tools@core
```

Ao instalar no Ubuntu, use `sudo`, da seguinte maneira:

```bash
sudo npm install -g azure-functions-core-tools@core
```

Ao instalar no macOS e no Linux, talvez seja necessário incluir o sinalizador `unsafe-perm`, da seguinte maneira:

```bash
sudo npm install -g azure-functions-core-tools@core --unsafe-perm true
```

## <a name="run-azure-functions-core-tools"></a>Executar as ferramentas básicas do Azure Functions
 
As ferramentas básicas do Azure Functions adicionam os aliases de comando a seguir:
* **func**
* **azfun**
* **azurefunctions**

Qualquer um desses alias pode ser usado onde `func` é mostrado nos exemplos.

```
func init MyFunctionProj
```

## <a name="create-a-local-functions-project"></a>Criar um projeto de funções local

Quando estiver em execução localmente, um projeto de funções é um diretório que tem os arquivos [host.json](functions-host-json.md) e [local.settings.json](#local-settings). Esse diretório é o equivalente ao de um aplicativo de funções no Azure. Para saber mais sobre a estrutura de pastas do Azure Functions, consulte o [guia de desenvolvedores do Azure Functions](functions-reference.md#folder-structure).

Na janela do terminal ou em um prompt de comando, execute o seguinte comando para criar o projeto e o repositório Git local:

```
func init MyFunctionProj
```

A saída se parece com o seguinte exemplo:

```
Writing .gitignore
Writing host.json
Writing local.settings.json
Created launch.json
Initialized empty Git repository in D:/Code/Playground/MyFunctionProj/.git/
```

Para criar o projeto sem um repositório Git local, use a opção `--no-source-control [-n]`.

<a name="local-settings"></a>

## <a name="local-settings-file"></a>Arquivo de configurações local

O arquivo local.settings.json armazena as configurações do aplicativo, as cadeias de conexão e as configurações para as Ferramentas básicas do Azure Functions. Ele contém a seguinte estrutura:

```json
{
  "IsEncrypted": false,   
  "Values": {
    "AzureWebJobsStorage": "<connection string>", 
    "AzureWebJobsDashboard": "<connection string>" 
  },
  "Host": {
    "LocalHttpPort": 7071, 
    "CORS": "*" 
  },
  "ConnectionStrings": {
    "SQLConnectionString": "Value"
  }
}
```
| Configuração      | Descrição                            |
| ------------ | -------------------------------------- |
| **IsEncrypted** | Quando definidos como **true**, todos os valores são criptografados usando uma chave de computador local. Usado com `func settings` comandos. O valor padrão é **false**. |
| **Valores** | Coleção de configuração de aplicativo usada quando executada localmente. **AzureWebJobsStorage** e **AzureWebJobsDashboard** são exemplos; para obter uma lista completa, consulte [referência de configurações de aplicativo](functions-app-settings.md).  |
| **Host** | As configurações nesta seção personalizam o processo de host do Functions quando executadas localmente. | 
| **LocalHttpPort** | Define a porta padrão usada ao executar o host local do Functions (`func host start` e `func run`). A opção de linha de comando `--port` tem precedência sobre esse valor. |
| **CORS** | Define as origens permitidas para [CORS (Compartilhamento de recurso entre origens)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). As origens são fornecidas como uma lista separada por vírgulas, sem espaços. Há suporte para o valor do caractere curinga (**\***), que permite solicitações de qualquer origem. |
| **ConnectionStrings** | Contém as cadeias de caracteres de conexão de banco de dados para suas funções. As cadeias de caracteres de conexão neste objeto são adicionadas ao ambiente com o tipo de provedor de **System.Data.SqlClient**.  | 

A maioria dos gatilhos e associações têm uma propriedade **Conexão** que mapeia para o nome de uma variável de ambiente ou configuração de aplicativo. Para cada propriedade de conexão, deve haver uma configuração de aplicativo definida no arquivo local.settings.json. 

Essas configurações também podem ser lidas em seu código como variáveis de ambiente. No C#, use [System.Environment.GetEnvironmentVariable](https://msdn.microsoft.com/library/system.environment.getenvironmentvariable(v=vs.110).aspx) ou [ConfigurationManager.AppSettings](https://msdn.microsoft.com/library/system.configuration.configurationmanager.appsettings%28v=vs.110%29.aspx). No JavaScript, use `process.env`. As configurações especificadas como uma variável de ambiente do sistema prevalecem sobre os valores no arquivo local.settings.json. 

As configurações no arquivo local.settings.json só são usadas pelas ferramentas do Functions quando são executadas localmente. Por padrão, essas configurações não são migradas automaticamente quando o projeto é publicado no Azure. Use a opção `--publish-local-settings` [quando publicar](#publish) para se certificar de que essas configurações serão adicionadas ao aplicativo de funções no Azure.

Se nenhuma cadeia de conexão de armazenamento válida for definida como **AzureWebJobsStorage**, a seguinte mensagem de erro será exibida:  

>Valor ausente para AzureWebJobsStorage em local.settings.json. Isso é necessário para todos os gatilhos diferentes de HTTP. Você pode executar 'func azure functionary fetch-app-settings <functionAppName>' ou especificar uma cadeia de conexão em local.settings.json.
  
[!INCLUDE [Note to not use local storage](../../includes/functions-local-settings-note.md)]

### <a name="configure-app-settings"></a>Definir configurações de aplicativo

Para definir um valor para cadeias de conexão, você pode realizar uma das seguintes opções:
* Insira a cadeia de conexão do [Gerenciador de Armazenamento do Azure](http://storageexplorer.com/).
* Use um dos seguintes comandos:

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
    ```
    func azure storage fetch-connection-string <StorageAccountName>
    ```
    Os dois comandos exigem que você entre no Azure primeiro.

<a name="create-func"></a>
## <a name="create-a-function"></a>Criar uma função

Para criar uma função, execute o seguinte comando:

```
func new
``` 
`func new` dá suporte para os seguintes argumentos opcionais:

| Argumento     | Descrição                            |
| ------------ | -------------------------------------- |
| **`--language -l`** | A linguagem de programação modelo, como C#, F# ou JavaScript. |
| **`--template -t`** | O nome do modelo. |
| **`--name -n`** | O nome da função. |

Por exemplo, para criar um gatilho de HTTP de JavaScript, execute:

```
func new --language JavaScript --template HttpTrigger --name MyHttpTrigger
```

Para criar uma função ativada por fila, execute:

```
func new --language JavaScript --template QueueTrigger --name QueueTriggerJS
```
<a name="start"></a>
## <a name="run-functions-locally"></a>Executar funções localmente

Para executar um projeto de funções, execute o host de funções. O host permite os gatilhos para todas as funções no projeto:

```
func host start
```

`func host start` dá suporte para as seguintes opções:

| Opção     | Descrição                            |
| ------------ | -------------------------------------- |
|**`--port -p`** | A porta local na qual escutar. Valor Padrão: 7071. |
| **`--debug <type>`** | As opções são `VSCode` e `VS`. |
| **`--cors`** | Uma lista separada por vírgulas de origens CORS, sem espaços. |
| **`--nodeDebugPort -n`** | A porta para usar o depurador de nós. Padrão: um valor de launch.json ou 5858. |
| **`--debugLevel -d`** | O nível de rastreamento do console (desativado, detalhado, informações, aviso ou erro). Padrão: informações.|
| **`--timeout -t`** | O tempo limite para o host de funções ser iniciado, em segundos. Padrão: 20 segundos.|
| **`--useHttps`** | Associar a https://localhost:{port} em vez de a http://localhost:{port}. Por padrão, essa opção cria um certificado confiável no computador.|
| **`--pause-on-error`** | Pausar para entrada adicional antes de encerrar o processo. Útil ao iniciar as ferramentas básicas do Azure Functions de um ambiente de desenvolvimento integrado (IDE).|

Quando o host de funções é iniciado, ele gera as funções acionadas por URL de HTTP:

```
Found the following functions:
Host.Functions.MyHttpTrigger

ob host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

### <a name="debug-in-vs-code-or-visual-studio"></a>Depurar no VSCode ou no Visual Studio

Para anexar um depurador, passe o argumento `--debug`. Para depurar funções de JavaScript, use o Visual Studio Code. Para funções C#, use o Visual Studio.

Para depurar funções C#, use `--debug vs`. Você também pode usar as [Ferramentas do Visual Studio 2017 no Azure Functions](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/). 

Para iniciar o host e configurar a depuração de JavaScript, execute:

```
func host start --debug vscode
```

Em seguida, no Visual Studio Code, na exibição **Depurar**, selecione **Anexar ao Azure Functions**. Você pode anexar os pontos de interrupção, inspecionar variáveis e o código por etapas.

![Depuração de JavaScript com o Visual Studio Code](./media/functions-run-local/vscode-javascript-debugging.png)

### <a name="passing-test-data-to-a-function"></a>Transferência dos dados de teste para uma função

Para testar as funções localmente, [inicie o host do Functions](#start) e chame os pontos de extremidade no servidor local usando solicitações HTTP. O ponto de extremidade que você chama depende do tipo de função. 

>[!NOTE]  
> Os exemplos neste tópico usam a ferramenta cURL para enviar solicitações HTTP do terminal ou de um prompt de comando. Você pode usar uma ferramenta de sua escolha para enviar solicitações HTTP para o servidor local. Observe que a ferramenta cURL está disponível por padrão nos sistemas baseados em Linux. No Windows, você deve primeiro baixar e instalar o [cURL ferramenta](https://curl.haxx.se/).

Para ter informações mais gerais sobre o teste de funções, confira [Estratégias para testar seu código no Azure Functions](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>Funções disparadas por HTTP e webhook

Chame o seguinte ponto de extremidade para executar localmente funções disparadas por HTTP e webhook:

    http://localhost:{port}/api/{function_name}

Use o mesmo nome de servidor e porta no qual o host do Functions está escutando. Veja isso na saída gerada ao iniciar o host do Function. Chame essa URL usando qualquer método HTTP com suporte do disparador. 

O seguinte comando cURL dispara a função de início rápido `MyHttpTrigger` de uma solicitação GET com o parâmetro _name_ transmitido na cadeia de consulta. 

```
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```
O exemplo a seguir é a mesma função chamada a partir de uma solicitação POST passando _name_ no corpo da solicitação:

```
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```

Observe que você pode fazer solicitações GET de um navegador passando dados na cadeia de consulta. Para todos os outros métodos HTTP, você deve usar cURL, Fiddler, Postman ou uma ferramenta de teste HTTP semelhante.  

#### <a name="non-http-triggered-functions"></a>Funções disparadas por algo diferente de HTTP
Para todos os tipos de funções que não sejam gatilhos HTTP e webhooks, você pode testar suas funções localmente chamando um ponto de extremidade de administração. Chamar esse ponto de extremidade no servidor local dispara a função. Opcionalmente, você pode passar dados de teste para a execução. Essa funcionalidade é semelhante á guia **Teste** no Portal do Azure.  

Chame o seguinte ponto de extremidade de administrador para disparar a funções que não são HTTP com uma solicitação HTTP POST:

    http://localhost:{port}/admin/functions/{function_name}

Embora você passe dados de teste para o ponto de extremidade administrador de uma função, é necessário fornecer os dados no corpo de uma mensagem de solicitação POST. O corpo da mensagem deve ter o seguinte formato JSON:

```JSON
{
    "input": "<trigger_input>"
}
```` 
O valor `<trigger_input>` contém dados em um formato esperado pela função. O exemplo de cURL a seguir é um POST para uma função `QueueTriggerJS`. Nesse caso, a entrada é uma cadeia de caracteres equivalente à mensagem esperada na fila.      

```
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTriggerJS
```

#### <a name="using-the-func-run-command-in-version-1x"></a>Usar o comando `func run` na versão 1.x

>[!IMPORTANT]  
> O comando `func run` não tem suporte na versão 2.x das ferramentas. Para saber mais, consulte o tópico [Como direcionar para versões de tempo de execução do Azure Functions](functions-versions.md).

Você também pode invocar uma função diretamente usando `func run <FunctionName>` e fornecer dados de entrada para a função. Esse comando é semelhante à execução de uma função usando a guia **Testar** no Portal do Azure. 

`func run` dá suporte para as seguintes opções:

| Opção     | Descrição                            |
| ------------ | -------------------------------------- |
| **`--content -c`** | Conteúdo embutido. |
| **`--debug -d`** | Anexe um depurador ao processo de host antes de executar a função.|
| **`--timeout -t`** | Tempo de espera (em segundos) até que o host local de funções esteja pronto.|
| **`--file -f`** | O nome do arquivo a ser usado como conteúdo.|
| **`--no-interactive`** | Não solicitará a entrada. Útil para cenários de automação.|

Por exemplo, para chamar uma função ativada por HTTP e passar o corpo do conteúdo, execute o seguinte comando:

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish"></a>Publicar no Azure

Para publicar um projeto de funções em um aplicativo de funções no Azure, use o comando `publish`:

```
func azure functionapp publish <FunctionAppName>
```

É possível usar as seguintes opções:

| Opção     | Descrição                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Configurações de publicação em local.settings.json do Azure, a solicitação para substituir se a configuração já existe.|
| **`--overwrite-settings -y`** | Deve ser usada com `-i`. Substitui o AppSettings no Azure com o valor local se for diferente. O padrão é solicitado.|

Esse comando publica a um aplicativo de funções existente no Azure. Um erro ocorre quando `<FunctionAppName>` não existe em sua assinatura. Para saber como criar um aplicativo de funções pelo prompt de comando ou pela janela do terminal usando a CLI do Azure, consulte [Criar um aplicativo de funções para execução sem servidor](./scripts/functions-cli-create-serverless.md).

O comando `publish` faz upload o conteúdo do diretório do projeto de funções. Se você excluir arquivos localmente, o comando `publish` não os excluirá do Azure. Você pode excluir arquivos no Azure usando a [ferramenta Kudu](functions-how-to-use-azure-function-app-settings.md#kudu) no [Portal do Azure].  

>[!IMPORTANT]  
> Quando você cria um aplicativo de funções no Azure, ele usa a versão 1.x do tempo de execução de função por padrão. Para fazer com que o aplicativo de funções use a versão 2.x do tempo de execução, adicione a configuração de aplicativo `FUNCTIONS_EXTENSION_VERSION=beta`.  
Use o seguinte código de CLI do Azure para adicionar essa configuração a seu aplicativo de funções: 
```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings FUNCTIONS_EXTENSION_VERSION=beta   
```

## <a name="next-steps"></a>Próximas etapas

As principais ferramentas do Azure Functions são [Código-fonte aberto e hospedado no GitHub](https://github.com/azure/azure-functions-cli).  
Para arquivar uma solicitação de bug ou recurso, [abra um problema do GitHub](https://github.com/azure/azure-functions-cli/issues). 

<!-- LINKS -->

[Ferramentas básicas do Azure Functions]: https://www.npmjs.com/package/azure-functions-core-tools
[Portal do Azure]: https://portal.azure.com 
