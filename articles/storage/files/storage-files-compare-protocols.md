---
title: Protocolos de arquivos do Azure disponíveis-NFS e SMB
description: Saiba mais sobre os protocolos disponíveis antes de criar um compartilhamento de arquivos do Azure, incluindo o protocolo SMB e o NFS (sistema de arquivos de rede).
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 482e6c9f2e25b867574090a1f34e95f466916ba9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101711834"
---
# <a name="azure-file-share-protocols"></a>Protocolos de compartilhamento de arquivos do Azure

Os arquivos do Azure oferecem dois protocolos para conectar e montar seus compartilhamentos de arquivos do Azure. [Protocolo SMB (Server Message](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) Protocol) e [protocolo NFS (sistema de arquivos de rede)](https://en.wikipedia.org/wiki/Network_File_System) (visualização). Atualmente, os arquivos do Azure não dão suporte ao acesso de vários protocolos, portanto, um compartilhamento pode ser apenas um compartilhamento NFS ou um compartilhamento SMB. Devido a isso, é recomendável determinar qual protocolo melhor atende às suas necessidades antes de criar compartilhamentos de arquivos do Azure.

## <a name="differences-at-a-glance"></a>Diferenças em um relance

|Recurso  |NFS (visualização)  |SMB  |
|---------|---------|---------|
|Protocolos de acesso     |NFS 4,1         |SMB 2,1, SMB 3,0         |
|Sistema operacional recomendado     |Kernel do Linux versão 4.3 +         |Windows 2008 R2 +, kernel do Linux versão 4.11 +         |
|[Camadas disponíveis](storage-files-planning.md#storage-tiers)     |Armazenamento Premium         |Armazenamento Premium, otimizado para transação, quente, frio         |
|Modelo de cobrança         |[Pagar por capacidade provisionada](https://docs.microsoft.com/azure/storage/files/understanding-billing#provisioned-model)         |[Pagar pela capacidade provisionada para camada Premium](https://docs.microsoft.com/azure/storage/files/understanding-billing#provisioned-model), pré- [pago para a camada Standard](https://docs.microsoft.com/azure/storage/files/understanding-billing#pay-as-you-go-model)         |
|[Redundância](storage-files-planning.md#redundancy)     |LRS, ZRS         |LRS, ZRS, GRS         |
|Autenticação     |Somente autenticação baseada em host        |Autenticação baseada em identidade, autenticação baseada no usuário         |
|Permissões     |Permissões de estilo UNIX         |Permissões de estilo NTFS         |
|Semântica do sistema de arquivos     |Compatível com POSIX         |Não compatível com POSIX         |
|Diferenciar maiúsculas de minúsculas     |Diferencia maiúsculas de minúsculas         |Não diferencia maiúsculas de minúsculas         |
|Suporte a links físicos     |Com suporte         |Sem suporte         |
|Suporte a links simbólicos     |Com suporte         |Sem suporte         |
|Excluindo ou modificando arquivos abertos     |Com suporte         |Sem suporte         |
|Bloqueio     |Advisory do intervalo de bytes do Gerenciador de bloqueio de rede         |Com suporte         |
|Listagem segura de IP público | Sem suporte | Com suporte|
|Interoperabilidade de protocolo| Sem suporte | Filerest|

## <a name="nfs-shares-preview"></a>Compartilhamentos NFS (visualização)

A montagem de compartilhamentos de arquivos do Azure com o NFS 4,1 está atualmente em versão prévia. Ele oferece uma integração mais rígida com o Linux. Essa é uma oferta totalmente compatível com POSIX, que é um padrão entre variantes do UNIX e outros sistemas operacionais baseados em * Nix. Esse serviço de armazenamento de arquivos de nível empresarial é dimensionado para atender às suas necessidades de armazenamento e pode ser acessado simultaneamente por milhares de instâncias de computação.

### <a name="limitations"></a>Limitações

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

#### <a name="regional-availability"></a>Disponibilidade regional

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

### <a name="best-suited"></a>Mais adequado

O NFS com arquivos do Azure é ideal para:

- Cargas de trabalho que exigem compartilhamentos de arquivos em conformidade com POSIX, diferencia maiúsculas de minúsculas ou permissões de estilo do UNIX (UID/GID).
- Cargas de trabalho centradas em Linux que não exigem o Windows Access.

### <a name="security"></a>Segurança

Todos os dados de arquivos do Azure são criptografados em repouso. Para criptografia em trânsito, o Azure fornece uma camada de criptografia para todos os dados em trânsito entre data centers do Azure usando o [MACSec](https://en.wikipedia.org/wiki/IEEE_802.1AE). Por meio disso, a criptografia existe quando os dados são transferidos entre os data centers do Azure. Ao contrário dos arquivos do Azure usando o protocolo SMB, os compartilhamentos de arquivos que usam o protocolo NFS não oferecem autenticação baseada no usuário. A autenticação para compartilhamentos NFS é baseada nas regras de segurança de rede configuradas. Devido a isso, para garantir que apenas conexões seguras sejam estabelecidas com seu compartilhamento NFS, você deve usar pontos de extremidade de serviço ou pontos de extremidade privados. Se você quiser acessar compartilhamentos do local, além de um ponto de extremidade privado, você deve configurar uma VPN ou ExpressRoute. As solicitações que não se originam das seguintes fontes serão rejeitadas:

- [Um ponto de extremidade privado](storage-files-networking-overview.md#private-endpoints)
- [Gateway de VPN do Azure](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
    - [VPN ponto a site (P2S)](../../vpn-gateway/point-to-site-about.md)
    - [Site a site](../../vpn-gateway/design.md#s2smulti)
- [ExpressRoute](../../expressroute/expressroute-introduction.md)
- [Um ponto de extremidade público restrito](storage-files-networking-overview.md#storage-account-firewall-settings)

Para obter mais detalhes sobre as opções de rede disponíveis, consulte [considerações de rede de arquivos do Azure](storage-files-networking-overview.md).

## <a name="smb-shares"></a>Compartilhamentos SMB

Os compartilhamentos de arquivos do Azure montados com o SMB oferecem mais recursos de arquivos do Azure e não têm restrições de recursos de arquivos do Azure, pois estão geralmente disponíveis.

### <a name="features"></a>Recursos

- Sincronização de arquivos do Azure
- Autenticação baseada em identidade
- Suporte de Backup do Azure
- Instantâneos
- Exclusão reversível
- Criptografia-em trânsito e criptografia em repouso

### <a name="best-suited"></a>Mais adequado

O SMB com os arquivos do Azure é ideal para:

- Ambientes de produção
- Clientes que exigem qualquer um dos recursos listados em [recursos](#features)

## <a name="next-steps"></a>Próximas etapas

- [Criar um compartilhamento de arquivos NFS](storage-files-how-to-create-nfs-shares.md)
- [Criar um compartilhamento de arquivos SMB](storage-how-to-create-file-share.md)
