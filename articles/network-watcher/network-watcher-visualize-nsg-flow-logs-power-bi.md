---
title: "Visualização de logs de fluxo do grupo de segurança de rede do Azure com o Power BI | Microsoft Docs"
description: "Esta página descreve como visualizar logs de fluxo NSG com o Power BI."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 1e4f95fa-f5f0-4e03-bc25-008fbfc4934c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: f32c8cd6e32e99bc34d62435bcd1883ba957e716
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="visualizing-network-security-group-flow-logs-with-power-bi"></a>Visualização de logs de fluxo do grupo de segurança de rede com o Power BI

Os logs de fluxo do grupo de segurança de rede permitem que você exiba informações sobre o tráfego IP de entrada e saída em grupos de segurança de rede. Esses logs de fluxo exibem os fluxos de entrada e saída baseados por regras. A NIC de fluxo se aplica às informações de 5 tuplas sobre o fluxo (IP de origem/destino, porta de origem/destino e protocolo) e se o tráfego foi permitido ou negado.

Não é fácil aprofundar-se sobre dados de registro em log de fluxo pesquisando manualmente os arquivos de log. Neste artigo, fornecemos uma solução para visualizar os logs de fluxo mais recentes e as informações sobre o tráfego na sua rede.

## <a name="scenario"></a>Cenário

No cenário a seguir, conectamos a área de trabalho do Power BI com a conta de armazenamento que havíamos configurado como o coletor para nossos dados de registro em log de fluxo de NSG. Depois de nos conectarmos com nossa conta de armazenamento, o Power BI baixa e analisa os logs para fornecer uma representação visual do tráfego registrado por grupos de segurança de rede.

Ao usar os visuais fornecidos no modelo, você pode examinar:

* Os principais locutores
* Dados de fluxo de série temporal por decisão de regra e direção
* Fluxos de endereço MAC da interface de rede
* Fluxos de NSG e regra
* Fluxos de porta de destino

O modelo fornecido é editável para que possa modificá-lo e adicionar elementos visuais ou dados novos ou editar consultas para atender às suas necessidades.

## <a name="setup"></a>Configuração

Antes de começar, você deve habilitar o registro em log de fluxo do grupo de segurança de rede em um ou mais grupos de segurança de rede em sua conta. Confira o artigo: [Introdução ao registro em log de fluxo para grupos de segurança de rede](network-watcher-nsg-flow-logging-overview.md) para obter instruções sobre como habilitar os logs de fluxo da segurança de rede.

Você também deve ter o cliente de desktop do Power BI instalado no seu computador e espaço livre suficiente para baixar e carregar os dados de log que existem em sua conta de armazenamento.

![Diagrama do Visio][1]

### <a name="steps"></a>Etapas

1. Baixe e abra o seguinte modelo do Power BI no aplicativo de área de trabalho do Power BI: [modelo de logs de fluxo do Power BI do observador de rede](https://aka.ms/networkwatcherpowerbiflowlogstemplate)
1. Insira os parâmetros de consulta necessários
    1. **StorageAccountName** – Especifica o nome da conta de armazenamento que contém os logs de fluxo NSG que você gostaria de carregar e visualizar.
    1. **NumberOfLogFiles** – Especifica o número de arquivos de log que você gostaria de baixar e visualizar no Power BI. Por exemplo: se especificar 50, serão baixados e disponibilizados para visualização os 50 arquivos de log mais recentes. Olhando à frente, temos 2 NSGs habilitados e configurados para enviar logs de fluxo NSG para esta conta, desta forma, as 25 horas de logs podem ser visualizadas.

    ![principal do Power BI][2]

1. Insira a chave de acesso da sua conta de armazenamento. Para encontrar as chaves de acesso válidas, acesse a sua conta de armazenamento no portal do Azure e selecione: **Chaves de Acesso** no menu Configurações. Clique em **Conectar** e, em seguida, aplique as alterações.

    ![teclas de acesso][3]

    ![chave de acesso 2][4]

4.  Depois de baixar e analisar os logs, você pode utilizar os visuais criados previamente.

## <a name="understanding-the-visuals"></a>Noções básicas sobre os elementos visuais

No modelo, é fornecido um conjunto de visuais que ajuda no entendimento dos dados do Log de Fluxo do NSG. As imagens a seguir mostram um exemplo da aparência de um painel populado com dados. Confira a seguir as informações detalhadas sobre cada visual 

![powerbi][5]
 
O visual dos principais locutores mostra os IPs que iniciaram a maioria das conexões durante o período especificado. O tamanho das caixas corresponde ao número relativo de conexões. 

![toptalkers][6]

Os gráficos de série de tempo a seguir mostram o número de fluxos durante o período. O gráfico superior é segmentado pelo sentido do fluxo, enquanto o inferior é segmentado pela decisão tomada (permitir ou negar). Com esse visual, é possível examinar as tendências do tráfego ao longo do tempo e identificar picos anormais ou quedas no tráfego ou na segmentação de tráfego.

![flowsoverperiod][7]

Os gráficos a seguir mostram os fluxos por interface de rede, com a parte superior segmentada por sentido do fluxo, e a inferior segmentada por decisão tomada. Com essas informações, você saberá qual das suas VMs comunicou-se mais em relação às outras, e se o tráfego para uma VM específica está sendo permitido ou negado.

![flowspernic][8]

O gráfico de rosca a seguir mostra uma divisão de fluxos de porta de destino. Com essas informações, você pode exibir as portas de destino mais usadas dentro do período especificado.

![donut][9]

O gráfico de barras a seguir mostra o fluxo de NSG e a regra. Com essas informações, você pode conferir os NSGs responsáveis pela maioria do tráfego e a análise de tráfego em um NSG por regra.

![barchart][10]
 
Os gráficos informativos a seguir exibem informações sobre os NSGs presentes nos logs, o número de fluxos capturados ao longo do período e os dados do log capturado mais antigo. Com essas informações, você terá uma ideia sobre quais NSGs estão sendo registrados e qual é a extensão de dados dos fluxos.

![infochart1][11]

![infochart2][12]

As segmentações exibidas no modelo a seguir, permitem que você visualize somente os dados que quiser. Você pode filtrar seus grupos de recursos, NSGs e regras. Além disso, você também pode filtrar as informações de 5 tuplas, a decisão e o momento em que o log foi gravado.

![slicers][13]

## <a name="conclusion"></a>Conclusão

Mostramos neste cenário que somos capazes de visualizar e compreender o tráfego se usarmos os logs de fluxo do grupo de segurança de rede fornecidos pelo Observador de rede e o Power BI. Usando o modelo fornecido, o Power BI baixa os logs diretamente do armazenamento e os processa localmente. O tempo necessário para carregar o modelo varia de acordo com o número de arquivos solicitados e o tamanho total dos arquivos baixados.

Fique à vontade para personalizar esse modelo para adequá-lo às suas necessidades. Há várias maneiras de usar o Power BI com logs de fluxo do grupo de segurança de rede. 

## <a name="notes"></a>Observações

* Os logs, por padrão, são armazenados em `https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/`

    * Se houver outros dados em outro diretório, as consultas para executar o pull e os processos de dados deverão ser modificados.

* Não é recomendado usar o modelo fornecido quando há mais de 1 GB de logs.

* Se você tiver uma quantidade grande de logs, recomendamos que estude uma solução que use outro tipo de armazenamento de dados, como o Data Lake ou o SQL server.

## <a name="next-steps"></a>Próximas etapas

Para saber como visualizar os logs de fluxo NSG com a pilha elástica, confira: [Como visualizar padrões de tráfego de rede de e para suas VMs usando ferramentas de software livre](network-watcher-using-open-source-tools.md)

[1]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure7.png
[8]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure8.png
[9]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure9.png
[10]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure10.png
[11]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure11.png
[12]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure12.png
[13]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure13.png
