---
title: Executar teste de carga em seu aplicativo usando o Visual Studio Team Services | Microsoft Docs
description: Saiba como testar o estresse de seus aplicativos do Azure Service Fabric usando o Visual Studio Team Services.
services: service-fabric
documentationcenter: na
author: cawams
manager: timlt
editor: 
ms.assetid: fc743585-0d1b-483f-981d-493f4552ac07
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/18/2016
ms.author: cawa
ms.openlocfilehash: e8e270ce865d4da3ee219958b308db2c1c89b11b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="load-test-your-application-by-using-visual-studio-team-services"></a>Executar teste de carga em seu aplicativo usando o Visual Studio Team Services
Este artigo mostra como usar os recursos de teste de carga do Microsoft Visual Studio para realizar teste de estresse em um aplicativo. Ele usa um back-end de serviço com estado e um front-end da Web de serviço sem estado do Service Fabric do Azure. O exemplo de aplicativo usado aqui é um simulador de localização de avião. Você fornece uma ID de avião, um horário de partida e um destino. O back-end do aplicativo processa as solicitações e o front-end exibe em um mapa o avião que corresponde aos critérios.

O diagrama a seguir ilustra o aplicativo do Service Fabric que você testará.

![Diagrama do aplicativo de exemplo de localização de avião][0]

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, você precisa fazer o seguinte:

* Obtenha uma conta do Visual Studio Team Services. É possível obter uma gratuitamente no site do [Visual Studio Team Services](https://www.visualstudio.com).
* Obtenha e instale o Visual Studio 2013 ou o Visual Studio 2015. Este artigo usa o Visual Studio 2015 Enterprise, mas o Visual Studio 2013 e outras edições devem funcionar da mesma forma.
* Implante seu aplicativo em um ambiente de preparo. Confira [Como implantar aplicativos em um cluster remoto usando o Visual Studio](service-fabric-publish-app-remote-cluster.md) para saber mais sobre isso.
* Entenda o padrão de uso do aplicativo. Estas informações são usadas para simular o padrão de carga.
* Compreenda o objetivo do teste de carga. Isso ajuda você a interpretar e analisar os resultados de teste de carga.

## <a name="create-and-run-the-web-performance-and-load-test-project"></a>Criar e executar o projeto de Teste de Carga e Desempenho na Web
### <a name="create-a-web-performance-and-load-test-project"></a>Criar um projeto de Teste de Carga e de Desempenho na Web
1. Abra o Visual Studio 2015. Escolha **Arquivo** > **Novo** > **Projeto** na barra de menus para abrir a caixa de diálogo **Novo Projeto**.
2. Expanda o nó **Visual C#** e escolha **Teste** > **Projeto de Teste de Carga e Desempenho na Web**. Dê um nome ao projeto e escolha o botão **OK** .

    ![Captura de tela da caixa de diálogo Novo Projeto][1]

    Você deverá ver, no Gerenciador de Soluções, um novo projeto de Teste de Carga e de Desempenho na Web.

    ![Captura de tela do Gerenciador de Soluções mostrando o novo projeto][2]

### <a name="record-a-web-performance-test"></a>Registrar um teste de desempenho Web
1. Abra o projeto .webtest.
2. Escolha o ícone **Adicionar Gravação** para iniciar uma sessão de gravação em seu navegador.

    ![Captura de tela do ícone Adicionar Gravação em um navegador][3]

    ![Captura de tela do ícone Gravar em um navegador][4]
3. Procure o aplicativo do Service Fabric. O painel de gravação deve mostrar as solicitações da Web.

    ![Captura de tela de solicitações da Web no painel de gravação][5]
4. Execute uma sequência de ações que você espera que os usuários executem. Essas ações serão usadas como um padrão para gerar a carga.
5. Quando terminar, escolha o botão **Parar** para interromper a gravação.

    ![Captura de tela do botão Parar][6]

    O projeto .webtest no Visual Studio deve ter capturado uma série de solicitações. Os parâmetros dinâmicos são substituídos automaticamente. Neste ponto, você pode excluir qualquer solicitação de dependência extra e repetida que não faça parte do seu cenário de teste.
6. Salve o projeto e escolha o comando **Executar Teste** para executar o teste de desempenho na Web localmente e verificar se tudo funcionou corretamente.

    ![Captura de tela do comando Executar Teste][7]

### <a name="parameterize-the-web-performance-test"></a>Parametrizar o teste de desempenho Web
É possível parametrizar o teste de desempenho Web convertendo-o em um teste de desempenho na Web codificado e depois editando o código. Como alternativa, você pode associar o teste de desempenho na Web a uma lista de dados, para que o teste realize a iteração por meio dos dados. Consulte [Gerar e executar um teste de desempenho na Web codificado](https://msdn.microsoft.com/library/ms182552.aspx) para obter detalhes sobre como converter o teste de desempenho Web em um teste codificado. Consulte [Adicionar uma fonte de dados a um teste de desempenho na Web](https://msdn.microsoft.com/library/ms243142.aspx) para obter informações sobre como associar dados a um teste de desempenho Web.

Para este exemplo, converteremos o teste de desempenho Web em um teste codificado para substituir a ID do avião por um GUID gerado, e adicionar mais solicitações para enviar voos a locais diferentes.

### <a name="create-a-load-test-project"></a>Criar um projeto de teste de carga
Um projeto de teste de carga é composto por um ou mais cenários descritos pelo teste de unidade e pelo teste de desempenho Web, juntamente com as configurações de teste de carga adicionais especificadas. As etapas a seguir mostram como criar um projeto de teste de carga:

1. No menu de atalho de seu projeto de teste de Carga e Desempenho na Web, escolha **Adicionar** > **Teste de Carga**. No assistente de **Teste de Carga**, escolha o botão **Próximo** para definir as configurações de teste.
2. Na seção **Padrão de Carga** , escolha se deseja que uma carga de usuário constante ou uma carga por etapa, que começa com alguns usuários e aumenta a quantidade de usuários com o tempo.

    Se você tiver uma boa estimativa da quantidade de carga de usuário e quiser ver o desempenho do sistema atual, escolha **Carga Constante**. Se a sua meta for saber se o sistema apresenta um desempenho consistente sob cargas diversas, escolha **Carga por Etapas**.
3. Na seção **Combinação de Testes**, escolha o botão **Adicionar** e selecione o teste que deseja incluir no teste de carga. Você pode usar a coluna **Distribuição** para especificar a porcentagem total de execuções do teste de cada teste.
4. Na seção **Configurações de Execução** , especifique a duração do teste de carga.

   > [!NOTE]
   > A opção **Iterações de Teste** só estará disponível quando você executar um teste de carga localmente usando o Visual Studio.
   >
   >
5. Na seção **Local** de **Configurações de Execução**, especifique o local em que as solicitações de teste de carga são geradas. O assistente pode solicitar o logon em sua conta do Team Services. Faça logon e, em seguida, escolha um local geográfico. Quando terminar, escolha o botão **Concluir** .
6. Após a criação do teste de carga, abra o projeto .loadtest e escolha a configuração de execução atual, por exemplo, **Configurações de Execução** > **Configurações de Execução1 [Active]**. Isso abre as configurações de execução na janela **Propriedades** .
7. Na seção **Resultados** da janela Propriedades **Configurações de Execução**, a configuração **Armazenamento de Detalhes de Medição de Tempo** deve ter **Nenhum** como valor padrão. Altere este valor para **Todos os Detalhes Individuais** para obter mais informações sobre os resultados de teste de carga. Confira [Teste de carga](https://www.visualstudio.com/load-testing.aspx) para saber mais sobre como conectar-se ao Visual Studio Team Services e executar um teste de carga.

### <a name="run-the-load-test-by-using-visual-studio-team-services"></a>Executar o teste de carga usando o Visual Studio Team Services
Escolha o comando **Executar Teste de Carga** para iniciar a execução do teste.

![Captura de tela do comando Executar Teste de Carga][8]

## <a name="view-and-analyze-the-load-test-results"></a>Exibir e analisar os resultados de teste de carga
À medida que o teste de carga progredir, as informações de desempenho serão incluídas em um gráfico. Você deverá ver algo semelhante ao gráfico a seguir.

![Captura de tela do gráfico de desempenho para resultados de testes de carga][9]

1. Escolha o link **Baixar relatório** próximo à parte superior da página. Após o download do relatório, escolha o botão **Exibir relatório** .

    Na guia **Gráfico** , você pode ver gráficos para vários contadores de desempenho. Na guia **Resumo** , os resultados gerais do teste são exibidos. A guia **Tabelas** mostra o número total de testes de carga aprovados e reprovados.
2. Escolha os links de números nas colunas **Teste** > **Falha** e **Erros** > **Contagem** para ver os detalhes dos erros.

    A guia **Detalhes** mostra informações virtuais de cenário de teste e de usuário para solicitações com falha. Esses dados podem ser úteis se o teste de carga incluir vários cenários.

Confira [Analisando os resultados de teste de carga no modo de exibição de gráficos do Analisador de Teste de Carga](https://www.visualstudio.com/load-testing.aspx) para obter mais informações sobre a exibição dos resultados de teste de carga.

## <a name="automate-your-load-test"></a>Automatizar o teste de carga
O teste de carga do Visual Studio Team Services fornece APIs para ajudá-lo a gerenciar testes de carga e a analisar os resultados em uma conta do Team Services. Confira [APIs Rest do teste de carga de nuvem](http://blogs.msdn.com/b/visualstudioalm/archive/2014/11/03/cloud-load-testing-rest-apis-are-here.aspx) para saber mais.

## <a name="next-steps"></a>Próximas etapas
* [Monitorando e diagnosticando serviços em uma configuração de desenvolvimento do computador local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[0]: ./media/service-fabric-vso-load-test/OverviewDiagram.png
[1]: ./media/service-fabric-vso-load-test/NewProjectDialog.png
[2]: ./media/service-fabric-vso-load-test/Project.png
[3]: ./media/service-fabric-vso-load-test/AddRecording.png
[4]: ./media/service-fabric-vso-load-test/AddRecording2.png
[5]: ./media/service-fabric-vso-load-test/ActionSequence.png
[6]: ./media/service-fabric-vso-load-test/StopRecording.png
[7]: ./media/service-fabric-vso-load-test/RunTest.png
[8]: ./media/service-fabric-vso-load-test/RunTest2.png
[9]: ./media/service-fabric-vso-load-test/Graph.png
