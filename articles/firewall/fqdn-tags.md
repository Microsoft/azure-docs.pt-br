---
title: Visão geral de marcas FQDN para o Firewall do Azure
description: Uma tag FQDN representa um grupo de nomes de domínio totalmente qualificados (FQDNs) associados aos serviços Microsoft conhecidos.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: c0bd2f6a021baae80dafcc3d544e1062d8e022e8
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94653327"
---
# <a name="fqdn-tags-overview"></a>Visão geral de marcas FQDN

Uma tag FQDN representa um grupo de nomes de domínio totalmente qualificados (FQDNs) associados aos serviços Microsoft conhecidos. Você pode usar uma tag FQDN em regras de aplicativo para permitir o tráfego de rede de saída necessário pelo firewall.

Por exemplo, para permitir manualmente a passagem do tráfego de rede do Windows Update pelo seu firewall, você precisará criar várias regras de aplicativo de acordo com a documentação da Microsoft. Com as marcas FQDN, você pode criar uma regra de aplicativo, inclusive a marca do **Windows Update**, e agora o tráfego de rede para pontos de extremidade do Microsoft Windows Update pode fluir através do firewall.

Não é possível criar suas próprias marcas FQDN, nem especificar quais FQDNs são incluídos em uma marca. A Microsoft gerencia os FQDNs englobados pela marca FQDN e atualiza a marca à medida que os FQDNs mudam. 

<!--- screenshot of application rule with a FQDN tag.-->

A tabela a seguir mostra as marcas FQDN atuais que você pode usar. A Microsoft mantém essas marcas e adiciona outras periodicamente.

## <a name="current-fqdn-tags"></a>Marcas de FQDN atuais

|Marca FQDN  |Descrição  |
|---------|---------|
|Windows Update     |Permitir acesso de saída ao Microsoft Update, conforme descrito em [Como configurar um Firewall para atualizações de software](/mem/configmgr/sum/get-started/install-a-software-update-point).|
|Diagnóstico do Windows|Permitir acesso de saída para todos os [pontos de extremidade de diagnóstico do Windows](/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints).|
|MAPS (Microsoft Active Protection Service)|Permitir acesso de saída ao [MAPS](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/).|
|ASE (Ambiente do Serviço de Aplicativo)|Permite acesso de saída para tráfego de plataforma do ASE. Essa marca não cobre pontos de extremidade de Armazenamento e SQL específicos ao cliente criados pelo ASE. Eles devem ser habilitados por meio de [pontos de extremidade de serviço](../virtual-network/tutorial-restrict-network-access-to-resources.md) ou adicionados manualmente.<br><br>Para obter mais informações sobre como integrar o Firewall do Azure com o ASE, consulte [bloquear um ambiente do serviço de aplicativo](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase).|
|Serviço de Backup do Azure|Permite acesso de saída para os serviços de Backup do Azure.|
|Azure HDInsight|Permite o acesso de saída para o tráfego da plataforma HDInsight. Essa marca não abrange o armazenamento específico do cliente ou o tráfego do SQL do HDInsight. Habilite-os usando [pontos de extremidade de serviço](../virtual-network/tutorial-restrict-network-access-to-resources.md) ou adicione-os manualmente.|
|WindowsVirtualDesktop (WVD)|Permite o tráfego de saída da plataforma de área de trabalho virtual do Windows. Essa marca não abrange o armazenamento específico da implantação e os pontos de extremidade do barramento de serviço criados por WVD. Além disso, as regras de rede DNS e KMS são necessárias. Para obter mais informações sobre como integrar o Firewall do Azure ao WVD, consulte [usar o Firewall do Azure para proteger as implantações de área de trabalho virtual do Windows](protect-windows-virtual-desktop.md).|
|AKS (Serviço de Kubernetes do Azure)|Permite o acesso de saída ao AKS. Para obter mais informações, consulte [usar o Firewall do Azure para proteger implantações do AKS (serviço kubernetes do Azure)](protect-azure-kubernetes-service.md).|

> [!NOTE]
> Ao selecionar a Marca de FQDN em uma regra de aplicativo, o campo protocolo:porta precisa ser definido como **HTTPS**.

## <a name="next-steps"></a>Próximas etapas

Para saber como implantar um Firewall do Azure, confira [Tutorial: Implantar e configurar o Firewall do Azure usando o portal do Azure](tutorial-firewall-deploy-portal.md).