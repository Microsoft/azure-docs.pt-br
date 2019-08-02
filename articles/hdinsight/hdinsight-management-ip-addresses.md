---
title: Endereços IP de gerenciamento do Azure HDInsight
description: Saiba quais endereços IP você deve permitir o tráfego de entrada, para configurar corretamente os grupos de segurança de rede e as rotas definidas pelo usuário para a rede virtual com o Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/19/2019
ms.openlocfilehash: 0a41d1d57257db8f88481766e65eb8ee7569da87
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479200"
---
# <a name="hdinsight-management-ip-addresses"></a>Endereços IP de gerenciamento do HDInsight

Se você usar NSGs (grupos de segurança de rede) ou UDRs (rotas definidas pelo usuário) para controlar o tráfego de entrada para o cluster HDInsight, deverá garantir que o cluster possa se comunicar com os serviços críticos de integridade e gerenciamento do Azure.  Alguns dos endereços IP para esses serviços são específicos da região e alguns deles se aplicam a todas as regiões do Azure. Talvez você também precise permitir o tráfego do serviço DNS do Azure se não estiver usando o DNS personalizado.

As seções a seguir discutem os endereços IP específicos que devem ser permitidos.

## <a name="azure-dns-service"></a>Serviço DNS do Azure

Se você estiver usando o serviço DNS fornecido pelo Azure, permita o acesso de __168.63.129.16__ na porta 53. Para obter mais informações, consulte o documento [Resolução de nomes para VMs e instâncias de função](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md). Se você estiver usando o DNS personalizado, ignore esta etapa.

## <a name="health-and-management-services-all-regions"></a>Serviços de integridade e gerenciamento: Todas as regiões

Permitir o tráfego dos seguintes endereços IP para serviços de integridade e gerenciamento do Azure HDInsight que se aplicam a todas as regiões do Azure:

| Endereço IP de origem | Destino  | Direction |
| ---- | ----- | ----- |
| 168.61.49.99 | \*:443 | Entrada |
| 23.99.5.239 | \*:443 | Entrada |
| 168.61.48.131 | \*:443 | Entrada |
| 138.91.141.162 | \*:443 | Entrada |

## <a name="health-and-management-services-specific-regions"></a>Serviços de integridade e gerenciamento: Regiões específicas

Permita o tráfego dos endereços IP listados para os serviços de integridade e gerenciamento do Azure HDInsight na região específica do Azure onde os recursos estão localizados:

> [!IMPORTANT]  
> Se a região do Azure que você está usando não estiver listada, use apenas os quatro endereços IP da seção anterior.

| País | Região | Endereços IP de origem permitidos | Destino permitido | Direction |
| ---- | ---- | ---- | ---- | ----- |
| Ásia | Ásia Oriental | 23.102.235.122</br>52.175.38.134 | \*:443 | Entrada |
| &nbsp; | Sudeste da Ásia | 13.76.245.160</br>13.76.136.249 | \*:443 | Entrada |
| Austrália | Leste da Austrália | 104.210.84.115</br>13.75.152.195 | \*:443 | Entrada |
| &nbsp; | Sudeste da Austrália | 13.77.2.56</br>13.77.2.94 | \*:443 | Entrada |
| Brasil | Sul do Brasil | 191.235.84.104</br>191.235.87.113 | \*:443 | Entrada |
| Canadá | Leste do Canadá | 52.229.127.96</br>52.229.123.172 | \*:443 | Entrada |
| &nbsp; | Canadá Central | 52.228.37.66</br>52.228.45.222 |\*: 443 | Entrada |
| China | Norte da China | 42.159.96.170</br>139.217.2.219</br></br>42.159.198.178</br>42.159.234.157 | \*:443 | Entrada |
| &nbsp; | Leste da China | 42.159.198.178</br>42.159.234.157</br></br>42.159.96.170</br>139.217.2.219 | \*:443 | Entrada |
| &nbsp; | Norte da China 2 | 40.73.37.141</br>40.73.38.172 | \*:443 | Entrada |
| &nbsp; | Leste da China 2 | 139.217.227.106</br>139.217.228.187 | \*:443 | Entrada |
| Europa | Europa Setentrional | 52.164.210.96</br>13.74.153.132 | \*:443 | Entrada |
| &nbsp; | Europa Ocidental| 52.166.243.90</br>52.174.36.244 | \*:443 | Entrada |
| França | Centro da França| 20.188.39.64</br>40.89.157.135 | \*:443 | Entrada |
| Alemanha | Alemanha Central | 51.4.146.68</br>51.4.146.80 | \*:443 | Entrada |
| &nbsp; | Nordeste da Alemanha | 51.5.150.132</br>51.5.144.101 | \*:443 | Entrada |
| Índia | Índia Central | 52.172.153.209</br>52.172.152.49 | \*:443 | Entrada |
| &nbsp; | Sul da Índia | 104.211.223.67<br/>104.211.216.210 | \*:443 | Entrada |
| Japão | Leste do Japão | 13.78.125.90</br>13.78.89.60 | \*:443 | Entrada |
| &nbsp; | Oeste do Japão | 40.74.125.69</br>138.91.29.150 | \*:443 | Entrada |
| Coreia do Sul | Coreia Central | 52.231.39.142</br>52.231.36.209 | \*:443 | Entrada |
| &nbsp; | Sul da Coreia | 52.231.203.16</br>52.231.205.214 | \*:443 | Entrada
| Reino Unido | Oeste do Reino Unido | 51.141.13.110</br>51.141.7.20 | \*:443 | Entrada |
| &nbsp; | Sul do Reino Unido | 51.140.47.39</br>51.140.52.16 | \*:443 | Entrada |
| Estados Unidos | EUA Central | 13.89.171.122</br>13.89.171.124 | \*:443 | Entrada |
| &nbsp; | East US | 13.82.225.233</br>40.71.175.99 | \*:443 | Entrada |
| &nbsp; | Centro-Norte dos EUA | 157.56.8.38</br>157.55.213.99 | \*:443 | Entrada |
| &nbsp; | Centro-Oeste dos EUA | 52.161.23.15</br>52.161.10.167 | \*:443 | Entrada |
| &nbsp; | Oeste dos EUA | 13.64.254.98</br>23.101.196.19 | \*:443 | Entrada |
| &nbsp; | Oeste dos EUA 2 | 52.175.211.210</br>52.175.222.222 | \*:443 | Entrada |

Para obter informações sobre os endereços IP a serem usados para o Azure Governamental, consulte o documento [Inteligência + Análise do Azure Governamental](https://docs.microsoft.com/azure/azure-government/documentation-government-services-intelligenceandanalytics).

Para obter mais informações, consulte a seção [Controlando o tráfego de rede](hdinsight-plan-virtual-network-deployment.md#networktraffic).

Se você estiver usando UDRs (rotas definidas pelo usuário), deverá especificar uma rota e permitir o tráfego de saída da VNET para os IPs acima com o próximo salto definido como "Internet".

## <a name="next-steps"></a>Próximas etapas

* [Criar redes virtuais para clusters do Azure HDInsight](hdinsight-create-virtual-network.md)
