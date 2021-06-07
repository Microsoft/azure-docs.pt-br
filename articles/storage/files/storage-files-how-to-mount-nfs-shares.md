---
title: Montar um compartilhamento de arquivos NFS do Azure-arquivos do Azure
description: Saiba como montar um compartilhamento do sistema de arquivos de rede.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 8a993d9c1de35132198de5e3becc4f16d6a2a437
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96621290"
---
# <a name="how-to-mount-an-nfs-file-share"></a>Como montar um compartilhamento de arquivos NFS

[Arquivos do Azure](storage-files-introduction.md) é o sistema de arquivos de nuvem de fácil acesso da Microsoft. Os compartilhamentos de arquivos do Azure podem ser montados em distribuições do Linux usando o protocolo SMB ou o protocolo NFS (sistema de arquivos de rede). Este artigo se concentra na montagem com NFS, para obter detalhes sobre como montar com o SMB, consulte [usar os arquivos do Azure com o Linux](storage-how-to-use-files-linux.md). Para obter detalhes sobre cada um dos protocolos disponíveis, consulte [protocolos de compartilhamento de arquivos do Azure](storage-files-compare-protocols.md).

## <a name="limitations"></a>Limitações

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Disponibilidade regional

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Pré-requisitos

- [Crie um compartilhamento NFS](storage-files-how-to-create-nfs-shares.md).

    > [!IMPORTANT]
    > Os compartilhamentos NFS só podem ser acessados de redes confiáveis. As conexões com o compartilhamento NFS devem se originar de uma das seguintes fontes:

- Use uma das seguintes soluções de rede:
    - [Crie um ponto de extremidade privado](storage-files-networking-endpoints.md#create-a-private-endpoint) (recomendado) ou [restrinja o acesso ao seu ponto de extremidade público](storage-files-networking-endpoints.md#restrict-public-endpoint-access).
    - [Configure uma VPN ponto a site (P2S) no Linux para uso com os arquivos do Azure](storage-files-configure-p2s-vpn-linux.md).
    - [Configure uma VPN site a site para uso com os arquivos do Azure](storage-files-configure-s2s-vpn.md).
    - Configure o [ExpressRoute](../../expressroute/expressroute-introduction.md).

## <a name="disable-secure-transfer"></a>Desabilitar transferência segura

1. Entre no portal do Azure e acesse a conta de armazenamento que contém o compartilhamento NFS que você criou.
1. Selecione **Configuração**.
1. Selecione **desabilitado** para **transferência segura necessária**.
1. Clique em **Salvar**.

    :::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="Captura de tela de configuração da conta de armazenamento com transferência segura desabilitada.":::

## <a name="mount-an-nfs-share"></a>Montar um compartilhamento NFS

1. Depois que o compartilhamento de arquivos for criado, selecione o compartilhamento e selecione **conectar do Linux**.
1. Insira o caminho de montagem que você deseja usar e copie o script.
1. Conecte-se ao seu cliente e use o script de montagem fornecido.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/mount-nfs-file-share-script.png" alt-text="Captura de tela da folha conexão de compartilhamento de arquivos":::

Agora você montou seu compartilhamento NFS.

### <a name="validate-connectivity"></a>Validar a conectividade

Se a montagem falhar, é possível que seu ponto de extremidade particular não tenha sido configurado corretamente ou inacessível. Para obter detalhes sobre como confirmar a conectividade, consulte a seção [verificar conectividade](storage-files-networking-endpoints.md#verify-connectivity) do artigo pontos de extremidade de rede.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os arquivos do Azure com nosso artigo, [planejando uma implantação de arquivos do Azure](storage-files-planning.md).
- Se você tiver problemas, consulte [solucionar problemas de compartilhamentos de arquivos NFS do Azure](storage-troubleshooting-files-nfs.md).