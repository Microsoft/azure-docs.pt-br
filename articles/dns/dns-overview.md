---
title: O que é o DNS do Azure?
description: Visão geral do Serviço de hospedagem de DNS no Microsoft Azure. Hospede seu domínio no Microsoft Azure.
author: vhorne
ms.service: dns
ms.topic: overview
ms.date: 3/21/2019
ms.author: victorh
ms.openlocfilehash: 06e53f07969ab5dfdebec88d36d0cb3d06cc87e1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078423"
---
# <a name="what-is-azure-dns"></a>O que é o DNS do Azure?

O DNS do Azure é um serviço de hospedagem para domínios DNS que fornece a resolução de nomes usando a infraestrutura do Microsoft Azure. Ao hospedar seus domínios no Azure, você pode gerenciar seus registros DNS usando as mesmas credenciais, APIs, ferramentas e faturamento que os outros serviços do Azure.

Você não pode usar o DNS do Azure para comprar um nome de domínio. Por um valor anual, você pode comprar um nome de domínio usando [domínios do Serviço de Aplicativo](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain#buy-the-domain) ou um registrador de nomes de domínio de terceiros. Seus domínios podem, então, ser hospedados no DNS do Azure para gerenciamento de registros. Confira [Delegar um domínio ao DNS do Azure](dns-domain-delegation.md)para saber mais.

Os recursos abaixo estão incluídos no DNS do Azure.

## <a name="reliability-and-performance"></a>Confiabilidade e desempenho

Domínios DNS no DNS do Azure são hospedados na rede global do Azure de servidores de nomes DNS. O DNS do Azure usa a rede anycast. Cada consulta DNS é atendida pelo servidor DNS disponível mais próximo para fornecer desempenho rápido e alta disponibilidade para seu domínio.

## <a name="security"></a>Segurança

 O DNS do Azure baseia-se no Azure Resource Manager, que fornece recursos como:

* [Controle de acesso baseado em função](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) para controlar quem tem acesso a ações específicas da sua organização.

* [Log de atividades](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) para monitorar como um usuário em sua organização modificou um recurso ou para encontrar um erro ao solucionar problemas.

* [Bloqueio de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) para bloquear uma assinatura, um grupo de recursos ou um recurso. O bloqueio impede que os usuários em sua organização acidentalmente excluam ou modifiquem recursos essenciais.

Para saber mais, confira [Como proteger zonas e registros DNS](dns-protect-zones-recordsets.md). 

## <a name="dnssec"></a>DNSSEC

Atualmente não há suporte para DNSSEC no DNS do Azure. Na maioria dos casos, você pode reduzir a necessidade de DNSSEC consistentemente usando HTTPS/TLS em seus aplicativos. Se o DNSSEC for um requisito crítico para as zonas DNS, você pode hospedar essas zonas com provedores de hospedagem de DNS de terceiros.

## <a name="ease-of-use"></a>Fácil de uso

 O DNS do Azure pode gerenciar os registros DNS para serviços do Azure e também fornece o DNS para recursos externos. O DNS do Azure é integrado ao portal do Azure e usa as mesmas credenciais, cobrança e contrato de suporte que outros serviços do Azure. 

O preço do DNS é baseado no número de zonas DNS hospedadas no Azure e o número de consultas DNS recebidas. Para saber mais sobre os preços, visite [Preços do DNS do Azure](https://azure.microsoft.com/pricing/details/dns/).

Seus domínios e registros podem ser gerenciados usando o portal do Azure, os cmdlets do Azure PowerShell e a CLI do Azure de plataforma cruzada. Aplicativos que requerem gerenciamento automatizado de DNS podem se integrar no serviço usando a API REST e os SDKs.

## <a name="customizable-virtual-networks-with-private-domains"></a>Redes virtuais personalizáveis com domínios privados

O DNS do Azure também dá suporte a domínios de DNS privado, que está atualmente em versão prévia pública. Esse recurso permite que você use seus próprios nomes de domínio personalizados em suas redes virtuais privadas em vez dos nomes fornecidos pelo Azure atualmente disponíveis.

Para ver mais informações, confira [Usar o DNS do Azure para domínios privados](private-dns-overview.md).

## <a name="alias-records"></a>Registros de alias

O DNS do Azure oferece suporte a conjuntos de registros de alias. É possível usar um conjunto de registros de alias para se referir a um recurso do Azure, como um endereço IP público do Azure, um perfil do Gerenciador de Tráfego do Azure ou um ponto de extremidade CDN (Rede de Distribuição de Conteúdo) do Azure. Se o endereço IP do recurso subjacente for alterado, o conjunto de registros de alias se atualizará perfeitamente durante a resolução de DNS. O alias registra os pontos de configuração na instância do serviço e a instância do serviço é associada a um endereço IP.

Além disso, agora é possível apontar seu domínio raiz ou de ápice para um perfil do Gerenciador de Tráfego ou um ponto de extremidade CDN usando um registro de alias. Um exemplo é contoso.com.

Para obter mais informações, consulte [registros de alias de visão geral do DNS do Azure](dns-alias.md).

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre zonas e registros DNS, confira [Visão geral de zonas e registros DNS](dns-zones-records.md).

* Para aprender a criar uma zona no DNS do Azure, confira [Criar uma zona DNS](./dns-getstarted-create-dnszone-portal.md).

* Para ver as perguntas frequentes sobre o DNS do Azure, veja o [Perguntas frequentes do DNS do Azure](dns-faq.md).

