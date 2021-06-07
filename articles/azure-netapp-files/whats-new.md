---
title: Novidades do Azure NetApp Files | Microsoft Docs
description: Um resumo sobre os novos recursos mais recentes e as melhorias do Azure NetApp Files.
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
ms.topic: overview
ms.date: 04/05/2021
ms.author: b-juche
ms.openlocfilehash: 94981cd0912f76b710b3a60040ffbffd38381bcd
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552097"
---
# <a name="whats-new-in-azure-netapp-files"></a>Novidades do Azure NetApp Files

O Azure NetApp Files é atualizado regularmente. Este artigo mostra um resumo dos novos recursos mais recentes e de melhorias. 

## <a name="april-2021"></a>Abril de 2021

* [Mapeamento de usuário LDAP do ADDS (Active Directory Domain Services) com grupos estendidos de NFS](configure-ldap-extended-groups.md) (versão prévia)   

    Por padrão, o Azure NetApp Files dá suporte a até 16 IDs de grupo ao gerenciar as credenciais de usuário do NFS, conforme definido no [RFC 5531](https://tools.ietf.org/html/rfc5531). Com essa nova funcionalidade, agora você pode aumentar o máximo para até 1.024 se tiver usuários que são membros de mais do que o número padrão de grupos. Para dar suporte a essa funcionalidade, os volumes de NFS agora também podem ser adicionados ao LDAP do ADDS, o que permite aos usuários LDAP do Active Directory com entradas de grupos estendidas (com até 1.024 grupos) acessar o volume. 

## <a name="march-2021"></a>Março de 2021

* [Compartilhamentos de CA (disponibilidade contínua) do SMB](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) (versão prévia)  

    O Failover Transparente do SMB permite operações de manutenção no serviço do Azure NetApp Files sem interromper a conectividade com os aplicativos de servidor que armazenam e acessam dados em volumes de SMB. Para dar suporte ao Failover Transparente do SMB, o Azure NetApp Files agora dá suporte à opção de compartilhamentos de Disponibilidade Contínua do SMB para uso com aplicativos SQL Server via SMB em execução em VMs do Azure. Atualmente, esse recurso tem suporte no Windows SQL Server. No momento, não há suporte para o Linux SQL Server. Habilitar esse recurso fornece aprimoramentos significativos de desempenho do SQL Server e benefícios de escala e custo para [implantações da Instância Única, da Instância de Cluster de Failover do Always-On e do Grupo de Disponibilidade do Always-On](azure-netapp-files-solution-architectures.md#sql-server). Confira os [Benefícios de usar o Azure NetApp Files para implantação do SQL Server](solutions-benefits-azure-netapp-files-sql-server.md).

* [Redimensionamento automático de um volume de destino de replicação entre regiões](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)

    Em uma relação de replicação entre regiões, um volume de destino é redimensionado automaticamente com base no tamanho do volume de origem. Assim, você não precisa redimensionar o volume de destino separadamente. Esse comportamento de redimensionamento automático é aplicável quando os volumes estão em uma relação de replicação ativa ou quando o emparelhamento da replicação é interrompido com a operação de ressincronização. Para que esse recurso funcione, você precisa garantir que haja espaço suficiente nos pools de capacidade dos volumes de origem e de destino.

## <a name="december-2020"></a>Dezembro de 2020

* [Ferramenta de Instantâneo Consistente do Aplicativo Azure](azacsnap-introduction.md) (versão prévia)    

    O AzAcSnap (Ferramenta de Instantâneo Consistente do Aplicativo Azure) é uma ferramenta de linha de comando que permite simplificar a proteção de dados para bancos de dados de terceiros (SAP HANA) em ambientes do Linux (por exemplo, SUSE e RHEL).   

    O AzAcSnap aproveita as funcionalidades de replicação e instantâneo de volume do Azure NetApp Files e da Instância Grande do Azure. Ele oferece os seguintes benefícios:

    * Proteção de dados consistente com o aplicativo 
    * Gerenciamento de catálogo do banco de dados 
    * Proteção *ad hoc* de volume 
    * Clonagem de volumes de armazenamento 
    * Suporte para recuperação de desastre 

## <a name="november-2020"></a>Novembro de 2020

* [Reversão de instantâneo](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert)

    A funcionalidade de reversão de instantâneo permite que você reverta rapidamente um volume para o estado em que ele estava quando um determinado instantâneo foi tomado. Na maioria dos casos, reverter um volume é muito mais rápido do que restaurar arquivos individuais de um instantâneo para o sistema de arquivos ativo. Também é mais eficiente em termos de espaço em comparação com a restauração de um instantâneo para um novo volume.

## <a name="september-2020"></a>Setembro de 2020

* [Replicação entre regiões do Azure NetApp Files](cross-region-replication-introduction.md) (versão prévia)

  O Azure NetApp Files agora é compatível com a replicação entre regiões. Com essa nova funcionalidade de recuperação de desastre, é possível replicar os volumes do Azure NetApp Files de uma região do Azure para outra de maneira rápida e econômica. Isso protege seus dados contra falhas regionais imprevistas. A replicação entre regiões do Azure NetApp Files usa a tecnologia NetApp SnapMirror®. Somente os blocos alterados são enviados pela rede em um formato compactado e eficiente. Essa tecnologia proprietária minimiza a quantidade de dados necessária para replicar entre as regiões. Desse modo, você economiza nos custos de transferência de dados. Além disso, reduz o tempo de replicação para que você possa ter um RPO (Objetivo de Ponto de Recuperação) menor.

* [Pool de Capacidade de QoS Manual](manual-qos-capacity-pool-introduction.md) (Versão Prévia)  

    Em um pool de capacidade com QoS manual, é possível atribuir a capacidade e a taxa de transferência a um volume de modo independente. A taxa de transferência total de todos os volumes criados com um pool de capacidade com QoS manual é limitada pela taxa de transferência total do pool. Esse valor é determinado pela combinação do tamanho do pool com a taxa de transferência no nível do serviço. Como alternativa, o [tipo de QoS](azure-netapp-files-understand-storage-hierarchy.md#qos_types) do pool de capacidade pode ser automático, que é o padrão. Em um pool de capacidade com QoS automático, a taxa de transferência é atribuída automaticamente aos volumes no pool, proporcionalmente à cota de tamanho atribuída aos volumes.

* [Assinatura LDAP](azure-netapp-files-create-volumes-smb.md) (Versão Prévia)   

    O Azure NetApp Files agora é compatível com a assinatura LDAP para pesquisas com LDAP Seguro entre o serviço do Azure NetApp Files e os controladores de domínio do Active Directory Domain Services especificados pelo usuário. Esse recurso está atualmente na visualização.

* [Criptografia AES para autenticação do AD](azure-netapp-files-create-volumes-smb.md) (Versão Prévia)

    O Azure NetApp Files agora é compatível com a criptografia AES na conexão LDAP com o DC para habilitar a criptografia AES para um volume SMB. Esse recurso está atualmente na visualização. 

* Novas [métricas](azure-netapp-files-metrics.md):   

    * Novas métricas de volume: 
        * *Tamanho alocado do volume*: O tamanho provisionado de um volume
    * Novas métricas do pool: 
        * *Tamanho alocado do pool*: O tamanho provisionado de um pool 
        * *Tamanho total de instantâneos do pool*: A soma do tamanho de instantâneos de todos os volumes no pool

## <a name="july-2020"></a>Julho de 2020

* [Volume de protocolo duplo (NFSv3 e SMB)](create-volumes-dual-protocol.md)

    É possível criar um volume do Azure NetApp Files que permite acesso simultâneo de protocolo duplo (NFS v3 e SMB) compatível com o mapeamento de usuário LDAP. Esse recurso permite casos de uso em que você pode ter uma carga de trabalho baseada em Linux que gera e armazena dados em um volume do Azure NetApp Files. Ao mesmo tempo, sua equipe precisa usar clientes e software baseados no Windows para analisar os dados gerados recentemente no mesmo volume do Azure NetApp Files. O recurso de acesso simultâneo de protocolo duplo elimina a necessidade de copiar os dados gerados pela carga de trabalho para um volume separado com um protocolo diferente para análise posterior. Isso economiza no custo de armazenamento e no tempo operacional. Esse recurso é gratuito (o [custo de armazenamento normal do Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/) ainda se aplica) e está em disponibilidade geral. Saiba mais na [documentação de acesso simultâneo de protocolo duplo](create-volumes-dual-protocol.MD).

* [Criptografia Kerberos em trânsito NFS v4.1](configure-kerberos-encryption.MD)

    O Azure NetApp Files agora é compatível com a criptografia de cliente NFS nos modos do Kerberos (krb5, krb5i e krb5p) com criptografia AES-256, o que aumenta a segurança dos dados. Esse recurso é gratuito (o [custo de armazenamento normal do Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/) ainda se aplica) e está em disponibilidade geral. Saiba mais na [documentação da criptografia Kerberos NFS v4.1](configure-kerberos-encryption.MD).

* [Alteração de nível de serviço do volume dinâmico](dynamic-change-volume-service-level.MD)

    A nuvem promete flexibilidade nos gastos de TI. Agora você pode alterar o nível de serviço de um volume existente do Azure NetApp Files. Basta movê-lo para outro pool de capacidade que use o nível de serviço desejado para o volume. Essa alteração no nível de serviço in-loco para o volume não exige a migração de dados. Também não afeta o acesso do plano de dados ao volume. É possível alterar um volume existente para usar um nível de serviço mais alto e melhorar o desempenho. Outra opção é usar um nível de serviço inferior para a otimizar os custos. Esse recurso é gratuito (o [custo de armazenamento normal do Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/) ainda se aplica) e está em versão prévia pública no momento. Para se registrar na versão prévia do recurso, siga as instruções na [documentação de alteração de nível de serviço do volume dinâmico](dynamic-change-volume-service-level.md).

* [Política de instantâneo de volume](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) (Versão Prévia) 

    O Azure NetApp Files permite criar instantâneos pontuais de seus volumes. Agora é possível criar uma política de instantâneo para que o Azure NetApp Files produza automaticamente instantâneos de volume na frequência escolhida por você. Agende a criação dos instantâneos por hora, por dia, por semana ou por mês. Também é possível especificar o número máximo de instantâneos a serem mantidos como parte da política de instantâneos. Esse recurso é gratuito (o [custo de armazenamento normal do Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/) ainda se aplica) e está em versão prévia no momento. Para se registrar na versão prévia do recurso, siga as instruções na [documentação da política de instantâneos de volume](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

* [Política de exportação de acesso à raiz NFS](azure-netapp-files-configure-export-policy.md)

    O Azure NetApp Files agora permite especificar se a conta raiz pode acessar o volume. 

* [Ocultar o caminho do instantâneo](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client)

    O Azure NetApp Files agora permite especificar se um usuário pode ver e acessar o diretório `.snapshot` (clientes NFS) ou a pasta `~snapshot` (clientes SMB) em um volume montado.

## <a name="may-2020"></a>Maio de 2020

* [Usuários da política de backup](create-active-directory-connections.md) (Versão Prévia)

    O Azure NetApp Files permite incluir outras contas que exigem privilégios elevados na conta de computador criada para uso com esse serviço. As contas especificadas terão permissão para alterar as permissões de NTFS no nível do arquivo ou da pasta. Por exemplo, você pode especificar uma conta de serviço sem privilégios usada para migrar dados para um compartilhamento de arquivos SMB no Azure NetApp Files. O recurso de usuários da política de backup está em versão prévia no momento.

## <a name="next-steps"></a>Próximas etapas
* [O que é o Azure NetApp Files](azure-netapp-files-introduction.md)
* [Compreender a hierarquia de armazenamento do Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md) 
