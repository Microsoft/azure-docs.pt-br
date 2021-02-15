---
title: Recursos da Central de Segurança do Azure de acordo com o sistema operacional, tipo de computador e nuvem
description: Saiba quais recursos da Central de Segurança do Azure estão disponíveis de acordo com o sistema operacional, tipo e implantação na nuvem.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2021
ms.author: memildin
ms.openlocfilehash: 7034d1b5f06fcbcead79d85df10042eee3581b0b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369349"
---
# <a name="feature-coverage-for-machines"></a>Cobertura de recursos para computadores

As duas guias abaixo mostram os recursos da Central de Segurança do Azure disponíveis para servidores e máquinas virtuais do Windows e do Linux.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Recursos com suporte para máquinas virtuais e servidores <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Máquinas do Windows**](#tab/features-windows)

|**Recurso**|**Máquinas Virtuais do Azure**|**Conjuntos de dimensionamento de máquina virtual do Azure**|**Computadores habilitados para o Azure Arc**|**Azure Defender necessário**
|----|:----:|:----:|:----:|:----:|
|[Integração do Microsoft Defender para Ponto de Extremidade](security-center-wdatp.md)|✔</br>(em versões compatíveis)|✔</br>(em versões compatíveis)|✔|Sim|
|[Análise comportamental de máquina virtual (e alertas de segurança)](alerts-reference.md)|✔|✔|✔|Sim|
|[Alertas de segurança sem arquivo](alerts-reference.md#alerts-windows)|✔|✔|✔|Sim|
|[Alertas de segurança baseados em rede](other-threat-protections.md#network-layer)|✔|✔|-|Sim|
|[Acesso à VM Just-In-Time](security-center-just-in-time.md)|✔|-|-|Sim|
|[Avaliação de vulnerabilidades nativa](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|✔|Sim|
|[Monitoramento de integridade do arquivo](security-center-file-integrity-monitoring.md)|✔|✔|✔|Sim|
|[Controles de aplicativo adaptáveis](security-center-adaptive-application.md)|✔|-|✔|Sim|
|[Mapa de rede](security-center-network-recommendations.md#network-map)|✔|✔|-|Sim|
|[Proteção de rede adaptável](security-center-adaptive-network-hardening.md)|✔|-|-|Sim|
|[Painel de conformidade regulatória e relatórios](security-center-compliance-dashboard.md)|✔|✔|✔|Sim|
|Recomendações e proteção contra ameaças em contêineres de IaaS hospedados pelo Docker|-|-|-|Sim|
|Avaliação de patches ausente do sistema operacional|✔|✔|✔|Azure: Não<br><br>Habilitado para Arc: Sim|
|Avaliação de configurações incorretas de segurança|✔|✔|✔|Azure: Não<br><br>Habilitado para Arc: Sim|
|[Avaliação da proteção de ponto de extremidade](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure: Não<br><br>Habilitado para Arc: Sim|
|Avaliação da criptografia de disco|✔</br>(para [cenários compatíveis](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios))|✔|-|Não|
|Avaliação de vulnerabilidades de terceiros|✔|-|✔|Não|
|[Avaliação de segurança de rede](security-center-network-recommendations.md)|✔|✔|-|Não|


### <a name="linux-machines"></a>[**Máquinas do Linux**](#tab/features-linux)

|**Recurso**|**Máquinas Virtuais do Azure**|**Conjuntos de dimensionamento de máquina virtual do Azure**|**Computadores habilitados para o Azure Arc**|**Azure Defender necessário**
|----|:----:|:----:|:----:|:----:|
|[Integração do Microsoft Defender para Ponto de Extremidade](security-center-wdatp.md)|-|-|-|-|
|[Análise comportamental de máquina virtual (e alertas de segurança)](./azure-defender.md)|✔</br>(em versões compatíveis)|✔</br>(em versões compatíveis)|✔|Sim|
|[Alertas de segurança sem arquivo](alerts-reference.md#alerts-windows)|-|-|-|Sim|
|[Alertas de segurança baseados em rede](other-threat-protections.md#network-layer)|✔|✔|-|Sim|
|[Acesso à VM Just-In-Time](security-center-just-in-time.md)|✔|-|-|Sim|
|[Avaliação de vulnerabilidades nativa](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|✔|Sim|
|[Monitoramento de integridade do arquivo](security-center-file-integrity-monitoring.md)|✔|✔|✔|Sim|
|[Controles de aplicativo adaptáveis](security-center-adaptive-application.md)|✔|-|✔|Sim|
|[Mapa de rede](security-center-network-recommendations.md#network-map)|✔|✔|-|Sim|
|[Proteção de rede adaptável](security-center-adaptive-network-hardening.md)|✔|-|-|Sim|
|[Painel de conformidade regulatória e relatórios](security-center-compliance-dashboard.md)|✔|✔|✔|Sim|
|Recomendações e proteção contra ameaças em contêineres de IaaS hospedados pelo Docker|✔|✔|✔|Sim|
|Avaliação de patches ausente do sistema operacional|✔|✔|✔|Azure: Não<br><br>Habilitado para Arc: Sim|
|Avaliação de configurações incorretas de segurança|✔|✔|✔|Azure: Não<br><br>Habilitado para Arc: Sim|
|[Avaliação da proteção de ponto de extremidade](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Não|
|Avaliação da criptografia de disco|✔</br>(para [cenários compatíveis](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios))|✔|-|Não|
|Avaliação de vulnerabilidades de terceiros|✔|-|✔|Não|
|[Avaliação de segurança de rede](security-center-network-recommendations.md)|✔|✔|-|Não|

--- 


> [!TIP]
>Para experimentar os recursos que só estão disponíveis no Azure Defender, inscreva-se em uma avaliação de 30 dias. Para saber mais, confira a [página de preço](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="supported-endpoint-protection-solutions"></a>Soluções de proteção de ponto de extremidade com suporte <a name="endpoint-supported"></a>

A tabela a seguir fornece uma matriz de:

 - Se você pode usar a Central de Segurança do Azure para instalar cada solução para você.
 - Quais soluções de proteção de ponto de extremidade a Central de Segurança pode descobrir. Se uma solução de proteção de ponto de extremidade dessa lista for descoberta, a Central de Segurança não recomendará a instalação de uma.

Para obter informações sobre quando as recomendações são geradas para cada uma dessas proteções, confira [Avaliação e recomendações de proteção de ponto de extremidade](security-center-endpoint-protection.md).

| Endpoint Protection| Plataformas | Instalação da Central de Segurança | Descoberta da Central de Segurança |
|------|------|-----|-----|
| Microsoft Defender Antivírus| Windows Server 2016 ou posterior| Não, Integrado no SO| Sim |
| System Center Endpoint Protection (antimalware da Microsoft) | Windows Server 2012 R2, 2012, 2008 R2 (consulte a observação abaixo) | Via extensão | Sim |
| Trend Micro – Deep Security | Família Windows Server  | Não | Sim |
| Symantec v12.1.1100+| Família Windows Server  | Não | Sim |
| McAfee v10+ | Família Windows Server  | Não | Sim |
| McAfee v10+ | Família de servidores Linux  | Não | Sim |
| Sophos V9 e posterior| Família de servidores Linux  | Não | Sim |

> [!NOTE]
> A detecção do SCEP (System Center Endpoint Protection) em uma máquina virtual do Windows Server 2008 R2 exige a instalação do SCEP após o PowerShell (v3.0 ou mais recente).



## <a name="feature-support-in-government-clouds"></a>Suporte a recursos em nuvens governamentais

| Serviço/Recurso | Gov dos EUA | Governo da China |
|------|:----:|:----:|
|[Acesso à VM Just-In-Time](security-center-just-in-time.md) (1)|✔|✔|
|[Monitoramento de Integridade do arquivo](security-center-file-integrity-monitoring.md) (1)|✔|✔|
|[Controles de aplicativos adaptáveis](security-center-adaptive-application.md) (1)|✔|✔|
|[Proteção de rede adaptável](security-center-adaptive-network-hardening.md) (1)|-|-|
|[Proteção de host do Docker](harden-docker-hosts.md) (1)|✔|✔|
|[Avaliação de vulnerabilidades integrada para computadores](deploy-vulnerability-assessment-vm.md) (1)|-|-|
|[Microsoft Defender para Ponto de extremidade](harden-docker-hosts.md) (1)|✔|-|
|[Conectar à conta da AWS](quickstart-onboard-aws.md) (1)|-|-|
|[Conectar contas GCP](quickstart-onboard-gcp.md) (1)|-|-|
|[Exportação contínua](continuous-export.md)|✔|✔ (2)|
|[Automação do fluxo de trabalho](workflow-automation.md)|✔ (2)|✔|
|[Regras de isenção de recomendação](exempt-resource.md)|-|-|
|[Regras de supressão de alertas](alerts-suppression-rules.md)|✔|✔|
|[Notificações por email para alertas de segurança](security-center-provide-security-contact-details.md)|✔|✔|
|[Inventário de ativos](asset-inventory.md)|✔|✔|
|[Azure Defender for Serviço de Aplicativo](defender-for-app-service-introduction.md)|-|-|
|[Azure Defender para Armazenamento](defender-for-storage-introduction.md)|✔|-|
|[Azure Defender para SQL](defender-for-sql-introduction.md)|✔|✔ (2)|
|[Azure Defender para Key Vault](defender-for-key-vault-introduction.md)|-|-|
|[Azure Defender para Resource Manager](defender-for-resource-manager-introduction.md)|-|-|
|[Azure Defender para DNS](defender-for-dns-introduction.md)|-|-|
|[Azure Defender para registros de contêiner](defender-for-container-registries-introduction.md)|✔ (2)|✔ (2)|
|[Azure Defender para Kubernetes](defender-for-kubernetes-introduction.md)|✔ (2)|✔|
|[Proteção de cargas de trabalho do Kubernetes](kubernetes-workload-protections.md)|-|-|
|||

(1) Exige o **Azure Defender para servidores**

(2) Parcial


## <a name="next-steps"></a>Próximas etapas

- Saiba como a [Central de Segurança coleta dados usando o Agente do Log Analytics](security-center-enable-data-collection.md).
- Saiba como a [Central de Segurança gerencia e protege os dados](security-center-data-security.md).
- Examine as [plataformas de que dão suporte à Central de Segurança](security-center-os-coverage.md).