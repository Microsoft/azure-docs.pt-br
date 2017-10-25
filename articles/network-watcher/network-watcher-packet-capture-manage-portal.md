---
title: Gerenciar as capturas de pacotes com o Observador de Rede do Azure - portal do Azure | Microsoft Docs
description: "Esta página explica como gerenciar o recurso de captura de pacotes do Observador de Rede usando o portal do Azure"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 59edd945-34ad-4008-809e-ea904781d918
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 818f6513625a2677668dd6b6869ef969fe015bf7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>Gerenciar as capturas de pacotes com o Observador de Rede do Azure usando o portal

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [CLI 1.0](network-watcher-packet-capture-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-packet-capture-manage-cli.md)
> - [API REST do Azure](network-watcher-packet-capture-manage-rest.md)

Captura de pacote do Observador de Rede permite que você crie sessões de captura para controlar o tráfego em uma máquina virtual. Os filtros são fornecidos para a sessão de captura garantir que somente o tráfego que você deseja capturar. Captura de pacote ajuda a diagnosticar problemas de rede reativo e proativo. Outros usos incluem a coleta de estatísticas de rede, obter informações sobre as invasões de rede, para depurar comunicações cliente-servidor e muito mais. Por poder remotamente disparar a captura de pacote, esse recurso alivia a carga da execução de uma captura de pacote e manualmente no computador desejado, o que economiza tempo.

Este artigo o guiará durante as tarefas de gerenciamento diferentes que estão atualmente disponíveis para captura de pacote.

- [**Iniciar uma captura de pacote**](#start-a-packet-capture)
- [**Parar uma captura de pacote**](#stop-a-packet-capture)
- [**Excluir uma captura de pacote**](#delete-a-packet-capture)
- [**Baixar uma captura de pacote**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tem os seguintes recursos:

- Uma instância do Observador de Rede na região na qual você deseja criar uma captura de pacotes
- Uma máquina virtual com a extensão da captura de pacotes habilitada.

> [!IMPORTANT]
> A captura de pacotes requer uma extensão da máquina virtual `AzureNetworkWatcherExtension`. Para instalar a extensão em uma VM do Windows, visite [Extensão da máquina virtual do Agente do Observador de Rede do Azure para Windows](../virtual-machines/windows/extensions-nwa.md) e para a VM do Linux, visite [Extensão da máquina virtual do Agente do Observador de Rede do Azure para Linux](../virtual-machines/linux/extensions-nwa.md).

### <a name="packet-capture-agent-extension-through-the-portal"></a>Extensão do agente de Captura de Pacotes por meio do portal

Para instalar o agente de VM da captura de pacotes por meio do portal, navegue até sua máquina virtual, clique em **Extensões** > **Adicionar** e pesquise **Agente do Observador de Rede para Windows**

![exibição de agentes][agent]

## <a name="packet-capture-overview"></a>visão geral da Captura de Pacotes

Navegue até o [portal do Azure](https://portal.azure.com) e clique em **Rede** > **Observador de Rede** > **Captura de Pacotes**

A página de visão geral mostra uma lista de todas as capturas de pacotes existentes, independentemente do estado.

> [!NOTE]
> A captura de pacotes requer uma conectividade com a conta de armazenamento na porta 443.

![tela de visão geral da captura de pacotes][1]

## <a name="start-a-packet-capture"></a>Iniciar uma captura de pacotes

Clique em **Adicionar** para criar uma captura de pacotes.

As propriedades que podem ser definidas em uma captura de pacotes são:

**Configurações principais**

- **Assinatura** - esse valor é a assinatura usada; uma instância do Observador de Rede é necessária em cada assinatura.
- **Grupo de recursos** - o grupo de recursos da máquina virtual que está sendo direcionado.
- **Máquina virtual de destino** - a máquina virtual que está executando a captura de pacotes
- **Nome da captura de pacotes** - esse valor é o nome da captura de pacotes.

**Configuração da captura**

- **Conta de Armazenamento** - Determina se a captura do pacote é salva em uma conta de armazenamento.
- **Arquivo** - determina se uma captura de pacote sé salva localmente na máquina virtual.
- **Contas de Armazenamento** - a conta de armazenamento selecionada na qual salvar a captura de pacotes. O local padrão é https://{nome da conta de armazenamento}.blob.core.windows.net/network-watcher-logs/subscriptions/{id assinatura}/resourcegroups/{nome do grupo de recursos}/providers/microsoft.compute/virtualmachines/{nome máquina virtual}/{AA}/{MM}/{DD}/packetcapture_{HH}_{MM}_{SS}_{XXX}.cap. (Habilitado somente se o **Armazenamento** estiver selecionado)
- **Caminho do arquivo local** - o caminho local em uma máquina virtual para salvar a captura de pacotes. (Habilitado somente se o **Arquivo** estiver selecionado). Um caminho válido deve ser fornecido
- **Máximo de bytes por pacote** - o número de bytes capturados de cada pacote; todos os bytes serão capturados se deixado em branco.
- **Máximo de bytes por sessão** – o número total de bytes capturados; quando o valor é atingido, a captura de pacotes é interrompida.
- **Tempo limite (segundos)** -define um limite de tempo para a captura de pacotes parar. O padrão é 18.000 segundos.

> [!NOTE]
> As contas de armazenamento Premium atualmente não têm suporte para armazenar as capturas de pacotes.

**Filtragem (Opcional)**

- **Protocolo** - o protocolo para filtrar a captura de pacotes. Os valores disponíveis são TCP, UDP e Qualquer.
- **Endereço IP local** - esse valor filtra a captura de pacotes para os pacotes cujo endereço IP local corresponde ao valor do filtro.
- **Porta local** - esse valor filtra a captura de pacotes para os pacotes cuja porta local corresponde ao valor do filtro.
- **Endereço IP remoto** - esse valor filtra a captura de pacotes para os pacotes cujo endereço IP remoto corresponde ao valor do filtro.
- **Porta remota** - esse valor filtra a captura de pacotes para os pacotes cuja porta remota corresponde ao valor do filtro.

> [!NOTE]
> Os valores da porta e do endereço IP podem ser um único valor, um intervalo de valores ou um conjunto. (ou seja, 80-1024 para a porta) Você pode definir quantos filtros quiser.

Depois que os valores forem preenchidos, clique em **OK** para criar a captura de pacotes.

![criar uma captura de pacotes][2]

Depois do limite de tempo definido na captura de pacotes ter expirado, a captura será interrompida e poderá ser examinada. Também é possível interromper manualmente as sessões de capturas de pacotes.

## <a name="delete-a-packet-capture"></a>Excluir uma captura de pacotes

Na exibição da captura de pacotes, clique no **menu de contexto** (...) ou clique com botão direito e clique em **excluir** para parar a captura de pacotes

![excluir uma captura de pacotes][3]

> [!NOTE]
> Excluir uma captura de pacotes não exclui o arquivo na conta de armazenamento.

Você será solicitado a confirmar se deseja excluir a captura de pacotes; clique em **Sim**

![confirmação][4]

## <a name="stop-a-packet-capture"></a>Parar uma captura de pacotes

Na exibição da captura de pacotes, clique no **menu de contexto** (...) ou clique com botão direito e clique em **Parar** para parar a captura de pacotes

## <a name="download-a-packet-capture"></a>Baixar um pacote de capturas

Quando a sessão de captura de pacotes concluir, o arquivo de captura será carregado no armazenamento de blobs ou em um arquivo local na VM. O local de armazenamento da captura de pacotes é definido na criação da sessão. Uma ferramenta conveniente para acessar esses arquivos de captura salvos em uma conta de armazenamento é o Gerenciador de Armazenamento do Microsoft Azure, que pode ser baixado aqui: http://storageexplorer.com/

Se uma conta de armazenamento for especificada, os arquivos de captura de pacote serão salvos em uma conta de armazenamento no seguinte local:
```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Próximas etapas

Saiba como automatizar as capturas de pacotes com alertas da Máquina Virtual exibindo [Criar uma captura de pacotes disparada por alertas](network-watcher-alert-triggered-packet-capture.md)

Localize se determinado tráfego é permitido dentro ou fora de sua VM visitando [Verificar o fluxo do IP](network-watcher-check-ip-flow-verify-portal.md)

<!-- Image references -->
[1]: ./media/network-watcher-packet-capture-manage-portal/figure1.png
[2]: ./media/network-watcher-packet-capture-manage-portal/figure2.png
[3]: ./media/network-watcher-packet-capture-manage-portal/figure3.png
[4]: ./media/network-watcher-packet-capture-manage-portal/figure4.png
[agent]: ./media/network-watcher-packet-capture-manage-portal/agent.png













