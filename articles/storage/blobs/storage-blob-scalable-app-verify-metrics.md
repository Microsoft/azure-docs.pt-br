---
title: Verificar as métricas de taxa de transferência e latência para uma conta de armazenamento no portal do Azure | Microsoft Docs
description: Saiba como verificar as métricas de taxa de transferência e latência para uma conta de armazenamento no portal.
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.subservice: blobs
ms.openlocfilehash: dc0f8171e18598e4f805a03a4bc6d17de220fbe1
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698982"
---
# <a name="verify-throughput-and-latency-metrics-for-a-storage-account"></a>Verificar as métricas de taxa de transferência e de latência para uma conta de armazenamento

Este tutorial é quarta e última parte de uma série. Nos tutoriais anteriores, você aprendeu a fazer upload e download de grandes quantidades de dados aleatórios para uma conta de armazenamento do Azure. Este tutorial mostra como você pode usar as métricas para exibir a taxa de transferência e latência no portal do Azure.

Na primeira parte da série, você aprende a:

> [!div class="checklist"]
> * Configurar gráficos no portal do Azure
> * Verificar as métricas de taxa de transferência e de latência

[As métricas de armazenamento do Azure](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) usa o monitor do Azure para fornecer uma exibição unificada para o desempenho e a disponibilidade de sua conta de armazenamento.

## <a name="configure-metrics"></a>Configurar métricas

Navegue até **Métricas (visualização)** em **CONFIGURAÇÕES** na sua conta de armazenamento.

Escolha Blob na lista suspensa **SUB SERVIÇO**.

Em **MÉTRICA**, selecione uma das métricas encontradas na tabela a seguir:

As métricas a seguir dão uma ideia da latência e taxa de transferência do aplicativo. As métricas que você configurar no portal são em médias de 1 minuto. Se uma transação é concluída no meio de um minuto os dados do minuto serão divididos pela metade para a média. No aplicativo, as operações de upload e download foram cronometradas e lhe foi fornecido o resultado da quantidade real do tempo necessário para o upload e download dos arquivos. Essa Essas informações podem ser usadas em conjunto com as métricas do portal para entender completamente a taxa de transferência.

|Métrica|Definição|
|---|---|
|**Latência de E2E com Sucesso**|A latência média de ponta a ponta das solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|
|**Latência de Servidor Com Sucesso**|O tempo médio usado para processar uma solicitação bem-sucedida pelo Armazenamento do Azure. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency. |
|**Transações**|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. No exemplo, o tamanho do bloco foi definido como 100 MB. Nesse caso, cada bloco de 100 MB é considerado uma transação.|
|**Entrada**|A quantidade de dados de entrada. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure. |
|**Saída**|A quantidade de dados de saída. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável. |

Selecione **as últimas 24 horas (automático)** ao lado de **tempo**. Escolha **Última hora** e **Minuto** para **Granularidade de tempo**, em seguida, clique em **Aplicar**.

![Métricas da conta de armazenamento](./media/storage-blob-scalable-app-verify-metrics/figure1.png)

Os gráficos podem ter mais de uma métrica atribuída a eles, mas atribuir mais de uma métrica desabilita a capacidade de grupo por dimensões.

## <a name="dimensions"></a>Dimensões

As [Dimensões](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#metrics-dimensions) são usadas para examinar mais profundamente os gráficos e obter informações mais detalhadas. Métricas diferentes têm diferentes dimensões. Uma dimensão que está disponível é a dimensão **nome da API**. Esta dimensão interrompe o gráfico em cada chamada de API separada. A primeira imagem abaixo mostra um gráfico de exemplo do total de transações para uma conta de armazenamento. A segunda imagem mostra o mesmo gráfico, mas com a API do nome de dimensão selecionada. Como você pode ver, cada transação é listada fornecendo mais detalhes para o número de chamadas feito pelo nome da API.

![Métricas da conta de armazenamento - transações sem uma dimensão](./media/storage-blob-scalable-app-verify-metrics/transactionsnodimensions.png)

![Métricas da conta de armazenamento - transações](./media/storage-blob-scalable-app-verify-metrics/transactions.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando o grupo de recursos, a máquina virtual e todos os recursos relacionados não forem mais necessários, exclua-os. Para fazer isso, selecione o grupo de recursos da VM e clique em Excluir.

## <a name="next-steps"></a>Próximas etapas

Na quarta parte da série, você aprendeu sobre como exibir métricas para a solução de exemplo, como por exemplo:

> [!div class="checklist"]
> * Configurar gráficos no portal do Azure
> * Verificar as métricas de taxa de transferência e de latência

Siga este link para ver amostras de armazenamento pré-compiladas.

> [!div class="nextstepaction"]
> [Amostras de script do Armazenamento do Azure](storage-samples-blobs-cli.md)

[previous-tutorial]: storage-blob-scalable-app-download-files.md
