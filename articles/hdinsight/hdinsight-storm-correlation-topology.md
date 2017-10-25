---
title: Correlacionar eventos ao longo do tempo com o Storm e o HBase no HDInsight
description: Saiba como correlacionar eventos que chegam em momentos diferentes usando o Storm e HBase no HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 17d11479-2d02-4790-8d29-05fb38351479
ms.service: hdinsight
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/07/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 06630096383601e48e8f69f8553314cee42f5f3e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="correlate-events-that-arrive-at-different-times-using-storm-and-hbase"></a>Correlacionar eventos que chegam em diferentes horários usando Storm e o HBase

Usando um armazenamento de dados persistentes com o Apache Storm, você pode correlacionar as entradas de dados que chegam em momentos diferentes. Por exemplo, vincular eventos de logon e logoff de uma sessão de usuário para calcular quanto tempo durou a sessão.

Neste documento, você aprenderá como criar uma topologia Storm C# básica que rastreie eventos de logon e logoff de sessões de usuário e calcule a duração da sessão. A topologia usa HBase como um repositório de dados persistentes. O HBase também permite que você execute consultas de lote nos dados históricos para produzir informações adicionais. Por exemplo, quantas sessões de usuário foram iniciadas ou encerradas durante um período de tempo específico.

## <a name="prerequisites"></a>Pré-requisitos

* O Visual Studio e as ferramentas do HDInsight para Visual Studio. Para obter mais informações, consulte [Introdução ao uso das ferramentas do HDInsight para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

* Apache Storm em cluster HDInsight (baseado em Windows).

  > [!WARNING]
  > Embora as topologias SCP.NET tenham suporte em clusters Storm baseados em Linux criados após 28/10/2016, o pacote SDK do HBase para .NET disponível a partir de 28/10/2016 não funciona corretamente no HDInsight baseado em Linux

* Apache HBase no cluster HDInsight (baseado em Linux ou Windows).

  > [!IMPORTANT]
  > O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* [Java](https://java.com) 1.7 ou posterior em seu ambiente de desenvolvimento. O Java é usado para o pacote da topologia quando ela é enviada ao cluster HDInsight.

  * A variável de ambiente **JAVA_HOME** deve apontar ao diretório que contém o Java.
  * O diretório **%JAVA_HOME%/bin** deve estar no caminho

## <a name="architecture"></a>Arquitetura

![Diagrama do fluxo de dados por meio da topologia](./media/hdinsight-storm-correlation-topology/correlationtopology.png)

A correlação de eventos requer um identificador comum para a origem do evento. Por exemplo, uma ID de usuário, ID de sessão ou outra parte dos dados que seja a) exclusiva e b) incluída em todos os dados enviados ao Storm. Este exemplo usa um valor de GUID para representar uma ID de sessão.

Esse exemplo consiste em dois clusters HDInsight:

* HBase: repositório de dados persistente dados históricos
* Storm: usado para incluir dados de entrada

Os dados são gerados aleatoriamente, pelo Storm e contém dos seguintes itens:

* ID da sessão: um GUID que identifica exclusivamente cada sessão
* Evento: um evento INICIAR ou ENCERRAR. Para este exemplo, INICIAR sempre ocorre antes do ENCERRAR
* Hora: a hora do evento.

Esses dados são processados e armazenados no HBase.

### <a name="storm-topology"></a>Topologia Storm

Quando uma sessão é iniciada, um evento **INICIAR** é recebido pela topologia e registrado no HBase. Quando um evento **ENCERRAR** é recebido, a topologia recupera o evento **INICIAR** e calcula o tempo entre os dois eventos. Esse valor **Duração** é armazenado no HBase com as informações do evento **ENCERRAR**.

> [!IMPORTANT]
> Enquanto essa topologia demonstra o padrão básico, uma solução de produção precisaria fazer o design para os seguintes cenários:
>
> * Eventos que chegam fora de ordem
> * Eventos duplicados
> * Eventos ignorados

A topologia de exemplo é composta dos seguintes componentes:

* Session.cs: simula uma sessão de usuário criando uma ID de sessão aleatória, hora de início e por quanto tempo durará a sessão.

* Spout.cs: cria 100 sessões, emite um evento START, aguarda o tempo limite aleatório para cada sessão e emite um evento END. Em seguida, recicla as sessões encerradas para gerar novas.

* HBaseLookupBolt.cs: usa a ID de sessão para pesquisar informações de sessão do HBase. Quando um evento ENCERRAR é processado, ele localiza o evento INICIAR correspondente e calcula a duração da sessão.

* HBaseBolt.cs: Armazena informações no HBase.

* TypeHelper.cs: ajuda na conversão de tipo ao ler/gravar no HBase.

### <a name="hbase-schema"></a>Esquema do HBase

No HBase, os dados são armazenados em uma tabela com os seguintes esquema/configurações:

* Chave de linha: a ID da sessão é usada como a chave para linhas nesta tabela.

* Família de coluna: o nome da família é “cf”. As colunas armazenadas nesta família são:

  * evento: INICIAR ou ENCERRAR.

  * tempo: o tempo em milissegundos durante o qual o evento ocorreu.

  * duração: o comprimento entre eventos INICIAR e ENCERRAR.

* VERSÕES: a família 'cf' é definida para reter cinco versões de cada linha.

  > [!NOTE]
  > As versões são um log dos valores anteriores armazenados para uma chave de linha específica. Por padrão, o HBase retorna apenas o valor para a versão mais recente de uma linha. Nesse caso, a mesma linha é usada para todos os eventos (INICIAR, ENCERRAR) por que cada versão de uma linha é identificada pelo valor de carimbo de data e hora. As versões fornecem exibição do histórico de eventos registrados para uma ID específica.

## <a name="download-the-project"></a>Baixe o projeto

O projeto de exemplo pode ser baixado de [https://github.com/Azure-Samples/hdinsight-storm-dotnet-event-correlation](https://github.com/Azure-Samples/hdinsight-storm-dotnet-event-correlation).

Este download contém os seguintes projetos C#:

* CorrelationTopology: topologia de Storm C# que emite aleatoriamente eventos de iniciar e encerrar para sessões de usuário. Cada sessão dura entre 1 e 5 minutos.

* SessionInfo: aplicativo do console C# que cria a tabela de HBase e fornece consultas de exemplo para retornar informações sobre dados de sessão armazenados.

## <a name="create-the-table"></a>Criar a tabela.

1. Abra o projeto **SessionInfo** no Visual Studio.

2. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **SessionInfo** e selecione **Propriedades**.

    ![Captura de tela do menu com as propriedades selecionadas](./media/hdinsight-storm-correlation-topology/selectproperties.png)

3. Selecione **configurações**e defina os seguintes valores:

   * HBaseClusterURL: a URL para o cluster HBase. Por exemplo, https://meuclusterhbase.azurehdinsight.net.

   * HBaseClusterUserName: a conta de usuário de admin/HTTP para o cluster

   * HBaseClusterPassword: a senha para a conta de usuário admin/HTTP

   * HBaseTableName: o nome da tabela a ser usada com este exemplo

   * HBaseTableColumnFamily: o nome da família de coluna

     ![Imagem da caixa de diálogo de configuração](./media/hdinsight-storm-correlation-topology/settings.png)

4. Execute a solução. Quando solicitado, selecione a chave "c" para criar a tabela no cluster HBase.

## <a name="build-and-deploy-the-storm-topology"></a>Criar e implantar a topologia Storm

1. Abra a solução **CorrelationTopology** no Visual Studio.

2. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **CorrelationTopology** e selecione Propriedades.

3. Na janela Propriedades, selecione **Configurações** e insira os valores de configuração para este projeto. Os cinco primeiros são os mesmos valores usados pelo projeto **SessionInfo**:

   * HBaseClusterURL: a URL para o cluster HBase. Por exemplo, https://meuclusterhbase.azurehdinsight.net.

   * HBaseClusterUserName: a conta de usuário de admin/HTTP para o cluster.

   * HBaseClusterPassword: a senha para a conta de usuário admin/HTTP.

   * HBaseTableName: o nome da tabela a ser usada com este exemplo. Esse valor deve conter o mesmo nome de tabela usado no projeto SessionInfo.

   * HBaseTableColumnFamily: o nome da família de coluna. Esse valor deve conter o mesmo nome de família de colunas usado no projeto SessionInfo.

   > [!IMPORTANT]
   > Não altere HBaseTableColumnNames, pois os padrões são os nomes usados por **SessionInfo** para recuperar dados.

4. Salvar as propriedades e compilar o projeto.

5. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Enviar para o Storm no HDInsight**. Se solicitado, insira as credenciais de logon para sua assinatura do Azure.

   ![Imagem do item de menu enviar para o storm](./media/hdinsight-storm-correlation-topology/submittostorm.png)

6. Na caixa de diálogo **Enviar Topologia**, selecione o cluster Storm no qual você deseja implantar essa topologia.

   > [!NOTE]
   > Na primeira vez que você enviar uma topologia, pode levar alguns segundos para recuperar o nome do seus clusters HDInsight.

7. Depois que a topologia for carregada e enviada para o cluster, a **Visualização da Topologia Storm** será aberta e exibirá a topologia em execução. Para atualizar os dados, selecione o **CorrelationTopology** e use o botão Atualizar na parte superior direita da página.

   ![Imagem da visualização da topologia](./media/hdinsight-storm-correlation-topology/topologyview.png)

   Quando a topologia começar a gerar dados, o valor da coluna **Emitido** será incrementado.

   > [!NOTE]
   > Se a **Visualização da topologia Storm** não abrir automaticamente, use as seguintes etapas para abri-la:
   >
   > 1. No **Gerenciador de Soluções**, expanda **Azure** e **HDInsight**.
   > 2. Clique com o botão direito do mouse no cluster Storm em que a topologia está em execução e selecione **Exibir topologias Storm**

## <a name="query-the-data"></a>Consultar os dados

Depois que os dados forem emitidos, use as seguintes etapas para consultar os dados.

1. Volte para o projeto **SessionInfo** . Se não estiver executando, inicie uma nova instância dele.

2. Quando solicitado, selecione **s** para pesquisar pelo evento INICIAR. Você será solicitado a inserir uma hora de início e encerramento para definir um intervalo de tempo; apenas os eventos entre essas duas horas serão retornados.

    Use o seguinte formato ao inserir as horas de início e encerramento: HH:MM e “am” ou “pm”. Por exemplo, 11:20pm.

    Para retornar eventos registrados em log, use uma hora de início anterior à implantação da topologia do Storm e uma hora de encerramento equivalente ao horário atual. Os dados retornados contêm entradas semelhantes ao seguinte texto:

        Session e6992b3e-79be-4991-afcf-5cb47dd1c81c started at 6/5/2015 6:10:15 PM. Timestamp = 1433527820737

A busca por eventos ENCERRAR funciona da mesma maneira que os eventos INICIAR. No entanto, os eventos ENCERRAR são gerados aleatoriamente entre 1 e 5 minutos após o evento INICIAR. Você terá que experimentar alguns intervalos de tempo para encontrar os eventos ENCERRAR. Os eventos ENCERRAR também contêm a duração da sessão; a diferença entre a hora do evento INICIAR e do evento ENCERRAR. Veja um exemplo de dados para eventos ENCERRAR:

    Session fc9fa8e6-6892-4073-93b3-a587040d892e lasted 2 minutes, and ended at 6/5/2015 6:12:15 PM

> [!NOTE]
> Embora os valores de hora que você insere estão na hora local, a hora retornada da consulta será em UTC.

## <a name="stop-the-topology"></a>Parar a topologia

Quando você estiver pronto para parar a topologia, volte para o projeto **CorrelationTopology** no Visual Studio. Na **Exibição da Topologia Storm**, selecione a topologia e use o botão **Eliminar** na parte superior da exibição da topologia.

## <a name="delete-your-cluster"></a>Excluir o cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Próximas etapas

Para obter mais exemplos de topologias Storm, consulte [Exemplo de topologias para Storm no HDInsight](hdinsight-storm-example-topology.md).
