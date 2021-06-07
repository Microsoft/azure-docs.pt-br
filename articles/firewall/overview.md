---
title: O que é o Firewall do Azure?
description: Firewall do Azure é um serviço de segurança de rede gerenciado e baseado em nuvem que protege seus recursos de Rede Virtual do Azure.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc, contperf-fy21q1
ms.date: 04/05/2021
ms.author: victorh
ms.openlocfilehash: bb89b6acbc76a4020ee721e87272b154bab6d0a4
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385166"
---
# <a name="what-is-azure-firewall"></a>O que é o Firewall do Azure?

![Certificação ICSA](media/overview/icsa-cert-firewall-small.png)

Firewall do Azure é um serviço de segurança de rede gerenciado e baseado em nuvem que protege seus recursos de Rede Virtual do Azure. É um firewall como serviço totalmente com estado com alta disponibilidade interna e escalabilidade de nuvem irrestrita.

![Visão geral do firewall](media/overview/firewall-threat.png)

É possível criar, impor e registrar centralmente políticas de conectividade de rede e de aplicativo em assinaturas e redes virtuais. O Firewall do Azure usa um endereço IP público estático para seus recursos de rede virtual, permitindo que firewalls externos identifiquem o tráfego originário de sua rede virtual.  O serviço é totalmente integrado ao Azure Monitor para registro em log e análise.

Para saber mais sobre os recursos do Firewall do Azure, confira [Recursos do Firewall do Azure](features.md).

## <a name="azure-firewall-premium-preview"></a>Firewall do Azure Premium Versão Prévia

O Firewall do Azure Premium Versão Prévia é um firewall de última geração com funcionalidades necessárias para ambientes altamente sensíveis e regulamentados. Essas funcionalidades incluem a inspeção de TLS, IDPS, filtragem de URL e categorias da Web.

Para saber mais sobre os recursos do Firewall do Azure Premium Versão Prévia, confira [Firewall do Azure Premium Versão Prévia](premium-features.md).


Para ver como o Firewall Premium Versão Prévia está configurado no portal do Azure, confira [Firewall do Azure Premium Versão Prévia no portal do Azure](premium-portal.md).


## <a name="pricing-and-sla"></a>Preço e SLA

Para obter informações sobre o preço do Firewall do Azure, confira os [Preços do Firewall do Azure](https://azure.microsoft.com/pricing/details/azure-firewall/).

Para obter informações sobre o SLA do Firewall do Azure, confira o [SLA do Firewall do Azure](https://azure.microsoft.com/support/legal/sla/azure-firewall/).

## <a name="whats-new"></a>Novidades

Para saber sobre as novidades do Firewall do Azure, confira as [Atualizações do Azure](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Firewall).


## <a name="known-issues"></a>Problemas conhecidos

O Firewall do Azure tem os seguintes problemas conhecidos:

|Problema  |Descrição  |Atenuação  |
|---------|---------|---------|
|As regras de filtragem de rede para protocolos não TCP/UDP (por exemplo, ICMP) não funcionam para o tráfego vinculado à Internet|As regras de filtragem de rede para protocolos não TCP/UDP não funcionam com o SNAT para seu endereço IP público. Protocolos não TCP/UDP têm suporte entre VNets e sub-redes spoke.|O Firewall do Azure usa o Standard Load Balancer [que não dá suporte a SNAT para protocolos IP](../load-balancer/load-balancer-overview.md). Estamos explorando as opções para dar suporte a esse cenário em uma versão futura.|
|Suporte do PowerShell e da CLI ausente para ICMP|O Azure PowerShell e a CLI não dão suporte ao ICMP como um protocolo válido nas regras de rede.|Ainda é possível usar o ICMP como protocolo por meio do portal e da API REST. Estamos trabalhando para adicionar o ICMP no PowerShell e na CLI em breve.|
|As marcas de FQDN requerem que um protocolo:porta seja definido|As regras de aplicativo com marcas de FQDN exigem a definição de um protocolo:porta.|Você pode usar **HTTPS** como o valor de porta:protocolo. Estamos trabalhando para tornar esse campo opcional quando marcas de FQDN são usadas.|
|Não há suporte para a movimentação de um firewall para um grupo de recursos ou uma assinatura diferente|Não há suporte para a movimentação de um firewall para um grupo de recursos ou uma assinatura diferente.|O suporte a essa funcionalidade está em nosso roteiro. Para mover um firewall para um grupo de recursos ou uma assinatura diferente, você precisa excluir a instância atual e recriá-la no novo grupo de recursos ou na nova assinatura.|
|Alertas de inteligência de ameaças podem ser mascarados|As regras de rede com destino 80/443 para filtragem de saída mascaram os alertas de inteligência de ameaças quando configuradas para o modo somente alerta.|Crie a filtragem de saída para 80/443 usando regras de aplicativo. Ou, alterar o modo de inteligência contra ameaças para **Alertar e negar**.|
|O DNAT de Firewall do Azure não funciona para destinos de IP privados|O suporte para DNAT do Firewall do Azure é limitado à saída/entrada da Internet. No momento, o DNAT não funciona para destinos de IP privados. Por exemplo, spoke para spoke.|Esta é uma limitação atual.|
|Não é possível remover a primeira configuração de IP público|Cada endereço IP público do Firewall do Azure é atribuído a uma *configuração de IP*.  A primeira configuração de IP é atribuída durante a implantação do firewall e geralmente também contém uma referência à sub-rede do firewall (a menos que configurado de maneira explicitamente diferente por meio de uma implantação de modelo). Não é possível excluir essa configuração de IP, pois ele desalocaria o firewall. Você ainda poderá alterar ou remover o endereço IP público associado a essa configuração de IP se o firewall tiver pelo menos um outro endereço IP público disponível para uso.|Isso ocorre por design.|
|As Zonas de disponibilidade só podem ser configuradas durante a implantação.|As Zonas de disponibilidade só podem ser configuradas durante a implantação. Você não pode configurar Zonas de Disponibilidade após a implantação de um firewall.|Isso ocorre por design.|
|SNAT em conexões de entrada|Além de DNAT, as conexões via o endereço IP público do firewall (entrada) estão no modo SNAT para um dos IPs privados do firewall. Esse requisito hoje (e também para NVAs ativa/ativa) garante o roteamento simétrico.|Para preservar a fonte original para HTTP/S, use os cabeçalhos [XFF](https://en.wikipedia.org/wiki/X-Forwarded-For). Por exemplo, use um serviço como o [Azure Front Door](../frontdoor/front-door-http-headers-protocol.md#front-door-to-backend) ou o [Gateway de Aplicativo do Azure](../application-gateway/rewrite-http-headers.md) na frente do firewall. Você também pode adicionar o WAF como parte Porta da frente do Azure e encadear ao firewall.
|Suporte para filtragem de FQDN do SQL apenas no modo de proxy (porta 1433)|Para o Banco de Dados SQL do Azure, o Azure Synapse Analytics e a Instância Gerenciada de SQL do Azure:<br><br>A filtragem de FQDN do SQL tem suporte apenas no modo de proxy (porta 1433).<br><br>Para IaaS do SQL do Azure:<br><br>Se estiver usando portas não padrão, você poderá especificar essas portas nas regras do aplicativo.|Para o SQL no modo de redirecionamento (o padrão ao se conectar de dentro do Azure), você pode filtrar o acesso usando a tag de serviço do SQL como parte das regras de rede do Firewall do Azure.
|O tráfego de saída na porta TCP 25 não é permitido| As conexões SMTP de saída que usam a porta TCP 25 foram bloqueadas. A porta 25 é usada principalmente para entrega de email não autenticado. Esse é o comportamento de plataforma padrão para máquinas virtuais. Para saber mais, confira [Solucionar problemas de conectividade de SMTP de saída no Azure](../virtual-network/troubleshoot-outbound-smtp-connectivity.md). No entanto, ao contrário de máquinas virtuais, no momento, não é possível habilitar essa funcionalidade no Firewall do Azure. Observação: para permitir o SMTP autenticado (porta 587) ou o SMTP por uma porta diferente de 25, configure uma regra de rede e não uma regra de aplicativo, porque não há suporte para a inspeção de SMTP no momento.|Siga o método recomendado para enviar email conforme documentado no artigo de solução de problemas de SMTP. Ou exclua a máquina virtual que precisa de acesso SMTP de saída da rota padrão para o firewall. Em vez disso, configure o acesso de saída diretamente para a Internet.
|A métrica de utilização da porta SNAT mostra 0%|A métrica de utilização da porta SNAT do Firewall do Azure pode mostrar o uso de 0% mesmo quando as portas SNAT são usadas. Nesse caso, o uso da métrica como parte da métrica de integridade do firewall fornece um resultado incorreto.|Esse problema foi corrigido e a distribuição para produção é destinada a maio de 2020. Em alguns casos, a reimplantação do firewall resolve o problema, mas não é consistente. Como uma solução alternativa intermediária, use apenas o estado de integridade do firewall para procurar *status = degradado*, não para *status = não íntegro*. O esgotamento de porta será exibido como *degradado*. *Não íntegro* é reservado para uso futuro quando mais métricas afetam a integridade do firewall.
|O DNAT não é compatível com o Túnel Forçado habilitado|Os firewalls implantados com o Túnel Forçado habilitado não são compatíveis com acesso de entrada proveniente da Internet devido ao roteamento assimétrico.|Isso ocorre por design devido ao roteamento assimétrico. O caminho de retorno para conexões de entrada passa pelo firewall local, que não viu a conexão estabelecida.
|O FTP Passivo de Saída pode não funcionar com os firewalls de vários endereços IP públicos, dependendo da configuração do servidor FTP.|O FTP Passivo estabelece conexões diferentes para canais de controle e de dados. Quando um Firewall com vários endereços IP públicos envia dados de saída, ele seleciona aleatoriamente um de seus endereços IP públicos para o endereço IP de origem. O FTP pode falhar quando os canais de controle e de dados usam endereços IP de origem diferentes, dependendo da configuração do servidor FTP.|Uma configuração SNAT explícita está em planejamento. Enquanto isso, você pode configurar o servidor FTP para aceitar canais de controle e de dados de diferentes endereços IP de origem (confira [um exemplo do IIS](/iis/configuration/system.applicationhost/sites/sitedefaults/ftpserver/security/datachannelsecurity)). Como alternativa, considere usar um só endereço IP nessa situação.|
|O FTP Passivo de Entrada pode não funcionar, dependendo da configuração do servidor FTP |O FTP Passivo estabelece conexões diferentes para canais de controle e de dados. As conexões de entrada no Firewall do Azure estão no modo SNAT para um dos endereços IP privados do firewall a fim de garantir o roteamento simétrico. O FTP pode falhar quando os canais de controle e de dados usam endereços IP de origem diferentes, dependendo da configuração do servidor FTP.|A preservação do endereço IP de origem original está sendo investigada. Enquanto isso, você pode configurar o servidor FTP para aceitar canais de controle e de dados de diferentes endereços IP de origem.|
|A métrica NetworkRuleHit não tem uma dimensão de protocolo|A métrica ApplicationRuleHit permite o protocolo baseado em filtragem, mas essa funcionalidade está ausente na métrica NetworkRuleHit correspondente.|Uma correção está sendo investigada.|
|Não há suporte para regras NAT com portas entre 64000 e 65535|O Firewall do Azure permite qualquer porta no intervalo de 1 a 65535 nas regras de rede e de aplicativo, no entanto, as regras de NAT dão suporte apenas a portas no intervalo de 1 a 63999.|Esta é uma limitação atual.
|As atualizações de configuração podem levar cinco minutos em média|Uma atualização de configuração do Firewall do Azure pode levar de três a cinco minutos em média e não há suporte para atualizações paralelas.|Uma correção está sendo investigada.|
|O Firewall do Azure usa cabeçalhos de TLS SNI para filtrar tráfego HTTPS e MSSQL|Se o navegador ou o software para servidores não der suporte à extensão SNI (Indicação de Nome do Servidor), você não poderá se conectar por meio do Firewall do Azure.|Se o navegador ou o software para servidores não der suporte à SNI, você poderá controlar a conexão usando uma regra de rede em vez de uma regra de aplicativo. Consulte [Indicação de Nome de Servidor](https://wikipedia.org/wiki/Server_Name_Indication) para conhecer software que seja compatível com a SNI.|
|O DNS personalizado não funciona com túnel forçado|Se o túnel forçado estiver habilitado, o DNS personalizado não funcionará.|Uma correção está sendo investigada.|
|Iniciar/Parar não funciona com um firewall configurado no modo de túnel forçado|Iniciar/Parar não funciona com um firewall do Azure configurado no modo de túnel forçado. A tentativa de iniciar o Firewall do Azure com o túnel forçado configurado resulta no seguinte erro:<br><br>*Set-AzFirewall: a configuração de IP de gerenciamento do AzureFirewall FW-xx não pode ser adicionada a um firewall existente. Reimplante com uma configuração de IP de gerenciamento se desejar usar o suporte de túnel forçado.<br>StatusCode: 400<br>ReasonPhrase: Bad Request*|Sob Investigação.<br><br>Como alternativa, você pode excluir o firewall existente e criar outro com os mesmos parâmetros.|
|Não é possível adicionar marcações de política de firewall usando o portal|A Política de Firewall do Azure tem uma limitação de suporte de patch que impede a adição de marcação usando o portal do Azure. O seguinte erro é gerado: *Não foi possível salvar as marcações para o recurso*.|Uma correção está sendo investigada. Como alternativa, você pode usar o cmdlet `Set-AzFirewallPolicy` do Azure PowerShell para atualizar as marcas.|
|IPv6 ainda não compatível|Se você adicionar um endereço IPv6 a uma regra, o firewall falhará.|Use somente endereços IPv4. O suporte a IPv6 está em investigação.|
|A atualização de vários grupos de IP falha com um erro de conflito.|Quando você atualiza dois ou mais IPGroups anexados ao mesmo firewall, um dos recursos entra em um estado de falha.|Esse é um problema conhecido e uma limitação conhecida. <br><br>Quando você atualiza um IPGroup, ele dispara uma atualização em todos os firewalls aos quais o IPGroup está anexado. Se uma atualização em um segundo IPGroup for iniciada enquanto o firewall ainda estiver no estado *Atualizando*, a atualização do IPGroup falhará.<br><br>Para evitar a falha, os IPGroups anexados ao mesmo firewall precisam ser atualizados um de cada vez. Permita tempo suficiente entre as atualizações para que o firewall saia do estado *Atualizando*.| 


## <a name="next-steps"></a>Próximas etapas

- [Início rápido: Criar um Firewall do Azure e uma política de firewall – modelo do ARM](../firewall-manager/quick-firewall-policy.md)
- [Início Rápido: Implantar o Firewall do Azure com as Zonas de Disponibilidade – Modelo do ARM](deploy-template.md)
- [Tutorial: Implantar e configurar o Firewall do Azure usando o portal do Azure](tutorial-firewall-deploy-portal.md)