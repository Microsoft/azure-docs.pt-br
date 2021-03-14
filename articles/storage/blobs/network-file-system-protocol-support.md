---
title: Suporte do sistema de arquivos de rede 3,0 no armazenamento de BLOBs do Azure (visualização) | Microsoft Docs
description: O armazenamento de BLOBs agora dá suporte ao protocolo NFS (Network File System) 3,0. Esse suporte permite que clientes Linux montem um contêiner no armazenamento de blobs de uma VM (máquina virtual) do Azure ou um computador que é executado no local.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 02/19/2021
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: b00956a6fb5a79b09602ca1752cc547595f32db6
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103224587"
---
# <a name="network-file-system-nfs-30-protocol-support-in-azure-blob-storage-preview"></a>Suporte ao protocolo NFS (sistema de arquivos de rede) 3,0 no armazenamento de BLOBs do Azure (versão prévia)

O armazenamento de BLOBs agora dá suporte ao protocolo NFS (Network File System) 3,0. Esse suporte fornece compatibilidade do sistema de arquivos do Linux em escala e preços de armazenamento de objetos e permite que os clientes Linux montem um contêiner no armazenamento de blobs de uma VM (máquina virtual) do Azure ou um computador local. 

> [!NOTE]
> O suporte ao protocolo NFS 3,0 no armazenamento de BLOBs do Azure está em visualização pública. Ele dá suporte a contas de armazenamento GPV2 com desempenho de camada Standard nas seguintes regiões: leste da Austrália, centro da Coreia e EUA Central do Sul. A visualização também dá suporte ao blob de blocos com o nível de desempenho premium em todas as regiões públicas.

Sempre foi um desafio executar cargas de trabalho herdadas em grande escala, como HPC (computação de alto desempenho) na nuvem. Um motivo é que os aplicativos geralmente usam protocolos de arquivo tradicionais, como NFS ou SMB (protocolo de mensagens de servidor) para acessar dados. Além disso, os serviços de armazenamento em nuvem nativos se concentram no armazenamento de objetos que têm um namespace simples e metadados extensivos em vez de sistemas de arquivos que fornecem um namespace hierárquico e operações de metadados eficientes. 

O armazenamento de BLOBs agora dá suporte a um namespace hierárquico e, quando combinado com o suporte ao protocolo NFS 3,0, o Azure torna muito mais fácil executar aplicativos herdados sobre o armazenamento de objetos em nuvem em larga escala. 

## <a name="applications-and-workloads-suited-for-this-feature"></a>Aplicativos e cargas de trabalho adequados para esse recurso

O recurso de protocolo NFS 3,0 é mais adequado para processar alta taxa de transferência, alta escala, cargas de trabalho pesadas de leitura, como processamento de mídia, simulações de risco e sequenciamento de genoma. Você deve considerar o uso desse recurso para qualquer outro tipo de carga de trabalho que usa vários leitores e muitos threads, que exigem alta largura de banda. 

## <a name="nfs-30-and-the-hierarchical-namespace"></a>NFS 3,0 e o namespace hierárquico

O suporte ao protocolo NFS 3,0 requer que os BLOBs sejam organizados em um namespace hierárquico. Você pode habilitar um namespace hierárquico ao criar uma conta de armazenamento. A capacidade de usar um namespace hierárquico foi introduzida por Azure Data Lake Storage Gen2. Ele organiza os objetos (arquivos) em uma hierarquia de diretórios e subdiretórios da mesma maneira que o sistema de arquivos em seu computador é organizado.  O namespace hierárquico é dimensionado linearmente e não prejudica a capacidade ou o desempenho dos dados. Protocolos diferentes se estendem do namespace hierárquico. O protocolo NFS 3,0 é um dos protocolos disponíveis.   

> [!div class="mx-imgBorder"]
> ![namespace hierárquico](./media/network-protocol-support/hierarchical-namespace-and-nfs-support.png)
  
## <a name="data-stored-as-block-blobs"></a>Dados armazenados como BLOBs de blocos

Se você habilitar o suporte ao protocolo NFS 3,0, todos os dados em sua conta de armazenamento serão armazenados como BLOBs de blocos. Blobs de blocos são otimizados para processar com eficiência grandes quantidades de dados de leitura intensa. Blobs de blocos são compostos de blocos. Cada bloco é identificado por uma ID de bloco. Um blob de blocos pode incluir até 50.000 blocos. Cada bloco em um blob de blocos pode ter um tamanho diferente, até o tamanho máximo permitido para a versão de serviço que sua conta usa.

Quando seu aplicativo faz uma solicitação usando o protocolo NFS 3,0, essa solicitação é convertida em combinação de operações de blob de blocos. Por exemplo, as solicitações de leitura RPC (chamada de procedimento remoto) do NFS 3,0 são convertidas em operação [obter blob](/rest/api/storageservices/get-blob) . As solicitações de RPC de gravação de NFS 3,0 são convertidas em uma combinação de lista de [blocos Get](/rest/api/storageservices/get-block-list), [bloco Put](/rest/api/storageservices/put-block)e [lista de blocos Put](/rest/api/storageservices/put-block-list).

## <a name="general-workflow-mounting-a-storage-account-container"></a>Fluxo de trabalho geral: montando um contêiner de conta de armazenamento

Seus clientes Linux podem montar um contêiner no armazenamento de blobs de uma VM (máquina virtual) do Azure ou um computador local. Para montar um contêiner de conta de armazenamento, você precisará fazer essas coisas.

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

  Neste artigo, vamos nos referir a essa VNet como a *vnet primária*. Para saber mais, confira [conceder acesso de uma rede virtual](../common/storage-network-security.md#grant-access-from-a-virtual-network).

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

Os seguintes recursos de NFS 3,0 ainda não têm suporte.

- NFS 3,0 sobre UDP. Somente o NFS 3,0 sobre TCP tem suporte.

- Bloqueando arquivos com o NLM (Gerenciador de bloqueio de rede). Os comandos mount devem incluir o `-o nolock` parâmetro.

- Montando subdiretórios. Você só pode montar o diretório raiz (contêiner).

- Listando montagens (por exemplo: usando o comando `showmount -a` )

- Listando exportações (por exemplo: usando o comando `showmount -e` )

- Link físico

- Exportando um contêiner como somente leitura

## <a name="nfs-30-clients-not-yet-supported"></a>Clientes NFS 3,0 ainda não suportados

Os seguintes clientes NFS 3,0 ainda não têm suporte.

- Cliente do Windows para NFS

## <a name="pricing"></a>Preço

Durante a visualização, os dados armazenados em sua conta de armazenamento são cobrados com a mesma taxa de capacidade que o armazenamento de BLOBs cobra por GB por mês. 

Uma transação não é cobrada durante a visualização. O preço das transações está sujeito a alterações e será determinado quando estiver disponível.

## <a name="next-steps"></a>Próximas etapas

- Para começar, consulte [montar o armazenamento de BLOBs usando o protocolo NFS (sistema de arquivos de rede) 3,0 (versão prévia)](network-file-system-protocol-support-how-to.md).

- Para otimizar o desempenho, consulte [considerações de desempenho do NFS (Network File System) 3,0 no armazenamento de BLOBs do Azure (versão prévia)](network-file-system-protocol-support-performance.md).
