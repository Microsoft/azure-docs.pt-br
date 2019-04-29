---
title: Usar o Navegador de Trabalhos e a Exibição de Trabalho para trabalhos do Azure Data Lake Analytics
description: Este artigo descrever como usar o Navegador de Trabalhos e a Exibição de Trabalho para trabalhos do Azure Data Lake Analytics.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonwhowell
ms.assetid: bdf27b4d-6f58-4093-ab83-4fa3a99b5650
ms.topic: conceptual
ms.date: 08/02/2017
ms.openlocfilehash: 905100f8a1444f6f6ee18d3bf9e9eab2ede8c805
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60616046"
---
# <a name="use-job-browser-and-job-view-for-azure-data-lake-analytics"></a>Usar o Navegador de Trabalhos e a Exibição de Trabalho para o Azure Data Lake Analytics
O serviço Azure Data Lake Analytics arquiva trabalhos enviados em um repositório de consultas. Neste artigo, você aprende como usar o Navegador de Trabalhos e Exibição de Trabalho nas Ferramentas do Azure Data Lake para Visual Studio para localizar as informações de histórico do trabalho. 

Por padrão, o serviço Data Lake Analytics arquiva os trabalhos por 30 dias. O período de validade pode ser configurado do Portal do Azure, configurando a política de expiração personalizada. Você não poderá acessar as informações sobre o trabalho após a expiração. 

## <a name="prerequisites"></a>Pré-requisitos
Veja [Pré-requisitos das Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md#prerequisites).

## <a name="open-the-job-browser"></a>Abrir o Navegador de Trabalhos
Acesse o Navegador de Trabalhos via **Gerenciador de Servidores > Azure > Data Lake Analytics > Trabalhos** no Visual Studio.  Usando o navegador de trabalho, você pode acessar o repositório de consultas de uma conta do Data Lake Analytics. O Navegador de Trabalho exibe o Repositório de Consultas à esquerda, mostrando as informações básicas do trabalho, bem como a Exibição de Trabalho à direita, mostrando as informações detalhadas do trabalho.

## <a name="job-view"></a>Exibição de Trabalho
A Exibição de Trabalho mostra as informações detalhadas de um trabalho. Para abrir um trabalho, clique duas vezes em um trabalho no Navegador de Trabalhos ou abra-o do menu do Data Lake clicando em Exibição de Trabalho. Você deve ver uma caixa de diálogo populada com a URL do trabalho.

![Navegador de Trabalhos das Ferramentas do Data Lake para Visual Studio](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view.png)

A Exibição de Trabalho contém:

* Resumo do trabalho
  
    Atualize a Exibição de Trabalho para ver as informações mais recentes sobre trabalhos em execução.
  
  * Status do Trabalho (grafo):
    
      Status do Trabalho descreve as fases do trabalho:
    
      ![Status das fases do trabalho do Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-phases.png)
    
    * Preparando: carregue o script na nuvem, compilando e otimizando o script usando o serviço de compilação.
    * Na fila: trabalhos estão na fila quando estão esperando por recursos suficientes ou quando os trabalhos excedem a limitação de máximo de trabalhos simultâneos por conta. A configuração de prioridade determina a sequência de trabalhos na fila – quanto menor o número, maior a prioridade.
    * Em execução: o trabalho está sendo executado em sua conta do Data Lake Analytics.
    * Finalizando: o trabalho está sendo concluído (por exemplo, finalizando o arquivo).
      
      O trabalho pode falhar em todas as fases. Por exemplo, erros de compilação na fase de Preparação, erros de tempo limite na fase Na fila e erros de execução na fase Em execução, etc.
  * Informações Básicas
    
      As informações básicas do trabalho são mostradas na parte inferior do painel de Resumo do Trabalho.
    
      ![Status das fases do trabalho do Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-info.png)
    
    * Resultado do Trabalho: êxito ou falha. O trabalho pode falhar em todas as fases.
    * Duração Total: a hora do relógio (duração) entre a hora do envio e a hora de término.
    * Tempo de Computação Total: a soma do tempo de execução de cada vértice, você pode considerá-lo como o tempo durante o qual o trabalho é executado em apenas um vértice. Consulte Total de Vértices para obter mais informações sobre o vértice.
    * Hora de Envio/Início/Término: a hora em que o serviço do Data Lake Analytics recebe o envio de trabalho/começa a executar o trabalho/termina o trabalho com êxito ou não.
    * Compilação/Na fila/Em execução: tempo gasto durante a fase de Preparação/Na fila/Em execução.
    * Conta: a conta do Data Lake Analytics usada para executar o trabalho.
    * Autor: o usuário que enviou o trabalho, pode ser uma conta do sistema ou de uma pessoa real.
    * Prioridade: a prioridade do trabalho. Quanto menor o número, maior a prioridade. Ela afeta somente a sequência dos trabalhos na fila. Definir uma prioridade mais alta não impede trabalhos em execução.
    * Paralelismo: o número máximo solicitado de ADLAUs (unidades simultâneas do Azure Data Lake Analytics), também conhecidas como vértices. Atualmente, um vértice é igual a uma VM com dois núcleos virtuais e 6 GB de RAM, embora isso possa ser atualizado em atualizações futuras do Data Lake Analytics.
    * Bytes Restantes: bytes que precisam ser processados até que o trabalho seja concluído.
    * Bytes lidos/gravados: bytes que foram lidos/gravados desde o início da execução do trabalho.
    * Total de vértices: o trabalho é dividido em várias partes, cada uma delas é chamada de um vértice. Esse valor descreve de quantas partes de trabalho o trabalho consiste. Você pode considerar um vértice como uma unidade de processo básico, também conhecido como ADLAU (Unidade do Azure Data Lake Analytics), sendo que vértices podem ser executados em paralelismo. 
    * Concluído/Em execução/Com falha: a contagem de vértices concluídos/em execução/com falha. Vértices podem falhar devido tanto a falhas do sistema quanto ao código do usuário, o sistema volta a tentar usar vértices com falha automaticamente por algumas vezes. Se o vértice ainda falhar após as novas tentativas, todo o trabalho falhará.
* Grafo do Trabalho
  
    Um script U-SQL representa a lógica de transformação de dados de entrada em dados de saída. O script é compilado e otimizado para um plano de execução física na fase de Preparação. O Grafo do Trabalho mostra o plano de execução física.  O diagrama a seguir ilustra o processo:
  
    ![Status das fases do trabalho do Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-logical-to-physical-plan.png)
  
    Um trabalho é dividido em várias partes de trabalho. Cada parte de trabalho é chamada de um vértice. Os vértices são agrupados como um Supervértice (também conhecido como estágio) e visualizados como Grafo do Trabalho. Os letreiros de estágio verdes no grafo de trabalho mostram os estágios.
  
    Cada vértice em um estágio está fazendo o mesmo tipo de trabalho com partes diferentes dos mesmos dados. Por exemplo, se você tiver um arquivo com 1 TB de dados e houver centenas de vértices lendo tal arquivo, cada um deles estará lendo uma parte. Esses vértices são agrupados no mesmo estágio e fazem o mesmo trabalho em diferentes partes do mesmo arquivo de entrada.
  
  * <a name="state-information"></a>Informações do estágio
    
      Em um estágio específico, alguns números são mostrados no letreiro.
    
      ![Estágio no grafo de trabalho do Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage.png)
    
    * SV1 Extract: o nome de um estágio, nomeado por um número e pelo método da operação.
    * 84 vértices: a contagem total de vértices neste estágio. O número indica em quantas partes o trabalho está dividido nesse estágio.
    * 12,9 s/vértice: o tempo de execução de vértice médio para este estágio. Esse número é calculado pela SOMA (tempo de execução de todos os vértices) / (contagem total de vértices). Isso significa que se você puder atribuir todos os vértices executados em paralelismo, o estágio inteiro será concluído em 12,9 s. Isso também significa que se todo o trabalho neste estágio for feito em série, o custo seria nº. de vértices * tempo MÉDIO.
    * 850.895 linhas gravadas: contagem total de linhas gravadas nesse estágio.
    * L/G: a quantidade de dados lidos/gravados nesse estágio, em bytes.
    * Cores: cores são usadas no estágio para indicar status de vértice diferentes.
      
      * Verde indica que o vértice foi bem-sucedido.
      * Laranja indica que o vértice realizou uma nova tentativa. O vértice que realizou uma nova tentativa falhou mas uma nova tentativa é realizada automaticamente pelo sistema e o estágio geral é concluído com êxito. Se ocorrerem novas tentativas para o vértice mas ele ainda falhar, a cor se tornará vermelha e indicando que todo o trabalho falhou.
      * Vermelho indica falha, que significa que foram realizadas novas tentativas para um determinado vértice algumas vezes pelo sistema, mas ainda assim ele falhou. Esse cenário faz com que todo o trabalho falhe.
      * Azul significa que um determinado vértice está em execução.
      * Branco indica que o vértice está Aguardando. O vértice pode estar aguardando para ser agendado após um ADLAU se tornar disponível ou ele pode estar esperando por entrada uma vez que seus dados de entrada podem não estar prontos.
      
      Você pode encontrar mais detalhes para o estágio passando o cursor do mouse sobre um estado:
      
      ![Detalhes do grafo de trabalho do Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage-details.png)
  * Vértices: descreve os detalhes de vértices, por exemplo, quantos vértices no total, quantos vértices foram concluídos, se eles falharam ou ainda estão em execução/aguardando, etc.
  * Dados lidos entre pods/dentro de um pod: arquivos e dados são armazenados em vários pods no sistema de arquivos distribuído. O valor aqui descreve a quantidade de dados lidos no mesmo pod ou em vários pods.
  * Tempo de computação total: a soma do tempo de execução de cada vértice no estágio, você pode considerá-lo como o tempo que levaria se todos os trabalhos no estágio fossem executados em apenas um vértice.
  * Dados e linhas gravadas/lidas: indica que volume de dados ou quantas linhas foram lidas/gravadas ou precisam ser lidas.
  * Falhas de leitura de vértice: descreve quantos vértices falharam durante a leitura de dados.
  * Descartes de vértices duplicados: se um vértice estiver muito lento, o sistema poderá agendar vários vértices para executar a mesma parte de trabalho. Vértices redundantes serão descartados quando um dos vértices for concluído com êxito. Os descartes de vértices duplicados registra o número de vértices que são descartados como duplicações no estágio.
  * Revogações de vértice: o vértice foi bem-sucedido, mas será executado novamente mais tarde devido a alguns motivos. Por exemplo, se o vértice downstream perder dados de entrada intermediários, ele solicitará ao vértice upstream que execute novamente.
  * Execuções de agendamento de vértice: o tempo total pelo qual os vértices foram agendados.
  * Leitura de dados de vértice Mín./Média/Máx.: o mínimo/médio/máximo de toda leitura de dados de vértice.
  * Duração: o tempo que um estágio leva, você precisará carregar o perfil para ver esse valor.
  * Reprodução do Trabalho
    
      O Data Lake Analytics executa trabalhos e arquiva as informações de execução de vértices dos trabalhos, por exemplo, quando os vértices são iniciados, interrompidos, quando falham e como novas tentativas são feitas, etc. Todas as informações são registradas automaticamente no repositório de consultas e armazenados em seu Perfil de Trabalho. Você pode baixar o Perfil de Trabalho por meio de "Carregar Perfil" na Exibição de Trabalho, além de poder exibir a Reprodução de Trabalho depois de baixar o Perfil de Trabalho.
    
      A Reprodução de Trabalho é uma visualização representativa do que aconteceu no cluster. Ela ajuda você a assistir ao andamento da execução do trabalho e detecte visualmente os gargalos e anomalias no desempenho em um tempo muito curto (geralmente menos de 30s).
  * Exibição de Mapa de Calor do Trabalho 
    
      O Mapa de Calor de Trabalho pode ser selecionado por meio do menu suspenso Modo de Exibição no Grafo do Trabalho. 
    
      ![Modo de exibição do mapa de heap do grafo do trabalho do Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-display.png)
    
      Ele mostra a E/S, o tempo e o mapa de calor de taxa de transferência de um trabalho, por meio do qual você pode localizar onde o trabalho passa a maior parte do tempo ou se o seu trabalho é um trabalho de limite de E/S e assim por diante.
    
      ![Exemplo de mapa de heap do grafo do trabalho do Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-example.png)
    
    * Progresso: o andamento da execução do trabalho, veja as informações em informações do estágio.
    * Dados lidos/gravados: o mapa de calor do total de dados lidos/gravados em cada estágio.
    * Tempo de computação: o mapa de calor da SOMA (tempo de execução de cada vértice), você pode considerar isso como o tempo que levaria se todo o trabalho no estágio fosse executado com apenas 1 vértice.
    * Tempo médio de execução por nó: o mapa de calor da SOMA (tempo de execução de cada vértice) / (número de vértices). Isso significa que se você puder atribuir todos os vértices executados em paralelismo, o estágio inteiro será concluído nesse período.
    * Taxa de transferência de entrada/saída: o mapa de calor da taxa de transferência de entrada/saída de cada estágio; com isso, você pode confirmar se o seu trabalho é um trabalho associado a E/S.
* Operações de Metadados
  
    Você pode executar algumas operações de metadados em seu script U-SQL, como criar um banco de dados, remover uma tabela, etc. Essas operações são mostradas na Operação de Metadados após a compilação. Aqui você pode encontrar asserções, além de criar e remover entidades.
  
    ![Operações de metadados de Exibição de Trabalho do Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-metadata-operations.png)
* Histórico de Estado
  
    O Histórico de Estado também é visualizado no Resumo do Trabalho, mas você pode obter mais detalhes aqui. Você pode encontrar informações mais detalhadas, por exemplo, quando o trabalho é preparado, colocado na fila, quando inicia a execução e quando encerra. Também é possível encontrar quantas vezes o trabalho foi compilado (o valor de CcsAttempts: 1), quando o trabalho é realmente expedido para o cluster (o valor de Detalhe: expedindo o trabalho para cluster), etc.
  
    ![Histórico de Estado de Exibição de Trabalho do Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-state-history.png)
* Diagnósticos
  
    A ferramenta diagnostica automaticamente a execução do trabalho. Você receberá alertas quando houver algum erro ou problemas de desempenho em seus trabalhos. Observe que você precisa baixar o Perfil para obter informações completas aqui. 
  
    ![Diagnóstico da Exibição de Trabalho do Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-diagnostics.png)
  
  * Avisos: um alerta aparece aqui com aviso do compilador. Quando o alerta for exibido, você poderá clicar no link "x problema(s)" para obter mais detalhes.
  * Vértice executado por tempo demais: se qualquer vértice for executado além do tempo limite para execução (digamos, por 5 horas), haverá problemas.
  * Uso de recursos: se você tiver alocado mais paralelismo do que necessário ou paralelismo insuficiente, haverá problemas. Você pode também clicar em Uso de recursos para ver mais detalhes e executar cenários hipotéticos para localizar uma melhor alocação de recurso (para obter mais detalhes, consulte este guia).
  * Verificação de memória: se qualquer um deles usar mais de 5 GB de memória, haverá problemas. Se o trabalho usar mais memória do que a limitação do sistema, o próprio sistema poderá interromper sua execução.

## <a name="job-detail"></a>Detalhes do Trabalho
Detalhes do Trabalho mostra as informações detalhadas do trabalho, incluindo o Script, Recursos e Modo de Exibição de Execução de Vértice.

![Detalhes do trabalho do Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-details.png)

* Script
  
    O script U-SQL do trabalho é armazenado no repositório de consultas. Você pode exibir o script U-SQL original e reenviá-lo se necessário.
* Recursos
  
    Você pode encontrar as saídas de compilação de trabalho armazenadas no repositório de consultas por meio de Recursos. Por exemplo, você pode encontrar aqui o "algebra.xml", que é usado para mostrar o Grafo do Trabalho, os assemblies que você registrou, etc.
* Modo de exibição de execução de vértice
  
    Mostra detalhes de execução de vértices. O Perfil de Trabalho arquiva o todos os logs de execução de vértices, como o total de dados lidos/gravados, tempo de execução, estado, etc. Por meio desse modo de exibição, você pode obter mais detalhes sobre como um trabalho foi executado. Para obter mais informações, veja [Usar o Modo de Exibição de Execução de Vértice nas Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).

## <a name="next-steps"></a>Próximas etapas
* Para registrar em log as informações de diagnóstico, veja [Acessando os logs de diagnóstico para o Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)
* Para ver uma consulta mais complexa, consulte [Analisar logs de site usando a Análise Data Lake do Azure](data-lake-analytics-analyze-weblogs.md).
* Para usar o modo de exibição de execução de vértice, veja [Usar o Modo de Exibição de Execução de Vértice nas Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)

