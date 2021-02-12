---
title: Suporte do sistema de arquivos de rede 3,0 no armazenamento de BLOBs do Azure (visualização) | Microsoft Docs
description: O armazenamento de BLOBs agora dá suporte ao protocolo NFS (Network File System) 3,0. Esse suporte permite que os clientes Windows e Linux montem um contêiner no armazenamento de blobs de uma VM (máquina virtual) do Azure ou um computador que é executado no local.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: 52f7b328b013fd520787fca420a45ffdc5e9d9b1
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98250801"
---
# <a name="network-file-system-nfs-30-protocol-support-in-azure-blob-storage-preview"></a>Suporte ao protocolo NFS (sistema de arquivos de rede) 3,0 no armazenamento de BLOBs do Azure (versão prévia)

O armazenamento de BLOBs agora dá suporte ao protocolo NFS (Network File System) 3,0. Esse suporte permite que clientes Windows ou Linux montem um contêiner no armazenamento de blob de uma VM (máquina virtual) do Azure ou um computador local. 

> [!NOTE]
> O suporte ao protocolo NFS 3,0 no armazenamento de BLOBs do Azure está em visualização pública. Ele dá suporte a contas de armazenamento GPV2 com desempenho de camada Standard nas seguintes regiões: leste da Austrália, centro da Coreia e EUA Central do Sul. A visualização também dá suporte ao blob de blocos com o nível de desempenho premium em todas as regiões públicas.

## <a name="general-workflow-mounting-a-storage-account-container"></a>Fluxo de trabalho geral: montando um contêiner de conta de armazenamento

Para montar um contêiner de conta de armazenamento, você precisará fazer essas coisas.

1. Registre o recurso de protocolo NFS 3,0 com sua assinatura.

2. Verifique se o recurso está registrado.

3. Crie uma VNet (rede virtual) do Azure.

4. Configure a segurança de rede.

5. Crie e configure a conta de armazenamento que aceita o tráfego somente da VNet.

6. Crie um contêiner na conta de armazenamento.

7. Monte o contêiner.

Para obter orientações passo a passo, consulte [montar o armazenamento de BLOBs usando o protocolo NFS (sistema de arquivos de rede) 3,0 (versão prévia)](network-file-system-protocol-support-how-to.md).

> [!IMPORTANT]
> É importante concluir essas tarefas na ordem. Você não pode montar contêineres criados antes de habilitar o protocolo NFS 3,0 em sua conta. Além disso, depois de habilitar o protocolo NFS 3,0 em sua conta, você não poderá desabilitá-lo.

## <a name="network-security"></a>Segurança de rede

Sua conta de armazenamento deve estar contida em uma VNet. Uma VNet permite que os clientes se conectem com segurança à sua conta de armazenamento. A única maneira de proteger os dados em sua conta é usando uma VNet e outras configurações de segurança de rede. Qualquer outra ferramenta usada para proteger dados, incluindo autorização de chave de conta, segurança de Azure Active Directory (AD) e listas de controle de acesso (ACLs), ainda não tem suporte em contas que têm o suporte ao protocolo NFS 3,0 habilitado neles. 

Para saber mais, confira [recomendações de segurança de rede para o armazenamento de BLOBs](security-recommendations.md#networking).

## <a name="supported-network-connections"></a>Conexões de rede com suporte

Um cliente pode se conectar por meio de um [ponto de extremidade](../common/storage-private-endpoints.md)público ou privado e pode se conectar de qualquer um dos seguintes locais de rede:

- A VNet que você configura para sua conta de armazenamento. 

  Para a finalidade deste artigo, vamos nos referir à VNet como a *vnet primária*. Para saber mais, confira [conceder acesso de uma rede virtual](../common/storage-network-security.md#grant-access-from-a-virtual-network).

- Uma VNet emparelhada que está na mesma região que a VNet primária.

  Você precisará configurar sua conta de armazenamento para permitir o acesso a essa VNet emparelhada. Para saber mais, confira [conceder acesso de uma rede virtual](../common/storage-network-security.md#grant-access-from-a-virtual-network).

- Uma rede local conectada à sua VNet primária usando o [Gateway de VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) ou um gateway de [ExpressRoute](../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md). 

  Para saber mais, confira [Configurando o acesso de redes locais](../common/storage-network-security.md#configuring-access-from-on-premises-networks).

- Uma rede local que está conectada a uma rede emparelhada.

  Isso pode ser feito usando o [Gateway de VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) ou um [Gateway de ExpressRoute](../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md) junto com o tráfego de [Gateway](/azure/architecture/reference-architectures/hybrid-networking/vnet-peering#gateway-transit). 

> [!IMPORTANT]
> Se você estiver se conectando de uma rede local, verifique se o cliente permite a comunicação de saída por meio das portas 111 e 2048. O protocolo NFS 3,0 usa essas portas.

## <a name="azure-storage-features-not-yet-supported"></a>Recursos de armazenamento do Azure ainda não suportados

Os recursos de armazenamento do Azure a seguir não têm suporte quando você habilita o protocolo NFS 3,0 em sua conta. 

- Segurança do Azure Active Directory (AD)

- ACLs (listas de controle de acesso) semelhantes a POSIX

- A capacidade de habilitar o suporte a NFS 3,0 em contas de armazenamento existentes

- A capacidade de desabilitar o suporte a NFS 3,0 em uma conta de armazenamento (depois de habilitá-la)

- Capacidade de gravar em BLOBs usando APIs REST ou SDKs. 
  
## <a name="nfs-30-features-not-yet-supported"></a>Recursos do NFS 3,0 ainda não suportados

Os seguintes recursos do NFS 3,0 ainda não têm suporte com o Azure Data Lake Storage Gen2.

- NFS 3,0 sobre UDP. Somente o NFS 3,0 sobre TCP tem suporte.

- Bloqueando arquivos com o NLM (Gerenciador de bloqueio de rede). Os comandos mount devem incluir o `-o nolock` parâmetro.

- Montando subdiretórios. Você só pode montar o diretório raiz (contêiner).

- Listando montagens (por exemplo: usando o comando `showmount -a` )

- Listando exportações (por exemplo: usando o comando `showmount -e` )

- Link físico

- Exportando um contêiner como somente leitura

## <a name="pricing"></a>Preços

Durante a visualização, os dados armazenados em sua conta de armazenamento são cobrados com a mesma taxa de capacidade que o armazenamento de BLOBs cobra por GB por mês. 

Uma transação não é cobrada durante a visualização. O preço das transações está sujeito a alterações e será determinado quando estiver disponível.

## <a name="next-steps"></a>Próximas etapas

Para começar, consulte [montar o armazenamento de BLOBs usando o protocolo NFS (sistema de arquivos de rede) 3,0 (versão prévia)](network-file-system-protocol-support-how-to.md).
