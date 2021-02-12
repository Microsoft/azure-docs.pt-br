---
title: Como usar o Gerenciamento de API do Azure com redes virtuais
description: Saiba como configurar uma conexão a uma rede virtual no Gerenciamento de API do Azure e acessar serviços Web por meio dela.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/10/2020
ms.author: apimpm
ms.custom: references_regions
ms.openlocfilehash: c63b71ad00a5621babe07597720a1e9ea87f1e4a
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99260241"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Como usar o Gerenciamento de API do Azure com redes virtuais
As redes virtuais do Azure (VNETs) permitem que você coloque qualquer um dos recursos do Azure em uma rede não roteável para a Internet com acesso controlado. Essas redes podem ser conectadas às redes locais usando várias tecnologias VPN. Para saber mais sobre Redes Virtuais do Azure comece com as informações aqui: [Visão geral da Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md).

O Gerenciamento de API do Azure pode ser implantado na VNET (rede virtual) para que possa acessar serviços de back-end na rede. O portal do desenvolvedor e o gateway de API podem ser configurados para serem acessados pela Internet ou somente na rede virtual.

> [!NOTE]
> A URL do documento de importação de API deve ser hospedada em um endereço de Internet acessível publicamente.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para executar as etapas descritas neste artigo, você precisa ter:

+ Uma assinatura ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Uma instância do APIM. Para obter mais informações, consulte [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).

## <a name="enable-vnet-connection"></a><a name="enable-vpn"> </a>Habilitar conexão de VNET

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Habilitar a conectividade VNET usando o portal do Azure

1. Vá para o [portal do Azure](https://portal.azure.com) para encontrar sua instância de gerenciamento de API. Procure e selecione **serviços de gerenciamento de API**.

2. Escolha sua instância de gerenciamento de API.

3. Selecione **Rede virtual**.
4. Configure a instância de Gerenciamento de API a ser implantada dentro de uma Rede virtual.

    :::image type="content" source="media/api-management-using-with-vnet/api-management-menu-vnet.png" alt-text="Selecione rede virtual em portal do Azure.":::
    
5. Selecione o tipo de acesso desejado:

    * **Off**: Esse é o padrão. O gerenciamento de API não é implantado em uma rede virtual.

    * **Externa**: O portal de desenvolvedor e o gateway de Gerenciamento de API podem ser acessados pela Internet pública por meio de um balanceador de carga externo. O gateway pode acessar recursos na rede virtual.

        ![Emparelhamento público][api-management-vnet-public]

    * **Interna**: O portal de desenvolvedor e o gateway de Gerenciamento de API só podem ser acessados de dentro da rede virtual por meio de um balanceador de carga interno. O gateway pode acessar recursos na rede virtual.

        ![Emparelhamento privado][api-management-vnet-private]

6. Se você selecionou **Externo** ou **Interno**, verá uma lista de todas as regiões em que seu serviço de Gerenciamento de API é provisionado. Escolha um **local** de e, em seguida, selecione sua **de rede virtual** e **de sub-rede**. A lista de redes virtuais é preenchida com as redes virtuais clássica e de Gerenciador de Recursos disponíveis nas assinaturas do Azure configuradas na região que você está configurando.

    > [!IMPORTANT]
    > Ao implantar uma instância do Gerenciamento de API do Azure a uma VNET do Resource Manager, o serviço deve estar em uma sub-rede dedicada que não contém outros recursos, exceto as instâncias do Gerenciamento de API do Azure. Se for feita uma tentativa de implantar uma instância do Gerenciamento de API do Azure em uma VNET do Resource Manager que contém outros recursos, a implantação falhará.

    Em seguida, selecione **Aplicar**. A página **rede virtual** da sua instância de gerenciamento de API é atualizada com suas novas opções de rede virtual e sub-rede.

    :::image type="content" source="media/api-management-using-with-vnet/api-management-using-vnet-select.png" alt-text="Configurações de rede virtual no Portal.":::

7. Na barra de navegação superior, selecione **salvar** e, em seguida, selecione **aplicar a configuração de rede**.

> [!NOTE]
> O endereço VIP da instância do Gerenciamento de API mudará sempre que a VNET for habilitada ou desabilitada.
> O endereço VIP também será alterado quando o Gerenciamento de API for movido de **Externo** para **Interno** ou vice-versa.
>

> [!IMPORTANT]
> Se você remove o gerenciamento de API de uma VNET ou alterar aquela na qual ele é implantado, a VNET usada anteriormente pode ficar bloqueada por até seis horas. Durante esse período, não será possível excluir a rede virtual nem implantar um novo recurso nela. Esse comportamento é verdadeiro para clientes que usam a API-versão 2018-01-01 e anterior. Clientes que usam a API-Version 2019-01-01 e posterior, a VNET é liberada assim que o serviço de gerenciamento de API associado é excluído.

## <a name="deploy-api-management-into-external-vnet"></a><a name="deploy-apim-external-vnet"> </a>Implantar o gerenciamento de API em VNET externa

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-management-create-with-external-vnet%2Fazuredeploy.json)

* **Criar um serviço de Gerenciamento de API dentro de uma VNET**: Use o cmdlet [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) para criar um serviço de Gerenciamento de API do Azure dentro de uma VNET.

* **Implantar um serviço de Gerenciamento de API existente dentro de uma VNET**: Use o cmdlet [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) para mover um serviço de Gerenciamento de API do Azure existente dentro de uma Rede Virtual.

## <a name="connect-to-a-web-service-hosted-within-a-virtual-network"></a><a name="connect-vnet"> </a>Conectar-se a um serviço Web hospedado em uma rede virtual
Depois que o serviço de Gerenciamento de API for conectado à VNET, acessar os serviços de back-end nela será o mesmo que acessar serviços públicos. Basta digitar o endereço IP local ou o nome do host (se um servidor DNS estiver configurado para a VNET) do serviço Web no campo **URL do serviço Web** ao criar uma nova API ou editar uma já existente.

![Adicionar a API da VPN][api-management-setup-vpn-add-api]

## <a name="common-network-configuration-issues"></a><a name="network-configuration-issues"> </a>Problemas comuns de configuração de rede
Veja a seguir uma lista de problemas comuns de erro de configuração que podem ocorrer ao implantar o serviço de Gerenciamento de API em uma rede virtual.

* **Instalação de servidor DNS personalizado**: O serviço de Gerenciamento de API depende de vários serviços do Azure. Quando o Gerenciamento de API estiver hospedado em uma VNET com um servidor DNS personalizado, será necessário resolver os nomes de host desses serviços do Azure. Siga [estas](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) orientações sobre a configuração de DNS personalizada. Consulte a tabela de portas abaixo e outros requisitos de rede para ter uma referência.

> [!IMPORTANT]
> Se você planeja usar Servidor(es) de DNS personalizados para a rede virtual, você deve configurá-lo **antes** de implantar um serviço de gerenciamento de API para ele. Caso contrário, você precisa atualizar o serviço de gerenciamento de API cada vez que alterar o(s) servidor(es) DNS executando a [Operação Aplicar configurações de rede](/rest/api/apimanagement/2019-12-01/apimanagementservice/applynetworkconfigurationupdates)

* **Portas necessárias para o Gerenciamento de API**: O tráfego de entrada e saída para a sub-rede na qual o Gerenciamento de API foi implantado pode ser controlado usando o [Grupo de Segurança de Rede][Network Security Group]. Se qualquer uma dessas portas estiver indisponível, o Gerenciamento de API poderá não funcionar corretamente e poderá se tornar inacessível. A existência de uma ou mais dessas portas bloqueadas é outro problema de configuração incorreta no uso do Gerenciamento de API em uma VNET.

<a name="required-ports"> </a> Quando uma instância do serviço Gerenciamento de API está hospedada em uma VNET, as portas na tabela a seguir são usadas.

| Porta(s) de Origem/Destino | Direção          | Protocolo de transporte |   [Marcas de Serviço](../virtual-network/network-security-groups-overview.md#service-tags) <br> Origem/Destino   | Finalidade (\*)                                                 | Tipo de Rede Virtual |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / [80], 443                  | Entrada            | TCP                | INTERNET / VIRTUAL_NETWORK            | Comunicação do cliente com o Gerenciamento de API                      | Externo             |
| * / 3443                     | Entrada            | TCP                | ApiManagement/VIRTUAL_NETWORK       | Ponto de extremidade de gerenciamento para o Portal do Azure e o Powershell         | Interno e externo  |
| */443                  | Saída           | TCP                | VIRTUAL_NETWORK / Armazenamento             | **Dependência no Armazenamento do Microsoft Azure**                             | Interno e externo  |
| */443                  | Saída           | TCP                | VIRTUAL_NETWORK/AzureActiveDirectory | [Azure Active Directory](api-management-howto-aad.md) e dependência do Keyvault do Azure                  | Interno e externo  |
| * / 1433                     | Saída           | TCP                | VIRTUAL_NETWORK / SQL                 | **Acesso aos pontos de extremidade do SQL do Azure**                           | Interno e externo  |
| */443                     | Saída           | TCP                | VIRTUAL_NETWORK/AzureKeyVault                 | **Acesso ao cofre de chaves do Azure**                           | Interno e externo  |
| * / 5671, 5672, 443          | Saída           | TCP                | VIRTUAL_NETWORK / EventHub            | Dependência para registrar em [log a política de Hub de Eventos](api-management-howto-log-event-hubs.md) e o agente de monitoramento | Interno e externo  |
| * / 445                      | Saída           | TCP                | VIRTUAL_NETWORK / Armazenamento             | Dependência do Compartilhamento de Arquivos do Azure para [GIT](api-management-configuration-repository-git.md)                      | Interno e externo  |
| */443, 12000                     | Saída           | TCP                | VIRTUAL_NETWORK/AzureCloud            | Extensão de integridade e monitoramento         | Interno e externo  |
| */1886, 443                     | Saída           | TCP                | VIRTUAL_NETWORK/AzureMonitor         | Publicar [logs e métricas de diagnóstico](api-management-howto-use-azure-monitor.md), [Resource Health](../service-health/resource-health-overview.md) e [Application insights](api-management-howto-app-insights.md)                   | Interno e externo  |
| */25, 587, 25028                       | Saída           | TCP                | VIRTUAL_NETWORK/INTERNET            | Conectar a retransmissão SMTP para enviar emails                    | Interno e externo  |
| * / 6381 - 6383              | Entrada e Saída | TCP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | Acessar o serviço Redis para políticas de [cache](api-management-caching-policies.md) entre computadores         | Interno e externo  |
| */4290              | Entrada e Saída | UDP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | Sincronizar contadores para políticas de [limite de taxa](api-management-access-restriction-policies.md#LimitCallRateByKey) entre computadores         | Interno e externo  |
| * / *                        | Entrada            | TCP                | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK | Balanceador de carga de infraestrutura do Azure                          | Interno e externo  |

>[!IMPORTANT]
> As Portas para as quais a *Finalidade* é **negrito** são necessárias para o serviço de Gerenciamento de API ser implantado com êxito. No entanto, bloquear as outras portas causará **degradação** na capacidade de usar e **monitorar o serviço em execução e fornecer o SLA confirmado**.

+ **Funcionalidade TLS**: Para habilitar a criação e validação de cadeia de certificados TLS/SSL o serviço Gerenciamento de API precisa de conectividade de rede de saída para ocsp.msocsp.com, mscrl.microsoft.com e crl.microsoft.com. Essa dependência não é necessária, se qualquer certificado que você carrega no Gerenciamento de API contém a cadeia completa para a raiz de CA.

+ **Acesso DNS**: O acesso de saída na porta 53 é necessário para a comunicação com servidores DNS. Se houver um servidor DNS personalizado na outra extremidade de um gateway de VPN, o servidor DNS deverá estar acessível pela sub-rede que hospeda o Gerenciamento de API.

+ **Monitoramento de integridade e métricas**: Conectividade de rede de saída para pontos de extremidade do Monitoramento do Azure, que são resolvidos sob os seguintes domínios. Conforme mostrado na tabela, essas URLs são representadas na marca de serviço AzureMonitor para uso com grupos de segurança de rede.

    | Azure Environment | Pontos de extremidade                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Público do Azure      | <ul><li>gcs.prod.monitoring.core.windows.net(**novo**)</li><li>prod.warmpath.msftcloudes.com(**será preterido**)</li><li>global.prod.microsoftmetrics.com(**novo**)</li><li>global.metrics.nsatc.net(**será preterido**)</li><li>shoebox2.prod.microsoftmetrics.com(**novo**)</li><li>shoebox2.metrics.nsatc.net(**será preterido**)</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com(**novo**)</li><li>prod3.metrics.nsatc.net(**será preterido**)</li><li>prod3-black.prod.microsoftmetrics.com(**novo**)</li><li>prod3-black.prod3.metrics.nsatc.net(**será preterido**)</li><li>prod3-red.prod.microsoftmetrics.com(**novo**)</li><li>prod3-red.prod3.metrics.nsatc.net(**será preterido**)</li><li>gcs.prod.warm.ingestion.monitoring.azure.com</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>global.prod.microsoftmetrics.com(**novo**)</li><li>global.metrics.nsatc.net(**será preterido**)</li><li>shoebox2.prod.microsoftmetrics.com(**novo**)</li><li>shoebox2.metrics.nsatc.net(**será preterido**)</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com(**novo**)</li><li>prod3.metrics.nsatc.net(**será preterido**)</li><li>prod3-black.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.us</li></ul>                                                                                                                                                                                                                                                |
    | Azure China 21Vianet     | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>global.prod.microsoftmetrics.com(**novo**)</li><li>global.metrics.nsatc.net(**será preterido**)</li><li>shoebox2.prod.microsoftmetrics.com(**novo**)</li><li>shoebox2.metrics.nsatc.net(**será preterido**)</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com(**novo**)</li><li>prod3.metrics.nsatc.net(**será preterido**)</li><li>prod3-black.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.cn</li></ul>                                                                                                                                                                                                                                                |

  >[!IMPORTANT]
  > A alteração de clusters acima com a zona DNS **. nsatc.net** como **. microsoftmetrics.com** é a maior parte de uma alteração de DNS. O endereço IP do cluster não será alterado.

+ **Tags de Serviço Regional**: As regras de NSG que permitem a conectividade de saída para as marcas de serviço de armazenamento, SQL e hubs de eventos podem usar as versões regionais dessas marcas correspondentes à região que contém a instância de gerenciamento de API (por exemplo, Storage. Westus para uma instância de gerenciamento de API na região oeste dos EUA). Em implantações de várias regiões, o NSG em cada região deve permitir o tráfego para as marcas de serviço para essa região e a região primária.

    > [!IMPORTANT]
    > Para habilitar a publicação do [portal do desenvolvedor](api-management-howto-developer-portal.md) para uma instância de gerenciamento de API em uma rede virtual, verifique se você também permite a conectividade de saída com o armazenamento de BLOBs na região oeste dos EUA. Por exemplo, use a marca de serviço **Storage. westus** em uma regra NSG. Atualmente, a conectividade com o armazenamento de BLOBs na região oeste dos EUA é necessária para publicar o portal do desenvolvedor para qualquer instância de gerenciamento de API.

+ **Retransmissão de SMTP**: Conectividade de rede de saída para a retransmissão de SMTP, que resolve sob o host `smtpi-co1.msn.com`, `smtpi-ch1.msn.com`, `smtpi-db3.msn.com`, `smtpi-sin.msn.com` e `ies.global.microsoft.com`

+ **CAPTCHA do portal do desenvolvedor**: Conectividade de rede de saída para o CAPTCHA do portal do desenvolvedor, que é resolvido sob os hosts `client.hip.live.com` e `partner.hip.live.com`.

+ **Diagnósticos do portal do Azure**: Para habilitar o fluxo de log de diagnóstico do portal do Azure ao usar a extensão de Gerenciamento de API de dentro de uma Rede Virtual, é necessário ter acesso de saída a `dc.services.visualstudio.com` na porta 443. Isso ajuda na solução de problemas que você pode enfrentar ao usar a extensão.

+ **Azure Load Balancer**: Permitir a solicitação de entrada da marca de serviço `AZURE_LOAD_BALANCER` não é um requisito para a SKU `Developer`, já que implantamos apenas uma unidade de computação por trás dela. Mas a entrada de [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) se torna crítica ao Dimensionar para uma SKU superior como `Premium`, como falha da investigação de integridade do Load Balancer, falha em uma implantação.

+ **Application insights**: se o monitoramento do [aplicativo Azure insights](api-management-howto-app-insights.md) estiver habilitado no gerenciamento de API, será necessário permitir a conectividade de saída para o [ponto de extremidade de telemetria](../azure-monitor/app/ip-addresses.md#outgoing-ports) da rede virtual. 

+ **Forçar o túnel de tráfego para o firewall local usando a rota expressa ou a solução de virtualização de rede**: Uma configuração de cliente comum é definir sua própria rota padrão (0.0.0.0/0), o que força todo o tráfego da sub-rede delegada do gerenciamento de API a fluir por um firewall local ou para uma solução de virtualização de rede. Esse fluxo de tráfego invariavelmente interrompe a conectividade com o Gerenciamento de API do Azure, pois o tráfego de saída é bloqueado localmente ou convertido em NAT para um conjunto irreconhecível de endereços que não funcionam mais com vários pontos de extremidade do Azure. A solução exige que você faça algumas coisas:

  * Habilite os pontos de extremidade de serviço na sub-rede em que o serviço de gerenciamento de API está implantado. [Os pontos de extremidade de serviço][ServiceEndpoints] precisam ser habilitados para SQL do Azure, armazenamento do Azure, EventHub do Azure e ServiceBus do Azure. Habilitar pontos de extremidade diretamente da sub-rede delegada do gerenciamento de API para esses serviços permite que eles usem o Microsoft Azure rede de backbone fornecendo roteamento ideal para o tráfego de serviço. Se você usar pontos de extremidade de serviço com um gerenciamento de APIs encapsulado forçado, o tráfego de serviços do Azure acima não será encapsulado. O outro tráfego de dependência do serviço de Gerenciamento de API é forçado em túnel e não pode ser perdido ou o serviço de Gerenciamento de API não funcionaria corretamente.
    
  * Todo o tráfego do plano de controle da Internet para o ponto de extremidade de gerenciamento do serviço de gerenciamento de API é roteado por meio de um conjunto específico de IPs de entrada hospedados pelo gerenciamento de API. Quando o tráfego é forçado por túnel, as respostas não são mapeadas de volta de forma simétrica para esses IPs de origem de entrada. Para superar a limitação, precisamos adicionar as seguintes rotas definidas pelo usuário ([UDRs][UDRs]) para direcionar o tráfego de volta para o Azure definindo o destino dessas rotas de host como "Internet". O conjunto de IPs de entrada para o tráfego do plano de controle é documentado [endereços IP do plano de controle](#control-plane-ips)

  * Para outras dependências de serviço de gerenciamento de API que são disparadas em túnel, deve haver uma maneira de resolver o nome de host e entrar em contato com o ponto de extremidade. Eles incluem:
      - Monitoramento de integridade e métricas
      - Diagnósticos do portal do Azure
      - Retransmissão de SMTP
      - CAPTCHA do portal do desenvolvedor

## <a name="troubleshooting"></a><a name="troubleshooting"> </a>Solução de problemas
* **Configuração inicial**: Quando a implantação inicial do serviço de Gerenciamento de API em uma sub-rede não for bem-sucedida, é recomendável primeiro implantar uma máquina virtual na mesma sub-rede. Na próxima área de trabalho remota na máquina virtual e valide se há conectividade com um de cada recurso abaixo em sua assinatura do Azure
    * Azure Storage Blob
    * Banco de Dados SQL do Azure
    * Tabela de armazenamento do Azure

  > [!IMPORTANT]
  > Após validar a conectividade, certifique-se de remover todos os recursos implantados na sub-rede antes de implantar o Gerenciamento de API na sub-rede.

* **Verificar o status de conectividade de rede**: depois de implantar o gerenciamento de API na sub-rede, use o portal para verificar a conectividade de sua instância para dependências como o armazenamento do Azure. No portal, no menu à esquerda, em **implantação e infraestrutura**, selecione **status de conectividade de rede**.

   :::image type="content" source="media/api-management-using-with-vnet/verify-network-connectivity-status.png" alt-text="Verificar o status de conectividade de rede no portal":::

    * Selecione **necessário** para examinar a conectividade com os serviços do Azure necessários para o gerenciamento de API. Uma falha indica que a instância não pode executar operações de núcleo para gerenciar APIs.
    * Selecione **opcional** para examinar a conectividade com os serviços opcionais. Qualquer falha indica apenas que a funcionalidade específica não funcionará (por exemplo, SMTP). Uma falha pode levar à degradação na capacidade de usar e monitorar a instância de gerenciamento de API e fornecer o SLA confirmado.

Para resolver problemas de conectividade, examine [problemas comuns de configuração de rede](#network-configuration-issues) e corrija as configurações de rede necessárias.

* **Atualizações incrementais**: Ao fazer alterações em sua rede, consulte [API NetworkStatus](/rest/api/apimanagement/2019-12-01/networkstatus) para verificar se o serviço de Gerenciamento de API não perdeu o acesso a qualquer um dos recursos críticos dos quais ele depende. O status de conectividade deve ser atualizado a cada 15 minutos.

* **Links de Navegação do Recurso**: Ao implantar na sub-rede da VNET do estilo Resource Manager, o Gerenciamento de API reserva a sub-rede criando um Link de navegação do recurso. Se a sub-rede já contiver um recurso de outro provedor, a implantação **falhará**. Da mesma forma, quando você move um serviço de Gerenciamento de API para uma sub-rede diferente ou o exclui, removeremos esse link de navegação do recurso.

## <a name="subnet-size-requirement"></a><a name="subnet-size"> </a> Requisito de tamanho de sub-rede
O Azure reserva alguns endereços IP em cada sub-rede, os quais não podem ser usados. O primeiro e o último endereço IP das sub-redes são reservados para fins de conformidade de protocolo, juntamente com três outros endereços usados para os serviços do Azure. Para obter mais informações, consulte [Existem restrições quanto ao uso de endereços IP dentro dessas sub-redes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Além dos endereços IP usados pela infraestrutura de rede virtual do Azure, cada instância do Gerenciamento de API da sub-rede usa dois endereços IP por unidade de SKU Premium ou um endereço IP para a SKU do desenvolvedor. Cada instância de reserva um endereço IP adicional para o balanceador externo de carga. Ao implantar na rede virtual interna, é necessário um endereço IP adicional para o balanceador de carga interno.

Dado o cálculo acima do tamanho mínimo da sub-rede, no qual o Gerenciamento de API pode ser implantado é/29 que fornece três endereços IP utilizáveis.

Cada unidade de escala adicional do gerenciamento de API requer mais dois endereços IP.

## <a name="routing"></a><a name="routing"> </a> Roteamento
+ Um endereço IP público com balanceamento de carga (VIP) será reservado para fornecer acesso a todos os pontos de extremidade de serviço.
+ Um endereço IP de um intervalo IP de sub-rede (DIP) será usado para acessar recursos na VNET e um VIP (endereço IP público) será usado para acessar recursos fora da VNET.
+ O endereço IP público com carga balanceada podem ser encontrados na folha Visão Geral/Essentials no portal do Azure.

## <a name="limitations"></a><a name="limitations"> </a>Limitações
* Uma sub-rede que contém instâncias de Gerenciamento de API não pode conter outros tipos de recursos do Azure.
* A sub-rede e o serviço de Gerenciamento de API devem estar na mesma assinatura.
* Uma sub-rede que contém instâncias de Gerenciamento de API não pode ser movida entre assinaturas.
* Para implantações de Gerenciamento de API de várias regiões configuradas no modo de rede virtual interna, os usuários são responsáveis por gerenciar o balanceamento de carga em várias regiões, uma vez que eles detêm o roteamento.
* Conectividade de um recurso em uma rede virtual emparelhada globalmente em outra região para o serviço de gerenciamento de API no modo interno não funcione devido à limitação de plataforma. Para obter mais informações, consulte [Recursos em uma rede virtual não podem se comunicar com o endereço IP de um balanceador de carga interno do Azure na rede virtual emparelhada](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)

## <a name="control-plane-ip-addresses"></a><a name="control-plane-ips"> </a> Controlar endereços IP planos

Os endereços IP são divididos por **ambiente do Azure**. Ao permitir que as solicitações de entrada, o endereço IP marcado com **global** deve ser permitido junto com o endereço IP específico da **região** .

| **Azure Environment**|   **Região**|  **Endereço IP**|
|-----------------|-------------------------|---------------|
| Público do Azure| Centro-Sul dos EUA (Global)| 104.214.19.224|
| Público do Azure| Centro-Norte dos EUA (Global)| 52.162.110.80|
| Público do Azure| Centro-Oeste dos EUA| 52.253.135.58|
| Público do Azure| Coreia Central| 40.82.157.167|
| Público do Azure| Oeste do Reino Unido| 51.137.136.0|
| Público do Azure| Oeste do Japão| 40.81.185.8|
| Público do Azure| Centro-Norte dos EUA| 40.81.47.216|
| Público do Azure| Sul do Reino Unido| 51.145.56.125|
| Público do Azure| Oeste da Índia| 40.81.89.24|
| Público do Azure| Leste dos EUA| 52.224.186.99|
| Público do Azure| Europa Ocidental| 51.145.179.78|
| Público do Azure| Leste do Japão| 52.140.238.179|
| Público do Azure| França Central| 40.66.60.111|
| Público do Azure| Leste do Canadá| 52.139.80.117|
| Público do Azure| Norte dos EAU| 20.46.144.85|
| Público do Azure| Sul do Brasil| 191.233.24.179|
| Público do Azure| Sudeste do Brasil| 191.232.18.181|
| Público do Azure| Sudeste Asiático| 40.90.185.46|
| Público do Azure| Norte da África do Sul| 102.133.130.197|
| Público do Azure| Canadá Central| 52.139.20.34|
| Público do Azure| Sul da Coreia| 40.80.232.185|
| Público do Azure| Índia Central| 13.71.49.1|
| Público do Azure| Oeste dos EUA| 13.64.39.16|
| Público do Azure| Sudeste da Austrália| 20.40.160.107|
| Público do Azure| Austrália Central| 20.37.52.67|
| Público do Azure| Sul da Índia| 20.44.33.246|
| Público do Azure| Centro dos EUA| 13.86.102.66|
| Público do Azure| Leste da Austrália| 20.40.125.155|
| Público do Azure| Oeste dos EUA 2| 51.143.127.203|
| Público do Azure| Leste dos EUA 2 EUAP| 52.253.229.253|
| Público do Azure| EUA Central EUAP| 52.253.159.160|
| Público do Azure| Centro-Sul dos Estados Unidos| 20.188.77.119|
| Público do Azure| Leste dos EUA 2| 20.44.72.3|
| Público do Azure| Norte da Europa| 52.142.95.35|
| Público do Azure| Leste da Ásia| 52.139.152.27|
| Público do Azure| Sul da França| 20.39.80.2|
| Público do Azure| Oeste da Suíça| 51.107.96.8|
| Público do Azure| Austrália Central 2| 20.39.99.81|
| Público do Azure| EAU Central| 20.37.81.41|
| Público do Azure| Norte da Suíça| 51.107.0.91|
| Público do Azure| Oeste da África do Sul| 102.133.0.79|
| Público do Azure| Centro-oeste da Alemanha| 51.116.96.0|
| Público do Azure| Norte da Alemanha| 51.116.0.0|
| Público do Azure| Leste da Noruega| 51.120.2.185|
| Público do Azure| Oeste da Noruega| 51.120.130.134|
| Azure China 21Vianet| Norte da China (Global)| 139.217.51.16|
| Azure China 21Vianet| Leste da China (Global)| 139.217.171.176|
| Azure China 21Vianet| Norte da China| 40.125.137.220|
| Azure China 21Vianet| Leste da China| 40.126.120.30|
| Azure China 21Vianet| Norte da China 2| 40.73.41.178|
| Azure China 21Vianet| Leste da China 2| 40.73.104.4|
| Azure Government| USGov Virginia (Global)| 52.127.42.160|
| Azure Government| USGov Texas (Global)| 52.127.34.192|
| Azure Government| USGov Virginia| 52.227.222.92|
| Azure Government| USGov Iowa| 13.73.72.21|
| Azure Government| Gov. dos EUA – Arizona| 52.244.32.39|
| Azure Government| Gov. dos EUA – Texas| 52.243.154.118|
| Azure Government| USDoD Central| 52.182.32.132|
| Azure Government| Leste do USDoD| 52.181.32.192|

## <a name="related-content"></a><a name="related-content"> </a>Conteúdo relacionado
* [Conectando uma rede virtual ao back-end usando o Gateway de VPN](../vpn-gateway/design.md#s2smulti)
* [Conectar uma rede virtual de diferentes modelos de implantação](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Como usar o Inspetor de API para rastrear chamadas no Gerenciamento de API do Azure](api-management-howto-api-inspector.md)
* [Perguntas frequentes sobre rede virtual](../virtual-network/virtual-networks-faq.md)
* [Marcas de serviço](../virtual-network/network-security-groups-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/network-security-groups-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags
