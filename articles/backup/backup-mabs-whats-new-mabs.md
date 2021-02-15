---
title: O que há de novo no Servidor de Backup do Azure
description: O Servidor de Backup do Azure oferece recursos avançados de backup para proteger VMs, arquivos e pastas, cargas de trabalho e muito mais.
ms.topic: conceptual
ms.date: 05/24/2020
ms.openlocfilehash: 39050d0f658e29b82f270f1fe53026e2fb80bfa1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91332756"
---
# <a name="whats-new-in-microsoft-azure-backup-server-mabs"></a>O que há de novo no Backup do Microsoft Azure Server (MABS)

## <a name="whats-new-in-mabs-v3-ur1"></a>O que há de novo no MABS v3 UR1

O Backup do Microsoft Azure Server (MABS) versão 3 UR1 é a atualização mais recente e inclui correções críticas de bugs e outros recursos e aprimoramentos. Para exibir a lista de bugs corrigidos e as instruções de instalação do MABS v3 UR1, consulte o artigo [4534062](https://support.microsoft.com/help/4534062)do KB.

>[!NOTE]
>O suporte para o agente de proteção de 32 bits foi preterido com UR1 MABS v3. Consulte [substituição do agente de proteção de 32 bits](#32-bit-protection-agent-deprecation).

### <a name="faster-backups-with-tiered-storage-using-ssds"></a>Backups mais rápidos com armazenamento em camadas usando SSDs

O MABS v2 apresentou [armazenamento de backup moderno](backup-mabs-add-storage.md) (MBS), melhorando o desempenho e a utilização do armazenamento. O Armazenamento de Backup moderno usa ReFS como sistema de arquivos subjacente e foi projetado para fazer uso de armazenamento híbrido, tal como o armazenamento em camadas.

Para obter a escala e o desempenho por MBS, é recomendável usar uma porcentagem pequena (4% do armazenamento geral) do SSD (armazenamento de flash) com o MABS v3 UR1 como um volume em camadas em combinação com o armazenamento de HDD do DPM. O MABS v3 UR1 com armazenamento em camadas fornece backups de 50-70% mais rápidos. Consulte o artigo do DPM [Configurar MBS com armazenamento em camadas](/system-center/dpm/add-storage#set-up-mbs-with-tiered-storage) para obter as etapas para configurar o armazenamento em camadas.

### <a name="support-for-refs-volumes"></a>Suporte para volumes ReFS

Com o MABS v3 UR1, você pode fazer backup dos volumes ReFS e das cargas de trabalho implantadas no volume ReFS. Você pode fazer backup das seguintes cargas de trabalho implantadas nos volumes ReFS:

* Sistema operacional (64 bits) : Windows Server 2019, 2016, 2012 R2, 2012.
* SQL Server: SQL Server 2019, SQL Server 2017, 2016.
* Exchange: Exchange 2019, 2016.
* SharePoint: SharePoint 2019, 2016 com o SP mais recente.

>[!NOTE]
> Há suporte para o backup de VMs do Hyper-V armazenadas em um volume ReFS com o MABS v3

>FUNDAMENTAL Identificamos alguns problemas com o backup de volumes ReFS com eliminação de duplicação. Estamos trabalhando para corrigi-los e atualizar esta seção assim que tivermos uma correção disponível. Até lá, estamos removendo o suporte para backup de volumes ReFS com eliminação de duplicação de MABSv3 UR1.

### <a name="azure-vmware-solution-protection-support"></a>Suporte à proteção da solução Azure VMware

Com o MABS v3 UR1, agora você pode proteger as máquinas virtuais implantadas na [solução Azure VMware](../azure-vmware/index.yml).

### <a name="vmware-parallel-backups"></a>Backups paralelos de VMware

Com o MABS v3 UR1, todos os backups de VMs VMware em um único grupo de proteção serão paralelos, levando a backups de VM 25% mais rápidos.
Com versões anteriores do MABS, os backups paralelos eram realizados somente em grupos de proteção. Com o MABS v3 UR1, os trabalhos de replicação delta do VMware são executados em paralelo. Por padrão, o número de trabalhos a serem executados em paralelo é definido como 8. Saiba mais sobre [os backups paralelos do VMware](backup-azure-backup-server-vmware.md#vmware-parallel-backups).

### <a name="disk-exclusion-for-vmware-vm-backup"></a>Exclusão de disco para o backup de VM do VMware

Com o MABS v3 UR1, você pode excluir discos específicos de um backup de VM do VMware. Saiba mais sobre como [excluir discos do backup de VM do VMware](backup-azure-backup-server-vmware.md#exclude-disk-from-vmware-vm-backup).  

### <a name="support-for-additional-layer-of-authentication-to-delete-online-backup"></a>Suporte para camada adicional de autenticação para excluir o backup online

Com o MABS v3 UR1, uma camada adicional de autenticação é adicionada para operações críticas. Você será solicitado a inserir um PIN de segurança ao executar a **interrupção da proteção com operações de exclusão de dados** .

### <a name="offline-backup-improvements"></a>Aprimoramentos de backup offline

O MABS v3 UR1 melhora a experiência de backup offline com o serviço de importação/exportação do Azure. Para obter mais informações, consulte as etapas atualizadas [aqui](./backup-azure-backup-server-import-export.md).

>[!NOTE]
>A atualização também traz a visualização para backup offline usando o Azure Data Box no MABS. Entre em contato [SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com) para saber mais.

### <a name="new-cmdlet-parameter"></a>Novo parâmetro de cmdlet

MABS v3 UR1 inclui um novo parâmetro **[-CheckReplicaFragmentation]**. O novo parâmetro calcula a porcentagem de fragmentação de uma réplica e é incluído no cmdlet **Copy-DPMDatasourceReplica** .

### <a name="32-bit-protection-agent-deprecation"></a>substituição do agente de proteção de 32 bits

Com o MABS v3 UR1, o suporte para o agente de proteção de 32 bits não é mais suportado. Você não conseguirá proteger as cargas de trabalho de 32 bits depois de atualizar o servidor MABS V3 para UR1. Os agentes de proteção de 32 bits existentes estarão em um estado desabilitado e os backups agendados falharão com o erro o **agente está desabilitado** . Se você quiser manter os dados de backup para esses agentes, poderá interromper a proteção com a opção manter dados. Caso contrário, o agente de proteção poderá ser removido.

>[!NOTE]
>Examine a [matriz de proteção atualizada](./backup-mabs-protection-matrix.md) para saber as cargas de trabalho com suporte para proteção com mAbs ur 1.

## <a name="whats-new-in-mabs-v3-rtm"></a>O que há de novo no MABS v3 RTM

O Backup do Microsoft Azure Server versão 3 (MABS v3) inclui correções críticas de bugs, suporte ao Windows Server 2019, suporte a SQL 2017 e outros recursos e aprimoramentos. Para ver a lista de bugs corrigidos e as instruções de instalação para o MABS V3, consulte o artigo KB [4457852](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3).

Os seguintes recursos estão incluídos no MABS V3:

### <a name="volume-to-volume-migration"></a>Migração para o Volume

Com o Armazenamento de Backup Moderno (MBS) no MABS V2, anunciamos o armazenamento com capacidade de carga de trabalho, em que você configura determinadas cargas de trabalho para backup em armazenamento específico, com base nas propriedades de armazenamento. No entanto, após a configuração, você pode achar necessário mover backups de determinadas fontes de dados para outro armazenamento para utilização otimizada de recursos. O MABS v3 oferece a capacidade de migrar seus backups e configurá-los para serem armazenados em um volume diferente em [três etapas](https://techcommunity.microsoft.com/t5/system-center-blog/sc-2016-dpm-ur4-migrate-backup-storage-in-3-simple-steps/ba-p/351842).

### <a name="prevent-unexpected-data-loss"></a>Evitar a perda de dados inesperados

Nas empresas, o MABS é gerenciado por uma equipe de administradores. Embora existam diretrizes sobre armazenamento que devem ser usadas para backups, um volume incorreto dado ao MABS como armazenamento de backup pode levar à perda de dados críticos. Com o MABS v3, você pode impedir esses cenários Configurando esses volumes como aqueles que não estão disponíveis para armazenamento usando [esses cmdlets do PowerShell](./backup-mabs-add-storage.md).

### <a name="custom-size-allocation"></a>Alocação de tamanho personalizado

O armazenamento de Backup Moderno (MBS) consome armazenamento thinly, como e quando necessário. Para fazer isso, o MABS calcula o tamanho dos dados cujo backup está sendo feito quando ele está configurado para proteção. No entanto, se muitos arquivos e pastas estiverem sendo submetidos a backup juntos, como no caso de um servidor de arquivos, o cálculo de tamanho pode levar muito tempo. Com o MABS v3, você pode configurar o MABS para aceitar o tamanho do volume como padrão, em vez de calcular o tamanho de cada arquivo, o que poupa tempo.

### <a name="optimized-cc-for-rct-vms"></a>CC otimizado para VMs RCT

O MABS usa o RCT (o controle de alterações nativo no Hyper-V), o que diminui a necessidade de verificações de consistência demoradas em cenários quando a VM trava. O RCT fornece melhor resiliência que o controle de alterações fornecido pelos backups baseados em instantâneos do VSS. O MABS V3 otimiza ainda mais o consumo de rede e armazenamento, transferindo apenas os dados alterados durante as verificações de consistência.

### <a name="support-to-tls-12"></a>Suporte ao TLS 1.2

O TLS 1.2 é a maneira segura de comunicação sugerida pela Microsoft com a melhor criptografia de classe. O MABS agora oferece suporte à comunicação TLS 1.2 entre o MABS e os servidores protegidos, para autenticação baseada em certificado e para backups na nuvem.

### <a name="vmware-vm-protection-support"></a>Suporte à proteção de VM do VMware

Agora há suporte para backup de VM do VMware para implantações de produção. O MABS V3 oferece o seguinte para a proteção de VMs VMware:

* Suporte para vCenter e ESXi 6.5, junto com suporte para 5.5 e 6.0.
* A proteção automática de VMs VMware para a nuvem. Se novas VMs VMware forem adicionadas a uma pasta protegida, elas serão automaticamente protegidas em disco e nuvem.
* Melhorias na eficiência de recuperação para recuperação de localização alternativa do VMware.

### <a name="sql-2017-support"></a>Suporte do SQL 2017

O MABS V3 pode ser instalado com o SQL 2017 como o banco de dados do MABS. Você pode atualizar o servidor SQL do SQL 2016 para o SQL 2017 ou instalá-lo recentemente. Você também pode fazer o backup da carga de trabalho do SQL 2017 em ambientes em cluster e não em cluster com o MABS V3.

### <a name="windows-server-2019-support"></a>Suporte para o Windows Server 2019

O MABS V3 pode ser instalado no Windows Server 2019. Para usar o MABS v3 com o WS2019, você pode atualizar seu sistema operacional para o WS2019 antes de instalar/atualizar para MABS V3 ou pode atualizar seu sistema operacional após instalar/atualizar v3 no WS2016.

O MABS V3 é uma versão completa e pode ser instalado diretamente no Windows Server 2016, Windows Server de 2019 ou pode ser atualizado do MABS V2. Antes de atualizar ou instalar o Backup Server V3, leia sobre os pré-requisitos de instalação.
Encontre mais informações sobre as etapas de instalação/atualização para o MABS [aqui](./backup-azure-microsoft-azure-backup.md#software-package).

> [!NOTE]
>
> O MABS tem o mesmo código base como o System Center Data Protection Manager. O MABS v3 é equivalente ao Data Protection Manager 1807. MABS v3 UR1 é equivalente a Data Protection Manager 2019 UR1.

## <a name="next-steps"></a>Próximas etapas

Saiba como preparar seu servidor, ou começar a proteger uma carga de trabalho:

* [Preparar as cargas de trabalho do Servidor de Backup](backup-azure-microsoft-azure-backup.md)
* [Usar o Servidor de Backup para fazer backup de um Servidor do VMware](backup-azure-backup-server-vmware.md)
* [Usar o Servidor de Backup para fazer backup de SQL Server](backup-azure-sql-mabs.md)
* [Usar o Armazenamento de Backup Moderno com o Servidor de Backup](backup-mabs-add-storage.md)
