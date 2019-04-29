---
title: Gerenciar as capturas de pacotes com o Observador de Rede do Azure - portal do Azure | Microsoft Docs
description: Aprenda a gerenciar o recurso de captura de pacote do Observador de Rede usando o portal do Azure.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 59edd945-34ad-4008-809e-ea904781d918
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: kumud
ms.openlocfilehash: 22bdd50f129a48ade97db323f904f7e652a00d39
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725849"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>Gerenciar as capturas de pacotes com o Observador de Rede do Azure usando o portal

Captura de pacote do Observador de Rede permite que você crie sessões de captura para controlar o tráfego em uma máquina virtual. Os filtros são fornecidos para a sessão de captura garantir que somente o tráfego que você deseja capturar. A captura de pacote ajuda a diagnosticar problemas de rede de maneiras reativas e proativas. Outros usos incluem a coleta de estatísticas de rede, obter informações sobre as invasões de rede, depurar comunicações entre cliente e servidor e muito mais. Poder disparar remotamente a captura de pacote alivia a carga de executar uma captura de pacote manualmente em uma máquina virtual desejada, o que economiza tempo.

Neste artigo, você aprenderá a iniciar, parar, baixar e excluir uma captura de pacotes. 

## <a name="before-you-begin"></a>Antes de começar

A captura de pacotes requer a seguinte conectividade:
* Conectividade de saída para uma conta de armazenamento na porta 443.
* Conectividade de entrada e saída para 169.254.169.254
* Conectividade de entrada e saída para 168.63.129.16

Se um grupo de segurança de rede estiver associado ao adaptador de rede ou à sub-rede na qual o adaptador de rede está, verifique se de existem regras que permitem as portas anteriores. 

## <a name="start-a-packet-capture"></a>Iniciar uma captura de pacotes

1. No seu navegador, vá para o [portal do Azure](https://portal.azure.com), selecione **Todos os serviços**, então selecione **Observador de Rede** na **Seção de rede**.
2. Selecione **Captura de pacotes** em **Ferramentas de diagnóstico de rede**. Qualquer captura de pacotes existentes é listada, independentemente de seu status.
3. Selecione **Adicionar** para criar uma captura de pacotes. Você pode selecionar valores para as seguintes propriedades:
   - **Assinatura**: A assinatura que a máquina virtual que você deseja criar o pacote de captura para está em.
   - **Grupo de recursos**: O grupo de recursos da máquina virtual.
   - **Máquina virtual de destino**: A máquina virtual que você deseja criar a captura de pacotes para.
   - **Nome da captura de pacote**: Um nome para a captura de pacotes.
   - **Conta de armazenamento ou o arquivo**: Selecione **conta de armazenamento**, **arquivo**, ou ambos. Se você selecionar **Arquivo**, a captura será gravada em um caminho na máquina virtual.
   - **Caminho do arquivo local**: O caminho local na máquina virtual onde a captura de pacote será salvo (válido somente quando *arquivo* está selecionado). O caminho deve ser um caminho válido. Se você estiver usando uma máquina virtual Linux, o caminho deverá começar com */var/captures*.
   - **Contas de armazenamento**: Selecione uma conta de armazenamento existente, se você selecionou *conta de armazenamento*. Essa opção só estará disponível se você tiver selecionado **Armazenamento**.
   
     > [!NOTE]
     > As contas de armazenamento Premium atualmente não têm suporte para armazenar as capturas de pacotes.

   - **Máximo de bytes por pacote**: O número de bytes de cada pacote que são capturados. Se deixado em branco, todos os bytes serão capturados.
   - **Máximo de bytes por sessão**: O número total de bytes capturados. Quando o valor é atingido, a captura de pacotes é interrompida.
   - **Tempo limite (segundos)**: O limite de tempo antes que a captura de pacotes é interrompida. O padrão é de 18 mil segundos.
   - Filtragem (Opcional). Selecione **+Adicionar filtro**
     - **Protocolo**: O protocolo de filtragem da captura de pacote. Os valores disponíveis são TCP, UDP e Qualquer.
     - **Endereço IP local**: Filtra a captura de pacote para pacotes onde o endereço IP local corresponde ao valor.
     - **Porta local**: Filtra a captura de pacotes para os pacotes em que a porta local corresponde ao valor.
     - **Endereço IP remoto**: Filtra a captura de pacote para pacotes onde o endereço IP remoto corresponde ao valor.
     - **Porta remota**: Filtra a captura de pacote para pacotes onde a porta remota corresponde ao valor.
    
     > [!NOTE]
     > Os valores da porta e do endereço IP podem ser um único valor, um intervalo de valores ou um intervalo, como 80 a 1024, para porta. Você pode definir tantos filtros quantos forem necessários.

4. Selecione **OK**.

Depois de o limite de tempo definido na captura de pacotes expirar, a captura é interrompida e pode ser examinada. Você também pode interromper uma sessão de captura de pacote manualmente.

> [!NOTE]
> O portal automaticamente:
>  * Cria um observador de rede na mesma região que a região em que a máquina virtual que você selecionou existe, se a região ainda não tem um observador de rede.
>  * Adiciona a extensão da máquina virtual *AzureNetworkWatcherExtension*, [Linux](../virtual-machines/linux/extensions-nwa.md) ou [Windows](../virtual-machines/windows/extensions-nwa.md) à máquina virtual se ela ainda não está instalada.

## <a name="delete-a-packet-capture"></a>Excluir uma captura de pacotes

1. Na exibição da captura de pacote, selecione **…** no lado direito da captura de pacote, ou clique com o botão direito do mouse em uma captura de pacote existente e selecione **Excluir**.
2. Você será solicitado a confirmar se deseja excluir a captura de pacotes. Selecione **Sim**.

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

## <a name="next-steps"></a>Próximos passos

- Para saber como automatizar a captura de pacote com alertas de máquina virtual, veja [Criar uma captura de pacote acionada por alerta](network-watcher-alert-triggered-packet-capture.md).
- Para determinar se um tráfego específico é permitido dentro ou fora de uma máquina virtual, veja [Diagnosticar um problema de filtro de tráfego de rede de máquina virtual](diagnose-vm-network-traffic-filtering-problem.md).
