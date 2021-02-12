---
title: Criar uma função Java no Azure Functions usando IntelliJ
description: Saiba como usar o IntelliJ para criar uma função Java simples disparada por HTTP, que você publica para executar em um ambiente sem servidor no Azure.
author: jeffhollan
ms.topic: how-to
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter, devx-track-java
ms.openlocfilehash: ed8948ddeddf25272355cd1dc06d4e95c52f7f62
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98035250"
---
# <a name="create-your-first-java-function-in-azure-using-intellij"></a>Criar sua primeira função Java no Azure usando IntelliJ

Este artigo mostra:
- Como criar uma função Java disparada por HTTP em um projeto IntelliJ IDEA.
- Etapas para testar e depurar o projeto no ambiente de desenvolvimento integrado (IDE) em seu próprio computador.
- Instruções para implantar o projeto de função para o Azure Functions

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Configurar seu ambiente de desenvolvimento

Para criar e publicar funções Java no Azure usando o IntelliJ, instale o seguinte software:

+ Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
+ Um [JDK (Kit de desenvolvimento Java) compatível com o Azure](/azure/developer/java/fundamentals/java-jdk-long-term-support) para Java 8
+ Um [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) Ultimate Edition ou Community Edition instalado
+ [Maven 3.5.0+](https://maven.apache.org/download.cgi)
+ Versão mais recente do [Functions Core Tools](https://github.com/Azure/azure-functions-core-tools)


## <a name="installation-and-sign-in"></a>Instalação e entrada

1. Na caixa de diálogo Configurações/Preferências do IntelliJ IDEA (Ctrl+Alt+S), selecione **Plug-ins**. Em seguida, localize o **Azure Toolkit for IntelliJ** no **Marketplace** e clique em **Instalar**. Depois de instalado, clique em **Reiniciar** para ativar o plug-in. 

    ![Plug-in do Azure Toolkit for IntelliJ no Marketplace][marketplace]

2. Para entrar sua conta do Azure, abra a barra lateral **Azure Explorer** e, em seguida, clique no ícone **Entrar no Azure** na barra na parte superior (ou no menu IDEA **Ferramentas/Azure/Entrada no Azure**).
    ![O comando de Entrada do IntelliJ no Azure][intellij-azure-login]

3. Na janela **Entrar no Azure**, selecione **Logon do Dispositivo** e, em seguida, clique em **Entrar** ([outras opções de entrada](/azure/developer/java/toolkit-for-intellij/sign-in-instructions)).

   ![A janela Entrar no Azure com o logon no dispositivo selecionado][intellij-azure-popup]

4. Clique em **Copiar e Abrir** na caixa de diálogo **Logon no Dispositivo do Azure**.

   ![A janela da caixa de diálogo Logon no Azure][intellij-azure-copycode]

5. No navegador, cole o código de dispositivo (que foi copiado quando você clicou em **Copiar e Abrir** na última etapa) e, em seguida, clique em **Avançar**.

   ![O navegador de logon do dispositivo][intellij-azure-link-ms-account]

6. Na caixa de diálogo **Selecionar Assinaturas**, selecione as assinaturas que deseja usar e, em seguida, clique em **OK**.

   ![A caixa de diálogo Selecionar Assinaturas][intellij-azure-login-select-subs]
   
## <a name="create-your-local-project"></a>Criar seu projeto local

Nesta seção, você usará o Azure Toolkit for IntelliJ para criar um projeto local do Azure Functions. Mais adiante neste artigo, você publicará o código de função no Azure. 

1. Abra a caixa de diálogo Bem-vindo do IntelliJ, selecione *Criar Projeto* para abrir um assistente de Novo projeto e selecione *Azure Functions*.

    ![Criar projeto de função](media/functions-create-first-java-intellij/create-functions-project.png)

1. Selecione *Gatilho Http*, clique em *Avançar* e siga o assistente para percorrer todas as configurações nas páginas a seguir, depois, confirme a localização do projeto e clique em *Concluir*. O Intellj IDEA abrirá o novo projeto.

    ![Término da criação do projeto de função](media/functions-create-first-java-intellij/create-functions-project-finish.png)

## <a name="run-the-project-locally"></a>Executar o projeto localmente

1. Navegue até `src/main/java/org/example/functions/HttpTriggerFunction.java` para ver o código gerado. Ao lado da linha *17*, você observará a presença de um botão *Executar* verde. Clique nele e selecione *Executar 'azure-function-exam...'* , você verá que seu aplicativo de funções está sendo executado localmente com alguns logs.

    ![Projeto de execução local](media/functions-create-first-java-intellij/local-run-functions-project.png)

    ![Saída do projeto de execução local](media/functions-create-first-java-intellij/local-run-functions-output.png)

1. Você pode experimentar a função usando o navegador para acessar o ponto de extremidade impresso, como `http://localhost:7071/api/HttpTrigger-Java?name=Azure`.

    ![Resultado do teste da função de execução local](media/functions-create-first-java-intellij/local-run-functions-test.png)

1. O log também é impresso em sua ideia, agora, pare o aplicativo de funções clicando no botão *parar* .

    ![Log de teste da função de execução local](media/functions-create-first-java-intellij/local-run-functions-log.png)

## <a name="debug-the-project-locally"></a>Depurar o projeto localmente

1. Para depurar o código de função em seu projeto localmente, selecione o botão *depurar* na barra de ferramentas. Se você não vir a barra de ferramentas, habilite-a escolhendo **Exibir**  >  **aparência**  >  **da barra de ferramentas**.

    ![Botão do aplicativo de função de depuração local](media/functions-create-first-java-intellij/local-debug-functions-button.png)

1. Clique na linha *20* do arquivo `src/main/java/org/example/functions/HttpTriggerFunction.java` para adicionar um ponto de interrupção e acesse o ponto de extremidade `http://localhost:7071/api/HttpTrigger-Java?name=Azure` novamente. Você verá que o ponto de interrupção foi atingido e poderá tentar mais recursos de depuração, como *step*, *watch* e *evaluation*. Interrompa a sessão de depuração clicando no botão parar.

    ![Interrupção do aplicativo de função de depuração local](media/functions-create-first-java-intellij/local-debug-functions-break.png)

## <a name="deploy-your-project-to-azure"></a>Implantar seu projeto no Azure

1. Clique com o botão direito do mouse no seu projeto no Explorador de Projeto do IntelliJ e selecione *Azure -> Implantar no Azure Functions*

    ![Implantar projeto no Azure](media/functions-create-first-java-intellij/deploy-functions-to-azure.png)

1. Se você ainda não tiver nenhum aplicativo de funções, clique em *Nenhuma função disponível, clique para criar uma*.

    ![Criar aplicativo de funções no Azure](media/functions-create-first-java-intellij/deploy-functions-create-app.png)

1. Digite o nome do aplicativo de funções e escolha o plano adequado de assinatura/plataforma/grupo de recursos/serviço de aplicativo. você também pode criar um grupo de recursos/plano do serviço de aplicativo aqui. Em seguida, mantenha as configurações do aplicativo inalteradas, clique em *OK* e aguarde alguns minutos para que o novo aplicativo de funções seja criado. Após a mensagem *Criando Aplicativo de Funções...* , a barra de progresso desaparece.

    ![Implantar o aplicativo de funções no assistente de criação de aplicativo do Azure](media/functions-create-first-java-intellij/deploy-functions-create-app-wizard.png)

1. Selecione o aplicativo de funções para o qual você deseja implantar (o novo aplicativo de funções que você acabou de criar será selecionado automaticamente). Clique em *Executar* para implantar as funções.

    ![Captura de tela mostra a caixa de diálogo implantar Azure Functions.](media/functions-create-first-java-intellij/deploy-functions-run.png)

    ![Implantar o aplicativo de funções no log do Azure](media/functions-create-first-java-intellij/deploy-functions-log.png)

## <a name="manage-function-apps-from-idea"></a>Gerenciar aplicativos de funções da ideia

1. Você pode gerenciar seus aplicativos de funções com o *Azure Explorer* em sua ideia, clicar em *aplicativo de funções*, verá todos os seus aplicativos de funções aqui.

    ![Exibir aplicativos de funções no Explorer](media/functions-create-first-java-intellij/explorer-view-functions.png)

1. Clique para selecionar em um dos seus aplicativos de funções e clique com o botão direito do mouse, selecione *mostrar propriedades* para abrir a página de detalhes. 

    ![Mostrar propriedades do aplicativo de funções](media/functions-create-first-java-intellij/explorer-functions-show-properties.png)

1. Clique com o botão direito do mouse em seu aplicativo *de funções HttpTrigger-Java* e selecione *função de gatilho*. você verá que o navegador está aberto com a URL do gatilho.

    ![Captura de tela mostra um navegador com o U R L.](media/functions-create-first-java-intellij/explorer-trigger-functions.png)

## <a name="add-more-functions-to-the-project"></a>Adicionar mais funções ao projeto

1. Clique com o botão direito do mouse no pacote *org.example.functions* e selecione *Nova -> Classe de Função do Azure*. 

    ![Adicionar funções à entrada do projeto](media/functions-create-first-java-intellij/add-functions-entry.png)

1. Preencha o nome de classe *HttpTest*, selecione *HttpTrigger* no assistente para criar classe de função e clique em *OK* para criar. Dessa forma, você pode criar funções como desejar.

    ![Captura de tela mostra a caixa de diálogo Criar classe de função.](media/functions-create-first-java-intellij/add-functions-trigger.png)
    
    ![Adicionar funções à saída do projeto](media/functions-create-first-java-intellij/add-functions-output.png)

## <a name="cleaning-up-functions"></a>Limpando funções

1. Excluindo funções no Azure Explorer
      
      ![A captura de tela mostra excluir selecionado em um menu de contexto.](media/functions-create-first-java-intellij/delete-function.png)
      

## <a name="next-steps"></a>Próximas etapas

Você criou um projeto Java com uma função disparada por HTTP, executa-o no computador local e o implantou no Azure. Agora, estenda sua função por meio da...

> [!div class="nextstepaction"]
> [Adição de uma associação de saída de fila do Armazenamento do Azure](./functions-add-output-binding-storage-queue-java.md)


[marketplace]:./media/functions-create-first-java-intellij/marketplace.png
[intellij-azure-login]: media/functions-create-first-java-intellij/intellij-azure-login.png
[intellij-azure-popup]: media/functions-create-first-java-intellij/intellij-azure-login-popup.png
[intellij-azure-copycode]: media/functions-create-first-java-intellij/intellij-azure-login-copyopen.png
[intellij-azure-link-ms-account]: media/functions-create-first-java-intellij/intellij-azure-login-linkms-account.png
[intellij-azure-login-select-subs]: media/functions-create-first-java-intellij/intellij-azure-login-selectsubs.png
