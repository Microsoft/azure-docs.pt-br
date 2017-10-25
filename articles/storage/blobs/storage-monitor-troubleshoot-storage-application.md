---
title: Monitorar e solucionar problemas de um aplicativo de armazenamento de nuvem no Azure | Microsoft Docs
description: "Use ferramentas de diagnóstico, métricas e alertas para solucionar problemas e monitorar um aplicativo em nuvem."
services: storage
documentationcenter: 
author: georgewallace
manager: timlt
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/19/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: db88c331f79d83e0124519f8b6dbb34514b456dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-troubleshoot-a-cloud-storage-application"></a>Monitorar e solucionar problemas de um aplicativo de armazenamento de nuvem

Este tutorial é quarta e última parte de uma série. Você aprende como monitorar e solucionar problemas de um aplicativo de armazenamento de nuvem.

Na primeira parte da série, você aprende a:

> [!div class="checklist"]
> * Ativar o registro em log e as métricas
> * Habilitar alertas para erros de autorização
> * Executar tráfego de teste com tokens SAS incorretos
> * Baixar e analisar logs

O [Azure Storage Analytics](../common/storage-analytics.md) fornece registro em log e dados de métrica para uma conta de armazenamento. Esses dados fornecem informações sobre a integridade da conta de armazenamento. Antes que você possa ter visibilidade de sua conta de armazenamento, você precisa configurar a coleta de dados. Esse processo envolve ativar o registro em log, configurar métricas e habilitar alertas.

Métricas e registro em log de contas de armazenamento são habilitados na guia **Diagnóstico** no Portal do Azure. Há dois tipos de métricas. Métricas de **Agregação** coletam os percentuais de entrada/saída, disponibilidade, latência e êxito. Essas métricas são agregadas para os serviços de blob, fila, tabela e arquivo. Métricas **Por API** coletam o mesmo conjunto de métricas para cada operação de armazenamento na API de serviço do Armazenamento do Azure. O log de armazenamento permite que você registre detalhes de solicitações bem-sucedidas e com falhas na conta de armazenamento. Esses logs permitem ver detalhes de operações de leitura, gravação e exclusão em suas tabelas, filas e blobs do Azure. Eles também permitem que você veja os motivos para solicitações com falha, tais como tempos limite atingidos, limitação e erros de autorização.

## <a name="log-in-to-the-azure-portal"></a>Faça logon no Portal do Azure

Faça logon no [Portal do Azure](https://portal.azure.com)

## <a name="turn-on-logging-and-metrics"></a>Ativar o registro em log e as métricas

No menu à esquerda, selecione **Grupos de Recursos**, selecione **myResourceGroup** e, em seguida, selecione sua conta de armazenamento na lista de recursos.

Em **Diagnóstico**, defina **Status** para **Ativado**. Verifique se **Métricas de agregação de Blob**, **Métricas por API de Blob** e **Logs de Blob** estão habilitados.

Quando concluir, clique em **Salvar**

![Painel Diagnósticos](media/storage-monitor-troubleshoot-storage-application/figure1.png)

## <a name="enable-alerts"></a>Habilitar alertas

Os alertas fornecem uma maneira de enviar email aos administradores ou disparar um webhook com base na transgressão de um limite por uma métrica. Neste exemplo, você pode habilitar um alerta para a métrica `SASClientOtherError`.

### <a name="navigate-to-the-storage-account-in-the-azure-portal"></a>Navegar até sua conta de armazenamento no Portal do Azure

No menu à esquerda, selecione **Grupos de Recursos**, selecione **myResourceGroup** e, em seguida, selecione sua conta de armazenamento na lista de recursos.

Na seção **Monitoramento**, selecione **Regras de alerta**.

Selecione **+ Adicionar alerta** e, em **Adicionar uma regra de alerta**, preencha as informações necessárias. Escolha `SASClientOtherError` na lista suspensa **Métrica**.

![Painel Diagnósticos](media/storage-monitor-troubleshoot-storage-application/figure2.png)

## <a name="simulate-an-error"></a>Simular um erro

Para simular um alerta válido, você pode tentar solicitar um blob inexistente da sua conta de armazenamento. Para fazer isso, substitua valor `<incorrect-blob-name>` por outro inexistente. Execute o exemplo de código a seguir algumas vezes para simular solicitações de blob com falha.

```azurecli-interactive
sasToken=$(az storage blob generate-sas \
    --account-name <storage-account-name> \
    --account-key <storage-account-key> \
    --container-name <container> \
    --name <incorrect-blob-name> \
    --permissions r \
    --expiry `date --date="next day" +%Y-%m-%d` \
    --output tsv)

curl https://<storage-account-name>.blob.core.windows.net/<container>/<incorrect-blob-name>?$sasToken
```

A imagem a seguir é um exemplo de alerta que é baseado em falha simulada executada com o exemplo anterior.

 ![Exemplo de alerta](media/storage-monitor-troubleshoot-storage-application/alert.png)

## <a name="download-and-view-logs"></a>Baixar e exibir logs

Os logs de repositório armazenam dados em um conjunto de blobs em um contêiner de blob denominado **$logs** em sua conta de armazenamento. Este contêiner não será exibido se você listar todos os contêineres de blob em sua conta, mas você poderá ver o conteúdo dele se acessá-lo diretamente.

Nesse cenário, você usa o [Analisador de Mensagens da Microsoft](http://technet.microsoft.com/library/jj649776.aspx) para interagir com a conta de Armazenamento do Azure.

### <a name="download-microsoft-message-analyzer"></a>Baixar Analisador de Mensagens da Microsoft

Baixe o [Analisador de Mensagens da Microsoft](https://www.microsoft.com/download/details.aspx?id=44226) e instale o aplicativo.

Inicie o aplicativo e escolha **Arquivo** > **Abrir** > **De Outras Fontes de Arquivo**.

Na caixa de diálogo **Seletor de Arquivos**, selecione **+ Adicionar Conexão do Azure**. Insira o **nome da conta de armazenamento** e a **chave de conta** e clique em **OK**.

![Analisador de Mensagens da Microsoft – caixa de diálogo Adicionar Conexão do Armazenamento do Azure](media/storage-monitor-troubleshoot-storage-application/figure3.png)

Quando você estiver conectado, expanda os contêineres no modo de exibição de árvore de armazenamento para exibir os blobs de log. Selecione o log mais recente e clique em **OK**.

![Analisador de Mensagens da Microsoft – caixa de diálogo Adicionar Conexão do Armazenamento do Azure](media/storage-monitor-troubleshoot-storage-application/figure4.png)

Na caixa de diálogo **Nova Sessão**, clique em **Iniciar** para exibir o log.

Depois que o log é aberto, você pode exibir os eventos de armazenamento. Como você pode ver na imagem a seguir, houve um `SASClientOtherError` disparado na conta de armazenamento. Para obter informações adicionais sobre o log de armazenamento, visite [Análise de Armazenamento](../common/storage-analytics.md).

![Analisador de Mensagens da Microsoft – exibindo eventos](media/storage-monitor-troubleshoot-storage-application/figure5.png)

O [Gerenciador de Armazenamento](https://azure.microsoft.com/features/storage-explorer/) é outra ferramenta que pode ser usada para interagir com suas contas de armazenamento, incluindo o contêiner **$logs** e os logs contidos nele.

## <a name="next-steps"></a>Próximas etapas

Na quarta e última parte da série, você aprendeu como monitorar e solucionar problemas de sua conta de armazenamento, por exemplo, como:

> [!div class="checklist"]
> * Ativar o registro em log e as métricas
> * Habilitar alertas para erros de autorização
> * Executar tráfego de teste com tokens SAS incorretos
> * Baixar e analisar logs

Siga este link para ver amostras de armazenamento pré-compiladas.

> [!div class="nextstepaction"]
> [Amostras de script do Armazenamento do Azure](storage-samples-blobs-cli.md)