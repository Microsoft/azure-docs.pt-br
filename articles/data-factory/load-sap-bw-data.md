---
title: Carregar dados do SAP Business Warehouse
description: Usar Azure Data Factory para copiar dados do SAP Business Warehouse (BW)
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/22/2019
ms.openlocfilehash: 3dabb6d5df0a74cc7ae2fb8b381ad9e0dfe04e63
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370692"
---
# <a name="copy-data-from-sap-business-warehouse-by-using-azure-data-factory"></a>Copiar dados do SAP Business Warehouse usando Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo mostra como usar Azure Data Factory para copiar dados do SAP Business Warehouse (BW) via hub aberto para Azure Data Lake Storage Gen2. Você pode usar um processo semelhante para copiar dados para outros [armazenamentos de dados de coletor com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

> [!TIP]
> Para obter informações gerais sobre como copiar dados de SAP BW, incluindo SAP BW integração de Hub aberta e fluxo de extração de Delta, consulte [copiar dados do SAP Business Warehouse via hub aberto usando Azure data Factory](connector-sap-business-warehouse-open-hub.md).

## <a name="prerequisites"></a>Pré-requisitos

- **Azure data Factory**: se você não tiver uma, siga as etapas para [criar uma data Factory](quickstart-create-data-factory-portal.md#create-a-data-factory).

- **SAP BW Ohd (destino de Hub aberto) com o tipo de destino "tabela de banco de dados"**: para criar um Ohd ou para verificar se o Ohd está configurado corretamente para integração de data Factory, consulte a seção [SAP BW configurações de destino do Hub aberto](#sap-bw-open-hub-destination-configurations) deste artigo.

- **O usuário SAP BW precisa das seguintes permissões**:

  - Autorização para RFC (chamadas de função remota) e SAP BW.
  - Permissões para a atividade "executar" do objeto de autorização de **S_SDSAUTH** .

- **Um [ir (Integration Runtime) auto-hospedado](concepts-integration-runtime.md#self-hosted-integration-runtime) com o conector do SAP .net 3,0**. Siga estas etapas de configuração:

  1. Instale e registre o Integration Runtime de hospedagem interna, versão 3,13 ou posterior. (Esse processo é descrito posteriormente neste artigo.)

  2. Baixe o [conector SAP de 64 bits para Microsoft .NET 3,0](https://support.sap.com/en/product/connectors/msnet.html) do site da SAP e instale-o no mesmo computador que o ir hospedado internamente. Durante a instalação, certifique-se de selecionar **instalar assemblies no GAC** na caixa de diálogo **etapas de configuração opcionais** , como mostra a imagem a seguir:

     ![Caixa de diálogo Configurar conector do SAP .NET](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="do-a-full-copy-from-sap-bw-open-hub"></a>Faça uma cópia completa do Hub SAP BW Open

No portal do Azure, acesse seu data factory. Selecione **criar & monitor** para abrir a interface do usuário do data Factory em uma guia separada.

1. Na página **vamos começar** , selecione **copiar dados** para abrir a ferramenta copiar dados.

2. Na página **Propriedades** , especifique um **nome de tarefa** e, em seguida, selecione **Avançar**.

3. Na página **armazenamento de dados de origem** , selecione **+ criar nova conexão**. Selecione **SAP BW Hub aberto** na galeria do conector e selecione **continuar**. Para filtrar os conectores, você pode digitar **SAP** na caixa de pesquisa.

4. Na página **especificar conexão de Hub aberta SAP BW** , siga estas etapas para criar uma nova conexão.

   ![Criar SAP BW página de serviço vinculado ao Hub aberto](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. Na lista **conectar via tempo de execução de integração** , selecione um ir para hospedagem interna existente. Ou então, escolha criar um se você ainda não tiver um.

      Para criar um novo IR de hospedagem interna, selecione **+ novo** e, em seguida, selecione **auto-hospedado**. Insira um **nome** e, em seguida, selecione **Avançar**. Selecione **instalação expressa** para instalar no computador atual ou siga as etapas de **configuração manual** que são fornecidas.

      Conforme mencionado em [pré-requisitos](#prerequisites), verifique se você tem o conector SAP para Microsoft .net 3,0 instalado no mesmo computador em que o ir auto-hospedado está em execução.

   2. Preencha o nome do **servidor** SAP BW, **o número do sistema**, a ID do **cliente,** o **idioma** (se diferente de **en**), o **nome de usuário** e a **senha**.

   3. Selecione **testar conexão** para validar as configurações e, em seguida, selecione **concluir**.

   4. Uma nova conexão é criada. Selecione **Avançar**.

5. Na página **selecionar destinos de Hub abertos** , procure os destinos de Hub abertos que estão disponíveis em seu SAP BW. Selecione o OHD para copiar os dados e, em seguida, selecione **Avançar**.

   ![Selecione SAP BW tabela de destino de Hub aberto](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. Especifique um filtro, se você precisar de um. Se o seu OHD contiver apenas dados de uma única execução do DTP (processo de transferência de dados) com uma única ID de solicitação, ou se você tiver certeza de que o DTP está concluído e deseja copiar os dados, desmarque a caixa de seleção **excluir última solicitação** .

   Saiba mais sobre essas configurações na seção [SAP BW configurações de destino do Hub aberto](#sap-bw-open-hub-destination-configurations) deste artigo. Selecione **validar** para verificar se os dados serão retornados. Em seguida, selecione **Avançar**.

   ![Configurar SAP BW filtro de Hub aberto](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. Na página **armazenamento de dados de destino** , selecione **+ criar nova conexão**  >  **Azure data Lake Storage Gen2**  >  **continuar**.

8. Na página **especificar conexão Azure data Lake Storage** , siga estas etapas para criar uma conexão.

   ![Criar uma página de serviço vinculado ADLS Gen2](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. Selecione sua conta com capacidade de Data Lake Storage Gen2 na lista suspensa **nome** .
   2. Selecione **Concluir** para criar a conexão. Em seguida, selecione **Avançar**.

9. Na página **escolher o arquivo de saída ou a pasta** , insira **copyfromopenhub** como o nome da pasta de saída. Em seguida, selecione **Avançar**.

   ![Página Escolher pasta de saída](media/load-sap-bw-data/choose-output-folder.png)

10. Na página **configuração de formato de arquivo** , selecione **Avançar** para usar as configurações padrão.

    ![Página especificar formato do coletor](media/load-sap-bw-data/specify-sink-format.png)

11. Na página **configurações** , expanda **configurações de desempenho**. Insira um valor para o **grau de paralelismo de cópia** , como 5, para carregar de SAP BW em paralelo. Em seguida, selecione **Avançar**.

    ![Definir configurações de cópia](media/load-sap-bw-data/configure-copy-settings.png)

12. Na página de **Resumo** , analise as configurações. Em seguida, selecione **Avançar**.

13. Na página **implantação** , selecione **Monitor** para monitorar o pipeline.

    ![Página Implantação](media/load-sap-bw-data/deployment.png)

14. Observe que a guia **Monitor** no lado esquerdo da página é selecionada automaticamente. A coluna **ações** inclui links para exibir os detalhes da execução da atividade e para executar novamente o pipeline.

    ![Exibição de monitoramento de pipeline](media/load-sap-bw-data/pipeline-monitoring.png)

15. Para exibir as execuções de atividade que estão associadas à execução do pipeline, selecione **Exibir execuções de atividade** na coluna **ações** . Há apenas uma atividade (atividade de cópia) no pipeline. Assim, você vê apenas uma entrada. Para voltar para a exibição pipeline – execuções, selecione o link **pipelines** na parte superior. Selecione **Atualizar** para atualizar a lista.

    ![Tela de monitoramento de atividade](media/load-sap-bw-data/activity-monitoring.png)

16. Para monitorar os detalhes de execução de cada atividade de cópia, selecione o link **detalhes** , que é um ícone de óculos abaixo de **ações** no modo de exibição atividade-monitoramento. Os detalhes disponíveis incluem o volume de dados copiado da origem para o coletor, a taxa de transferência de dados, as etapas de execução e a duração e as configurações usadas.

    ![Detalhes do monitoramento de atividades](media/load-sap-bw-data/activity-monitoring-details.png)

17. Para exibir a **ID de solicitação máxima**, volte para o modo de exibição de monitoramento de atividade e selecione **saída** em **ações**.

    ![Tela de saída da atividade](media/load-sap-bw-data/activity-output.png)

    ![Exibição de detalhes da saída da atividade](media/load-sap-bw-data/activity-output-details.png)

## <a name="incremental-copy-from-sap-bw-open-hub"></a>Cópia incremental do Hub SAP BW Open

> [!TIP]
> Veja [SAP BW fluxo de extração Delta do conector de Hub aberto](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) para saber como o SAP BW conector de Hub aberto no data Factory copia dados incrementais do SAP BW. Este artigo também pode ajudá-lo a entender a configuração básica do conector.

Agora, vamos continuar a configurar a cópia incremental de SAP BW Hub aberto.

A cópia incremental usa um mecanismo de "marca d' água alta" com base na **ID da solicitação**. Essa ID é gerada automaticamente em SAP BW destino de Hub aberto pelo DTP. O diagrama a seguir mostra este fluxo de trabalho:

![Fluxograma de fluxo de trabalho de cópia incremental](media/load-sap-bw-data/incremental-copy-workflow.png)

Na página data factory **vamos começar** , selecione **criar pipeline do modelo** para usar o modelo interno.

1. Pesquise **SAP BW** para localizar e selecionar a **cópia incremental de SAP BW para Azure data Lake Storage Gen2** modelo. Este modelo copia dados em Azure Data Lake Storage Gen2. Você pode usar um fluxo de trabalho semelhante para copiar para outros tipos de coletor.

2. Na página principal do modelo, selecione ou crie as três conexões a seguir e, em seguida, selecione **usar este modelo** no canto inferior direito da janela.

   - **Armazenamento de BLOBs do Azure**: neste passo a passos, usamos o armazenamento de BLOBs do Azure para armazenar a marca d' água alta, que é a *ID de solicitação máxima copiada*.
   - **SAP BW Open Hub**: essa é a origem da qual copiar dados. Consulte a explicação completa anterior para obter uma configuração detalhada.
   - **Azure data Lake Storage Gen2**: é o coletor no qual copiar dados. Consulte a explicação completa anterior para obter uma configuração detalhada.

   ![Cópia incremental do modelo de SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. Este modelo gera um pipeline com as três atividades a seguir e as torna encadeadas em um êxito: *Lookup*, *copiar dados* e *Web*.

   Vá para a guia **parâmetros** de pipeline. Você verá todas as configurações que precisa fornecer.

   ![Cópia incremental da configuração de SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**: especifique o nome da tabela de Hub aberta para a qual copiar dados.

   - **Data_Destination_Container**: especifique o contêiner de Azure data Lake Storage Gen2 de destino para copiar dados. Se o contêiner não existir, a atividade de cópia Data Factory criará uma durante a execução.
  
   - **Data_Destination_Directory**: especifique o caminho da pasta no contêiner de Azure data Lake Storage Gen2 para copiar dados. Se o caminho não existir, a atividade de cópia Data Factory criará um caminho durante a execução.
  
   - **HighWatermarkBlobContainer**: especifique o contêiner para armazenar o valor de marca d' água alta.

   - **HighWatermarkBlobDirectory**: especifique o caminho da pasta no contêiner para armazenar o valor de marca d' água alta.

   - **HighWatermarkBlobName**: especifique o nome do blob para armazenar o valor de marca d' água alta, como `requestIdCache.txt` . No armazenamento de BLOBs, vá para o caminho correspondente de HighWatermarkBlobContainer + HighWatermarkBlobDirectory + HighWatermarkBlobName, como *container/Path/requestIdCache.txt*. Crie um blob com o conteúdo 0.

      ![Conteúdo do blob](media/load-sap-bw-data/blob.png)

   - **LogicAppURL**: neste modelo, usamos o webactivity para chamar os aplicativos lógicos do Azure para definir o valor de marca d' água alta no armazenamento de BLOBs. Ou, você pode usar o banco de dados SQL do Azure para armazená-lo. Use uma atividade de procedimento armazenado para atualizar o valor.

      Primeiro, você deve criar um aplicativo lógico, como mostra a imagem a seguir. Em seguida, Cole a **URL http post**.

      ![Configuração do aplicativo lógico](media/load-sap-bw-data/logic-app-config.png)

      1. Acesse o portal do Azure. Selecione um novo serviço de **aplicativos lógicos** . Selecione **+ aplicativo lógico em branco** para ir para o **Designer de aplicativos lógicos**.

      2. Crie um gatilho de **quando uma solicitação HTTP é recebida**. Especifique o corpo da solicitação HTTP da seguinte maneira:

         ```json
         {
            "properties": {
               "sapOpenHubMaxRequestId": {
                  "type": "string"
               }
            },
            "type": "object"
         }
         ```

      3. Adicione uma ação **criar blob** . Para o **caminho da pasta** e o nome do **blob**, use os mesmos valores que você configurou anteriormente em *HighWatermarkBlobContainer + HighWatermarkBlobDirectory* e *HighWatermarkBlobName*.

      4. Selecione **Salvar**. Em seguida, copie o valor da **URL http post** para usar no pipeline data Factory.

4. Depois de fornecer os parâmetros de pipeline data Factory, selecione **depurar**  >  **concluir** para invocar uma execução para validar a configuração. Ou selecione **publicar** para publicar todas as alterações e, em seguida, selecione **Adicionar gatilho** para executar uma execução.

## <a name="sap-bw-open-hub-destination-configurations"></a>SAP BW configurações de destino de Hub aberto

Esta seção apresenta a configuração do lado de SAP BW para usar o conector de Hub SAP BW Open no Data Factory para copiar dados.

### <a name="configure-delta-extraction-in-sap-bw"></a>Configurar extração de Delta no SAP BW

Se você precisar de cópia histórica e cópia incremental ou apenas cópia incremental, configure a extração Delta em SAP BW.

1. Crie o destino do Hub aberto. Você pode criar o OHD no SAP Transaction RSA1, que cria automaticamente a transformação necessária e o processo de transferência de dados. Use as configurações a seguir:

   - **Objecttype**: você pode usar qualquer tipo de objeto. Aqui, usamos **InfoCube** como exemplo.
   - **Tipo de destino**: selecione **tabela de banco de dados**.
   - **Chave da tabela**: selecione **chave técnica**.
   - **Extração**: selecione **manter dados e inserir registros na tabela**.

   ![Criar SAP BW caixa de diálogo extração de OHD Delta](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![Criar SAP BW caixa de diálogo de extração OHD Delta2](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   Você pode aumentar o número de processos de trabalho do SAP em execução paralela para o DTP:

   ![A captura de tela mostra as configurações de processamento paralelo em que você pode selecionar o número de processos paralelos para a D T P.](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. Agende as cadeias de DTP em processo.

   Um DTP de Delta para um cubo só funcionará se as linhas necessárias não tiverem sido compactadas. Certifique-se de que a compactação de cubo BW não esteja em execução antes do DTP para a tabela de Hub aberta. A maneira mais fácil de fazer isso é integrar o DTP às cadeias de processo existentes. No exemplo a seguir, o DTP (para o OHD) é inserido na cadeia de processo entre as etapas *ajustar* (ROLLUP agregado) e *recolher* (compactação de cubo).

   ![Criar SAP BW gráfico de fluxo de cadeia de processo](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>Configurar extração completa no SAP BW

Além da extração Delta, talvez você queira uma extração completa do mesmo SAP BW Infoprovider. Isso geralmente se aplica se você quiser fazer cópia completa, mas não incremental, ou se quiser [ressincronizar a extração Delta](#resync-delta-extraction).

Você não pode ter mais de um DTP para o mesmo OHD. Portanto, você deve criar um OHD adicional antes da extração de Delta.

![Criar SAP BW OHD completo](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

Para obter um OHD de carga completo, escolha opções diferentes em vez de extração Delta:

- Em OHD: defina a opção de **extração** para **excluir dados e inserir registros**. Caso contrário, os dados serão extraídos muitas vezes quando você repetir o DTP em uma cadeia de processo BW.

- No DTP: defina o **modo de extração** como **completo**. Você deve alterar o DTP criado automaticamente de **Delta** para **completo** imediatamente após a criação do Ohd, pois essa imagem mostra:

   ![Criar SAP BW caixa de diálogo OHD configurada para extração "completa"](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- No conector de Hub aberto BW do Data Factory: desative **excluir a última solicitação**. Caso contrário, nada será extraído.

Normalmente, você executa o DTP completo manualmente. Ou, você pode criar uma cadeia de processo para o DTP completo. Normalmente, é uma cadeia separada que é independente de suas cadeias de processo existentes. Em ambos os casos, *Verifique se o DTP foi concluído antes de iniciar a extração usando data Factory cópia*. Caso contrário, somente os dados parciais serão copiados.

### <a name="run-delta-extraction-the-first-time"></a>Executar extração de Delta na primeira vez

A primeira extração Delta é tecnicamente uma *extração completa*. Por padrão, o conector de Hub aberto SAP BW exclui a última solicitação ao copiar dados. Para a primeira extração Delta, nenhum dado é extraído pelo Data Factory atividade de cópia até que um DTP subsequente gere dados Delta na tabela com uma ID de solicitação separada. Há duas maneiras de evitar esse cenário:

- Desative a opção de **exclusão da última solicitação** para a primeira extração Delta. Verifique se o primeiro DTP de Delta foi concluído antes de iniciar a extração Delta na primeira vez.
-  Use o procedimento para ressincronizar a extração Delta, conforme descrito na próxima seção.

### <a name="resync-delta-extraction"></a>Resincronização de reextração Delta

Os cenários a seguir alteram os dados em SAP BW cubos, mas não são considerados pelo DTP do Delta:

- SAP BW exclusão seletiva (de linhas usando qualquer condição de filtro)
- SAP BW a exclusão da solicitação (de solicitações com falha)

Um destino do Hub aberto do SAP não é um destino de dados controlado por Data Mart (em todos os pacotes de suporte a SAP BW desde 2015). Portanto, você pode excluir dados de um cubo sem alterar os dados no OHD. Em seguida, você deve ressincronizar os dados do cubo com Data Factory:

1. Execute uma extração completa no Data Factory (usando um DTP completo no SAP).
2. Exclua todas as linhas na tabela de Hub aberta para o centro de DTP Delta.
3. Defina o status do Delta DTP a ser **buscado**.

Depois disso, todas as extrações Delta DTPs e Data Factory Delta funcionam conforme o esperado.

Para definir o status do Delta DTP a ser **buscado**, você pode usar a seguinte opção para executar o conjunto de DTP Delta manualmente:

*Sem Transferência de Dados; Status Delta na origem: buscada*

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre SAP BW suporte ao conector de Hub aberto:

> [!div class="nextstepaction"]
>[Conector de Hub aberto do SAP Business Warehouse](connector-sap-business-warehouse-open-hub.md)
