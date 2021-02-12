---
title: Testar um trabalho do Azure Stream Analytics com dados de exemplo
description: Este artigo descreve como usar o portal do Azure para testar um trabalho do Azure Stream Analytics, uma entrada de exemplo e fazer o upload de dados de exemplo.
author: ajetasin
ms.author: ajetasi
ms.service: stream-analytics
ms.topic: how-to
ms.date: 3/6/2020
ms.custom: seodec18
ms.openlocfilehash: eff9103f476e6074ab46198ff8cc78588675569f
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020036"
---
# <a name="test-an-azure-stream-analytics-job-in-the-portal"></a>Testar um trabalho de Azure Stream Analytics no portal

No Azure Stream Analytics, você pode testar sua consulta sem iniciar ou parar seu trabalho. Você pode testar consultas em dados de entrada de suas fontes de streaming ou carregar dados de exemplo de um arquivo local no portal do Azure. Você também pode testar as consultas localmente de seus dados de exemplo locais ou dados dinâmicos no [Visual Studio](stream-analytics-live-data-local-testing.md) e [Visual Studio Code](visual-studio-code-local-run-live-input.md).

## <a name="automatically-sample-incoming-data-from-input"></a>Amostragem automática de dados de entrada de entrada

Azure Stream Analytics busca automaticamente eventos de suas entradas de streaming. Você pode executar consultas no exemplo padrão ou definir um intervalo de tempo específico para o exemplo.

1. Entre no portal do Azure.

2. Localize e selecione seu trabalho de Stream Analytics existente.

3. Na página de trabalho do Stream Analytics, sob o título **Topologia de Trabalho**, selecione **Consulta** para abrir a janela Editor de consultas. 

4. Para ver uma lista de exemplos de eventos de entrada, selecione o ícone entrada com arquivo e os eventos de exemplo serão exibidos automaticamente na **visualização de entrada**.

   a. O tipo de serialização para seus dados será detectado automaticamente se seu JSON ou CSV. Você pode alterá-lo manualmente, bem como JSON, CSV, AVRO alterando a opção no menu suspenso.
    
   b. Use o seletor para exibir seus dados em formato de **tabela** ou **bruto** .
    
   c. Se os dados mostrados não estiverem atuais, selecione **Atualizar** para ver os eventos mais recentes.

   A tabela a seguir é um exemplo de dados no **formato de tabela**:

   ![Azure Stream Analytics entrada de exemplo no formato de tabela](./media/stream-analytics-test-query/asa-sample-table.png)

   A tabela a seguir é um exemplo de dados no **formato bruto**:

   ![Azure Stream Analytics entrada de exemplo no formato bruto](./media/stream-analytics-test-query/asa-sample-raw.png)

5. Para testar sua consulta com os dados de entrada, selecione **testar consulta**. Os resultados aparecem na guia **resultados do teste** . Você também pode selecionar **baixar resultados** para baixar os resultados.

   ![Azure Stream Analytics resultados da consulta de teste de exemplo](./media/stream-analytics-test-query/asa-test-query.png)

6. Para testar sua consulta em um intervalo de tempo específico de eventos de entrada, selecione **selecionar intervalo de tempo**.
   
   ![Azure Stream Analytics intervalo de tempo para eventos de exemplo de entrada](./media/stream-analytics-test-query/asa-select-time-range.png)

7. Defina o intervalo de tempo dos eventos que você deseja usar para testar sua consulta e selecione **exemplo**. Dentro desse período, você pode recuperar até 1000 eventos ou 1 MB, o que vier primeiro.

   ![Azure Stream Analytics definir intervalo de tempo para eventos de exemplo de entrada](./media/stream-analytics-test-query/asa-set-time-range.png)

8. Depois que os eventos são amostrados para o intervalo de tempo selecionado, eles aparecem na guia **visualização de entrada** .

   ![Azure Stream Analytics exibir resultados de teste](./media/stream-analytics-test-query/asa-view-test-results.png)

9. Selecione **Redefinir** para ver a lista de exemplos de eventos de entrada. Se você selecionar **Redefinir**, sua seleção de intervalo de tempo será perdida. Selecione **testar consulta** para testar sua consulta e examine os resultados na guia **resultados do teste** .

10. Ao fazer alterações em sua consulta, selecione **Salvar consulta** para testar a nova lógica de consulta. Isso permite que você modifique a consulta iterativamente e teste-a novamente para ver como a saída é alterada.

11. Depois de verificar os resultados mostrados no navegador, você estará pronto para **Iniciar** o trabalho.

## <a name="upload-sample-data-from-a-local-file"></a>Carregar dados de exemplo de um arquivo local

Em vez de usar dados dinâmicos, você pode usar dados de exemplo de um arquivo local para testar sua consulta de Azure Stream Analytics.

1. Entre no portal do Azure.
   
2. Localize o trabalho existente do Stream Analytics e selecione-o.

3. Na página de trabalho do Stream Analytics, sob o título **Topologia de Trabalho**, selecione **Consulta** para abrir a janela Editor de consultas.

4. Para testar sua consulta com um arquivo local, selecione **carregar entrada de exemplo** na guia **visualização de entrada** . 

   ![Captura de tela mostra a opção carregar entrada de exemplo.](./media/stream-analytics-test-query/asa-upload-sample-file.png)

5. Carregue seu arquivo local para testar a consulta. Você só pode carregar arquivos com os formatos JSON, CSV ou AVRO. Selecione **OK**.

   ![Captura de tela mostra a caixa de diálogo carregar dados de exemplo, em que é possível selecionar um arquivo.](./media/stream-analytics-test-query/asa-upload-sample-json-file.png)

6. Assim que você carregar o arquivo, também poderá ver o conteúdo do arquivo no formato como uma tabela ou no formato bruto. Se você selecionar **Redefinir**, os dados de exemplo serão retornados para os dados de entrada de entrada explicados na seção anterior. Você pode carregar qualquer outro arquivo para testar a consulta a qualquer momento.

7. Selecione **testar consulta** para testar sua consulta em relação ao arquivo de exemplo carregado.

8. Os resultados de teste são mostrados com base em sua consulta. Você pode alterar a consulta e selecionar **Salvar consulta** para testar a nova lógica de consulta. Isso permite que você modifique a consulta iterativamente e teste-a novamente para ver como a saída é alterada.

9. Quando você usa várias saídas na consulta, os resultados são mostrados com base na saída selecionada. 

   ![Azure Stream Analytics saída selecionada](./media/stream-analytics-test-query/asa-sample-test-selected-output.png)

10. Depois de verificar os resultados mostrados no navegador, você pode **Iniciar** o trabalho.

## <a name="limitations"></a>Limitações

1.  A política de tempo não tem suporte no teste do portal:

    * Fora de ordem: todos os eventos de entrada serão ordenados.
    * Chegada tardia: não haverá evento de chegada tardia, pois Stream Analytics só poderá usar dados existentes para teste.
   
2.  Não há suporte para UDF em C#.

3.  Todos os testes serão executados com um trabalho que tenha uma unidade de streaming.

4.  O tamanho do tempo limite é de um minuto. Portanto, qualquer consulta com um tamanho de janela maior que um minuto não pode obter dados.

5.  Não há suporte para Machine Learning.

6. A API de dados de exemplo é limitada após cinco solicitações em uma janela de 15 minutos. Após o final da janela de 15 minutos, você pode fazer mais solicitações de dados de exemplo. Essa limitação é aplicada no nível de assinatura.

## <a name="troubleshooting"></a>Solução de problemas

1.  Se você receber esse erro "houve um problema de conectividade de rede ao buscar os resultados. Verifique suas configurações de rede e firewall. ", siga as etapas abaixo:

  * Para verificar a conexão com o serviço, abra [https://queryruntime.azurestreamanalytics.com/api/home/index](https://queryruntime.azurestreamanalytics.com/api/home/index) em um navegador. Se você não puder abrir este link, atualize suas configurações de firewall.
  
2. Se você receber esse erro "o tamanho da solicitação é muito grande. Reduza o tamanho dos dados de entrada e tente novamente. ", siga as etapas abaixo:

  * Reduzir o tamanho de entrada – teste sua consulta com um arquivo de exemplo de tamanho menor ou com um intervalo de tempo menor.
  * Reduzir o tamanho da consulta – para testar uma seleção de consulta, selecione uma parte da consulta e clique em **testar consulta selecionada**.


## <a name="next-steps"></a>Próximas etapas
* [Crie uma solução de IOT usando Stream Analytics](./stream-analytics-build-an-iot-solution-using-stream-analytics.md): Este tutorial orientará você a criar uma solução de ponta a ponta com um gerador de dados que simulará o tráfego em um estande de Tarifa.

* [Referência de Linguagem de Consulta do Stream Analytics do Azure](/stream-analytics-query/stream-analytics-query-language-reference)

* [Exemplos de consulta para padrões de uso do Stream Analytics](stream-analytics-stream-analytics-query-patterns.md)

* [Entender as entradas para o Azure Stream Analytics](stream-analytics-add-inputs.md)

* [Entender as saídas do Azure Stream Analytics](stream-analytics-define-outputs.md)
