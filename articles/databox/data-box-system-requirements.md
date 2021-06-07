---
title: Requisitos de sistema do Microsoft Azure Data Box | Microsoft Docs
description: Saiba mais sobre os requisitos de sistema importantes para seu Azure Data Box e para clientes que se conectam ao Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 03/25/2021
ms.author: alkohli
ms.openlocfilehash: 7f999dbf7a4e0262e36181a98560931d32d3296b
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612493"
---
# <a name="azure-data-box-system-requirements"></a>Requisitos de sistema do Azure Data Box

Este artigo descreve os requisitos de sistema importantes para seu Microsoft Azure Data Box e para clientes que se conectam ao Data Box. Recomendamos que você examine as informações cuidadosamente antes de implantar seu Data Box e, em seguida, consulte-os quando precisar durante a implantação e operação.

Os requisitos do sistema incluem:

* **Requisitos de software:** Para hosts que se conectam ao Data Box, descreve sistemas operacionais com suporte, protocolos de transferência de arquivo, contas de armazenamento, tipos de armazenamento e navegadores para a interface do usuário da Web local.
* **Requisitos de rede:** Para o Data Box, descreve os requisitos para conexões de rede e portas para a melhor operação do Data Box.


## <a name="software-requirements"></a>Requisitos de software

Os requisitos de software incluem sistemas operacionais com suporte, protocolos de transferência de arquivo, contas de armazenamento, tipos de armazenamento e navegadores para a interface do usuário da Web local.

### <a name="supported-operating-systems-for-clients"></a>Sistemas operacionais compatíveis para clientes

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]


### <a name="supported-file-transfer-protocols-for-clients"></a>Protocolos de transferência de arquivo com suporte para clientes

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

> [!IMPORTANT] 
> Não há suporte para a conexão com Data Box compartilhamentos via REST para ordens de exportação.

### <a name="supported-storage-accounts"></a>Contas de armazenamento com suporte

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Tipos de armazenamento com suporte

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Navegadores da Web com suporte

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Requisitos de rede

Seu datacenter deve ter uma rede de alta velocidade. É altamente recomendável que você tenha pelo menos a conexão de 1 10-GbE. Se uma conexão de 10 GbE não estiver disponível, você poderá usar um link de dados de 1-GbE para copiar dados, mas as velocidades de cópia serão afetadas.

### <a name="port-requirements"></a>Requisitos de porta

A tabela a seguir lista as portas que precisam ser abertas no firewall para permitir o tráfego SMB ou NFS. Nesta tabela, *in* (*entrada*) refere-se à direção da qual o cliente de entrada solicita acesso ao seu dispositivo. *Out* (ou *saída*) refere-se à direção na qual o dispositivo de data Box envia dados externamente, além da implantação. Por exemplo, os dados podem ser de saída para a Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>Próximas etapas

* [Implante seu Azure Data Box](data-box-deploy-ordered.md)
