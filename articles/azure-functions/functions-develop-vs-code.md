---
title: Desenvolver o Azure Functions usando o Visual Studio Code
description: Saiba como desenvolver e testar Azure Functions usando a extensão Azure Functions para Visual Studio Code.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/21/2019
ms.openlocfilehash: d4353e6be313d61716933879efa930e22472781b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99493932"
---
# <a name="develop-azure-functions-by-using-visual-studio-code"></a>Desenvolver o Azure Functions usando o Visual Studio Code

A [extensão Azure Functions para Visual Studio Code] permite que você desenvolva localmente funções e as implante no Azure. Se esta for sua primeira experiência com o Azure Functions, você pode aprender mais em [Uma introdução ao Azure Functions](functions-overview.md).

A extensão Azure Functions fornece estes benefícios:

* Editar, criar e executar funções em seu computador de desenvolvimento local.
* Publicar seu projeto do Azure Functions diretamente no Azure.
* Escreva suas funções em várias linguagens, aproveitando os benefícios de Visual Studio Code.

A extensão pode ser usada com os seguintes idiomas, que têm suporte do tempo de execução Azure Functions a partir da versão 2. x:

* [Compilado em C#](functions-dotnet-class-library.md)
* [Script C#](functions-reference-csharp.md)<sup>*</sup>
* [JavaScript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [PowerShell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>Requer que você [defina o script C# como seu idioma de projeto padrão](#c-script-projects).

Neste artigo, os exemplos estão disponíveis atualmente apenas para funções de biblioteca de classes JavaScript (Node.js) e C#.  

Este artigo fornece detalhes sobre como usar a extensão Azure Functions para desenvolver funções e publicá-las no Azure. Antes de ler este artigo, você deve [criar sua primeira função usando Visual Studio Code](./create-first-function-vs-code-csharp.md).

> [!IMPORTANT]
> Não combine desenvolvimento local e desenvolvimento de portal para um único aplicativo de funções. Ao publicar a partir de um projeto local para um aplicativo de funções, o processo de implantação substitui quaisquer funções que você desenvolveu o portal.

## <a name="prerequisites"></a>Pré-requisitos

Antes de instalar e executar a extensão de Azure Functions de [extensão de Azure Functions][para Visual Studio Code], você deve atender a esses requisitos:

* [Visual Studio Code](https://code.visualstudio.com/) instalado em uma das [plataformas com suporte](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Uma assinatura ativa do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Outros recursos de que você precisa, como uma conta de armazenamento do Azure, são criados em sua assinatura quando você [publica usando Visual Studio Code](#publish-to-azure). 

### <a name="run-local-requirements"></a>Executar requisitos locais

Esses pré-requisitos só são necessários para [executar e depurar suas funções localmente](#run-functions-locally). Eles não são necessários para criar ou publicar projetos para Azure Functions.

# <a name="c"></a>[C\#](#tab/csharp)

+ O [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) versão 2. x ou posterior. O pacote de ferramentas principais é baixado e instalado automaticamente quando você inicia o projeto localmente. As ferramentas principais incluem todo o tempo de execução de Azure Functions, portanto, o download e a instalação podem levar algum tempo.

+ A [Extensão do C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) para Visual Studio Code. 

+ [Ferramentas de CLI do .NET Core](/dotnet/core/tools/?tabs=netcore2x).  

# <a name="java"></a>[Java](#tab/java)

+ O [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) versão 2. x ou posterior. O pacote de ferramentas principais é baixado e instalado automaticamente quando você inicia o projeto localmente. As ferramentas principais incluem todo o tempo de execução de Azure Functions, portanto, o download e a instalação podem levar algum tempo.

+ [Depurador para extensão Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug).

+ [Java 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) recomendado. Para outras versões com suporte, consulte [versões do Java](functions-reference-java.md#java-versions).

+ [Maven 3 ou posterior](https://maven.apache.org/)

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

+ O [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) versão 2. x ou posterior. O pacote de ferramentas principais é baixado e instalado automaticamente quando você inicia o projeto localmente. As ferramentas principais incluem todo o tempo de execução de Azure Functions, portanto, o download e a instalação podem levar algum tempo.

+ [Node.js](https://nodejs.org/), versões Active LTS e versões LTS de manutenção (10.14.1 recomendada). Use o comando `node --version` para verificar sua versão. 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

+ O [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) versão 2. x ou posterior. O pacote de ferramentas principais é baixado e instalado automaticamente quando você inicia o projeto localmente. As ferramentas principais incluem todo o tempo de execução de Azure Functions, portanto, o download e a instalação podem levar algum tempo.

+ [PowerShell 7](/powershell/scripting/install/installing-powershell-core-on-windows) recomendado. Para obter informações sobre a versão, consulte [versões do PowerShell](functions-reference-powershell.md#powershell-versions).

+ O [runtime do .NET Core 3.1](https://www.microsoft.com/net/download) e o [runtime do .NET Core 2.1](https://dotnet.microsoft.com/download/dotnet-core/2.1)  

+ A [Extensão do PowerShell para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).  

# <a name="python"></a>[Python](#tab/python)

+ O [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) versão 2. x ou posterior. O pacote de ferramentas principais é baixado e instalado automaticamente quando você inicia o projeto localmente. As ferramentas principais incluem todo o tempo de execução de Azure Functions, portanto, o download e a instalação podem levar algum tempo.

+ [Python 3. x](https://www.python.org/downloads/). Para obter informações sobre a versão, consulte [versões do Python](functions-reference-python.md#python-version) pelo Azure Functions Runtime.

+ [Extensão do Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) para Visual Studio Code.

---

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Criar um projeto do Azure Functions

A extensão Functions permite criar um projeto de aplicativo de funções, juntamente com sua primeira função. As etapas a seguir mostram como criar uma função disparada por HTTP em um novo projeto do functions. O [gatilho http](functions-bindings-http-webhook.md) é o modelo de gatilho de função mais simples para demonstrar.

1. No **Azure: Functions**, selecione o ícone **criar função** :

    ![Criar uma função](./media/functions-develop-vs-code/create-function.png)

1. Selecione a pasta para seu projeto de aplicativo de funções e, em seguida, **Selecione um idioma para seu projeto de função**.

1. Selecione o modelo de função de **gatilho http** ou selecione **ignorar agora** para criar um projeto sem uma função. Você sempre pode [Adicionar uma função ao seu projeto](#add-a-function-to-your-project) posteriormente.

    ![Escolher o modelo de gatilho HTTP](./media/functions-develop-vs-code/create-function-choose-template.png)

1. Digite **HttpExample** para o nome da função e selecione Enter e, em seguida, selecione autorização de **função** . Esse nível de autorização exige que você forneça uma [chave de função](functions-bindings-http-webhook-trigger.md#authorization-keys) ao chamar o ponto de extremidade da função.

    ![Selecionar autorização de função](./media/functions-develop-vs-code/create-function-auth.png)

    Uma função é criada no idioma escolhido e no modelo para uma função disparada por HTTP.

    ![Modelo de função disparada por HTTP no Visual Studio Code](./media/functions-develop-vs-code/new-function-full.png)

### <a name="generated-project-files"></a>Arquivos de projeto gerados

O modelo de projeto cria um projeto no idioma escolhido e instala as dependências necessárias. Para qualquer idioma, o novo projeto tem estes arquivos:

* **host.json**: Permite que você configure o host do Functions. Essas configurações se aplicam quando você estiver executando o Functions localmente e quando estiver executando-os no Azure. Para obter mais informações, consulte a [referência para host.json](functions-host-json.md).

* **local.settings.jsem**: mantém as configurações usadas quando você está executando funções localmente. Essas configurações são usadas somente quando você está executando funções localmente. Para obter mais informações, consulte [Local Settings File](#local-settings-file).

    >[!IMPORTANT]
    >Como o local.settings.jsno arquivo pode conter segredos, você precisa excluí-lo do seu controle do código-fonte do projeto.

Dependendo do seu idioma, esses outros arquivos serão criados:

# <a name="c"></a>[C\#](#tab/csharp)

* [Arquivo de biblioteca de classes HttpExample. cs](functions-dotnet-class-library.md#functions-class-library-project) que implementa a função.

# <a name="java"></a>[Java](#tab/java)

+ Um arquivo de pom.xml na pasta raiz que define os parâmetros de implantação e projeto, incluindo dependências do projeto e a [versão do Java](functions-reference-java.md#java-versions). O pom.xml também contém informações sobre os recursos do Azure que são criados durante uma implantação.   

+ Um [arquivo functions. java](functions-reference-java.md#triggers-and-annotations) em seu caminho src que implementa a função.

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

* Um package.jsno arquivo na pasta raiz.

* Uma pasta HttpExample que contém a [function.jsno arquivo de definição](functions-reference-node.md#folder-structure) e o [ arquivo deindex.js](functions-reference-node.md#exporting-a-function), um arquivo de Node.js que contém o código da função.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

* Uma pasta HttpExample que contém a [function.jsno arquivo de definição](functions-reference-powershell.md#folder-structure) e o arquivo de run.ps1, que contém o código de função.
 
# <a name="python"></a>[Python](#tab/python)
    
* Um arquivo de requirements.txt de nível de projeto que lista os pacotes exigidos pelas funções.
    
* Uma pasta HttpExample que contém a [function.jsno arquivo de definição](functions-reference-python.md#folder-structure) e o \_ \_ \_ \_ arquivo init. py, que contém o código de função.

---

Neste ponto, você pode [Adicionar associações de entrada e saída](#add-input-and-output-bindings) à sua função. Você também pode [Adicionar uma nova função ao seu projeto](#add-a-function-to-your-project).

## <a name="install-binding-extensions"></a>Instalar extensões de associação

Exceto para gatilhos HTTP e de temporizador, as associações são implementadas em pacotes de extensão. Você deve instalar os pacotes de extensão para os gatilhos e as associações que precisam deles. O processo de instalação de extensões de associação depende do idioma do seu projeto.

# <a name="c"></a>[C\#](#tab/csharp)

Execute o comando [dotnet adicionar pacote](/dotnet/core/tools/dotnet-add-package) na janela do terminal para instalar os pacotes de extensão de que você precisa em seu projeto. O comando a seguir instala a extensão de armazenamento do Azure, que implementa associações para armazenamento de BLOBs, filas e tabelas.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

# <a name="java"></a>[Java](#tab/java)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="powershell"></a>[PowerShell](#tab/powershell)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="python"></a>[Python](#tab/python)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

---

## <a name="add-a-function-to-your-project"></a>Adicionar uma função ao projeto

Você pode adicionar uma nova função a um projeto existente usando um dos modelos de gatilho de funções predefinidos. Para adicionar um novo gatilho de função, selecione F1 para abrir a paleta de comandos e, em seguida, procure e execute o comando **Azure Functions: criar função**. Siga os prompts para escolher o tipo de disparador e definir os atributos necessários do gatilho. Se o gatilho exigir uma chave de acesso ou uma cadeia de conexão para se conectar a um serviço, prepare-o antes de criar o gatilho de função.

Os resultados dessa ação dependem do idioma do seu projeto:

# <a name="c"></a>[C\#](#tab/csharp)

Um novo arquivo de biblioteca de classes C# (. cs) é adicionado ao seu projeto.

# <a name="java"></a>[Java](#tab/java)

Um novo arquivo Java (. java) é adicionado ao seu projeto.

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

Uma nova pasta é criada no projeto. A pasta contém um novo function.jsno arquivo e o novo arquivo de código JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Uma nova pasta é criada no projeto. A pasta contém um novo function.jsno arquivo e o novo arquivo de código do PowerShell.

# <a name="python"></a>[Python](#tab/python)

Uma nova pasta é criada no projeto. A pasta contém um novo function.jsno arquivo e o novo arquivo de código Python.

---

## <a name="connect-to-services"></a><a name="add-input-and-output-bindings"></a>Conectar-se a serviços

Você pode conectar sua função a outros serviços do Azure adicionando associações de entrada e saída. As associações conectam sua função a outros serviços sem a necessidade de escrever o código de conexão. O processo para adicionar associações depende do idioma do seu projeto. Para saber mais sobre associações, confira [Conceitos de gatilhos e de associações do Azure Functions](functions-triggers-bindings.md).

Os exemplos a seguir se conectam a uma fila de armazenamento chamada `outqueue` , em que a cadeia de conexão para a conta de armazenamento é definida na `MyStorageConnection` configuração de aplicativo no local.settings.jsem.

# <a name="c"></a>[C\#](#tab/csharp)

Atualize o método de função para adicionar o seguinte parâmetro à `Run` definição do método:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

O parâmetro `msg` é um tipo `ICollector<T>`, que representa uma coleção de mensagens que são gravadas em uma associação de saída quando a função é concluída. O código a seguir adiciona uma mensagem à coleção:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="30-31":::

 As mensagens são enviadas para a fila quando a função é concluída.

Para saber mais, confira a documentação do [artigo referência de associação de saída de armazenamento de fila](functions-bindings-storage-queue-output.md?tabs=csharp) . Para saber mais em geral sobre quais associações podem ser adicionadas a uma função, consulte [Adicionar associações a uma função existente no Azure Functions](add-bindings-existing-function.md?tabs=csharp). 

# <a name="java"></a>[Java](#tab/java)

Atualize o método de função para adicionar o seguinte parâmetro à `Run` definição do método:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="20-21":::

O `msg` parâmetro é um `OutputBinding<T>` tipo, em que é `T` uma cadeia de caracteres que é gravada em uma associação de saída quando a função é concluída. O código a seguir define a mensagem na associação de saída:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="33-34":::

Essa mensagem é enviada para a fila quando a função é concluída.

Para saber mais, confira a documentação do [artigo referência de associação de saída de armazenamento de fila](functions-bindings-storage-queue-output.md?tabs=java) . Para saber mais em geral sobre quais associações podem ser adicionadas a uma função, consulte [Adicionar associações a uma função existente no Azure Functions](add-bindings-existing-function.md?tabs=java). 

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

No seu código de função, a `msg` associação é acessada do `context` , como neste exemplo:

:::code language="javascript" range="5-7" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/index.js":::

Essa mensagem é enviada para a fila quando a função é concluída.

Para saber mais, confira a documentação do [artigo referência de associação de saída de armazenamento de fila](functions-bindings-storage-queue-output.md?tabs=javascript) . Para saber mais em geral sobre quais associações podem ser adicionadas a uma função, consulte [Adicionar associações a uma função existente no Azure Functions](add-bindings-existing-function.md?tabs=javascript). 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

:::code language="powershell" range="18-19" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/run.ps1":::

Essa mensagem é enviada para a fila quando a função é concluída.

Para saber mais, confira a documentação do [artigo referência de associação de saída de armazenamento de fila](functions-bindings-storage-queue-output.md?tabs=powershell) . Para saber mais em geral sobre quais associações podem ser adicionadas a uma função, consulte [Adicionar associações a uma função existente no Azure Functions](add-bindings-existing-function.md?tabs=powershell). 

# <a name="python"></a>[Python](#tab/python)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

Atualize a `Main` definição para adicionar um parâmetro de saída `msg: func.Out[func.QueueMessage]` para que a definição seja parecida com o exemplo a seguir:

:::code language="python" range="6" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

O código a seguir adiciona dados de cadeia de caracteres da solicitação à fila de saída:

:::code language="python" range="18" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

Essa mensagem é enviada para a fila quando a função é concluída.

Para saber mais, confira a documentação do [artigo referência de associação de saída de armazenamento de fila](functions-bindings-storage-queue-output.md?tabs=python) . Para saber mais em geral sobre quais associações podem ser adicionadas a uma função, consulte [Adicionar associações a uma função existente no Azure Functions](add-bindings-existing-function.md?tabs=python). 

---

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-to-azure"></a>Publicar no Azure

Visual Studio Code permite publicar seu projeto do Functions diretamente no Azure. No processo, você criará um aplicativo de funções e recursos relacionados em sua assinatura do Azure. O aplicativo de funções fornece um contexto de execução para suas funções. O projeto é empacotado e implantado para o novo aplicativo de função em sua assinatura do Azure.

Ao publicar de Visual Studio Code em um novo aplicativo de funções no Azure, você pode escolher um aplicativo de função rápida criar um caminho usando os padrões ou um caminho avançado, no qual você tem mais controle sobre os recursos remotos criados. 

Ao publicar do Visual Studio Code, você tira proveito da tecnologia de [implantação de zip](functions-deployment-technologies.md#zip-deploy) . 

### <a name="quick-function-app-create"></a>Criação rápida de aplicativos de funções

Quando você escolhe **+ criar novo aplicativo de funções no Azure...**, a extensão gera automaticamente valores para os recursos do Azure necessários para seu aplicativo de funções. Esses valores se baseiam no nome do aplicativo de funções que você escolher. Para obter um exemplo de como usar os padrões para publicar seu projeto em um novo aplicativo de funções no Azure, consulte o [artigo Visual Studio Code início rápido](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure).

Se você quiser fornecer nomes explícitos para os recursos criados, deverá escolher o caminho avançado de criação.

### <a name="publish-a-project-to-a-new-function-app-in-azure-by-using-advanced-options"></a><a name="enable-publishing-with-advanced-create-options"></a>Publicar um projeto em um novo aplicativo de funções no Azure usando opções avançadas

As etapas a seguir publicam seu projeto em um novo aplicativo de funções criado com opções de criação avançadas:

1. No palete de comando, insira **Azure Functions: implantar no aplicativo de funções**.

1. Se você não estiver conectado, será solicitado a **Entrar no Azure**. Você também pode **criar uma conta gratuita do Azure**. Depois de entrar no navegador, volte para Visual Studio Code.

1. Se você tiver várias assinaturas, **Selecione uma assinatura** para o aplicativo de funções e, em seguida, selecione **+ criar novo aplicativo de funções no Azure... _Avançado_**. Essa opção _avançada_ oferece mais controle sobre os recursos que você cria no Azure. 

1. Seguindo os prompts, forneça estas informações:

    | Prompt | Valor | Descrição |
    | ------ | ----- | ----------- |
    | Selecionar aplicativo de funções no Azure | Criar novos Aplicativo de funções no Azure | No prompt seguinte, digite um nome globalmente exclusivo que identifique o novo aplicativo de funções e selecione Enter. Caracteres válidos para um nome de aplicativo de funções são `a-z`, `0-9` e `-`. |
    | Selecionar um sistema operacional | Windows | O aplicativo de funções é executado no Windows. |
    | Selecionar um plano de hospedagem | Plano de consumo | Uma hospedagem de [plano de consumo](consumption-plan.md) sem servidor é usada. |
    | Selecione um tempo de execução para seu novo aplicativo | A linguagem do projeto | O tempo de execução deve corresponder ao projeto que você está publicando. |
    | Selecionar um grupo de recursos para novos recursos | Criar novo grupo de recursos | No prompt seguinte, digite um nome de grupo de recursos, como `myResourceGroup` e, em seguida, selecione Enter. Você também pode selecionar um grupo de recursos existente. |
    | Selecione uma conta de armazenamento | Criar nova conta de armazenamento | No prompt seguinte, digite um nome globalmente exclusivo para a nova conta de armazenamento usada pelo seu aplicativo de funções e selecione Enter. Os nomes de conta de armazenamento devem ter entre 3 e 24 caracteres e podem conter apenas números e letras minúsculas. Você também pode selecionar uma conta existente. |
    | Selecione uma localização para novos recursos | region | Selecione um local em uma [região](https://azure.microsoft.com/regions/) perto de você ou perto de outros serviços que suas funções acessam. |

    Uma notificação é exibida depois que seu aplicativo de funções é criado e o pacote de implantação é aplicado. Escolha **Exibir Saída** nessa notificação para exibir a criação e os resultados da implantação, incluindo os recursos do Azure que você criou.

### <a name="get-the-url-of-an-http-triggered-function-in-azure"></a><a name="get-the-url-of-the-deployed-function"></a>Obter a URL de uma função disparada por HTTP no Azure

Para chamar uma função disparada por HTTP de um cliente, você precisa da URL da função quando ela é implantada em seu aplicativo de funções. Essa URL inclui quaisquer chaves de função necessárias. Você pode usar a extensão para obter essas URLs para suas funções implantadas. Se você quiser apenas executar a função remota no Azure, [use a funcionalidade executar função agora](#run-functions-in-azure) da extensão.

1. Selecione F1 para abrir a paleta de comandos e, em seguida, procure e execute o comando **Azure Functions: Copiar URL da função**.

1. Siga os prompts para selecionar seu aplicativo de funções no Azure e, em seguida, o gatilho HTTP específico que você deseja invocar.

A URL da função é copiada para a área de transferência, juntamente com as chaves necessárias passadas pelo `code` parâmetro de consulta. Use uma ferramenta HTTP para enviar solicitações POST ou um navegador para solicitações GET para a função remota.  

Ao obter a URL das funções no Azure, a extensão usa sua conta do Azure para recuperar automaticamente as chaves necessárias para iniciar a função. [Saiba mais sobre as chaves de acesso de função](security-concepts.md#function-access-keys). Iniciar funções disparadas não HTTP requer o uso da chave de administração.

## <a name="republish-project-files"></a>Republicar arquivos de projeto

Quando você configura a [implantação contínua](functions-continuous-deployment.md), seu aplicativo de funções no Azure é atualizado quando você atualiza os arquivos de origem no local de origem conectado. Recomendamos a implantação contínua, mas você também pode republicar as atualizações do arquivo de projeto de Visual Studio Code.

> [!IMPORTANT]
> Publicar em um aplicativo de funções existente substitui o conteúdo desse aplicativo no Azure.

[!INCLUDE [functions-republish-vscode](../../includes/functions-republish-vscode.md)]

## <a name="run-functions"></a>Executar funções

A extensão Azure Functions permite executar funções individuais em seu projeto em seu computador de desenvolvimento local ou em sua assinatura do Azure. 

Para funções de gatilho HTTP, a extensão chama o ponto de extremidade HTTP. Para outros tipos de gatilhos, ele chama APIs de administrador para iniciar a função. O corpo da mensagem da solicitação enviada para a função depende do tipo de gatilho. Quando um gatilho exige dados de teste, você é solicitado a inserir dados em um formato JSON específico.

### <a name="run-functions-in-azure"></a>Executar funções no Azure

Para executar uma função no Azure de Visual Studio Code. 

1. No palete de comando, insira **Azure Functions: execute a função agora** e escolha sua assinatura do Azure. 

1. Escolha seu aplicativo de funções no Azure na lista. Se você não vir seu aplicativo de funções, verifique se você está conectado à assinatura correta. 

1. Escolha a função que você deseja executar na lista e digite o corpo da mensagem da solicitação em **Inserir corpo da solicitação**. Clique em ENTER para enviar essa mensagem de solicitação à função. O texto padrão no **corpo da solicitação Enter** deve indicar o formato do corpo. Se seu aplicativo de funções não tiver funções, um erro de notificação será mostrado com esse erro. 

1. Quando a função é executada no Azure e retorna uma resposta, uma notificação é gerada no Visual Studio Code.
 
Você também pode executar sua função na área **Azure: funções** clicando com o botão direito do mouse (Ctrl-clicando no Mac) a função que você deseja executar de seu aplicativo de funções em sua assinatura do Azure e escolhendo **Executar função agora...**.

Ao executar funções no Azure, a extensão usa sua conta do Azure para recuperar automaticamente as chaves necessárias para iniciar a função. [Saiba mais sobre as chaves de acesso de função](security-concepts.md#function-access-keys). Iniciar funções disparadas não HTTP requer o uso da chave de administração.

### <a name="run-functions-locally"></a>Executar funções localmente

O tempo de execução local é o mesmo tempo de execução que hospeda seu aplicativo de funções no Azure. As configurações locais são lidas do [local.settings.jsno arquivo](#local-settings-file). Para executar seu projeto do Functions localmente, você deve atender a [requisitos adicionais](#run-local-requirements).

#### <a name="configure-the-project-to-run-locally"></a>Configurar o projeto para ser executado localmente

O tempo de execução do Functions usa uma conta de armazenamento do Azure internamente para todos os tipos de gatilho diferentes de HTTP e WebHooks. Portanto, você precisa definir a chave **Values. AzureWebJobsStorage** para uma cadeia de conexão válida da conta de armazenamento do Azure.

Esta seção usa a [extensão de armazenamento do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) com [Gerenciador de armazenamento do Azure](https://storageexplorer.com/) para se conectar e recuperar a cadeia de conexão de armazenamento.

Para definir a cadeia de conexão da conta de armazenamento:

1. No Visual Studio, abra o **Cloud Explorer**, expanda **conta de armazenamento**  >  **sua conta de armazenamento** e selecione **Propriedades** e copie o valor da **cadeia de conexão primária** .

2. Em seu projeto, abra o arquivo local.settings.json e defina o valor da chave **AzureWebJobsStorage** na cadeia de conexão que você copiou.

3. Repita a etapa anterior para adicionar as chaves exclusivas para a matriz de **Valores** para todas as outras conexões necessárias para as suas funções.

Para obter mais informações, consulte [Local Settings File](#local-settings-file).

#### <a name="debug-functions-locally"></a><a name="debugging-functions-locally"></a>Depurar funções localmente  

Para depurar suas funções, selecione F5. Se você ainda não tiver baixado as [ferramentas principais][Azure Functions Core Tools], você será solicitado a fazê-lo. Quando as ferramentas principais estão instaladas e em execução, a saída é mostrada no terminal. Isso é o mesmo que executar o `func host start` comando de ferramentas principais do terminal, mas com tarefas de compilação adicionais e um depurador anexado.  

Quando o projeto estiver em execução, você poderá usar o recurso **Executar função agora...** da extensão para disparar suas funções como você faria quando o projeto for implantado no Azure. Com o projeto em execução no modo de depuração, os pontos de interrupção são atingidos em Visual Studio Code como você esperaria. 

1. No palete de comando, insira **Azure Functions: execute a função agora** e escolha **projeto local**. 

1. Escolha a função que você deseja executar em seu projeto e digite o corpo da mensagem da solicitação em **Inserir corpo da solicitação**. Clique em ENTER para enviar essa mensagem de solicitação à função. O texto padrão no **corpo da solicitação Enter** deve indicar o formato do corpo. Se seu aplicativo de funções não tiver funções, um erro de notificação será mostrado com esse erro. 

1. Quando a função é executada localmente e depois que a resposta é recebida, uma notificação é gerada em Visual Studio Code. As informações sobre a execução da função são mostradas no painel **Terminal**.

A execução de funções localmente não requer o uso de chaves. 

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Por padrão, essas configurações não são migradas automaticamente quando o projeto é publicado no Azure. Depois que a publicação for concluída, você terá a opção de publicar configurações de local.settings.jsem seu aplicativo de funções no Azure. Para saber mais, consulte  [Publicar configurações do aplicativo](#publish-application-settings).

Os valores em **ConnectionStrings** nunca são publicados.

Os valores das configurações do aplicativo de funções também podem ser lidos em seu código como variáveis de ambiente. Para obter mais informações, consulte as seções de variáveis de ambiente desses artigos de referência específicos a um idioma:

* [C# pré-compilado](functions-dotnet-class-library.md#environment-variables)
* [Script do C# (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)
* [PowerShell](functions-reference-powershell.md#environment-variables)
* [Python](functions-reference-python.md#environment-variables)

## <a name="application-settings-in-azure"></a>Configurações do aplicativo no Azure

As configurações na local.settings.jsno arquivo em seu projeto devem ser iguais às configurações do aplicativo no aplicativo de funções no Azure. Todas as configurações adicionadas ao local.settings.jsem você também devem adicionar ao aplicativo de funções no Azure. Essas configurações não são carregadas automaticamente quando você publica o projeto. Da mesma forma, todas as configurações que você cria em seu aplicativo [de funções no portal](functions-how-to-use-azure-function-app-settings.md#settings) devem ser baixadas em seu projeto local.

### <a name="publish-application-settings"></a>Publicar configurações do aplicativo

A maneira mais fácil de publicar as configurações necessárias para seu aplicativo de funções no Azure é usar o link **carregar configurações** que aparece depois de publicar o projeto:

![Carregar configurações do aplicativo](./media/functions-develop-vs-code/upload-app-settings.png)

Você também pode publicar as configurações usando o comando **Azure Functions: carregar configuração local** na paleta de comandos. Você pode adicionar configurações individuais às configurações do aplicativo no Azure usando o comando **Azure Functions: Adicionar nova configuração** .

> [!TIP]
> Certifique-se de salvar seu local.settings.jsno arquivo antes de publicá-lo.

Se o arquivo local for criptografado, ele será descriptografado, publicado e criptografado novamente. Se houver configurações com valores conflitantes nos dois locais, você será solicitado a escolher como proceder.

Exiba configurações de aplicativo existentes na área **Azure: funções** expandindo sua assinatura, seu aplicativo de funções e **as configurações do aplicativo**.

![Exibir configurações do aplicativo de funções no Visual Studio Code](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Baixar configurações do Azure

Se você tiver criado as configurações do aplicativo no Azure, poderá baixá-las em seu local.settings.jsno arquivo usando o comando **Azure Functions: baixar configurações remotas** .

Assim como acontece com o carregamento, se o arquivo local for criptografado, ele será descriptografado, atualizado e criptografado novamente. Se houver configurações com valores conflitantes nos dois locais, você será solicitado a escolher como proceder.

## <a name="monitoring-functions"></a>Funções de monitoramento

Quando você [executa funções localmente](#run-functions-locally), os dados de log são transmitidos para o console do terminal. Você também pode obter dados de log quando seu projeto do Functions está em execução em um aplicativo de funções no Azure. Você pode se conectar aos logs de streaming no Azure para ver os dados de log quase em tempo real, ou pode habilitar Application Insights para um entendimento mais completo de como seu aplicativo de funções está se comportando.

### <a name="streaming-logs"></a>Logs de streaming

Quando você está desenvolvendo um aplicativo, muitas vezes é útil ver informações de log em tempo quase real. Você pode exibir um fluxo de arquivos de log que estão sendo gerados por suas funções. Essa saída é um exemplo de logs de streaming para uma solicitação para uma função disparada por HTTP:

![Saída de logs de streaming para gatilho HTTP](media/functions-develop-vs-code/streaming-logs-vscode-console.png)

Para saber mais, confira [logs de streaming](functions-monitoring.md#streaming-logs).

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> Os logs de streaming dão suporte a apenas uma única instância do host do functions. Quando a função é dimensionada para várias instâncias, os dados de outras instâncias não são mostrados no fluxo de log. [Live Metrics Stream](../azure-monitor/app/live-stream.md) no Application insights oferece suporte a várias instâncias. Embora também esteja quase em tempo real, o Stream Analytics é baseado em [dados de amostra](configure-monitoring.md#configure-sampling).

### <a name="application-insights"></a>Application Insights

Recomendamos que você monitore a execução de suas funções integrando seu aplicativo de funções com Application Insights. Quando você cria um aplicativo de funções no portal do Azure, essa integração ocorre por padrão. Ao criar seu aplicativo de funções durante a publicação do Visual Studio, você precisa integrar Application Insights você mesmo. Para saber como, consulte [habilitar a integração de Application insights](configure-monitoring.md#enable-application-insights-integration).

Para saber mais sobre o monitoramento usando Application Insights, consulte [monitorar Azure Functions](functions-monitoring.md).

## <a name="c-script-projects"></a>\#Projetos de script C

Por padrão, todos os projetos C# são criados como [projetos de biblioteca de classes compilados em c#](functions-dotnet-class-library.md). Se você preferir trabalhar com projetos de script C# em vez disso, deverá selecionar script C# como o idioma padrão nas configurações de extensão de Azure Functions:

1. Selecione **arquivo**  >  **preferências**  >  **configurações**.

1. Vá para **configurações de usuário**  >  **extensões**  >  **Azure Functions**.

1. Selecione o **script C #** do **Azure Function: linguagem do projeto**.

Depois de concluir essas etapas, as chamadas feitas às ferramentas básicas subjacentes incluem a `--csx` opção, que gera e publica arquivos de projeto do script C# (. CSX). Quando você tem esse idioma padrão especificado, todos os projetos que você cria padrão para projetos de script C#. Não será solicitado que você escolha uma linguagem de projeto quando um padrão for definido. Para criar projetos em outros idiomas, você deve alterar essa configuração ou removê-la do usuário settings.jsno arquivo. Depois de remover essa configuração, você será solicitado a escolher o idioma novamente quando criar um projeto.

## <a name="command-palette-reference"></a>Referência da paleta de comandos

A extensão Azure Functions fornece uma interface gráfica útil na área para interagir com seus aplicativos de funções no Azure. A mesma funcionalidade também está disponível como comandos na paleta de comandos (F1). Estes Azure Functions comandos estão disponíveis:

|Azure Functions comando  | Descrição  |
|---------|---------|
|**Adicionar novas configurações**  |  Cria uma nova configuração de aplicativo no Azure. Para saber mais, consulte [Publicar configurações do aplicativo](#publish-application-settings). Talvez você também precise [baixar essa configuração para suas configurações locais](#download-settings-from-azure). |
| **Configurar a origem da implantação** | Conecta seu aplicativo de funções no Azure a um repositório git local. Para saber mais, consulte [implantação contínua para Azure Functions](functions-continuous-deployment.md). |
| **Conectar ao repositório GitHub** | Conecta seu aplicativo de funções a um repositório GitHub. |
| **Copiar URL da função** | Obtém a URL remota de uma função disparada por HTTP que está sendo executada no Azure. Para saber mais, veja [obter a URL da função implantada](#get-the-url-of-the-deployed-function). |
| **Criar aplicativo de funções no Azure** | Cria um novo aplicativo de funções em sua assinatura no Azure. Para saber mais, consulte a seção sobre como [publicar em um novo aplicativo de funções no Azure](#publish-to-azure).        |
| **Descriptografar configurações** | Descriptografa [as configurações locais](#local-settings-file) que foram criptografadas por **Azure Functions: configurações de criptografia**.  |
| **Excluir Aplicativo de funções** | Remove um aplicativo de funções de sua assinatura no Azure. Quando não há outros aplicativos no plano do serviço de aplicativo, você tem a opção de excluí-lo também. Outros recursos, como contas de armazenamento e grupos de recursos, não são excluídos. Para remover todos os recursos, você deve, em vez disso, [excluir o grupo de recursos](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources). Seu projeto local não é afetado. |
|**Excluir função**  | Remove uma função existente de um aplicativo de funções no Azure. Como essa exclusão não afeta seu projeto local, considere remover a função localmente e, em seguida, [republicar seu projeto](#republish-project-files). |
| **Excluir proxy** | Remove um proxy Azure Functions do seu aplicativo de funções no Azure. Para saber mais sobre proxies, confira [trabalhar com proxies do Azure Functions](functions-proxies.md). |
| **Excluir configuração** | Exclui uma configuração de aplicativo de funções no Azure. Essa exclusão não afeta as configurações em seu local.settings.jsno arquivo. |
| **Desconectar do repositório**  | Remove a conexão de [implantação contínua](functions-continuous-deployment.md) entre um aplicativo de funções no Azure e um repositório de controle do código-fonte. |
| **Baixar configurações remotas** | Baixa as configurações do aplicativo de funções escolhido no Azure em seu local.settings.jsno arquivo. Se o arquivo local for criptografado, ele será descriptografado, atualizado e criptografado novamente. Se houver configurações com valores conflitantes nos dois locais, você será solicitado a escolher como proceder. Lembre-se de salvar as alterações em seu local.settings.jsno arquivo antes de executar esse comando. |
| **Editar configurações** | Altera o valor de uma configuração de aplicativo de funções existente no Azure. Esse comando não afeta as configurações em seu local.settings.jsno arquivo.  |
| **Configurações de criptografia** | Criptografa itens individuais na `Values` matriz nas [configurações locais](#local-settings-file). Nesse arquivo, `IsEncrypted` também é definido como `true` , que especifica que o tempo de execução local descriptografará as configurações antes de usá-las. Criptografe as configurações locais para reduzir o risco de vazar informações valiosas. No Azure, as configurações do aplicativo sempre são armazenadas criptografadas. |
| **Executar função agora** | Inicia manualmente uma função usando APIs de administrador. Esse comando é usado para teste, tanto localmente durante a depuração quanto em funções em execução no Azure. Ao disparar uma função no Azure, a extensão primeiro obtém automaticamente uma chave de administração, que é usada para chamar as APIs de administração remota que iniciam as funções no Azure. O corpo da mensagem enviada para a API depende do tipo de gatilho. Gatilhos de temporizador não exigem que você transmita dados. |
| **Inicializar o projeto para uso com VS Code** | Adiciona os arquivos de projeto de Visual Studio Code necessários a um projeto de funções existente. Use esse comando para trabalhar com um projeto que você criou usando as ferramentas básicas. |
| **Instalar ou atualizar Azure Functions Core Tools** | Instala ou atualiza [Azure Functions Core Tools], que é usado para executar funções localmente. |
| **Reimplantar**  | Permite reimplantar arquivos de projeto de um repositório git conectado para uma implantação específica no Azure. Para republicar as atualizações locais do Visual Studio Code, [Republique o projeto](#republish-project-files). |
| **Renomear configurações** | Altera o nome da chave de uma configuração de aplicativo de funções existente no Azure. Esse comando não afeta as configurações em seu local.settings.jsno arquivo. Depois de renomear as configurações no Azure, você deve [baixar essas alterações no projeto local](#download-settings-from-azure). |
| **Reiniciar** | Reinicia o aplicativo de funções no Azure. A implantação de atualizações também reinicia o aplicativo de funções. |
| **Definir AzureWebJobsStorage**| Define o valor da `AzureWebJobsStorage` configuração do aplicativo. Essa configuração é exigida pelo Azure Functions. Ele é definido quando um aplicativo de funções é criado no Azure. |
| **Iniciar** | Inicia um aplicativo de função parado no Azure. |
| **Iniciar Streaming de Logs** | Inicia os logs de streaming para o aplicativo de funções no Azure. Use logs de streaming durante a solução de problemas remota no Azure se você precisar ver informações de log em tempo quase real. Para saber mais, confira [logs de streaming](#streaming-logs). |
| **Parar** | Interrompe um aplicativo de funções que está sendo executado no Azure. |
| **Parar logs de streaming** | Interrompe os logs de streaming para o aplicativo de funções no Azure. |
| **Alternar como configuração de slot** | Quando habilitado, o garante que uma configuração de aplicativo persista para um determinado slot de implantação. |
| **Desinstalar o Azure Functions Core Tools** | Remove Azure Functions Core Tools, que é exigido pela extensão. |
| **Carregar configurações locais** | Carrega as configurações de seu local.settings.jsno arquivo para o aplicativo de funções escolhido no Azure. Se o arquivo local for criptografado, ele será descriptografado, carregado e criptografado novamente. Se houver configurações com valores conflitantes nos dois locais, você será solicitado a escolher como proceder. Lembre-se de salvar as alterações em seu local.settings.jsno arquivo antes de executar esse comando. |
| **Exibir confirmação no GitHub** | Mostra a confirmação mais recente em uma implantação específica quando seu aplicativo de funções está conectado a um repositório. |
| **Exibir logs de implantação** | Mostra os logs de uma implantação específica para o aplicativo de funções no Azure. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre Azure Functions Core Tools, confira [trabalhar com Azure Functions Core Tools](functions-run-local.md).

Para saber mais sobre como desenvolver funções como bibliotecas de classes do .NET, consulte [Referência do desenvolvedor de C# do Azure Functions](functions-dotnet-class-library.md). Este artigo também fornece links para exemplos de como usar atributos para declarar os vários tipos de associações com suporte pelo Azure Functions.

[Extensão do Azure Functions para Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Azure Functions Core Tools]: functions-run-local.md