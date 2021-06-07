---
title: Contêiner do perfil do FSLogix de armazenamento área de trabalho virtual do Windows – Azure
description: Opções para armazenar seu perfil de FSLogix de área de trabalho virtual do Windows no armazenamento do Azure.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2ec166c1df9727052d4980f5d5758ece8c499880
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99526595"
---
# <a name="storage-options-for-fslogix-profile-containers-in-windows-virtual-desktop"></a>Opções de armazenamento para contêineres de perfil FSLogix na área de trabalho virtual do Windows

O Azure oferece várias soluções de armazenamento que você pode usar para armazenar seu contêiner de perfil do FSLogix. Este artigo compara as soluções de armazenamento oferecidas pelo Azure para contêineres de perfil de usuário FSLogix de área de trabalho virtual do Windows. É recomendável armazenar contêineres de perfil FSLogix em arquivos do Azure para a maioria de nossos clientes.

A área de trabalho virtual do Windows oferece contêineres de perfil de FSLogix como a solução de perfil de usuário recomendada. O FSLogix é projetado para usar perfis móveis em ambientes de computação remota, como área de trabalho virtual do Windows. Ao entrar, esse contêiner é dinamicamente anexado ao ambiente de computação usando um VHD (disco rígido virtual) com suporte nativo e um VHDX (disco rígido virtual) do Hyper-V. O perfil do usuário fica imediatamente disponível e aparece no sistema exatamente como um perfil do usuário nativo.

As tabelas a seguir comparam as soluções de armazenamento ofertas de armazenamento do Azure para perfis de usuário de contêiner do Windows Virtual Desktop FSLogix.

## <a name="azure-platform-details"></a>Detalhes da plataforma Azure

|Recursos|Arquivos do Azure|Azure NetApp Files|Espaços de Armazenamento Direct|
|--------|-----------|------------------|---------------------|
|Caso de uso|Propósito geral|Ultra desempenho ou migração do NetApp local|Plataforma cruzada|
|Serviço de plataforma|Sim, solução nativa do Azure|Sim, solução nativa do Azure|Não, autogerenciado|
|Disponibilidade regional|Todas as regiões|[Selecionar regiões](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)|Todas as regiões|
|Redundância|Localmente redundante/com redundância de zona/geograficamente redundante/com redundância de zona|Localmente redundante|Localmente redundante/com redundância de zona/redundância geográfica|
|Camadas e desempenho| Standard (com otimização de transação)<br>Premium<br>Até máx. de 100 mil IOPS por compartilhamento com 10 GBps por compartilhamento em aproximadamente 3 ms de latência|Standard<br>Premium<br>Ultra<br>Até 320K (16K) IOPS com 4,5 GBps por volume em aproximadamente 1 ms de latência|HDD Standard: até 500 de IOPS por disco limites<br>SSD Standard: até 4K IOPS por disco limites<br>SSD Premium: limites de até 20 mil IOPS por disco<br>Recomendamos discos Premium para Espaços de Armazenamento Diretos|
|Capacity|100 TiB por compartilhamento, até 5 PiB por conta de uso geral |100 TiB por volume, até 12,5 PiB por assinatura|Máximo de 32 TiB por disco|
|Infraestrutura necessária|Tamanho mínimo de compartilhamento 1 GiB|Pool mínimo de capacidade 4 TiB, tamanho mínimo de volume 100 GiB|Duas VMs no Azure IaaS (+ testemunha de nuvem) ou pelo menos três VMs sem e custos para discos|
|Protocolos|SMB 3.0/2.1, NFSv 4.1 (versão prévia), REST|NFSv3, NFSv 4.1 (visualização), SMB 3. x/2. x|NFSv3, NFSv 4.1, SMB 3,1|

## <a name="azure-management-details"></a>Detalhes de gerenciamento do Azure

|Recursos|Arquivos do Azure|Azure NetApp Files|Espaços de Armazenamento Direct|
|--------|-----------|------------------|---------------------|
|Access|Nuvem, local e híbrido (sincronização de arquivos do Azure)|Nuvem, local (via ExpressRoute)|Nuvem, local|
|Backup|Integração de instantâneo do backup do Azure|Instantâneos de Azure NetApp Files|Integração de instantâneo do backup do Azure|
|Segurança e conformidade|[Todos os certificados com suporte do Azure](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|ISO concluído|[Todos os certificados com suporte do Azure](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|
|Integração do Microsoft Azure Active Directory|[Active Directory e Azure Active Directory Domain Services nativos](../storage/files/storage-files-active-directory-overview.md)|[Azure Active Directory Domain Services e Active Directory nativo](../azure-netapp-files/azure-netapp-files-faqs.md#does-azure-netapp-files-support-azure-active-directory)|Somente suporte nativo Active Directory ou Azure Active Directory Domain Services|

Depois de escolher o método de armazenamento, confira [preços da área de trabalho virtual do Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/) para obter informações sobre nossos planos de preços.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre contêineres de perfil do FSLogix, discos de perfil do usuário e outras tecnologias de perfil de usuário, consulte a tabela em [contêineres de perfil do FSLogix e arquivos do Azure](fslogix-containers-azure-files.md).

Se você estiver pronto para criar seus próprios contêineres de perfil do FSLogix, comece com um destes tutoriais:

- [Introdução aos contêineres de perfil do FSLogix nos arquivos do Azure na área de trabalho virtual do Windows](create-file-share.md)
- [Criar um contêiner de perfil do FSLogix para um pool de hosts usando os arquivos do Azure NetApp](create-fslogix-profile-container.md)
- As instruções em [implantar um servidor de arquivos de escalabilidade horizontal de dois nós espaços de armazenamento diretos para o armazenamento UPD no Azure](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) também se aplicam quando você usa um contêiner de perfil FSLogix em vez de um disco de perfil de usuário

Você também pode começar desde o início e configurar sua própria solução de área de trabalho virtual do Windows em [criar um locatário na área de trabalho virtual do Windows](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md).
