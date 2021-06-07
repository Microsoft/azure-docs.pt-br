---
title: Monitorar e solucionar problemas de um aplicativo de armazenamento de nuvem no Azure | Microsoft Docs
description: Use ferramentas de diagnóstico, métricas e alertas para solucionar problemas e monitorar um aplicativo em nuvem.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 07/20/2018
ms.author: normesta
ms.reviewer: fryu
ms.openlocfilehash: 6c1d9b813db15900521968865c1036d53ad53f41
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95544778"
---
# <a name="monitor-and-troubleshoot-a-cloud-storage-application"></a>Monitorar e solucionar problemas de um aplicativo de armazenamento de nuvem

Este tutorial é quarta e última parte de uma série. Você aprende como monitorar e solucionar problemas de um aplicativo de armazenamento de nuvem.

Na primeira parte da série, você aprende a:

> [!div class="checklist"]
> * Ativar o registro em log e as métricas
> * Habilitar alertas para erros de autorização
> * Executar tráfego de teste com tokens SAS incorretos
> * Baixar e analisar logs

O [Azure Storage Analytics](../common/storage-analytics.md) fornece registro em log e dados de métrica para uma conta de armazenamento. Esses dados fornecem informações sobre a integridade da conta de armazenamento. Para coletar dados de análise de armazenamento do Azure, você pode configurar o registro em log, métricas e alertas. Esse processo envolve ativar o registro em log, configurar métricas e habilitar alertas.

Métricas e registro em log de contas de armazenamento são habilitados na guia **Diagnóstico** no Portal do Azure. O log de armazenamento permite que você registre detalhes de solicitações bem-sucedidas e com falhas na conta de armazenamento. Esses logs permitem ver detalhes de operações de leitura, gravação e exclusão em suas tabelas, filas e blobs do Azure. Eles também permitem que você veja os motivos para solicitações com falha, tais como tempos limite atingidos, limitação e erros de autorização.

## <a name="log-in-to-the-azure-portal"></a>Faça logon no Portal do Azure

Faça logon no [Portal do Azure](https://portal.azure.com)

## <a name="turn-on-logging-and-metrics"></a>Ativar o registro em log e as métricas

No menu à esquerda, selecione **Grupos de Recursos**, selecione **myResourceGroup** e, em seguida, selecione sua conta de armazenamento na lista de recursos.

Em **Configurações de diagnóstico (clássico)** , defina **Status** para **Ativado**. Verifique se todas as opções em **Propriedades de blob** estão habilitadas.

Quando concluir, clique em **Salvar**

![Captura de tela que realça a seção que contém as definições de configuração para ativar o registro em log e as métricas.](media/storage-monitor-troubleshoot-storage-application/enable-diagnostics.png)

## <a name="enable-alerts"></a>Habilitar alertas

Os alertas fornecem uma maneira de enviar email aos administradores ou disparar um webhook com base na transgressão de um limite por uma métrica. Neste exemplo, você pode habilitar um alerta para a métrica `SASClientOtherError`.

### <a name="navigate-to-the-storage-account-in-the-azure-portal"></a>Navegar até sua conta de armazenamento no Portal do Azure

Na seção **Monitoramento**, selecione **Alertas (clássico)** .

Selecione **Adicionar alerta de métrica (clássico)** e preencha o formulário **Adicionar regra** com as informações necessárias. Na lista suspensa **Métrica**, selecione `SASClientOtherError`. Para permitir que o alerta dispare após o primeiro erro, na lista suspensa **Condição** selecione **Maior que ou igual a**.

![Painel Diagnósticos](media/storage-monitor-troubleshoot-storage-application/add-alert-rule.png)

## <a name="simulate-an-error"></a>Simular um erro

Para simular um alerta válido, você pode tentar solicitar um blob inexistente da sua conta de armazenamento. O comando a seguir requer um nome de contêiner de armazenamento. Você pode usar o nome de um contêiner existente ou criar um novo para os fins deste exemplo.

Substitua os espaços reservados pelos valores reais (certifique-se de que `<INCORRECT_BLOB_NAME>` é definido como um valor que não existe) e execute o comando.

```azurecli-interactive
sasToken=$(az storage blob generate-sas \
    --account-name <STORAGE_ACCOUNT_NAME> \
    --account-key <STORAGE_ACCOUNT_KEY> \
    --container-name <CONTAINER_NAME> \
    --name <INCORRECT_BLOB_NAME> \
    --permissions r \
    --expiry `date --date="next day" +%Y-%m-%d`)

curl https://<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/<CONTAINER_NAME>/<INCORRECT_BLOB_NAME>?$sasToken
```

A imagem a seguir é um exemplo de alerta que é baseado em falha simulada executada com o exemplo anterior.

 ![Exemplo de alerta](media/storage-monitor-troubleshoot-storage-application/email-alert.png)

## <a name="download-and-view-logs"></a>Baixar e exibir logs

Os logs de repositório armazenam dados em um conjunto de blobs em um contêiner de blob denominado **$logs** em sua conta de armazenamento. Este contêiner não será exibido se você listar todos os contêineres de blob em sua conta, mas você poderá ver o conteúdo dele se acessá-lo diretamente.

Nesse cenário, você usa o [Analisador de Mensagens da Microsoft](/message-analyzer/microsoft-message-analyzer-operating-guide) para interagir com a conta de Armazenamento do Azure.

### <a name="download-microsoft-message-analyzer"></a>Baixar Analisador de Mensagens da Microsoft

Baixe o [Analisador de Mensagens da Microsoft](/message-analyzer/installing-and-upgrading-message-analyzer) e instale o aplicativo.

Inicie o aplicativo e escolha **Arquivo** > **Abrir** > **De Outras Fontes de Arquivo**.

Na caixa de diálogo **Seletor de Arquivos**, selecione **+ Adicionar Conexão do Azure**. Insira o **nome da conta de armazenamento** e a **chave de conta** e clique em **OK**.

![Analisador de Mensagens da Microsoft – caixa de diálogo Adicionar Conexão do Armazenamento do Azure](media/storage-monitor-troubleshoot-storage-application/figure3.png)

Quando você estiver conectado, expanda os contêineres no modo de exibição de árvore de armazenamento para exibir os blobs de log. Selecione o log mais recente e clique em **OK**.

![Captura de tela que mostra o Analisador de Mensagens da Microsoft e realça o arquivo de log selecionado.](media/storage-monitor-troubleshoot-storage-application/figure4.png)

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