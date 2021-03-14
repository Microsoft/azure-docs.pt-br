---
title: Visão geral do Azure Defender e os planos disponíveis
description: Saiba mais sobre os planos, as proteções e os alertas do Azure Defender. Depois, habilite o Azure Defender nas suas assinaturas para ter segurança avançada.
author: memildin
ms.author: memildin
ms.date: 9/30/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: aafd4c6695101042cb30a44e1d2bd30611256779
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096149"
---
# <a name="introduction-to-azure-defender"></a>Introdução ao Azure Defender

Os recursos da Central de Segurança do Azure abrangem os dois pilares mais amplos da segurança na nuvem:

- **GPSN (gerenciamento da postura de segurança na nuvem)** – A Central de Segurança está disponível **gratuitamente** para todos os usuários do Azure. A experiência gratuita inclui recursos de GPSN, como classificação de segurança, detecção de configurações incorretas de segurança em seus computadores do Azure, inventário de ativos e muito mais. Use esses recursos de GPSN para fortalecer sua postura de nuvem híbrida e controlar a conformidade com as políticas internas.

- **PCTN (proteção de cargas de trabalho na nuvem)** – A PPCTN (plataforma de proteção de cargas de trabalho na nuvem) integrada da Central de Segurança, o **Azure Defender**, oferece proteção avançada e inteligente para cargas de trabalho e recursos híbridos do Azure. A habilitação do Azure Defender oferece uma série de recursos de segurança adicionais, conforme descrito nesta página. Além das políticas internas, ao habilitar qualquer plano do Azure Defender, você pode adicionar políticas e iniciativas personalizadas. Você pode adicionar padrões regulatórios, como NIST e Azure CIS, bem como o Azure Security Benchmark para obter uma exibição verdadeiramente personalizada da sua conformidade.

O painel do Azure Defender na Central de Segurança fornece visibilidade e controle dos recursos de PCTN em seu ambiente:

:::image type="content" source="./media/azure-defender/sample-defender-dashboard.png" alt-text="Um exemplo do painel do Azure Defender" lightbox="./media/azure-defender/sample-defender-dashboard.png":::

## <a name="what-resource-types-can-azure-defender-secure"></a>Quais tipos de recursos o Azure Defender pode proteger?

O Azure Defender fornece alertas de segurança e proteção avançada contra ameaças para máquinas virtuais, bancos de dados SQL, contêineres, aplicativos Web, sua rede, entre outros.

Quando você habilita o Azure Defender na área **Preços e configurações** da Central de Segurança do Azure, os seguintes planos do Defender são todos habilitados simultaneamente e fornecem proteções abrangentes para as camadas de computação, dados e serviço do seu ambiente:

- [Azure Defender para servidores](defender-for-servers-introduction.md)
- [Azure Defender for Serviço de Aplicativo](defender-for-app-service-introduction.md)
- [Azure Defender para Armazenamento](defender-for-storage-introduction.md)
- [Azure Defender para SQL](defender-for-sql-introduction.md)
- [Azure Defender para Kubernetes](defender-for-kubernetes-introduction.md)
- [Azure Defender para registros de contêiner](defender-for-container-registries-introduction.md)
- [Azure Defender para Key Vault](defender-for-key-vault-introduction.md)
- [Azure Defender para Resource Manager](defender-for-resource-manager-introduction.md)
- [Azure Defender para DNS](defender-for-dns-introduction.md)

Cada um desses planos é explicado separadamente na documentação da Central de Segurança.

> [!TIP]
> O Azure Defender para IoT (versão prévia) é um produto separado. Você encontrará todos os detalhes em [Como introduzir o Azure Defender para IoT (versão prévia)](../defender-for-iot/overview.md). 

## <a name="hybrid-cloud-protection"></a>Proteção de nuvem híbrida

Além de defender seu ambiente do Azure, você pode adicionar funcionalidades do Azure Defender ao seu ambiente de nuvem híbrida:

- Proteger seus servidores não Azure
- Proteger suas máquinas virtuais em outras nuvens (como a AWS e o GCP)

Você obterá inteligência contra ameaças personalizada e alertas priorizados de acordo com seu ambiente específico, de modo que possa se concentrar no que é mais importante.

Para estender a proteção para máquinas virtuais e bancos de dados SQL em outras nuvens ou no local, implante o [Azure Arc](https://azure.microsoft.com/services/azure-arc/) e habilite o Azure Defender. O Azure Arc para servidores é um serviço gratuito, mas os serviços usados nos servidores habilitados para Arc, por exemplo, o Azure Defender, serão cobrados de acordo com o preço do serviço. Saiba mais em [Adicionar computadores não Azure com o Azure Arc](quickstart-onboard-machines.md#add-non-azure-machines-with-azure-arc).

> [!TIP]
> O conector nativo para AWS lida de forma transparente com a implantação do Azure Arc para você. Saiba mais em [Conectar as contas do AWS à Central de Segurança do Azure](quickstart-onboard-aws.md).



## <a name="azure-defender-security-alerts"></a>Alertas de segurança do Azure Defender 

Quando o Azure Defender detecta uma ameaça em qualquer área do seu ambiente, ele gera um alerta de segurança. Esses alertas descrevem os detalhes dos recursos afetados, as etapas de correção sugeridas e, em alguns casos, uma opção para disparar um aplicativo lógico em resposta.

Seja um alerta for gerado pela Central de Segurança ou recebido pela Central de Segurança de um produto de segurança integrada, você pode exportá-lo. Para exportar seus alertas para o Azure Sentinel, qualquer SIEM de terceiros ou qualquer outra ferramenta externa, siga as instruções em [Transmitir alertas para uma solução de Gerenciamento de Serviço de TI, SOAR ou SIEM](export-to-siem.md).

> [!NOTE]
> Alertas de fontes diferentes podem levar diferentes quantidades de tempo para serem exibidos. Por exemplo, alertas que exigem análise de tráfego de rede podem levar mais tempo para aparecer do que alertas relacionados a processos suspeitos em execução em máquinas virtuais.


## <a name="azure-defender-advanced-protection-capabilities"></a>Funcionalidades de proteção avançada do Azure Defender

O Azure Defender usa análises avançadas para fornecer recomendações personalizadas relacionadas aos seus recursos. 

As proteções incluem proteger as portas de gerenciamento das suas VMs com o acesso just-in-time e controles de aplicativo adaptáveis a fim de criar listas de permissões para quais aplicativos devem e não devem ser executados nos computadores. 

Use os blocos proteção avançada do painel do Azure Defender para monitorar e configurar cada uma dessas proteções. 

## <a name="vulnerability-assessment-and-management"></a>Avaliação e gerenciamento de vulnerabilidades

O Azure Defender inclui a verificação de vulnerabilidades para máquinas virtuais e registros de contêiner sem custo adicional. Os verificadores são fornecidos pela Qualys, mas você não precisa ter uma licença nem de uma conta da Qualys: a Central de Segurança cuida de tudo isso perfeitamente. 

Examine as descobertas desses verificadores de vulnerabilidade e responda a todas elas na Central de Segurança. Isso aproxima a Central de Segurança de se tornar um só painel de controle para todos os seus esforços de segurança na nuvem.

Saiba mais nas seguintes páginas:

- [Solução de avaliação de vulnerabilidades integrada da Central de Segurança para máquinas virtuais do Azure](deploy-vulnerability-assessment-vm.md)
- [Identificar as vulnerabilidades em imagens em registros de contêiner do Azure](defender-for-container-registries-usage.md#identify-vulnerabilities-in-images-in-other-container-registries)



## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu mais sobre os benefícios do Azure Defender. 

> [!div class="nextstepaction"]
> [Habilitar o Azure Defender](enable-azure-defender.md)