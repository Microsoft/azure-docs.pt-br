---
title: Proteger o tráfego destinado a pontos de extremidade privados na WAN Virtual do Azure
description: Saiba como usar regras de rede e de aplicativo para proteger o tráfego destinado a pontos de extremidade privados na WAN Virtual do Azure
services: firewall-manager
author: jocortems
ms.service: firewall-manager
ms.topic: how-to
ms.date: 04/02/2021
ms.author: jocorte
ms.openlocfilehash: a1237077b9e0a1af67d34c85d8ea8d526bd71372
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281139"
---
# <a name="secure-traffic-destined-to-private-endpoints-in-azure-virtual-wan"></a>Proteger o tráfego destinado a pontos de extremidade privados na WAN Virtual do Azure

O [ponto de extremidade privado do Azure](../private-link/private-endpoint-overview.md) é o bloco de construção básico do [Link Privado do Azure](../private-link/private-link-overview.md). Pontos de extremidade privados permitem que recursos do Azure implantados em uma rede virtual se comuniquem de maneira privada com recursos de link privado.

Pontos de extremidade privados permitem que recursos acessem o serviço de link privado implantado em uma rede virtual. O acesso ao ponto de extremidade privado por meio do emparelhamento de rede virtual e as conexões de rede locais estendem a conectividade.

Talvez seja necessário filtrar o tráfego de clientes locais ou no Azure destinado a serviços expostos por meio de pontos de extremidade privados em uma rede virtual conectada da WAN virtual. Este artigo descreve essa tarefa usando o [hub virtual seguro](../firewall-manager/secured-virtual-hub.md) com o [Firewall do Azure](../firewall/overview.md) como o provedor de segurança.

O Firewall do Azure filtra o tráfego usando qualquer um dos seguintes métodos:

* [FQDN em regras de rede](../firewall/fqdn-filtering-network-rules.md) para os protocolos TCP e UDP
* [FQDN em regras de aplicativo](../firewall/features.md#application-fqdn-filtering-rules) para HTTP, HTTPS e MSSQL.
* Endereços IP de origem e de destino, porta e protocolo usando [regras de rede](../firewall/features.md#network-traffic-filtering-rules)

Use regras de aplicativo com regras de rede para inspecionar o tráfego destinado a pontos de extremidade privados.
Um hub virtual seguro é gerenciado pela Microsoft e não pode ser vinculado a uma [zona de DNS privado](../dns/private-dns-privatednszone.md). Isso é necessário para resolver o FQDN de um [recurso de link privado](../private-link/private-endpoint-overview.md#private-link-resource) para o endereço IP do ponto de extremidade privado correspondente.

A filtragem de FQDN do SQL tem suporte apenas no [modo de proxy](../azure-sql/database/connectivity-architecture.md#connection-policy) (porta 1433). O modo de *proxy* pode gerar mais latência em comparação ao *redirecionamento*. Se quiser continuar usando o modo de redirecionamento, que é o padrão para clientes que se conectam no Azure, filtre o acesso usando o FQDN nas regras de rede do firewall.

## <a name="filter-traffic-using-fqdn-in-network-and-application-rules"></a>Filtrar o tráfego usando o FQDN nas regras de rede e de aplicativo

As seguintes etapas permitem que o Firewall do Azure filtre o tráfego usando o FQDN em regras de rede e de aplicativo:

1. Implante uma máquina virtual do [encaminhador de DNS](../private-link/private-endpoint-dns.md#virtual-network-and-on-premises-workloads-using-a-dns-forwarder) em uma rede virtual conectada ao hub virtual seguro e vinculada às zonas de DNS privado que hospedam os tipos de registro A dos pontos de extremidade privados.

2. Defina [configurações personalizadas de DNS](../firewall/dns-settings.md#configure-custom-dns-servers---azure-portal) para apontar para o endereço IP da máquina virtual do encaminhador de DNS e habilite o proxy DNS na política de firewall associada ao Firewall do Azure implantado no hub virtual seguro.

3. Configure [servidores DNS personalizados](../virtual-network/manage-virtual-network.md#change-dns-servers) para as redes virtuais conectadas ao hub virtual seguro para apontar para o endereço IP privado associado ao Firewall do Azure implantado no hub virtual seguro.

4. Configure os servidores DNS locais para encaminhar as consultas DNS das zonas DNS públicas dos pontos de extremidade privados para o endereço IP privado associado ao Firewall do Azure implantado no hub virtual seguro.

5. Configure uma [regra de aplicativo](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule) ou uma [regra de rede](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule), conforme necessário, na política de firewall associada ao Firewall do Azure implantado no hub virtual seguro com o *Tipo de destino* FQDN e o FQDN público do recurso de link privado como *Destino*.

6. Navegue até os *hubs virtuais seguros* na política de firewall associada ao Firewall do Azure implantado no hub virtual seguro e selecione o hub virtual seguro em que a filtragem de tráfego destinado a pontos de extremidade privados será configurada.

7. Navegue até **Configuração de segurança**, selecione **Enviar por meio do Firewall do Azure** em **Tráfego privado**.

8. Selecione **Prefixos de tráfego privado** para editar os prefixos CIDR que serão inspecionados por meio do Firewall do Azure no hub virtual seguro e adicione um prefixo /32 para cada ponto de extremidade privado da seguinte maneira:

   > [!IMPORTANT]
   > Se esses prefixos /32 não estiverem configurados, o tráfego destinado aos pontos de extremidade privados ignorará o Firewall do Azure.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-manager-security-configuration.png" alt-text="Configuração de segurança do Gerenciador de Firewall" border="true":::

Essas etapas funcionam somente quando os clientes e os pontos de extremidade privados são implantados em redes virtuais diferentes conectadas ao mesmo hub virtual seguro e para clientes locais. Se os clientes e os pontos de extremidade privados estiverem implantados na mesma rede virtual, um UDR com rotas /32 para os pontos de extremidade privados deverá ser criado. Configure essas rotas com o **Tipo do próximo salto**  definido como **Solução de virtualização** e **Endereço do próximo salto** definido como o endereço IP privado do Firewall do Azure implantado no hub virtual seguro. **Propagar rotas de gateway** deve estar definido como **Sim**.

O diagrama a seguir ilustra os fluxos de tráfego de dados e do DNS para que os diferentes clientes se conectem a um ponto de extremidade privado implantado na WAN virtual do Azure:

:::image type="content" source="./media/private-link-inspection-secure-virtual-hub/private-link-inspection-virtual-wan-architecture.png" alt-text="Fluxos de tráfego" border="true":::

## <a name="troubleshooting"></a>Solução de problemas

Os principais problemas que você pode encontrar ao tentar filtrar o tráfego destinado a pontos de extremidade privados por meio do hub virtual seguro são:

- Os clientes não conseguem se conectar a pontos de extremidade privados.

- O Firewall do Azure é ignorado. Esse sintoma pode ser validado pela ausência de entradas de log das regras de aplicativo ou de rede no Firewall do Azure.

Na maioria dos casos, esses problemas são causados por um dos seguintes problemas:

- Resolução de nomes DNS incorreta

- Configuração de roteamento incorreta

### <a name="incorrect-dns-name-resolution"></a>Resolução de nomes DNS incorreta

1. Verifique se os servidores DNS da rede virtual estão definidos como *Personalizados* e se o endereço IP é o endereço IP privado do Firewall do Azure no hub virtual seguro.

   CLI do Azure:

   ```azurecli-interactive
   az network vnet show --name <VNET Name> --resource-group <Resource Group Name> --query "dhcpOptions.dnsServers"
   ```
2. Verifique se os clientes na mesma rede virtual que a máquina virtual do encaminhador de DNS podem resolver o FQDN público do ponto de extremidade privado para o endereço IP privado correspondente consultando diretamente a máquina virtual configurada como encaminhador de DNS.

   Linux:

   ```bash
   dig @<DNS forwarder VM IP address> <Private endpoint public FQDN>
   ```
3. Inspecione as entradas do log *AzureFirewallDNSProxy* do Firewall do Azure e valide que elas podem receber e resolver consultas DNS dos clientes.

   ```kusto
   AzureDiagnostics
   | where Category contains "DNS"
   | where msg_s contains "database.windows.net"
   ```
4. Verifique se o *proxy DNS* foi habilitado e se um servidor DNS *Personalizado* apontando para o endereço IP da máquina virtual do encaminhador de DNS foi configurado na política de firewall associada ao Firewall do Azure no hub virtual seguro.

   CLI do Azure:

   ```azurecli-interactive
   az network firewall policy show --name <Firewall Policy> --resource-group <Resource Group Name> --query dnsSettings
   ```

### <a name="incorrect-routing-configuration"></a>Configuração de roteamento incorreta

1. Verifique a *Configuração de segurança* na política de firewall associada ao Firewall do Azure implantado no hub virtual seguro. Verifique se na coluna **TRÁFEGO PRIVADO**, aparece **Protegido pelo Firewall do Azure** para todas as conexões de rede virtual e de branches para as quais você deseja filtrar o tráfego.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-policy-private-traffic-configuration.png" alt-text="Tráfego privado protegido pelo Firewall do Azure" border="true":::

2. Verifique a **Configuração de segurança** na política de firewall associada ao Firewall do Azure implantado no hub virtual seguro. Verifique se há uma entrada /32 para cada endereço IP privado do ponto de extremidade privado para o qual você deseja filtrar o tráfego em **Prefixos de tráfego privado**.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-manager-security-configuration.png" alt-text="Configuração de segurança do Gerenciador de Firewall – Prefixos de tráfego privado" border="true":::

3. No hub virtual seguro na WAN virtual, inspecione rotas efetivas para as tabelas de rotas associadas às conexões de branches e redes virtuais para as quais você deseja filtrar o tráfego. Verifique se há entradas /32 para cada endereço IP privado do ponto de extremidade privado para o qual deseja filtrar o tráfego.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/secured-virtual-hub-effective-routes.png" alt-text="Rotas efetivas do hub virtual seguro" border="true":::

4. Inspecione as rotas efetivas nas NICs anexadas às máquinas virtuais implantadas nas redes virtuais para as quais deseja filtrar o tráfego. Verifique se há entradas /32 para cada endereço IP privado do ponto de extremidade privado para o qual deseja filtrar o tráfego.
 
   CLI do Azure:

   ```azurecli-interactive
   az network nic show-effective-route-table --name <Network Interface Name> --resource-group <Resource Group Name> -o table
   ```
5. Inspecione as tabelas de roteamento dos dispositivos de roteamento locais. Verifique se você está aprendendo os espaços de endereço das redes virtuais em que os pontos de extremidade privados são implantados.

   A WAN virtual do Azure não anuncia os prefixos configurados em **Prefixos de tráfego privado** na **Configuração de segurança** da política de firewall para o local. Espera-se que as entradas /32 não sejam mostradas nas tabelas de roteamento dos dispositivos de roteamento locais.

6. Inspecione os logs **AzureFirewallApplicationRule** e **AzureFirewallNetworkRule** do Firewall do Azure. Verifique se o tráfego destinado aos pontos de extremidade privados está sendo registrado em log.

   As entradas de log **AzureFirewallNetworkRule** não incluem informações de FQDN. Filtre segundo o endereço IP e a porta ao inspecionar as regras de rede.

   Ao filtrar o tráfego destinado aos pontos de extremidade privados dos [Arquivos do Azure](../storage/files/storage-files-introduction.md), entradas do log **AzureFirewallNetworkRule** serão geradas apenas na primeira vez em que um cliente montar ou se conectar ao compartilhamento de arquivos. O Firewall do Azure não gerará logs para operações [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) para arquivos no compartilhamento de arquivo. Isso ocorre porque operações CRUD são transportadas pelo canal TCP persistente aberto na primeira vez em que o cliente se conecta ou monta no compartilhamento de arquivos.

   Exemplo de consulta de log da regra de aplicativo:

   ```kusto
   AzureDiagnostics
   | where msg_s contains "database.windows.net"
   | where Category contains "ApplicationRule"
   ```
## <a name="next-steps"></a>Próximas etapas

- [Usar o Firewall do Azure para inspecionar o tráfego destinado a um ponto de extremidade privado](../private-link/inspect-traffic-with-azure-firewall.md)
