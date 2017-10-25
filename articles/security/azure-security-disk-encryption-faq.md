---
title: Perguntas Frequentes sobre o Azure Disk Encryption | Microsoft Docs
description: Este artigo fornece respostas a perguntas frequentes sobre o Microsoft Azure Disk Encryption para VMs IaaS Windows e Linux.
services: security
documentationcenter: na
author: deventiwari
manager: avibm
editor: yuridio
ms.assetid: 7188da52-5540-421d-bf45-d124dee74979
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2017
ms.author: devtiw
ms.openlocfilehash: 2ccadfdec0e653264671f5a9a38d4541b0fc4e69
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-disk-encryption-faq"></a>Perguntas frequentes sobre o Azure Disk Encryption

Este artigo fornece respostas a perguntas frequentes sobre o Azure Disk Encryption para VMs IaaS Windows e Linux. Para saber mais sobre esse serviço, confira [Azure Disk Encryption para VMs IaaS Windows e Linux](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

## <a name="general-questions"></a>Perguntas gerais
**P:** Onde o Azure Disk Encryption está na GA (disponibilidade geral)?

**R:** O Azure Disk Encryption para VMs IaaS Windows e Linux está na disponibilidade geral em todas as regiões públicas do Azure.

**P:** Quais experiências de usuário estão disponíveis com o Azure Disk Encryption?

**R:** A GA do Azure Disk Encryption dá suporte a modelos do Azure Resource Manager, ao Azure PowerShell e à CLI do Azure. Isso proporciona muita flexibilidade. Você tem três opções diferentes para habilitar a criptografia de disco para as VMs IaaS. Para saber mais sobre a experiência do usuário e as orientações passo a passo disponíveis no Azure Disk Encryption, confira os cenários de implantação e experiências do Azure Disk Encryption.

**P:** Quanto custa o Azure Disk Encryption?

**R:** Não são cobradas taxas para a criptografia de discos de VM com o Azure Disk Encryption.

**P:** A quais camadas de máquina virtual o Azure Disk Encryption dá suporte?

**R:** O Azure Disk Encryption está disponível nas VMs da camada padrão, incluindo as VMs IaaS da série [A, D, DS, G, GS e F](https://azure.microsoft.com/pricing/details/virtual-machines/). Ele também está disponível para VMs com armazenamento premium. Ele não está disponível em VMs da camada básica.

**P:** A quais distribuições Linux o Azure Disk Encryption dá suporte?

**R:** O Azure Disk Encryption tem suporte nas seguintes distribuições e versões do servidor Linux:

| Distribuição Linux | Versão | Tipo de volume suportado para criptografia|
| --- | --- |--- |
| Ubuntu | 16.04-LTS-DIÁRIO | SO e disco de dados |
| Ubuntu | 14.04.5-LTS-DIÁRIO | SO e disco de dados |
| RHEL | 7.3 | SO e disco de dados |
| RHEL | 7,2 | SO e disco de dados |
| RHEL | 6,8 | SO e disco de dados |
| RHEL | 6.7 | Disco de dados |
| CentOS | 7.3 | SO e disco de dados |
| CentOS | 7.2n | SO e disco de dados |
| CentOS | 6,8 | SO e disco de dados |
| CentOS | 7.1 | Disco de dados |
| CentOS | 7.0 | Disco de dados |
| CentOS | 6.7 | Disco de dados |
| CentOS | 6.6 | Disco de dados |
| CentOS | 6.5 | Disco de dados |
| openSUSE | 13.2 | Disco de dados |
| SLES | 12 SP1 | Disco de dados |
| SLES | Prioridade: 12-SP1 | Disco de dados |
| SLES | HPC 12 | Disco de dados |
| SLES | Prioridade: 11-SP4 | Disco de dados |
| SLES | 11 SP4 | Disco de dados |

**P:** Como posso começar a usar o Azure Disk Encryption?

**R:** Para começar, leia o white paper [Azure Disk Encryption para VMs IaaS Windows e Linux](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

**P:** Posso criptografar volumes de dados e de inicialização com o Azure Disk Encryption?

**R:** Sim, você pode criptografar volumes de inicialização e de dados para VMs IaaS Windows e Linux. Em VMs Windows, você não pode criptografar os dados sem primeiro criptografar o volume do SO. Em VMs Linux, você pode criptografar o volume de dados sem criptografar o volume do SO primeiro. Depois de criptografar o volume do SO para Linux, não há mais suporte para desabilitar a criptografia de um volume de SO para VMs IaaS Linux.

**P:** O Azure Disk Encryption permite o recurso BYOK (bring your own key)?

**R:** Sim, você pode fornecer suas próprias chaves de criptografia de chave. Essas chaves ficam protegidas no Azure Key Vault, que é o repositório de chaves do Azure Disk Encryption. Para obter mais detalhes sobre os cenários de suporte a chaves de criptografia de chave, confira as experiências e os cenários de implantação do Azure Disk Encryption.

**P:** Posso usar uma chave de criptografia de chave criada pelo Azure?

**R:** Sim, você pode usar o Azure Key Vault para gerar uma chave de criptografia de chave para ser usada pela criptografia de disco do Azure. Essas chaves ficam protegidas no Azure Key Vault, que é o repositório de chaves do Azure Disk Encryption. Para obter mais detalhes sobre os cenários de suporte a chaves de criptografia de chave, confira as experiências e os cenários de implantação do Azure Disk Encryption.

**P:** Posso usar um serviço de gerenciamento de chaves local ou HSM para proteger as chaves de criptografia?

**R:** Você não pode usar o serviço de gerenciamento de chaves local ou HSM para proteger as chaves de criptografia com o Azure Disk Encryption. Você só pode usar o serviço Azure Key Vault para proteger as chaves de criptografia. Para obter mais detalhes sobre os cenários de suporte a chaves de criptografia de chave, confira as experiências e os cenários de implantação do Azure Disk Encryption.

**P:** Quais são os pré-requisitos para configurar o Azure Disk Encryption?

**R:** Há um script do PowerShell como pré-requisito. Com esse script, você pode criar um aplicativo do Azure Active Directory, criar um novo cofre de chaves ou configurar um cofre de chaves existente para acesso à criptografia de disco a fim de habilitar a criptografia e a proteção de segredos e chaves. Para saber mais sobre os cenários de suporte a chaves de criptografia de chave, confira os pré-requisitos e cenários e experiências de implantação do Azure Disk Encryption.

**P:** Onde posso saber mais sobre como usar o PowerShell para configurar o Azure Disk Encryption?

**R:** Temos alguns ótimos artigos sobre como executar tarefas básicas do Azure Disk Encryption e cenários mais avançados. Para as tarefas básicas, confira [Explore Azure Disk Encryption with Azure PowerShell – Part 1](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/explore-azure-disk-encryption-with-azure-powershell/) (Explorar o Azure Disk Encryption com o Azure PowerShell – Parte 1). Para cenários mais avançados, confira [Explore Azure Disk Encryption with Azure PowerShell – Part 2](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2/) (Explorar o Azure Disk Encryption com o Azure PowerShell – Parte 2).

**P:** À qual versão do Azure PowerShell o Azure Disk Encryption dá suporte?

**R:** Use a versão mais recente do SDK do Azure PowerShell para configurar o Azure Disk Encryption. Baixe a última versão do [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). O Azure Disk Encryption *não* é suportado pela versão 1.1.0 do SDK do Azure.

> [!NOTE]
> A extensão de versão prévia do Azure Disk Encryption foi preterida. Para obter detalhes, confira [Deprecating Azure disk encryption preview extension for Linux IaaS VMs](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/12/deprecating-azure-disk-encryption-preview-extension-for-linux-iaas-vms/) (Substituindo a versão prévia da extensão de criptografia para VMs IaaS Linux).

**P:** Posso aplicar o Azure Disk Encryption à minha imagem personalizada do Linux?

**R:** Você não pode aplicar o Azure Disk Encryption à imagem personalizada do Linux. Há suporte apenas para imagens da galeria Linux nas distribuições suportadas indicadas anteriormente. No momento, não há suporte para imagens personalizadas do Linux.

**P:** Posso aplicar atualizações a uma VM Red Hat Linux que usa a atualização do yum?

**R:** Sim, você pode executar uma atualização ou aplicar patch em uma VM do Red Hat Linux. Para saber mais, confira [Applying updates to an encrypted Azure IaaS Red Hat VM by using the yum update](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/13/applying-updates-to-a-encrypted-azure-iaas-red-hat-vm-using-yum-update/) (Aplicando atualizações a uma VM Red Hat IaaS do Azure criptografada usando a atualização do yum).

**P:** O que é o fluxo de trabalho recomendado de criptografia de disco do Azure recomendado para Linux?

**R:** O seguinte fluxo de trabalho é recomendado para ter os melhores resultados no Linux:
* Inicie na imagem da galeria de estoque não modificada que corresponde à distribuição e à versão desejadas do sistema operacional
* Faça backup de todas as unidades montadas que serão criptografadas.  Isso permite a recuperação em caso de falha, por exemplo, se a VM for reiniciada antes de a criptografia ser concluída.
* Criptografar (pode levar várias horas ou mesmo dias dependendo das características da VM e do tamanho dos discos de dados anexados)
* Personalizar e adicionar o software à imagem conforme necessário.

Se esse fluxo de trabalho não for possível, depender de SSE [(Criptografia do Serviço de Armazenamento)](https://docs.microsoft.com/en-us/azure/storage/common/storage-service-encryption) na camada da conta de armazenamento de plataforma poderá ser uma alternativa para criptografia de disco cheio usando dm-crypt.

**P:** O que é o disco "Volume Bek" ou "/mnt/azure_bek_disk"?

**R:** "Volume Bek" para Windows ou "/mnt/azure_bek_disk" para Linux é um volume de dados local que armazena com segurança as chaves de criptografia para VMs de IaaS do Azure Criptografadas.
> [!NOTE]
> Não exclua ou edite nenhum conteúdo neste disco. Não desmonte o disco, uma vez que a presença da chave de criptografia é necessária para operações de criptografia na VM IaaS.

**P:** Onde posso fazer perguntas ou fornecer comentários?

**R:** Você pode fazer perguntas ou fornecer comentários no [Fórum do Azure Disk Encryption](https://social.msdn.microsoft.com/Forums/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu mais sobre as perguntas mais frequentes relativas ao Azure Disk Encryption. Para saber mais sobre esse serviço e seus recursos, confira os seguintes artigos:

- [Aplicar a criptografia de disco na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Criptografar uma máquina virtual do Azure](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [Criptografia de dados em repouso do Azure](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
