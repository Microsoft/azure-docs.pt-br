---
title: Conectividade de ponto de extremidade público para VMs do Azure e ILB Standard em cenários de HA de SAP
description: Conectividade de ponto de extremidade público para Máquinas Virtuais usando o Azure Standard Load Balancer em cenários de alta disponibilidade do SAP
services: virtual-machines-windows,virtual-network,storage,
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/01/2020
ms.author: radeltch
ms.openlocfilehash: 6f2268ae9a86978e9266ea0e35411727b238d4b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101671644"
---
# <a name="public-endpoint-connectivity-for-virtual-machines-using-azure-standard-load-balancer-in-sap-high-availability-scenarios"></a>Conectividade de ponto de extremidade público para Máquinas Virtuais usando o Azure Standard Load Balancer em cenários de alta disponibilidade do SAP

O escopo deste artigo é descrever as configurações que permitirão a conectividade de saída para pontos de extremidade públicos. As configurações estão principalmente no contexto de Alta Disponibilidade com Pacemaker para SUSE/RHEL.  

Se você estiver usando o Pacemaker com o agente de isolamento do Azure em sua solução de alta disponibilidade, as VMs precisarão ter conectividade de saída para a API de gerenciamento do Azure. O artigo apresenta várias opções para permitir que você selecione a opção mais adequada para seu cenário.  

## <a name="overview"></a>Visão geral

Ao implementar alta disponibilidade para soluções SAP por meio de clustering, um dos componentes necessários é o [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md). O Azure oferece duas SKUs do balanceador de carga: Standard e Basic.

O balanceador de carga do Azure Standard oferece algumas vantagens em relação ao balanceador de carga Basic. Por exemplo, ele funciona em zonas de disponibilidade do Azure, tem recursos de monitoramento e de registro melhores para facilitar a solução de problemas e latência reduzida. O recurso "portas de HA" abrange todas as portas, ou seja, não é mais necessário listar todas as portas individualmente.  

Há algumas diferenças importantes entre o SKU Basic e o Standard do Azure Load Balancer. Uma delas é o tratamento do tráfego de saída para o ponto de extremidade público. Para uma comparação completa entre as SKUs Basic e Standard do balanceador de carga, confira [Comparação de SKUs do Load Balancer](../../../load-balancer/load-balancer-overview.md).  
 
Quando as VMs sem endereços IP públicos são colocadas no pool de back-end do Standard Azure Load Balancer (sem endereço IP público), não há nenhuma conectividade de saída para pontos de extremidade públicos enquanto a configuração adicional não é feita.  

Se uma VM receber um endereço IP público, ou se estiver no pool de back-end de um balanceador de carga com endereço IP público, ela terá conectividade de saída para pontos de extremidade públicos.  

Os sistemas SAP geralmente contêm dados comerciais confidenciais. Raramente é aceitável que as VMs que hospedam sistemas SAP possam ser acessadas por meio de endereços IP públicos. Ao mesmo tempo, há cenários que poderiam exigir conectividade de saída da VM para pontos de extremidade públicos.  

Exemplos de cenários que exigem acesso a um ponto de extremidade público do Azure são:  
- O agente de isolamento do Azure requer acesso a **Management.Azure.com** e **login.microsoftonline.com**  
- [Backup do Azure](../../../backup/tutorial-backup-sap-hana-db.md#set-up-network-connectivity)
- [Azure Site Recovery](../../../site-recovery/azure-to-azure-about-networking.md#outbound-connectivity-for-urls)  
- Uso de repositório público para aplicar patch ao sistema operacional
- O fluxo de dados do aplicativo SAP pode exigir conectividade de saída para o ponto de extremidade público

Se sua implantação do SAP não exigir conectividade de saída para pontos de extremidade públicos, você não precisará implementar a configuração adicional. É suficiente criar um Azure Load Balancer interno de SKU padrão para seu cenário de alta disponibilidade, supondo que também não haja a necessidade de conectividade de entrada de pontos de extremidade públicos.  

> [!Note]
> Quando as VMs sem endereços IP públicos forem colocadas no pool de back-end do Standard Azure Load Balancer (sem endereço IP público), não haverá nenhuma conectividade de saída com a Internet se não houver configuração adicional a fim de permitir o roteamento para pontos de extremidade públicos.  
>Se as VMs tiverem endereços IP públicos ou já estiverem no pool de back-end do Azure Load Balancer com o endereço IP público, a VM terá conectividade de saída para pontos de extremidade públicos.


Leia os seguintes documentos primeiro:

* Azure Standard Load Balancer
  * [Visão geral do Azure Standard Load Balancer](../../../load-balancer/load-balancer-overview.md): visão geral abrangente do Azure Standard Load Balancer, princípios importantes, conceitos e tutoriais 
  * [Conexões de saída no Azure](../../../load-balancer/load-balancer-outbound-connections.md#scenarios): cenários sobre como obter conectividade de saída no Azure
  * [Regras de saída do balanceador de carga](../../../load-balancer/load-balancer-outbound-connections.md#outboundrules): explica os conceitos das regras de saída do balanceador de carga e como criar regras de saída
* Firewall do Azure
  * [Visão geral do Firewall do Azure](../../../firewall/overview.md): visão geral do Firewall do Azure
  * [Tutorial: Implantar e configurar o Firewall do Azure](../../../firewall/tutorial-firewall-deploy-portal.md): instruções sobre como configurar o Firewall do Azure por meio do portal do Azure
* [Redes virtuais – regras definidas pelo usuário](../../../virtual-network/virtual-networks-udr-overview.md#user-defined): conceitos e regras de roteamento do Azure  
* [Marcas de Serviço de Grupos de Segurança](../../../virtual-network/network-security-groups-overview.md#service-tags): como simplificar os Grupos de Segurança de Rede e a configuração do Firewall com marcas de serviço

## <a name="option-1-additional-external-azure-standard-load-balancer-for-outbound-connections-to-internet"></a>Opção 1: Standard Load Balancer do Azure externo adicional para conexões de saída com a Internet

Uma opção para ter conectividade de saída para pontos de extremidade públicos, sem permitir a entrada da conectividade do ponto de extremidade público para a VM, é criar um segundo balanceador de carga com o endereço IP público, adicionar as VMs ao pool de back-end do segundo balanceador de carga e definir somente [regras de saída](../../../load-balancer/load-balancer-outbound-connections.md#outboundrules).  
Use [Grupos de Segurança de Rede](../../../virtual-network/network-security-groups-overview.md) para controlar os pontos de extremidade públicos que podem ser acessados para chamadas de saída da VM.  
Para obter mais informações, confira o Cenário 2 no documento [Conexões de saída](../../../load-balancer/load-balancer-outbound-connections.md#scenarios).  
A configuração tem a seguinte aparência:  

![Controlar a conectividade para pontos de extremidade públicos com Grupos de Segurança de Rede](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-public.png)

### <a name="important-considerations"></a>Considerações importantes

- Você pode usar um Load Balancer Público adicional em várias VMs na mesma sub-rede para obter conectividade de saída para o ponto de extremidade público e otimizar o custo  
- Use [Grupos de Segurança de Rede](../../../virtual-network/network-security-groups-overview.md) para controlar quais pontos de extremidade públicos podem ser acessados nas VMs. Você pode atribuir o Grupo de Segurança de Rede à sub-rede ou a cada VM. Sempre que possível, use [Marcas de serviço](../../../virtual-network/network-security-groups-overview.md#service-tags) para reduzir a complexidade das regras de segurança.  
- O Azure Standard Load Balancer com o endereço IP público e as regras de saída permite o acesso direto ao ponto de extremidade público. Se você tiver requisitos de segurança corporativa de que todo o tráfego de saída deva passar por meio da solução corporativa centralizada para auditoria e registro em log, talvez não seja possível atender ao requisito nesse cenário.  

>[!TIP]
>Sempre que possível, use [Marcas de serviço](../../../virtual-network/network-security-groups-overview.md#service-tags) para reduzir a complexidade do Grupo de Segurança de Rede. 

### <a name="deployment-steps"></a>Etapas de implantação.

1. Criar balanceador de carga  
   1. No [portal do Azure](https://portal.azure.com), clique em Todos os recursos, Adicionar, e pesquise **Load Balancer**  
   1. Clique em **Criar** 
   1. Nome do Load Balancer **MyPublicILB**  
   1. Selecione **Público** como Tipo e **Standard** como SKU  
   1. Selecione **Criar endereço IP público** e especifique **MyPublicILBFrondEndIP** como nome  
   1. Selecione **Com Redundância de Zona** como a Zona de disponibilidade  
   1. Clique em Revisar e criar e em Criar  
2. Crie um pool de back-end **MyBackendPoolOfPublicILB** e adicione as VMs.  
   1. Selecione a rede virtual  
   1. Selecione as VMs e seus endereços IP e adicione-as ao pool de back-end  
3. [Criar regras de saída](../../../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard%3ftabs%3doption-1-create-load-balancer-standard#create-outbound-rule-configuration). No momento, não é possível criar regras de saída do portal do Azure. Você pode criar regras de saída com a [CLI do Azure](../../../cloud-shell/overview.md).  

   ```azurecli
    az network lb outbound-rule create --address-pool MyBackendPoolOfPublicILB --frontend-ip-configs MyPublicILBFrondEndIP --idle-timeout 30 --lb-name MyPublicILB --name MyOutBoundRules  --outbound-ports 10000 --enable-tcp-reset true --protocol All --resource-group MyResourceGroup
   ```

4. Crie regras de Grupo de Segurança de Rede para restringir o acesso a pontos de extremidade públicos específicos. Se houver um Grupo de Segurança de Rede existente, você poderá ajustá-lo. O exemplo a seguir mostra como habilitar o acesso à API de gerenciamento do Azure: 
   1. Navegue até o Grupo de Segurança de Rede
   1. Clique em Regras de Segurança de Saída
   1. Adicione uma regra para **Negar** todo o acesso de saída à **Internet**.
   1. Adicione uma regra para **Permitir** acesso ao **AzureCloud** com prioridade inferior à prioridade da regra para negar todo o acesso à Internet.


   As regras de segurança de saída têm a seguinte aparência: 

   ![Conexão de saída com um segundo Load Balancer com IP público](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-network-security-groups.png)

   Para obter mais informações sobre os grupos de segurança de rede do Azure, confira [Grupos de Segurança](../../../virtual-network/network-security-groups-overview.md). 

## <a name="option-2-azure-firewall-for-outbound-connections-to-internet"></a>Opção 2: Firewall do Azure para conexões de saída para a Internet

Outra opção para obter a conectividade de saída para pontos de extremidade públicos sem permitir a conectividade de entrada para a VM de pontos de extremidade públicos é com o Firewall do Azure. O Firewall do Azure é um serviço gerenciado, com alta disponibilidade interna, e pode abranger várias Zonas de Disponibilidade.  
Você também precisará implantar uma [Rota Definida pelo Usuário](../../../virtual-network/virtual-networks-udr-overview.md#custom-routes), associada à sub-rede em que as VMs e o Azure Load Balancer estão implantados e apontando para o Firewall do Azure, para rotear o tráfego pelo Firewall do Azure.  
Para obter detalhes sobre como implantar o Firewall do Azure, confira [Implantar e configurar o Firewall do Azure](../../../firewall/tutorial-firewall-deploy-portal.md).  

A arquitetura seria semelhante a:

![Conexão de saída com o Firewall do Azure](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall.png)

### <a name="important-considerations"></a>Considerações importantes

- O Firewall do Azure é um serviço de nuvem nativo, com alta disponibilidade interna, e dá suporte à implantação zonal.
- Requer sub-rede adicional que precisa ter o nome AzureFirewallSubnet. 
- Se estiver transferindo grandes conjuntos de dados saindo da rede virtual em que as VMs SAP estão localizadas para uma VM em outra rede virtual ou para o ponto de extremidade público, talvez essa não seja uma solução econômica. Um exemplo é copiar grandes backups entre redes virtuais. Para obter detalhes, confira Preços do Firewall do Azure.  
- Se a solução de firewall corporativo não for o Firewall do Azure e você tiver requisitos de segurança que façam com que todo o tráfego de saída deva passar por uma solução corporativa centralizada, essa solução poderá não ser prática.  

>[!TIP]
>Sempre que possível, use [Marcas de serviço](../../../virtual-network/network-security-groups-overview.md#service-tags) para reduzir a complexidade das regras do Firewall do Azure.  

### <a name="deployment-steps"></a>Etapas de implantação.

1. As etapas de implantação pressupõem que você já tenha a rede virtual e a sub-rede definidas para suas VMs.  
2. Crie a sub-rede **AzureFirewallSubnet** na mesma Rede Virtual em que as VMS e o Standard Load Balancer estão implantados.  
   1. No portal do Azure, navegue até a Rede Virtual: Clique em Todos os Recursos, procure a Rede Virtual, clique na Rede Virtual, selecione Sub-redes.  
   1. Clique em Adicionar Sub-rede. Insira **AzureFirewallSubnet** como Nome. Insira o intervalo de endereços apropriado. Salve.  
3. Crie o Firewall do Azure.  
   1. No portal do Azure, selecione Todos os recursos, clique em Adicionar, Firewall, Criar. Selecione Grupo de recursos (selecione o mesmo grupo de recursos em que a Rede Virtual está).  
   1. Insira o nome do recurso do Firewall do Azure. Por exemplo, **MyAzureFirewall**.  
   1. Selecione Região e selecione pelo menos duas Zonas de disponibilidade alinhadas com as Zonas de disponibilidade nas quais suas VMs estão implantadas.  
   1. Selecione a Rede Virtual em que as VMs SAP e o Azure Load Balancer estão implantados.  
   1. Endereço IP público: Clique em Criar e insira um nome. Por exemplo, **MyFirewallPublicIP**.  
4. Crie uma regra do Firewall do Azure para permitir a conectividade de saída para pontos de extremidade públicos especificados. O exemplo mostra como permitir o acesso ao ponto de extremidade público da API de gerenciamento do Azure.  
   1. Selecione Regras, Coleção de Regras de Rede e clique em Adicionar coleção de regras de rede.  
   1. Nome: **MyOutboundRule**, insira a Prioridade, Selecionar Ação, **Permitir**.  
   1. Serviço: Dê o nome de **ToAzureAPI**.  Protocolo: Selecione **Qualquer**. Endereço de origem: insira o intervalo da sub-rede em que as VMs e o Standard Load Balancer estão implantados, por exemplo, **11.97.0.0/24**. Portas de Destino: insira <b>*</b>.  
   1. Salvar
   1. Como você ainda está no Firewall do Azure, selecione Visão geral. Anote o endereço IP privado do Firewall do Azure.  
5. Criar rota para o Firewall do Azure  
   1. No portal do Azure, selecione Todos os recursos, clique em Adicionar, Tabela de Rotas, Criar.  
   1. Insira o nome MyRouteTable, selecione Assinatura, Grupo de recursos e Local (que deve corresponder ao local da Rede virtual e do Firewall).  
   1. Salvar  

   A regra de firewall teria a seguinte aparência: ![ diagrama que mostra a aparência do firewall.](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall-rule.png)

6. Crie uma Rota Definida pelo Usuário da sub-rede de suas VMs para o IP privado do **MyAzureFirewall**.
   1. Como você está na Tabela de Roteamento, clique em Rotas. Selecione Adicionar. 
   1. Nome da rota: ToMyAzureFirewall, Prefixo de endereço: **0.0.0.0/0** Tipo do próximo salto: Selecione Solução de Virtualização. Endereço do próximo salto: insira o endereço IP privado do firewall que você configurou: **11.97.1.4**.  
   1. Salvar

## <a name="option-3-using-proxy-for-pacemaker-calls-to-azure-management-api"></a>Opção 3: usando proxy para chamadas de pacemaker para a API de gerenciamento do Azure

Você pode usar proxy para permitir chamadas do Pacemaker para o ponto de extremidade público da API de gerenciamento do Azure.  

### <a name="important-considerations"></a>Considerações importantes

  - Se já houver um proxy corporativo configurado, você poderá encaminhar chamadas de saída para pontos de extremidade públicos por meio dele. As chamadas de saída para pontos de extremidade públicos passarão pelo ponto de controle corporativo.  
  - Verifique se a configuração do proxy permite a conectividade de saída para a API de gerenciamento do Azure: `https://management.azure.com` e `https://login.microsoftonline.com`  
  - Verifique se há uma rota das VMs para o proxy  
  - O proxy tratará apenas chamadas HTTP/HTTPS. Se houver necessidade adicional de fazer chamadas de saída para o ponto de extremidade público em protocolos diferentes (como RFC), será necessário pensar em uma solução alternativa  
  - A solução de proxy precisa estar altamente disponível para evitar instabilidade no cluster do Pacemaker  
  - Dependendo do local do proxy, ele pode introduzir latência adicional nas chamadas do agente de isolamento do Azure para a API de Gerenciamento do Azure. Se o seu proxy corporativo ainda estiver no local e o cluster do Pacemaker estiver no Azure, meça a latência e considere se essa solução é adequada para você  
  - Se ainda não houver um proxy corporativo altamente disponível, não recomendamos a opção, pois o cliente estaria incorrendo em custos e complexidade extras. No entanto, se você decidir implantar uma solução de proxy adicional para permitir a conectividade de saída do Pacemaker para a API pública de gerenciamento do Azure, verifique se o proxy está altamente disponível e se a latência das VMs para o proxy é baixa.  

### <a name="pacemaker-configuration-with-proxy"></a>Configuração do Pacemaker com proxy 

Há muitas opções de proxy diferentes disponíveis na indústria. As instruções passo a passo para a implantação de proxy estão fora do escopo deste documento. No exemplo a seguir, presumimos que o proxy está respondendo a **MyProxyService** e escutando a porta **MyProxyPort**.  
Para permitir que o Pacemaker se comunique com a API de gerenciamento do Azure, execute as seguintes etapas em todos os nós de cluster:  

1. Edite o arquivo de configuração /etc/sysconfig/pacemaker e adicione as seguintes linhas (todos os nós de cluster):

   ```console
   sudo vi /etc/sysconfig/pacemaker
   # Add the following lines
   http_proxy=http://MyProxyService:MyProxyPort
   https_proxy=http://MyProxyService:MyProxyPort
   ```

2. Reinicie o serviço Pacemaker em **todos** os nós de cluster.  
  - SUSE
 
     ```console
     # Place the cluster in maintenance mode
     sudo crm configure property maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo crm configure property maintenance-mode=true
     ```

  - Red Hat  

     ```console
     # Place the cluster in maintenance mode
     sudo pcs property set maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo pcs property set maintenance-mode=false
     ```

## <a name="other-options"></a>Outras opções

Se o tráfego de saída for roteado por meio de proxy de firewall baseado em URL de terceiros:

- Se estiver usando o agente de isolamento do Azure, verifique se a configuração do firewall permite a conectividade de saída com a API de gerenciamento do Azure: `https://management.azure.com` e `https://login.microsoftonline.com`   
- se usar a infraestrutura de atualização de nuvem pública do Azure do SUSE para aplicar atualizações e patches, consulte [infraestrutura de atualização de nuvem pública do azure 101](https://suse.com/c/azure-public-cloud-update-infrastructure-101/)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como configurar o Pacemaker no SUSE no Azure](./high-availability-guide-suse-pacemaker.md)
* [Saiba como configurar o Pacemaker no Red Hat no Azure](./high-availability-guide-rhel-pacemaker.md)