---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/09/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 612e7dc2f3d1ef6aa5d3598999aa214a2f377e1f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96016346"
---
## <a name="overview-of-ssh-and-keys"></a>Visão geral do SSH e das chaves

O [SSH](https://www.ssh.com/ssh/) é um protocolo de conexão criptografado que fornece entradas seguras em conexões não seguras. SSH é o protocolo de conexão padrão para as VMs Linux hospedadas no Azure. Embora o SSH forneça uma conexão criptografada, o uso de senhas com conexões SSH ainda deixa a VM vulnerável a ataques de força bruta. É recomendável conectar-se a uma VM por SSH usando um par de chaves pública-privada, também conhecido como *chaves SSH*. 

- A *chave pública* é colocada em sua VM Linux.

- A *chave privada* permanece em seu sistema local. Proteja essa chave privada. Não a compartilhe.

Quando você usa um cliente SSH para se conectar à VM do Linux (que tem a chave pública), a VM remota testa o cliente para verificar se ele tem a chave privada correta. Se o cliente tiver a chave privada, será concedido o acesso à VM. 

Dependendo das políticas de segurança da sua organização, você pode reutilizar um único par de chaves públicas e privadas para acessar vários serviços e VMs do Azure. Não é preciso um par de chaves separado para cada VM ou serviço que você deseja acessar. 

Sua chave pública pode ser compartilhada com qualquer pessoa, mas apenas você (ou sua infraestrutura de segurança local) deve ter acesso à sua chave privada.