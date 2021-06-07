---
title: 'Início Rápido: Criar sua primeira função no Azure usando o Visual Studio'
description: Neste início rápido, você aprenderá a criar e publicar uma Função do Azure de gatilho HTTP usando o Visual Studio.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 09/30/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, 23113853-34f2-4f
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./functions-create-your-first-function-visual-studio-uiex
ms.openlocfilehash: d691ee60f624f75c89e44e905e5343bf9c71fd4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101701376"
---
# <a name="quickstart-create-your-first-function-in-azure-using-visual-studio"></a>Início Rápido: Criar sua primeira função no Azure usando o Visual Studio

Neste artigo, você usará o Visual Studio para criar uma função baseada em biblioteca de classes C# que responde a solicitações HTTP. Após testar o código localmente, implante-o no ambiente sem servidor do Azure Functions.  

A realização deste início rápido gera um pequeno custo de alguns centavos de dólar ou menos em sua conta do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, instale primeiro o [Visual Studio 2019](https://azure.microsoft.com/downloads/). Selecione a carga de trabalho de **desenvolvimento do Azure** durante a instalação. Se você quiser criar um projeto do Azure Functions usando o Visual Studio 2017, primeiro deve instalar as [ferramentas mais recentes do Azure Functions](functions-develop-vs.md#check-your-tools-version).

![Instalar o Visual Studio com a carga de trabalho de desenvolvimento do Azure](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

Se você não tiver uma [assinatura do Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), crie uma [conta gratuita](https://azure.microsoft.com/free/dotnet/) antes de começar.

## <a name="create-a-function-app-project"></a>Crie um projeto de aplicativo de funções

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

O Visual Studio cria um projeto e uma classe que contém o código clichê do tipo de função do gatilho HTTP. O código padronizado envia uma resposta HTTP que inclui um valor do corpo de solicitação ou da cadeia de consulta. O atributo `HttpTrigger` especifica que a função é disparada por uma solicitação HTTP. 

## <a name="rename-the-function"></a>Renomear a função

O atributo do método `FunctionName` define o nome da função, que por padrão é gerado como `Function1`. Como as ferramentas não permitem que você substitua o nome padrão da função quando cria seu projeto, dedique um minuto a criar um nome melhor para a classe da função, o arquivo e os metadados.

1. No **Explorador de Arquivos**, clique com o botão direito do mouse no arquivo Function1.cs e renomeie-o como `HttpExample.cs`.

1. No código, renomeie a classe Function1 como 'HttpExample'.

1. No método `HttpTrigger` chamado `Run`, renomeie o atributo do método `FunctionName` como `HttpExample`.

Agora que renomeou a função, você pode testá-la no computador local.

## <a name="run-the-function-locally"></a>Executar a função localmente

O Visual Studio integra-se ao Azure Functions Core Tools para que você possa testar suas funções localmente usando o runtime completo do Azure Functions.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Após verificar se a função foi executada corretamente no computador local, é hora de publicar o projeto no Azure.

## <a name="publish-the-project-to-azure"></a>Publicar o projeto no Azure

Antes de publicar o projeto, você deve ter um aplicativo de funções em sua assinatura do Azure. A publicação do Visual Studio cria um aplicativo de funções para você na primeira vez que você publica seu projeto.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Testar sua função no Azure

1. No Cloud Explorer, seu novo aplicativo de funções deve estar selecionado. Caso contrário, expanda sua assinatura > **Serviços de Aplicativos** e selecione seu novo aplicativo de funções.

1. Clique com o botão direito do mouse no aplicativo de funções e escolha **Abrir no navegador**. Isso abre a raiz do aplicativo de funções no navegador da Web padrão e exibe a página que indica que o aplicativo de funções está em execução. 

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/function-app-running-azure.png" alt-text="Aplicativo de funções em execução":::

1. Na barra de endereços no navegador, acrescente a cadeia de caracteres `/api/HttpExample?name=Functions` à URL base e execute a solicitação.

    A URL que chama a função de gatilho HTTP está no seguinte formato:

    `http://<APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions`

2. Vá para essa URL e você verá uma resposta no navegador à solicitação GET remota retornada pela função, que é semelhante ao seguinte exemplo:

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png" alt-text="Resposta da função no navegador":::

## <a name="clean-up-resources"></a>Limpar os recursos

Outros inícios rápidos nessa coleção aproveitam esse início rápido. Se você planeja trabalhar com guias de início rápido subsequentes, tutoriais ou com qualquer serviço criado por você nesse início rápido, não limpe os recursos.

Os *Recursos* no Azure se referem a aplicativos de funções, funções, contas de armazenamento e assim por diante. Eles são agrupados em *grupos de recursos* e você pode excluir tudo junto ao excluir o grupo. 

Você criou recursos para concluir esses guias de início rápido. Você pode ser cobrado por esses recursos, dependendo do [status de conta](https://azure.microsoft.com/account/) e [preços do serviço](https://azure.microsoft.com/pricing/). Caso não precise mais dos recursos, é possível excluí-los desta maneira:

1. No Cloud Explorer, expanda sua assinatura > **Serviços de Aplicativos**, clique com o botão direito do mouse no aplicativo de funções e escolha **Abrir no Portal**. 

1. Na página do aplicativo de funções, selecione a guia **Visão geral** e depois selecione o link em **Grupo de recursos**.

   :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-app-delete-resource-group.png" alt-text="Selecione o grupo de recursos a ser excluído da página do aplicativo de funções":::

2. Na página **Grupo de recursos**, examine a lista de recursos incluídos e verifique se eles são aqueles que deseja excluir.
 
3. Selecione **Excluir grupo de recursos** e siga as instruções.

   A exclusão poderá levar alguns minutos. Ao ser concluída, uma notificação será exibida por alguns segundos. Também é possível selecionar o ícone de sino na parte superior da página para exibir a notificação.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você usou o Visual Studio para criar e publicar um aplicativo de funções em C# no Azure com uma função de gatilho HTTP simples. 

Avance para o próximo artigo para saber como adicionar uma associação de fila do Armazenamento do Azure à sua função:
> [!div class="nextstepaction"]
> [Adicionar uma associação de fila do Armazenamento do Azure à sua função](functions-add-output-binding-storage-queue-vs.md)

