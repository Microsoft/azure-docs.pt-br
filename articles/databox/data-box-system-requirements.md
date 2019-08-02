---
title: Requisitos de sistema do Microsoft Azure Data Box | Microsoft Docs
description: Aprenda sobre os requisitos de software e de rede para seu Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: e232ad131b1c0930afcf5e7e78b386aba2c9490b
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839765"
---
# <a name="azure-data-box-system-requirements"></a>Requisitos de sistema do Azure Data Box

Este artigo descreve os requisitos de sistema importantes para o Microsoft Azure Data Box e para os clientes se conectando ao Data Box. Recomendamos que você examine as informações com atenção antes de implantar o Data Box e consulte-as, quando necessário, durante a implantação e a subsequente operação.

Os requisitos do sistema incluem:

* **Requisitos de software para hosts que se conectam ao Data Box** – descreve plataformas com suporte, navegadores da interface do usuário Web local, clientes SMB e quaisquer requisitos adicionais para hosts que podem se conectar ao Data Box.
* **Requisitos de rede para o Data Box** – fornece informações sobre os requisitos de rede para a operação ideal do Data Box.


## <a name="software-requirements"></a>Requisitos de software

Os requisitos de software incluem as informações sobre os sistemas operacionais com suporte, os navegadores com suporte para a interface do usuário Web local e os clientes SMB.

### <a name="supported-operating-systems-for-clients"></a>Sistemas operacionais compatíveis para clientes

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>Sistemas de arquivos com suporte para clientes Linux

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>Contas de armazenamento com suporte

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Tipos de armazenamento com suporte

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Navegadores da Web com suporte

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Requisitos de rede

Seu datacenter deve ter uma rede de alta velocidade. É altamente recomendável que você tenha pelo menos uma conexão de 10 GbE. Se uma conexão de 10 GbE não estiver disponível, um link de dados de 1 GbE poderá ser usado para copiar dados, mas as velocidades de cópia serão afetadas.

### <a name="port-requirements"></a>Requisitos de porta

A tabela a seguir lista as portas que precisam ser abertas no firewall para permitir o tráfego SMB ou NFS. Nesta tabela, *entrada* ou *de entrada* refere-se à direção da qual as solicitações do cliente acessam o dispositivo. *-Out* ou *saída* refere-se à direção na qual o dispositivo Data Box envia dados externamente, além da implantação: por exemplo, saída à Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>Próximas etapas

* [Implante seu Azure Data Box](data-box-deploy-ordered.md)
