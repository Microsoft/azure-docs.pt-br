---
title: Recomendações de segurança para máquinas virtuais no Azure
description: Aplique essas recomendações para máquinas virtuais no Azure para atender às obrigações de segurança descritas no modelo de responsabilidade compartilhada e para aprimorar a segurança geral de suas implantações.
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102499349"
---
# <a name="security-recommendations-for-virtual-machines-in-azure"></a>Recomendações de segurança para máquinas virtuais no Azure


Este artigo contém as recomendações de segurança para as Máquinas Virtuais do Azure. Siga estas recomendações para atender às obrigações de segurança descritas em nosso modelo para responsabilidade compartilhada. As recomendações também ajudarão você a aprimorar a segurança geral para soluções de aplicativo Web. Para obter mais informações sobre o que a Microsoft faz para atender às responsabilidades de provedor de serviços, consulte as [Responsabilidades compartilhadas para computação em nuvem](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).

Algumas das recomendações deste artigo podem ser abordadas automaticamente pela Central de Segurança do Azure. A Central de Segurança do Azure é a primeira linha de defesa para a proteção dos recursos no Azure. Ela analisa periodicamente o estado de segurança de seus recursos do Azure para identificar possíveis vulnerabilidades na segurança. Em seguida, ela recomenda como tratar as vulnerabilidades. Para obter mais informações, consulte as [Recomendações de segurança na Central de Segurança do Azure](../security-center/security-center-recommendations.md).

Para informações gerais sobre a Central de Segurança do Azure, confira [O que é a Central de Segurança do Azure?](../security-center/security-center-introduction.md).

## <a name="general"></a>Geral

| Recomendação | Comentários | Central de Segurança |
|-|----|--|
| Ao criar imagens de VM personalizadas, aplique as atualizações mais recentes. | Antes de criar imagens, instale as atualizações mais recentes para o sistema operacional e para todos os aplicativos que farão parte da sua imagem.  | - |
| manter as VMs atualizadas. | Use a solução [Gerenciamento de Atualizações](../automation/update-management/overview.md) na Automação do Azure para gerenciar as atualizações dos sistemas operacionais Windows e Linux dos computadores com Azure. | [Sim](../security-center/asset-inventory.md) |
| Fazer backup das suas VMs. | O [Backup do Azure](../backup/backup-overview.md) ajuda a proteger os dados do aplicativo e tem custos operacionais mínimos. Erros de aplicativo podem corromper seus dados e erros humanos podem introduzir bugs em seus aplicativos. O Backup do Azure protege suas VMs que executam Windows e Linux. | - |
| Use várias VMs para maior resiliência e disponibilidade. | Se a sua VM executar aplicativos que devem ter alta disponibilidade, use várias VMs ou vários [conjuntos de disponibilidade](./availability.md). | - |
| Adote uma estratégia de continuidade dos negócios e recuperação de desastres (BCDR). | O Azure Site Recovery permite que você escolha entre diferentes opções criadas para dar suporte à continuidade dos negócios. Ele dá suporte a diferentes cenários de replicação e de failover. Para obter mais informações, confira [Sobre o Site Recovery](../site-recovery/site-recovery-overview.md). | - |

## <a name="data-security"></a>Segurança de dados

| Recomendação | Comentários | Central de Segurança |
|-|----|--|
| Criptografar discos do sistema operacional. | O [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) ajuda você a criptografar seus discos de VM IaaS do Windows e do Linux. Sem as chaves adequadas, o conteúdo dos discos criptografados não pode ser lidos. A criptografia de disco protege os dados armazenados de acessos não autorizados que poderiam acontecer se o disco fosse copiado.| [Sim](../security-center/asset-inventory.md) |
| Criptografar discos de dados. | O [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) ajuda você a criptografar seus discos de VM IaaS do Windows e do Linux. Sem as chaves adequadas, o conteúdo dos discos criptografados não pode ser lidos. A criptografia de disco protege os dados armazenados de acessos não autorizados que poderiam acontecer se o disco fosse copiado.| -  |
| Limitar softwares instalados. | Limite a instalação de software somente ao que é necessário para aplicar a sua solução com sucesso. Essa diretriz ajuda a reduzir a superfície de ataque da sua solução. | - |
| Usar antivírus ou antimalware. | Com o Azure, você pode usar um software antimalware de fornecedores de segurança, como a Microsoft, a Symantec, a Trend Micro e a Kaspersky. Esse software ajuda a proteger suas VMs contra arquivos mal-intencionados, adware e outras ameaças. Você pode implantar o Microsoft Antimalware com base nas cargas de trabalho dos aplicativos. O Microsoft Antimalware está disponível somente para computadores Windows. Use a configuração básica padrão de segurança ou personalizada avançada. Para obter detalhes, consulte [Microsoft Antimalware para Serviços de Nuvem e Máquinas Virtuais do Azure](../security/fundamentals/antimalware.md). | - |
| Armazenar segredos e chaves com segurança. | Simplifique o gerenciamento de chaves e segredos oferecendo aos proprietários do aplicativo uma opção segura e gerenciada centralmente. Esse gerenciamento reduz o risco de um comprometimento acidental ou um vazamento. O Azure Key Vault pode armazenar com segurança suas chaves em HSMs (módulos de segurança de hardware) certificados para o FIPS 140-2 nível 2. Se você precisa usar o FIPs 140.2 nível 3 para armazenar suas chaves e segredos, você poderá usar o [HSM Dedicado do Azure](../dedicated-hsm/overview.md). | - |

## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso 

| Recomendação | Comentários | Central de Segurança |
|-|----|--|
| Centralizar a autenticação de VM. | Você pode centralizar a autenticação de suas VMs para Windows e Linux usando a [autenticação Azure Active Directory](../active-directory/develop/authentication-vs-authorization.md). | - |

## <a name="monitoring"></a>Monitoramento

| Recomendação | Comentários | Central de Segurança |
|-|----|--|
| Monitorar suas VMs. | Você pode usar o [Azure Monitor para VMs](../azure-monitor/vm/vminsights-overview.md) para monitorar o status das VMs do Azure e dos conjuntos de dimensionamento de máquinas virtuais. Problemas de desempenho com uma máquina virtual podem levar a interrupção do serviço, o que viola o princípio de segurança de disponibilidade. | - |

## <a name="networking"></a>Rede

| Recomendação | Comentários | Central de Segurança |
|-|----|--|
| Restringir o acesso às portas de gerenciamento. | Os invasores examinam intervalos de IP de nuvem pública para localizar portas de gerenciamento abertas e fazer ataques "fáceis", como senhas comuns e vulnerabilidades não corrigidas e já conhecidas. Use o [acesso JIT (just-in-time) à VM](../security-center/security-center-just-in-time.md) para bloquear o tráfego de entrada às suas VMs do Azure, reduzindo a exposição aos ataques ao mesmo tempo em que oferece conexão fácil às VMs quando necessário. | - |
| Limitar o acesso à rede. | Os grupos de segurança de rede permitem restringir o acesso à rede e controlar o número de pontos de extremidade expostos. Para saber mais, confira [Criar, alterar ou excluir um grupo de segurança de rede](../virtual-network/manage-network-security-group.md). | - |

## <a name="next-steps"></a>Próximas etapas

Verifique com seu provedor de aplicativos se há requisitos de segurança adicionais. Para obter mais informações sobre como desenvolver aplicativos seguros, consulte [Documentação de desenvolvimento seguro](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/).