---
title: Desenvolver Azure Functions usando o Visual Studio | Microsoft Docs
description: Saiba como desenvolver e testar o Azure Functions usando as Ferramentas Azure Functions para o Visual Studio 2017.
services: functions
documentationcenter: .net
author: ggailey777
manager: cfowler
editor: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: glenga
ms.openlocfilehash: 5f0b346ec1c4a42d11ef74ab8ac8701dd45b00eb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-tools-for-visual-studio"></a>Ferramentas do Azure Functions para Visual Studio 2017  

As Ferramentas Azure Functions para Visual Studio de 2017 são uma extensão do Visual Studio que permite que você desenvolva, teste e implante funções do C# para o Azure. Se esta for sua primeira experiência com o Azure Functions, você pode aprender mais em [Uma introdução ao Azure Functions](functions-overview.md).

As Ferramentas do Azure Functions fornecem os seguintes benefícios: 

* Editar, criar e executar funções em seu computador de desenvolvimento local. 
* Publicar seu projeto do Azure Functions diretamente no Azure. 
* Usar atributos do WebJobs para declarar associações de função diretamente no código C# em vez de manter um function.json separado para definições de associação.
* Desenvolver e implantar funções de pré-compiladas C#. Funções pré-compiladas fornecem um desempenho de inicialização a frio melhor que funções baseadas em script C#. 
* Codificar suas funções em C# tendo todos os benefícios de desenvolvimento do Visual Studio. 

Este tópico mostra como usar as Ferramentas do Azure Functions para Visual Studio 2017 para desenvolver suas funções no C#. Você também aprenderá como publicar seu projeto no Azure como um assembly .NET.

## <a name="prerequisites"></a>Pré-requisitos

As Ferramentas do Azure Functions estão incluídas na carga de trabalho de desenvolvimento do Azure do [Visual Studio 2017 versão 15.3](https://www.visualstudio.com/vs/) ou uma versão posterior. Certifique-se de incluir a carga de trabalho de **desenvolvimento do Azure** na sua instalação do Visual Studio 2017 versão 15.3:

![Instalar o Visual Studio de 2017 com a carga de trabalho de desenvolvimento do Azure](./media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

>[!NOTE]  
>Depois de instalar ou fazer upgrade para o Visual Studio 2017 versão 15.3, você deve atualizar manualmente as ferramentas do Visual Studio 2017 para Azure Functions. Você pode atualizar as ferramentas no menu **Ferramentas**, em **Extensões e atualizações...** > **Atualizações** > **Visual Studio Marketplace** > **Ferramentas Azure Functions e Trabalhos da Web** > **Atualizar**. 

Para criar e implantar funções, você também precisa:

* Uma assinatura ativa do Azure. Se você ainda não tiver uma assinatura do Azure, há [contas gratuitas](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) disponíveis.

* Uma conta de armazenamento do Azure. Para criar uma conta de armazenamento, consulte [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account).  
## <a name="create-an-azure-functions-project"></a>Criar um projeto do Azure Functions 

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]


## <a name="configure-the-project-for-local-development"></a>Configurar seu projeto para desenvolvimento local

Quando você cria um novo projeto usando o modelo do Azure Functions, você obtém um projeto C# vazio que contém os seguintes arquivos:

* **host.json**: Permite que você configure o host do Functions. Essas configurações se aplicam para execução local e no Azure. Para obter mais informações, consulte a [referência para host.json](functions-host-json.md).
    
* **local.Settings.json**: Mantém as configurações usadas ao executar as funções localmente. Essas configurações não são usadas pelo Azure, elas são usadas pelas [Ferramentas Básicas do Azure Functions](functions-run-local.md). Use esse arquivo para especificar as configurações, como cadeias de conexão a outros serviços do Azure. Adicione uma nova chave na matriz **Valores** para cada conexão exigida pelas funções em seu projeto. Para obter mais informações, consulte [Arquivo de configurações locais](functions-run-local.md#local-settings-file) no tópico Ferramentas Básicas do Azure Functions.

O tempo de execução do Functions usa internamente uma conta de Armazenamento do Azure. Para todos os tipos de gatilhos diferentes de HTTP e webhooks, você deve definir a chave **Values.AzureWebJobsStorage** para uma cadeia de conexão de conta de Armazenamento do Azure válida.

[!INCLUDE [Note to not use local storage](../../includes/functions-local-settings-note.md)]

 Para definir a cadeia de conexão da conta de armazenamento:

1. No Visual Studio, abra **Cloud Explorer**, expanda **Conta de armazenamento** > **Sua conta de armazenamento**, selecione **Propriedades** e copie o valor da **Cadeia de conexão primária**.   

2. Em seu projeto, abra o arquivo de projeto local.settings.json e defina o valor da chave **AzureWebJobsStorage** na cadeia de conexão que você copiou.

3. Repita a etapa anterior para adicionar as chaves exclusivas para a matriz de **Valores** para todas as outras conexões necessárias para as suas funções.  

## <a name="create-a-function"></a>Criar uma função

Em funções pré-compiladas, as associações usadas pela função são definidas aplicando atributos no código. Quando você usar as Ferramentas do Azure Functions para criar suas funções a partir de modelos fornecidos, esses atributos são aplicados para você. 

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no nó do projeto e selecione **Adicionar** > **Novo Item**. Selecione **Azure Function**, digite um **Nome** para a classe e clique em **Adicionar**.

2. Escolha o gatilho, defina as propriedades de associação e clique em **Criar**. O exemplo a seguir mostra as configurações ao criar uma função disparada por armazenamento de filas. 

    ![](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)
    
    Uma chave de cadeia de conexão chamada **QueueStorage** é fornecida, que é definida no arquivo local.settings.json. 
 
3. Examine a classe recém-adicionada. Você verá um método estático **Run**, que é atribuído ao atributo **FunctionName**. Esse atributo indica que o método é o ponto de entrada para a função. 

    Por exemplo, a classe C# a seguir representa uma função básica disparada pelo armazenamento de filas:

    ````csharp
    using System;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;
    
    namespace FunctionApp1
    {
        public static class Function1
        {
            [FunctionName("QueueTriggerCSharp")]        
            public static void Run([QueueTrigger("myqueue-items", Connection = "QueueStorage")]string myQueueItem, TraceWriter log)
            {
                log.Info($"C# Queue trigger function processed: {myQueueItem}");
            }
        }
    } 
    ````
 
    Um atributo específico de associação é aplicado a cada parâmetro de associação fornecido ao método do ponto de entrada. O atributo utiliza as informações de associação como parâmetros. No exemplo anterior, o primeiro parâmetro possui um atributo **QueueTrigger** aplicado, que indica a função disparada por fila. O nome da fila e o nome de configuração da cadeia de conexão são passadas como parâmetros.  

## <a name="testing-functions"></a>Funções de teste

As Ferramentas Principais do Azure Functions permitem executar o projeto do Azure Functions no seu computador de desenvolvimento local. Você precisa instalar essas ferramentas na primeira vez em que inicia uma função no Visual Studio.  

Para testar sua função, pressione F5. Se solicitado, aceite a solicitação do Visual Studio para baixar e instalar as ferramentas principais (CLI) do Azure Functions.  Você também precisará habilitar a exceção de firewall de forma que as ferramentas possam lidar com solicitações HTTP.

Com o projeto em execução, você pode testar seu código da mesma forma que você testaria a função implantada. Para saber mais informações, consulte [Estratégias para testar seu código no Azure Functions](functions-test-a-function.md). Quando em execução no modo de depuração, os pontos de interrupção são atingidos no Visual Studio, conforme o esperado. 

Para obter um exemplo de como testar uma função disparada por fila, consulte o [tutorial de início rápido de função disparada por fila](functions-create-storage-queue-triggered-function.md#test-the-function).  

Para saber mais sobre o uso das Ferramentas Básicas do Azure Functions, consulte [Codificar e testar o Azure Functions localmente](functions-run-local.md).

## <a name="publish-to-azure"></a>Publicar no Azure

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

>[!NOTE]  
>As configurações que você adicionou no local.settings.json também devem ser adicionadas ao aplicativo de funções no Azure. Essas configurações não são adicionadas automaticamente. Você pode adicionar as configurações necessárias ao seu aplicativo de funções de uma dessas maneiras:
>
>* [Usando o Portal do Azure](functions-how-to-use-azure-function-app-settings.md#settings).
>* [Usando a opção de publicação `--publish-local-settings` nas ferramentas básicas do Azure Functions](functions-run-local.md#publish).
>* [Usando a CLI do Azure](/cli/azure/functionapp/config/appsettings#set). 

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre as Ferramentas do Azure Functions, consulte a seção de Perguntas Comuns da postagem de blog [Ferramentas do Visual Studio 2017 para o Azure Functions](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/).

Para saber mais sobre as Ferramentas Básicas do Azure Functions, consulte [Codificar e testar o Azure Functions localmente](functions-run-local.md).  
Para saber mais sobre como desenvolver funções como bibliotecas de classes do .NET, veja [Como usar bibliotecas de classes do .NET com o Azure Functions](functions-dotnet-class-library.md). Este tópico também fornece exemplos de como usar atributos para declarar os vários tipos de associações com suporte pelo Azure Functions.    
