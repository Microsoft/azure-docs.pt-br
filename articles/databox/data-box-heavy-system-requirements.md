---
title: Requisitos de sistema pesadas de caixa de dados do Microsoft Azure | Microsoft Docs
description: Saiba mais sobre o software e requisitos de rede para seu pesada de caixa de dados do Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 6097dba9193fce4c8158137b488080107881994a
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839772"
---
# <a name="azure-data-box-heavy-system-requirements"></a>Requisitos de sistema pesadas de caixa de dados do Azure

Este artigo descreve os requisitos de sistema importantes para seu dispositivo pesada de caixa de dados do Azure e para clientes que se conectam ao dispositivo. Recomendamos que você examine as informações com atenção antes de implantar seu pesada de caixa de dados e, em seguida, voltar a ela conforme necessário durante a implantação e operação subsequente.

Os requisitos do sistema incluem:

* **Requisitos de software para hosts que se conectam a dados caixa pesada** -descreve as plataformas com suporte, navegadores da Web local da interface do usuário, os clientes SMB e quaisquer requisitos adicionais para hosts que podem se conectar à caixa de dados.
* **Requisitos de rede para os dados de caixa pesada** -fornece informações sobre os requisitos de rede para a operação ideal do dispositivo pesada de caixa de dados.

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

Seu datacenter deve ter uma rede de alta velocidade. Para velocidades de cópia mais rápidas, duas conexões de 40 GbE podem ser utilizadas em paralelo (uma por nó). Se você não tenha 40-GbE disponível, recomendamos que você tenha pelo menos duas conexões de 10 GbE (uma por nó).

### <a name="port-requirements"></a>Requisitos de porta

A tabela a seguir lista as portas que precisam ser abertas no firewall para permitir o tráfego SMB ou NFS. Nesta tabela, *entrada* ou *de entrada* refere-se à direção da qual as solicitações do cliente acessam o dispositivo. *-Out* ou *saída* refere-se à direção na qual o seu dispositivo pesada de caixa de dados envia dados externamente, além da implantação: por exemplo, saída à Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]

## <a name="next-steps"></a>Próximas etapas

* [Implante seu Azure Data Box](data-box-deploy-ordered.md)
