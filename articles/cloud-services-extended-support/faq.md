---
title: Perguntas frequentes sobre os serviços de nuvem do Azure (suporte estendido)
description: Perguntas frequentes sobre os serviços de nuvem do Azure (suporte estendido)
ms.topic: conceptual
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 9cac6cdd8e68af77b611c89e8b62e6f8d8845fd0
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105107509"
---
# <a name="frequently-asked-questions-for-azure-cloud-services-extended-support"></a>Perguntas frequentes sobre os Serviços de Nuvem do Microsoft Azure (suporte estendido)
Este artigo aborda as perguntas frequentes relacionadas aos serviços de nuvem do Azure (suporte estendido).

## <a name="general"></a>Geral

### <a name="what-is-the-resource-name-for-cloud-services-classic--cloud-services-extended-support"></a>Qual é o nome do recurso para serviços de nuvem (clássicos) & serviços de nuvem (suporte estendido)?
- Serviços de nuvem (clássicos): `microsoft.classiccompute/domainnames`
- Serviços de nuvem (suporte estendido): `microsoft.compute/cloudservices`

### <a name="what-locations-are-available-to-deploy-cloud-services-extended-support"></a>Quais locais estão disponíveis para implantar serviços de nuvem (suporte estendido)?
Os serviços de nuvem (suporte estendido) estão disponíveis em todas as regiões de nuvem pública.

### <a name="how-does-my-quota-change"></a>Como minha cota é alterada? 
Os clientes precisarão solicitar a cota usando os mesmos processos que qualquer outro produto Azure Resource Manager. A cota no Azure Resource Manager é regional e uma solicitação de cota separada será necessária para cada região.

### <a name="why-dont-i-see-a-production--staging-slot-anymore"></a>Por que não vejo mais um slot de preparo & de produção?
Os serviços de nuvem (suporte estendido) não dão suporte ao conceito lógico de um serviço hospedado, que incluiu dois slots (produção & preparo). Cada implantação é uma implantação independente de serviço de nuvem (suporte estendido). Para testar e preparar uma nova versão de um serviço de nuvem, implante um serviço de nuvem (suporte estendido) e marque-o como permutable VIP com outro serviço de nuvem (suporte estendido)

### <a name="why-cant-i-create-an-empty-cloud-service-anymore"></a>Por que não consigo mais criar um serviço de nuvem vazio?
O conceito de nomes de serviços hospedados não existe mais, você não pode criar um serviço de nuvem vazio (suporte estendido).

### <a name="does-cloud-services-extended-support-support-resource-health-check-rhc"></a>Os serviços de nuvem (suporte estendido) dão suporte à verificação de Resource Health (RHC)?
Não, os serviços de nuvem (suporte estendido) não dão suporte à verificação de Resource Health (RHC).

### <a name="how-are-role-instance-metrics-changing"></a>Como as métricas de instância de função são alteradas?
Não há alterações nas métricas da instância de função. 

### <a name="how-are-web--worker-roles-changing"></a>Como as funções de trabalho & Web são alteradas?
Não há nenhuma alteração no design, na arquitetura ou nos componentes das funções Web e de trabalho. 

### <a name="how-are-role-instances-changing"></a>Como as instâncias de função são alteradas?
Não há nenhuma alteração no design, na arquitetura ou nos componentes das instâncias de função. 

### <a name="how-will-guest-os-updates-change"></a>Como as atualizações do sistema operacional convidado serão alteradas?
 Não há nenhuma alteração no método de distribuição. Serviços de nuvem (clássicos) e serviços de nuvem (suporte estendido) receberão as mesmas atualizações.
 
### <a name="does-cloud-services-extended-support-support-stopped-allocated-and-stopped-deallocated-states"></a>Os serviços de nuvem (suporte estendido) dão suporte a Estados interrompidos e interrompidos por parada?

A implantação de serviços de nuvem (suporte estendido) só dá suporte ao estado reservado para parado, que aparece como "parado" no portal do Azure. Não há suporte para o estado parado-desalocado. 

### <a name="do-cloud-services-extended-support-deployments-support-scaling-across-clusters-availability-zones-and-regions"></a>As implantações de serviços de nuvem (suporte estendido) dão suporte ao dimensionamento em clusters, zonas de disponibilidade e regiões?
Implantações de serviços de nuvem (suporte estendido) não podem ser dimensionadas em vários clusters, zonas de disponibilidade e regiões. 

### <a name="are-there-any-pricing-differences-between-cloud-services-classic-and-cloud-services-extended-support"></a>Há alguma diferença de preço entre os serviços de nuvem (clássicos) e os serviços de nuvem (suporte estendido)?
Os serviços de nuvem (suporte estendido) usam Azure Key Vault e endereços IP públicos (ARM) básicos.Os clientes que exigem certificados precisam usar Azure Key Vault para o gerenciamento de certificados ([saiba mais](https://azure.microsoft.com/pricing/details/key-vault/) sobre preços de Azure Key Vault.)   Cada endereço IP público para serviços de nuvem (suporte estendido) é cobrado separadamente ([saiba mais](https://azure.microsoft.com/pricing/details/ip-addresses/) sobre preços de endereço IP público). 
## <a name="resources"></a>Recursos 

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-resource-group"></a>Quais recursos vinculados a uma implantação de serviços de nuvem (suporte estendido) precisam residir no mesmo grupo de recursos?
Balanceadores de carga, grupos de segurança de rede e tabelas de rotas precisam residir na mesma região e no mesmo grupo de recursos. 

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-region"></a>Quais recursos vinculados a uma implantação de serviços de nuvem (suporte estendido) precisam residir na mesma região?
Key Vault, rede virtual, endereços IP públicos, grupos de segurança de rede e tabelas de rotas precisam residir na mesma região.

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-virtual-network"></a>Quais recursos vinculados a uma implantação de serviços de nuvem (suporte estendido) precisam residir na mesma rede virtual?
Endereços IP públicos, balanceadores de carga, grupos de segurança de rede e tabelas de rotas precisam residir na mesma rede virtual. 

## <a name="deployment-files"></a>Arquivos de implantação 

### <a name="how-can-i-use-a-template-to-deploy-or-manage-my-deployment"></a>Como posso usar um modelo para implantar ou gerenciar minha implantação?
Os arquivos de modelo e parâmetro podem ser passados como um parâmetro usando REST, PowerShell e CLI. Eles também podem ser carregados usando o portal do Azure.  

### <a name="do-i-need-to-maintain-four-files-now-template-parameter-csdef-cscfg"></a>Preciso manter quatro arquivos agora? (modelo, parâmetro, csdef, cscfg)
Arquivos de modelo e parâmetro são usados somente para automação de implantação. Como os serviços de nuvem (clássicos), você pode criar manualmente os recursos dependentes primeiro e, em seguida, uma implantação de serviços de nuvem (suporte estendido) usando o PowerShell, comandos da CLI ou por meio do portal com o csdef, cscfg existente.

### <a name="how-does-my-application-code-change-on-cloud-services-extended-support"></a>Como o código do aplicativo é alterado nos serviços de nuvem (suporte estendido)
Não há nenhuma alteração necessária para o pacote de código do aplicativo no cspkg. Seus aplicativos existentes continuarão a funcionar como antes. 

### <a name="does-cloud-services-extended-support-allow-ctp-package-format"></a>Os serviços de nuvem (suporte estendido) permitem o formato de pacote CTP?
Não há suporte para o formato de pacote CTP em serviços de nuvem (suporte estendido). No entanto, ele permite um limite de tamanho de pacote avançado de 800 MB

## <a name="migration"></a>Migração

### <a name="will-cloud-services-extended-support-mitigate-the-failures-due-to-allocation-failures"></a>Os serviços de nuvem (suporte estendido) atenuam as falhas devido a falhas de alocação?
Não, as implantações de serviço de nuvem (suporte estendido) são vinculadas a um cluster como serviços de nuvem (clássico). Portanto, as falhas de alocação continuarão a existir se o cluster estiver cheio. 

### <a name="when-do-i-need-to-migrate"></a>Quando preciso migrar? 
Estimar o tempo necessário e a migração de complexidade depende de um intervalo de variáveis. O planejamento é a etapa mais eficaz para entender o escopo do trabalho, os bloqueadores e a complexidade da migração.

## <a name="networking"></a>Rede 

### <a name="why-cant-i-create-a-deployment-without-virtual-network"></a>Por que não posso criar uma implantação sem rede virtual?
As redes virtuais são um recurso necessário para qualquer implantação no Azure Resource Manager. A implantação de serviços de nuvem (suporte estendido) deve residir dentro de uma rede virtual. 

### <a name="why-am-i-now-seeing-so-many-networking-resources"></a>Por que agora estou vendo tantos recursos de rede? 
No Azure Resource Manager, os componentes de sua implantação de serviços de nuvem (suporte estendido) são expostos como um recurso para melhor visibilidade e controle aprimorado. Os mesmos tipos de recursos foram usados nos serviços de nuvem (clássicos), mas estavam apenas ocultos. Um exemplo desse recurso é o Load Balancer público, que agora é um recurso "somente leitura" explícito criado automaticamente pela plataforma

### <a name="what-restrictions-apply-for-a-subnet-with-respective-to-cloud-services-extended-support"></a>Quais restrições se aplicam a uma sub-rede com o respectivo serviço de nuvem (suporte estendido)?
Uma sub-rede contendo serviços de nuvem (suporte estendido) implantações não podem ser compartilhadas com implantações de outros produtos de computação, como máquinas virtuais, conjuntos de dimensionamento de máquinas virtuais, Service Fabric, etc.

### <a name="what-ip-allocation-methods-are-supported-on-cloud-services-extended-support"></a>Quais métodos de alocação de IP têm suporte nos serviços de nuvem (suporte estendido)?
Os serviços de nuvem (suporte estendido) dão suporte a métodos de alocação IP estáticos & dinâmicos. Endereços IP estáticos são referenciados como IPs reservados no arquivo cscfg.

### <a name="why-am-i-getting-charged-for-ip-addresses"></a>Por que estou recebendo cobranças por endereços IP?
Os clientes são cobrados pelo uso do endereço IP nos serviços de nuvem (suporte estendido) assim como os usuários são cobrados por endereços IP associados a máquinas virtuais. 

### <a name="can-i-use-a-dns-name-with-cloud-services-extended-support"></a>Posso usar um nome DNS com serviços de nuvem (suporte estendido)? 
Sim. Os serviços de nuvem (suporte estendido) também podem receber um nome DNS. Com Azure Resource Manager, o rótulo DNS é uma propriedade opcional do endereço IP público que é atribuído ao serviço de nuvem. O formato do nome DNS para implantações baseadas em Azure Resource Manager é `<userlabel>.<region>.cloudapp.azure.com`

### <a name="can-i-update-or-change-the-virtual-network-reference-for-an-existing-cloud-service-extended-support"></a>Posso atualizar ou alterar a referência de rede virtual para um serviço de nuvem existente (suporte estendido)? 
Não. A referência de rede virtual é obrigatória durante a criação de um serviço de nuvem. Para um serviço de nuvem existente, a referência de rede virtual não pode ser alterada. O próprio espaço de endereço de rede virtual pode ser modificado usando APIs de VNet. 

## <a name="certificates--key-vault"></a>& de certificados Key Vault

### <a name="why-do-i-need-to-manage-my-certificates-on-cloud-services-extended-support"></a>Por que preciso gerenciar meus certificados em serviços de nuvem (suporte estendido)?
Os serviços de nuvem (suporte estendido) adotaram o mesmo processo que outras ofertas de computação em que os certificados residem em cofres de chaves gerenciadas pelo cliente. Isso permite que os clientes tenham controle total sobre seus segredos & certificados. 

### <a name="can-i-use-one-key-vault-for-all-my-deployments-in-all-regions"></a>Posso usar um Key Vault para todas as minhas implantações em todas as regiões?
Não. Key Vault é um recurso regional e os clientes precisam de um Key Vault em cada região. No entanto, um Key Vault pode ser usado para todas as implantações em uma determinada região.

## <a name="next-steps"></a>Próximas etapas
Para começar a usar os serviços de nuvem (suporte estendido), consulte [implantar um serviço de nuvem (suporte estendido) usando o PowerShell](deploy-powershell.md)
