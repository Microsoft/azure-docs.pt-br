---
title: Solucionar problemas de rede com o registro
description: Sintomas, causas e resolução de problemas comuns ao acessar um registro de contêiner do Azure em uma rede virtual ou atrás de um firewall
ms.topic: article
ms.date: 10/01/2020
ms.openlocfilehash: 75c94d40663a7058dab7ed691183dd578964edcc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699599"
---
# <a name="troubleshoot-network-issues-with-registry"></a>Solucionar problemas de rede com o registro

Este artigo ajuda você a solucionar problemas que podem ser encontrados ao acessar um registro de contêiner do Azure em uma rede virtual ou atrás de um firewall. 

## <a name="symptoms"></a>Sintomas

Pode incluir um ou mais dos seguintes:

* Não é possível enviar por Push ou efetuar pull de imagens e você recebe um erro `dial tcp: lookup myregistry.azurecr.io`
* Não é possível enviar por Push ou efetuar pull de imagens e você recebe CLI do Azure erro `Could not connect to the registry login server`
* Não é possível efetuar pull de imagens do registro para o serviço kubernetes do Azure ou outro serviço do Azure
* Não é possível acessar um registro por trás de um proxy HTTPS e você recebe um erro `Error response from daemon: login attempt failed with status: 403 Forbidden`
* Não é possível definir as configurações de rede virtual e você recebe um erro `Failed to save firewall and virtual network settings for container registry`
* Não é possível acessar ou exibir as configurações do registro no portal do Azure ou gerenciar o registro usando o CLI do Azure
* Não é possível adicionar ou modificar as configurações de rede virtual ou as regras de acesso público
* As tarefas ACR não podem enviar por Push ou efetuar pull de imagens
* A central de segurança do Azure não pode verificar imagens no registro ou os resultados da verificação não aparecem na central de segurança do Azure

## <a name="causes"></a>Causas

* Um firewall ou proxy cliente impede a [solução](#configure-client-firewall-access) de acesso
* As regras de acesso de rede pública no registro impedem a [solução](#configure-public-access-to-registry) de acesso
* A configuração de rede virtual impede a [solução](#configure-vnet-access) de acesso
* Você tenta integrar a central de segurança do Azure ou alguns outros serviços do Azure com um registro que tem um ponto de extremidade privado, ponto de extremidade de serviço ou regras de acesso de IP público- [solução](#configure-service-access)

## <a name="further-diagnosis"></a>Diagnóstico adicional 

Execute o comando [AZ ACR check-Health](/cli/azure/acr#az-acr-check-health) para obter mais informações sobre a integridade do ambiente do registro e, opcionalmente, o acesso a um registro de destino. Por exemplo, diagnostique certos problemas de conectividade de rede ou configuração. 

Consulte [verificar a integridade de um registro de contêiner do Azure](container-registry-check-health.md) para obter exemplos de comando. Se forem relatados erros, examine a [referência de erro](container-registry-health-error-reference.md) e as seções a seguir para obter as soluções recomendadas.

Se você estiver tendo problemas ao usar o serviço wih do Azure kubernetes, execute o comando [AZ AKs check-ACR](/cli/azure/aks#az_aks_check_acr) para validar se o registro está acessível no cluster AKs.

> [!NOTE]
> Alguns sintomas de conectividade de rede também podem ocorrer quando há problemas com a autenticação ou autorização do registro. Consulte [solucionar problemas de logon do registro](container-registry-troubleshoot-login.md).

## <a name="potential-solutions"></a>Possíveis soluções

### <a name="configure-client-firewall-access"></a>Configurar o acesso do firewall do cliente

Para acessar um registro por trás de um firewall cliente ou servidor proxy, configure as regras de firewall para acessar os pontos de extremidade públicos de dados e REST do registro. Se [os pontos de extremidade de dados dedicados](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) estiverem habilitados, você precisará de regras para acessar:

* Ponto de extremidade REST: `<registryname>.azurecr.io`
* Ponto (s) de dados: `<registry-name>.<region>.data.azurecr.io`

Para um registro replicado geograficamente, configure o acesso ao ponto de extremidade de dados para cada réplica regional.

Por trás de um proxy HTTPS, certifique-se de que o cliente Docker e o daemon do Docker estejam configurados para o comportamento do proxy.

Os logs de recursos do registro na tabela ContainerRegistryLoginEvents podem ajudar a diagnosticar uma tentativa de conexão bloqueada.

Links relacionados:

* [Configurar regras para acessar um registro de contêiner do Azure atrás de um firewall](container-registry-firewall-access-rules.md)
* [Configuração de proxy HTTP/HTTPS](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
* [Replicação geográfica no registro de contêiner do Azure](container-registry-geo-replication.md)
* [Logs de registro de contêiner do Azure para avaliação de diagnóstico e auditoria](container-registry-diagnostics-audit-logs.md)

### <a name="configure-public-access-to-registry"></a>Configurar acesso público ao registro

Se estiver acessando um registro pela Internet, confirme se o registro permite o acesso à rede pública do seu cliente. Por padrão, um registro de contêiner do Azure permite o acesso aos pontos de extremidade do registro público de todas as redes. Um registro pode limitar o acesso a redes selecionadas ou endereços IP selecionados. 

Se o registro estiver configurado para uma rede virtual com um ponto de extremidade de serviço, desabilitar o acesso à rede pública também desabilita o acesso ao longo do ponto de extremidade de serviço. Se o registro estiver configurado para uma rede virtual com link privado, as regras de rede IP não se aplicarão aos pontos de extremidade particulares do registro. 

Links relacionados:

* [Configurar regras de rede IP pública](container-registry-access-selected-networks.md)
* [Conectar-se de forma privada a um registro de contêiner do Azure usando o link privado do Azure](container-registry-private-link.md)
* [Restringir o acesso a um registro de contêiner usando um ponto de extremidade de serviço em uma rede virtual do Azure](container-registry-vnet.md)


### <a name="configure-vnet-access"></a>Configurar o acesso à VNet

Confirme se a rede virtual está configurada com um ponto de extremidade privado para o link privado ou um ponto de extremidade de serviço (versão prévia). Atualmente, não há suporte para um ponto de extremidade de bastiões do Azure.

Examine as regras de NSG e as marcas de serviço usadas para limitar o tráfego de outros recursos na rede para o registro. 

Se um ponto de extremidade de serviço para o registro estiver configurado, confirme se uma regra de rede foi adicionada ao registro que permite o acesso dessa sub-rede de rede. O ponto de extremidade de serviço só dá suporte ao acesso de máquinas virtuais e clusters AKS na rede.

Se você quiser restringir o acesso ao registro usando uma rede virtual em uma assinatura do Azure diferente, certifique-se de registrar o `Microsoft.ContainerRegistry` provedor de recursos nessa assinatura. [Registre o provedor de recursos para o](../azure-resource-manager/management/resource-providers-and-types.md) registro de contêiner do Azure usando o portal do Azure, CLI do Azure ou outras ferramentas do Azure.

Se o Firewall do Azure ou uma solução semelhante estiver configurada na rede, verifique se o tráfego de saída de outros recursos, como um cluster AKS, está habilitado para alcançar os pontos de extremidade do registro.

Se um ponto de extremidade privado estiver configurado, confirme se o DNS resolve o FQDN público do registro, como *myregistry.azurecr.Io* , para o endereço IP privado do registro. Use um utilitário de rede como `dig` ou `nslookup` para pesquisa de DNS.

Links relacionados:

* [Conectar-se de forma privada a um registro de contêiner do Azure usando o link privado do Azure](container-registry-private-link.md)
* [Restringir o acesso a um registro de contêiner usando um ponto de extremidade de serviço em uma rede virtual do Azure](container-registry-vnet.md)
* [Regras de rede de saída necessárias e FQDNs para clusters AKS](../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
* [Kubernetes: Depurando resolução DNS](https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/)
* [Marcas de serviço de rede virtual](../virtual-network/service-tags-overview.md)

### <a name="configure-service-access"></a>Configurar o acesso ao serviço

Atualmente, o acesso a um registro de contêiner com restrições de rede não é permitido de vários serviços do Azure:

* A central de segurança do Azure não pode executar a [verificação de vulnerabilidade de imagem](../security-center/defender-for-container-registries-introduction.md?bc=%2fazure%2fcontainer-registry%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fcontainer-registry%2ftoc.json) em um registro que restringe o acesso a pontos de extremidade privados, sub-redes selecionadas ou endereços IP. 
* Os recursos de determinados serviços do Azure não podem acessar um registro de contêiner com restrições de rede, incluindo Azure App serviço e instâncias de contêiner do Azure.

Se o acesso ou a integração desses serviços do Azure com o registro de contêiner for necessário, remova a restrição de rede. Por exemplo, remova os pontos de extremidade privados do registro ou remova ou modifique as regras de acesso público do registro.

A partir de janeiro de 2021, você pode configurar um registro de rede restrita para [permitir o acesso](allow-access-trusted-services.md) de selecionar serviços confiáveis.

Links relacionados:

* [Verificação de imagem do registro de contêiner do Azure por central de segurança](../security-center/defender-for-container-registries-introduction.md)
* Fornecer [comentários](https://feedback.azure.com/forums/347535-azure-security-center/suggestions/41091577-enable-vulnerability-scanning-for-images-that-are)
* [Permitir que os serviços confiáveis acessem com segurança um registro de contêiner restrito à rede](allow-access-trusted-services.md)


## <a name="advanced-troubleshooting"></a>Solução de problemas avançada

Se a [coleção de logs de recursos](container-registry-diagnostics-audit-logs.md) estiver habilitada no registro, examine o log ContainterRegistryLoginEvents. Esse log armazena os eventos de autenticação e o status, incluindo a identidade de entrada e o endereço IP. Consulte o log para obter [falhas de autenticação do registro](container-registry-diagnostics-audit-logs.md#registry-authentication-failures). 

Links relacionados:

* [Logs para avaliação de diagnóstico e auditoria](container-registry-diagnostics-audit-logs.md)
* [Perguntas frequentes do registro de contêiner](container-registry-faq.md)
* [Linha de base de segurança do Azure para registro de contêiner do Azure](security-baseline.md)
* [Melhores práticas para o Registro de Contêiner do Azure](container-registry-best-practices.md)

## <a name="next-steps"></a>Próximas etapas

Se você não resolver o problema aqui, consulte as opções a seguir.

* Outros tópicos de solução de problemas de registro incluem:
  * [Solucionar problemas de logon do registro](container-registry-troubleshoot-login.md) 
  * [Solucionar problemas de desempenho de registro](container-registry-troubleshoot-performance.md)
* Opções de [suporte da Comunidade](https://azure.microsoft.com/support/community/)
* [P e R da Microsoft](/answers/products/)
* [Abra um tíquete de suporte](https://azure.microsoft.com/support/create-ticket/)