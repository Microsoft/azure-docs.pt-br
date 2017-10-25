---
title: Implantar e gerenciar topologias do Apache Storm no HDInsight| Microsoft Docs
description: Aprenda a implantar, monitorar e gerenciar topologias do Apache Storm usando o Painel do Storm no HDInsight. Use as ferramentas do Hadoop para Visual Studio.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 5e542072-f014-42aa-82d6-2694a76df520
ms.service: hdinsight
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/01/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 34072574f83b51280e60e2f8766c6c5d5a33c307
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-and-manage-apache-storm-topologies-on-windows-based-hdinsight"></a>Implantar e gerenciar topologias Apache Storm no HDInsight baseado no Windows

O Painel Storm permite que você implante e execute facilmente topologias Apache Storm para seu cluster HDInsight usando o navegador da Web. Você também pode usar o painel para monitorar e gerenciar topologias em execução. Se você usar o Visual Studio, as ferramentas do HDInsight para Visual Studio fornecem recursos semelhantes no Visual Studio.

Os recursos do Storm e do Painel Storm das Ferramentas do HDInsight contam com API REST Storm, que pode ser usada para criar suas próprias soluções de monitoramento e gerenciamento.

> [!IMPORTANT]
> As etapas deste documento exigem um cluster de Storm no HDInsight que usa o Windows como o sistema operacional. O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> Para obter informações sobre como implantar e gerenciar topologias Storm com um cluster HDInsight que usa Linux, veja [Implantar e gerenciar topologias Apache Storm no HDInsight baseado em Linux](hdinsight-storm-deploy-monitor-topology-linux.md)

## <a name="prerequisites"></a>Pré-requisitos

* **Apache Storm no HDInsight** - veja [Introdução ao Apache Storm no HDInsight](hdinsight-apache-storm-tutorial-get-started.md) para obter as etapas de criação de um cluster.

* Para o **Painel Storm**: um navegador da Web moderno que oferece suporte a HTML5.

* Para **Visual Studio** - SDK do Azure 2.5.1 ou mais recente e as Ferramentas do HDInsight para Visual Studio. Consulte [Introdução ao uso das Ferramentas do HDInsight para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md) para instalar e configurar as ferramentas do HDInsight para Visual Studio.

    Uma das seguintes versões do Visual Studio:

  * Visual Studio 2012 com Atualização 4

  * Visual Studio 2013 com Atualização 4 ou Visual Studio 2013 Community

  * Visual Studio 2015 (qualquer edição)

  * Visual Studio 2017 (qualquer edição)

## <a name="storm-dashboard"></a>Painel Storm

O Painel do Strom é uma página da Web disponível no cluster Storm. A URL é **https://&lt;nomedocluster>.azurehdinsight.net/**, onde **nomedocluster** é o nome do seu Storm no cluster HDInsight.

Na parte superior do Painel Storm, selecione **Enviar Topologia**. Siga as instruções na página para executar uma topologia de amostra ou para carregar e executar uma topologia que você criou.

![a página da topologia de envio][storm-dashboard-submit]

### <a name="storm-ui"></a>Interface do Usuário do Storm

No Painel Storm, selecione o link **Interface do Usuário do Storm** . Isso exibirá informações sobre o cluster, além de qualquer topologia em execução.

![a interface do usuário do storm][storm-dashboard-ui]

> [!NOTE]
> Com algumas versões do Internet Explorer, talvez você descubra que a interface do usuário do Storm não é atualizada depois da primeira vez que você a visita. Por exemplo, ela pode não mostrar as novas topologias enviadas ou pode mostrar uma topologia como ativa quando na verdade ela foi desativada anteriormente. A Microsoft está ciente desse problema e está trabalhando em uma solução.

#### <a name="main-page"></a>Página principal

A página principal da interface do usuário do Storm fornece as seguintes informações:

* **Resumo do cluster**: informações básicas sobre o cluster do Storm

* **Resumo da topologia**: uma lista das topologias em execução. Use os links desta seção para exibir mais informações sobre topologias específicas.

* **Resumo do Supervisor**: informações sobre o supervisor do Storm.

* **Configuração do Nimbus**: configuração do Nimbus para o cluster.

#### <a name="topology-summary"></a>Resumo da topologia

Selecionar um link na seção **Resumo da topologia** exibirá as seguintes informações sobre a topologia:

* **Resumo da topologia**: informações básicas sobre a topologia.

* **Ações da topologia**: ações de gerenciamento que podem ser executadas para a topologia.

  * **Ativar**: retoma o processamento de uma topologia de desativada.

  * **Desativar**: pausa uma topologia em execução.

  * **Reequilibrar**: ajusta o paralelismo da topologia. Você deve reequilibrar topologias em execução depois de alterar o número de nós no cluster. Isso permite que a topologia ajuste o paralelismo para compensar o aumento/diminuição do número de nós no cluster.

      Para saber mais, veja [Understanding the parallelism of a Storm topology (http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) (Noções básicas do paralelismo de uma topologia Storm).

  * **Eliminar**: encerra uma topologia Storm após o tempo limite especificado.

* **Estatísticas da topologia**: estatísticas sobre a topologia. Use os links na coluna **Janela** para definir o período de tempo para as entradas restantes na página.

* **Spouts**: os spouts usados pela topologia. Use os links desta seção para exibir mais informações sobre spouts específicos.

* **Bolts**: os bolts usados pela topologia. Use os links desta seção para exibir mais informações sobre bolts específicos.

* **Configuração da topologia**: a configuração da topologia selecionada.

#### <a name="spout-and-bolt-summary"></a>Resumo de Spout e Bolt

Selecionar um spout nas seções **Spouts** ou **Bolts** exibirá as seguintes informações sobre o item selecionado:

* **Resumo do componente**: informações básicas sobre o spout ou o bolt.

* **Estatísticas de Spout/Bolt**: estatísticas sobre o spout ou o bolt. Use os links na coluna **Janela** para definir o período de tempo para as entradas restantes na página.

* **Estatísticas de entrada** (somente bolt): informações sobre os streams de entrada consumidos pelo bolt.

* **Estatísticas de saída**: informações sobre os streams emitidos por esse spout ou bolt.

* **Executores**: informações sobre as instâncias do spout ou bolt. Selecione a entrada **Porta** gerada por um executor específico para exibir um log de informações de diagnóstico produzido para esta instância.

* **Erros**: qualquer informação de erro para este spout ou bolt.

## <a name="hdinsight-tools-for-visual-studio"></a>Ferramentas do HDInsight para Visual Studio

As Ferramentas do HDInsight podem ser usadas para enviar topologias C# ou híbridas para seu cluster do Storm. As etapas a seguir usam um aplicativo de exemplo. Para obter informações sobre como criar suas próprias topologias usando as Ferramentas do HDInsight, consulte [Desenvolver topologias em C# usando as Ferramentas do HDInsight para Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

Use as etapas a seguir para implantar um exemplo para seu Storm no cluster HDInsight e exibir e gerenciar a topologia.

1. Se você ainda não instalou a versão mais recente das Ferramentas do HDInsight para Visual Studio, consulte [Introdução ao uso das Ferramentas do HDInsight para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

2. Abra o Visual Studio, selecione **Arquivo** > **Novo** > **Projeto**.

3. Na caixa de diálogo **Novo Projeto**, expanda **Instalados** > **Modelos** e selecione **HDInsight**. Na lista de modelos, selecione **Amostra do Storm**. Na parte inferior da caixa de diálogo, digite um nome para o aplicativo.

    ![imagem](./media/hdinsight-storm-deploy-monitor-topology/sample.png)

4. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Enviar para o Storm no HDInsight**.

   > [!NOTE]
   > Se solicitado, insira as credenciais de logon para sua assinatura do Azure. Se você tiver mais de uma assinatura, faça o logon naquela que contém seu Storm no cluster HDInsight.

5. Selecione seu Storm no cluster HDInsight no menu suspenso **Cluster Storm** e selecione **Enviar**. Você pode monitorar se o envio teve êxito ou não usando a janela **Saída** .

6. Depois que a topologia tiver sido enviada com êxito, as **Topologias Storm** para o cluster deverão aparecer. Selecione a topologia da lista para exibir informações sobre a topologia em execução.

    ![monitor do visual studio](./media/hdinsight-storm-deploy-monitor-topology/vsmonitor.png)

   > [!NOTE]
   > Você também pode exibir **Topologias Storm** no **Gerenciador de Servidores** expandindo **Azure** > **HDInsight** e clicando com o botão direito do mouse em um Storm no cluster do HDInsight e selecionando **Exibir Topologias Storm**.

    Escolha a forma dos spouts ou bolts para exibir informações sobre esses componentes. Uma nova janela será aberta para cada item selecionado.

   > [!NOTE]
   > O nome da topologia é o nome de classe da topologia (nesse caso, `HelloWord`), com um carimbo de data e hora anexado.

7. Na exibição **Resumo da Topologia**, selecione **Eliminar** para parar a topologia.

   > [!NOTE]
   > As topologias Storm continuarão em execução até serem paradas ou até que o cluster seja excluído.


## <a name="rest-api"></a>API REST

A interface do usuário do Storm é criada sobre a API REST e, portanto, você pode realizar gerenciamento semelhante e monitorar a funcionalidade usando a API REST. Você pode usar a API REST para criar ferramentas personalizadas para o gerenciamento e o monitoramento de topologias Storm.

Para obter mais informações, veja [API REST da interface do usuário do Storm](https://github.com/apache/storm/blob/0.9.3-branch/STORM-UI-REST-API.md). As informações a seguir são específicas para o uso da API REST com Apache Storm no HDInsight.

### <a name="base-uri"></a>URI de base

A URI de base para a API REST em clusters HDInsight é **https://&lt;nomedocluster>.azurehdinsight.net/stormui/api/v1/**, onde **nomedocluster** é o nome do seu Storm no cluster do HDInsight.

### <a name="authentication"></a>Autenticação

As solicitações para a API REST devem usar a **autenticação básica**e, portanto, você usará o nome do administrador e a senha do cluster HDInsight.

> [!NOTE]
> Como a autenticação básica é enviada usando texto não criptografado, você **sempre** deverá usar HTTPS para proteger as comunicações com o cluster.

### <a name="return-values"></a>Valores de retorno

As informações retornadas da API REST só poderão ser usadas dentro do cluster ou de máquinas virtuais na mesma Rede Virtual do Azure que o cluster. Por exemplo, o nome de domínio totalmente qualificado (FQDN) retornado para servidores Zookeeper não será acessível pela Internet.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como implantar e monitorar topologias usando o Painel Storm, saiba como:

* [Desenvolver topologias C# usando as Ferramentas do HDInsight para Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

* [Desenvolver topologias baseadas em Java usando Maven](hdinsight-storm-develop-java-topology.md)

Para obter mais topologias de exemplo, consulte [Topologias de exemplo para Storm no HDInsight](hdinsight-storm-example-topology.md).

[hdinsight-dashboard]: ./media/hdinsight-storm-deploy-monitor-topology/dashboard-link.png
[storm-dashboard-submit]: ./media/hdinsight-storm-deploy-monitor-topology/submit.png
[storm-dashboard-ui]: ./media/hdinsight-storm-deploy-monitor-topology/storm-ui-summary.png
