---
title: "Perguntas frequentes sobre problemas de rede e conectividade para Serviços de Nuvem do Microsoft Azure | Microsoft Docs"
description: "Este artigo lista as perguntas frequentes sobre conectividade e rede para Serviços de Nuvem do Microsoft Azure."
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: genli
ms.openlocfilehash: 7b435b6904b05228a63e3ed3a9fed78747b843c9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problemas de rede e conectividade para Serviços de Nuvem do Azure: perguntas frequentes

Este artigo inclui perguntas frequentes sobre problemas de conectividade e rede para [Serviços de Nuvem do Microsoft Azure](https://azure.microsoft.com/services/cloud-services). Você também pode consultar a [página de tamanho de VM de Serviços de Nuvem](cloud-services-sizes-specs.md) para obter informações de tamanho.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Eu não consigo reservar um IP em um serviço de nuvem de vários VIPs
Primeiro, certifique-se de que a instância de máquina virtual que você está tentando reservar o IP esteja ativada. Segundo, certifique-se de usar IPs Reservados para as implantações de preparo e produção. **Não** altere as configurações enquanto a implantação é atualizada.

## <a name="how-do-i-remote-desktop-when-i-have-an-nsg"></a>Como acesso a área de trabalho remota quando tenho um NSG?
Adicione regras para o NSG que permitem o tráfego nas portas **3389** e **20000**.  A Área de Trabalho Remota usa a porta **3389**.  Instâncias de serviço de nuvem têm a carga balanceada, de modo que você não pode controlar diretamente a escolha da instância à qual se conectar.  Os agentes *RemoteForwarder* e *RemoteAccess* gerenciam o tráfego RDP e permitem que o cliente envie um cookie RDP e especifique uma instância individual à qual se conectar.  Os agentes *RemoteForwarder* e *RemoteAccess* exigem que a porta **20000** esteja aberta, que poderá ser bloqueada se você tiver um NSG.

## <a name="can-i-ping-a-cloud-service"></a>Posso executar ping de um serviço de nuvem?

Não, não usando o protocolo ICMP/"ping" normal. O protocolo ICMP não é permitido por meio do Azure Load Balancer.

Para testar a conectividade, é recomendável fazer um ping de porta. Enquanto Ping.exe utiliza ICMP, outras ferramentas, como PSPing, Nmap e telnet, permitem testar a conectividade com uma porta TCP específica.

Para obter mais informações, consulte [Usar pings de porta em vez de ICMP para testar a conectividade de VM do Azure](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/).

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-indicate-some-sort-of-malicious-attack-to-the-cloud-service"></a>Como fazer para impedir o recebimento de milhares de visitas de endereços IP desconhecidos que indiquem algum tipo de ataque mal-intencionado ao serviço de nuvem?
O Azure implementa um segurança de rede de várias camadas para proteger seus serviços de plataforma contra ataques de DDoS (negação de serviço distribuída). O sistema de proteção contra DDoS do Azure faz parte do processo de monitoramento contínuo do Azure, que é aprimorado continuamente por meio de teste de penetração. Esse sistema de proteção DDoS foi projetado para resistir não só a ataques externos, como também a de outros locatários do Azure. Para obter mais detalhes, consulte [Segurança de Rede do Microsoft Azure](http://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf).

Você também pode criar uma tarefa de inicialização para bloquear seletivamente alguns endereços IP específicos. Para obter mais informações, consulte [Bloquear um endereço IP específico](cloud-services-startup-tasks-common.md#block-a-specific-ip-address).

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>Quando tento fazer RDP para minha instância de serviço de nuvem, recebo a mensagem: "A conta do usuário expirou".
Você pode receber a mensagem de erro "Esta conta de usuário expirou" quando você ignora a data de validade configurada nas configurações de RDP. Você pode alterar a data de validade no portal seguindo estas etapas:
1. Faça logon no Console de Gerenciamento do Azure (https://manage.windowsazure.com), navegue até seu serviço de nuvem e selecione a guia **Configurar**.
2. Selecione **Remoto**.
3. Altere a data "Expira em" e, em seguida, salve a configuração.

Agora você deve ser capaz de fazer RDP para seu computador.

## <a name="why-is-loadbalancer-not-balancing-traffic-equally"></a>Por que o Balanceador de Carga não está balanceamento o tráfego uniformemente?
Para obter informações sobre como funciona o balanceador de carga interno, consulte [Novo modo de distribuição do Azure Load Balancer](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/).

O algoritmo de distribuição usado é um hash de 5 tuplas (IP de origem, porta de origem, IP de destino, porta de destino, tipo de protocolo) para mapear o tráfego para os servidores disponíveis. Ele fornece permanência somente dentro de uma sessão de transporte. Os pacotes na mesma sessão TCP ou UDP serão direcionados para a mesma instância do DIP (IP de Datacenter), atrás do ponto de extremidade de balanceamento de carga. Quando o cliente fecha e reabre a conexão ou inicia uma nova sessão por meio do mesmo IP de origem, a porta de origem é alterada e faz com que o tráfego vá para um ponto de extremidade DIP diferente.

## <a name="how-can-i-redirect-the-incoming-traffic-to-my-default-url-of-cloud-service-to-a-custom-url"></a>Como redirecionar o tráfego de entrada para a minha URL padrão do Serviço de Nuvem para uma URL personalizada? 

O Módulo de Reescrita de URL do IIS poderia ser usado para redirecionar o tráfego que chega na URL padrão do serviço de nuvem (por exemplo, \*.cloudapp.net) para alguma URL/Nome DNS personalizada. Como o módulo de reescrita de URL é habilitado por padrão nas Funções Web e suas regras são configuradas no web.config do aplicativo, ele sempre estaria disponível na VM independentemente das reinicializações/recriações de imagem. Para obter mais informações, consulte:

- [Criando regras de reescrita para o Módulo de Reescrita de URL](https://docs.microsoft.com/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)
- [Como remover o link padrão](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top)

## <a name="how-can-i-blockdisable-the-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>Como bloquear/desabilitar o tráfego de entrada para a URL padrão do meu Serviço de Nuvem? 

É possível impedir o tráfego de entrada para a URL/Nome padrão do seu serviço de nuvem (por exemplo, \*.cloudapp.net) definindo o cabeçalho de host para um nome DNS personalizado (por exemplo, www.MyCloudService.com) na configuração de associação de site no arquivo de definição do serviço de nuvem (*.csdef) conforme indicado a seguir: 
 

    <?xml version="1.0" encoding="utf-8"?> 
    <ServiceDefinition name="AzureCloudServicesDemo" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6"> 
      <WebRole name="MyWebRole" vmsize="Small"> 
        <Sites> 
          <Site name="Web"> 
            <Bindings> 
              <Binding name="Endpoint1" endpointName="Endpoint1" hostHeader="www.MyCloudService.com" /> 
            </Bindings> 
          </Site> 
        </Sites> 
        <Endpoints> 
          <InputEndpoint name="Endpoint1" protocol="http" port="80" /> 
        </Endpoints> 
        <ConfigurationSettings> 
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" /> 
        </ConfigurationSettings> 
      </WebRole> 
    </ServiceDefinition> 
 
Como essa associação do cabeçalho do host é imposta por meio do arquivo csdef, o serviço só poderia ser acessado por meio do nome personalizado 'www.MyCloudService.com', enquanto todas as solicitações de entrada para o domínio '*.cloudapp.net' sempre falhariam. Dito isso, se você usar, no entanto, uma sonda SLB personalizada ou um balanceador de carga interno no serviço, bloquear a URL/Nome padrão do serviço poderá interferir no comportamento de investigação. 

## <a name="how-to-make-sure-the-public-facing-ip-address-of-a-cloud-service-aka-vip-never-changes-so-that-it-could-be-customarily-whitelisted-by-few-specific-clients"></a>Como garantir que o endereço IP voltado para o público de um Serviço de Nuvem (também conhecido como VIP) nunca seja alterado para ele poder ser adicionado normalmente à lista de permissões por clientes específicos?

Para adicionar o endereço IP do seus Serviços de Nuvem à lista de permissões, é recomendável que você tenha um IP Reservado associado a ele, caso contrário, o IP virtual fornecido pelo Azure será desalocado da sua assinatura se você excluir a implantação. Observe que, para uma operação de permuta VIP bem-sucedida, seriam necessários IPs Reservados individuais para slots de Preparo e de Produção. Na ausência deles, a operação de permuta falhará. Siga estes artigos para reservar um endereço IP e associá-lo aos seus Serviços de Nuvem:  
 
- [Reservar o endereço IP de um serviço de nuvem existente](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
- [Associar um IP reservado a um serviço de nuvem usando um arquivo de configuração de serviço](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file) 

Desde que você tenha mais de uma instância para suas funções, associar RIP ao seu Serviço de Nuvem não causaria nenhum tempo de inatividade. Como alternativa, é possível adicionar o intervalo IP de seu Datacenter do Azure à lista de permissões. É possível encontrar todos os intervalos de IP do Azure [aqui](https://www.microsoft.com/en-us/download/details.aspx?id=41653). 

Esse arquivo contém intervalos de endereços IP (inclusive intervalos de Computação, SQL e Armazenamento) usados em Datacenters do Microsoft Azure. Um arquivo atualizado é postado semanalmente que reflete os intervalos atualmente implantados e quaisquer alterações futuras para os intervalos de IP. Novos intervalos que aparecem no arquivo não serão usados em datacenters por pelo menos uma semana. Baixe o novo arquivo xml semanalmente e faça as alterações necessárias no seu site para identificar corretamente os serviços em execução no Azure. Os usuários do Express Route podem observar esse arquivo usado para atualizar o anúncio de BGP de espaço do Azure na primeira semana de cada mês. 

## <a name="how-can-i-use-azure-resource-manager-vnets-with-cloud-services"></a>Como usar VNets do Azure Resource Manager com Serviços de Nuvem? 

Os Serviços de Nuvem não podem ser colocados em uma VNet do Azure Resource Manager, mas uma VNet do Azure Resource Manager e uma VNet clássica podem ser conectadas por emparelhamento. Para obter mais informações, consulte [Emparelhamento de rede virtual do Azure](../virtual-network/virtual-network-peering-overview.md).