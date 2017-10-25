---
title: Usar ferramentas do Azure Stream Analytics para Visual Studio | Microsoft Docs
description: "Tutorial de introdução para as Ferramentas do Stream Analytics do Azure para Visual Studio"
keywords: visual studio
documentationcenter: 
services: stream-analytics
author: su-jie
manager: jhubbard
editor: cgronlun
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: sujie
ms.openlocfilehash: b0304265dd43986a59515e8d68630a76d0364748
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-stream-analytics-tools-for-visual-studio"></a>Usar ferramentas do Azure Stream Analytics para Visual Studio
As Ferramentas do Stream Analytics do Azure para Visual Studio agora estão disponíveis para o público geral. Essas ferramentas permitem uma experiência mais avançada para os usuários do Stream Analytics solucionarem problemas, bem como escreverem consultas complexas e até mesmo escreverem consultas localmente. Você também terá a capacidade de exportar um trabalho do Stream Analytics em um projeto do Visual Studio.

## <a name="introduction"></a>Introdução
Neste tutorial, você aprenderá como usar as Ferramentas do Stream Analytics do Azure para Visual Studio para criar, testar localmente, gerenciar e depurar os trabalhos do Stream Analytics do Azure. 

Depois de concluir este tutorial, você poderá:
* Familiarizar-se com as Ferramentas do Stream Analytics do Azure para Visual Studio.
* Configurar e implantar um trabalho do Stream Analytics.
* Testar seu trabalho local com os dados de exemplo locais.
* Usar o monitoramento para solucionar problemas.
* Exportar os trabalhos existentes para projetos.

## <a name="prerequisites"></a>Pré-requisitos
Você precisará dos seguintes pré-requisitos para concluir este tutorial:
* Concluir as etapas anteriores a **Criar um trabalho de Stream Analytics** do [tutorial Criar uma solução de IoT usando o Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics). 
* Instalar o Visual Studio 2017, Visual Studio 2015, Visual Studio 2013 atualização 4. As edições Enterprise (Ultimate/Premium), Professional, Community têm suporte; não há suporte para a edição Express. 
* Siga as [instruções de instalação](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install) para instalar o Stream Analytics Tools para o Visual Studio.

## <a name="create-a-stream-analytics-project"></a>Criar um projeto do Stream Analytics
No Visual Studio, clique no **Menu Arquivo** e escolha **Novo Projeto**. Escolha **Stream Analytics** na lista de modelos à esquerda e clique em **Aplicativo do Stream Analytics do Azure**.
Insira o Nome do Projeto, Localização e Nome da solução na parte inferior como faria para outros projetos.

![Criar um projeto do Stream Analytics do Azure](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-01.png)

Você verá um projeto **Toll** gerado no **Gerenciador de Soluções**.

![Criar um projeto do Stream Analytics do Azure](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-02.png)

## <a name="choose-the-correct-subscription"></a>Escolha a assinatura correta
1. Abra o **Gerenciador de Servidores** no Visual Studio pelo menu **Exibir**.
2. Faça logon com sua Conta do Azure. 

## <a name="define-input-sources"></a>Definir fontes de entrada
1.  Em **Gerenciador de Soluções**, expanda o nó **Entradas** e renomeie **Input.json** para **EntryStream.json**. Clique duas vezes em **EntryStream.json**.
2.  O **ALIAS DE ENTRADA** agora deve ser **EntryStream**. Observe que o alias de entrada é o que será usado no script de consulta. 
3.  O Tipo de Origem é **Fluxo de Dados**.
4.  A Fonte é **Hub de eventos**.
5.  O Namespace do barramento de serviço deve ser o **TollData** no menu suspenso.
6.  O Nome do hub de eventos deve ser definido como **entrada**.
7.  O nome da política do hub de eventos é **RootManageSharedAccessKey** (o valor padrão).
8.  Selecione **JSON** como **FORMATO DE SERIALIZAÇÃO DO EVENTO** e **UTF8** como **CODIFICAÇÃO**.
   
   As configurações têm a seguinte aparência:
   
   ![Definir fontes de entrada](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-01.png)
   
9.  Clique em **Salvar** na parte inferior da página para concluir o assistente. Agora você pode adicionar outra fonte de entrada para criar o fluxo de saída. Clique com o botão direito do mouse no nó de entradas e clique em **Novo Item**.
   
   ![Definir fontes de entrada](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-02.png)
   
10. Na janela exibida, escolha **Entrada do Stream Analytics do Azure** e altere o Nome para **ExitStream.json**. Clique em **Adicionar**.
   
   ![Definir fontes de entrada](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-03.png)
   
11. Clique duas vezes em **ExitStream.json** no projeto e siga as mesmas etapas do fluxo de entrada para preencher. Lembre-se de inserir valores para o nome do Hub de Eventos como mostra a captura de tela a seguir:
   
   ![Definir fontes de entrada](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-04.png)
   
   Agora você definiu dois fluxos de entrada.
   
   ![Definir fontes de entrada](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-05.png)
   
   Em seguida, você adicionará a entrada de dados de referência para o arquivo de blob que contém os dados de registro do carro.
   
12. Clique com o botão direito do mouse em **Entradas** e siga o mesmo processo para as entradas de fluxo, mas selecione **DADOS DE REFERÊNCIA** em vez de Fluxo de Dados e o Alias de Entrada é **Registro**.
   
   ![Definir fontes de entrada](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-06.png)
   
13. Selecione a conta de armazenamento que contém **tolldata**. O nome do contêiner deve ser **tolldata** e o **PADRÃO DO CAMINHO** deve ser **registration.json**. Este nome de arquivo diferencia maiúsculas de minúsculas e deve estar em minúsculas.
14. Clique em **Salvar** para concluir o assistente.

Agora, todas as entradas são definidas.

## <a name="define-output"></a>Definir saída
1.  No **Gerenciador de Soluções**, expanda o nó **Entradas** e clique duas vezes em **Output.json**.
2.  Defina o Alias de saída como **saída** e Coletor como Banco de Dados SQL.
2.  Insira o nome do banco de dados: **TollDataDB**.
3.  Digite **tolladmin** no campo **USERNAME**, **123toll!** no campo **SENHA**, e **TollDataRefJoin** no campo **TABELA**.
4.  Clique em **Salvar**.

![Definir saída](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-output-01.png)
 
## <a name="azure-stream-analytics-query"></a>Consulta do Stream Analytics do Azure
Este tutorial tenta responder várias perguntas de negócios relacionadas aos dados de pedágio e criar consultas do Stream Analytics que possam ser usadas no Stream Analytics do Azure para fornecer uma resposta relevante.
Antes de começar seu primeiro trabalho do Stream Analytics, vamos explorar um cenário simples e a sintaxe de consulta.

### <a name="introduction-to-azure-stream-analytics-query-language"></a>Introdução à linguagem de consulta do Stream Analytics do Azure
Digamos que você precise contar o número de veículos que entram em uma cabine de pedágio. Como se trata de um fluxo contínuo de eventos, você precisa definir um “período”. Vamos modificar a pergunta para "Quantos veículos entram em uma cabine de pedágio a cada três minutos?". Isso é conhecido como contagem em cascata.

Vejamos a consulta do Stream Analytics do Azure que responde essa pergunta:

        SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count 
        FROM EntryStream TIMESTAMP BY EntryTime 
        GROUP BY TUMBLINGWINDOW(minute, 3), TollId 

Como você pode ver, o Stream Analytics do Azure usa uma linguagem de consulta parecida com SQL e adiciona algumas extensões para especificar aspectos da consulta relacionados ao tempo.

Para obter mais detalhes, leia sobre [Gerenciamento de tempo](https://msdn.microsoft.com/library/azure/mt582045.aspx) e construções de [Janelas](https://msdn.microsoft.com/library/azure/dn835019.aspx) usadas na consulta no MSDN.

Agora que você escreveu sua primeira consulta do Stream Analytics do Azure, é hora de testá-la usando os arquivos de dados de exemplo localizados na pasta TollApp no caminho a seguir:

**..\TollApp\TollApp\Data**

Esta pasta contém os seguintes arquivos: •   Entry.json •   Exit.json •   Registration.json

## <a name="question-number-of-vehicles-entering-a-toll-booth"></a>Pergunta: número de veículos que entram em uma cabine de pedágio
No projeto, clique duas vezes em Script.asaql para abrir o script no editor e cole o script da seção anterior no editor. O editor de consulta dá suporte ao IntelliSense, cores de sintaxe e marcador de erro.

![Editar consulta](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="testing-azure-stream-analytics-queries-locally"></a>Testando as consultas do Stream Analytics do Azure localmente

1. Você pode primeiro compilar a consulta para ver se há algum erro de sintaxe. [TBD]
2. Para validar essa consulta em relação aos dados de exemplo, você pode usar dados de exemplo locais clicando com o botão direito do mouse na entrada e selecionando **Adicionar entrada local** no menu de contexto.
   
   ![Adicionar entrada local](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-01.png)
   
   Na janela pop-up, selecione os dados de exemplo do caminho local. Clique em **Salvar**.
   
   ![Adicionar entrada local](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-02.png)
   
   Um arquivo chamado **local_EntryStream.json** é adicionado automaticamente à pasta de entradas.
   
   ![Adicionar entrada local](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-03.png)
   
3. Clique em Executar Localmente no editor de consulta. Ou você pode pressionar F5.
   
   ![Execução local](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-01.png)
   
   Você pode encontrar o caminho de saída da saída do console e pressionar qualquer tecla para abrir a pasta de resultados.
   
   ![Execução local](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-02.png)
   
4. Verifique o resultado na pasta local.
   
   ![Execução local](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-03.png)
   
   
### <a name="sample-input"></a>Entrada de exemplo
Você também pode tirar exemplos de dados de entrada de fontes de entrada para o arquivo local. Clique com o botão direito do mouse no arquivo de configuração de entrada e selecione **Dados de Exemplo**. 

![Dados de amostra](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-01.png)

Observe que por enquanto você pode tirar exemplos apenas do Hub de Eventos ou do Hub IoT. Não há suporte para outras fontes de entrada.  Na janela de diálogo pop-up, preencha o caminho local para salvar os dados de exemplo. Clique em **Exemplo**.

![Dados de amostra](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-02.png)
 
Você pode ver o andamento na janela Saída. 

![Dados de amostra](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-03.png)
 
### <a name="submit-azure-stream-analytics-query-to-azure"></a>Enviar a consulta do Stream Analytics do Azure para o Azure
No **Editor de Consultas**, clique em **Enviar para o Azure no editor de script**.

![Enviar trabalho](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-01.png)
 
Escolha Criar um Novo Trabalho do Stream Analytics do Azure. Insira o Nome do Trabalho como mostrado a seguir. Escolha a Assinatura correta. Clique em Enviar.

![Enviar trabalho](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-02.png)

 
### <a name="start-the-job"></a>Iniciar o trabalho
Agora, o trabalho foi criado e a exibição de trabalho é aberta automaticamente. Clique no botão **VERDE** para iniciar o trabalho.

![Iniciar trabalho](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-01.png)
 
Escolha a configuração padrão e clique em **Iniciar**.
 
![Iniciar trabalho](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-02.png)

Você pode ver que o status do trabalho foi alterado para **Em execução** e há eventos de entrada/saída.

![Iniciar trabalho](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-03.png)

## <a name="check-results-in-visual-studio"></a>Verificar resultados no Visual Studio
1. Abra o Gerenciador de Servidores do Visual Studio e clique com o botão direito do mouse na tabela **TollDataRefJoin** .
2. Selecione **Mostrar Dados da Tabela** para ver a saída do seu trabalho.
   
   ![Seleção de "Mostrar Dados da Tabela" no Gerenciador de Servidores](media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-check-results.jpg)
   

### <a name="view-job-metrics"></a>Exibir métricas do trabalho
Algumas estatísticas básicas de trabalho podem ser encontradas em **Métricas do Trabalho**. 

![Métricas do trabalho](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-metrics-01.png)

 
## <a name="list-the-job-in-server-explorer"></a>Listar o trabalho no Gerenciador de Servidores
Clique em **Trabalhos do Stream Analytics** no **Gerenciador de Servidores** e clique em **Atualizar**. Você deve ser capaz de ver seu trabalho exibido em **Trabalhos do Stream Analytics**.

![Listar trabalhos](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-list-jobs-01.png)


## <a name="open-job-view"></a>Abrir a exibição do trabalho
Expanda o nó de trabalho e clique duas vezes no nó **Exibição de Trabalho** para abrir a exibição do trabalho.

![Exibição de trabalho](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-view-01.png)


## <a name="export-an-existing-job-to-a-project"></a>Exportar um trabalho existente para um projeto
Há duas maneiras que você pode usar para exportar um trabalho para um projeto.
1. Clique com botão direito do mouse no nó do trabalho no nó **Trabalhos do Stream Analytics** no **Gerenciador de Servidores**. Clique em **Exportar para o Novo Projeto do Stream Analytics** no menu de contexto.
   
   ![Trabalho de exportação](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-01.png)
   
   O projeto gerado no **Gerenciador de Soluções**.
   
   ![Trabalho de exportação](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-02.png)
   
2. Na exibição do trabalho, clique em **Gerar Projeto**.
   
   ![Trabalho de exportação](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-03.png)
   
## <a name="known-issues-and-limitations"></a>Problemas e limitações conhecidos
 
1. O teste local não funcionará se a consulta tiver funções geoespaciais. 
2. Não há suporte do editor para adicionar ou alterar a UDF do JavaScript.
3. O teste local não dá suporte ao salvamento da saída no formato JSON. 
4. Não há suporte para as saídas ADLS e Power BI.



## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


