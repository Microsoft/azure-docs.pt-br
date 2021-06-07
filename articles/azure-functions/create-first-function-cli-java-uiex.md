---
title: Criar uma função Java da linha de comando – Azure Functions
description: Saiba como criar uma função Java da linha de comando e publicar o projeto local por meio da hospedagem sem servidor no Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-java
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: a270f482438129b7b07506744bf3f9489b242900
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787477"
---
# <a name="quickstart-create-a-java-function-in-azure-from-the-command-line"></a>Início rápido: criar uma função Java no Azure da linha de comando

> [!div class="op_single_selector" title1="Selecione a linguagem da função: "]
> - [Java](create-first-function-cli-java.md)
> - [Python](create-first-function-cli-python.md)
> - [C#](create-first-function-cli-csharp.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [TypeScript](create-first-function-cli-typescript.md)

Use ferramentas da linha de comando para criar uma função Java que responde a solicitações HTTP. Teste o código localmente e implante-o no ambiente sem servidor do Azure Functions.

A realização deste início rápido gera um pequeno custo de alguns centavos de dólar ou menos em sua <abbr title="O perfil que mantém as informações de cobrança pelo uso do Azure.">Conta do Azure</abbr>.

Se o Maven não for sua ferramenta de desenvolvimento preferida, confira nossos tutoriais semelhantes para desenvolvedores Java usando [Gradle](./functions-create-first-java-gradle.md), [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions) e [Visual Studio Code](create-first-function-vs-code-java.md).

## <a name="1-prepare-your-environment"></a>1. Prepare o seu ambiente

Antes de começar, você deverá ter o seguinte:

+ Uma conta do Azure com uma assinatura ativa <abbr title="A estrutura organizacional básica na qual você gerencia recursos no Azure, normalmente associada a um indivíduo ou departamento da organização.">subscription</abbr>. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#v2), versão 3.x.

+ A [CLI do Azure](/cli/azure/install-azure-cli) versão 2.4 ou posterior.

+ O [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), versão 8 ou 11. A variável de ambiente `JAVA_HOME` precisa ser definida como a localização de instalação da versão correta do JDK.

+ [Apache Maven](https://maven.apache.org), versão 3.0 ou posterior.

### <a name="prerequisite-check"></a>Verificação de pré-requisitos

+ Em um terminal ou janela de comando, execute `func --version` para verificar se o <abbr title="O conjunto de ferramentas de linha de comando para trabalhar com o Azure Functions no computador local.">Azure Functions Core Tools</abbr> é a versão 3.x.

+ Execute `az --version` para verificar se a versão da CLI do Azure é a 2.4 ou posterior.

+ Execute `az login` para entrar no Azure e verifique se a assinatura está ativa.

<br>
<hr/>

## <a name="2-create-a-local-function-project"></a>2. Criar um projeto de função local

No Azure Functions, um projeto de função é um contêiner para uma ou mais funções individuais que respondem, cada uma, a um determinado <abbr title="O tipo de evento que invoca o código da função, por exemplo, uma solicitação HTTP, uma mensagem da fila ou uma hora específica.">gatilho</abbr>. Todas as funções em um projeto compartilham as configurações locais e de hospedagem. Nesta seção, você cria um projeto de função que contém apenas uma função.

1. Em uma pasta vazia, execute o seguinte comando para gerar o projeto do Functions a partir de um [arquétipo Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html). 

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" 
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8"
    ```

    ---

    <br/>
    <details>
    <summary><strong>Para executar funções no Java 11</strong></summary>

    Use `-DjavaVersion=11` se desejar que as funções sejam executadas no Java 11. Para saber mais, confira [Versões Java](functions-reference-java.md#java-versions).
    </details>

1. O Maven solicita os valores necessários para concluir a geração do projeto na implantação.
    Forneça os seguintes valores quando solicitado:

    | Prompt | Valor | Descrição |
    | ------ | ----- | ----------- |
    | **groupId** | `com.fabrikam` | Um valor que identifica exclusivamente o projeto em todos os projetos, seguindo as [regras de nomenclatura do pacote](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) para Java. |
    | **artifactId** | `fabrikam-functions` | Um valor que é o nome do jar, sem um número de versão. |
    | **version** | `1.0-SNAPSHOT` | Escolha o valor padrão. |
    | **package** | `com.fabrikam` | Um valor que é o pacote Java para o código de função gerado. Use o padrão. |

1. Digite `Y` ou pressione Enter para confirmar.

    O Maven cria os arquivos de projeto em uma nova pasta com o nome _artifactId_, que, neste exemplo, é `fabrikam-functions`. 

1. Navegue até a pasta do projeto:

    ```console
    cd fabrikam-functions
    ```

<br/>
<details>
<summary><strong>O que é criado na pasta LocalFunctionProj?</strong></summary>

Essa pasta contém vários arquivos do projeto, como *Function.java*, *FunctionTest.java* e *pom.xml*. Também há arquivos de configuração chamados [local.settings.json](functions-run-local.md#local-settings-file) e [host.json](functions-host-json.md). Como *local.settings.json* pode conter segredos baixados do Azure, o arquivo é excluído do controle do código-fonte por padrão no arquivo *.gitignore*.
</details>

<br/>
<details>
<summary><strong>Código para Function.java</strong></summary>

O *Function.java* contém um método `run` que recebe dados de solicitação na variável `request`, que é uma [HttpRequestMessage](/java/api/com.microsoft.azure.functions.httprequestmessage) decorada com a anotação [HttpTrigger](/java/api/com.microsoft.azure.functions.annotation.httptrigger), que define o comportamento do gatilho. 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

A mensagem de resposta é gerada pela API [HttpResponseMessage.Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder).

O arquétipo também gera um teste de unidade para sua função. Ao alterar sua função para adicionar associações ou adicionar novas funções ao projeto, você também precisará modificar os testes no arquivo *FunctionTest.java*.
</details>

<br/>
<details>
<summary><strong>Código para pom.xml</strong></summary>

As configurações dos recursos do Azure criados para hospedar o aplicativo são definidas no elemento **configuration** do plug-in, com a **groupId** igual a `com.microsoft.azure` no arquivo *pom.xml* gerado. Por exemplo, o elemento de configuração abaixo instrui uma implantação baseada em Maven a criar um aplicativo de funções no grupo de recursos `java-functions-group` na região `westus` <abbr title="Uma referência geográfica a um datacenter específico do Azure no qual os recursos estão alocados.">region</abbr>. O próprio aplicativo de funções é executado no Windows hospedado no plano `java-functions-app-service-plan`, que, por padrão, é um plano de Consumo sem servidor.

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-107":::

Você pode alterar essas configurações para controlar como os recursos são criados no Azure, por exemplo, alterando `runtime.os` de `windows` para `linux` antes da implantação inicial. Para obter uma lista completa das configurações compatíveis com o plug-in do Maven, confira os [detalhes da configuração](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details).
</details>

<br>
<hr/>

## <a name="3-run-the-function-locally"></a>3. Executar a função localmente

1. **Execute a função** iniciando o host de runtime do Azure Functions local na pasta *LocalFunctionProj*:

    ```console
    mvn clean package
    mvn azure-functions:run
    ```

    Perto do fim da saída, devem aparecer as seguintes linhas:

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    ...

    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.

    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    </pre>

    Se HttpExample não aparece conforme mostrado acima, é provável que você tenha iniciado o host fora da pasta raiz do projeto. Nesse caso, use <kbd>Ctrl+C</kbd> para parar o host, navegue até a pasta raiz do projeto e execute o comando anterior novamente.

1. **Copie a URL** da função `HttpExample` dessa saída para um navegador e acrescente a cadeia de caracteres de consulta `?name=<YOUR_NAME>`, fazendo com que a URL completa seja `http://localhost:7071/api/HttpExample?name=Functions`. O navegador deve exibir uma mensagem como `Hello Functions`:

    ![Resultado da execução local da função no navegador](./media/functions-create-first-azure-function-azure-cli/function-test-local-browser.png)

    O terminal em que você iniciou seu projeto também mostra a saída do log conforme você faz solicitações.

1. Quando terminar, use <kbd>Ctrl+C</kbd> e escolha <kbd>y</kbd> para interromper o host de funções.

<br>
<hr/>

## <a name="4-deploy-the-function-project-to-azure"></a>4. Implantar o projeto de funções no Azure

Um aplicativo de funções e os recursos relacionados são criados no Azure quando você implanta o projeto de funções pela primeira vez. As configurações dos recursos do Azure criados para hospedar o aplicativo são definidas no arquivo *pom.xml*. Neste artigo, você aceitará os padrões.

<br/>
<details>
<summary><strong>Para criar um aplicativo de funções para execução no Linux</strong></summary>

Para criar um aplicativo de funções para execução no Linux em vez do Windows, altere o elemento `runtime.os` no arquivo *pom.xml* de `windows` para `linux`. A execução do Linux em um plano de consumo é compatível com [essas regiões](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions). Você não pode ter aplicativos que são executados no Linux e aplicativos que são executados no Windows no mesmo grupo de recursos.
</details>

1. Para fazer a implantação, entre na sua assinatura do Azure usando a CLI do Azure ou o Azure PowerShell. 

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
    ```azurecli
    az login
    ```

    O comando [az login](/cli/azure/reference-index#az_login) conecta você à conta do Azure.

    # <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    O cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) conecta você à conta do Azure.

    ---

1. Use o comando a seguir para implantar o projeto em um novo aplicativo de funções.

    ```console
    mvn azure-functions:deploy
    ```

    A implantação empacota os arquivos de projeto e implanta-os no novo aplicativo de funções usando [implantação zip](functions-deployment-technologies.md#zip-deploy). O código é executado no pacote de implantação no Azure.

<br/>
<details>
<summary><strong>O que é criado no Azure?</strong></summary>

+ Grupo de recursos. Nomeado como _java-functions-group_.
+ Conta de armazenamento. Necessária para o Functions. O nome é gerado aleatoriamente de acordo com os requisitos de nome da conta de armazenamento.
+ Plano de hospedagem. Hospedagem sem servidor para o aplicativo de funções na região _westus_. O nome é _java-functions-app-service-plan_.
+ Aplicativo de funções. Um aplicativo de funções é a unidade de implantação e execução para suas funções. O nome é gerado aleatoriamente com base no _artifactId_, anexado a um número gerado aleatoriamente.
</details>

<br>
<hr/>

## <a name="5-invoke-the-function-on-azure"></a>5. Invocar a função no Azure

Como a função usa um gatilho HTTP, você a **invoca fazendo uma solicitação HTTP para sua URL** no navegador ou usando uma ferramenta como <abbr title="Uma ferramenta de linha de comando para gerar solicitações HTTP para uma URL; confira https://curl.se/">curl</abbr>.

# <a name="browser"></a>[Navegador](#tab/browser)

Copie a **URL de Invocação** completa mostrada na saída do comando `publish` na barra de endereços de um navegador, acrescentando o parâmetro de consulta `&name=Functions`. O navegador deverá exibir uma saída semelhante à que foi exibida quando você executou a função localmente.

![A saída da função executada no Azure em um navegador](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

Execute [`curl`](https://curl.haxx.se/) com a **URL de Invocação**, acrescentando o parâmetro `&name=Functions`. A saída do comando deverá ser o texto, "Olá, Functions".

![A saída da função executada no Azure usando curl](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

<br>
<hr/>

## <a name="6-clean-up-resources"></a>6. Limpar os recursos

Se você prosseguir para a [próxima etapa](#next-steps) e adicionar uma saída da fila do <abbr title="No Armazenamento do Azure, uma forma de associar uma função a uma fila de armazenamento para que ela possa criar mensagens na fila.">Armazenamento do Azure,</abbr>mantenha todos os seus recursos no local para dar continuidade ao que já fez.

Caso contrário, use o comando a seguir para excluir o grupo de recursos e todos os recursos contidos nele para evitar custos adicionais.

 # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group delete --name java-functions-group
```

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name java-functions-group
```

---

<br>
<hr/>

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Conectar-se a uma fila do Armazenamento do Azure](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-java)
