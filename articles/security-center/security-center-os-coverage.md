---
title: Recursos e plataformas compatíveis com a Central de Segurança do Azure | Microsoft Docs
description: Este documento fornece uma lista de recursos e plataformas compatíveis com a Central de Segurança do Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/03/2019
ms.author: v-mohabe
ms.openlocfilehash: 02d993d760338356fa29ee58a03215e14d6583f1
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295599"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Plataformas e recursos compatíveis com a Central de Segurança do Azure

As recomendações e o monitoramento do estado de segurança estão disponíveis para computadores e VMs (máquinas virtuais) criadas com o modelo de implantação do Resource Manager e o modelo clássico.

> [!NOTE]
> Saiba mais sobre os [modelos de implantação clássica e do Gerenciador de Recursos](../azure-classic-rm.md) para recursos do Azure.
>

## <a name="platforms-that-support-the-data-collection-agent"></a>Plataformas que dão suporte ao agente de coleta de dados 

Esta seção lista as plataformas em que o agente da Central de Segurança do Azure pode ser executado e das quais ele pode coletar dados.

### <a name="supported-platforms-for-windows-computers-and-vms"></a>Plataformas com suporte para computadores com Windows e VMs
Os sistemas operacionais Windows a seguir são compatíveis:

* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2
* Windows Server 2008

> [!NOTE]
> A integração com o Windows Defender ATP dá suporte a apenas ao Windows Server 2012 R2 e ao Windows Server 2016.

### <a name="supported-platforms-for-linux-computers-and-vms"></a>Plataformas com suporte para computadores Linux e VMs

Os sistemas operacionais Linux a seguir são compatíveis:

> [!NOTE]
> Como a lista de sistemas operacionais Linux com suporte está em constante mudança, se você preferir, clique [aqui](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) para exibir a lista mais atualizada de versões com suporte, caso haja alterações desde que este tópico foi publicado pela última vez.

64 bits
* CentOS 6 e 7
* Amazon Linux 2017.09
* Oracle Linux 6 e 7
* Red Hat Enterprise Linux Server 6 e 7
* Debian GNU/Linux 8 e 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS e 18.04 LTS
* SUSE Linux Enterprise Server 12

32 bits
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 e 9
* Ubuntu Linux 14, 4 LTS e 16, 4 LTS

## <a name="vms-and-cloud-services"></a>VMs e Serviços de Nuvem
VMs que são executadas em um serviço de nuvem também são compatíveis. Apenas funções Web e de trabalho de serviços de nuvem que são executadas em slots de produção são monitorados. Para saber mais sobre os serviços de nuvem, confira [Visão geral dos Serviços de Nuvem do Azure](../cloud-services/cloud-services-choose-me.md).


## <a name="supported-iaas-features"></a>Recursos de IaaS compatíveis

> [!div class="mx-tableFixed"]
> 

|Servidor|Windows|||Linux|||Preços|
|----|----|----|----|----|----|----|----|
|**Ambiente**|**As tabelas**||**Não Azure**|**As tabelas**||**Não Azure**||
||**Máquina Virtual**|**Conjunto de Dimensionamento de Máquinas Virtuais**||**Máquina Virtual**|**Conjunto de Dimensionamento de Máquinas Virtuais**|
|Alertas de detecção de ameaças VMBA|✔|✔|✔|✔ (em versões compatíveis)|✔ (em versões compatíveis)|✔|Recomendações (gratuito) detecção de ameaças (padrão)|
|Alertas de detecção de ameaças baseadas em rede|✔|✔|X|✔|✔|X|Standard|
|Integração com o Windows Defender ATP|✔ (em versões compatíveis)|✔ (em versões compatíveis)|✔|X|X|X|Standard|
|Patches ausentes|✔|✔|✔|✔|✔|✔|Grátis|
|Configurações de segurança|✔|✔|✔|✔|✔|✔|Grátis|
|Avaliação do Endpoint Protection|✔|✔|✔|X|X|X|Grátis|
|Acesso à VM JIT|✔|X|X|✔|X|X|Standard|
|Controles de aplicativo adaptáveis|✔|X|✔|✔|X|✔|Standard|
|FIM|✔|✔|✔|✔|✔|✔|Standard|
|Avaliação de criptografia de disco|✔|✔|X|✔|✔|X|Grátis|
|Implantação de terceiros|✔|X|X|✔|X|X|Grátis|
|Avaliação do NSG|✔|✔|X|✔|✔|X|Grátis|
|Detecção de ameaças sem arquivo|✔|✔|✔|X|X|X|Standard|
|Mapa de rede|✔|✔|X|✔|✔|X|Standard|
|Controles de rede adaptável|✔|✔|X|✔|✔|X|Standard|
|Painel de conformidade regulatória & relatórios|✔|✔|✔|✔|✔|✔|Standard|
|Recomendações e detecção de ameaças em contêineres de IaaS hospedados pelo Docker|X|X|X|✔|✔|✔|Standard|

### <a name="supported-endpoint-protection-solutions"></a>Soluções de proteção de ponto de extremidade com suporte

A tabela a seguir fornece uma matriz de:
 - Se você pode usar a Central de Segurança do Azure para instalar cada solução para você.
 - Quais soluções de proteção de ponto de extremidade a Central de Segurança pode descobrir. Se uma dessas soluções de proteção de ponto de extremidade for descoberta, a Central de Segurança não recomendará a instalação de uma.

Para obter informações sobre quando as recomendações são geradas para cada uma dessas proteções, consulte [Endpoint Protection avaliação e recomendações](security-center-endpoint-protection.md).

| Proteção do ponto de extremidade| Plataformas | Instalação da Central de Segurança | Descoberta da Central de Segurança |
|------|------|-----|-----|
| Windows Defender (Microsoft Antimalware)| Windows Server 2016| Não, Integrado no SO| Sim |
| System Center Endpoint Protection (antimalware da Microsoft) | Windows Server 2012 R2, 2012, 2008 R2 (consulte a observação abaixo) | Via extensão | Sim |
| Trend Micro – Todas as versões | Família Windows Server  | Não | Sim |
| Symantec v12.1.1100+| Família Windows Server  | Não | Sim |
| McAfee v10+ | Família Windows Server  | Não | Sim |
| Kaspersky| Família Windows Server  | Não | Não  |
| Sophos| Família Windows Server  | Não | Não  |

> [!NOTE]
> - A detecção do System Center Endpoint Protection (SCEP) em uma máquina virtual do Windows Server 2008 R2 exige que o SCEP seja instalado após o PowerShell 3.0 (ou uma versão superior).

## <a name="supported-paas-features"></a>Recursos de PaaS compatíveis


|Serviço|Recomendações (gratuito)|Detecção de ameaças (padrão)|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|Contas de armazenamento de BLOBs do Azure|✔| ✔|
|Serviços de aplicativos|✔| ✔|
|Serviços de Nuvem|✔| X|
|VNets|✔| ND|
|Sub-redes|✔| ND|
|NICs|✔| ND|
|NSGs|✔| ND|
|Assinatura|✔ **| ✔|
|Lote|✔| ND|
|Service fabric|✔| ND|
|Conta de automação|✔| ND|
|Balanceador de carga|✔| ND|
|Pesquisar|✔| ND|
|Barramento de Serviço|✔| ND|
|Stream Analytics|✔| ND|
|Hub de Eventos|✔| ND|
|Aplicativos lógicos|✔| ND|
|Conta de armazenamento|✔| ND|
|Redis|✔| ND|
|Data Lake Analytics|✔| ND|
|Cofre de chaves|✔| ND|




\* Esses recursos são compatíveis atualmente com a versão prévia pública.

\*\*As recomendações do AAD estão disponíveis somente para assinaturas padrão



## <a name="next-steps"></a>Próximas etapas

- Saiba como [planejar e entender as considerações de design para adotar a Central de Segurança do Azure](security-center-planning-and-operations-guide.md).
- Saiba mais sobre a [análise comportamental de máquina virtual e análise de memória de despejo de memória na Central de Segurança](security-center-alerts-type.md#virtual-machine-behavioral-analysis).
- Encontre [perguntas frequentes sobre como usar a Central de Segurança do Azure](security-center-faq.md).
- Encontre [postagens no blog sobre a conformidade e segurança do Azure](https://blogs.msdn.com/b/azuresecurity/).
