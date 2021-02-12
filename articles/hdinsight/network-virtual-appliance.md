---
title: Configurar solução de virtualização de rede no Azure HDInsight
description: Saiba como configurar vários recursos adicionais para sua solução de virtualização de rede no Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 7fe266c3c7b75762133fca4645e0675845c28972
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943968"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Configurar solução de virtualização de rede no Azure HDInsight

> [!Important]
> As informações a seguir **só** serão necessárias se você quiser configurar uma solução de virtualização de rede (NVA) diferente do [Firewall do Azure](./hdinsight-restrict-outbound-traffic.md).

A marca de FQDN do firewall do Azure é configurada automaticamente para permitir o tráfego para muitos dos FQDNs importantes comuns. Usar outra solução de virtualização de rede exigirá que você configure vários recursos adicionais. Lembre-se dos seguintes fatores ao configurar sua solução de virtualização de rede:

* Os serviços com capacidade de ponto de extremidade de serviço podem ser configurados com pontos de extremidade de serviço que resultam em ignorar o NVA, geralmente para considerações de custo ou desempenho.
* Se ResourceProviderConnection for definido como *saída*, você poderá usar pontos de extremidade privados para o armazenamento e os servidores SQL para metastores e não haverá necessidade de adicioná-los ao NVA.
* As dependências de endereço IP são para tráfego não HTTP/S (tráfego TCP e UDP).
* Os pontos de extremidade HTTP/HTTPS do FQDN podem ser aprovados em seu dispositivo NVA.
* Atribua a tabela de rotas que você cria à sub-rede do HDInsight.

## <a name="service-endpoint-capable-dependencies"></a>Dependências compatíveis com ponto de extremidade de serviço

Opcionalmente, você pode habilitar um ou mais dos seguintes pontos de extremidade de serviço, o que resultará em ignorar o NVA. Essa opção pode ser útil para grandes quantidades de transferências de dados para economizar em custos e também para otimizações de desempenho. 

| **Ponto de extremidade** |
|---|
| SQL do Azure |
| Armazenamento do Azure |
| Azure Active Directory |

### <a name="ip-address-dependencies"></a>Dependências de endereço IP

| **Ponto de extremidade** | **Detalhes** |
|---|---|
| IPs publicados [aqui](hdinsight-management-ip-addresses.md) | Esses IPs são para o provedor de recursos do HDInsight e devem ser incluídos no UDR para evitar o roteamento assimétrico. Essa regra só será necessária se o ResourceProviderConnection estiver definido como *entrada*. Se o ResourceProviderConnection for definido como *saída* , esses IPS não serão necessários no UDR.  |
| AAD – IPs privados DS | Necessário somente para clusters ESP, se os VNETs não estiverem emparelhados.|


### <a name="fqdn-httphttps-dependencies"></a>Dependências de HTTP/HTTPS do FQDN

Você pode obter a lista de dependências de FQDNs (principalmente o armazenamento do Azure e o barramento de serviço do Azure) para configurar seu NVA [neste repositório](https://github.com/Azure-Samples/hdinsight-fqdn-lists/). Para a lista regional, veja [aqui](https://github.com/Azure-Samples/hdinsight-fqdn-lists/tree/master/Regional). Essas dependências são usadas pelo provedor de recursos do HDInsight (RP) para criar e monitorar e gerenciar clusters com êxito. Isso inclui logs de telemetria/diagnóstico, metadados de provisionamento, configurações relacionadas a cluster, scripts, etc. Essa lista de dependências FQDN pode mudar com a liberação de atualizações futuras do HDInsight.

A lista a seguir fornece apenas alguns FQDNs que podem ser necessários para o sistema operacional e aplicação de patches de segurança ou validações de certificado *depois* que o cluster é criado e durante o tempo de vida das operações de cluster:

| **Dependências de tempo de execução FQDNs**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| microsoft.com/pki/mscorp/cps/default.htm:443                                      |
| microsoft.com:80                                                      |
|login.windows.net:443                                                  |
|login.microsoftonline.com:443                                          |

## <a name="next-steps"></a>Próximas etapas

* [Usar o firewall para restringir o tráfego de saída](./hdinsight-restrict-outbound-traffic.md)
* [Arquitetura de rede virtual do Azure HDInsight](hdinsight-virtual-network-architecture.md)
