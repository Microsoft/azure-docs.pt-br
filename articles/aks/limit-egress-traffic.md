---
title: Restringir o tráfego de saída no Serviço de Kubernetes do Azure (AKS)
description: Saiba quais portas e endereços são necessários para controlar o tráfego de saída no Serviço de Kubernetes do Azure (AKS)
services: container-service
ms.topic: article
ms.author: jpalma
ms.date: 01/12/2021
author: palma21
ms.openlocfilehash: 9e65e2736578ce04dfa79d5a7827e190d47fb312
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103573822"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>Controlar o tráfego de saída dos nós de cluster no Serviço de Kubernetes do Azure (AKS)

Este artigo fornece os detalhes necessários que permitem proteger o tráfego de saída do seu AKS (serviço kubernetes do Azure). Ele contém os requisitos de cluster para uma implantação de AKS de base e requisitos adicionais para Complementos e recursos opcionais. [Um exemplo será fornecido no final de como configurar esses requisitos com o Firewall do Azure](#restrict-egress-traffic-using-azure-firewall). No entanto, você pode aplicar essas informações a qualquer método ou dispositivo de restrição de saída.

## <a name="background"></a>Tela de fundo

Os clusters AKS são implantados em uma rede virtual. Essa rede pode ser gerenciada (criada por AKS) ou personalizada (previamente configurada pelo usuário com antecedência). Em ambos os casos, o cluster tem dependências de **saída** em serviços fora dessa rede virtual (o serviço não tem dependências de entrada).

Para fins operacionais e de gerenciamento, os nós em um cluster do AKS precisam acessar portas e nomes de domínio totalmente qualificados (FQDNs) específicos. Esses pontos de extremidade são necessários para que os nós se comuniquem com o servidor de API, ou para baixar e instalar componentes principais do cluster kubernetes e atualizações de segurança do nó. Por exemplo, o cluster precisa extrair imagens de contêiner do sistema base do registro de contêiner da Microsoft (MCR).

As dependências de saída do AKS são quase totalmente definidas com FQDNs, que não têm endereços estáticos por trás delas. A falta de endereços estáticos significa que os grupos de segurança de rede não podem ser usados para bloquear o tráfego de saída de um cluster AKS. 

Por padrão, os clusters do AKS têm acesso irrestrito de internet de saída. Esse nível de acesso à rede permite que os nós e os serviços que você executa acessem recursos externos, conforme necessário. Se quiser restringir o tráfego de saída, um número limitado de portas e endereços precisar estar acessível para manter a integridade das tarefas de manutenção de cluster. A solução mais simples para proteger endereços de saída está em uso de um dispositivo de firewall que pode controlar o tráfego de saída com base em nomes de domínio. O Firewall do Azure, por exemplo, pode restringir o tráfego HTTP e HTTPS de saída com base no FQDN do destino. Você também pode configurar o firewall e as regras de segurança preferenciais para permitir essas portas e endereços necessários.

> [!IMPORTANT]
> Este documento aborda apenas como bloquear o tráfego de saída da sub-rede do AKS. O AKS não tem requisitos de entrada por padrão.  Não há suporte para o bloqueio de **tráfego de sub-rede interna** usando NSGs (grupos de segurança de rede) e firewalls. Para controlar e bloquear o tráfego no cluster, use [**_as políticas de rede_**][network-policy].

## <a name="required-outbound-network-rules-and-fqdns-for-aks-clusters"></a>Regras de rede de saída necessárias e FQDNs para clusters AKS

As regras de rede e FQDN/aplicativo a seguir são necessárias para um cluster AKS, você poderá usá-las se desejar configurar uma solução diferente do firewall do Azure.

* As dependências de Endereço IP são para o tráfego não HTTP/S (tráfego TCP e UDP)
* Pontos de extremidade HTTP/HTTPS do FQDN podem ser colocados em seu dispositivo de firewall.
* Os pontos de extremidade HTTP/HTTPS curinga são dependências que podem variar com o cluster AKS com base em vários qualificadores.
* O AKS usa um controlador de admissão para injetar o FQDN como uma variável de ambiente para todas as implantações em Kube-System e gatekeeper-System, que garante que toda a comunicação do sistema entre nós e o servidor de API use o FQDN do servidor de API e não o IP do servidor de API. 
* Se você tiver um aplicativo ou uma solução que precise se comunicar com o servidor de API, deverá adicionar uma regra de rede **adicional** para permitir a *comunicação TCP com a porta 443 do IP do seu servidor de API*.
* Em raras ocasiões, se houver uma operação de manutenção, o IP do servidor de API poderá ser alterado. As operações de manutenção planejadas que podem alterar o IP do servidor de API sempre são comunicadas antecipadamente.


### <a name="azure-global-required-network-rules"></a>Regras de rede necessárias globais do Azure

As regras de rede e as dependências de endereço IP necessárias são:

| Ponto de extremidade de destino                                                             | Protocolo | Porta    | Use  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *Or* <br/> [CIDRs regionais](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Or* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | Para uma comunicação segura em túnel entre os nós e o plano de controle. Isso não é necessário para [clusters privados](private-clusters.md)|
| **`*:9000`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Or* <br/> [CIDRs regionais](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Or* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | Para uma comunicação segura em túnel entre os nós e o plano de controle. Isso não é necessário para [clusters privados](private-clusters.md) |
| **`*:123`** ou **`ntp.ubuntu.com:123`** (se estiver usando regras de rede do firewall do Azure)  | UDP      | 123     | Necessário para sincronização de tempo de protocolo NTP (NTP) em nós do Linux.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Se você estiver usando servidores DNS personalizados, deverá garantir que eles estejam acessíveis pelos nós do cluster. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Necessário se estiver executando pods/implantações que acessam o servidor de API, esses pods/implantações usarão o IP da API. Isso não é necessário para [clusters privados](private-clusters.md)  |

### <a name="azure-global-required-fqdn--application-rules"></a>Regras de FQDN/aplicativo necessárias globais do Azure 

As seguintes regras de FQDN/aplicativo são obrigatórias:

| FQDN de destino                 | Porta            | Use      |
|----------------------------------|-----------------|----------|
| **`*.hcp.<location>.azmk8s.io`** | **`HTTPS:443`** | Necessário para a comunicação de servidor de API de > de < de nó. Substitua *\<location\>* pela região em que o cluster AKs está implantado. |
| **`mcr.microsoft.com`**          | **`HTTPS:443`** | Necessário para acessar imagens no registro de contêiner da Microsoft (MCR). Esse registro contém gráficos/imagens de terceiros (por exemplo, coreDNS, etc.). Essas imagens são necessárias para a criação e o funcionamento corretos do cluster, incluindo operações de escala e atualização.  |
| **`*.data.mcr.microsoft.com`**   | **`HTTPS:443`** | Necessário para o armazenamento MCR apoiado pela CDN (rede de distribuição de conteúdo) do Azure. |
| **`management.azure.com`**       | **`HTTPS:443`** | Necessário para operações de kubernetes na API do Azure. |
| **`login.microsoftonline.com`**  | **`HTTPS:443`** | Necessário para autenticação de Azure Active Directory. |
| **`packages.microsoft.com`**     | **`HTTPS:443`** | Esse endereço é o repositório de pacotes da Microsoft usado para as operações *app-get*.  Os pacotes de exemplo incluem Moby, PowerShell e CLI do Azure. |
| **`acs-mirror.azureedge.net`**   | **`HTTPS:443`** | Esse endereço é para o repositório necessário para baixar e instalar os binários necessários, como kubenet e Azure CNI. |

### <a name="azure-china-21vianet-required-network-rules"></a>Regras de rede necessárias do Azure China 21Vianet

As regras de rede e as dependências de endereço IP necessárias são:

| Ponto de extremidade de destino                                                             | Protocolo | Porta    | Use  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.Region:1194`** <br/> *Or* <br/> [CIDRs regionais](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Or* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | Para uma comunicação segura em túnel entre os nós e o plano de controle. |
| **`*:9000`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Or* <br/> [CIDRs regionais](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Or* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | Para uma comunicação segura em túnel entre os nós e o plano de controle. |
| **`*:22`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:22`** <br/> *Or* <br/> [CIDRs regionais](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:22`** <br/> *Or* <br/> **`APIServerPublicIP:22`** `(only known after cluster creation)`  | TCP           | 22      | Para uma comunicação segura em túnel entre os nós e o plano de controle. |
| **`*:123`** ou **`ntp.ubuntu.com:123`** (se estiver usando regras de rede do firewall do Azure)  | UDP      | 123     | Necessário para sincronização de tempo de protocolo NTP (NTP) em nós do Linux.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Se você estiver usando servidores DNS personalizados, deverá garantir que eles estejam acessíveis pelos nós do cluster. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Necessário se estiver executando pods/implantações que acessam o servidor de API, esses Pod/implantações usarão o IP da API.  |

### <a name="azure-china-21vianet-required-fqdn--application-rules"></a>Regras de FQDN/aplicativo necessárias do Azure China 21Vianet

As seguintes regras de FQDN/aplicativo são obrigatórias:

| FQDN de destino                               | Porta            | Use      |
|------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | Necessário para a comunicação de servidor de API de > de < de nó. Substitua *\<location\>* pela região em que o cluster AKs está implantado. |
| **`*.tun.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | Necessário para a comunicação de servidor de API de > de < de nó. Substitua *\<location\>* pela região em que o cluster AKs está implantado. |
| **`mcr.microsoft.com`**                        | **`HTTPS:443`** | Necessário para acessar imagens no registro de contêiner da Microsoft (MCR). Esse registro contém gráficos/imagens de terceiros (por exemplo, coreDNS, etc.). Essas imagens são necessárias para a criação e o funcionamento corretos do cluster, incluindo operações de escala e atualização. |
| **`.data.mcr.microsoft.com`**                  | **`HTTPS:443`** | Necessário para o armazenamento MCR apoiado pela CDN (rede de distribuição de conteúdo) do Azure. |
| **`management.chinacloudapi.cn`**              | **`HTTPS:443`** | Necessário para operações de kubernetes na API do Azure. |
| **`login.chinacloudapi.cn`**                   | **`HTTPS:443`** | Necessário para autenticação de Azure Active Directory. |
| **`packages.microsoft.com`**                   | **`HTTPS:443`** | Esse endereço é o repositório de pacotes da Microsoft usado para as operações *app-get*.  Os pacotes de exemplo incluem Moby, PowerShell e CLI do Azure. |
| **`*.azk8s.cn`**                               | **`HTTPS:443`** | Esse endereço é para o repositório necessário para baixar e instalar os binários necessários, como kubenet e Azure CNI. |

### <a name="azure-us-government-required-network-rules"></a>Regras de rede necessárias para o governo dos EUA do Azure

As regras de rede e as dependências de endereço IP necessárias são:

| Ponto de extremidade de destino                                                             | Protocolo | Porta    | Use  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *Or* <br/> [CIDRs regionais](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Or* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | Para uma comunicação segura em túnel entre os nós e o plano de controle. |
| **`*:9000`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Or* <br/> [CIDRs regionais](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Or* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | Para uma comunicação segura em túnel entre os nós e o plano de controle. |
| **`*:123`** ou **`ntp.ubuntu.com:123`** (se estiver usando regras de rede do firewall do Azure)  | UDP      | 123     | Necessário para sincronização de tempo de protocolo NTP (NTP) em nós do Linux.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Se você estiver usando servidores DNS personalizados, deverá garantir que eles estejam acessíveis pelos nós do cluster. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Necessário se estiver executando pods/implantações que acessam o servidor de API, esses pods/implantações usarão o IP da API.  |

### <a name="azure-us-government-required-fqdn--application-rules"></a>Regras de FQDN/aplicativo necessárias do governo dos EUA do Azure 

As seguintes regras de FQDN/aplicativo são obrigatórias:

| FQDN de destino                                        | Porta            | Use      |
|---------------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.aks.containerservice.azure.us`** | **`HTTPS:443`** | Necessário para a comunicação de servidor de API de > de < de nó. Substitua *\<location\>* pela região em que o cluster AKs está implantado.|
| **`mcr.microsoft.com`**                                 | **`HTTPS:443`** | Necessário para acessar imagens no registro de contêiner da Microsoft (MCR). Esse registro contém gráficos/imagens de terceiros (por exemplo, coreDNS, etc.). Essas imagens são necessárias para a criação e o funcionamento corretos do cluster, incluindo operações de escala e atualização. |
| **`*.data.mcr.microsoft.com`**                          | **`HTTPS:443`** | Necessário para o armazenamento MCR apoiado pela CDN (rede de distribuição de conteúdo) do Azure. |
| **`management.usgovcloudapi.net`**                      | **`HTTPS:443`** | Necessário para operações de kubernetes na API do Azure. |
| **`login.microsoftonline.us`**                          | **`HTTPS:443`** | Necessário para autenticação de Azure Active Directory. |
| **`packages.microsoft.com`**                            | **`HTTPS:443`** | Esse endereço é o repositório de pacotes da Microsoft usado para as operações *app-get*.  Os pacotes de exemplo incluem Moby, PowerShell e CLI do Azure. |
| **`acs-mirror.azureedge.net`**                          | **`HTTPS:443`** | Esse endereço é para o repositório necessário para instalar os binários necessários, como kubenet e CNI do Azure. |

## <a name="optional-recommended-fqdn--application-rules-for-aks-clusters"></a>Regras de FQDN/aplicativo recomendadas opcionais para clusters AKS

As seguintes regras de FQDN/aplicativo são opcionais, mas recomendadas para clusters AKS:

| FQDN de destino                                                               | Porta          | Use      |
|--------------------------------------------------------------------------------|---------------|----------|
| **`security.ubuntu.com`, `azure.archive.ubuntu.com`, `changelogs.ubuntu.com`** | **`HTTP:80`** | Esse endereço permite que os nós de cluster do Linux baixem as atualizações e os patches de segurança necessários. |

Se você optar por bloquear/não permitir esses FQDNs, os nós só receberão atualizações do sistema operacional quando você fizer uma atualização de [imagem de nó](node-image-upgrade.md) ou [atualização de cluster](upgrade-cluster.md).

## <a name="gpu-enabled-aks-clusters"></a>Clusters AKS habilitados para GPU

### <a name="required-fqdn--application-rules"></a>Regras de FQDN/aplicativo necessárias

As seguintes regras de FQDN/aplicativo são necessárias para clusters do AKS com GPU habilitada:

| FQDN de destino                        | Porta      | Use      |
|-----------------------------------------|-----------|----------|
| **`nvidia.github.io`**                  | **`HTTPS:443`** | Esse endereço é usado para a instalação e a operação corretas do driver nos nós baseados em GPU. |
| **`us.download.nvidia.com`**            | **`HTTPS:443`** | Esse endereço é usado para a instalação e a operação corretas do driver nos nós baseados em GPU. |
| **`apt.dockerproject.org`**             | **`HTTPS:443`** | Esse endereço é usado para a instalação e a operação corretas do driver nos nós baseados em GPU. |

## <a name="windows-server-based-node-pools"></a>Pools de nós baseados no Windows Server 

### <a name="required-fqdn--application-rules"></a>Regras de FQDN/aplicativo necessárias

As seguintes regras de FQDN/aplicativo são necessárias para usar pools de nós baseados no Windows Server:

| FQDN de destino                                                           | Porta      | Use      |
|----------------------------------------------------------------------------|-----------|----------|
| **`onegetcdn.azureedge.net, go.microsoft.com`**                            | **`HTTPS:443`** | Para instalar binários relacionados ao Windows |
| **`*.mp.microsoft.com, www.msftconnecttest.com, ctldl.windowsupdate.com`** | **`HTTP:80`**   | Para instalar binários relacionados ao Windows |

## <a name="aks-addons-and-integrations"></a>Complementos e integrações do AKS

### <a name="azure-monitor-for-containers"></a>Azure Monitor para contêineres

Há duas opções para fornecer acesso a Azure Monitor para contêineres, você pode permitir que o Azure Monitor [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) **ou** fornecer acesso às regras de FQDN/aplicativo necessárias.

#### <a name="required-network-rules"></a>Regras de rede necessárias

As seguintes regras de FQDN/aplicativo são obrigatórias:

| Ponto de extremidade de destino                                                             | Protocolo | Porta    | Use  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureMonitor:443`**  | TCP           | 443      | Esse ponto de extremidade é usado para enviar logs e dados de métricas para Azure Monitor e Log Analytics. |

#### <a name="required-fqdn--application-rules"></a>Regras de FQDN/aplicativo necessárias

As seguintes regras de FQDN/aplicativo são necessárias para clusters do AKS que têm o Azure Monitor para contêineres habilitado:

| FQDN                                    | Porta      | Use      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | **`HTTPS:443`**    | Esse ponto de extremidade é usado para métricas e monitoramento de telemetria usando Azure Monitor. |
| *.ods.opinsights.azure.com    | **`HTTPS:443`**    | Esse ponto de extremidade é usado pelo Azure Monitor para ingerir dados do log Analytics. |
| *.oms.opinsights.azure.com | **`HTTPS:443`** | Esse ponto de extremidade é usado pelo omsagent, que é usado para autenticar o serviço do log Analytics. |
| *.monitoring.azure.com | **`HTTPS:443`** | Esse ponto de extremidade é usado para enviar dados de métricas para Azure Monitor. |

### <a name="azure-dev-spaces"></a>Azure Dev Spaces

Atualize o firewall ou a configuração de segurança para permitir o tráfego de rede de e para todos os FQDNs e [serviços de infraestrutura de Azure dev Spaces][dev-spaces-service-tags]a seguir.

#### <a name="required-network-rules"></a>Regras de rede necessárias

| Ponto de extremidade de destino                                                             | Protocolo | Porta    | Use  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureDevSpaces`**  | TCP           | 443      | Esse ponto de extremidade é usado para enviar logs e dados de métricas para Azure Monitor e Log Analytics. |

#### <a name="required-fqdn--application-rules"></a>Regras de FQDN/aplicativo necessárias 

As seguintes regras de FQDN/aplicativo são necessárias para clusters do AKS que têm o Azure Dev Spaces para contêineres habilitado:

| FQDN                                    | Porta      | Use      |
|-----------------------------------------|-----------|----------|
| `cloudflare.docker.com` | **`HTTPS:443`** | Este endereço é usado para extrair imagens do Linux Alpine e outras do Azure Dev Spaces |
| `gcr.io` | **`HTTPS:443`** | Esse endereço é usado para efetuar pull de imagens helm/tiller |
| `storage.googleapis.com` | **`HTTPS:443`** | Esse endereço é usado para efetuar pull de imagens helm/tiller |


### <a name="azure-policy"></a>Azure Policy

#### <a name="required-fqdn--application-rules"></a>Regras de FQDN/aplicativo necessárias 

As seguintes regras de FQDN/aplicativo são necessárias para clusters do AKS que têm o Azure Policy habilitado.

| FQDN                                          | Porta      | Use      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.core.windows.net`** | **`HTTPS:443`** | Esse endereço é usado para efetuar pull das políticas kubernetes e relatar o status de conformidade do cluster ao serviço de política. |
| **`store.policy.core.windows.net`** | **`HTTPS:443`** | Esse endereço é usado para extrair os artefatos do gatekeeper de políticas internas. |
| **`gov-prod-policy-data.trafficmanager.net`** | **`HTTPS:443`** | Esse endereço é usado para que o Azure Policy funcione corretamente.  |
| **`raw.githubusercontent.com`**               | **`HTTPS:443`** | Esse endereço é usado para efetuar pull das políticas internas do GitHub para garantir que o Azure Policy funcione corretamente. |
| **`dc.services.visualstudio.com`**            | **`HTTPS:443`** | Complemento do Azure Policy que envia dados telemétricos para pontos de extremidade de insights de aplicativo. |

#### <a name="azure-china-21vianet-required-fqdn--application-rules"></a>Regras de FQDN/aplicativo necessárias do Azure China 21Vianet 

As seguintes regras de FQDN/aplicativo são necessárias para clusters do AKS que têm o Azure Policy habilitado.

| FQDN                                          | Porta      | Use      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.azure.cn`** | **`HTTPS:443`** | Esse endereço é usado para efetuar pull das políticas kubernetes e relatar o status de conformidade do cluster ao serviço de política. |
| **`store.policy.azure.cn`** | **`HTTPS:443`** | Esse endereço é usado para extrair os artefatos do gatekeeper de políticas internas. |

#### <a name="azure-us-government-required-fqdn--application-rules"></a>Regras de FQDN/aplicativo necessárias do governo dos EUA do Azure

As seguintes regras de FQDN/aplicativo são necessárias para clusters do AKS que têm o Azure Policy habilitado.

| FQDN                                          | Porta      | Use      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.azure.us`** | **`HTTPS:443`** | Esse endereço é usado para efetuar pull das políticas kubernetes e relatar o status de conformidade do cluster ao serviço de política. |
| **`store.policy.azure.us`** | **`HTTPS:443`** | Esse endereço é usado para extrair os artefatos do gatekeeper de políticas internas. |

## <a name="restrict-egress-traffic-using-azure-firewall"></a>Restringir o tráfego de saída usando o Firewall do Azure

O Firewall do Azure fornece uma marca de FQDN do serviço kubernetes do Azure ( `AzureKubernetesService` ) para simplificar essa configuração. 

> [!NOTE]
> A marca FQDN contém todos os FQDNs listados acima e é mantida automaticamente atualizada.
>
> É recomendável ter um mínimo de 20 IPs de front-end no firewall do Azure para cenários de produção para evitar incorrer em problemas de esgotamento de porta SNAT.

Veja abaixo uma arquitetura de exemplo da implantação:

![Topologia bloqueada](media/limit-egress-traffic/aks-azure-firewall-egress.png)

* A entrada pública é forçada a fluir por filtros de firewall
  * Os nós de agente AKS são isolados em uma sub-rede dedicada.
  * O [Firewall do Azure](../firewall/overview.md) é implantado em sua própria sub-rede.
  * Uma regra DNAT converte o IP público do FW no IP de front-end LB.
* Solicitações de saída iniciam de nós de agente para o IP interno do firewall do Azure usando uma [rota definida pelo usuário](egress-outboundtype.md)
  * As solicitações de nós de agente do AKS seguem um UDR que foi colocado na sub-rede em que o cluster AKS foi implantado.
  * O Firewall do Azure sai da rede virtual de um front-end de IP público
  * Acesso à Internet pública ou outros fluxos de serviços do Azure fluem de e para o endereço IP de front-end do firewall
  * Opcionalmente, o acesso ao plano de controle AKS é protegido por [intervalos de IP autorizados do servidor de API](./api-server-authorized-ip-ranges.md), que inclui o endereço IP público de front-end do firewall.
* Tráfego interno
  * Opcionalmente, em vez disso ou além de um [Load Balancer público](load-balancer-standard.md) , você pode usar um [Load balancer interno](internal-lb.md) para tráfego interno, que também pode ser isolado em sua própria sub-rede.


As etapas a seguir usam a marca FQDN do firewall do Azure `AzureKubernetesService` para restringir o tráfego de saída do cluster AKs e fornecem um exemplo de como configurar o tráfego de entrada público por meio do firewall.


### <a name="set-configuration-via-environment-variables"></a>Definir configuração por meio de variáveis de ambiente

Defina um conjunto de variáveis de ambiente a ser usado em criações de recursos.

```bash
PREFIX="aks-egress"
RG="${PREFIX}-rg"
LOC="eastus"
PLUGIN=azure
AKSNAME="${PREFIX}"
VNET_NAME="${PREFIX}-vnet"
AKSSUBNET_NAME="aks-subnet"
# DO NOT CHANGE FWSUBNET_NAME - This is currently a requirement for Azure Firewall.
FWSUBNET_NAME="AzureFirewallSubnet"
FWNAME="${PREFIX}-fw"
FWPUBLICIP_NAME="${PREFIX}-fwpublicip"
FWIPCONFIG_NAME="${PREFIX}-fwconfig"
FWROUTE_TABLE_NAME="${PREFIX}-fwrt"
FWROUTE_NAME="${PREFIX}-fwrn"
FWROUTE_NAME_INTERNET="${PREFIX}-fwinternet"
```

### <a name="create-a-virtual-network-with-multiple-subnets"></a>Criar uma rede virtual com várias sub-redes

Provisione uma rede virtual com duas sub-redes separadas, uma para o cluster, uma para o firewall. Opcionalmente, você também pode criar um para entrada de serviço interno.

![Esvaziar a topologia de rede](media/limit-egress-traffic/empty-network.png)

Crie um grupo de recursos para armazenar todos os recursos.

```azurecli
# Create Resource Group

az group create --name $RG --location $LOC
```

Crie uma rede virtual com duas sub-redes para hospedar o cluster AKS e o Firewall do Azure. Cada uma terá sua sub-rede. Vamos começar com a rede do AKS.

```
# Dedicated virtual network with AKS subnet

az network vnet create \
    --resource-group $RG \
    --name $VNET_NAME \
    --location $LOC \
    --address-prefixes 10.42.0.0/16 \
    --subnet-name $AKSSUBNET_NAME \
    --subnet-prefix 10.42.1.0/24

# Dedicated subnet for Azure Firewall (Firewall name cannot be changed)

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $FWSUBNET_NAME \
    --address-prefix 10.42.2.0/24
```

### <a name="create-and-set-up-an-azure-firewall-with-a-udr"></a>Criar e configurar um firewall do Azure com um UDR

As regras de entrada e saída do Firewall do Azure devem ser configuradas. A principal finalidade do firewall é permitir que as organizações configurem as regras de entrada granulares e de tráfego de saída para dentro e fora do cluster AKS.

![Firewall e UDR](media/limit-egress-traffic/firewall-udr.png)


> [!IMPORTANT]
> Se o cluster ou aplicativo criar um grande número de conexões de saída direcionadas para o mesmo subconjunto de destinos ou pequenos, você poderá exigir mais IPs de front-end de firewall para evitar maximizandor as portas por IP de front-end.
> Para obter mais informações sobre como criar um firewall do Azure com vários IPs, consulte [ **aqui**](../firewall/quick-create-multiple-ip-template.md)

Crie um recurso IP público de SKU padrão que será usado como o endereço de front-end do firewall do Azure.

```azurecli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

Registre a extensão da CLI de versão prévia para criar um Firewall do Azure.
```azurecli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC --enable-dns-proxy true
```
O endereço IP criado anteriormente agora pode ser atribuído ao front-end do firewall.

> [!NOTE]
> A configuração do endereço IP público para o Firewall do Azure pode levar alguns minutos.
> Para aproveitar o FQDN nas regras de rede, precisamos do proxy de DNS habilitado, quando habilitado, o firewall escutará na porta 53 e encaminhará as solicitações de DNS para o servidor DNS especificado acima. Isso permitirá que o firewall converta esse FQDN automaticamente.

```azurecli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

Quando o comando anterior tiver sido bem-sucedido, salve o endereço IP de front-end do firewall para configuração posterior.

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

> [!NOTE]
> Se você usar o acesso seguro ao servidor de API do AKS com [intervalos de endereços IP autorizados](./api-server-authorized-ip-ranges.md), será necessário adicionar o IP público do firewall ao intervalo de IP autorizado.

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>Criar um UDR com um salto para o Firewall do Azure

O Azure roteia o tráfego automaticamente entre redes virtuais, redes locais e sub-redes do Azure. Se você desejar alterar qualquer roteamento padrão do Azure, poderá criar uma tabela de rotas para fazer isso.

Crie uma tabela de rotas vazia a ser associada a uma determinada sub-rede. A tabela de rotas definirá o próximo salto como o Firewall do Azure criado acima. Cada sub-rede pode ter zero ou uma tabela de rotas associada a ela.

```azurecli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG -l $LOC --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

Confira a [documentação da tabela de rotas de rede virtual](../virtual-network/virtual-networks-udr-overview.md#user-defined) sobre como você pode substituir as rotas do sistema padrão do Azure ou adicionar outras rotas a uma tabela de rotas de uma sub-rede.

### <a name="adding-firewall-rules"></a>Adicionando regras de firewall

Abaixo estão três regras de rede que você pode usar para configurar o em seu firewall, talvez seja necessário adaptar essas regras com base em sua implantação. A primeira regra permite o acesso à porta 9000 por meio de TCP. A segunda regra permite o acesso à porta 1194 e 123 via UDP (se você estiver implantando na 21Vianet do Azure na China, talvez precise de [mais](#azure-china-21vianet-required-network-rules)). Essas duas regras só permitirão o tráfego destinado ao CIDR da região do Azure que estamos usando, neste caso, leste dos EUA. Por fim, adicionaremos uma terceira porta de rede de abertura 123 ao `ntp.ubuntu.com` FQDN via UDP (adicionar um FQDN como uma regra de rede é um dos recursos específicos do firewall do Azure, e você precisará adaptá-lo ao usar suas próprias opções).

Depois de definir as regras de rede, adicionaremos também uma regra de aplicativo usando o `AzureKubernetesService` que abrange todos os FQDNs necessários acessíveis por meio da porta TCP 443 e da porta 80.

```
# Add FW Network Rules

az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apiudp' --protocols 'UDP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 1194 --action allow --priority 100
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apitcp' --protocols 'TCP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 9000
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'time' --protocols 'UDP' --source-addresses '*' --destination-fqdns 'ntp.ubuntu.com' --destination-ports 123

# Add FW Application Rules

az network firewall application-rule create -g $RG -f $FWNAME --collection-name 'aksfwar' -n 'fqdn' --source-addresses '*' --protocols 'http=80' 'https=443' --fqdn-tags "AzureKubernetesService" --action allow --priority 100
```

Confira a [documentação do Firewall do Azure](../firewall/overview.md) para saber mais sobre o serviço do Firewall do Azure.

### <a name="associate-the-route-table-to-aks"></a>Associe a tabela de rotas ao AKS

Para associar o cluster ao firewall, a sub-rede dedicada para a sub-rede do cluster deve fazer referência à tabela de rotas criada acima. A associação pode ser feita emitindo um comando para a rede virtual que mantém o cluster e o firewall para atualizar a tabela de rotas da sub-rede do cluster.

```azurecli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

### <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>Implantar o AKS com o tipo de saída do UDR na rede existente

Agora um cluster AKS pode ser implantado na rede virtual existente. Também usaremos o [tipo `userDefinedRouting` de saída ](egress-outboundtype.md), esse recurso garante que qualquer tráfego de saída será forçado pelo firewall e nenhum outro caminho de saída existirá (por padrão, o tipo de saída Load Balancer pode ser usado).

![aks-deploy](media/limit-egress-traffic/aks-udr-fw.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>Criar uma entidade de serviço com acesso para provisionar dentro da rede virtual existente

Uma identidade de cluster (identidade gerenciada ou entidade de serviço) é usada pelo AKS para criar recursos de cluster. Uma entidade de serviço que é passada no momento da criação é usada para criar recursos de AKS subjacentes, como recursos de armazenamento, IPs e balanceadores de carga usados pelo AKS (você também pode usar uma [identidade gerenciada](use-managed-identity.md) em vez disso). Se não tiver concedido as permissões apropriadas abaixo, você não poderá provisionar o cluster AKS.

```azurecli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

Agora, substitua o `APPID` e `PASSWORD` abaixo pela AppID da entidade de serviço e pela senha da entidade de serviço gerada automaticamente pela saída do comando anterior. Vamos referenciar a ID do recurso VNET para conceder as permissões para a entidade de serviço para que AKS possa implantar recursos nela.

```azurecli
APPID="<SERVICE_PRINCIPAL_APPID_GOES_HERE>"
PASSWORD="<SERVICEPRINCIPAL_PASSWORD_GOES_HERE>"
VNETID=$(az network vnet show -g $RG --name $VNET_NAME --query id -o tsv)

# Assign SP Permission to VNET

az role assignment create --assignee $APPID --scope $VNETID --role "Network Contributor"
```

Você pode verificar as permissões detalhadas necessárias [aqui](kubernetes-service-principal.md#delegate-access-to-other-azure-resources).

> [!NOTE]
> Se estiver usando o plug-in de rede kubenet, você precisará conceder a entidade de serviço do AKS ou permissões de identidade gerenciadas para a tabela de rotas criada previamente, pois o kubenet requer uma tabela de rotas para adicionar regras de roteamento necessária. 
> ```azurecli-interactive
> RTID=$(az network route-table show -g $RG -n $FWROUTE_TABLE_NAME --query id -o tsv)
> az role assignment create --assignee $APPID --scope $RTID --role "Network Contributor"
> ```

### <a name="deploy-aks"></a>Implantar AKS

Por fim, o cluster AKS pode ser implantado na sub-rede existente que dedicamos ao cluster. A sub-rede de destino a ser implantada é definida com a variável de ambiente, `$SUBNETID`. Não definimos a variável `$SUBNETID` nas etapas anteriores. Para definir o valor da ID de sub-rede, você pode usar o seguinte comando:

```azurecli
SUBNETID=$(az network vnet subnet show -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --query id -o tsv)
```

Você definirá o tipo de saída para usar o UDR que já existe na sub-rede. Essa configuração permitirá que o AKS ignore a configuração e o provisionamento de IP para o balanceador de carga.

> [!IMPORTANT]
> Para obter mais informações sobre o tipo de saída UDR incluindo limitações, consulte [**tipo de saída de EGRESSO UDR**](egress-outboundtype.md#limitations).


> [!TIP]
> Recursos adicionais podem ser adicionados à implantação de cluster, como [**cluster privado**](private-clusters.md). 
>
> O recurso AKS para [**intervalos de IP autorizados do servidor de API**](api-server-authorized-ip-ranges.md) pode ser adicionado para limitar o acesso do servidor de API somente ao ponto de extremidade público do firewall. O recurso de intervalos de IP autorizado é indicado no diagrama como opcional. Ao habilitar o recurso de intervalo de IP autorizado para limitar o acesso do servidor de API, suas ferramentas de desenvolvedor deverão usar um Jumpbox da rede virtual do firewall ou você deverá adicionar todos os pontos de extremidade do desenvolvedor ao intervalo de IP autorizado.

```azurecli
az aks create -g $RG -n $AKSNAME -l $LOC \
  --node-count 3 --generate-ssh-keys \
  --network-plugin $PLUGIN \
  --outbound-type userDefinedRouting \
  --service-cidr 10.41.0.0/16 \
  --dns-service-ip 10.41.0.10 \
  --docker-bridge-address 172.17.0.1/16 \
  --vnet-subnet-id $SUBNETID \
  --service-principal $APPID \
  --client-secret $PASSWORD \
  --api-server-authorized-ip-ranges $FWPUBLIC_IP
```

### <a name="enable-developer-access-to-the-api-server"></a>Habilitar o acesso do desenvolvedor ao servidor de API

Se você usou intervalos de IP autorizados para o cluster na etapa anterior, você deve adicionar os endereços IP das ferramentas de desenvolvedor à lista de clusters do AKS de intervalos de IP aprovados para acessar o servidor de API a partir daí. Outra opção é configurar um Jumpbox com as ferramentas necessárias dentro de uma sub-rede separada na rede virtual do firewall.

Adicionar outro endereço IP aos intervalos aprovados com o seguinte comando

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

 Use o comando [AZ AKs Get-Credentials] [AZ-AKs-Get-Credentials] para configurar `kubectl` o para se conectar ao cluster kubernetes recém-criado. 

 ```azurecli
 az aks get-credentials -g $RG -n $AKSNAME
 ```

### <a name="deploy-a-public-service"></a>Implantar um serviço público
Agora você pode iniciar a exposição de serviços e a implantação de aplicativos nesse cluster. Neste exemplo, vamos expor um serviço público, mas você também pode optar por expor um serviço interno por meio do [balanceador de carga interno](internal-lb.md).

![Serviço público DNAT](media/limit-egress-traffic/aks-create-svc.png)

Implante o aplicativo de votação do Azure copiando o YAML abaixo para um arquivo chamado `example.yaml`.

```yaml
# voting-storage-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-storage
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-storage
  template:
    metadata:
      labels:
        app: voting-storage
    spec:
      containers:
      - name: voting-storage
        image: mcr.microsoft.com/aks/samples/voting/storage:2.0
        args: ["--ignore-db-dir=lost+found"]
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 3306
          name: mysql
        volumeMounts:
        - name: mysql-persistent-storage
          mountPath: /var/lib/mysql
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_ROOT_PASSWORD
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
      volumes:
      - name: mysql-persistent-storage
        persistentVolumeClaim:
          claimName: mysql-pv-claim
---
# voting-storage-secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: voting-storage-secret
type: Opaque
data:
  MYSQL_USER: ZGJ1c2Vy
  MYSQL_PASSWORD: UGFzc3dvcmQxMg==
  MYSQL_DATABASE: YXp1cmV2b3Rl
  MYSQL_ROOT_PASSWORD: UGFzc3dvcmQxMg==
---
# voting-storage-pv-claim.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
---
# voting-storage-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-storage
  labels: 
    app: voting-storage
spec:
  ports:
  - port: 3306
    name: mysql
  selector:
    app: voting-storage
---
# voting-app-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-app
  template:
    metadata:
      labels:
        app: voting-app
    spec:
      containers:
      - name: voting-app
        image: mcr.microsoft.com/aks/samples/voting/app:2.0
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
          name: http
        env:
        - name: MYSQL_HOST
          value: "voting-storage"
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
        - name: ANALYTICS_HOST
          value: "voting-analytics"
---
# voting-app-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-app
  labels: 
    app: voting-app
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 8080
    name: http
  selector:
    app: voting-app
---
# voting-analytics-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-analytics
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-analytics
      version: "2.0"
  template:
    metadata:
      labels:
        app: voting-analytics
        version: "2.0"
    spec:
      containers:
      - name: voting-analytics
        image: mcr.microsoft.com/aks/samples/voting/analytics:2.0
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
          name: http
        env:
        - name: MYSQL_HOST
          value: "voting-storage"
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
---
# voting-analytics-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-analytics
  labels: 
    app: voting-analytics
spec:
  ports:
  - port: 8080
    name: http
  selector:
    app: voting-analytics
```

Implante o serviço executando:

```bash
kubectl apply -f example.yaml
```

### <a name="add-a-dnat-rule-to-azure-firewall"></a>Adicionar uma regra DNAT ao Firewall do Azure

> [!IMPORTANT]
> Ao usar o Firewall do Azure para restringir o tráfego de saída e criar uma rota definida pelo usuário (UDR) para forçar todo o tráfego de saída, crie uma regra DNAT apropriada no firewall para permitir corretamente o tráfego de entrada. Usar o Firewall do Azure com uma UDR interrompe a configuração de entrada devido ao roteamento assimétrico. (O problema ocorre se a sub-rede do AKS tiver uma rota padrão para o endereço IP privado do firewall e você estiver usando um balanceador de carga de entrada ou serviço do tipo do Kubernetes: LoadBalancer). Nesse caso, o tráfego de entrada do balanceador de carga é recebido por meio de seu endereço IP público, mas o caminho de retorno passa pelo endereço IP privado do firewall. Como o firewall tem monitoramento de estado, ele descarta o pacote de retorno porque o firewall não está ciente de uma sessão estabelecida. Para saber como integrar o Firewall do Azure com o balanceador de carga de serviço ou de entrada, confira [Integrar o Firewall do Azure com o Azure Standard Load Balancer](../firewall/integrate-lb.md).


Para configurar a conectividade de entrada, uma regra DNAT deve ser gravada no Firewall do Azure. Para testar a conectividade com o cluster, uma regra é definida para o endereço IP público de front-end do firewall para rotear para o IP interno exposto pelo serviço interno.

O endereço de destino pode ser personalizado, pois é a porta no firewall a ser acessado. O endereço traduzido deve ser o endereço IP do balanceador de carga interno. A porta traduzida deve ser a porta exposta para o serviço de Kubernetes.

Você precisará especificar o endereço IP interno atribuído ao balanceador de carga criado pelo serviço kubernetes. Recupere o endereço executando:

```bash
kubectl get services
```

O endereço IP necessário será listado na coluna EXTERNAL-IP semelhante à seguinte.

```bash
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes         ClusterIP      10.41.0.1       <none>        443/TCP        10h
voting-analytics   ClusterIP      10.41.88.129    <none>        8080/TCP       9m
voting-app         LoadBalancer   10.41.185.82    20.39.18.6    80:32718/TCP   9m
voting-storage     ClusterIP      10.41.221.201   <none>        3306/TCP       9m
```

Obtenha o IP do serviço executando:
```bash
SERVICE_IP=$(kubectl get svc voting-app -o jsonpath='{.status.loadBalancer.ingress[*].ip}')
```

Adicione a regra NAT executando:
```azurecli
az network firewall nat-rule create --collection-name exampleset --destination-addresses $FWPUBLIC_IP --destination-ports 80 --firewall-name $FWNAME --name inboundrule --protocols Any --resource-group $RG --source-addresses '*' --translated-port 80 --action Dnat --priority 100 --translated-address $SERVICE_IP
```

### <a name="validate-connectivity"></a>Validar a conectividade

Navegue até o endereço IP de front-end do Firewall do Azure em um navegador para validar a conectividade.

Você deve ver o aplicativo de votação AKS. Neste exemplo, o IP público do firewall era `52.253.228.132` .


![Captura de tela mostra o aplicativo de votação K S com botões para gatos, cachorros, redefinição e totais.](media/limit-egress-traffic/aks-vote.png)


### <a name="clean-up-resources"></a>Limpar recursos

Para limpar os recursos do Azure, exclua o grupo de recursos do AKS.

```azurecli
az group delete -g $RG
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu quais portas e endereços permitir se deseja restringir o tráfego de saída para o cluster. Você também viu como proteger seu tráfego de saída usando o Firewall do Azure. 

Se necessário, você pode generalizar as etapas acima para encaminhar o tráfego para sua solução de saída preferida, seguindo a [ `userDefinedRoute` documentação do tipo de saída](egress-outboundtype.md).

Se você quiser restringir como os pods se comunicam entre si e East-West restrições de tráfego no cluster, consulte [proteger o tráfego entre pods usando as políticas de rede no AKs][network-policy].

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-upgrade]: upgrade-cluster.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[dev-spaces-service-tags]: ../dev-spaces/configure-networking.md#virtual-network-or-subnet-configurations
