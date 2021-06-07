---
title: Gerenciar capturas de pacote-portal do Azure
titleSuffix: Azure Network Watcher
description: Aprenda a gerenciar o recurso de captura de pacote do Observador de Rede usando o portal do Azure.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: d4112bd1d632ec21e2d8cb565027277eeb85452d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98018252"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>Gerenciar as capturas de pacotes com o Observador de Rede do Azure usando o portal

Captura de pacote do Observador de Rede permite que você crie sessões de captura para controlar o tráfego em uma máquina virtual. Os filtros são fornecidos para a sessão de captura garantir que somente o tráfego que você deseja capturar. A captura de pacote ajuda a diagnosticar problemas de rede de maneiras reativas e proativas. Outros usos incluem a coleta de estatísticas de rede, obter informações sobre as invasões de rede, depurar comunicações entre cliente e servidor e muito mais. Poder disparar remotamente a captura de pacote alivia a carga de executar uma captura de pacote manualmente em uma máquina virtual desejada, o que economiza tempo.

Neste artigo, você aprenderá a iniciar, parar, baixar e excluir uma captura de pacotes. 

## <a name="before-you-begin"></a>Antes de começar

A captura de pacote requer a seguinte conectividade TCP de saída:
- para a conta de armazenamento escolhida pela porta 443
- para 169.254.169.254 sobre a porta 80
- para 168.63.129.16 sobre a porta 8037

> [!NOTE]
> As portas mencionadas nos últimos dois casos acima são comuns em todos os recursos do observador de rede que envolvem a extensão do observador de rede e podem ocasionalmente ser alteradas.


Se um grupo de segurança de rede estiver associado ao adaptador de rede ou à sub-rede na qual o adaptador de rede está, verifique se de existem regras que permitem as portas anteriores. Da mesma forma, adicionar rotas de tráfego definidas pelo usuário à sua rede pode impedir a conectividade com os IPs e as portas mencionados acima. Verifique se eles estão acessíveis. 

## <a name="start-a-packet-capture"></a>Iniciar uma captura de pacotes

1. No seu navegador, vá para o [portal do Azure](https://portal.azure.com), selecione **Todos os serviços**, então selecione **Observador de Rede** na **Seção de rede**.
2. Selecione **Captura de pacotes** em **Ferramentas de diagnóstico de rede**. Qualquer captura de pacotes existentes é listada, independentemente de seu status.
3. Selecione **Adicionar** para criar uma captura de pacotes. Você pode selecionar valores para as seguintes propriedades:
   - **Assinatura**: a assinatura em que máquina virtual para a qual você deseja criar o pacote de captura está.
   - **Grupo de recursos**: o grupo de recursos da máquina virtual.
   - **Máquina virtual de destino**: a máquina virtual para a qual você deseja criar a captura de pacotes.
   - **Nome da captura de pacote**: um nome para a captura de pacotes.
   - **Conta de armazenamento ou arquivo**: selecione **Conta de armazenamento**, **Arquivo** ou ambos. Se você selecionar **Arquivo**, a captura será gravada em um caminho na máquina virtual.
   - **Caminho de arquivo local**: o caminho local na máquina virtual em que a captura de pacote será salva (válido somente quando *Arquivo* está selecionado). O caminho deve ser um caminho válido. Se você estiver usando uma máquina virtual Linux, o caminho deverá começar com */var/captures*.
   - **Contas de armazenamento**: selecione uma conta de armazenamento existente se você tiver selecionado *Conta de armazenamento*. Essa opção só estará disponível se você tiver selecionado **Armazenamento**.
   
     > [!NOTE]
     > As contas de armazenamento Premium atualmente não têm suporte para armazenar as capturas de pacotes.

   - **Máximo de bytes por pacote**: o número de bytes de cada pacote capturado. Se deixado em branco, todos os bytes serão capturados.
   - **Máximo de bytes por sessão**: o número total de bytes capturados. Quando o valor é atingido, a captura de pacotes é interrompida.
   - **Tempo limite (segundos)**: o limite de tempo antes que a captura de pacotes seja interrompida. O padrão é de 18 mil segundos.
   - Filtragem (Opcional). Selecione **+Adicionar filtro**
     - **Protocolo**: o protocolo para filtrar a captura de pacotes. Os valores disponíveis são TCP, UDP e Qualquer.
     - **Endereço IP local**: filtra a captura de pacote para pacotes em que o endereço IP local corresponde a esse valor.
     - **Porta local:** filtra a captura de pacotes para os pacotes em que a porta local corresponde a esse valor.
     - **Endereço IP remoto**: filtra a captura de pacote para pacotes em que o endereço IP remoto corresponde a esse valor.
     - **Porta remota:** filtra a captura de pacotes para os pacotes em que a porta remota corresponde a esse valor.
    
     > [!NOTE]
     > Os valores da porta e do endereço IP podem ser um único valor, um intervalo de valores ou um intervalo, como 80 a 1024, para porta. Você pode definir tantos filtros quantos forem necessários.

4. Selecione **OK**.

Depois de o limite de tempo definido na captura de pacotes expirar, a captura é interrompida e pode ser examinada. Você também pode interromper uma sessão de captura de pacote manualmente.

> [!NOTE]
> O portal automaticamente:
>  * Cria um observador de rede na mesma região que a região em que a máquina virtual que você selecionou existe, se a região ainda não tem um observador de rede.
>  * Adiciona a extensão da máquina virtual *AzureNetworkWatcherExtension,* [Linux](../virtual-machines/extensions/network-watcher-linux.md) ou [Windows](../virtual-machines/extensions/network-watcher-windows.md) à máquina virtual se ela ainda não está instalada.

## <a name="delete-a-packet-capture"></a>Excluir uma captura de pacotes

1. Na exibição da captura de pacote, selecione **…** no lado direito da captura de pacote, ou clique com o botão direito do mouse em uma captura de pacote existente e selecione **Excluir**.
2. Você será solicitado a confirmar se deseja excluir a captura de pacotes. Selecione **Sim** na barra superior.

> [!NOTE]
> Excluir uma captura de pacotes não exclui o arquivo de captura na conta de armazenamento nem na máquina virtual.

## <a name="stop-a-packet-capture"></a>Parar uma captura de pacotes

Na exibição da captura de pacote, selecione **…** no lado direito da captura de pacote, ou clique com o botão direito do mouse em uma captura de pacote existente e selecione **Parar**.

## <a name="download-a-packet-capture"></a>Baixar um pacote de capturas

Quando a sessão de captura de pacotes for concluída, o arquivo de captura será carregado no Armazenamento de Blobs ou em um arquivo local na máquina virtual. O local de armazenamento da captura de pacote será definido durante a criação da captura de pacote. Uma ferramenta conveniente para acessar esses arquivos de captura salvos em uma conta de armazenamento é o Gerenciador de Armazenamento do Microsoft Azure, que você pode [baixar](https://storageexplorer.com/).

Se uma conta de armazenamento for especificada, os arquivos de captura de pacote serão salvos em uma conta de armazenamento no seguinte local:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

Se você selecionou **Arquivo** ao criar a captura, pode exibir ou baixar o arquivo do caminho configurado na máquina virtual.

## <a name="next-steps"></a>Próximas etapas

- Para saber como automatizar a captura de pacote com alertas de máquina virtual, veja [Criar uma captura de pacote acionada por alerta](network-watcher-alert-triggered-packet-capture.md).
- Para determinar se um tráfego específico é permitido dentro ou fora de uma máquina virtual, veja [Diagnosticar um problema de filtro de tráfego de rede de máquina virtual](diagnose-vm-network-traffic-filtering-problem.md).