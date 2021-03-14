---
title: Recomendações de segurança para máquinas virtuais no Azure
description: Aplique essas recomendações para VMs no Azure para ajudar a atender às obrigações de segurança descritas no modelo de responsabilidade compartilhada e para melhorar a segurança geral de suas implantações.
author: msmbaldwin
manager: RKarlin
ms.service: virtual-machines
ms.subservice: security
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.reviewer: cynthn
ms.openlocfilehash: 622ebfed8294dbcb441aa509fc4f6ba75114f28d
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102499349"
---
# <a name="security-recommendations-for-virtual-machines-in-azure"></a>Recomendações de segurança para máquinas virtuais no Azure


Este artigo contém recomendações de segurança para máquinas virtuais do Azure. Siga estas recomendações para ajudar a atender as obrigações de segurança descritas em nosso modelo para responsabilidade compartilhada. As recomendações também ajudarão você a melhorar a segurança geral para suas soluções de aplicativo Web. Para obter mais informações sobre o que a Microsoft faz para atender às responsabilidades do provedor de serviços, consulte [responsabilidades compartilhadas para computação em nuvem](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).

Algumas das recomendações deste artigo podem ser abordadas automaticamente pela central de segurança do Azure. A central de segurança do Azure é a primeira linha de defesa para seus recursos no Azure. Ela analisa periodicamente o estado de segurança de seus recursos do Azure para identificar possíveis vulnerabilidades na segurança. Em seguida, ele recomenda como tratar as vulnerabilidades. Para obter mais informações, consulte [recomendações de segurança na central de segurança do Azure](../security-center/security-center-recommendations.md).

Para obter informações gerais sobre a central de segurança do Azure, consulte [o que é a central de segurança do Azure?](../security-center/security-center-introduction.md).

## <a name="general"></a>Geral

| Recomendação | Comentários | Central de Segurança |
|-|----|--|
| Ao criar imagens de VM personalizadas, aplique as atualizações mais recentes. | Antes de criar imagens, instale as atualizações mais recentes para o sistema operacional e para todos os aplicativos que serão parte da sua imagem.  | - |
| manter as VMs atualizadas. | Você pode usar a solução [Gerenciamento de atualizações](../automation/update-management/overview.md) na automação do Azure para gerenciar atualizações do sistema operacional para seus computadores Windows e Linux no Azure. | [Sim](../security-center/asset-inventory.md) |
| Faça backup de suas VMs. | O [backup do Azure](../backup/backup-overview.md) ajuda a proteger os dados do aplicativo e tem custos operacionais mínimos. Erros de aplicativo podem corromper seus dados e erros humanos podem introduzir bugs em seus aplicativos. O backup do Azure protege suas VMs que executam Windows e Linux. | - |
| Use várias VMs para obter maior resiliência e disponibilidade. | Se sua VM executar aplicativos que devem ser altamente disponíveis, use várias VMs ou [conjuntos de disponibilidade](./availability.md). | - |
| Adote uma estratégia de BCDR (continuidade dos negócios e recuperação de desastre). | Azure Site Recovery permite que você escolha entre diferentes opções criadas para dar suporte à continuidade dos negócios. Ele dá suporte a diferentes cenários de replicação e failover. Para obter mais informações, consulte  [About site Recovery](../site-recovery/site-recovery-overview.md). | - |

## <a name="data-security"></a>Segurança de dados

| Recomendação | Comentários | Central de Segurança |
|-|----|--|
| Criptografar discos do sistema operacional. | [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) ajuda a criptografar seus discos de VM IaaS Windows e Linux. Sem as chaves necessárias, o conteúdo dos discos criptografados não podem ser lidos. A criptografia de disco protege dados armazenados de acesso não autorizado que de outra forma seriam possíveis se o disco fosse copiado.| [Sim](../security-center/asset-inventory.md) |
| Criptografar discos de dados. | [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) ajuda a criptografar seus discos de VM IaaS Windows e Linux. Sem as chaves necessárias, o conteúdo dos discos criptografados não podem ser lidos. A criptografia de disco protege dados armazenados de acesso não autorizado que de outra forma seriam possíveis se o disco fosse copiado.| -  |
| Limite o software instalado. | Limite o software instalado ao que é necessário para aplicar a solução com êxito. Essa diretriz ajuda a reduzir a superfície de ataque da solução. | - |
| Use antivírus ou antimalware. | No Azure, você pode usar o software antimalware de fornecedores de segurança, como Microsoft, Symantec, Trend Micro e Kaspersky. Esse software ajuda a proteger suas VMs contra arquivos mal-intencionados, adware e outras ameaças. Você pode implantar o Microsoft Antimalware com base em suas cargas de trabalho de aplicativo. O Microsoft Antimalware está disponível somente para computadores Windows. Use a configuração básica segura por padrão ou personalizada avançada. Para obter mais informações, consulte [Microsoft antimalware para serviços de nuvem do Azure e máquinas virtuais](../security/fundamentals/antimalware.md). | - |
| Armazene segredos e chaves com segurança. | Simplifique o gerenciamento de seus segredos e chaves fornecendo aos proprietários do aplicativo uma opção segura e gerenciada centralmente. Esse gerenciamento reduz o risco de um comprometimento acidental ou vazamento. Azure Key Vault pode armazenar com segurança suas chaves em HSMs (módulos de segurança de hardware) certificados para o FIPS 140-2 nível 2. Se você precisar usar o FIPs 140,2 nível 3 para armazenar suas chaves e segredos, poderá usar o [HSM dedicado do Azure](../dedicated-hsm/overview.md). | - |

## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso 

| Recomendação | Comentários | Central de Segurança |
|-|----|--|
| Centralize a autenticação de VM. | Você pode centralizar a autenticação de suas VMs Windows e Linux usando [Azure Active Directory autenticação](../active-directory/develop/authentication-vs-authorization.md). | - |

## <a name="monitoring"></a>Monitoramento

| Recomendação | Comentários | Central de Segurança |
|-|----|--|
| Monitore suas VMs. | Você pode usar [Azure monitor para VMs](../azure-monitor/vm/vminsights-overview.md) para monitorar o estado de suas VMs do Azure e conjuntos de dimensionamento de máquinas virtuais. Problemas de desempenho com uma máquina virtual podem levar a interrupção do serviço, o que viola o princípio de segurança de disponibilidade. | - |

## <a name="networking"></a>Rede

| Recomendação | Comentários | Central de Segurança |
|-|----|--|
| Restrinja o acesso às portas de gerenciamento. | Os invasores verificam intervalos de IP de nuvem pública para portas de gerenciamento aberto e tentam ataques "fáceis", como senhas comuns e vulnerabilidades não corrigidas conhecidas. Você pode usar o [acesso à VM just-in-time (JIT)](../security-center/security-center-just-in-time.md) para bloquear o tráfego de entrada para suas VMs do Azure, reduzindo a exposição a ataques ao mesmo tempo em que fornece conexões fáceis às VMs quando elas forem necessárias. | - |
| Limite o acesso à rede. | Os grupos de segurança de rede permitem restringir o acesso à rede e controlar o número de pontos de extremidade expostos. Para obter mais informações, consulte [criar, alterar ou excluir um grupo de segurança de rede](../virtual-network/manage-network-security-group.md). | - |

## <a name="next-steps"></a>Próximas etapas

Verifique com seu provedor de aplicativos para saber mais sobre os requisitos de segurança adicionais. Para obter mais informações sobre como desenvolver aplicativos seguros, consulte [a documentação de desenvolvimento seguro](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/).