---
title: Como visualizar os padrões de tráfego de rede com ferramentas de software livre e observador de rede do Azure | Microsoft Docs
description: Esta página descreve como usar a captura de pacote do observador de rede com CapAnalysis para visualizar os padrões de tráfego para e de suas VMs.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 936d881b-49f9-4798-8e45-d7185ec9fe89
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 7c239bbf577645ddb8ab12c525d1b3a8832421df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60680464"
---
# <a name="visualize-network-traffic-patterns-to-and-from-your-vms-using-open-source-tools"></a>Como visualizar padrões de tráfego de rede de e para suas VMs usando ferramentas de software livre

As capturas de pacote contêm dados de rede que permitem que você execute análise forense e inspeções profundas. Você pode usar muitas das ferramentas de softwares livres disponíveis para analisar as capturas de pacote para aprofundar-se sobre a sua rede. Uma dessas ferramentas é a CapAnalysis, uma ferramenta de visualização de captura de pacote de software livre. Visualizar dados de captura de pacote é uma maneira valiosa para aprofundar-se rapidamente sobre os padrões e anomalias na rede. As visualizações também fornecem um meio de compartilhar essas informações de maneira facilmente consumível.

O Observador de Rede do Azure fornece a capacidade de capturar dados, permitindo que você execute as capturas de pacote na sua rede. Este artigo fornece um passo a passo sobre como visualizar e aprofundar-se sobre as capturas de pacote usando CapAnalysis com o Observador de Rede.

## <a name="scenario"></a>Cenário

Você tem um aplicativo simples da web implantado em uma VM no Azure para usar ferramentas de software livre para visualizar seu tráfego de rede para identificar rapidamente os padrões de fluxo e anomalias possíveis. Com o observador de rede, você poderá obter uma captura de pacote de seu ambiente de rede e armazená-lo diretamente em sua conta de armazenamento. O CapAnalysis pode incluir a captura de pacote diretamente do armazenamento de blobs e visualizar seu conteúdo.

![cenário][1]

## <a name="steps"></a>Etapas

### <a name="install-capanalysis"></a>Instale o CapAnalysis

Para instalar o CapAnalysis em uma máquina virtual, confira as instruções oficiais neste local https://www.capanalysis.net/ca/how-to-install-capanalysis.
Para acessar o CapAnalysis remotamente, é preciso abrir a porta 9877 na sua VM, adicionando uma nova regra de segurança de entrada. Para obter mais informações sobre como criar regras em grupos de segurança de rede, veja [Como criar regras em uma NSG existente](../virtual-network/manage-network-security-group.md#create-a-security-rule). Depois de adicionar a regra com êxito, você pode acessar o CapAnalysis em `http://<PublicIP>:9877`

### <a name="use-azure-network-watcher-to-start-a-packet-capture-session"></a>Como usar o observador de rede do Azure para iniciar uma sessão de captura de pacote

O observador de rede permite que você capture pacotes para acompanhar o tráfego de entrada e saída de uma máquina virtual. Confira as instruções em: [Capturas de pacote de gerenciamento com o observador de rede](network-watcher-packet-capture-manage-portal.md) para iniciar uma sessão de captura de pacote. Uma captura de pacote pode ser armazenada em um armazenamento de blobs para ser acessada pelo CapAnalysis.

### <a name="upload-a-packet-capture-to-capanalysis"></a>Como carregar uma captura de pacote no CapAnalysis
Você pode carregar diretamente uma captura de pacote realizada pelo observador de rede usando a guia "Importação de URL" e fornecer um link para o armazenamento de blobs onde a captura de pacote é armazenada.

Ao fornecer um link para o CapAnalysis, não deixe de acrescentar um token SAS à URL do armazenamento de blobs.  Para fazer isso, navegue até a assinatura de acesso compartilhado da conta de armazenamento, designe as permissões permitidas e pressione o botão Gerar SAS para criar um token. Em seguida, você pode acrescentar esse token SAS à URL do armazenamento de blobs de captura de pacote.

A URL resultante será semelhantes à seguinte URL: http://storageaccount.blob.core.windows.net/container/location?addSASkeyhere


### <a name="analyzing-packet-captures"></a>Análise de capturas de pacote

O CapAnalysis oferece várias opções para visualizar sua captura de pacote, cada uma fornece análise de uma perspectiva diferente. Com esses resumos visuais, você pode entender as tendências do tráfego de rede e identificar rapidamente qualquer atividade incomum. Alguns desses recursos são mostrados na lista a seguir:

1. Tabelas de fluxo

    Esta tabela fornece a lista de fluxos de dados de pacote, o carimbo de hora associado com os fluxos, vários protocolos associados ao fluxo e o IP de origem e destino.

    ![página de fluxo do capanalysis][5]

1. Visão geral do protocolo

    Este painel permite que você veja rapidamente a distribuição do tráfego de rede em diferentes protocolos e regiões geográficas.

    ![visão geral do protocolo do capanalysis][6]

1. Estatísticas

    Este painel permite exibir estatísticas de tráfego de rede — bytes enviados e IPs de origem e destino, fluxos de cada IP de origem e destino, o protocolo usado para vários fluxos e a duração de fluxos.

    ![estatísticas do capanalysis][7]

1. Geomap

    Este painel fornece uma exibição de mapa de seu tráfego de rede, com cores de dimensionamento para o volume de tráfego de cada país. Você pode selecionar os países destacados para exibir estatísticas de fluxo adicionais, assim como a proporção dos dados enviados e recebidos pelos IPs nesse país.

    ![geomap][8]

1. Filtros

    O CapAnalysis fornece um conjunto de filtros para análise rápida dos pacotes específicos. Por exemplo, você pode optar por filtrar os dados por protocolo para obter informações específicas sobre esse subconjunto de tráfego.

    ![filtros][11]

    Visite [https://www.capanalysis.net/ca/#about](https://www.capanalysis.net/ca/#about) para saber mais sobre todos os recursos de CapAnalysis.

## <a name="conclusion"></a>Conclusão

O recurso de captura de pacote do observador de rede e permite que você capture os dados necessários para executar uma análise forense e compreender melhor o tráfego de rede. Nesse cenário, mostramos como as capturas de pacote do Observador de Rede podem ser facilmente integradas a ferramentas de visualização de software livre. Usando ferramentas de software livre, como o CapAnalysis, para visualizar a captura de pacotes, você pode realizar inspeções de pacotes minuciosas e identificar rapidamente as tendências do seu tráfego de rede.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre logs de fluxo NSG, acesse [Logs de fluxo NSG](network-watcher-nsg-flow-logging-overview.md)

Para saber como visualizar os logs de fluxo NSG com o Power BI, veja [Como visualizar logs de fluxos NSG com Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
<!--Image references-->

[1]: ./media/network-watcher-using-open-source-tools/figure1.png
[2]: ./media/network-watcher-using-open-source-tools/figure2.png
[3]: ./media/network-watcher-using-open-source-tools/figure3.png
[4]: ./media/network-watcher-using-open-source-tools/figure4.png
[5]: ./media/network-watcher-using-open-source-tools/figure5.png
[6]: ./media/network-watcher-using-open-source-tools/figure6.png
[7]: ./media/network-watcher-using-open-source-tools/figure7.png
[8]: ./media/network-watcher-using-open-source-tools/figure8.png
[9]: ./media/network-watcher-using-open-source-tools/figure9.png
[10]: ./media/network-watcher-using-open-source-tools/figure10.png
[11]: ./media/network-watcher-using-open-source-tools/figure11.png
