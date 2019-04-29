---
title: Fluxos de dados IoT em tempo real usando o Stream Analytics
description: Marcas e dados de sensor de fluxos IoT com o processamento de dados em tempo real e Stream Analytics
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: b883dcf941683d6065c9b6ee5075d2a358f3452a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61481265"
---
# <a name="get-started-with-azure-stream-analytics-to-process-data-from-iot-devices"></a>Introdução ao Stream Analytics do Azure para processar dados de dispositivos IoT
Neste tutorial, você aprenderá a criar a lógica de processamento de fluxo para reunir dados dos dispositivos da Internet das Coisas (IoT). Usaremos um caso de uso real da Internet das coisas (IoT) para demonstrar como compilar uma solução rápida e econômica.

## <a name="prerequisites"></a>Pré-requisitos
* [Assinatura do Azure](https://azure.microsoft.com/pricing/free-trial/)
* Exemplos de arquivos de consulta e de dados que podem ser baixados de [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot)

## <a name="scenario"></a>Cenário
Contoso, que é uma empresa no espaço de automação industrial, automatizou completamente seu processo de fabricação. O maquinário desta fábrica tem sensores capazes de emitir fluxos de dados em tempo real. Nesse cenário, um gerente de chão de fábrica quer ter informações em tempo real dos dados de sensor para procurar por padrões e tomar ações com relação a eles. Nós usaremos a Stream Analytics Query Language (SAQL) sobre os dados do sensor para descobrir padrões interessantes no fluxo de entrada de dados.

Aqui, os dados estão sendo gerados de um dispositivo de tag de sensor da Texas Instruments. A carga de dados está no formato JSON e é semelhante ao seguinte:

```json
{
    "time": "2016-01-26T20:47:53.0000000",  
    "dspl": "sensorE",  
    "temp": 123,  
    "hmdt": 34  
}  
```

Em um cenário real, você poderia ter centenas desses sensores gerando eventos como um fluxo. De forma ideal, um dispositivo de gateway executaria um código para enviar por push esses eventos aos [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/) ou aos [Hubs IoT do Azure](https://azure.microsoft.com/services/iot-hub/). Seu trabalho do Stream Analytics receberia esses eventos dos Hubs de Eventos e executaria consultas de análise em tempo real nos fluxos. Em seguida, você poderia enviar os resultados a uma das [saídas com suporte](stream-analytics-define-outputs.md).

Para facilitar o uso, este guia de Introdução fornece um arquivo de dados de exemplo, capturado de dispositivos de tag de sensor reais. Você pode executar consultas nos dados de exemplo e ver os resultados. Nos tutoriais subsequentes, você aprenderá a conectar seu trabalho a entradas e saídas e implantá-lo para o serviço do Azure.

## <a name="create-a-stream-analytics-job"></a>Criar um trabalho de Stream Analytics
1. No [Portal do Azure](https://portal.azure.com), clique no sinal de adição e, em seguida, digite **STREAM ANALYTICS** na janela de texto à direita. Em seguida, selecione **Trabalho do Stream Analytics** na lista de resultados.
   
    ![Criar um novo trabalho do Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)
2. Insira um nome exclusivo de trabalho e verifique se a assinatura é a correta para seu trabalho. Em seguida, crie um novo grupo de recursos ou selecione um já existente em sua assinatura.
3. Selecione um local para seu trabalho. Para velocidade de processamento e redução do custo na transferência de dados, selecione o mesmo local do grupo de recursos e a conta de armazenamento pretendida é recomendada.
   
    ![Detalhes de Como criar um novo trabalho do Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.png)
   
   > [!NOTE]
   > Você deve criar essa conta de armazenamento apenas uma vez por região. Esse armazenamento será compartilhado entre todos os trabalhos do Stream Analytics que forem criados nessa região.
   > 
   > 
4. Marque a caixa para colocar o trabalho em seu painel e, em seguida, clique em **CRIAR**.
   
    ![Criação do trabalho do Stream Analytics em andamento](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03a.png)
5. Você deve ver uma mensagem 'Implantação iniciada...' exibida no canto superior direito da janela do navegador. Em breve será alterada para uma janela concluída conforme mostrado abaixo.
   
    ![Implantação do Stream Analytics com êxito](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03b.png)

## <a name="create-an-azure-stream-analytics-query"></a>Criar uma instância do Azure Stream Analytics
Depois que o trabalho é criado, é hora de abri-lo e criar uma consulta. Você pode acessar facilmente o trabalho clicando no bloco para ele.

![Bloco do trabalho do Stream Analytics no portal do Azure](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-04.png)

No painel **Topologia do trabalho**, clique na caixa **CONSULTA** para ir para o Editor de Consultas. O editor **CONSULTA** permite que você insira uma consulta T-SQL que realiza a transformação nos dados de eventos de entrada.

![Bloco de consulta de painel do Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

### <a name="query-archive-your-raw-data"></a>Consulta: Arquivar seus dados brutos
A forma mais simples de consulta é uma consulta passagem que arquiva todos os dados de entrada em suas saídas indicadas. Baixe o arquivo de dados de exemplo do [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot) para um local em seu computador. 

1. Cole a consulta do arquivo PassThrough.txt. 
   
    ![Cole a consulta no editor de consultas do Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06.png)
2. Clique nos três pontos ao lado de sua entrada e selecione a caixa **Carregar dados de exemplo do arquivo**.
   
    ![Escolha carregar dados de exemplo do arquivo](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06a.png)
3. Um painel abre à direita como resultado. Nele, selecione o arquivo de dados de HelloWorldASA-InputStream.json a partir de seu local de download e clique em **OK** na parte inferior do painel.
   
    ![Carregar um arquivo de dados de exemplo](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06b.png)
4. Em seguida, clique na engrenagem **Teste** na parte superior esquerda da janela e processe a sua consulta de teste no conjunto de dados de exemplo. Será aberta uma janela de resultados abaixo da consulta conforme o processamento é concluído.
   
    ![Resultados do teste para consulta do Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

### <a name="query-filter-the-data-based-on-a-condition"></a>Consulta: Filtrar os dados com base em uma condição
Vamos tentar filtrar os resultados com base em uma condição. Gostaríamos de mostrar resultados apenas dos eventos provenientes de "sensorA." A consulta está no arquivo Filtering.txt.

![Filtrar um fluxo de dados](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

Observe que a consulta, que diferencia maiúsculas de minúsculas, compara um valor de cadeia de caracteres. Clique na engrenagem **Teste** novamente para executar a consulta. A consulta deve retornar apenas 389 linhas de 1860 eventos.

![Segundo resultado do teste do teste de consulta](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-09.png)

### <a name="query-alert-to-trigger-a-business-workflow"></a>Consulta: Alerta para disparar o fluxo de trabalho de negócios
Vamos tornar nossa consulta mais detalhada. Para cada tipo de sensor, se você deseja monitorar a temperatura média por 30 segundos e exibir os resultados somente se a temperatura média estiver acima de 100 graus. Escreveremos a consulta a seguir e depois clicaremos em **Teste** para ver os resultados. A consulta está no arquivo ThresholdAlerting.txt.

![Consulta de filtro de 30 segundos](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

Agora, você pode ver os resultados que contêm apenas 245 linhas e nomes dos sensores nos quais a temperatura média é superior a 100. Esta consulta agrupa o fluxo de eventos por **dspl**, que é o nome de sensor, sobre uma **Janela em Cascata** de 30 segundos. Consultas temporais devem declarar como queremos que o tempo progrida. Usando a cláusula **TIMESTAMP BY**, especificamos a coluna **OUTPUTTIME** para associar tempos a todos os cálculos temporais. Para obter informações detalhadas, leia os artigos do MSDN sobre [Gerenciamento de tempo](https://msdn.microsoft.com/library/azure/mt582045.aspx) e [Funções de janelas](https://msdn.microsoft.com/library/azure/dn835019.aspx).

### <a name="query-detect-absence-of-events"></a>Consulta: Detectar ausência de eventos
Como podemos escrever uma consulta para localizar a falta de eventos de entrada? Vamos descobrir a última vez que um sensor enviou dados e, depois, não enviou mais eventos nos cinco segundos seguintes. A consulta está no arquivo AbsenceOfEvent.txt.

![Detectar ausência de eventos](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

Aqui, usamos uma junção **EXTERNA ESQUERDA** no mesmo fluxo de dados (autojunção). Para uma junção **INTERNA**, um resultado retorna somente quando uma correspondência é encontrada.  Mas para uma junção **EXTERNA ESQUERDA**, se um evento do lado esquerdo da junção for incomparável, uma linha com NULL para todas as colunas do lado direito retornará. Essa técnica é muito útil para localizar a ausência de eventos. Consulte a documentação do MSDN para saber mais sobre [JUNÇÃO](https://msdn.microsoft.com/library/azure/dn835026.aspx).

## <a name="conclusion"></a>Conclusão
O objetivo deste tutorial é demonstrar como escrever consultas de linguagem de consulta do Stream Analytics e ver os resultados no navegador. No entanto, isso está apenas começando. Você pode fazer muito mais com Stream Analytics. O Stream Analytics dá suporte a várias entradas e saídas e ainda pode usar as funções no Azure Machine Learning para torná-la uma ferramenta robusta de análise de fluxos de dados. Você pode começar a explorar mais sobre o Stream Analytics usando nosso [Mapa de aprendizagem](https://docs.microsoft.com/azure/stream-analytics/). Para saber mais sobre como escrever consultas, leia o artigo sobre [padrões comuns de consulta](stream-analytics-stream-analytics-query-patterns.md).

