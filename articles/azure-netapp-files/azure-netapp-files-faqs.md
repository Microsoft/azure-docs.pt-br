---
title: Perguntas frequentes sobre Azure NetApp Files | Microsoft Docs
description: Examine as perguntas frequentes sobre Azure NetApp Files, como rede, segurança, desempenho, gerenciamento de capacidade e migração/proteção de dados.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: b-juche
ms.openlocfilehash: 3ca4938d8666fd60ebac9e75bb2da1780e0914d3
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607993"
---
# <a name="faqs-about-azure-netapp-files"></a>Perguntas frequentes sobre Azure NetApp Files

Este artigo responde às perguntas frequentes sobre Azure NetApp Files. 

## <a name="networking-faqs"></a>Perguntas frequentes sobre rede

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>O caminho de dados do NFS passa pela Internet?  

Não. O caminho de dados do NFS não passa pela Internet. Azure NetApp Files é um serviço nativo do Azure que é implantado na VNet (rede virtual) do Azure onde o serviço está disponível. Azure NetApp Files usa uma sub-rede delegada e provisiona uma interface de rede diretamente na VNet. 

Consulte as [diretrizes para Azure NetApp files planejamento de rede](./azure-netapp-files-network-topologies.md) para obter detalhes.  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Posso conectar uma VNet que eu já criei ao serviço de Azure NetApp Files?

Sim, você pode conectar VNets que você criou ao serviço. 

Consulte as [diretrizes para Azure NetApp files planejamento de rede](./azure-netapp-files-network-topologies.md) para obter detalhes.  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Posso montar um volume NFS de Azure NetApp Files usando o nome FQDN do DNS?

Sim, você pode, se criar as entradas DNS necessárias. Azure NetApp Files fornece o IP do serviço para o volume provisionado. 

> [!NOTE] 
> Azure NetApp Files pode implantar IPs adicionais para o serviço, conforme necessário.  As entradas DNS talvez precisem ser atualizadas periodicamente.

### <a name="can-i-set-or-select-my-own-ip-address-for-an-azure-netapp-files-volume"></a>Posso definir ou selecionar meu próprio endereço IP para um volume Azure NetApp Files?  

Não. A atribuição de IP para Azure NetApp Files volumes é dinâmica. Não há suporte para atribuição de IP estático. 

### <a name="does-azure-netapp-files-support-dual-stack-ipv4-and-ipv6-vnet"></a>Azure NetApp Files oferece suporte a VNet de pilha dupla (IPv4 e IPv6)?

Não, o Azure NetApp Files atualmente não dá suporte à VNet de pilha dupla (IPv4 e IPv6).  
 
## <a name="security-faqs"></a>Perguntas frequentes sobre segurança

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>O tráfego de rede entre a VM do Azure e o armazenamento pode ser criptografado?

O tráfego de dados entre os clientes NFSv 4.1 e os volumes Azure NetApp Files pode ser criptografado usando o Kerberos com a criptografia AES-256. Consulte [Configurar nfsv 4.1 criptografia Kerberos para Azure NetApp files](configure-kerberos-encryption.md) para obter detalhes.   

O tráfego de dados entre clientes NFSv3 ou SMB3 para Azure NetApp Files volumes não é criptografado. No entanto, o tráfego de uma VM do Azure (executando um cliente NFS ou SMB) para Azure NetApp Files é tão seguro quanto qualquer outro tráfego do Azure-VM para VM. Esse tráfego é local para a rede do Data Center do Azure. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>O armazenamento pode ser criptografado em repouso?

Todos os volumes de Azure NetApp Files são criptografados usando o padrão FIPS 140-2. Todas as chaves são gerenciadas pelo serviço de Azure NetApp Files. 

### <a name="how-are-encryption-keys-managed"></a>Como as chaves de criptografia são gerenciadas? 

O gerenciamento de chaves para Azure NetApp Files é tratado pelo serviço. Uma chave de criptografia de dados XTS-AES-256 exclusiva é gerada para cada volume. Uma hierarquia de chave de criptografia é usada para criptografar e proteger todas as chaves de volume. Essas chaves de criptografia nunca são exibidas ou relatadas em um formato não criptografado. As chaves de criptografia são excluídas imediatamente quando um volume é excluído.

O suporte para chaves gerenciadas pelo cliente (Bring Your Own Key) usando o HSM dedicado do Azure está disponível de acordo com as regiões leste dos EUA, Sul EUA Central, oeste dos EUA 2 e US Gov-Virgínia. Você pode solicitar acesso em [anffeedback@microsoft.com](mailto:anffeedback@microsoft.com) . À medida que a capacidade se tornar disponível, as solicitações serão aprovadas.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Posso configurar as regras da política de exportação do NFS para controlar o acesso ao destino de montagem do serviço Azure NetApp Files?

Sim, você pode configurar até cinco regras em uma única política de exportação de NFS.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Azure NetApp Files dá suporte a grupos de segurança de rede?

Não, no momento, não é possível aplicar grupos de segurança de rede à sub-rede delegada do Azure NetApp Files ou interfaces de rede criadas pelo serviço.

### <a name="can-i-use-azure-rbac-with-azure-netapp-files"></a>Posso usar o RBAC do Azure com Azure NetApp Files?

Sim, Azure NetApp Files dá suporte aos recursos do RBAC do Azure.

## <a name="performance-faqs"></a>Perguntas frequentes sobre o desempenho

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>O que devo fazer para otimizar ou ajustar o desempenho de Azure NetApp Files?

Você pode executar as seguintes ações de acordo com os requisitos de desempenho: 
- Verifique se a máquina virtual está dimensionada corretamente.
- Habilite a rede acelerada para a VM.
- Selecione o nível de serviço e o tamanho desejados para o pool de capacidade.
- Crie um volume com o tamanho de cota desejado para a capacidade e o desempenho.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Como fazer converter níveis de serviço baseados em taxa de transferência de Azure NetApp Files em IOPS?

Você pode converter MB/s em IOPS usando a seguinte fórmula:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>Como fazer alterar o nível de serviço de um volume?

Você pode alterar o nível de serviço de um volume existente movendo o volume para outro pool de capacidade que usa o [nível de serviço](azure-netapp-files-service-levels.md) desejado para o volume. Consulte [alterar dinamicamente o nível de serviço de um volume](dynamic-change-volume-service-level.md). 

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Como fazer monitorar Azure NetApp Files desempenho?

Azure NetApp Files fornece métricas de desempenho de volume. Você também pode usar Azure Monitor para monitorar as métricas de uso de Azure NetApp Files.  Consulte [métricas para Azure NetApp files](azure-netapp-files-metrics.md) para obter a lista de métricas de desempenho para Azure NetApp files.

### <a name="whats-the-performance-impact-of-kerberos-on-nfsv41"></a>Qual é o impacto no desempenho do Kerberos no NFSv 4.1?

Consulte [impacto no desempenho do Kerberos em volumes nfsv 4.1](performance-impact-kerberos.md) para obter informações sobre as opções de segurança do nfsv 4.1, os vetores de desempenho testados e o impacto esperado no desempenho. 

## <a name="nfs-faqs"></a>Perguntas frequentes sobre NFS

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Quero ter um volume montado automaticamente quando uma VM do Azure é iniciada ou reinicializada.  Como fazer configurar meu host para volumes NFS persistentes?

Para que um volume NFS seja montado automaticamente na inicialização ou na reinicialização da VM, adicione uma entrada ao `/etc/fstab` arquivo no host. 

Confira [montar ou desmontar um volume para máquinas virtuais Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) para obter detalhes.  

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Por que o comando DF no cliente NFS não mostra o tamanho do volume provisionado?

O tamanho do volume relatado em DF é o tamanho máximo para o qual o volume de Azure NetApp Files pode crescer. O tamanho do volume de Azure NetApp Files no comando DF não é refletido da cota ou do tamanho do volume.  Você pode obter a Azure NetApp Files o tamanho ou a cota do volume por meio do portal do Azure ou da API.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Qual versão do NFS Azure NetApp Files dá suporte?

Azure NetApp Files dá suporte a NFSv3 e NFSv 4.1. Você pode [criar um volume](azure-netapp-files-create-volumes.md) usando a versão do NFS. 

### <a name="how-do-i-enable-root-squashing"></a>Como fazer habilitar o desativação de raiz?

Você pode especificar se a conta raiz pode acessar o volume ou não usando a política de exportação do volume. Consulte [Configurar política de exportação para um volume de NFS](azure-netapp-files-configure-export-policy.md) para obter detalhes.

### <a name="can-i-use-the-same-file-path-volume-creation-token-for-multiple-volumes"></a>Posso usar o mesmo caminho de arquivo (token de criação de volume) para vários volumes?

Sim, você pode. No entanto, o caminho do arquivo deve ser usado em uma assinatura diferente ou em uma região diferente.   

Por exemplo, você cria um volume chamado `vol1` . Em seguida, você cria outro volume também chamado `vol1` em um pool de capacidade diferente, mas na mesma assinatura e região. Nesse caso, o uso do mesmo nome de volume causará `vol1` um erro. Para usar o mesmo caminho de arquivo, o nome deve estar em uma região ou assinatura diferente.

### <a name="when-i-try-to-access-nfs-volumes-through-a-windows-client-why-does-the-client-take-a-long-time-to-search-folders-and-subfolders"></a>Quando tento acessar volumes NFS por meio de um cliente Windows, por que o cliente demora muito para pesquisar pastas e subpastas?

Verifique se `CaseSensitiveLookup` o está habilitado no cliente Windows para acelerar a pesquisa de pastas e subpastas:

1. Use o seguinte comando do PowerShell para habilitar o CaseSensitiveLookup:   
    `Set-NfsClientConfiguration -CaseSensitiveLookup 1`    
2. Monte o volume no Windows Server.   
    Exemplo:   
    `Mount -o rsize=1024 -o wsize=1024 -o mtype=hard \\10.x.x.x\testvol X:*`

### <a name="how-does-azure-netapp-files-support-nfsv41-file-locking"></a>Como Azure NetApp Files dá suporte ao bloqueio de arquivos do NFSv 4.1? 

Para clientes NFSv 4.1, o Azure NetApp Files dá suporte ao mecanismo de bloqueio de arquivos NFSv 4.1 que mantém o estado de todos os bloqueios de arquivo em um modelo baseado em concessão. 

Por RFC 3530, Azure NetApp Files define um único período de concessão para todos os Estados mantidos por um cliente NFS. Se o cliente não renovar sua concessão dentro do período definido, todos os Estados associados à concessão do cliente serão liberados pelo servidor.  

Por exemplo, se um cliente montando um volume ficar sem resposta ou falhar além dos tempos limite, os bloqueios serão liberados. O cliente pode renovar sua concessão de forma explícita ou implícita, executando operações como a leitura de um arquivo.   

Um período de carência define um período de processamento especial no qual os clientes podem tentar recuperar seu estado de bloqueio durante a recuperação de um servidor. O tempo limite padrão para as concessões é de 30 segundos com um período de carência de 45 segundos. Após esse período, a concessão do cliente será liberada.   

## <a name="smb-faqs"></a>Perguntas frequentes sobre o SMB

### <a name="which-smb-versions-are-supported-by-azure-netapp-files"></a>Quais versões SMB têm suporte pelo Azure NetApp Files?

O Azure NetApp Files dá suporte a SMB 2,1 e SMB 3,1 (que inclui suporte para SMB 3,0).    

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>É uma conexão Active Directory necessária para acesso SMB? 

Sim, você deve criar uma conexão de Active Directory antes de implantar um volume SMB. Os controladores de domínio especificados devem ser acessíveis pela sub-rede delegada do Azure NetApp Files para uma conexão bem-sucedida.  Consulte [criar um volume SMB](./azure-netapp-files-create-volumes-smb.md) para obter detalhes. 

### <a name="how-many-active-directory-connections-are-supported"></a>Há suporte para quantas conexões Active Directory?

Azure NetApp Files não oferece suporte a várias conexões de Active Directory (AD) em uma única *região*, mesmo que as conexões do AD estejam em contas diferentes do NetApp. No entanto, você pode ter várias conexões do AD em uma única *assinatura*, desde que as conexões do AD estejam em regiões diferentes. Se você precisar de várias conexões do AD em uma única região, poderá usar assinaturas separadas para fazer isso. 

Uma conexão do AD é configurada por conta da NetApp; a conexão do AD é visível somente por meio da conta do NetApp na qual ela é criada.

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Azure NetApp Files oferece suporte a Azure Active Directory? 

Há suporte para os [serviços de domínio Azure Active Directory (AD)](../active-directory-domain-services/overview.md) e [Active Directory Domain Services (AD DS)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) . Você pode usar os controladores de domínio Active Directory existentes com Azure NetApp Files. Os controladores de domínio podem residir no Azure como máquinas virtuais ou localmente por meio de ExpressRoute ou VPN S2S. O Azure NetApp Files não oferece suporte ao ingresso no AD para [Azure Active Directory](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/) no momento.

Se estiver usando o Azure NetApp Files com o Azure Active Directory Domain Services, o caminho da unidade organizacional será `OU=AADDC Computers` quando você configurar o Active Directory para sua conta do NetApp.

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>Quais versões do Windows Server Active Directory têm suporte?

O Azure NetApp Files oferece suporte a versões do Windows Server 2008r2SP1-2019 do Active Directory Domain Services.

### <a name="why-does-the-available-space-on-my-smb-client-not-show-the-provisioned-size"></a>Por que o espaço disponível no meu cliente SMB não mostra o tamanho provisionado?

O tamanho do volume relatado pelo cliente SMB é o tamanho máximo para o qual o volume Azure NetApp Files pode crescer. O tamanho do volume de Azure NetApp Files, conforme mostrado no cliente SMB, não está refletindo a cota ou o tamanho do volume. Você pode obter a Azure NetApp Files o tamanho ou a cota do volume por meio do portal do Azure ou da API.

### <a name="im-having-issues-connecting-to-my-smb-share-what-should-i-do"></a>Estou tendo problemas para se conectar ao meu compartilhamento SMB. O que devo fazer?

Como prática recomendada, defina a tolerância máxima para a sincronização do relógio do computador como cinco minutos. Para obter mais informações, consulte [tolerância máxima para sincronização de relógio do computador](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/jj852172(v=ws.11)). 

### <a name="how-can-i-obtain-the-ip-address-of-an-smb-volume-via-the-portal"></a>Como posso obter o endereço IP de um volume SMB por meio do portal?

Use o link **modo de exibição JSON** no painel Visão geral do volume e procure o identificador **IP_inicial** em **Propriedades**  ->  **mountTargets**.

## <a name="capacity-management-faqs"></a>Perguntas frequentes sobre gerenciamento de capacidade

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Como fazer monitorar o uso do pool de capacidade e do volume de Azure NetApp Files? 

Azure NetApp Files fornece métricas de uso de volume e pool de capacidade. Você também pode usar Azure Monitor para monitorar o uso de Azure NetApp Files. Consulte [métricas para Azure NetApp files](azure-netapp-files-metrics.md) para obter detalhes. 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Posso gerenciar Azure NetApp Files por meio de Gerenciador de Armazenamento do Azure?

Não. O Gerenciador de Armazenamento do Azure não dá suporte a Azure NetApp Files.

### <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>Como fazer determinar se um diretório está se aproximando do tamanho do limite?

Você pode usar o `stat` comando de um cliente para ver se um diretório está se aproximando do limite de tamanho máximo para metadados de diretório (320 MB).   

Para um diretório de 320 MB, o número de blocos é 655360, sendo que cada tamanho de bloco é de 512 bytes.  (Ou seja, 320x1024x1024/512.)  Esse número se traduz em aproximadamente 4 milhões arquivos máximos para um diretório de 320 MB. No entanto, o número real de arquivos máximos pode ser menor, dependendo de fatores como o número de arquivos que contêm caracteres não-ASCII no diretório. Como tal, você deve usar o `stat` comando da seguinte maneira para determinar se seu diretório está se aproximando do seu limite.  

Exemplos:

```console
[makam@cycrh6rtp07 ~]$ stat bin
File: 'bin'
Size: 4096            Blocks: 8          IO Block: 65536  directory

[makam@cycrh6rtp07 ~]$ stat tmp
File: 'tmp'
Size: 12288           Blocks: 24         IO Block: 65536  directory
 
[makam@cycrh6rtp07 ~]$ stat tmp1
File: 'tmp1'
Size: 4096            Blocks: 8          IO Block: 65536  directory
```


## <a name="data-migration-and-protection-faqs"></a>Perguntas frequentes sobre migração de dados e proteção

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Como fazer migrar dados para Azure NetApp Files?
Azure NetApp Files fornece volumes de NFS e SMB.  Você pode usar qualquer ferramenta de cópia baseada em arquivo para migrar dados para o serviço. 

A NetApp oferece uma solução baseada em SaaS, [sincronização de nuvem da NetApp](https://cloud.netapp.com/cloud-sync-service).  A solução permite replicar dados de NFS ou SMB para Azure NetApp Files exportações de NFS ou compartilhamentos SMB. 

Você também pode usar uma ampla variedade de ferramentas gratuitas para copiar dados. Para NFS, você pode usar ferramentas de cargas de trabalho, como [rsync](https://rsync.samba.org/examples.html) , para copiar e sincronizar dados de origem em um volume Azure NetApp files. Para o SMB, você pode usar cargas de trabalho do [Robocopy](/windows-server/administration/windows-commands/robocopy) da mesma maneira.  Essas ferramentas também podem replicar permissões de arquivo ou pasta. 

Os requisitos para a migração de dados do local para o Azure NetApp Files são os seguintes: 

- Verifique se Azure NetApp Files está disponível na região do Azure de destino.
- Valide a conectividade de rede entre a origem e o endereço IP do volume de destino Azure NetApp Files. Transferência de dados entre o local e o serviço de Azure NetApp Files tem suporte no ExpressRoute.
- Crie o volume de Azure NetApp Files de destino.
- Transfira os dados de origem para o volume de destino usando sua ferramenta de cópia de arquivo preferencial.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Como fazer criar uma cópia de um volume de Azure NetApp Files em outra região do Azure?
    
Azure NetApp Files fornece volumes de NFS e SMB.  Qualquer ferramenta de cópia baseada em arquivo pode ser usada para replicar dados entre regiões do Azure. 

A NetApp oferece uma solução baseada em SaaS, [sincronização de nuvem da NetApp](https://cloud.netapp.com/cloud-sync-service).  A solução permite replicar dados de NFS ou SMB para Azure NetApp Files exportações de NFS ou compartilhamentos SMB. 

Você também pode usar uma ampla variedade de ferramentas gratuitas para copiar dados. Para NFS, você pode usar ferramentas de cargas de trabalho, como [rsync](https://rsync.samba.org/examples.html) , para copiar e sincronizar dados de origem em um volume Azure NetApp files. Para o SMB, você pode usar cargas de trabalho do [Robocopy](/windows-server/administration/windows-commands/robocopy) da mesma maneira.  Essas ferramentas também podem replicar permissões de arquivo ou pasta. 

Os requisitos para replicar um volume de Azure NetApp Files para outra região do Azure são os seguintes: 
- Verifique se Azure NetApp Files está disponível na região do Azure de destino.
- Valide a conectividade de rede entre VNets em cada região. Atualmente, o emparelhamento global entre VNets não tem suporte.  Você pode estabelecer a conectividade entre VNets vinculando-se a um circuito do ExpressRoute ou usando uma conexão VPN S2S. 
- Crie o volume de Azure NetApp Files de destino.
- Transfira os dados de origem para o volume de destino usando sua ferramenta de cópia de arquivo preferencial.

### <a name="is-migration-with-azure-data-box-supported"></a>Há suporte para migração com Azure Data Box?

Não. Azure Data Box não oferece suporte a Azure NetApp Files no momento. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>Há suporte para a migração com o serviço de importação/exportação do Azure?

Não. O serviço de importação/exportação do Azure não oferece suporte a Azure NetApp Files no momento.

## <a name="product-faqs"></a>Perguntas frequentes sobre o produto

### <a name="can-i-use-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>Posso usar Azure NetApp Files volumes de NFS ou SMB com a AVS (solução VMware do Azure)?

Você pode montar Azure NetApp Files volumes do NFS em VMs do Windows da AVS ou VMs do Linux. Você pode mapear Azure NetApp Files compartilhamentos SMB em VMs do Windows AVS. Para obter mais detalhes, consulte [Azure NetApp files com a solução VMware do Azure]( ../azure-vmware/netapp-files-with-azure-vmware-solution.md).  

### <a name="what-regions-are-supported-for-using-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>Quais regiões têm suporte para usar Azure NetApp Files volumes NFS ou SMB com a solução VMware do Azure (AVS)?

Usar Azure NetApp Files volumes de NFS ou SMB com AVS tem suporte nas seguintes regiões – leste dos EUA, oeste dos EUA, Europa Ocidental e leste da Austrália.

## <a name="next-steps"></a>Próximas etapas  

- [Perguntas frequentes Microsoft Azure ExpressRoute](../expressroute/expressroute-faqs.md)
- [Perguntas frequentes Rede Virtual do Microsoft Azure](../virtual-network/virtual-networks-faq.md)
- [Como criar uma solicitação de suporte do Azure](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Azure Data Box](../databox/index.yml)
- [Perguntas frequentes sobre o desempenho do SMB para Azure NetApp Files](azure-netapp-files-smb-performance.md)
