---
title: Criar uma função Kotlin no Azure Functions usando IntelliJ
description: Saiba como usar o IntelliJ para criar uma função Kotlin simples disparada por HTTP, que você publica para executar em um ambiente sem servidor no Azure.
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.openlocfilehash: f02643ee28d76d4f90206a1aa2879b4672da2a38
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179435"
---
# <a name="create-your-first-kotlin-function-in-azure-using-intellij"></a>Criar sua primeira função Kotlin no Azure usando o IntelliJ

Este artigo mostra como criar uma função Java disparada por HTTP em um projeto IntelliJ IDEA, executar e depurar o projeto no IDE (ambiente de desenvolvimento integrado) e, por fim, implantar o projeto de função em um aplicativo de funções no Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Configurar seu ambiente de desenvolvimento

Para criar e publicar funções Kotlin no Azure usando o IntelliJ, instale o seguinte software:

- JDK ([Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support)), versão 8
- [Apache Maven](https://maven.apache.org), versão 3.0 ou superior
- [IntelliJ IDEA](https://www.jetbrains.com/idea/download), versões Community ou Ultimate com Maven
- [CLI do Azure](/cli/azure)
- [Versão 2.x](functions-run-local.md#v2) do Azure Functions Core Tools. Ele fornece um ambiente de desenvolvimento local para escrever, executar e depurar o Azure Functions.

> [!IMPORTANT]
> A variável de ambiente JAVA_HOME deve ser definida como o local de instalação do JDK para concluir as etapas neste artigo.

## <a name="create-a-function-project"></a>Criar um projeto de função

1. No IntelliJ IDEA, selecione **Criar Novo Projeto**.  
1. Na janela **Novo Projeto**, selecione **Maven** no painel esquerdo.
1. Marque a caixa de seleção **Criar com base no arquétipo** e, em seguida, selecione **Adicionar Arquétipo** para [azure-functions-kotlin-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype).
1. Na janela **Adicionar Arquétipo**, preencha os campos da seguinte maneira:
    - _GroupId_: com.microsoft.azure
    - _ArtifactId_: azure-functions-kotlin-archetype
    - _Versão_: use a versão mais recente do [repositório central](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype)
    ![Criar um projeto Maven do arquétipo no IntelliJ IDEA](media/functions-create-first-kotlin-intellij/functions-create-intellij.png)  
1. Selecione **OK** e, em seguida, selecione **Avançar**.
1. Insira os detalhes do projeto atual e selecione **Concluir**.

Maven cria os arquivos de projeto em uma nova pasta com o mesmo nome que o valor _ArtifactId_. O código gerado no projeto é uma função [Disparada por HTTP](./functions-bindings-http-webhook.md) simples que ecoa o corpo da solicitação HTTP que está sendo disparada.

## <a name="run-project-locally-in-the-ide"></a>Executar o projeto localmente no IDE

> [!NOTE]
> Para executar e depurar o projeto localmente, verifique se você instalou o [Azure Functions Core Tools, versão 2](functions-run-local.md#v2).

1. Importe as alterações manualmente ou habilite a [importação automática](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html).
1. Abra a barra de ferramentas **Projetos Maven**.
1. Expanda **Ciclo de Vida** e, em seguida, abra o **pacote**. A solução é compilada e empacotada em um diretório de destino criado recentemente.
1. Expanda **Plug-ins** > **azure-functions** e abra **azure-functions:run** para iniciar o runtime local do Azure Functions.  
  ![Barra de ferramentas Maven para Azure Functions](media/functions-create-first-kotlin-intellij/functions-intellij-kotlin-maven-toolbar.png)  

1. Feche a caixa de diálogo de execução quando terminar de testar a função. Apenas um host de função pode estar ativo e em execução localmente de cada vez.

## <a name="debug-the-project-in-intellij"></a>Depure o projeto no IntelliJ

1. Para iniciar o host de função no modo de depuração, adicione **-DenableDebug** como argumento quando ao executar sua função. Você pode alterar a configuração em [metas do maven](https://www.jetbrains.com/help/idea/maven-support.html#run_goal) ou execute o seguinte comando em uma janela de terminal:  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   Esse comando faz com que o host de função abra uma porta de depuração em 5005.

1. No menu **Executar**, selecione **Editar Configurações**.
1. Selecione **(+)** para adicionar um **Remoto**.
1. Preencha os campos _Nome_ e _Configurações_ e, em seguida, selecione **OK** para salvar a configuração.
1. Após a instalação, selecione **Depurar < Nome da Configuração Remota >** ou pressione Shift+F9 em seu teclado para iniciar a depuração.

   ![Depurar projeto no IntelliJ](media/functions-create-first-kotlin-intellij/debug-configuration-intellij.PNG)

1. Ao terminar, pare o depurador e o processo em execução. Apenas um host de função pode estar ativo e em execução localmente de cada vez.

## <a name="deploy-the-project-to-azure"></a>Implantar o projeto no Azure

1. Para implantar seu projeto em um aplicativo de funções no Azure, [faça logon usando a CLI do Azure](/cli/azure/authenticate-azure-cli).

   ``` azurecli
   az login
   ```

1. Implante seu código em um novo aplicativo de funções usando o destino Maven `azure-functions:deploy`. Você também pode selecionar a opção **azure-functions:deploy** na janela de projetos do Maven.

   ```
   mvn azure-functions:deploy
   ```

1. Encontre a URL para sua função de gatilho HTTP na saída da CLI do Azure depois que o aplicativo de funções tiver sido implantado com êxito.

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>Próximas etapas

Agora que você implantou seu primeiro aplicativo de funções Kotlin no Azure, leia o [Guia do desenvolvedor do Java do Azure Functions](functions-reference-java.md) para obter mais informações sobre o desenvolvimento de funções do Java e do Kotlin.
- Adicione outros aplicativos de funções com gatilhos diferentes ao seu projeto usando o destino do Maven `azure-functions:add`.
