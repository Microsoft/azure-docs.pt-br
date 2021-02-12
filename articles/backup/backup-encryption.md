---
title: Criptografia no Backup do Azure
description: Saiba como os recursos de criptografia no backup do Azure ajudam a proteger seus dados de backup e atender às necessidades de segurança de sua empresa.
ms.topic: conceptual
ms.date: 08/04/2020
ms.custom: references_regions
ms.openlocfilehash: c9b1b2782a34285ae194f2998a7cd053cf3c0c70
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325665"
---
# <a name="encryption-in-azure-backup"></a>Criptografia no Backup do Azure

Todos os dados submetidos a backup são criptografados automaticamente quando armazenados na nuvem usando a criptografia de armazenamento do Azure, o que ajuda você a atender aos seus compromissos de segurança e conformidade. Esses dados em repouso são criptografados usando a criptografia AES de 256 bits, uma das codificações de bloco mais fortes disponíveis e é compatível com o FIPS 140-2. Além da criptografia em repouso, todos os dados de backup em trânsito são transferidos por HTTPS. Ele sempre permanece na rede de backbone do Azure.

## <a name="levels-of-encryption-in-azure-backup"></a>Níveis de criptografia no backup do Azure

O backup do Azure inclui criptografia em dois níveis:

- **Criptografia de dados no cofre dos serviços de recuperação**
  - **Usando chaves gerenciadas por plataforma**: por padrão, todos os seus dados são criptografados usando chaves gerenciadas por plataforma. Você não precisa executar nenhuma ação explícita do seu fim para habilitar essa criptografia. Ele se aplica a todas as cargas de trabalho cujo backup está sendo feito em seu cofre dos Serviços de Recuperação.
  - **Usando chaves gerenciadas pelo cliente**: ao fazer backup de suas máquinas virtuais do Azure, você pode optar por criptografar seus dados usando chaves de criptografia de propriedade e gerenciadas por você. O backup do Azure permite que você use suas chaves RSA armazenadas no Azure Key Vault para criptografar seus backups. A chave de criptografia usada para criptografar backups pode ser diferente da usada para a origem. Os dados são protegidos usando uma DEK (chave de criptografia de dados) baseada em AES 256, que é, por sua vez, protegida usando suas chaves. Isso lhe dá controle total sobre os dados e as chaves. Para permitir a criptografia, é necessário que você conceda ao cofre dos serviços de recuperação acesso à chave de criptografia na Azure Key Vault. Você pode desabilitar a chave ou revogar o acesso sempre que necessário. No entanto, você deve habilitar a criptografia usando as chaves antes de tentar proteger todos os itens para o cofre. [Saiba mais aqui](encryption-at-rest-with-cmk.md).
  - **Criptografia no nível da infraestrutura**: além de criptografar seus dados no cofre dos serviços de recuperação usando chaves gerenciadas pelo cliente, você também pode optar por ter uma camada adicional de criptografia configurada na infraestrutura de armazenamento. Essa criptografia de infraestrutura é gerenciada pela plataforma. Junto com a criptografia em repouso usando chaves gerenciadas pelo cliente, ela permite a criptografia de duas camadas de seus dados de backup. A criptografia de infraestrutura só poderá ser configurada se você optar pela primeira vez para usar suas próprias chaves para criptografia em repouso. A criptografia de infraestrutura usa chaves gerenciadas por plataforma para criptografar dados.
- **Criptografia específica para a carga de trabalho cujo backup está sendo feito**  
  - **Backup de máquina virtual do Azure**: o backup do Azure dá suporte ao backup de VMs com discos criptografados usando [chaves gerenciadas por plataforma](../virtual-machines/disk-encryption.md#platform-managed-keys), bem como [chaves gerenciadas pelo cliente](../virtual-machines/disk-encryption.md#customer-managed-keys) de propriedade e gerenciadas por você. Além disso, você também pode fazer backup de suas máquinas virtuais do Azure que têm seu sistema operacional ou discos de dados criptografados usando [Azure Disk Encryption](backup-azure-vms-encryption.md#encryption-support-using-ade). O ADE usa o BitLocker para VMs do Windows e DM-Crypt para VMs do Linux, para executar a criptografia no convidado.

>[!NOTE]
>A criptografia de infraestrutura está atualmente em visualização limitada e está disponível no leste dos EUA, nos EUA West2, centro-sul dos EUA, US Gov Arizona e nas regiões do GOV. EUA. Se você quiser usar o recurso em qualquer uma dessas regiões, preencha [este formulário](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUN0VHNEpJS0ZUWklUNVdJSTEzR0hIOVRMVC4u) e envie um email para [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

## <a name="next-steps"></a>Próximas etapas

- [Criptografia do Armazenamento do Azure para dados em repouso](../storage/common/storage-service-encryption.md)
- [Perguntas frequentes sobre o backup do Azure](backup-azure-backup-faq.md#encryption) para perguntas que você possa ter sobre criptografia