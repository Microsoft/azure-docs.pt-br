---
title: Perguntas frequentes sobre problemas de rede e conectividade para Serviços de Nuvem do Microsoft Azure | Microsoft Docs
description: Este artigo lista as perguntas frequentes sobre conectividade e rede para Serviços de Nuvem do Microsoft Azure.
services: cloud-services
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2018
ms.author: genli
ms.openlocfilehash: 2a46879a6882e6d45e4a7ccce59e4a02feea9005
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61432952"
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Conectividade e problemas de rede para serviços de nuvem do Azure: Perguntas frequentes (FAQs)

Este artigo inclui perguntas frequentes sobre os problemas de conectividade e rede para [Serviços de Nuvem do Microsoft Azure](https://azure.microsoft.com/services/cloud-services). Para obter informações sobre tamanho, consulte a [página de tamanho de VM de Serviços de Nuvem](cloud-services-sizes-specs.md).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Eu não consigo reservar um IP em um serviço de nuvem de vários VIPs.
Primeiro, certifique-se de que a instância de máquina virtual que você está tentando reservar o IP esteja ativada. Segundo, certifique-se de usar IPs reservados para as implantações de preparo e produção. *Não* altere as configurações enquanto a implantação é atualizada.

## <a name="how-do-i-use-remote-desktop-when-i-have-an-nsg"></a>Como acesso a área de trabalho remota quando tenho um NSG?
Adicione regras para o NSG que permitem o tráfego nas portas **3389** e **20000**. A Área de Trabalho Remota usa a porta **3389**. Instâncias de serviço de nuvem têm a carga balanceada, de modo que você não pode controlar diretamente a escolha da instância à qual se conectar. Os agentes *RemoteForwarder* e *RemoteAccess* gerenciam o tráfego de protocolo RDP e permitem que o cliente envie um cookie RDP e especifique uma instância individual à qual se conectar. Os agentes *RemoteForwarder* e *RemoteAccess* exigem que a porta **20000** seja aberta, que poderá ser bloqueada se você tiver um NSG.

## <a name="can-i-ping-a-cloud-service"></a>Posso executar ping de um serviço de nuvem?

Não, não usando o protocolo ICMP/"ping" normal. O protocolo ICMP não é permitido por meio do Azure Load Balancer.

Para testar a conectividade, é recomendável fazer um ping de porta. Enquanto Ping.exe utiliza ICMP, você pode usar outras ferramentas, como PSPing, Nmap e telnet para testar a conectividade com uma porta TCP específica.

Para obter mais informações, consulte [Usar pings de porta em vez de ICMP para testar a conectividade de VM do Azure](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/).

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service"></a>Como fazer para impedir o recebimento de milhares de visitas de endereços IP desconhecidos que indiquem um ataque mal-intencionado ao serviço de nuvem?
O Azure implementa um segurança de rede de várias camadas para proteger seus serviços de plataforma contra ataques de DDoS (negação de serviço distribuída). O sistema de proteção contra DDoS do Azure faz parte do processo de monitoramento contínuo do Azure, que é aprimorado continuamente por meio de teste de penetração. Esse sistema de proteção DDoS foi projetado para resistir não só a ataques externos, como também a de outros locatários do Azure. Para obter mais informações, consulte [Segurança de rede do Azure](https://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf).

Você pode criar uma tarefa de inicialização para bloquear seletivamente alguns endereços IP específicos. Para obter mais informações, consulte [Bloquear um endereço IP específico](cloud-services-startup-tasks-common.md#block-a-specific-ip-address).

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>Quando tento fazer RDP para minha instância de serviço de nuvem, recebo a mensagem: "A conta do usuário expirou".
Você pode receber a mensagem de erro "Esta conta de usuário expirou" quando você ignora a data de validade configurada nas configurações de RDP. Você pode alterar a data de validade no portal seguindo estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com), navegue até o seu serviço de nuvem e selecione o guia **Área de Trabalho Remota**.

2. Selecione o slot de implantação de **Produção** ou **Preparo**.

3. Altere a data **Expira em** e, em seguida, salve a configuração.

Agora você deve ser capaz de fazer RDP para seu computador.

## <a name="why-is-azure-load-balancer-not-balancing-traffic-equally"></a>Por que o Azure Load Balancer não está balanceando o tráfego igualmente?
Para obter informações sobre como funciona o balanceador de carga interno, consulte [Novo modo de distribuição do Azure Load Balancer](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/).

O algoritmo de distribuição usado é um hash de 5 tuplas (IP de origem, porta de origem, IP de destino, porta de destino e tipo de protocolo) para mapear o tráfego para os servidores disponíveis. Ele fornece permanência somente dentro de uma sessão de transporte. Os pacotes na mesma sessão TCP ou UDP são direcionados para a mesma instância do DIP (IP de Datacenter), atrás do ponto de extremidade de balanceamento de carga. Quando o cliente fecha e reabre a conexão ou inicia uma nova sessão por meio do mesmo IP de origem, a porta de origem é alterada e faz com que o tráfego vá para um ponto de extremidade DIP diferente.

## <a name="how-can-i-redirect-incoming-traffic-to-the-default-url-of-my-cloud-service-to-a-custom-url"></a>Como redirecionar o tráfego de entrada para a URL padrão do meu Serviço de Nuvem para uma URL personalizada?

O Módulo de Reescrita de URL do IIS pode ser usado para redirecionar o tráfego que chega na URL padrão do serviço de nuvem (por exemplo, \*.cloudapp.net) para alguma URL/Nome DNS personalizada. Como o módulo de reescrita de URL é habilitado nas funções web por padrão e suas regras são configuradas no Web. config do aplicativo, ele estará sempre disponível na VM independentemente das reinicializações/recriações de imagem. Para obter mais informações, consulte:

- [Criar regras de reescrita para o Módulo de Reescrita de URL](https://docs.microsoft.com/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)
- [Remova o link padrão](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top)

## <a name="how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>Como bloquear/desabilitar o tráfego de entrada para a URL padrão do meu serviço de nuvem?

Você pode impedir o tráfego de entrada para o padrão de URL/nome do seu serviço da sua nuvem (por exemplo, \*. >.cloudapp.NET). Defina o cabeçalho de host para um nome DNS personalizado (por exemplo, www.MyCloudService.com) na configuração de associação de site no arquivo de definição (*.csdef) do serviço de nuvem, conforme indicado:

```xml
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
```

Como essa associação de cabeçalho de host é imposta por meio do arquivo csdef, o serviço é acessível somente por meio do nome personalizado "www.MyCloudService.com". Todas as solicitações de entrada para o "*. c" domínio sempre falharão. Se você usar uma sonda SLB personalizada ou um balanceador de carga interno no serviço, bloquear a URL/Nome padrão do serviço poderá interferir no comportamento de investigação.

## <a name="how-can-i-make-sure-the-public-facing-ip-address-of-a-cloud-service-never-changes"></a>Como posso ter certeza de que o endereço de IP público de um serviço de nuvem nunca muda?

Para certificar-se de que o endereço IP público do seu serviço de nuvem (também conhecido como VIP) nunca será alterado para que ele possa estar normalmente na lista de permissões por alguns clientes específicos, é recomendável que você tem um IP reservado associado a ele. Caso contrário, o IP virtual fornecido pelo Azure é desalocado da sua assinatura, se você excluir a implantação. Para a operação de permuta de VIP bem-sucedida, você precisará de IPs reservados individuais para slots de preparo e produção. Sem eles, a operação de troca falhará. Para reservar um endereço IP e associá-lo aos seus Serviços de Nuvem, veja os artigos:

- [Reservar o endereço IP de um serviço de nuvem existente](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
- [Associar um IP reservado a um serviço de nuvem usando um arquivo de configuração de serviço](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

Se você tiver mais de uma instância para suas funções, associar RIP ao seu serviço de nuvem não causaria nenhum tempo de inatividade. Como alternativa, você pode colocar o intervalo de IP do seu datacenter do Azure. Você pode encontrar todos os intervalos de IP do Azure na [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

Esse arquivo contém intervalos de endereços IP (inclusive intervalos de Computação, SQL e Armazenamento) usados em Datacenters do Azure. Um arquivo atualizado que reflete os intervalos atualmente implantados e quaisquer alterações futuras para os intervalos de IP, é postado semanalmente. Novos intervalos que aparecem no arquivo não são usados nos centros de dados por pelo menos uma semana. Baixe o novo arquivo .xml semanalmente e faça as alterações necessárias no seu site para identificar corretamente os serviços em execução no Azure. Os usuários do Azure ExpressRoute podem observar esse arquivo usado para atualizar o anúncio de BGP de espaço do Azure na primeira semana de cada mês.

## <a name="how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services"></a>Como usar as redes virtuais do Azure Resource Manager com Serviços de Nuvem?

Os serviços de nuvem não podem ser colocados em redes virtuais do Azure Resource Manager. Redes virtuais do Gerenciador de Recursos e redes virtuais de implantação clássica podem ser conectadas através de emparelhamento. Para obter mais informações, consulte [Emparelhamento de rede virtual do Azure](../virtual-network/virtual-network-peering-overview.md).


## <a name="how-can-i-get-the-list-of-public-ips-used-by-my-cloud-services"></a>Como é possível obter a lista de IPs públicos usados pelos meus Serviços de Nuvem?

Você pode usar o script do PS a seguir para obter a lista de IPs públicos para Serviços de Nuvem em sua assinatura

```powershell
$services = Get-AzureService  | Group-Object -Property ServiceName

foreach ($service in $services)
{
    "Cloud Service '$($service.Name)'"

    $deployment = Get-AzureDeployment -ServiceName $service.Name
    "VIP - " +  $deployment.VirtualIPs[0].Address
    "================================="
}
```
