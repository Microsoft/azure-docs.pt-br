---
title: Azure Stream Analytics trabalhos do Edge no Visual Studio
description: Este artigo descreve como criar e depurar o Stream Analytics em trabalhos IoT Edge usando as ferramentas do Stream Analytics para Visual Studio.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 09151ea0fe3d419401d576149f6655b8cdc09f8e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019951"
---
# <a name="develop-stream-analytics-edge-jobs-using-visual-studio-tools"></a>Desenvolver trabalhos de Borda do Stream Analytics utilizando ferramentas do Visual Studio

Neste tutorial, você aprenderá a usar as ferramentas de Stream Analytics para o Visual Studio. Você aprende a criar, a depurar e cria seus trabalhos do Stream Analytics Edge. Após criar e testar o trabalho, você poderá acessar o Portal do Azure para implantá-lo nos dispositivos. 

## <a name="prerequisites"></a>Pré-requisitos

Você precisará dos seguintes pré-requisitos para concluir este tutorial:

* Instale o [visual studio 2019](https://visualstudio.microsoft.com/downloads/), o [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/)ou o [Visual Studio 2013 Update 4](https://www.microsoft.com/download/details.aspx?id=45326). As edições Enterprise (Ultimate/Premium), Professional, Community têm suporte. Não há suporte para a edição Express.  

* Siga as [instruções de instalação](stream-analytics-tools-for-visual-studio-edge-jobs.md) para instalar o Stream Analytics Tools para Visual Studio.
 
## <a name="create-a-stream-analytics-edge-project"></a>Criar um projeto de Borda do Azure Stream Analytics 

No Visual Studio, selecione **arquivo**  >  **novo**  >  **projeto**. Navegue até a lista de **Modelos** à esquerda > expanda **Azure Stream Analytics** > **Borda do Stream Analytics** > **Aplicativo de Borda do Azure Stream Analytics**. Forneça um Nome, Local e nome da Solução para o projeto e selecione **OK**.

![Novo projeto do Edge Stream Analytics no Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-stream-analytics-edge-project.png)

Depois que o projeto for criado, navegue para o **Gerenciador de Soluções** para exibir a hierarquia de pastas.

![Modo de exibição de Gerenciador de soluções do trabalho do Edge do Stream Analytics](./media/stream-analytics-tools-for-visual-studio-edge-jobs/edge-project-in-solution-explorer.png)

 
## <a name="choose-the-correct-subscription"></a>Escolha a assinatura correta

1. No menu **Exibir** do Visual Studio, selecione **Gerenciador de Servidores**.  

2. Clique com o botão direito do mouse em **Azure** > selecione **Conectar-se à assinatura do Microsoft Azure** e, em seguida, entre com a conta do Azure.

## <a name="define-inputs"></a>Definir entradas

1. No **Gerenciador de Soluções**, expanda o nó de **Entradas** e você deverá ver uma entrada nomeada **EdgeInput.json**. Clique duas vezes para exibir as configurações.  

2. Definir o Tipo de Origem para **Transmissão de Dados**. Em seguida, defina a origem de **Hub Edge**, o formato de serialização de evento para **Json** e a codificação para **UTF8**. Opcionalmente, é possível renomear o **Alias de Entrada**, mas para este exemplo manteremos como está. Se for renomear o alias de entrada, utilize o nome especificado durante a definição da consulta. Clique em **Salvar** para salvar as configurações.  
   ![Configuração de entrada de trabalho do Stream Analytics](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-input-configuration.png)
 


## <a name="define-outputs"></a>Definir saídas

1. No **Gerenciador de Soluções**, expanda o nó de **Saídas** e você deverá ver uma saída nomeada **EdgeOutput.json**. Clique duas vezes para exibir as configurações.  

2. Certifique-se de definir o coletor para selecionar o **Hub do Edge**, definir o formato de serialização de evento como **JSON**, definir codificação como **UTF8** e definir a **matriz** de formato. Opcionalmente, é possível renomear o **Alias de Saída**, mas para este exemplo manteremos como está. Se for renomear o alias de saída, utilize o nome especificado durante a definição da consulta. Clique em **Salvar** para salvar as configurações. 
   ![Configuração de saída de trabalho do Stream Analytics](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>Definir a consulta de transformação

Stream Analytics trabalhos implantados no Stream Analytics IoT Edge ambientes dão suporte à maioria das [Stream Analytics referência de linguagem de consulta](/stream-analytics-query/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396). No entanto, as operações a seguir ainda não têm suporte para trabalhos do Stream Analytics Edge: 


|**Categoria**  | **Comando**  |
|---------|---------|
|Outros operadores | <ul><li>PARTITION BY</li><li>CARIMBO DE DATA/HORA POR MAIS DE</li><li>UDF do JavaScript</li><li>Agregações definidas pelo usuário (UDA)</li><li>GetMetadataPropertyValue</li><li>Usando mais de 14 agregações em uma única etapa</li></ul>   |

Quando você cria um trabalho de borda Stream Analytics no portal, o compilador avisará automaticamente se você não estiver usando um operador com suporte.

No Visual Studio, defina a consulta de transformação a seguir no editor de consultas (**script.asaql file**)

```sql
SELECT * INTO EdgeOutput
FROM EdgeInput 
```

## <a name="test-the-job-locally"></a>Testar o trabalho localmente

Para testar a consulta localmente, será necessário carregar os dados de exemplo. É possível obter dados de exemplo, baixando dados do Registro do [Repositório GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/Registration.json) e salve-o no computador local. 

1. Para fazer carregar os dados de exemplo, > clique com o botão direito do mouse no arquivo **EdgeInput.json** e escolha **Adicionar Entrada Local**  

2. Na janela pop-up > **Procure** os dados de exemplo do caminho local > Selecione **Salvar**.
   ![Configuração de entrada local no Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-local-input-configuration.png)
 
3. Um arquivo nomeado **local_EdgeInput.json** é adicionado automaticamente à pasta de entradas.  
4. Você pode executá-lo localmente ou enviar para o Azure. Para testar a consulta, selecione **executar localmente**.  
   ![Opções de execução no Visual Studio de trabalho do Azure Stream Analytics](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-visual-stuidio-run-options.png)
 
5. A janela do prompt de comando mostra o status do trabalho. Quando o trabalho executa com êxito, ele cria uma pasta semelhante a "2018-02-23-11-31-42" no caminho da pasta do projeto "Visual Studio 2015\Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42". Navegue até o caminho da pasta para exibir os resultados na pasta local:

   Também é possível entrar no Portal do Azure e verificar se o trabalho foi criado. 

   ![Pasta de resultados de trabalho do Azure Stream Analytics](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-job-result-folder.png)

## <a name="submit-the-job-to-azure"></a>Enviar o trabalho para o Azure

1. Antes de enviar o trabalho para o Azure, será necessário conectar-se à Assinatura do Azure. Abra **Gerenciador de Servidores** > clique com o botão direito em **Azure** > **Conectar-se à assinatura do Microsoft Azure** > entre na sua assinatura do Azure.  

2. Para enviar o trabalho para o Azure, navegue até o editor de consultas > selecione **Enviar para o Azure**.  

3. Uma janela pop-up é aberta. Escolha atualizar um trabalho de borda Stream Analytics existente ou crie um novo. Quando você atualizar um trabalho existente, ele substituirá toda a configuração do trabalho, neste cenário, você publicará um novo trabalho. Selecione **Criar um novo trabalho do Azure Stream Analytics** > insira um nome para o trabalho, semelhante a **MyASAEdgeJob** > escolha a **Assinatura** exigida, **Grupo de Recursos** e o **Local** > Selecione **Enviar**.

   ![Enviar trabalho do Stream Analytics para o Azure do Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-stream-analytics-job-to-azure.png)
 
   Agora, o trabalho do Stream Analytics Edge foi criado. Você pode consultar o [tutorial executar trabalhos no IOT Edge](stream-analytics-edge.md) para saber como implantá-lo em seus dispositivos. 

## <a name="manage-the-job"></a>Gerenciar o trabalho 

É possível exibir o status do trabalho e o diagrama do trabalho no Gerenciador de Servidores. Em **Stream Analytics** em **Gerenciador de servidores**, expanda a assinatura e o grupo de recursos em que você implantou o trabalho do Stream Analytics Edge. Você pode visualizar o MyASAEdgejob com o status **Criado**. Expanda o nó de trabalho e clique duas vezes nele para abrir a exibição de trabalho.

![Opções de gerenciamento de trabalho do gerenciador de servidores](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
A janela de exibição de trabalhos fornece operações como atualizar o trabalho, excluir o trabalho, abrir o trabalho no portal do Azure.

![Diagrama do trabalho e outras opções no Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>Próximas etapas

* [Mais informações sobre o Azure IoT Edge](../iot-edge/about-iot-edge.md)
* [ASA no tutorial de IoT Edge](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Enviar comentários para a equipe usando esta pesquisa](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u)