---
title: "Glossário do Azure - dicionário do Azure | Microsoft Docs"
description: "Use o glossário do Azure para entender a terminologia de nuvem na plataforma do Azure Este pequeno dicionário do Azure oferece definições para termos comuns de nuvem do Azure."
keywords: "Dicionário do Azure, terminologia de nuvem, Glossário do Azure, definições de terminologia, termos de nuvem"
services: na
documentationcenter: na
author: MonicaRush
manager: jhubbard
editor: 
ms.assetid: d7ac12f7-24b5-4bcd-9e4d-3d76fbd8d297
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: monicar
ms.openlocfilehash: b55eed31ddb5be23dc28a23b2f0588b8c8ff8833
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Glossário do Microsoft Azure: um dicionário de terminologia de nuvem na plataforma do Azure

O glossário do Microsoft Azure é um pequeno dicionário de terminologia de nuvem para a plataforma do Azure Consulte também:

* [Microsoft Azure e Amazon Web Services](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/) – definições dos serviços do Azure e de suas contrapartes em AWS.<!-- I propose to link to https://azure.microsoft.com/en-us/services/ instead of this -->
* [Termos de computação em nuvem](https://azure.microsoft.com/overview/cloud-computing-dictionary/) – termos gerais de nuvem do setor.

## <a name="account"></a>conta
Uma conta que é usada para acessar e gerenciar uma assinatura do Azure. Ele também é conhecido como uma conta do Azure Embora uma conta pode ser um dos seguintes: um trabalho existente, escola, ou pessoais conta da Microsoft, ou um nome de usuário do Office 365 e senha. Você também pode criar uma conta para gerenciar uma assinatura do Azure, quando você se inscreve para o [avaliação gratuita](https://azure.microsoft.com).  
veja [inscrever-se para uma assinatura do Azure com sua conta do Office 365](billing/billing-use-existing-office-365-account-azure-subscription.md) e [contas que você pode usar para entrar no](active-directory/active-directory-how-subscriptions-associated-directory.md).

## <a name="api-app"></a>Aplicativo de API
Outro nome para o [aplicativo de Serviço de Aplicativo](#app-service-app).

## <a name="app-service-app"></a>Aplicativo de Serviço de Aplicativo
Os recursos de computação que o [Serviço de Aplicativo do Azure](app-service/app-service-web-overview.md) fornece para hospedar um site ou aplicativo Web, API Web ou [back-end de aplicativo móvel](app-service-mobile/app-service-mobile-value-prop.md). Aplicativos de Serviços de Aplicativos também são conhecidos como *Serviços de Aplicativos*, *aplicativos Web*, *aplicativos de API* e *aplicativos móveis*.

## <a name="availability-set"></a>conjunto de disponibilidade
Uma coleção de máquinas virtuais gerenciadas em conjunto a fim de fornecer redundância e confiabilidade de aplicativo. O uso de um conjunto de disponibilidade garante que durante um evento de manutenção planejada ou não planejada, pelo menos uma máquina virtual esteja disponível.  
Veja [Gerenciar a disponibilidade de máquinas virtuais do Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e [Gerenciar a disponibilidade de máquinas virtuais do Linux](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="classic-model"></a>modelo de implantação clássico do Azure
Um dos dois [modelos de implantação](resource-manager-deployment-model.md) usados para implantar recursos no Azure (o novo modelo é o Azure Resource Manager). Alguns serviços do Azure dão suporte somente ao modelo de implantação do Resource Manager, alguns dão suporte somente para o modelo de implantação clássico e alguns dão suporte a ambos. A documentação para cada serviço do Azure especifica a quais modelos esse serviço dá suporte.

## <a name="cli"></a>CLI (interface de linha de comando) do Azure
Uma interface de linha de comando que pode ser usada para gerenciar os serviços do Azure de Windows, macOS e Linux.  Alguns serviços, ou recursos do serviço, podem ser gerenciados apenas por meio do PowerShell ou da CLI. Veja [CLI do Azure 2.0](/cli/azure/overview)

## <a name="powershell"></a>PowerShell do Azure
Uma interface de linha de comando para gerenciar os serviços do Azure por meio de uma linha de comando em computadores Windows. Alguns serviços, ou recursos do serviço, podem ser gerenciados apenas por meio do PowerShell ou da CLI.
Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview)

## <a name="arm-model"></a>modelo de implantação do Azure Resource Manager
Um dos dois [modelos de implantação](resource-manager-deployment-model.md) usados para implantar recursos no Microsoft Azure (o outro é o modelo de implantação clássico). Alguns serviços do Azure dão suporte somente ao modelo de implantação do Resource Manager, alguns dão suporte somente para o modelo de implantação clássico e alguns dão suporte a ambos. A documentação para cada serviço do Azure especifica a quais modelos esse serviço dá suporte.

## <a name="fault-domain"></a>domínio de falha
A coleção de máquinas virtuais em um conjunto de disponibilidade que podem falhar ao mesmo tempo. Um exemplo disso é um grupo de máquinas em um rack que compartilham uma fonte de alimentação e um comutador de rede comum. No Azure, as máquinas virtuais em um conjunto de disponibilidade são separadas automaticamente em vários domínios de falha.  
Veja [Gerenciar a disponibilidade de máquinas virtuais do Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou [Gerenciar a disponibilidade de máquinas virtuais do Linux](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

## <a name="geo"></a>área geográfica
Um limite definido para a residência dos dados e que geralmente contém duas ou mais regiões. Os limites podem ser dentro ou fora de fronteiras nacionais e são influenciados pela regulamentação fiscal. Cada área geográfica tem pelo menos uma região. Entre os exemplos de áreas geográficas estão Pacífico Asiático e Japão. Também é chamada de *geografia*.  
Veja [Regiões do Azure](best-practices-availability-paired-regions.md)

## <a name="geo-replication"></a>replicação geográfica
O processo de replicação automaticamente de conteúdo como blobs, tabelas e filas dentro de um par regional.  
Veja [Replicação Geográfica Ativa para o Banco de Dados SQL do Azure](sql-database/sql-database-geo-replication-overview.md)
<!-- The meaning of "geo" in this term seems to be different than the meaning provided in the "geo" entry -->

## <a name="image"></a>imagem
Um arquivo que contém o sistema operacional e a configuração de aplicativo que pode ser usada para criar qualquer quantidade de máquinas virtuais. No Azure, há dois tipos de imagens: a imagem da VM e a imagem do SO. Uma imagem de VM inclui um sistema operacional e todos os discos anexados a uma máquina virtual quando a imagem é criada. Uma imagem do SO contém apenas um sistema operacional generalizado sem qualquer configuração de disco de dados.  
Veja [Navegar e selecionar imagens de máquinas virtuais do Windows no Azure com o PowerShell ou a CLI](virtual-machines/windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="limits"></a>limites
O número de recursos que podem ser criados, ou o parâmetro de comparação de desempenho que pode ser atingido. Geralmente, os limites estão associados a assinaturas, serviços e ofertas.  
Veja [Assinatura do Azure e limites de serviços, cotas e restrições](azure-subscription-service-limits.md)

## <a name="load-balancer"></a>balanceador de carga
Um recurso que distribui o tráfego recebido entre computadores em uma rede. No Azure, um balanceador de carga distribui o tráfego para máquinas virtuais definidas em um conjunto de balanceadores de carga. Um [balanceador de carga](load-balancer/load-balancer-overview.md) pode ser voltado à Internet ou interno.  

## <a name="mobile-app"></a>aplicativo móvel
Outro nome para o [Aplicativo de Serviço de Aplicativo](#app-service-app).

## <a name="offer"></a>oferta
Os preços, os créditos e os termos relacionados aplicáveis a uma assinatura do Azure.  
Confira a página [Detalhes da oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/)

## <a name="portal"></a>portal
O portal da Web seguro usado para implantar e gerenciar serviços do Azure.  Há dois portais: o [Portal do Azure](http://portal.azure.com/) e o [portal clássico](http://manage.windowsazure.com/). Alguns serviços estão disponíveis nos dois portais, enquanto outros só estão disponíveis em um ou no outro. O [Gráfico de disponibilidade do portal do Azure](https://azure.microsoft.com/features/azure-portal/availability/) lista quais serviços estão disponíveis em qual portal.

## <a name="region"></a>region
Uma área dentro de uma área geográfica que não ultrapassa fronteiras nacionais e contém um ou mais datacenters. Preços, serviços regionais e tipos de oferta são expostos no nível da região. Normalmente, uma região é emparelhada com outra região, que pode estar a até várias centenas de quilômetros de distância. Os pares regionais podem ser usados como um mecanismo para cenários de alta disponibilidade e de recuperação de desastres. Isso também é chamado de *localização*.  
Veja [Regiões do Azure](best-practices-availability-paired-regions.md)

## <a name="resource"></a>recurso
Um item que faz parte de sua solução do Azure. Cada serviço do Azure permite que você implante tipos diferentes de recursos, como bancos de dados ou máquinas virtuais.   
Veja [Visão geral do Azure Resource Manager](azure-resource-manager/resource-group-overview.md)

## <a name="resource-group"></a>grupo de recursos
Um contêiner no Resource Manager que armazena os recursos relacionados para um aplicativo. O grupo de recursos pode incluir todos os recursos de um aplicativo ou apenas os recursos que são agrupados logicamente. Você pode decidir como deseja alocar recursos para grupos de recursos com base no que faz mais sentido para sua organização.  
Veja [Visão geral do Azure Resource Manager](azure-resource-manager/resource-group-overview.md)

## <a name="arm-template"></a>modelo do Resource Manager
Um arquivo JSON que define declarativamente um ou mais recursos do Azure e que define as dependências entre os recursos implantados. O modelo pode ser usado para implantar os recursos de forma consiste e repetida.  
Veja [Criando modelos do Azure Resource Manager](resource-group-authoring-templates.md)

## <a name="resource-provider"></a>provedor de recursos
Um serviço que fornece os recursos que você pode implantar e gerenciar por meio do Resource Manager. Cada provedor de recursos oferece operações para trabalhar com os recursos implantados. Os provedores de recursos podem ser acessados por meio do portal do Azure, Azure PowerShell e vários SDKs de programação.  
Veja [Visão geral do Azure Resource Manager](azure-resource-manager/resource-group-overview.md)

## <a name="role"></a>função
Um meio para controlar o acesso que pode ser atribuído a usuários, grupos e serviços. As funções são capazes de executar ações, por exemplo, criar, gerenciar e ler recursos do Azure.  
Veja [RBAC: funções internas](active-directory/role-based-access-built-in-roles.md)

## <a name="sla"></a>SLA (contrato de nível de serviço)
O contrato que descreve os compromissos da Microsoft em relação ao tempo de atividade e à conectividade. Cada serviço do Azure tem um SLA específico.  
Veja [Contratos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/)

## <a name="sas"></a>assinatura de acesso compartilhado (SAS)
Uma assinatura que permite que você conceda acesso limitado a um recurso, sem expor sua chave de conta. Por exemplo, o [Armazenamento do Azure usa SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) para conceder acesso de cliente a objetos tais como blobs. O [Hub IoT usa SAS](iot-hub/iot-hub-devguide-security.md#security-tokens) para conceder a dispositivos a permissão para enviar telemetria.

## <a name="storage-account"></a>do Azure
Uma conta que fornece acesso aos serviços de Blob do Azure, de Fila, de Tabelas e de Arquivo no Armazenamento do Azure. O nome da conta de armazenamento define o namespace exclusivo para os objetos de dados de Armazenamento do Azure.  
Veja [Sobre as contas de armazenamento do Azure](storage/common/storage-create-storage-account.md)

## <a name="subscription"></a>subscription
Um contrato do cliente com a Microsoft que permite a obtenção dos serviços do Azure. Os preços da assinatura e os termos relacionados são governados pela oferta escolhida para a assinatura.
Veja [Contrato de Assinatura do Microsoft Online](https://azure.microsoft.com/support/legal/subscription-agreement/) e [Como as assinaturas do Azure estão associadas ao Azure Active Directory](active-directory/active-directory-how-subscriptions-associated-directory.md)

## <a name="tag"></a>marca
Um termo de indexação que permite a classificação dos recursos de acordo com suas necessidades de gerenciamento ou cobrança. Quando você tiver uma coleção complexa de recursos, você poderá usar marcas para visualizar esses ativos da maneira que fizer mais sentido. Por exemplo, você pode marcar os recursos que servem para uma função semelhante em sua organização ou pertencem ao mesmo departamento.  
Veja [Usando marcas para organizar os recursos do Azure](resource-group-using-tags.md)

## <a name="update-domain"></a>domínio de atualização
A coleção de máquinas virtuais em um conjunto de disponibilidade que são atualizadas ao mesmo tempo. As máquinas virtuais no mesmo domínio de atualização são reiniciadas juntas durante a manutenção planejada. O Azure nunca reinicia mais de um domínio de atualização por vez. Isso também é chamado de domínio de upgrade.  
Veja [Gerenciar a disponibilidade de máquinas virtuais do Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e [Gerenciar a disponibilidade de máquinas virtuais do Linux](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="vm"></a>máquina virtual
A implementação de software de um computador físico que executa um sistema operacional. Diversas máquinas virtuais podem ser executadas simultaneamente no mesmo hardware. No Azure, as máquinas virtuais estão disponíveis em vários tamanhos.  
Veja [Documentação de Máquinas Virtuais](https://azure.microsoft.com/documentation/services/virtual-machines/)

## <a name="vm-extension"></a>extensão da máquina virtual
Um recurso que implementa comportamentos ou recursos que ajudam outros programas a funcionar, ou que fornecem a capacidade de interação com um computador em execução. Por exemplo, você pode usar a extensão de Acesso da Máquina Virtual para redefinir ou modificar valores de acesso remoto em uma máquina virtual do Azure.
<!-- This definition seems obscure to me; maybe a list of examples would work better than a conceptual definition? -->
Veja [Sobre os recursos e extensões de máquina virtual (Windows)](virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou [Sobre os recursos e extensões de máquina virtual (Linux)](virtual-machines/linux/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="vnet"></a>rede virtual
Uma rede que fornece conectividade entre seus recursos do Azure que estão isolados de todos os outros locatários do Azure. Um [Gateway de VPN do Azure](vpn-gateway/vpn-gateway-about-vpngateways.md) lhe permite estabelecer conexões entre redes virtuais e [entre uma rede virtual e uma rede local](vpn-gateway/vpn-gateway-plan-design.md). Você pode controlar os blocos de endereços IP, as configurações de DNS, as políticas de segurança e as tabelas de rotas em uma rede virtual.  
Veja [Visão geral da Rede Virtual](virtual-network/virtual-networks-overview.md)  

## <a name="web-app"></a>Aplicativo Web
Outro nome para o [Aplicativo de Serviço de Aplicativo](#app-service-app).

## <a name="see-also"></a>Consulte também

* [Introdução ao Azure](https://azure.microsoft.com/get-started/)
* [Centro de Recurso de Nuvem](https://azure.microsoft.com/resources/)  
* [Azure para seus aplicativos de negócios](https://azure.microsoft.com/overview/business-apps-on-azure/)
* [Azure em seu datacenter](https://azure.microsoft.com/overview/business-apps-on-azure/)

