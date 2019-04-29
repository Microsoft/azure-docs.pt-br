---
title: Carregar dados do SAP Business Warehouse usando o Azure Data Factory | Microsoft Docs
description: Usar o Azure Data Factory para copiar dados do SAP BW (Business Warehouse)
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: jingwang
ms.openlocfilehash: 9a123ed45b5857aa40fc9853a95c528833ba8aa9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60548489"
---
# <a name="copy-data-from-sap-business-warehouse-by-using-azure-data-factory"></a>Copiar dados do SAP Business Warehouse usando o Azure Data Factory

Este artigo mostra como usar o Azure Data Factory para copiar dados do SAP BW (Business Warehouse) por meio do Open Hub para o armazenamento do Azure Data Lake Gen2. Você pode usar um processo semelhante para copiar dados para outro [suporte para repositórios de dados](copy-activity-overview.md#supported-data-stores-and-formats).

> [!TIP]
> Para obter informações gerais sobre como copiar dados do SAP BW, incluindo a integração do SAP BW Open Hub e o fluxo de extração de delta, consulte [copiar dados do SAP Business Warehouse por meio do Open Hub usando o Azure Data Factory](connector-sap-business-warehouse-open-hub.md).

## <a name="prerequisites"></a>Pré-requisitos

- **Azure Data Factory**: Se você não tiver uma, siga as etapas a serem [criar um data factory](quickstart-create-data-factory-portal.md#create-a-data-factory).

- **SAP BW Open Hub de destino (OHD) com o tipo de destino "Tabela de banco de dados"**: Para criar um OHD ou verifique se o seu OHD está configurado corretamente para integração com o Data Factory, consulte a [configurações de destino SAP BW do Open Hub](#sap-bw-open-hub-destination-configurations) seção deste artigo.

- **O usuário do SAP BW precisa das seguintes permissões**:

  - Autorização para chamadas de função remota (RFC) e SAP BW.
  - Permissões para a atividade de "Executar" das **S_SDSAUTH** o objeto de autorização.

- **Um [integration runtime (IR) auto-hospedado](concepts-integration-runtime.md#self-hosted-integration-runtime) com o conector do SAP .NET 3.0**. Siga estas etapas de instalação:

  1. Instalar e registrar o integration runtime auto-hospedado, versão 3.13 ou posterior. (Esse processo é descrito neste artigo).

  2. Baixe o [conector do SAP de 64 bits para o Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) no site da SAP e instalá-lo no mesmo computador que o ir auto-hospedado. Durante a instalação, verifique se você selecionou **instalar Assemblies ao GAC** na **etapas de configuração opcionais** caixa de diálogo, como mostra a imagem a seguir:

     ![Configurar a caixa de diálogo SAP .NET Connector](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="do-a-full-copy-from-sap-bw-open-hub"></a>Fazer uma cópia completa do SAP BW Open Hub

No portal do Azure, vá para seu data factory. Selecione **autor & Monitor** para abrir a IU do Data Factory em uma guia separada.

1. Sobre o **vamos começar** página, selecione **copiar dados** para abrir a ferramenta Copy Data.

2. Sobre o **propriedades** , especifique um **nome da tarefa**e, em seguida, selecione **próxima**.

3. Sobre o **armazenamento de dados de origem** página, selecione **+ criar nova conexão**. Selecione **Open Hub do SAP BW** na Galeria de conector e, em seguida, selecione **continuar**. Para filtrar os conectores, você pode digitar **SAP** na caixa de pesquisa.

4. Sobre o **conexão especificar SAP BW Open Hub** página, siga estas etapas para criar uma nova conexão.

   ![Página Criar serviço vinculado SAP BW Open Hub](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. Dos **conectar por meio do tempo de execução de integração** , selecione um ir auto-hospedado existente. Ou optar por criar um se você ainda não tiver uma.

      Para criar um novo IR auto-hospedado, selecione **+ novo**e, em seguida, selecione **auto-hospedado**. Insira um **nome**e, em seguida, selecione **próxima**. Selecione **instalação expressa** para instalar no computador atual, ou siga o **Configuração Manual** etapas que são fornecidas.

      Conforme mencionado na [pré-requisitos](#prerequisites), certifique-se de que você tem o conector do SAP para o Microsoft .NET 3.0 instalado no mesmo computador onde o IR auto-hospedado está em execução.

   2. Preencha o SAP BW **nome do servidor**, **número do sistema**, **ID do cliente** **idioma** (se diferente de **EN**) , **Nome de usuário**, e **senha**.

   3. Selecione **Testar conexão** para validar as configurações e, em seguida, selecione **concluir**.

   4. Uma conexão nova é criada. Selecione **Avançar**.

5. Sobre o **selecionar destinos de Hub aberto** página, procure os destinos de Hub aberto que estão disponíveis no seu SAP BW. Selecione o OHD para copiar dados de e, em seguida, selecione **próxima**.

   ![Selecione a tabela de destino SAP BW do Open Hub](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. Especifique um filtro, se você precisar de um. Se seu OHD contém apenas dados de uma execução de processo (DTP) de transferência de dados única com uma ID de solicitação única ou se você tiver certeza de que seu DTP for concluída e você deseja copiar os dados, desmarque a **excluir a última solicitação** caixa de seleção.

   Saiba mais sobre essas configurações na [configurações de destino SAP BW do Open Hub](#sap-bw-open-hub-destination-configurations) seção deste artigo. Selecione **validar** verificar quais dados serão retornados. Em seguida, selecione **Avançar**.

   ![Configurar filtro do Open Hub do SAP BW](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. Sobre o **armazenamento de dados de destino** página, selecione **+ criar nova conexão** > **Gen2 de armazenamento do Azure Data Lake**  >   **Continuar**.

8. Sobre o **conexão de especificar o Azure Data Lake Storage** página, siga estas etapas para criar uma conexão.

   ![Criar uma página de serviço vinculado do ADLS Gen2](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. Selecione sua conta com capacidade de armazenamento do Data Lake Gen2 na **nome** lista suspensa.
   2. Selecione **Concluir** para criar a conexão. Em seguida, selecione **Avançar**.

9. Sobre o **escolha o arquivo de saída ou a pasta** , insira **copyfromopenhub** como o nome da pasta de saída. Em seguida, selecione **Avançar**.

   ![Escolha a página da pasta de saída](media/load-sap-bw-data/choose-output-folder.png)

10. Sobre o **formato de arquivo de configuração** página, selecione **próxima** para usar as configurações padrão.

    ![Especifique a página do formato de coletor](media/load-sap-bw-data/specify-sink-format.png)

11. Sobre o **as configurações** página, expanda **configurações de desempenho**. Insira um valor para **grau de paralelismo de cópia** como 5 para carregar do SAP BW em paralelo. Em seguida, selecione **Avançar**.

    ![Definir as configurações de cópia](media/load-sap-bw-data/configure-copy-settings.png)

12. Sobre o **resumo** , reveja as configurações. Em seguida, selecione **Avançar**.

13. Sobre o **implantação** página, selecione **Monitor** para monitorar o pipeline.

    ![Página Implantação](media/load-sap-bw-data/deployment.png)

14. Observe que o **Monitor** guia no lado esquerdo da página é selecionado automaticamente. O **ações** coluna inclui links para exibir detalhes de execução de atividade e executar novamente o pipeline.

    ![Exibição de monitoramento do pipeline](media/load-sap-bw-data/pipeline-monitoring.png)

15. Para exibir execuções de atividade que estão associadas com a execução do pipeline, selecione **exibir execuções de atividade** na **ações** coluna. Há apenas uma atividade (atividade de cópia) no pipeline, assim, você vê apenas uma entrada. Para voltar para o modo de exibição de execuções de pipeline, selecione a **Pipelines** link na parte superior. Selecione **Atualizar** para atualizar a lista.

    ![Tela monitoramento de atividades](media/load-sap-bw-data/activity-monitoring.png)

16. Para monitorar os detalhes de execução para cada atividade de cópia, selecione o **detalhes** link, que é um ícone de óculos abaixo **ações** no modo de exibição de monitoramento de atividades. Detalhes disponíveis incluem o volume de dados copiados da origem para o coletor, taxa de transferência de dados, as etapas de execução e duração e configurações usadas.

    ![Detalhes do monitoramento de atividade](media/load-sap-bw-data/activity-monitoring-details.png)

17. Para exibir o **ID da solicitação máxima**, volte para o modo de exibição e selecione monitoramento de atividades **saída** sob **ações**.

    ![Tela de saída de atividade](media/load-sap-bw-data/activity-output.png)

    ![Exibição de detalhes de saída de atividade](media/load-sap-bw-data/activity-output-details.png)

## <a name="do-an-incremental-copy-from-sap-bw-open-hub"></a>Fazer uma cópia incremental do Open Hub do SAP BW

> [!TIP]
> Ver [fluxo de extração de delta de conector SAP BW Open Hub](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) para saber como o conector do SAP BW Open Hub no Data Factory copia dados incrementais do SAP BW. Este artigo pode ajudá-lo a entender a configuração básica do conector.

Agora, vamos continuar a configurar uma cópia incremental de Hub aberto do SAP BW.

Cópia incremental usa um mecanismo de "alto-marca d'água" que se baseia o **ID da solicitação**. Essa ID é gerado automaticamente no destino SAP BW do Open Hub pelo DTP. O diagrama a seguir mostra esse fluxo de trabalho:

![Fluxograma de fluxo de trabalho de cópia incremental](media/load-sap-bw-data/incremental-copy-workflow.png)

No data factory **vamos começar** página, selecione **criar o pipeline de modelo** para usar o modelo interno.

1. Pesquise **SAP BW** para localizar e selecionar o **Incremental copiar do SAP BW para armazenamento do Azure Data Lake Gen2** modelo. Este modelo copia dados para o armazenamento do Azure Data Lake Gen2. Você pode usar um fluxo de trabalho semelhante para copiar para outros tipos de coletor.

2. Na página principal do modelo, selecione ou crie as seguintes conexões de três e, em seguida, selecione **Use este modelo** no canto inferior direito da janela.

   - **O armazenamento de BLOBs do Azure**: Neste passo a passo, usamos o armazenamento de BLOBs do Azure para armazenar a marca d'água alta, que é o *máx copiados ID da solicitação*.
   - **SAP BW Open Hub**: Essa é a fonte para copiar dados do. Consulte o passo a passo completo de cópia anterior para a configuração detalhada.
   - **Azure Data Lake Storage Gen2**: Isso é para copiar dados para o coletor. Consulte o passo a passo completo de cópia anterior para a configuração detalhada.

   ![Cópia incremental do modelo de SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. Esse modelo gera um pipeline com as três seguintes atividades e faz com que sejam encadeadas em caso de êxito: *Pesquisa*, *copiar dados*, e *Web*.

   Vá para o pipeline **parâmetros** guia. Você verá todas as configurações que você precisa fornecer.

   ![Cópia incremental de configuração do SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**: Especifique o nome da tabela para copiar dados de Hub aberto.

   - **ADLSGen2SinkPath**: Especifique o caminho de destino de armazenamento do Azure Data Lake Gen2 para copiar dados para. Se o caminho não existir, a atividade de cópia do Data Factory cria um caminho durante a execução.

   - **HighWatermarkBlobPath**: Especifique o caminho para armazenar o valor de marca d'água alta, tais como `container/path`.

   - **HighWatermarkBlobName**: Especifique o nome do blob para armazenar o valor de marca d'água alta, tais como `requestIdCache.txt`. No armazenamento de BLOBs, vá para o caminho correspondente do HighWatermarkBlobPath + HighWatermarkBlobName, tais como *container/path/requestIdCache.txt*. Crie um blob com conteúdo 0.

      ![Conteúdo do blob](media/load-sap-bw-data/blob.png)

   - **LogicAppURL**: Neste modelo, usamos WebActivity chamar aplicativos de lógicos do Azure para definir o valor de marca d'água alta no armazenamento de BLOBs. Ou, você pode usar o banco de dados SQL do Azure para armazená-lo. Use uma atividade de procedimento armazenado para atualizar o valor.

      Primeiro, você deve criar um aplicativo lógico, como mostra a imagem a seguir. Em seguida, cole a **URL de HTTP POST**.

      ![Configuração do aplicativo lógico](media/load-sap-bw-data/logic-app-config.png)

      1. Vá para o portal do Azure. Selecione uma nova **aplicativos lógicos** service. Selecione **+ aplicativo lógico em branco** para ir até **Designer de aplicativos lógicos**.

      2. Criar um gatilho de **quando uma solicitação HTTP é recebida**. Especifique o corpo da solicitação HTTP da seguinte maneira:

         ```json
         {
            "properties": {
               "sapOpenHubMaxRequestId": {
                  "type": "string"
               },
               "type": "object"
            }
         }
         ```

      3. Adicionar um **criar blob** ação. Para **caminho da pasta** e **nome do Blob**, use os mesmos valores que você configurou anteriormente no **HighWatermarkBlobPath** e **HighWatermarkBlobName**.

      4. Clique em **Salvar**. Em seguida, copie o valor de **URL de HTTP POST** usar no pipeline do Data Factory.

4. Depois de fornecer os parâmetros de pipeline do Data Factory, selecione **Debug** > **concluir** para invocar uma execução para validar a configuração. Ou, selecione **publicar tudo** para publicar as alterações e, em seguida, selecione **gatilho** executar uma execução.

## <a name="sap-bw-open-hub-destination-configurations"></a>Configurações de destino SAP BW do Open Hub

Esta seção apresenta a configuração do lado do SAP BW para usar o conector do SAP BW Open Hub no Data Factory para copiar dados.

### <a name="configure-delta-extraction-in-sap-bw"></a>Configurar a extração de delta no SAP BW

Se você precisar copiar histórico e cópia incremental ou somente cópia incremental, configure a extração de delta no SAP BW.

1. Crie o destino do Open Hub. Você pode criar o OHD no SAP transação RSA1, que cria automaticamente o processo de transferência de dados e transformação necessária. Use as configurações a seguir:

   - **ObjectType**: Você pode usar qualquer tipo de objeto. Aqui, usaremos **InfoCube** como exemplo.
   - **Tipo de destino**: Selecione **tabela de banco de dados**.
   - **Chave da tabela**: Selecione **técnica chave**.
   - **Extração**: Selecione **manter dados e inserir registros na tabela**.

   ![Criar caixa de diálogo de extração do SAP BW OHD delta](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![Criar caixa de diálogo do SAP BW OHD delta2 extração](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   Você pode aumentar o número de paralelo executando os processos de trabalho do SAP para o DTP:

   ![create-sap-bw-ohd-delta3](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. Agende o DTP em cadeias de processo.

   Um delta DTP para um cubo só funciona se as linhas necessárias ainda não foram compactadas. Certifique-se de compactação de cubo BW não está em execução antes do DTP na tabela do Open Hub. A maneira mais fácil de fazer isso é integrar o DTP em suas cadeias de processo existente. No exemplo a seguir, o DTP (para OHD) é inserido na cadeia de processo entre o *ajustar* (acumulação agregada) e *Collapse* etapas (compactação de cubo).

   ![Criar um gráfico de fluxo de cadeia de processo do SAP BW](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>Configurar a extração completa no SAP BW

Além de extração de delta, talvez você queira uma extração completa da mesma BW InfoProvider da SAP. Isso geralmente se aplica se você quiser que a cópia completa, mas não incremental, ou você deseja [ressincronizar a extração de delta](#resync-delta-extraction).

Você não pode ter o DTP mais de um para o mesmo OHD. Portanto, você deve criar um OHD adicional antes da extração de delta.

![Criar SAP BW OHD completo](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

Para uma carga completa OHD, escolha Opções de diferentes para extração de delta:

- No OHD: Defina a **extração** opção **registros de inserir e excluir dados**. Caso contrário, os dados serão extraídos muitas vezes, quando você repetir o DTP em uma cadeia de processo do BW.

- No DTP: Definir **modo de extração** à **completo**. Você deve alterar o DTP criado automaticamente na **Delta** ao **completo** imediatamente após a criação de OHD, como mostra esta imagem:

   ![Criar SAP BW OHD caixa de diálogo configurada para extração "Full"](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- No conector BW Open Hub da fábrica de dados: Desative **última solicitação de exclusão**. Caso contrário, nada será extraído.

Você normalmente executa o DTP completo manualmente. Ou, você pode criar uma cadeia de processo para o DTP completo. Geralmente, é uma cadeia separada que é independente de suas cadeias de processo existente. Em ambos os casos *Certifique-se de que o DTP é concluído antes de iniciar a extração usando a cópia do Data Factory*. Caso contrário, apenas os dados parciais serão copiados.

### <a name="run-delta-extraction-the-first-time"></a>Executar a extração de delta na primeira vez

Tecnicamente, a extração de delta a primeira é uma *completo extração*. Por padrão, o conector do SAP BW Open Hub exclui a última solicitação quando ela copia os dados. Para a extração de delta primeiro, nenhum dado é extraído pela atividade de cópia de fábrica de dados até que um DTP subsequente gera dados de delta na tabela com uma ID de solicitação separada. Há duas maneiras de evitar esse cenário:

- Desativar o **última solicitação de exclusão** opção para a extração de delta primeiro. Certifique-se de que o primeiro delta DTP é concluído antes de iniciar a extração de delta na primeira vez.
-  Use o procedimento para ressincronizar a extração de delta, conforme descrito na próxima seção.

### <a name="resync-delta-extraction"></a>Ressincronização de extração de delta

Os seguintes cenários alteram os dados em cubos do SAP BW, mas não são considerados pelo DTP delta:

- Exclusão seletiva de SAP BW (de linhas, usando qualquer condição de filtro)
- Exclusão de solicitação do SAP BW (de solicitações com falha)

Um destino do Open Hub SAP não é um destino de dados controlados de armazém de dados (em todos os pacotes de suporte do SAP BW desde 2015). Portanto, você pode excluir dados de um cubo sem alterar os dados em que o OHD. Em seguida, será preciso ressincronizar os dados do cubo com o Data Factory:

1. Execute uma extração completa no Data Factory (por meio de um DTP completa no SAP).
2. Exclua todas as linhas da tabela do Open Hub para o delta DTP.
3. Definir o status do delta DTP para **buscadas**.

Depois disso, todas as extrações de delta de Data Factory e delta subsequentes DTPs funcionam conforme o esperado.

Para definir o status do delta DTP para **buscadas**, você pode usar a opção a seguir para executar o delta DTP manualmente:

    *No Data Transfer; Delta Status in Source: Fetched*

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o suporte do conector SAP BW Open Hub:

> [!div class="nextstepaction"]
>[Conector do SAP Business Warehouse Open Hub](connector-sap-business-warehouse-open-hub.md)
