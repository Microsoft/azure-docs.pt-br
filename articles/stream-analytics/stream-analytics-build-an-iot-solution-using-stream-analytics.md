---
title: Compilar uma solução de IoT usando o Azure Stream Analytics
description: Tutorial de introdução da solução de IoT Stream Analytics de um cenário de pedágio
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 4b250a5e14ab37553d93453d05f8ff388bf1ba84
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620514"
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Compilar uma solução de IoT usando o Stream Analytics

## <a name="introduction"></a>Introdução
Nessa solução, você aprende como usar o Azure Stream Analytics para obter insights em tempo real dos dados. Os desenvolvedores podem facilmente combinar fluxos de dados, como fluxos de cliques, logs e eventos gerados pelo dispositivo, com registros históricos ou dados de referência para gerar insights comerciais. Como um serviço de computação de fluxo em tempo real totalmente gerenciado e hospedado no Microsoft Azure, o Stream Analytics do Azure fornece resiliência interna, baixa latência e escalabilidade para você colocar tudo em funcionamento em minutos.

Após concluir essa solução, você será capaz de:

* Familiarizar-se com o portal do Stream Analytics do Azure.
* Configurar e implantar um trabalho de transmissão.
* Articular problemas do mundo real e resolvê-los usando a linguagem de consulta do Stream Analytics.
* Desenvolver com confiança soluções de transmissão para seus clientes usando o Stream Analytics do Azure.
* Use o monitoramento e experiência de log para solucionar problemas.

## <a name="prerequisites"></a>Pré-requisitos
Os pré-requisitos a seguir serão necessários para concluir essa solução:
* Uma [assinatura do Azure](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-introduction-hello-toll"></a>Introdução ao cenário: "Olá, pedágio!"
Uma praça de pedágio é um fenômeno comum. Você se depara com elas em várias estradas, pontes e túneis em todo o mundo. Cada praça de pedágio tem várias cabines do pedágio. Em cabines manuais, você para e paga o pedágio para um atendente. Em cabines automatizadas, um sensor sobre cada uma das cabines lê um cartão RFID fixado ao para-brisa do veículo conforme você passa pela cabine. É fácil visualizar a passagem dos veículos por essas estações de pedágio como uma transmissão de eventos sobre quais operações interessantes podem ser executadas.

![Imagem de carros em cabines de pedágio](media/stream-analytics-build-an-iot-solution-using-stream-analytics/cars-in-toll-booth.jpg)

## <a name="incoming-data"></a>Dados de entrada
Essa solução funciona com dois fluxos de dados. Sensores instalados na entrada e na saída das praças de pedágio produzem o primeiro fluxo. O segundo fluxo é um conjunto de dados de pesquisa estático que contém dados de registro dos veículos.

### <a name="entry-data-stream"></a>Fluxo de dados de entrada
O fluxo de dados de entrada contém informações sobre os carros que entram nas praças de pedágio. Os eventos de dados de saída são transmitidos em tempo real para uma fila do Hub de Eventos a partir de um aplicativo Web incluído no aplicativo de exemplo.

| TollID | EntryTime | PlacaDeCarro | Estado | Faça | Modelo | VehicleType | VehicleWeight | Pedágio | Marca |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |JNB 7001 |NOVA IORQUE |Honda |CRV |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |1001 YXZ |NOVA IORQUE |Toyota |Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |ABC 1004 |CT |Ford |Taurus |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |1003 XYZ |CT |Toyota |Corolla |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |1007 BNJ |NOVA IORQUE |Honda |CRV |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |CDE 1007 |NJ |Toyota |4x4 |1 |0 |6 |321987654 |

Aqui está uma breve descrição das colunas:

| Coluna | DESCRIÇÃO |
| --- | --- |
| TollID |A ID da cabine de pedágio que identifica exclusivamente uma cabine de pedágio |
| EntryTime |A data e hora da entrada do veículo na cabine de pedágio no horário UTC |
| PlacaDeCarro |O número da placa de licença do veículo |
| Estado |Um estado nos Estados Unidos |
| Faça |O fabricante do automóvel |
| Modelo |O número do modelo do automóvel |
| VehicleType |1 para veículos de passageiros ou 2 para veículos comerciais |
| WeightType |Peso do veículo em toneladas; 0 para veículos de passageiros |
| Pedágio |Valor do pedágio em dólares americanos |
| Marca |A e-Tag no automóvel que automatiza o pagamento; deixado em branco quando o pagamento é feito manualmente |

### <a name="exit-data-stream"></a>Fluxo de dados de saída
O fluxo de dados de saída contém informações sobre os carros que estão saindo da praça de pedágio. Os eventos de dados de saída são transmitidos em tempo real para uma fila do Hub de Eventos a partir de um aplicativo Web incluído no aplicativo de exemplo.

| **TollId** | **ExitTime** | **PlacaDeCarro** |
| --- | --- | --- |
| 1 |2014-09-10T12:03:00.0000000Z |JNB 7001 |
| 1 |2014-09-10T12:03:00.0000000Z |1001 YXZ |
| 3 |2014-09-10T12:04:00.0000000Z |ABC 1004 |
| 2 |2014-09-10T12:07:00.0000000Z |1003 XYZ |
| 1 |2014-09-10T12:08:00.0000000Z |1007 BNJ |
| 2 |2014-09-10T12:07:00.0000000Z |CDE 1007 |

Aqui está uma breve descrição das colunas:

| Coluna | DESCRIÇÃO |
| --- | --- |
| TollID |A ID da cabine de pedágio que identifica exclusivamente uma cabine de pedágio |
| ExitTime |A data e hora de saída do veículo da cabine de pedágio no horário UTC |
| PlacaDeCarro |O número da placa de licença do veículo |

### <a name="commercial-vehicle-registration-data"></a>Dados de registro de veículo comercial
A solução usa um instantâneo estático de um banco de dados de registro de veículos comerciais. Esses dados são salvos como um arquivo JSON no armazenamento de blobs do Azure, incluído no exemplo.

| PlacaDeCarro | RegistrationId | Expirado |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| BAC 1005 |876133137 |1 |
| RIV 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

Aqui está uma breve descrição das colunas:

| Coluna | DESCRIÇÃO |
| --- | --- |
| PlacaDeCarro |O número da placa de licença do veículo |
| RegistrationId |A ID de registro do veículo |
| Expirado |O status do registro do veículo: 0, se o registro do veículo estiver ativo; 1, se o registro estiver vencido |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Configurar o ambiente para o Stream Analytics do Azure
Para completar essa solução, você precisará de uma assinatura do Microsoft Azure. Se não tiver uma conta do Azure, [solicite uma versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

Siga as etapas na seção "Limpar sua conta do Azure" no final deste artigo para que você possa fazer o melhor uso de seu crédito do Azure.

## <a name="deploy-the-sample"></a>Implantar o exemplo
Há vários recursos que podem ser facilmente implantados em um grupo de recursos com alguns cliques. A definição da solução está hospedada no repositório GitHub em [https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp).

### <a name="deploy-the-tollapp-template-in-the-azure-portal"></a>Implantar o modelo do TollApp no Portal do Azure
1. Para implantar o ambiente do TollApp no Azure, use esse link para [Implantar modelo do TollApp do Azure ](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-stream-analytics%2Fmaster%2FSamples%2FTollApp%2FVSProjects%2FTollAppDeployment%2Fazuredeploy.json).

2. Entre no Portal do Azure, se solicitado.

3. Escolha a assinatura na qual os vários recursos são cobrados.

4. Especifique um novo grupo de recursos, com um nome exclusivo, por exemplo, `MyTollBooth`.

5. Selecione um local do Azure.

6. Especifique um **Intervalo** como um número de segundos. Esse valor é usado no aplicativo Web de exemplo para informar com que frequência enviar os dados para o Hub de Eventos.

7. **Marque** para concordar com os termos e condições.

8. Selecione **Fixar no painel** para que seja possível localizar os recursos com facilidade posteriormente.

9. Selecione **Comprar** para implantar o modelo de exemplo.

10. Após alguns instantes, uma notificação será exibida para confirmar a **Implantação com êxito**.

### <a name="review-the-azure-stream-analytics-tollapp-resources"></a>Examinar os recursos do TollApp do Azure Stream Analytics

1. Entre no Portal do Azure

2. Localize o grupo de recursos que você nomeou na seção anterior.

3. Verifique se os recursos a seguir estão listados no grupo de recursos:
   - Uma conta do Azure Cosmos DB
   - Um trabalho do Azure Stream Analytics
   - Uma conta de Armazenamento do Microsoft Azure
   - Um Hub de Eventos do Azure
   - Dois aplicativos Web

## <a name="examine-the-sample-tollapp-job"></a>Examinar o trabalho do TollApp de exemplo
1. A partir do grupo de recursos na seção anterior, selecione o trabalho de streaming do Azure Stream Analytics, iniciando com o nome **tollapp** (o nome contém caracteres aleatórios para exclusividade).

2. Na página **Visão geral** do trabalho, observe a caixa **Consulta** para visualizar a sintaxe de consulta.

   ```sql
   SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
   INTO CosmosDB
   FROM EntryStream TIMESTAMP BY EntryTime
   GROUP BY TUMBLINGWINDOW(minute, 3), TollId
   ```

   Para parafrasear a intenção da consulta, digamos que você precise contar a quantidade de veículos que entra no pedágio. Como um pedágio tem um fluxo contínuo de veículos entrando, esses são eventos de entrada análogos a um fluxo que nunca é interrompido. Para quantificar o fluxo, é necessário definir um "período de tempo" para medida suplementar. Vamos aprimorar ainda mais a questão: "Quantos veículos entram em um pedágio a cada três minutos?" Isso é conhecido como contagem em cascata.

   Como você pode ver, o Stream Analytics do Azure usa uma linguagem de consulta parecida com SQL e adiciona algumas extensões para especificar aspectos da consulta relacionados ao tempo.  Para obter mais detalhes, leia sobre os constructos [Gerenciamento de Tempo](https://docs.microsoft.com/stream-analytics-query/time-management-azure-stream-analytics) e [Janelas](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics) usados na consulta.

3. Examine as entradas do trabalho de exemplo do TollApp. Apenas a entrada EntryStream é usada na consulta atual.
   - A entrada **EntryStream** é uma conexão do Hub de Eventos que coloca os dados em fila, representando cada vez que um carro entra em um pedágio na rodovia. Um aplicativo Web que faz parte do exemplo está criando os eventos e esses dados são enfileirados nesse Hub de Eventos. Observe que essa entrada é consultada na cláusula FROM da consulta de streaming.
   - A entrada **ExitStream** é uma conexão do Hub de Eventos que coloca os dados em fila, representando cada vez que um carro sai de um pedágio na rodovia. Essa entrada de streaming é usada em variações posteriores da sintaxe de consulta.
   - O **Registro** é uma conexão do Armazenamento de Blobs do Azure, apontando para um arquivo registration.json estático usado para pesquisas, conforme necessário. Essa entrada de dados de referência é usada em variações posteriores da sintaxe de consulta.

4. Examinar as Saídas do trabalho de exemplo do TollApp.
   - **O cosmos DB** saída é um contêiner de banco de dados do Cosmos que recebe eventos de coletor de saída. Observe que essa saída é usada na cláusula INTO da consulta de streaming.

## <a name="start-the-tollapp-streaming-job"></a>Iniciar o trabalho de streaming do TollApp
Siga estas etapas para iniciar o trabalho de streaming:

1. Na página **Visão geral** do trabalho, selecione **Iniciar**.

2. No painel **Iniciar trabalho**, selecione **Agora**.

3. Após alguns instantes, quando o trabalho estiver em execução, na página **Visão geral** do trabalho de streaming, exiba o gráfico de **Monitoramento**. O gráfico deve mostrar vários milhares de eventos de entrada e dezenas de eventos de saída.

## <a name="review-the-cosmosdb-output-data"></a>Examinar os dados de saída CosmosDB
1. Localize o grupo de recursos que contém os recursos do TollApp.

2. Selecione a Conta do Azure Cosmos DB com o padrão de nome **tollapp\<random\>-cosmos**.

3. Selecione o cabeçalho **Data Explorer** para abrir a página do Data Explorer.

4. Expanda o **tollAppDatabase** > **tollAppCollection** > **Documents**.

5. Na lista de IDs, vários documentos serão mostrados quando a saída estiver disponível.

6. Selecione cada ID para examinar o documento JSON. Observe cada tollid, o tempo de fim da janela e a contagem de carros daquela janela.

7. Após mais três minutos, outro conjunto de quatro documentos estará disponível, um documento por tollid.


## <a name="report-total-time-for-each-car"></a>Relatar o tempo total de cada carro
O tempo médio necessário para um carro passar pela cabine de pedágio ajuda a avaliar a eficiência do processo e a experiência do cliente.

Para localizar o tempo total, faça a junção do fluxo EntryTime com o fluxo ExitTime. Junte os dois fluxos de entrada nas colunas TollId e LicencePlate de correspondência igual. O operador **JOIN** exige a especificação de um espaço de manobra temporal que descreve a diferença de tempo aceitável entre os eventos associados. Use a função **DATEDIFF** para especificar que os eventos não deverão ter mais de 15 minutos um do outro. Aplique também a função **DATEDIFF** para sair e os tempos de entrada para calcular o tempo real que um carro passa na estação de pedágio. Observe a diferença do uso da **DATEDIFF** quando usada em uma instrução **SELECT** em comparação com uma condição **JOIN**.

```sql
SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute, EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN ExitStream TIMESTAMP BY ExitTime
ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15
```

### <a name="to-update-the-tollapp-streaming-job-query-syntax"></a>Para atualizar a sintaxe de consulta do trabalho de streaming do TollApp:

1. Na página **Visão geral** do trabalho, selecione **Parar**.

2. Aguarde alguns instantes pela notificação de que o trabalho parou.

3. No cabeçalho JOB TOPOLOGY, selecione **< > Consulta**

4. Cole a consulta SQL de streaming ajustada.

5. Selecione **Salvar** para salvar a consulta. Confirme **Sim** para salvar as alterações.

6. Na página **Visão geral** do trabalho, selecione **Iniciar**.

7. No painel **Iniciar trabalho**, selecione **Agora**.

### <a name="review-the-total-time-in-the-output"></a>Examinar o tempo total na saída
Repita as etapas na seção anterior para examinar os dados de saída do CosmosDB do trabalho de streaming. Examinar os últimos documentos JSON.

Por exemplo, este documento mostra um carro de exemplo com uma determinada placa de licença, a hora de entrada e hora de saída e o campo de duração em minutos calculado do DATEDIFF, mostrando a duração da cabine como dois minutos:
```JSON
{
    "tollid": 4,
    "entrytime": "2018-04-05T06:51:39.0491173Z",
    "exittime": "2018-04-05T06:53:09.0491173Z",
    "licenseplate": "JVR 9425",
    "durationinminutes": 2,
    "id": "ff52eb25-d580-7566-2879-1f52bba6601e",
    "_rid": "+8E4AI1DZgBjAAAAAAAAAA==",
    "_self": "dbs/+8E4AA==/colls/+8E4AI1DZgA=/docs/+8E4AI1DZgBjAAAAAAAAAA==/",
    "_etag": "\"ad02f6b8-0000-0000-0000-5ac5c8330000\"",
    "_attachments": "attachments/",
    "_ts": 1522911283
}
```

## <a name="report-vehicles-with-expired-registration"></a>Relatar veículos com registro expirado
O Azure Stream Analytics pode usar instantâneos estáticos de dados de referência para fazer a junção com fluxos de dados temporais. Para demonstrar essa funcionalidade, use a pergunta de exemplo a seguir. A entrada de Registro é um arquivo json de blob estático que lista as expirações de marcas da licença. Associando a placa de licença, os dados de referência são comparados a cada veículo que passa pelo pedágio.

Se um veículo comercial estiver registrado na empresa de pedágio, ele poderá passar pela cabine sem ser parado para inspeção. Use a tabela de consulta de registro para identificar todos os veículos comerciais que possuem registros expirados.

```sql
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

1. Repita as etapas na seção anterior para atualizar a sintaxe de consulta do trabalho de streaming do TollApp.

2. Repita as etapas na seção anterior para examinar os dados de saída do CosmosDB do trabalho de streaming.

Saída de exemplo:
```json
    {
        "entrytime": "2018-04-05T08:01:28.0252168Z",
        "licenseplate": "GMT 3221",
        "tollid": 1,
        "registrationid": "763220582",
        "id": "47db0535-9716-4eb2-db58-de7886966cbf",
        "_rid": "y+F8AJ9QWACSAQAAAAAAAA==",
        "_self": "dbs/y+F8AA==/colls/y+F8AJ9QWAA=/docs/y+F8AJ9QWACSAQAAAAAAAA==/",
        "_etag": "\"88007d8d-0000-0000-0000-5ac5d7e20000\"",
        "_attachments": "attachments/",
        "_ts": 1522915298
    }
```

## <a name="scale-out-the-job"></a>Escalar horizontalmente o trabalho
O Azure Stream Analytics foi projetado para escalar de maneira elástica, de modo que seja possível manipular grandes volumes de dados. A consulta do Azure Stream Analytics pode usar uma cláusula **PARTITION BY** para informar ao sistema que essa etapa escala horizontalmente. **PartitionId** é uma coluna especial adicionada pelo sistema e corresponde à ID da partição de entrada (hub de evento).

Para escalar horizontalmente a consulta para partições, edite a sintaxe de consulta para o código a seguir:
```sql
SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
INTO CosmosDB
FROM EntryStream
TIMESTAMP BY EntryTime
PARTITION BY PartitionId
GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId
```

Para escalar verticalmente o trabalho de streaming para mais unidades de streaming:

1. **Pare** o trabalho atual.

2. Atualize a sintaxe de consulta na página **< > Consulta** e salve as alterações.

3. No cabeçalho CONFIGURE no trabalho de streaming, selecione **Escalar**.

4. Deslize o controle deslizante de **Unidades de streaming** de 1 a 6. As unidades de streaming definem a quantidade de potência de computação que o trabalho pode receber. Clique em **Salvar**.

5. **Inicie** o trabalho de streaming para demonstrar a escala adicional. O Azure Stream Analytics distribui o trabalho em mais recursos de computação e obtém melhor taxa de transferência, particionando o trabalho entre os recursos usando a coluna designada na cláusula PARTITION BY.

## <a name="monitor-the-job"></a>Monitorar trabalho
A área **MONITORAR** contém estatísticas sobre o trabalho em execução. A primeira configuração é necessária para usar a conta de armazenamento na mesma região (início do nome como o restante deste documento).

![Monitoramento de trabalhos do Azure Stream Analytics](media/stream-analytics-build-an-iot-solution-using-stream-analytics/stream-analytics-job-monitoring.png)

Você também pode acessar **Logs de Atividade** na área **Configurações** do painel do trabalho.

## <a name="clean-up-the-tollapp-resources"></a>Limpar os recursos do TollApp
1. Interrompa o trabalho do Stream Analytics do portal do Azure.

2. Localize o grupo de recursos que contém oito recursos relacionados ao modelo do TollApp.

3. Selecione **Excluir grupo de recursos**. Digite o nome do grupo de recursos para confirmar a exclusão.

## <a name="conclusion"></a>Conclusão
Esta solução apresentou o serviço do Azure Stream Analytics. Ele demonstrou como configurar entradas e saídas para o trabalho do Stream Analytics. Usando o cenário de Toll Data, a solução explicou tipos comuns de problemas que surgem no espaço de dados em movimento e como eles podem ser resolvidos com consultas simples como SQL no Azure Stream Analytics. A solução descreveu constructos de extensão de SQL para trabalhar com dados temporais. Ele mostrou como combinar transmissões de dados, como aprimorar a transmissão de dados com os dados de referência estáticos e como escalar horizontalmente uma consulta para aumentar a taxa de transferência.

Embora essa solução forneça uma boa introdução, ela não está totalmente completa. Você pode encontrar mais padrões de consulta usando a linguagem SAQL em [Exemplos de consulta para padrões de uso do Stream Analytics](stream-analytics-stream-analytics-query-patterns.md).
