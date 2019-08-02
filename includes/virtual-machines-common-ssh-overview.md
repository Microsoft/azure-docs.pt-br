---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 12/21/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: bca78e2963f19b60071b1b27c8dc65c76818e10e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171932"
---
## <a name="overview-of-ssh-and-keys"></a>Visão geral do SSH e das chaves

O [SSH](https://www.ssh.com/ssh/) é um protocolo de conexão criptografado que permite credenciais seguras em conexões não protegidas. SSH é o protocolo de conexão padrão para as VMs Linux hospedadas no Azure. Embora o SSH em si forneça uma conexão criptografada, usar senhas com conexões SSH ainda deixa a VM vulnerável a ataques de força bruta ou à adivinhação de senhas. Um método mais seguro e preferencial de se conectar a uma VM usando SSH é usar um par de chaves públicas e privadas, também conhecidas como *chaves SSH*. 

* A *chave pública* é colocada na VM Linux ou em qualquer outro serviço que você queira usar com criptografia de chave pública.

* A *chave privada* permanece em seu sistema local. Proteja essa chave privada. Não a compartilhe.

Quando você usa um cliente SSH para se conectar à sua VM do Linux (que tem a chave pública), a VM remota testa o cliente para garantir que ele tenha a chave privada. Se o cliente tiver a chave privada, será concedido o acesso à VM. 

Dependendo das políticas de segurança da sua organização, você pode reutilizar um único par de chaves públicas e privadas para acessar vários serviços e VMs do Azure. Não é preciso um par de chaves separado para cada VM ou serviço que você deseja acessar. 

Sua chave pública pode ser compartilhada com qualquer pessoa, mas apenas você (ou sua infraestrutura de segurança local) possui sua chave privada.