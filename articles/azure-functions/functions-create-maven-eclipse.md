---
title: Criar um aplicativo de funções do Azure com Java e Eclipse
description: Guia prático para criar e publicar um simples aplicativo sem servidor disparado por HTTP usando Java e Eclipse para as Funções do Azure.
author: jeffhollan
ms.topic: how-to
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter, devx-track-java
ms.openlocfilehash: a4e240837dd3c95efc4dc83a7491f2ad9163435a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87373310"
---
# <a name="create-your-first-function-with-java-and-eclipse"></a>Criar sua primeira função com Java e Eclipse 

Este artigo mostra como criar um projeto de função [sem servidor](https://azure.microsoft.com/solutions/serverless/) com o Eclipse IDE e o Apache Maven, testá-lo e depurá-lo e implantá-lo nas Funções do Azure. 

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Configurar seu ambiente de desenvolvimento

Para desenvolver um aplicativo de funções com Java e Eclipse, você deve ter o seguinte instalado:

-  [Java Developer Kit](https://www.azul.com/downloads/zulu/), versão 8.
-  [Apache Maven](https://maven.apache.org), versão 3.0 ou posterior.
-  [Eclipse](https://www.eclipse.org/downloads/packages/), com suporte a Java e Maven.
-  [CLI do Azure](/cli/azure)

> [!IMPORTANT] 
> A variável de ambiente JAVA_HOME deve ser definida como o local de instalação do JDK para concluir este guia de início rápido.

É altamente recomendável também instalar as [Ferramentas Principais do Azure Functions, versão 2](functions-run-local.md#v2), que fornecem um ambiente local para executar e depurar as Funções do Azure. 

## <a name="create-a-functions-project"></a>Criar um projeto do Functions

1. No Eclipse, selecione o menu **arquivo** e, em seguida, selecione **novo- &gt; Maven projeto**. 
1. Aceite os padrões de **novo projeto Maven** de caixa de diálogo e selecione **próxima**.
1. Localize e selecione [Azure-Functions-arquétipo](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype) e clique em **Avançar**.
1. Certifique-se de preencher valores para todos os campos, incluindo `resourceGroup` , `appName` e `appRegion` (Use outro AppName diferente de **fabrikam-function-20170920120101928**) e, eventualmente, **concluir**.
    ![Create2 do eclipse Maven](media/functions-create-first-java-eclipse/functions-create-eclipse2.png)  

O Maven cria os arquivos de projeto em uma nova pasta com um nome de _artifactId_. O código gerado no projeto é uma função [disparada por HTTP](./functions-bindings-http-webhook.md) simples que ecoa o corpo da solicitação HTTP disparando.

## <a name="run-functions-locally-in-the-ide"></a>Executar funções localmente no IDE

> [!NOTE]
> [As Ferramentas Principais do Azure Functions, versão 2](functions-run-local.md#v2) devem ser instaladas para executar e depurar funções localmente.

1. Clique com botão direito no projeto gerado e, em seguida, escolha **executar como** e **build do Maven**.
1. Em **Editar configuração** caixa de diálogo, insira `package` no **metas** e **nome** campos, em seguida, selecione **executar**. Isso irá compilar e empacotar o código de função.
1. Quando a construção estiver concluída, crie outra configuração de execução, como acima, usando `azure-functions:run` como meta e nome. Selecione **Executar** para executar a função no IDE.

Encerrar o runtime na janela do console, quando você terminar de testar sua função. Apenas um host de função pode estar ativo e em execução localmente de cada vez.

### <a name="debug-the-function-in-eclipse"></a>Depurar a função no Eclipse

Em sua configuração de **Executar como** configurada na etapa anterior, altere `azure-functions:run` para `azure-functions:run -DenableDebug` e execute a configuração atualizada para iniciar o aplicativo de funções no modo de depuração.

Selecione o **executados** menu e open **configurações de depuração**. Escolher **aplicativo Java remoto** e crie um novo. Dê um nome à sua configuração e preencha as configurações. A porta deve ser consistente com a porta de depuração aberta pelo host de função, que por padrão é `5005`. Após a instalação, clique em `Debug` para iniciar a depuração.

![Depurar funções no Eclipse](media/functions-create-first-java-eclipse/debug-configuration-eclipse.PNG)

Defina pontos de interrupção e inspecione objetos em sua função usando o IDE. Quando terminar, pare o depurador e o host da função de execução. Apenas um host de função pode estar ativo e em execução localmente de cada vez.

## <a name="deploy-the-function-to-azure"></a>Implantar a função no Azure

O processo de implantação no Azure Functions usa credenciais de conta da CLI do Azure. [Faça logon com a CLI do Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) antes de continuar usando o prompt de comando do computador.

```azurecli
az login
```

Implante seu código em um novo aplicativo de funções usando o `azure-functions:deploy` meta Maven em uma nova **executar como** configuração.

Quando a implantação for concluída, você verá a URL que pode usar para acessar seu aplicativo de funções do Azure:

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

## <a name="next-steps"></a>Próximas etapas

- Examine o [Guia do desenvolvedor de funções Java](functions-reference-java.md) para saber mais sobre o desenvolvimento de funções Java.
- Adicione outras funções com gatilhos diferentes ao seu projeto usando o destino Maven `azure-functions:add`.
