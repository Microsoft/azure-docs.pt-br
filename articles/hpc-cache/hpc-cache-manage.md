---
title: Gerenciar e atualizar o cache HPC do Azure
description: Como gerenciar e atualizar o cache HPC do Azure usando o portal do Azure ou CLI do Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 08/31/2020
ms.author: v-erkel
ms.openlocfilehash: 067b12d4dcfd5ba2b730204ef680b900d79f1b72
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94648065"
---
# <a name="manage-your-cache"></a>Gerenciar seu cache

A página Visão geral do cache na portal do Azure mostra detalhes do projeto, status do cache e estatísticas básicas para seu cache. Ele também tem controles para parar ou iniciar o cache, excluir o cache, liberar dados para o armazenamento de longo prazo e atualizar o software.

Este artigo também explica como realizar essas tarefas básicas com o CLI do Azure.

Para abrir a página Visão geral, selecione o recurso de cache na portal do Azure. Por exemplo, carregue a página **todos os recursos** e clique no nome do cache.

![captura de tela de uma página de visão geral da instância do cache HPC do Azure](media/hpc-cache-overview.png)

Os botões na parte superior da página podem ajudá-lo a gerenciar o cache:

* **Iniciar** e [**parar**](#stop-the-cache) – retoma ou suspende a operação de cache
* [**Flush**](#flush-cached-data) -grava dados alterados para destinos de armazenamento
* [**Atualização**](#upgrade-cache-software) – atualiza o software de cache
* [**Coletar diagnóstico**](#collect-diagnostics) – carrega informações de depuração
* **Atualizar** – recarrega a página Visão geral
* [**Excluir**](#delete-the-cache) – destrói permanentemente o cache

Leia mais sobre essas opções abaixo.

Clique na imagem abaixo para assistir a um [vídeo](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) que demonstra as tarefas de gerenciamento de cache.

[![miniatura de vídeo: cache do HPC do Azure: gerenciar (clique para visitar a página de vídeo)](media/video-5-manage.png)](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)

## <a name="stop-the-cache"></a>Parar o cache

Você pode interromper o cache para reduzir os custos durante um período inativo. Você não é cobrado pelo tempo de atividade enquanto o cache é interrompido, mas você é cobrado pelo armazenamento em disco alocado do cache. (Consulte a página de [preços](https://aka.ms/hpc-cache-pricing) para obter detalhes.)

Um cache interrompido não responde às solicitações do cliente. Você deve desmontar os clientes antes de parar o cache.

### <a name="portal"></a>[Portal](#tab/azure-portal)

O botão **parar** suspende um cache ativo. O botão **parar** está disponível quando o status de um cache está **íntegro** ou **degradado**.

![captura de tela dos botões superior com parar realçado e uma mensagem pop-up que descreve a ação de parada e perguntando "deseja continuar?" com Sim (padrão) e nenhum botão](media/stop-cache.png)

Depois de clicar em Sim para confirmar a interrupção do cache, o cache libera automaticamente seu conteúdo para os destinos de armazenamento. Esse processo pode levar algum tempo, mas garante a consistência dos dados. Por fim, o status do cache é alterado para **parado**.

Para reativar um cache interrompido, clique no botão **Iniciar** . Nenhuma confirmação é necessária.

![captura de tela dos botões superior com início realçado](media/start-cache.png)

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[Configurar CLI do Azure para o cache do HPC do Azure](./az-cli-prerequisites.md).

Suspenda temporariamente um cache com o comando [AZ HPC-cache Stop](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-stop) . Essa ação só é válida quando o status de um cache está **íntegro** ou **degradado**.

O cache libera automaticamente seu conteúdo para os destinos de armazenamento antes de parar. Esse processo pode levar algum tempo, mas garante a consistência dos dados.

Quando a ação for concluída, o status do cache será alterado para **parado**.

Reative um cache interrompido com [AZ HPC-cache Start](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-start).

Quando você emite o comando iniciar ou parar, a linha de comando mostra uma mensagem de status "em execução" até que a operação seja concluída.

```azurecli
$ az hpc-cache start --name doc-cache0629
 - Running ..
```

Na conclusão, a mensagem é atualizada para "concluído" e mostra códigos de retorno e outras informações.

```azurecli
$ az hpc-cache start --name doc-cache0629
{- Finished ..
  "endTime": "2020-07-01T18:46:43.6862478+00:00",
  "name": "c48d320f-f5f5-40ab-8b25-0ac065984f62",
  "properties": {
    "output": "success"
  },
  "startTime": "2020-07-01T18:40:28.5468983+00:00",
  "status": "Succeeded"
}
```

---

## <a name="flush-cached-data"></a>Liberar dados armazenados em cache

O botão **liberar** na página Visão geral informa o cache para gravar imediatamente todos os dados alterados armazenados no cache para os destinos de armazenamento de back-end. O cache salva rotineiramente os dados nos destinos de armazenamento, portanto, não é necessário fazer isso manualmente, a menos que você queira verificar se o sistema de armazenamento de back-end está atualizado. Por exemplo, você pode usar **flush** antes de tirar um instantâneo de armazenamento ou verificar o tamanho do conjunto de dados.

> [!NOTE]
> Durante o processo de liberação, o cache não pode atender às solicitações do cliente. O acesso ao cache é suspenso e continua após a conclusão da operação.

Quando você inicia a operação de liberação de cache, o cache para de aceitar solicitações de cliente e o status do cache na página de visão geral muda para a **liberação**.

Os dados no cache são salvos nos destinos de armazenamento apropriados. Dependendo da quantidade de dados que precisa ser liberada, o processo pode levar alguns minutos ou mais de uma hora.

Depois que todos os dados são salvos em destinos de armazenamento, o cache começa automaticamente a executar solicitações de cliente novamente. O status do cache retorna para **íntegro**.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Para liberar o cache, clique no botão **liberar** e, em seguida, clique em **Sim** para confirmar a ação.

![captura de tela dos botões superior com liberação realçada e uma mensagem pop-up descrevendo a ação de liberação e perguntando "deseja continuar?" com Sim (padrão) e nenhum botão](media/hpc-cache-flush.png)

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[Configurar CLI do Azure para o cache do HPC do Azure](./az-cli-prerequisites.md).

Use [AZ HPC-cache flush](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-flush) para forçar o cache a gravar todos os dados alterados para os destinos de armazenamento.

Exemplo:

```azurecli
$ az hpc-cache flush --name doc-cache0629 --resource-group doc-rg
 - Running ..
```

Quando a liberação for concluída, uma mensagem de êxito será retornada.

```azurecli
{- Finished ..
  "endTime": "2020-07-09T17:26:13.9371983+00:00",
  "name": "c22f8e12-fcf0-49e5-b897-6a6e579b6489",
  "properties": {
    "output": "success"
  },
  "startTime": "2020-07-09T17:25:21.4278297+00:00",
  "status": "Succeeded"
}
$
```

---

## <a name="upgrade-cache-software"></a>Atualizar o software de cache

Se uma nova versão de software estiver disponível, o botão **Atualizar** se tornará ativo. Você também deverá ver uma mensagem na parte superior da página sobre como atualizar o software.

![captura de tela da linha superior de botões com o botão de atualização habilitado](media/hpc-cache-upgrade-button.png)

O acesso do cliente não é interrompido durante uma atualização de software, mas o desempenho do cache fica mais lento. Planeje a atualização de software durante horas de uso fora do pico ou em um período de manutenção planejada.

A atualização de software pode levar várias horas. Os caches configurados com maior taxa de transferência levam mais tempo para atualizar que os caches com valores menores de taxa de transferência de pico.

Quando uma atualização de software estiver disponível, você terá uma semana ou mais para aplicá-la manualmente. A data de término é listada na mensagem de atualização. Se você não atualizar durante esse tempo, o Azure aplicará automaticamente a atualização ao seu cache. O tempo de atualização automática não é configurável. Se você estiver preocupado com o impacto no desempenho do cache, atualize o software por conta própria antes que o período de tempo expire.

Se o cache for interrompido quando a data de término passar, o cache atualizará automaticamente o software na próxima vez que for iniciado. (A atualização pode não iniciar imediatamente, mas será iniciada na primeira hora.)

### <a name="portal"></a>[Portal](#tab/azure-portal)

Clique no botão **Atualizar** para iniciar a atualização de software. O status do cache muda para **atualizando** até que a operação seja concluída.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[Configurar CLI do Azure para o cache do HPC do Azure](./az-cli-prerequisites.md).

Na CLI do Azure, novas informações de software são incluídas no final do relatório de status do cache. (Use [AZ HPC-cache show](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-show) para verificar.) Procure a cadeia de caracteres "upgradeStatus" na mensagem.

Use [AZ HPC-cache upgrade-firmware](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-upgrade-firmware) para aplicar a atualização, se existir alguma.

Se nenhuma atualização estiver disponível, essa operação não terá efeito.

Este exemplo mostra o status do cache (nenhuma atualização está disponível) e os resultados do comando upgrade-firmware.

```azurecli
$ az hpc-cache show --name doc-cache0629
{
  "cacheSizeGb": 3072,
  "health": {
    "state": "Healthy",
    "statusDescription": "The cache is in Running state"
  },

<...>

  "tags": null,
  "type": "Microsoft.StorageCache/caches",
  "upgradeStatus": {
    "currentFirmwareVersion": "5.3.61",
    "firmwareUpdateDeadline": "0001-01-01T00:00:00+00:00",
    "firmwareUpdateStatus": "unavailable",
    "lastFirmwareUpdate": "2020-06-29T22:18:32.004822+00:00",
    "pendingFirmwareVersion": null
  }
}
$ az hpc-cache upgrade-firmware --name doc-cache0629
$
```

---

## <a name="collect-diagnostics"></a>Coletar diagnóstico

O botão **coletar diagnósticos** inicia manualmente o processo para coletar informações do sistema e carregá-lo para o serviço Microsoft e o suporte para solução de problemas. O cache coleta e carrega automaticamente as mesmas informações de diagnóstico se ocorre um problema grave de cache.

Use este controle se o serviço da Microsoft e o suporte solicitarem.

Depois de clicar no botão, clique em **Sim** para confirmar o carregamento.

![captura de tela da mensagem de confirmação de pop-up ' Iniciar coleta de diagnóstico '. O botão padrão ' Sim ' está realçado.](media/diagnostics-confirm.png)

## <a name="delete-the-cache"></a>Excluir o cache

O botão **excluir** destrói o cache. Quando você exclui um cache, todos os seus recursos são destruídos e não incorrem mais em encargos de conta.

Os volumes de armazenamento de back-end usados como destinos de armazenamento não são afetados quando você exclui o cache. Você pode adicioná-los a um cache futuro posteriormente ou descomissiona-los separadamente.

> [!NOTE]
> O cache HPC do Azure não grava automaticamente os dados alterados do cache nos sistemas de armazenamento de back-end antes de excluir o cache.
>
> Para garantir que todos os dados no cache tenham sido gravados no armazenamento de longo prazo, [interrompa o cache antes de](#stop-the-cache) excluí-lo. Verifique se ele mostra o status **parado** antes de excluir.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Depois de parar o cache, clique no botão **excluir** para remover permanentemente o cache.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[Configurar CLI do Azure para o cache do HPC do Azure](./az-cli-prerequisites.md).

Use o comando CLI do Azure [AZ HPC-cache Delete](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-delete) para remover permanentemente o cache.

Exemplo:
```azurecli
$ az hpc-cache delete --name doc-cache0629
 - Running ..

<...>

{- Finished ..
  "endTime": "2020-07-09T22:24:35.1605019+00:00",
  "name": "7d3cd0ba-11b3-4180-8298-d9cafc9f22c1",
  "startTime": "2020-07-09T22:13:32.0732892+00:00",
  "status": "Succeeded"
}
$
```

---

## <a name="cache-metrics-and-monitoring"></a>Métricas e monitoramento de cache

A página Visão geral mostra grafos para algumas estatísticas básicas de cache-taxa de transferência de cache, operações por segundo e latência.

![captura de tela de três gráficos de linha mostrando as estatísticas mencionadas acima para um cache de exemplo](media/hpc-cache-overview-stats.png)

Esses gráficos fazem parte das ferramentas internas de monitoramento e análise do Azure. Ferramentas e alertas adicionais estão disponíveis nas páginas no cabeçalho **monitoramento** na barra lateral do Portal. Saiba mais na seção portal da documentação de [monitoramento do Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre as [ferramentas de métricas e estatísticas do Azure](../azure-monitor/index.yml)
* Obtenha [ajuda com o cache do HPC do Azure](hpc-cache-support-ticket.md)
