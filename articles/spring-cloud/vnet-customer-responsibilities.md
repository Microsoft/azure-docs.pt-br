---
title: Responsabilidades do cliente executando o Azure Spring Cloud na vnet
description: Este artigo descreve as responsabilidades do cliente que executam o Azure Spring Cloud na vnet.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 32b41c1c4446ba34e3bfad52f1d3cbd7ed72096d
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105108802"
---
# <a name="customer-responsibilities-for-running-azure-spring-cloud-in-vnet"></a>Responsabilidades do cliente para executar o Azure Spring Cloud na VNET
Este documento inclui especificações para o uso do Azure Spring Cloud em uma rede virtual.

Quando o Azure Spring Cloud é implantado em sua rede virtual, ele tem dependências de saída em serviços fora da rede virtual. Para fins operacionais e de gerenciamento, o Azure Spring Cloud deve acessar determinadas portas e FQDNs (nomes de domínio totalmente qualificados). Esses pontos de extremidade são necessários para se comunicar com o plano de gerenciamento de nuvem Spring do Azure e para baixar e instalar os principais componentes do cluster kubernetes e atualizações de segurança.

Por padrão, o Azure Spring Cloud tem acesso irrestrito à Internet (saída). Esse nível de acesso à rede permite que os aplicativos que você executa acessem recursos externos, conforme necessário. Se você quiser restringir o tráfego de saída, um número limitado de portas e endereços deve ser acessível para tarefas de manutenção. A solução mais simples para proteger endereços de saída é o uso de um dispositivo de firewall que pode controlar o tráfego de saída com base em nomes de domínio. O Firewall do Azure, por exemplo, pode restringir o tráfego HTTP e HTTPS de saída com base no FQDN do destino. Você também pode configurar o firewall e as regras de segurança preferenciais para permitir essas portas e endereços necessários.

## <a name="azure-spring-cloud-resource-requirements"></a>Requisitos de recursos de nuvem do Azure Spring 

Veja a seguir uma lista de requisitos de recursos para os serviços de nuvem do Azure Spring. Como um requisito geral, você não deve modificar os grupos de recursos criados pelo Azure Spring Cloud e os recursos de rede subjacentes.
- Não modifique grupos de recursos criados e pertencentes ao Azure Spring Cloud.
  - Por padrão, esses grupos de recursos são nomeados como AP-svc-rt_ [SERVICE-INSTANCE-NAME]_[Região] * e AP_[nome-da-instância-do-serviço] _ [Região] *.
- Não modifique as sub-redes usadas pelo Azure Spring Cloud.
- Não crie mais de uma instância do serviço de nuvem do Azure Spring na mesma sub-rede.
- Ao usar um firewall para controlar o tráfego, *não* bloqueie o tráfego de saída a seguir para os componentes do Azure Spring Cloud que operam, mantêm e dão suporte à instância do serviço.

## <a name="azure-spring-cloud-network-requirements"></a>Requisitos de rede da nuvem do Azure Spring

  | Ponto de extremidade de destino | Porta | Use | Observação |
  |------|------|------|
  | *: 1194 *ou* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -AzureCloud: 1194 | UDP: 1194 | Gerenciamento de cluster kubernetes subjacente. | |
  | *: 443 *ou* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -AzureCloud: 443 | TCP: 443 | Gerenciamento de serviço de nuvem do Azure Spring. | As informações da instância de serviço "requiredTraffics" podem ser conhecidas na carga do recurso, na seção "networkProfile". |
  | *: 9000 *ou* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -AzureCloud: 9000 | TCP: 9000 | Gerenciamento de cluster kubernetes subjacente. |
  | *: 123 *ou* NTP.Ubuntu.com:123 | UDP:123 | Sincronização de tempo de NTP em nós do Linux. | |
  | *. azure.io:443 *ou* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -AzureContainerRegistry: 443 | TCP: 443 | Registro de Contêiner do Azure. | Pode ser substituído habilitando o [ponto de extremidade de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) *do registro de contêiner do Azure* na rede virtual. |
  | *. core.windows.net:443 e *. core.windows.net:445 *ou* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -Storage: 443 e armazenamento: 445 | TCP: 443, TCP: 445 | Armazenamento de Arquivos do Azure | Pode ser substituído habilitando o [ponto de extremidade do serviço de](../virtual-network/virtual-network-service-endpoints-overview.md) *armazenamento do Azure* na rede virtual. |
  | *. servicebus.windows.net:443 *ou* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -EventHub: 443 | TCP: 443 | Hub de eventos do Azure. | Pode ser substituído habilitando o [ponto de extremidade de serviço](../virtual-network/virtual-network-service-endpoints-overview.md)dos *hubs de eventos do Azure* na rede virtual. |
  

## <a name="azure-spring-cloud-fqdn-requirements--application-rules"></a>Requisitos de FQDN/regras de aplicativo do Azure Spring Cloud

O Firewall do Azure fornece uma marca de nome de domínio totalmente qualificado (FQDN) **AzureKubernetesService** para simplificar as configurações a seguir.

  | FQDN de destino | Porta | Use |
  |------|------|------|
  | *. azmk8s.io | HTTPS:443 | Gerenciamento de cluster kubernetes subjacente. |
  | <i>mcr.microsoft.com</i> | HTTPS:443 | Registro de contêiner da Microsoft (MCR). |
  | *.cdn.mscr.io | HTTPS:443 | Armazenamento MCR apoiado pela CDN do Azure. |
  | *.data.mcr.microsoft.com | HTTPS:443 | Armazenamento MCR apoiado pela CDN do Azure. |
  | <i>management.azure.com</i> | HTTPS:443 | Gerenciamento de cluster kubernetes subjacente. |
  | <i>* login.microsoftonline.com</i> | HTTPS:443 | Autenticação Azure Active Directory. |
  | <i>* login.microsoft.com</i> | HTTPS:443 | Autenticação Azure Active Directory. |
  |<i>packages.microsoft.com</i>    | HTTPS:443 | Repositório de pacotes da Microsoft. |
  | <i>acs-mirror.azureedge.net</i> | HTTPS:443 | Repositório necessário para instalar os binários necessários, como kubenet e Azure CNI. |
  | *mscrl.microsoft.com* | HTTPS: 80 | Caminhos da cadeia de certificados da Microsoft necessários. |
  | *crl.microsoft.com* | HTTPS: 80 | Caminhos da cadeia de certificados da Microsoft necessários. |
  | *crl3.digicert.com* | HTTPS: 80 | Caminhos de cadeia de certificados SSL de terceiros. |

## <a name="see-also"></a>Confira também
* [Acessar seu aplicativo em uma rede privada](access-app-virtual-network.md)
* [Expor aplicativos usando o gateway de aplicativo e o Firewall do Azure](expose-apps-gateway-azure-firewall.md)