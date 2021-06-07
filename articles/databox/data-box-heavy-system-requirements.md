---
title: Microsoft Azure Data Box Heavy requisitos do sistema | Microsoft Docs
description: Saiba mais sobre os requisitos de software e rede para seu Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 03/25/2021
ms.author: alkohli
ms.openlocfilehash: d4fed8a79b9d74f44a511bd019531c7843965d0e
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105611915"
---
# <a name="azure-data-box-heavy-system-requirements"></a>Azure Data Box Heavy requisitos do sistema

Este artigo descreve os requisitos de sistema importantes para seu Azure Data Box Heavy dispositivo e para os clientes que se conectam ao dispositivo. Recomendamos que você revise as informações cuidadosamente antes de implantar sua Data Box Heavy e, em seguida, reveja-as conforme necessário durante a implantação e a operação subsequente.

Os requisitos do sistema incluem:

* **Requisitos de software para hosts que se conectam ao data Box Heavy** -descreve as plataformas com suporte, os navegadores para a interface do usuário da Web local, clientes SMB e quaisquer requisitos adicionais para hosts que podem se conectar ao data box.
* **Requisitos de rede para o data Box Heavy** -fornece informações sobre os requisitos de rede para a operação ideal do dispositivo de data Box Heavy.

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

Seu datacenter deve ter uma rede de alta velocidade. Para velocidades de cópia mais rápidas, conexões 2 40-GbE podem ser utilizadas em paralelo (uma por nó). Se você não tiver 40-GbE disponível, recomendamos que você tenha pelo menos conexões 2 10-GbE (uma por nó).

### <a name="port-requirements"></a>Requisitos de porta

A tabela a seguir lista as portas que precisam ser abertas no firewall para permitir o tráfego SMB ou NFS. Nesta tabela, *entrada* ou *de entrada* refere-se à direção da qual as solicitações do cliente acessam o dispositivo. *Saída* ou *saída* refere-se à direção na qual o dispositivo de data Box Heavy envia dados externamente, além da implantação: por exemplo, de saída para a Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]

## <a name="next-steps"></a>Próximas etapas

* [Implante seu Azure Data Box](data-box-deploy-ordered.md)
