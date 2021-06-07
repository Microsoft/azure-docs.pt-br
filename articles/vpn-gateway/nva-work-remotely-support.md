---
title: 'Trabalhando remotamente: considerações sobre NVA (solução de virtualização de rede) para trabalho remoto | Gateway de VPN do Azure'
description: Este artigo ajuda você a entender as coisas que você deve levar em consideração para trabalhar com NVAs (soluções de virtualização de rede) no Azure durante o COVID-19 pandemia.
services: vpn-gateway
author: scottnap
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: scottnap
ms.openlocfilehash: 70b5732e1293e35127a19fbe736d8562056a870b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96499672"
---
# <a name="working-remotely-network-virtual-appliance-nva-considerations-for-remote-work"></a>Trabalhando remotamente: considerações sobre NVA (solução de virtualização de rede) para trabalho remoto

>[!NOTE]
>Este artigo descreve como você pode aproveitar as soluções de virtualização de rede, o Azure, a rede da Microsoft e o ecossistema de parceiros do Azure para trabalhar remotamente e atenuar os problemas de rede que você está enfrentando devido à crise COVID-19.
>

Alguns clientes do Azure utilizam soluções de virtualização de rede de terceiros (NVAs) do Azure Marketplace para fornecer serviços críticos, como VPN ponto a site para seus funcionários que trabalham em casa durante o COVID-19 epidemia. Este artigo descreve algumas diretrizes de alto nível a serem levadas em consideração ao aproveitar o NVAs no Azure para fornecer soluções de acesso remoto.

## <a name="nva-performance-considerations"></a>Considerações de desempenho do NVA

Todos os principais fornecedores de NVA no Azure Marketplace devem ter recomendações sobre o tamanho da VM e o número de instâncias a serem usadas ao implantar suas soluções.  Embora quase todos os fornecedores de NVA permitam que você escolha qualquer tamanho que esteja disponível para você em uma determinada região, é muito importante que você siga as recomendações de fornecedores para tamanhos de instância de VM do Azure, pois essas recomendações são os tamanhos de VM para os quais o fornecedor fez testes de desempenho no Azure.  

### <a name="consider-the-following"></a>Considere o seguinte

- **Capacidade e número de usuários simultâneos** – esse número é particularmente importante para usuários VPN ponto a site, uma vez que cada usuário conectado criará um túnel criptografado (IPSec ou VPN SSL).  
- **Taxa de transferência agregada** – Qual é a largura de banda agregada que você precisará para acomodar o número de usuários de que precisa para fornecer acesso remoto.
- **O tamanho da VM que você precisará** -você sempre deve usar os tamanhos de VM recomendados pelo fornecedor NVA.  Para VPN ponto a site, se você tiver muitas conexões de usuário simultâneas, deverá usar tamanhos de VM maiores, como VMs da [série Dv2 e DSv2](../virtual-machines/dv2-dsv2-series.md "Série Dv2 e Dsv2") . Essas VMs tendem a ter mais vCPUs e podem lidar com mais sessões de VPN simultâneas.  Além de ter mais núcleos virtuais, tamanhos maiores de VM no Azure têm mais capacidade de largura de banda agregada do que tamanhos menores de VM.
    > **Importante:** Cada fornecedor utiliza recursos de forma diferente.  Se não estiver claro quais tamanhos de instância você deve usar para acomodar a carga de usuário estimada, entre em contato diretamente com o fornecedor do software e solicite a eles uma recomendação.
- **Número de instâncias** – se você espera ter um grande número de usuários e conexões, há limites para o que escalar verticalmente os tamanhos de instância do NVA podem atingir.  Considere implantar várias instâncias de VM.
- VPN **IPSec vs SSL** de IP-em implementações gerais de VPN IPSec, execute melhor do que as implementações de VPN SSL.  
- **Licenciamento** -Verifique se as licenças de software que você comprou para a solução NVA abordarão o crescimento repentino que você pode enfrentar durante o epidemia de COVID-19.  Muitos programas de licenciamento NVA limitam o número de conexões ou largura de banda que a solução é capaz de.
- **Rede acelerada** -considere uma solução NVA que tem suporte para rede acelerada.  Rede acelerada permite SR-IOV (virtualização de E/S de raiz única) para uma VM, melhorando muito seu desempenho de rede. Esse caminho de alto desempenho ignora o host do caminho de dados, reduzindo a latência, a tremulação e a utilização da CPU para uso com as cargas de trabalho de rede mais exigentes em tipos de VM com suporte. A rede acelerada tem suporte na maioria dos tamanhos de instância de uso geral e otimizado para computação com dois ou mais vCPUs.

## <a name="monitoring-resources"></a>Monitorando recursos

Cada solução NVA tem suas próprias ferramentas e recursos para monitorar o desempenho de seus NVA.  Consulte a documentação de seus fornecedores para saber se você entendeu as limitações de desempenho e pode detectar quando sua NVA está perto ou atingindo a capacidade.  Além disso, você pode examinar Azure Monitor insights de rede e ver informações básicas de desempenho sobre suas soluções de virtualização de rede, como:

- Utilização da CPU
- Entrada na rede
- Saída da rede
- Fluxos de Entrada
- Fluxos de Saída

## <a name="next-steps"></a>Próximas etapas

A maioria dos principais parceiros de NVA publicou orientação sobre o dimensionamento para um crescimento repentino e inesperado durante o COVID-19. Aqui estão alguns links úteis para os recursos do parceiro.

[O Barracuda permite o trabalho de casa enquanto protege seus dados durante o COVID-19](https://www.barracuda.com/covid-19/work-from-home "Habilite o trabalho de casa ao proteger seus dados durante o COVID-19")

[Ponto de obra remoto seguro do Check Point durante coronavirus](https://www.checkpoint.com/solutions/secure-remote-workforce-during-coronavirus/ "Proteger força de equipe remota durante coronavirus")

[Implementação do Cisco AnyConnect e referência de desempenho/dimensionamento para preparação do COVID-19](https://www.cisco.com/c/en/us/support/docs/security/anyconnect-secure-mobility-client/215331-anyconnect-implementation-and-performanc.html "Implementação do Cisco AnyConnect e referência de desempenho/dimensionamento para preparação do COVID-19")

[Centro de suporte de resposta Citrix COVID-19](https://www.citrix.com/support/covid-19-coronavirus.html "Centro de suporte de resposta Citrix COVID-19")

[Diretrizes F5 para abordar o aumento drástico em funcionários remotos](https://www.f5.com/business-continuity "Diretrizes F5 para abordar o aumento drástico em funcionários remotos")

[Fortinet COVID-19 atualizações para clientes e parceiros](https://www.fortinet.com/covid-19.html "Atualizações do COVID-19 para clientes e parceiros")

[Centro de respostas do Palo Alto Networks COVID-19](https://live.paloaltonetworks.com/t5/COVID-19-Response-Center/ct-p/COVID-19_Response_Center "Centro de respostas do Palo Alto Networks COVID-19")

[Kemp habilitar o trabalho remoto e a experiência do aplicativo de Always-On para continuidade dos negócios](https://kemptechnologies.com/remote-work-always-on-application-experience-business-continuity/ "Kemp habilitar o trabalho remoto e a experiência do aplicativo de Always-On para continuidade dos negócios")

