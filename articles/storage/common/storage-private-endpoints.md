---
title: Usar pontos de extremidade privados
titleSuffix: Azure Storage
description: Visão geral de pontos de extremidade privados para acesso seguro a contas de armazenamento de redes virtuais.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 3fcc58f626622bcc728265e782906226859e1bf9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104600455"
---
# <a name="use-private-endpoints-for-azure-storage"></a>Usar pontos de extremidade privados para o armazenamento do Azure

Você pode usar [pontos de extremidade privados](../../private-link/private-endpoint-overview.md) para suas contas de armazenamento do Azure para permitir que clientes em uma rede virtual (VNet) acessem dados com segurança por meio de um [link privado](../../private-link/private-link-overview.md). O ponto de extremidade privado usa um endereço IP do espaço de endereço de VNet para seu serviço de conta de armazenamento. O tráfego de rede entre os clientes na VNet e a conta de armazenamento atravessa a VNet e um link privado na rede de backbone da Microsoft, eliminando a exposição da Internet pública.

O uso de pontos de extremidade privados para sua conta de armazenamento permite que você:

- Proteja sua conta de armazenamento Configurando o firewall de armazenamento para bloquear todas as conexões no ponto de extremidade público para o serviço de armazenamento.
- Aumente a segurança para a VNet (rede virtual), permitindo que você bloqueie vazamento de dados da VNet.
- Conecte-se com segurança a contas de armazenamento de redes locais que se conectam à VNet usando [VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [expressroute ao qual](../../expressroute/expressroute-locations.md) com emparelhamento privado.

## <a name="conceptual-overview"></a>Visão geral conceitual

![Visão geral de pontos de extremidade privados para o armazenamento do Azure](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

Um ponto de extremidade privado é uma interface de rede especial para um serviço do Azure em sua [rede virtual](../../virtual-network/virtual-networks-overview.md) (VNet). Quando você cria um ponto de extremidade privado para sua conta de armazenamento, ele fornece conectividade segura entre clientes em sua VNet e seu armazenamento. O ponto de extremidade privado recebe um endereço IP do intervalo de endereços IP de sua VNet. A conexão entre o ponto de extremidade privado e o serviço de armazenamento usa um link privado seguro.

Os aplicativos na VNet podem se conectar ao serviço de armazenamento por meio do ponto de extremidade privado diretamente, **usando as mesmas cadeias de conexão e mecanismos de autorização que eles usariam de outra forma**. Pontos de extremidade privados podem ser usados com todos os protocolos compatíveis com a conta de armazenamento, incluindo REST e SMB.

Pontos de extremidade privados podem ser criados em sub-redes que usam [pontos de extremidade de serviço](../../virtual-network/virtual-network-service-endpoints-overview.md). Os clientes em uma sub-rede podem, portanto, se conectar a uma conta de armazenamento usando o ponto de extremidade privado, ao mesmo tempo que usam pontos de extremidades de serviço para acessar outros.

Quando você cria um ponto de extremidade privado para um serviço de armazenamento em sua rede virtual, uma solicitação de consentimento é enviada para aprovação para o proprietário da conta de armazenamento. Se o usuário que solicita a criação do ponto de extremidade privado também for um proprietário da conta de armazenamento, essa solicitação de consentimento será aprovada automaticamente.

Os proprietários da conta de armazenamento podem gerenciar solicitações de consentimento e os pontos de extremidade privados, por meio da guia '*pontos de extremidade particulares*' para a conta de armazenamento na [portal do Azure](https://portal.azure.com).

> [!TIP]
> Se você quiser restringir o acesso à sua conta de armazenamento somente por meio do ponto de extremidade privado, configure o firewall de armazenamento para negar ou controlar o acesso por meio do ponto de extremidade público.

Você pode proteger sua conta de armazenamento para aceitar somente conexões de sua VNet, [Configurando o firewall de armazenamento](storage-network-security.md#change-the-default-network-access-rule) para negar o acesso por meio de seu ponto de extremidade público por padrão. Você não precisa de uma regra de firewall para permitir o tráfego de uma VNet que tem um ponto de extremidade privado, já que o firewall de armazenamento controla apenas o acesso por meio do ponto de extremidade público. Em vez disso, pontos de extremidade privados dependem do fluxo de autorização para conceder acesso de sub-redes ao serviço de armazenamento.

> [!NOTE]
> Ao copiar BLOBs entre contas de armazenamento, o cliente deve ter acesso à rede para ambas as contas. Portanto, se você optar por usar um link privado para apenas uma conta (a origem ou o destino), verifique se o cliente tem acesso à rede para a outra conta. Para saber mais sobre outras maneiras de configurar o acesso à rede, consulte [configurar redes virtuais e firewalls de armazenamento do Azure](storage-network-security.md?toc=/azure/storage/blobs/toc.json). 

<a id="private-endpoints-for-azure-storage"></a>

## <a name="creating-a-private-endpoint"></a>Criando um ponto de extremidade privado

Para criar um ponto de extremidade privado usando o portal do Azure, consulte [conectar-se de forma privada a uma conta de armazenamento da experiência de conta de armazenamento no portal do Azure](../../private-link/tutorial-private-endpoint-storage-portal.md).

Para criar um ponto de extremidade privado usando o PowerShell ou o CLI do Azure, consulte um destes artigos. Ambos apresentam um aplicativo Web do Azure como o serviço de destino, mas as etapas para criar um link privado são as mesmas para uma conta de armazenamento do Azure.

- [Criar um ponto de extremidade privado usando a CLI do Azure](../../private-link/create-private-endpoint-cli.md)

- [Criar um ponto de extremidade privado usando o Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)



Ao criar um ponto de extremidade privado, você deve especificar a conta de armazenamento e o serviço de armazenamento ao qual ele se conecta. 

Você precisa de um ponto de extremidade privado separado para cada recurso de armazenamento que precisa acessar, ou seja, [BLOBs](../blobs/storage-blobs-overview.md), [Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md), [arquivos](../files/storage-files-introduction.md), [filas](../queues/storage-queues-introduction.md), [tabelas](../tables/table-storage-overview.md)ou [sites estáticos](../blobs/storage-blob-static-website.md). No ponto de extremidade privado, esses serviços de armazenamento são definidos como o **subrecurso de destino** da conta de armazenamento associada. 

Se você criar um ponto de extremidade privado para o recurso de armazenamento Data Lake Storage Gen2, você também deverá criar um para o recurso de armazenamento de BLOBs. Isso ocorre porque as operações que se destinam ao ponto de extremidade de Data Lake Storage Gen2 podem ser redirecionadas para o ponto de extremidade de BLOB. Ao criar um ponto de extremidade privado para ambos os recursos, você garante que as operações possam ser concluídas com êxito.

> [!TIP]
> Crie um ponto de extremidade privado separado para a instância secundária do serviço de armazenamento para melhor desempenho de leitura em contas RA-GRS.
> Certifique-se de criar uma conta de armazenamento de uso geral v2 (Standard ou Premium).

Para acesso de leitura à região secundária com uma conta de armazenamento configurada para armazenamento com redundância geográfica, você precisa de pontos de extremidade privados separados para as instâncias primária e secundária do serviço. Você não precisa criar um ponto de extremidade privado para a instância secundária para **failover**. O ponto de extremidade privado se conectará automaticamente à nova instância primária após o failover. Para obter mais informações sobre opções de redundância de armazenamento, consulte [redundância de armazenamento do Azure](storage-redundancy.md).

<a id="connecting-to-private-endpoints"></a>

## <a name="connecting-to-a-private-endpoint"></a>Conectando a um ponto de extremidade privado

Os clientes em uma VNet usando o ponto de extremidade privado devem usar a mesma cadeia de conexão para a conta de armazenamento, como clientes que se conectam ao ponto de extremidade público. Nós confiamos na resolução DNS para rotear automaticamente as conexões da VNet para a conta de armazenamento por meio de um link privado.

> [!IMPORTANT]
> Use a mesma cadeia de conexão para se conectar à conta de armazenamento usando pontos de extremidade privados, como você usaria o contrário. Não se conecte à conta de armazenamento usando sua `privatelink` URL de subdomínio.

Criamos uma [zona DNS privada](../../dns/private-dns-overview.md) anexada à VNet com as atualizações necessárias para os pontos de extremidade privados, por padrão. No entanto, se você estiver usando seu próprio servidor DNS, talvez seja necessário fazer alterações adicionais na configuração do DNS. A seção sobre o [DNS é alterada](#dns-changes-for-private-endpoints) abaixo descreve as atualizações necessárias para pontos de extremidade privados.

## <a name="dns-changes-for-private-endpoints"></a>Alterações de DNS para pontos de extremidade particulares

Quando você cria um ponto de extremidade privado, o registro de recurso DNS CNAME para a conta de armazenamento é atualizado para um alias em um subdomínio com o prefixo `privatelink` . Por padrão, também criamos uma [zona DNS privada](../../dns/private-dns-overview.md), correspondente ao `privatelink` subdomínio, com os registros de recurso de DNS a para os pontos de extremidade privados.

Quando você resolve a URL do ponto de extremidade de armazenamento de fora da VNet com o ponto de extremidade privado, ele é resolvido para o ponto de extremidade público do serviço de armazenamento. Quando resolvido da VNet que hospeda o ponto de extremidade privado, a URL do ponto de extremidade de armazenamento é resolvida para o endereço IP do ponto de extremidade privado.

Para o exemplo ilustrado acima, os registros de recurso de DNS para a conta de armazenamento ' StorageAccountA ', quando resolvidos de fora da VNet que hospeda o ponto de extremidade privado, serão:

| Nome                                                  | Tipo  | Valor                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<storage service public endpoint\>                   |
| \<storage service public endpoint\>                   | A     | \<storage service public IP address\>                 |

Conforme mencionado anteriormente, você pode negar ou controlar o acesso para clientes fora da VNet por meio do ponto de extremidade público usando o firewall de armazenamento.

Os registros de recurso DNS para StorageAccountA, quando resolvido por um cliente na VNet que hospeda o ponto de extremidade privado, serão:

| Nome                                                  | Tipo  | Valor                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | A     | 10.1.1.5                                              |

Essa abordagem habilita o acesso à conta de armazenamento **usando a mesma cadeia de conexão** para clientes na vnet que hospeda os pontos de extremidade privados, bem como clientes fora da vnet.

Se você estiver usando um servidor DNS personalizado em sua rede, os clientes deverão ser capazes de resolver o FQDN do ponto de extremidade da conta de armazenamento para o endereço IP do ponto de extremidade privado. Você deve configurar o servidor DNS para delegar seu subdomínio de vínculo privado à zona DNS privada para a VNet ou configurar os registros a para '*StorageAccountA.privatelink.blob.Core.Windows.net*' com o endereço IP do ponto de extremidade privado.

> [!TIP]
> Ao usar um servidor DNS personalizado ou local, você deve configurar o servidor DNS para resolver o nome da conta de armazenamento no `privatelink` subdomínio para o endereço IP do ponto de extremidade privado. Você pode fazer isso delegando o `privatelink` subdomínio à zona DNS privada da VNet ou configurando a zona DNS no servidor DNS e adicionando os registros a do DNS.

Os nomes de zona DNS recomendados para pontos de extremidade privados para os serviços de armazenamento e os subrecursos de destino do ponto final associado são:

| Serviço de armazenamento        | Sub-recurso de destino | Nome da zona                            |
| :--------------------- | :------------------ | :----------------------------------- |
| Serviço Blob           | blob                | `privatelink.blob.core.windows.net`  |
| Data Lake Storage Gen2 | DFS                 | `privatelink.dfs.core.windows.net`   |
| Serviço de arquivos           | arquivo                | `privatelink.file.core.windows.net`  |
| Serviço Fila          | fila               | `privatelink.queue.core.windows.net` |
| Serviço tabela          | table               | `privatelink.table.core.windows.net` |
| Sites estáticos        | web                 | `privatelink.web.core.windows.net`   |

Para obter mais informações sobre como configurar seu próprio servidor DNS para dar suporte a pontos de extremidade privados, consulte os seguintes artigos:

- [Resolução de nomes para recursos em redes virtuais do Azure](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [Configuração de DNS para pontos de extremidade privados](../../private-link/private-endpoint-overview.md#dns-configuration)

## <a name="pricing"></a>Preços

Para obter detalhes de preço, confira [Preço do Link Privado do Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Problemas conhecidos

Tenha em mente os seguintes problemas conhecidos sobre pontos de extremidade privados para o armazenamento do Azure.

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Restrições de acesso de armazenamento para clientes no VNets com pontos de extremidade privados

Os clientes no VNets com pontos de extremidade privados existentes enfrentam restrições ao acessar outras contas de armazenamento que têm pontos de extremidade privados. Por exemplo, suponha que uma VNet N1 tenha um ponto de extremidade privado para uma conta de armazenamento a1 para o armazenamento de BLOBs. Se a conta de armazenamento a2 tiver um ponto de extremidade privado em um N2 de VNet para o armazenamento de BLOBs, os clientes na VNet N1 também deverão acessar o armazenamento de blob na conta a2 usando um ponto de extremidade privado. Se a conta de armazenamento a2 não tiver nenhum ponto de extremidade privado para o armazenamento de BLOBs, os clientes na VNet N1 poderão acessar o armazenamento de BLOBs nessa conta sem um ponto de extremidade privado.

Essa restrição é um resultado das alterações de DNS feitas quando a conta a2 cria um ponto de extremidade privado.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>Regras de grupo de segurança de rede para sub-redes com pontos de extremidade privados

No momento, não é possível configurar as regras do NSG ( [grupo de segurança de rede](../../virtual-network/network-security-groups-overview.md) ) e as rotas definidas pelo usuário para pontos de extremidade privados. As regras de NSG aplicadas à sub-rede que hospeda o ponto de extremidade privado não são aplicadas ao ponto de extremidade privado. Eles são aplicados somente a outros pontos de extremidade (por exemplo: controladores de interface de rede). Uma solução alternativa limitada para esse problema é implementar suas regras de acesso para pontos de extremidade privados nas sub-redes de origem, embora essa abordagem possa exigir uma sobrecarga de gerenciamento maior.

### <a name="copying-blobs-between-storage-accounts"></a>Copiando BLOBs entre contas de armazenamento

Você pode copiar BLOBs entre contas de armazenamento usando pontos de extremidade privados somente se usar a API REST do Azure ou ferramentas que usam a API REST. Essas ferramentas incluem AzCopy, Gerenciador de Armazenamento, Azure PowerShell, CLI do Azure e SDKs do armazenamento de BLOBs do Azure. 

Somente pontos de extremidade privados destinados ao recurso de armazenamento de BLOBs têm suporte. Pontos de extremidade privados que destinam-se ao Data Lake Storage Gen2 ou ao recurso de arquivo ainda não têm suporte. Além disso, ainda não há suporte para a cópia entre contas de armazenamento usando o protocolo NFS (sistema de arquivos de rede). 

## <a name="next-steps"></a>Próximas etapas

- [Configurar redes virtuais e firewalls do Armazenamento do Microsoft Azure](storage-network-security.md)
- [Recomendações de segurança para o armazenamento de blobs](../blobs/security-recommendations.md)
