---
title: O que há de novo no Servidor de Backup do Azure
description: O Servidor de Backup do Azure oferece recursos avançados de backup para proteger VMs, arquivos e pastas, cargas de trabalho e muito mais. Aprenda a instalar ou atualizar o Servidor de Backup do Azure V3.
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adigan
ms.openlocfilehash: 131f5ae31649bd1973a3048b8b52a451f27522b4
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464996"
---
# <a name="whats-new-in-microsoft-azure-backup-server"></a>O que há de novo no Servidor de Backup do Azure

O Servidor de Backup do Azure versão 3 (MABS V3) é a atualização mais recente e inclui correções de bugs importantes, suporte ao Windows Server 2019, suporte ao SQL 2017 e outros recursos e aprimoramentos. Para ver a lista de bugs corrigidos e as instruções de instalação para o MABS V3, consulte o artigo KB [4457852](https://support.microsoft.com/en-us/help/4457852/microsoft-azure-backup-server-v3).

Os seguintes recursos estão incluídos no MABS V3:

## <a name="volume-to-volume-migration"></a>Migração para o Volume
Com o Armazenamento de Backup Moderno (MBS) no MABS V2, anunciamos o armazenamento com capacidade de carga de trabalho, em que você configura determinadas cargas de trabalho para backup em armazenamento específico, com base nas propriedades de armazenamento. No entanto, após a configuração, você pode achar necessário mover backups de determinadas fontes de dados para outro armazenamento para utilização otimizada de recursos. O MABS V3 oferece a capacidade de migrar seus backups e configurá-los para serem armazenados em um volume diferente em [3 etapas](https://blogs.technet.microsoft.com/dpm/2017/10/24/storage-migration-with-dpm-2016-mbs/).

## <a name="prevent-unexpected-data-loss"></a>Evitar a perda de dados inesperados
Nas empresas, o MABS é gerenciado por uma equipe de administradores. Embora existam diretrizes sobre armazenamento que devem ser usadas para backups, um volume incorreto dado ao MABS como armazenamento de backup pode levar à perda de dados críticos. Com o MABS V3, você pode evitar esses cenários configurando esses volumes como os que não estão disponíveis para armazenamento usando [esses cmdlets do PowerShell](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage).

## <a name="custom-size-allocation"></a>Alocação de tamanho personalizado
O armazenamento de Backup Moderno (MBS) consome armazenamento thinly, como e quando necessário. Para fazer isso, o MABS calcula o tamanho dos dados que estão sendo armazenados em backup quando estão configurados para proteção. No entanto, se muitos arquivos e pastas estiverem sendo submetidos a backup juntos, como no caso de um servidor de arquivos, o cálculo de tamanho pode levar muito tempo. Com o MABS V3, você pode configurar o MABS para aceitar o tamanho do volume como padrão, em vez de calcular o tamanho de cada arquivo, economizando tempo.

## <a name="optimized-cc-for-rct-vms"></a>CC otimizado para VMs RCT
O MABS usa o RCT (o controle de alterações nativo no Hyper-V), o que diminui a necessidade de verificações de consistência demoradas em cenários quando a VM trava. O RCT fornece melhor resiliência do que o rastreamento de alterações fornecido pelos backups baseados em instantâneos do VSS. O MABS V3 otimiza ainda mais o consumo de rede e armazenamento, transferindo apenas os dados alterados durante as verificações de consistência.

## <a name="support-to-tls-12"></a>Suporte ao TLS 1.2
O TLS 1.2 é a maneira segura de comunicação sugerida pela Microsoft com a melhor criptografia de classe. O MABS agora oferece suporte à comunicação TLS 1.2 entre o MABS e os servidores protegidos, para autenticação baseada em certificado e para backups na nuvem.

## <a name="vmware-vm-protection-support"></a>Suporte à proteção de VM do VMware
Agora há suporte para backup de VM do VMware para implantações de produção. O MABS V3 oferece o seguinte para a proteção de VMs VMware:

-   Suporte para vCenter e ESXi 6.5, junto com suporte para 5.5 e 6.0.
- A proteção automática de VMs VMware para a nuvem. Se novas VMs VMware forem adicionadas a uma pasta protegida, elas serão automaticamente protegidas para disco e nuvem.
- Melhorias na eficiência de recuperação para recuperação de localização alternativa do VMware.

## <a name="sql-2017-support"></a>Suporte do SQL 2017
O MABS V3 pode ser instalado com o SQL 2017 como o banco de dados do MABS. Você pode atualizar o servidor SQL do SQL 2016 para o SQL 2017 ou instalá-lo recentemente. Você também pode fazer o backup da carga de trabalho do SQL 2017 em ambientes em cluster e não em cluster com o MABS V3.

## <a name="windows-server-2019-support"></a>Suporte do Windows Server 2019
O MABS V3 pode ser instalado no Windows Server 2019. Para usar o MABS V3 com o WS2019, você pode atualizar seu sistema operacional para o WS2019 antes de instalar/atualizar para o MABS V3 ou pode atualizar seu sistema operacional após instalar/atualizar o V3 no WS2016.

O MABS V3 é uma versão completa e pode ser instalado diretamente no Windows Server 2016, Windows Server de 2019 ou pode ser atualizado do MABS V2. Antes de atualizar ou instalar o Backup Server V3, leia sobre os pré-requisitos de instalação.
Encontre mais informações sobre as etapas de instalação/atualização para o MABS [aqui](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#software-package).


> [!NOTE]
>
> O MABS tem o mesmo código base como o System Center Data Protection Manager. O MABS v3 é equivalente ao Data Protection Manager 1807.

## <a name="next-steps"></a>Próximas etapas

Saiba como preparar seu servidor, ou começar a proteger uma carga de trabalho:
- [Problemas conhecidos no MABS V3](backup-mabs-release-notes-v3.md)
- [Preparar as cargas de trabalho do Servidor de Backup](backup-azure-microsoft-azure-backup.md)
- [Usar o Servidor de Backup para fazer backup de um Servidor do VMware](backup-azure-backup-server-vmware.md)
- [Usar o Servidor de Backup para fazer backup de SQL Server](backup-azure-sql-mabs.md)
- [Usar o Armazenamento de Backup Moderno com o Servidor de Backup](backup-mabs-add-storage.md)
