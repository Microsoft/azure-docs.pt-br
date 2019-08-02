---
title: Desenvolver Azure Functions usando o Visual Studio Code | Microsoft Docs
description: Saiba como desenvolver e testar o Azure Functions usando a extensão de funções do Azure para Visual Studio Code.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: glenga
ms.openlocfilehash: 17550e148ea61eb69a20fc6a3215dfb63b65f18e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67452690"
---
# <a name="develop-azure-functions-using-visual-studio-code"></a>Desenvolver Azure Functions usando o Visual Studio Code

O [Extensão Azure Functions para Visual Studio Code] permite desenvolver localmente e implantar funções no Azure. Se esta for sua primeira experiência com o Azure Functions, você pode aprender mais em [Uma introdução ao Azure Functions](functions-overview.md).

A extensão de funções do Azure oferece os seguintes benefícios: 

* Editar, criar e executar funções em seu computador de desenvolvimento local. 
* Publicar seu projeto do Azure Functions diretamente no Azure. 
* Escreva suas funções em vários idiomas tendo todos os benefícios do Visual Studio Code. 

A extensão pode ser usada com os seguintes idiomas compatíveis com o tempo de execução do Azure Functions versão 2.x: 

* [C#compilado](functions-dotnet-class-library.md) 
* [C#script](functions-reference-csharp.md)<sup>*</sup>
* [JavaScript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [PowerShell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>Exige que você [definir C# script como o idioma padrão do projeto](#c-script-projects).

Neste artigo, exemplos atualmente só estão disponíveis para JavaScript (Node. js) e C# funções de biblioteca de classe.  

Este artigo fornece detalhes sobre como usar a extensão de funções do Azure para desenvolver funções e publicá-los no Azure. Antes de ler este artigo, você deve [criar sua primeira função usando o Visual Studio Code](functions-create-first-function-vs-code.md).

> [!IMPORTANT]
> Não combine o desenvolvimento local com o desenvolvimento do portal no mesmo aplicativo de funções. Ao publicar a partir de um projeto local para um aplicativo de funções, o processo de implantação substitui quaisquer funções que você desenvolveu o portal.

## <a name="prerequisites"></a>Pré-requisitos

Antes de instalar e executar o [extensão do Azure Functions][extensão azure functions para visual studio code], você deve atender aos seguintes requisitos:

* [Visual Studio Code](https://code.visualstudio.com/) instalado em um dos [plataformas com suporte](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Uma assinatura ativa do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Outros recursos que você precisa, como uma conta de armazenamento do Azure, são criados em sua assinatura quando você [publicar usando o Visual Studio Code](#publish-to-azure).

> [!IMPORTANT]
> Você pode desenvolver funções localmente e publicar no Azure sem ter que iniciar e executá-los localmente. Há requisitos adicionais para executar seu funcional localmente, incluindo um download automático de ferramentas do Azure Functions Core. Para obter mais informações, consulte [requisitos adicionais para executar localmente](#additional-requirements-to-run-locally). 

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Criar um projeto do Azure Functions

A extensão de funções permite que você crie um projeto de aplicativo de função, juntamente com sua primeira função. As etapas a seguir mostram como criar uma função HTTP disparada em um novo projeto de funções. [Gatilho HTTP](functions-bindings-http-webhook.md) é o modelo de gatilho de função mais simples para demonstrar.

1. Do **Azure: Functions**, escolha o ícone Criar Função.

    ![Criar uma função](./media/functions-develop-vs-code/create-function.png)

1. Selecione a pasta do seu projeto de aplicativo de função e, em seguida **selecione um idioma para o seu projeto de função**. 

1. Selecione o **gatilho HTTP** modelo de função, ou você pode optar por **ignorar agora** para criar um projeto sem uma função. Você pode sempre [adicionar uma função ao seu projeto](#add-a-function-to-your-project) em um momento posterior. 

    ![Escolher o modelo de gatilho HTTP](./media/functions-develop-vs-code/create-function-choose-template.png)

1. Tipo de `HTTPTrigger` para o nome da função e pressione Enter, em seguida, selecione **função** autorização. Esse nível de autorização exige que você forneça uma [tecla de função](functions-bindings-http-webhook.md#authorization-keys) ao chamar o ponto de extremidade de função.

    ![Escolher a autenticação de função](./media/functions-develop-vs-code/create-function-auth.png)

    Uma função é criada na linguagem escolhida por você usando o modelo de uma função disparada por HTTP.

    ![Modelo de função disparada por HTTP no Visual Studio Code](./media/functions-develop-vs-code/new-function-full.png)

O modelo de projeto cria um projeto no idioma escolhido, instala as dependências necessárias. Para qualquer idioma, o novo projeto tem os seguintes arquivos:

* **host.json**: Permite configurar o host do Functions. Essas configurações se aplicam para execução local e no Azure. Para obter mais informações, consulte a [referência para host.json](functions-host-json.md).

* **local.settings.json**: Mantém as configurações usadas ao executar as funções localmente. Essas configurações são usadas somente quando em execução localmente. Para obter mais informações, consulte [arquivo de configurações Local](#local-settings-file).

    >[!IMPORTANT]
    >Como o arquivo Settings pode conter segredos, ele devem ser excluídos do seu controle de origem do projeto.

Neste ponto, você pode adicionar entrada e associações de saída à sua função pela [modificando o arquivo Function. JSON](#javascript-2), ou pelo [adicionando um parâmetro para um C# função de biblioteca de classes](#c-class-library-2).

Você também pode [adicionar uma nova função ao seu projeto](#add-a-function-to-your-project).

## <a name="install-binding-extensions"></a>Instalar extensões de associação

Exceto para gatilhos HTTP e o Timer, associações são implementadas em pacotes de extensão. Você deve instalar os pacotes de extensão para os gatilhos e associações que necessitam deles. A maneira que você instale as extensões de associação depende do idioma de seu projeto.

### <a name="javascript"></a>JavaScript

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

### <a name="c-class-library"></a>C\# biblioteca de classes

Execute o [dotnet Adicionar pacote](/dotnet/core/tools/dotnet-add-package) comando na janela do Terminal para instalar os pacotes de extensão que você precisa em seu projeto. O exemplo a seguir instala a extensão de armazenamento do Azure, que implementa associações para o armazenamento de Blob, fila e tabela.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

## <a name="add-a-function-to-your-project"></a>Adicionar uma função ao projeto

Você pode adicionar uma nova função a um projeto existente usando um dos modelos de gatilho de funções predefinidos. Para adicionar um novo gatilho de função, pressione a tecla F1 para abrir a paleta de comandos, em seguida, procure e execute o comando **Azure Functions: Crie função...** . Siga os prompts para escolher o tipo de disparador e definir os atributos necessários do gatilho. Se o gatilho requer uma cadeia de chave ou conexão de acesso para se conectar a um serviço, prepará-lo antes de criar o gatilho da função. 

Os resultados dessa operação dependem do idioma de seu projeto:

### <a name="javascript"></a>JavaScript

Uma nova pasta é criada no projeto, que contém um novo arquivo Function. JSON e o novo arquivo de código JavaScript.

### <a name="c-class-library"></a>C\# biblioteca de classes

Um novo C# arquivo de biblioteca (. cs) de classe é adicionado ao seu projeto.

## <a name="add-input-and-output-bindings"></a>Adicionar entrada e associações de saída

Você pode expandir sua função, adicionando associações de entrada e saída. A maneira de fazer isso depende do idioma de seu projeto. Para obter mais informações sobre associações, consulte [conceitos de associações e gatilhos do Azure Functions](functions-triggers-bindings.md). 

Os exemplos a seguir se conectar a uma fila de armazenamento denominada `outqueue`, em que a cadeia de caracteres de conexão da conta de armazenamento é definida no `MyStorageConnection` configuração de aplicativo no Settings. 

### <a name="javascript"></a>JavaScript

Visual Studio Code permite que você adicionar associações ao seu arquivo Function. JSON, seguindo um conjunto prático de prompts. Para criar uma associação, botão direito do mouse (Ctrl + clique no macOS) a `function.json` arquivo na pasta da função e escolha **Adicionar associação de...** . 

![Adicionar uma associação a uma função JavaScript existente ](media/functions-develop-vs-code/function-add-binding.png)

Veja a seguir os prompts de exemplo para definir uma nova associação de saída de armazenamento:

| Prompt | Value | DESCRIÇÃO |
| -------- | ----- | ----------- |
| **Selecione a direção de associação** | `out` | A associação é uma associação de saída. |
| **Selecione a associação com a direção...** | `Azure Queue Storage` | A associação é uma associação de fila do Armazenamento do Azure. |
| **O nome usado para identificar esta associação em seu código** | `msg` | Nome que identifica o parâmetro de associação referenciado em seu código. |
| **A fila à qual a mensagem será enviada** | `outqueue` | O nome da fila na qual a associação escreve. Quando o *queueName* não existe, a associação o cria no primeiro uso. |
| **Selecione a configuração de "local.setting.json"** | `MyStorageConnection` | O nome de uma configuração de aplicativo que contém a cadeia de conexão para a conta de armazenamento. A configuração `AzureWebJobsStorage` contém a cadeia de conexão para a Conta de armazenamento criada com o aplicativo de funções. |

Neste exemplo, a associação a seguir é adicionada para o `bindings` matriz em seu arquivo Function. JSON:

```javascript
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```

Você também pode adicionar a mesma definição de associação diretamente para seu Function. JSON.

No código da função, o `msg` associação é acessada a partir de `context`, conforme mostrado no exemplo a seguir:

```javascript
context.bindings.msg = "Name passed to the function: " req.query.name;
```

Para obter mais informações, consulte o [associação de saída do armazenamento de filas](functions-bindings-storage-queue.md#output---javascript-example) referência.

### <a name="c-class-library"></a>C\# biblioteca de classes

Atualize o método de função para adicionar o parâmetro a seguir para o `Run` definição de método:

```cs
[Queue("outqueue"),StorageAccount("MyStorageConnection")] ICollector<string> msg
```

Esse código requer que você adicione o seguinte `using` instrução:

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

O `msg` parâmetro é um `ICollector<T>` tipo, que representa uma coleção de mensagens que são gravados em uma saída de associação quando a função for concluída. Você adicionar uma ou mais mensagens na coleção, que são enviadas para a fila quando a função é concluída.

Para obter mais informações, consulte o [associação de saída do armazenamento de filas](functions-bindings-storage-queue.md#output---c-example) referência.

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="publish-to-azure"></a>Publicar no Azure

O Visual Studio Code permite que você publique seu projeto de funções diretamente no Azure. No processo, você criará um aplicativo de funções e recursos relacionados em sua assinatura do Azure. O aplicativo de funções fornece um contexto de execução para suas funções. O projeto é empacotado e implantado para o novo aplicativo de função em sua assinatura do Azure.

Ao publicar no Visual Studio Code, um dos dois métodos de implantação são usados:

* [Implantar com a execução do pacote habilitado de zip](functions-deployment-technologies.md#zip-deploy): usado para a maioria das implantações do Azure Functions.
* [URL do pacote externo](functions-deployment-technologies.md#external-package-url): usado para a implantação para aplicativos Linux em um [plano de consumo](functions-scale.md#consumption-plan).

### <a name="quick-function-app-creation"></a>Criação de aplicativos de função rápida

Por padrão, o código do Visual Studio gera automaticamente valores para os recursos do Azure necessários para seu aplicativo de funções. Esses valores são com base no nome do aplicativo de função escolhida. Para obter um exemplo de como usar os padrões para publicar seu projeto para um novo aplicativo de funções no Azure, consulte a [artigo de início rápido do Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure).

Se você quiser fornecer nomes explícitos para os recursos criados, você deve habilitar a publicação usando opções avançadas.

### <a name="enabled-publishing-with-advanced-create-options"></a>Opções de criação de publicação habilitada com avançada

Para dar a você controle sobre as configurações com o Azure Functions criando aplicativos associados, atualize a extensão de funções do Azure para habilitar as configurações avançadas.

1. Clique em **arquivo > Preferências > configurações**

1. Navegar pelos **as configurações do usuário > extensões > o Azure Functions**

1. Marque a caixa de seleção **função do Azure: Criação avançada**

### <a name="publish-to-a-new-function-app-in-azure-with-advanced-creation"></a>Publicar em um novo aplicativo de funções no Azure com a criação avançada

As etapas a seguir publicar seu projeto em um novo aplicativo de funções criado usando avançado de opções de criação.

1. Na área **Azure: Functions**, escolha o ícone Implantar no aplicativo de funções.

    ![Configurações do aplicativo de funções](./media/functions-develop-vs-code/function-app-publish-project.png)

1. Se você ainda não estiver conectado, será solicitado a **Entrar no Azure**. Você também pode **Criar uma conta gratuita do Azure**. Após entrar pelo navegador, volte ao Visual Studio Code.

1. Se você tiver mais de uma assinatura, **Escolha uma assinatura** para o aplicativo de funções e, em seguida, escolha **+ Criar novo aplicativo de funções no Azure**.

1. Siga os prompts, forneça as seguintes informações:

    | Prompt | Value | DESCRIÇÃO |
    | ------ | ----- | ----------- |
    | Selecione o aplicativo de funções no Azure | + Criar novo aplicativo de funções no Azure | No próximo prompt, digite um nome exclusivo que identifica seu novo aplicativo de função e pressione Enter. Caracteres válidos para um nome de aplicativo de funções são `a-z`, `0-9` e `-`. |
    | Selecione um sistema operacional | Windows | Aplicativo de funções é executado no Windows |
    | Selecione um plano de hospedagem | Plano de consumo | Sem servidor [hospedagem de plano de consumo](functions-scale.md#consumption-plan) é usado. |
    | Selecione um tempo de execução para o novo aplicativo | O idioma do projeto | O tempo de execução deve coincidir com o projeto que você está publicando. |
    | Selecione um grupo de recursos para os novos recursos | Criar novo grupo de recursos | No próximo prompt, digite um nome de grupo de recursos, como `myResourceGroup`, e pressione enter. Você também pode escolher um grupo de recursos. |
    | Selecione uma conta de armazenamento | Criar nova conta de armazenamento | No próximo prompt, digite um nome globalmente exclusivo da nova conta de armazenamento usado pelo seu aplicativo de funções e pressione Enter. Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres e podem conter apenas números e letras minúsculas. Você também pode escolher uma conta existente. |
    | Selecione um local para os novos recursos | region | Escolha um local em uma [região](https://azure.microsoft.com/regions/) perto de você ou perto de outros serviços acessados pelas suas funções. |

    Uma notificação é exibida depois que seu aplicativo de funções é criado e o pacote de implantação é aplicado. Escolha **Exibir Saída** nessa notificação para exibir a criação e os resultados da implantação, incluindo os recursos do Azure que você criou.

## <a name="republish-project-files"></a>Republicar os arquivos de projeto

Quando você configura [implantação contínua](functions-continuous-deployment.md), seu aplicativo de funções no Azure é atualizado sempre que os arquivos de origem são atualizados no local de origem conectado. Embora seja recomendável essa prática de desenvolvimento, você também pode publicar novamente as atualizações de arquivo de projeto do Visual Studio Code. 

> [!IMPORTANT]
> Publicar em um aplicativo de funções existente substitui o conteúdo desse aplicativo no Azure.

1. No Visual Studio Code, pressione F1 para abrir a paleta de comandos. Na paleta de comandos, pesquise e selecione `Azure Functions: Deploy to function app...`.

1. Se você ainda não estiver conectado, será solicitado a **Entrar no Azure**. Após entrar pelo navegador, volte ao Visual Studio Code. Se você tiver várias assinaturas, **selecione uma assinatura** que contém seu aplicativo de funções.

1. Escolha seu aplicativo de funções existente no Azure. Quando avisado sobre a substituição de todos os arquivos no aplicativo de funções, escolha **Deploy** para confirmar o aviso e continuar. 

O projeto é recriado, reempacotado e carregado no Azure. O projeto existente é substituído pelo novo pacote e reinicia o aplicativo de funções.

## <a name="get-deployed-function-url"></a>Obter URL de função implantadas

Para poder chamar uma função disparada por HTTP, você precisa da URL da função quando implantado em seu aplicativo de função. Essa URL inclui quaisquer [teclas de função](functions-bindings-http-webhook.md#authorization-keys). Você pode usar a extensão para obter essas URLs para suas funções implantadas.

1. chave de pressione F1 para abrir a paleta de comandos, em seguida, procure e execute o comando **Azure Functions: Copiar URL da função**.

1. Siga os prompts para escolher seu aplicativo de funções no Azure e, em seguida, o gatilho HTTP específico que deseja invocar. 

A função de URL é copiado para a área de transferência, juntamente com quaisquer chaves necessárias passado usando o `code` parâmetro de consulta. Use uma ferramenta de HTTP para enviar solicitações POST ou um navegador para solicitações GET para a função remota.  

## <a name="run-functions-locally"></a>Executar funções localmente

A extensão de funções do Azure permite executar um projeto de funções em seu computador de desenvolvimento local. O tempo de execução local é o tempo de execução mesmo que hospeda seu aplicativo de funções no Azure. As configurações locais são lidos a partir de [arquivo Settings](#local-settings-file).

### <a name="additional-requirements-to-run-locally"></a>Requisitos adicionais para executar localmente

Para poder executar seu projeto de funções localmente, você deve também atender a esses requisitos adicionais:

* Instale a versão 2.x do [Azure Functions Core Tools](functions-run-local.md#v2). O pacote de ferramentas principal é baixado e instalado para seu automaticamente quando você inicia o projeto localmente. As principais ferramentas incluem todo o tempo de execução de funções do Azure, portanto, o download e a instalação podem levar algum tempo.

* Instale os requisitos específicos para a linguagem de programação escolhida:

    | Linguagem | Requisito |
    | -------- | --------- |
    | **C#** | [Extensão C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)<br/>[Ferramentas de CLI do .NET core](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)   |
    | **Java** | [Depurador para a extensão de Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](https://aka.ms/azure-jdks)<br/>[Maven 3+](https://maven.apache.org/) |
    | **JavaScript** | [Node.js](https://nodejs.org/)<sup>*</sup> |  
    | **Python** | [Extensão do Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)<br/>[Python 3.6 +](https://www.python.org/downloads/)|

    <sup>*</sup>LTS e manutenção LTS versões ativas (8.11.1 e 10.14.1 recomendado).

### <a name="configure-the-project-to-run-locally"></a>Configurar o projeto para executar localmente

O tempo de execução de Functions usa internamente uma conta de armazenamento do Azure para todos os tipos de gatilho diferente de HTTP e webhooks. Isso significa que você deve definir a **azurewebjobsstorage** chave para uma cadeia de conexão de conta de armazenamento do Azure válida.

Esta seção usa o [extensão de armazenamento do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) com [Microsoft Azure Storage Explorer](https://storageexplorer.com/) para conectar e recuperar a cadeia de caracteres de conexão de armazenamento.   

Para definir a cadeia de conexão da conta de armazenamento:

1. No Visual Studio, abra **Cloud Explorer**, expanda **Conta de armazenamento** > **Sua conta de armazenamento**, selecione **Propriedades** e copie o valor da **Cadeia de conexão primária**.

2. Em seu projeto, abra o arquivo local.settings.json e defina o valor da chave **AzureWebJobsStorage** na cadeia de conexão que você copiou.

3. Repita a etapa anterior para adicionar as chaves exclusivas para a matriz de **Valores** para todas as outras conexões necessárias para as suas funções.

Para obter mais informações, consulte [arquivo de configurações Local](#local-settings-file).

### <a name="debugging-functions-locally"></a>Depurando funções localmente  

Para depurar suas funções, pressione F5. Se você ainda não tiver baixado o [ferramentas básicas][ferramentas básicas do azure functions], você precisará fazê-lo. Quando as ferramentas principais é instalada e em execução, a saída é mostrada no Terminal. Isso é o mesmo que executar `func host start` comando de ferramentas básicas do Terminal, mas com adicionais criar tarefas e um depurador anexado.  

Com o projeto em execução, você pode disparar as funções, como você faria quando implantado no Azure. Quando em execução no modo de depuração, os pontos de interrupção são atingidos no Visual Studio Code, conforme o esperado.

A URL de solicitação para gatilhos HTTP é exibida na saída no terminal. Teclas de função para gatilhos HTTP não são usadas ao executar localmente. Para saber mais informações, consulte [Estratégias para testar seu código no Azure Functions](functions-test-a-function.md).  

Para obter mais informações, consulte [trabalhar com as ferramentas básicas do Azure Functions][ferramentas básicas do azure functions].

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Por padrão, essas configurações não são migradas automaticamente quando o projeto é publicado no Azure. Após a publicação for concluída, você terá a opção de publicar configurações de Settings para seu aplicativo de funções no Azure. Para obter mais informações, consulte [configurações de aplicativo de publicação](#publish-application-settings).

Os valores em **ConnectionStrings** nunca são publicados.

Os valores de configurações do aplicativo de função também podem ser lidas em seu código, como variáveis de ambiente. Para obter mais informações, consulte a seção de variáveis de ambiente desses artigos de referência específicos de linguagem:

* [C# pré-compilado](functions-dotnet-class-library.md#environment-variables)
* [Script do C# (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

## <a name="application-settings-in-azure"></a>Configurações do aplicativo no Azure

As configurações no arquivo Settings no seu projeto devem ser o mesmo que as configurações do aplicativo no aplicativo de funções no Azure. Todas as configurações que você adicionar para o Settings também devem ser adicionadas ao aplicativo de funções no Azure. Essas configurações não são carregadas automaticamente quando você publicar o projeto. Da mesma forma, todas as configurações que você cria no seu aplicativo de funções [no portal do](functions-how-to-use-azure-function-app-settings.md#settings) devem ser baixados para seu projeto local.

### <a name="publish-application-settings"></a>Configurações de aplicativo de publicação

A maneira mais fácil para publicar as configurações necessárias para seu aplicativo de funções no Azure é usar o **configurações de carregamento** link que é exibido depois de publicar seu projeto com êxito.

![Implantação concluída carregar as configurações de aplicativo](./media/functions-develop-vs-code/upload-app-settings.png)

Você também pode publicar as configurações usando o `Azure Functions: Upload Local Setting` comando na paleta de comandos. As configurações individuais são adicionadas às configurações do aplicativo no Azure usando o `Azure Functions: Add New Setting...` comando. 

> [!TIP]
> Certifique-se de salvar seu arquivo Settings antes de publicá-lo.

Se o arquivo local for criptografado, ele é descriptografado, publicado e criptografado novamente. Se existem configurações com valores diferentes nos dois locais, será perguntado ao escolher como proceder.

Exibir configurações de aplicativo existente no **Azure: Funções** área, expandindo sua assinatura, seu aplicativo de funções, e **configurações do aplicativo**.

![Configuração de aplicativo de função do modo de exibição no Visual Studio Code](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Configurações de download do Azure

Se você tiver criado as configurações do aplicativo no Azure, você pode baixá-los em seu arquivo Settings. usando o `Azure Functions: Download Remote Settings...` comando. 

Assim como acontece com carregamento, se o arquivo local é criptografado, ele é descriptografado, atualizado e criptografado novamente. Se existem configurações com valores diferentes nos dois locais, será perguntado ao escolher como proceder.

## <a name="monitoring-functions"></a>Funções de monitoramento

Quando você [executados localmente](#run-functions-locally), dados de log são transmitidos para o console de Terminal. Você também pode obter dados de log quando seu projeto de funções está em execução em um aplicativo de função no Azure. Você a se conectar ao streaming de logs no Azure para ver os dados de log quase em tempo real, ou você pode habilitar o Application Insights para obter um entendimento mais completo de como seu aplicativo de funções está se comportando.

### <a name="streaming-logs"></a>Logs de streaming

Ao desenvolver um aplicativo, é sempre útil visualizar informações de registro em log realizado em tempo quase real. Você pode exibir um fluxo de arquivos de log que estão sendo gerados por suas funções. A saída a seguir é uma função disparada por um exemplo de streaming de logs para que uma solicitação HTTP:

![Logs de saída para o gatilho HTTP de streaming](media/functions-develop-vs-code/streaming-logs-vscode-console.png) 

Para obter mais informações, consulte [Logs de Streaming](functions-monitoring.md#streaming-logs). 

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> Logs de streaming dão suporte a apenas uma única instância de host do Functions. Quando sua função é dimensionada para várias instâncias, os dados de outras instâncias não são mostrados no fluxo de log. O [Stream de métricas ao vivo](../azure-monitor/app/live-stream.md) no Application Insights, suporte para várias instâncias. Embora também em quase em tempo real, análise de streaming também é baseado na [dados de amostra](functions-monitoring.md#configure-sampling).

### <a name="application-insights"></a>Application Insights

A maneira recomendada de monitorar a execução de suas funções é a integração do aplicativo de funções com o Azure Application Insights. Ao criar um aplicativo de funções no portal do Azure, essa integração é realizada por padrão. No entanto, ao criar o aplicativo de funções durante a publicação do Visual Studio, a integração no aplicativo de funções no Azure não é realizada.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Para saber mais, consulte [Monitorar Azure Functions](functions-monitoring.md).

## <a name="c-script-projects"></a>C\# projetos de script

Por padrão, todos os C# projetos são criados como [ C# compilado projetos de biblioteca de classes](functions-dotnet-class-library.md). Se você preferir em vez disso trabalhar com C# projetos de script, você deve selecionar C# script como o idioma padrão nas configurações de extensão do Azure Functions.

1. Clique em **arquivo > Preferências > configurações**.

1. Navegar pelos **as configurações do usuário > extensões > o Azure Functions**.

1. Escolher **C #Script** de **função do Azure: Idioma do projeto**.

Neste ponto, as chamadas feitas para as principais ferramentas subjacente incluem o `--csx` opção, que gera e publica C# arquivos de projeto (. CSx) de script. Com um idioma padrão especificado, todos criados padrão de projetos para C# projetos de script. Você não será solicitado para escolher um idioma do projeto quando um padrão é definido. Para criar outros projetos de linguagem, você deve alterar essa configuração ou removê-lo do usuário arquivo Settings JSON. Depois de remover essa configuração, você será solicitado novamente para escolher seu idioma ao criar um projeto.

## <a name="command-palette-reference"></a>Referência da paleta de comandos

A extensão do Azure Functions fornece uma interface gráfica úteis na área de trabalho do Azure para interagir com seus aplicativos de funções no Azure. A mesma funcionalidade também está disponível como comandos na paleta de comandos (F1). Os seguintes comandos específicos de funções do Azure estão disponíveis:

|Comando de funções do Azure  | DESCRIÇÃO  |
|---------|---------|
|**Adicione novas configurações...**  |  Cria uma nova configuração de aplicativo no Azure. Para obter mais informações, consulte [configurações de aplicativo de publicação](#publish-application-settings). Você talvez também precise [baixar essa configuração para suas configurações locais](#download-settings-from-azure). |
| **Configure a origem de implantação...** | Conecte seu aplicativo de função no Azure a um repositório Git local. Para obter mais informações, consulte [implantação contínua para o Azure Functions](functions-continuous-deployment.md). |
| **Conectar-se ao repositório do GitHub...** | Conecte seu aplicativo de funções de um repositório do GitHub. |
| **Copiar URL da função** | Obtém a URL remota de um HTTP disparada função em execução no Azure. Para obter mais informações, consulte como [obter a URL da função implantada](#get-deployed-function-url). |
| **Crie aplicativo de funções no Azure...** | Cria um novo aplicativo de funções em sua assinatura no Azure. Para obter mais informações, consulte como [publicar em um novo aplicativo de funções no Azure](#publish-to-azure).        |
| **Descriptografar configurações** | Usar para descriptografar [as configurações locais](#local-settings-file) criptografados usando `Azure Functions: Encrypt Settings`.  |
| **Excluir o aplicativo de funções...** | Remove um aplicativo de função existente de sua assinatura no Azure. Quando não houver nenhum outro aplicativo no plano de serviço de aplicativo, você recebe a opção de exclusão que muito. Outros recursos, como contas de armazenamento e grupos de recursos, não são excluídos. Para remover todos os recursos, em vez disso, você deve [excluir o grupo de recursos](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources). Seu projeto local não é afetado. |
|**Exclua função...**  | Remove uma função existente de um aplicativo de funções no Azure. Porque essa exclusão não afeta seu projeto local, em vez disso, considere remover a função localmente e, em seguida [republicando seu projeto](#republish-project-files). |
| **Exclua um Proxy...** | Remove um proxy do Azure Functions no seu aplicativo de função no Azure. Para saber mais sobre proxies, confira [trabalhar com Proxies do Azure Functions](functions-proxies.md). |
| **Exclua a configuração...** | Exclui uma configuração de aplicativo de função no Azure. Não afeta as configurações no seu arquivo Settings. |
| **Desconecte do repositório...**  | Remover o [implantação contínua](functions-continuous-deployment.md) conexão entre um aplicativo de funções no Azure e um repositório de controle do código-fonte. |
| **Baixe configurações remotas...** | Baixa as configurações do aplicativo de função escolhida no Azure em seu arquivo Settings. Se o arquivo local for criptografado, ele é descriptografado, atualizado e criptografado novamente. Se existem configurações com valores diferentes nos dois locais, será perguntado ao escolher como proceder. Certifique-se de que você salvou as alterações ao seu arquivo Settings antes de executar esse comando. |
| **Edite configurações...** | Altera o valor de uma configuração de aplicativo de função existente no Azure. Não afeta as configurações no seu arquivo Settings.  |
| **Criptografar configurações** | Criptografa itens individuais na `Values` matriz na [as configurações locais](#local-settings-file). Nesse arquivo, `IsEncrypted` também é definido como `true`, que informa ao tempo de execução local para descriptografar as configurações antes de usá-los. Criptografe configurações locais para reduzir o risco de vazamento de informações valiosas. No Azure, o aplicativo configurações sempre são armazenadas criptografados. |
| **Executar função agora** | Inicia uma [função disparada por temporizador](functions-bindings-timer.md) no Azure manualmente para fins de teste. Para saber mais sobre disparar funções não-HTTP no Azure, consulte [executar manualmente uma função disparada HTTP não](functions-manually-run-non-http.md). |
| **Inicialize o projeto para uso com o VS Code...** | Adiciona os arquivos de projeto do Visual Studio Code necessários a um projeto de funções existente. Use este comando para trabalhar com um projeto que você criou usando as ferramentas básicas. |
| **A instalação de ferramentas de atualização de núcleo do Azure Functions** | Instala ou atualiza o [Ferramentas básicas do Azure Functions] que são usados para executar localmente. |
| **Redeploy**  | Permite que você reimplante os arquivos de projeto de um repositório Git conectado a uma implantação específica no Azure. Para republicar atualizações locais do Visual Studio Code, [republique seu projeto](#republish-project-files). |
| **Renomear as configurações...** | Altera o nome da chave de uma configuração de aplicativo de função existente no Azure. Não afeta as configurações no seu arquivo Settings. Depois de renomear as configurações no Azure, você deve [baixar essas alterações para o local do projeto](#download-settings-from-azure). |
| **Reiniciar** | Reinicia o aplicativo de funções no Azure. Implantando atualizações também reinicia o aplicativo de funções. |
| **Definir AzureWebJobStorage...**| Define o valor da `AzureWebJobStorage` configuração de aplicativo. Essa configuração é exigida pelas funções do Azure e é definida quando o aplicativo de funções é criado no Azure. |
| **Iniciar** | Inicia um aplicativo de funções interrompido no Azure. | 
| **Iniciar o Streaming de Logs** | Inicia os logs de fluxo para o aplicativo de funções no Azure. Use logs de streaming durante a solução de problemas remota no Azure se você precisar ver essas informações em tempo quase real. Para obter mais informações, consulte [Logs de Streaming](#streaming-logs). |
| **Parar** | Busca desliga um aplicativo de funções em execução no Azure. |
| **Parar o Streaming de Logs** | Interrompe os logs de fluxo para o aplicativo de funções no Azure. |
| **Ativar/desativar como configuração de Slot** | Quando habilitada, garante-se de que uma configuração de aplicativo persiste por um determinado slot de implantação. |
| **Desinstalar as ferramentas básicas do Azure Functions** | Remove as ferramentas básicas do Azure Functions, que é exigido pela extensão. |
| **Carregar as configurações locais...** | Carrega as configurações do seu arquivo Settings para o aplicativo de função escolhida no Azure. Se o arquivo local for criptografado, ele é descriptografado, carregado e criptografado novamente. Se existem configurações com valores diferentes nos dois locais, será perguntado ao escolher como proceder. Certifique-se de que você salvou as alterações ao seu arquivo Settings antes de executar esse comando. |
| **Exibir confirmação no GitHub** | Mostra a última confirmação em uma implantação específica quando seu aplicativo de funções está conectado a um repositório. |
| **Exibir Logs de implantação** | Mostra os logs para uma implantação específica ao aplicativo de funções no Azure. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as Ferramentas Básicas do Azure Functions, consulte [Codificar e testar o Azure Functions localmente](functions-run-local.md).

Para saber mais sobre como desenvolver funções como bibliotecas de classes do .NET, consulte [Referência do desenvolvedor de C# do Azure Functions](functions-dotnet-class-library.md). Este tópico também fornece links para exemplos de como usar atributos para declarar os vários tipos de associações com suporte pelo Azure Functions.    

[Extensão Azure Functions para Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Ferramentas básicas do Azure Functions]: functions-run-local.md