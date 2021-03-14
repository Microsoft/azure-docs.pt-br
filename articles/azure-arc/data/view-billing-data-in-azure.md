---
title: Carregar dados de cobrança no Azure e exibi-los no portal do Azure
description: Carregar dados de cobrança no Azure e exibi-los no portal do Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 03/02/2021
ms.topic: how-to
ms.openlocfilehash: 7ef1cd43d2efbc5ab92cc2b4cba4d237805d8921
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102202647"
---
# <a name="upload-billing-data-to-azure-and-view-it-in-the-azure-portal"></a>Carregar dados de cobrança no Azure e exibi-los no portal do Azure

> [!IMPORTANT] 
>  Não há nenhum custo para usar os serviços de dados habilitados para o Azure Arc durante o período de versão prévia. Embora o sistema de cobrança funcione de ponta a ponta, o medidor de cobrança é definido como $0.  Se você seguir esse cenário, verá entradas em sua cobrança para um serviço chamado atualmente serviços de **dados híbridos** e para recursos de um tipo chamado **Microsoft. AzureArcData/ `<resource type>`**. Você poderá ver um registro para cada serviço de dados-arco do Azure que você criar, mas cada registro será cobrado por $0.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connectivity-modes---implications-for-billing-data"></a>Modos de conectividade – implicações para dados de cobrança

No futuro, haverá dois modos nos quais você pode executar seus serviços de dados habilitados para o Azure Arc:

- **Conectado indiretamente** – não há nenhuma conexão direta com o Azure. Os dados são enviados para o Azure somente por meio de um processo de exportação/carregamento. Todas as implantações do Azure Arc Data Services funcionam neste modo hoje em visualização.
- **Conectado diretamente** – neste modo, haverá uma dependência no serviço kubernetes habilitado para Arc do Azure para fornecer uma conexão direta entre o Azure e o cluster kubernetes no qual os serviços de dados habilitados para Arc do Azure estão em execução. Isso permitirá mais recursos e também permitirá que você use o portal do Azure e o CLI do Azure para gerenciar seus serviços de dados habilitados para o Azure Arc, exatamente como você gerencia seus serviços de dados no PaaS do Azure.  Esse modo de conectividade ainda não está disponível na versão prévia, mas será disponibilizado em breve.

Você pode ler mais sobre a diferença entre os [modos de conectividade](./connectivity.md).

No modo indiretamente conectado, os dados de cobrança são exportados periodicamente do controlador de dados de arco do Azure para um arquivo seguro e, em seguida, carregados no Azure e processados.  No próximo modo conectado diretamente, os dados de cobrança serão enviados automaticamente para o Azure aproximadamente 1/hora para fornecer uma exibição quase em tempo real dos custos de seus serviços. O processo de exportar e carregar os dados no modo indiretamente conectado também pode ser automatizado usando scripts ou poderemos criar um serviço que o fará para você.

## <a name="upload-billing-data-to-azure"></a>Carregar dados de cobrança no Azure

Para carregar dados de cobrança no Azure, o seguinte deve ocorrer primeiro:

1. Crie um serviço de dados habilitado para o Azure Arc se você ainda não tiver um. Por exemplo, crie um dos seguintes:
   - [Criar uma instância gerenciada do SQL do Azure no arco do Azure](create-sql-managed-instance.md)
   - [Criar um grupo de servidores de hiperescala PostgreSQL habilitado para o Azure Arc](create-postgresql-hyperscale-server-group.md)
1. [Carregue o inventário de recursos, dados de uso, métricas e logs para Azure monitor](upload-metrics-and-logs-to-azure-monitor.md) se ainda não tiver feito isso.
1. Aguarde pelo menos 2 horas desde a criação do serviço de dados para que o processo de coleta de telemetria de cobrança possa coletar alguns dados de cobrança.

Execute o seguinte comando para exportar os dados de cobrança:

```console
azdata arc dc export -t usage -p usage.json
```

Por enquanto, o arquivo não é criptografado para que você possa ver o conteúdo. Sinta-se à vontade para abrir em um editor de texto e ver a aparência do conteúdo.

Você observará que há dois conjuntos de dados: `resources` e `data` . O `resources` é o controlador de dados, grupos de servidores de hiperescala PostgreSQL e instâncias gerenciadas do SQL. Os `resources` registros nos dados capturam os eventos pertinentes no histórico de um recurso – quando ele foi criado, quando ele foi atualizado e quando ele foi excluído. Os `data` registros capturam quantos núcleos estavam disponíveis para serem usados por uma determinada instância para cada hora.

Exemplo de uma `resource` entrada:

```console
    {
        "customObjectName": "<resource type>-2020-29-5-23-13-17-164711",
        "uid": "4bc3dc6b-9148-4c7a-b7dc-01afc1ef5373",
        "instanceName": "sqlInstance001",
        "instanceNamespace": "arc",
        "instanceType": "<resource>",
        "location": "eastus",
        "resourceGroupName": "production-resources",
        "subscriptionId": "482c901a-129a-4f5d-86e3-cc6b294590b2",
        "isDeleted": false,
        "externalEndpoint": "32.191.39.83:1433",
        "vCores": "2",
        "createTimestamp": "05/29/2020 23:13:17",
        "updateTimestamp": "05/29/2020 23:13:17"
    }
```

Exemplo de uma `data` entrada:

```console
        {
          "requestType": "usageUpload",
          "clusterId": "4b0917dd-e003-480e-ae74-1a8bb5e36b5d",
          "name": "DataControllerTestName",
          "subscriptionId": "482c901a-129a-4f5d-86e3-cc6b294590b2",
          "resourceGroup": "production-resources",
          "location": "eastus",
          "uploadRequest": {
            "exportType": "usages",
            "dataTimestamp": "2020-06-17T22:32:24Z",
            "data": "[{\"name\":\"sqlInstance001\",
                       \"namespace\":\"arc\",
                       \"type\":\"<resource type>\",
                       \"eventSequence\":1, 
                       \"eventId\":\"50DF90E8-FC2C-4BBF-B245-CB20DC97FF24\",
                       \"startTime\":\"2020-06-17T19:11:47.7533333\",
                       \"endTime\":\"2020-06-17T19:59:00\",
                       \"quantity\":1,
                       \"id\":\"4BC3DC6B-9148-4C7A-B7DC-01AFC1EF5373\"}]",
           "signature":"MIIE7gYJKoZIhvcNAQ...2xXqkK"
          }
        }
```

Execute o seguinte comando para carregar o usage.jsno arquivo no Azure:

```console
azdata arc dc upload -p usage.json
```

## <a name="view-billing-data-in-azure-portal"></a>Exibir dados de cobrança no portal do Azure

Siga estas etapas para exibir dados de cobrança no portal do Azure:

1. Abra o [Portal do Azure](https://portal.azure.com).
1. Na caixa de pesquisa na parte superior do tipo de tela em **Gerenciamento de custos** e clique no serviço gerenciamento de custos.
1. Em **visão geral do gerenciamento de custos**, clique na guia **Gerenciamento de custos** .
1. Clique na guia **análise de custo** à esquerda.
1. Clique no botão **custo por recurso** na parte superior da exibição.
1. Verifique se o escopo está definido para a assinatura na qual os recursos do serviço de dados foram criados.
1. Selecione **custo por recurso** na lista suspensa de exibição ao lado do seletor de escopo próximo à parte superior da exibição.
1. Verifique se o filtro de data está definido para **este mês** ou outro intervalo de tempo que faz sentido dado o tempo de quando você criou os recursos do serviço de dados.
1. Clique em **Adicionar filtro** para adicionar um filtro por **tipo de recurso**  =  `Microsoft.AzureArcData/<data service type>` se você quiser filtrar para apenas um tipo de serviço de dados habilitado para Arc do Azure.
1. Agora, você verá uma lista de todos os recursos que foram criados e carregados no Azure. Como o medidor de cobrança é $0, você verá que o custo é sempre $0.

## <a name="download-billing-data"></a>Baixar dados de cobrança

Você pode baixar dados de Resumo de cobrança diretamente do portal do Azure.

1. Na mesma **análise de custo-> modo de exibição por tipo de recurso** que você atingiu seguindo as instruções acima, clique no botão baixar na parte superior.
1. Escolha o tipo de arquivo de download-Excel ou CSV-e clique no botão **baixar dados** .
1. Abra o arquivo em um editor apropriado, dado o tipo de arquivo selecionado.

## <a name="export-billing-data"></a>Exportar dados de cobrança

Você também pode periodicamente exportar automaticamente dados **detalhados** de uso e cobrança para um contêiner de armazenamento do Azure criando um trabalho de exportação de cobrança. Isso será útil se você quiser ver os detalhes de sua cobrança, como quantas horas uma determinada instância foi cobrada no período de cobrança.

Siga estas etapas para configurar um trabalho de exportação de cobrança:

1. Clique em **exportações** à esquerda.
1. Clique em **Adicionar**.
1. Insira um nome e uma frequência de exportação e clique em Avançar.
1. Escolha criar uma nova conta de armazenamento ou use uma existente e preencha o formulário para especificar a conta de armazenamento, o contêiner e o caminho do diretório para exportar os arquivos de dados de cobrança e clique em Avançar.
1. Clique em **Criar**.

Os arquivos de exportação de dados de cobrança estarão disponíveis em aproximadamente 4 horas e serão exportados no agendamento especificado durante a criação do trabalho de exportação de cobrança.

Siga estas etapas para exibir os arquivos de dados de cobrança exportados:

Você pode validar os arquivos de dados de cobrança no portal do Azure. 

> [!IMPORTANT]
> Depois de criar o trabalho de exportação de cobrança, aguarde 4 horas antes de prosseguir com as etapas a seguir.

1. Na caixa de pesquisa na parte superior do portal, digite contas de **armazenamento** e clique em **contas de armazenamento**.
3. Clique na conta de armazenamento que você especificou ao criar o trabalho de exportação de cobrança acima.
4. Clique em contêineres à esquerda.
5. Clique no contêiner que você especificou ao criar o trabalho de exportação de cobrança acima.
6. Clique na pasta que você especificou ao criar o trabalho de exportação de cobrança acima.
7. Faça uma busca detalhada nas pastas e nos arquivos gerados e clique em um dos arquivos. csv gerados.
8. Clique no botão **baixar** , que salvará o arquivo em sua pasta de downloads local.
9. Abra o arquivo usando um visualizador de arquivos. csv, como o Excel.
10. Filtre os resultados para mostrar apenas as linhas com o **tipo de recurso**  =  `Microsoft.AzureArcData/<data service resource type` .
11. Você verá o número de horas que a instância foi usada no período de 24 horas atual na coluna UsageQuantity.
