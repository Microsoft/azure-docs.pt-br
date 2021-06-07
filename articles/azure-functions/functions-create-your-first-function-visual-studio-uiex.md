---
title: 'Início Rápido: Criar sua primeira função no Azure usando o Visual Studio'
description: Neste início rápido, você aprenderá a criar e publicar uma Função do Azure de gatilho HTTP usando o Visual Studio.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 09/30/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, 23113853-34f2-4f
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 9e3144738bd259ab9be75059af00f125581bb37c
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "102050113"
---
# <a name="quickstart-create-your-first-function-in-azure-using-visual-studio"></a>Início Rápido: Criar sua primeira função no Azure usando o Visual Studio

Neste artigo, você usará o Visual Studio para criar uma função baseada em biblioteca de classes C# que responde a solicitações HTTP. Após testar o código localmente, você o implanta no <abbr title="Um ambiente de computação de runtime no qual todos os detalhes do servidor são transparentes para os desenvolvedores de aplicativos, o que simplifica o processo de implantação e gerenciamento de código.">sem servidor</abbr> ambiente do <abbr title="Um serviço do Azure que fornece um ambiente de computação sem servidor de baixo custo para os aplicativos.">Funções do Azure</abbr>.

A realização deste início rápido gera um pequeno custo de alguns centavos de dólar ou menos em sua <abbr title="O perfil que mantém as informações de cobrança pelo uso do Azure.">Conta do Azure</abbr>.

## <a name="1-prepare-your-environment"></a>1. Prepare o seu ambiente

+ Crie uma conta do Azure <abbr title="O perfil que mantém as informações de cobrança pelo uso do Azure.">account</abbr> com uma assinatura ativa <abbr title="A estrutura organizacional básica na qual você gerencia recursos no Azure, normalmente associada a um indivíduo ou departamento da organização.">subscription</abbr>. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ Instale o [Visual Studio 2019](https://azure.microsoft.com/downloads/) e selecione a carga de trabalho de **Desenvolvimento do Azure** durante a instalação. 

![Instalar o Visual Studio com a carga de trabalho de desenvolvimento do Azure](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

<br/>
<details>
<summary><strong>Usar um projeto do Azure Functions</strong></summary>
Se você quiser criar um <abbr title="Um contêiner lógico para uma ou mais funções individuais que podem ser implantadas e gerenciadas juntas.">projeto local do Azure Functions</abbr> usando o Visual Studio 2017, precisará primeiro instalar as [ferramentas mais recentes do Azure Functions](functions-develop-vs.md#check-your-tools-version).
</details>

## <a name="2-create-a-function-app-project"></a>2. Crie um projeto de aplicativo de funções

1. No menu do Visual Studio, selecione **Arquivo** > **Novo** > **Projeto**.

1. Em **Criar um projeto**, insira *funções* na caixa de pesquisa, escolha o modelo **Azure Functions** e, em seguida, selecione **Próximo**.

1. Em **Configurar o novo projeto**, insira um **<abbr title="O nome do aplicativo de funções deve ser válido como um namespace de C#, portanto, não use sublinhados, hifens nem qualquer outro caractere não alfanumérico.">Nome do projeto</abbr>** para o projeto e selecione **Criar**. 

1. Forneça as seguintes informações para as configurações de **Criar um aplicativo do Azure Functions**:

    + Selecione **<abbr title=" Esse valor cria um projeto de função que usa o runtime versão 3.x do Azure Functions, compatível com o .NET Core 3.x. O Azure Functions 1.x dá suporte ao .NET Framework.">Azure Functions v3 (.NET Core)</abbr>** na lista suspensa de runtimes do Functions. (Para saber mais, confira [Visão geral das versões de runtime do Azure Functions](functions-versions.md).)
    
    + Selecione **<abbr title="Este valor cria uma função disparada por uma solicitação HTTP.">Gatilho HTTP</abbr>** como o modelo de função.
    
    + Selecione **<abbr title="Como uma Função do Azure requer uma conta de armazenamento, uma será atribuída ou criada quando você publicar seu projeto no Azure. Um gatilho HTTP não usa uma cadeia de conexão da conta do Armazenamento do Azure; todos os outros tipos de gatilho exigem uma cadeia de conexão válida da conta do Armazenamento do Azure.">Emulador de armazenamento</abbr>** na lista suspensa da Conta de armazenamento.
        
    + Selecione **Anônimo** no <abbr title="A função criada pode ser acionada por qualquer cliente sem fornecer uma chave. Essa configuração de autorização torna fácil testar a nova função.">Nível de autorização</abbr> . (Para saber mais sobre chaves e autorização, confira [Chaves de autorização](functions-bindings-http-webhook-trigger.md#authorization-keys) e [Associações HTTP e webhook](functions-bindings-http-webhook.md).)

    + Escolha **Criar**
        
## <a name="3-rename-the-function"></a>3. Renomear a função

O atributo do método `FunctionName` define o nome da função, que por padrão é gerado como `Function1`. Como as ferramentas não permitem que você substitua o nome padrão da função quando cria seu projeto, dedique um minuto a criar um nome melhor para a classe da função, o arquivo e os metadados.

1. No **Explorador de Arquivos**, clique com o botão direito do mouse no arquivo Function1.cs e renomeie-o como *HttpExample.cs*.

1. No código, renomeie a classe Function1 como 'HttpExample'.

1. No método `HttpTrigger` chamado `Run`, renomeie o atributo do método `FunctionName` como `HttpExample`.


## <a name="4-run-the-function-locally"></a>4. Executar a função localmente

1. Para executar sua função, pressione <kbd>F5</kbd> no Visual Studio.

1. Copie a URL da sua função da saída do Azure Functions runtime.

    ![runtime local do Azure](../../includes/media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

1. Cole a URL para a solicitação HTTP na barra de endereços do navegador. Acrescente a cadeia de caracteres de consulta **?name=<YOUR_NAME>** a essa URL e execute a solicitação. 

    ![Resposta da função localhost no navegador](../../includes/media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

1. Pare a depuração pressionando <kbd>Shift</kbd>+<kbd>F5</kbd> no Visual Studio.

<br/>
<details>
<summary><strong>Solução de problemas</strong></summary>
 Talvez seja necessário habilitar uma exceção de firewall para que as ferramentas possam manipular solicitações HTTP. Os níveis de autorização nunca são impostos quando você executa uma função localmente.
</details>

## <a name="5-publish-the-project-to-azure"></a>5. Publicar o projeto no Azure

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no nome do projeto e selecione **Publicar**.

1. Em **Destino**, selecione **Azure**

    :::image type="content" source="../../includes/media/functions-vstools-publish/functions-visual-studio-publish-profile-step-1.png" alt-text="Selecionar destino do Azure":::

1. Em **Destino específico**, selecione **Aplicativo de Funções do Azure (Windows)**

    :::image type="content" source="../../includes/media/functions-vstools-publish/functions-visual-studio-publish-profile-step-2.png" alt-text="Selecionar o Aplicativo de Funções do Azure":::

1. Em **Instância de Função**, selecione **Criar uma Função do Azure...** e use os seguintes valores especificados:

    + Para **Nome**, forneça um <abbr title="Use um nome que identifique o novo aplicativo de funções de maneira exclusiva. Aceite esse nome ou insira um novo nome. Os caracteres válidos são `a-z`, `0-9` e `-`.">Nome globalmente exclusivo</abbr>
    
    + **Selecione** uma assinatura na lista suspensa.
    
    + **Selecione** um <abbr title="Um contêiner lógico para recursos do Azure relacionados que você pode gerenciar como uma unidade.">grupo de recursos</abbr> existente na lista suspensa ou escolha **Novo** para criar um grupo de recursos.
    
    + **Selecionar** <abbr title="Quando você publica seu projeto em um aplicativo de funções executado em um Plano de consumo, você paga apenas pelas execuções do seu aplicativo de funções. Outros planos de hospedagem incorrem em custos mais altos.">Consumo</abbr> na lista suspensa Tipo de reprodução. (Para saber mais, confira [Plano de consumo](consumption-plan.md).)
    
    + **Selecione** um  <abbr title="Uma referência geográfica a um datacenter específico do Azure no qual os recursos estão alocados. Confira em [regiões](https://azure.microsoft.com/regions/) uma lista de regiões disponíveis.">local</abbr> na lista suspensa.
    
    + **Selecione** uma <abbr="Uma conta do Armazenamento do Azure é exigida pelo runtime do Functions. Selecione Novo para configurar uma conta de armazenamento para uso geral. Você também pode escolher uma conta existente que atenda aos requisitos da conta de armazenamento".>conta do Armazenamento do Azure</abbr> na lista suspensa

    ![Criar caixa de diálogo do Serviço de Aplicativo](../../includes/media/functions-vstools-publish/functions-visual-studio-publish.png)

1. Escolha **Criar** 

1. Na **Instância do Functions**, verifique se **Executar no arquivo de pacote** está marcado. 

    :::image type="content" source="../../includes/media/functions-vstools-publish/functions-visual-studio-publish-profile-step-4.png" alt-text="Concluir a criação do perfil":::

    <br/>
    <details>
    <summary><strong>O que essa configuração faz?</strong></summary>
    Ao usar **Executar do arquivo de pacote**, seu aplicativo de funções é implantado usando a [Implantação de Zip](functions-deployment-technologies.md#zip-deploy) com o modo [Run-From-Package](run-functions-from-deployment-package.md) habilitado. Esse é o método de implantação recomendado para o seu projeto do Functions, pois ele resulta em um melhor desempenho.    
    </details>   

1. Selecione **Concluir**.

1. Na página Publicar, selecione **Publicar**.

1. Na página Publicar, examine a URL raiz do aplicativo de funções.

1. Na guia Publicar, escolha **Gerenciar no <abbr title="O Cloud Explorer permite que você use o Visual Studio para exibir o conteúdo do site, iniciar e parar o aplicativo de funções e navegar diretamente para recursos do aplicativo de funções no Azure e no portal do Azure.">Cloud Explorer</abbr>** .
    
    :::image type="content" source="../../includes/media/functions-vstools-publish/functions-visual-studio-publish-complete.png" alt-text="Publicar mensagem de êxito":::
    

## <a name="6-test-your-function-in-azure"></a>6. Testar sua função no Azure

1. No Cloud Explorer, seu novo aplicativo de funções deve estar selecionado. Caso contrário, expanda sua assinatura, expanda **Serviços de Aplicativos** e selecione seu novo aplicativo de funções.

1. Clique com o botão direito do mouse no aplicativo de funções e escolha **Abrir no navegador**. Isso abre a raiz do aplicativo de funções no navegador da Web padrão e exibe a página que indica que o aplicativo de funções está em execução. 

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/function-app-running-azure.png" alt-text="Aplicativo de funções em execução":::

1. Na barra de endereços no navegador, acrescente a cadeia de caracteres **/api/HttpExample?name=Functions** à URL base e execute a solicitação.

    A URL que chama a função de gatilho HTTP está no seguinte formato:

    `http://<APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions`

2. Vá para essa URL e você verá uma resposta no navegador à solicitação GET remota retornada pela função, que é semelhante ao seguinte exemplo:

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png" alt-text="Resposta da função no navegador":::

## <a name="7-clean-up-resources"></a>7. Limpar os recursos

Exclua o aplicativo de funções e seus recursos para evitar incorrer em mais custos.

1. No Cloud Explorer, expanda sua assinatura, expanda **Serviços de Aplicativos**, clique com o botão direito do mouse no aplicativo de funções e escolha **Abrir no Portal**. 

1. Na página do aplicativo de funções, selecione a guia **Visão geral** e depois selecione o link em **Grupo de recursos**.

   :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-app-delete-resource-group.png" alt-text="Selecione o grupo de recursos a ser excluído da página do aplicativo de funções":::

1. Na página **Grupo de recursos**, examine a lista de recursos incluídos e verifique se eles são aqueles que deseja excluir.
 
1. Selecione **Excluir grupo de recursos** e siga as instruções.

    A exclusão poderá levar alguns minutos. Ao ser concluída, uma notificação será exibida por alguns segundos. Também é possível selecionar o ícone de sino na parte superior da página para exibir a notificação.

## <a name="next-steps"></a>Próximas etapas

Prossiga para o próximo artigo para saber como adicionar um <abbr title="Uma forma de associar uma função a uma fila de armazenamento para que ela possa criar mensagens na fila. Associações são conexões declarativas entre uma função e outros recursos. Uma associação de entrada fornece dados à função; uma associação de saída fornece dados da função para outros recursos."> Associação de saída de fila do Armazenamento do Azure</abbr> para sua função:

> [!div class="nextstepaction"]
> [Adicionar uma associação de fila do Armazenamento do Azure à sua função](functions-add-output-binding-storage-queue-vs.md)
