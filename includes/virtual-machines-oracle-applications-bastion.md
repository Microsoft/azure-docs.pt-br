---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 35f506235f698fbcf42308e6f0b0f400e925df29
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361538"
---
### <a name="bastion-tier"></a>Camada de bastiões

O host de bastiões é um componente opcional que você pode usar como um servidor de salto para acessar as instâncias de aplicativo e banco de dados. A VM do host de bastiões pode ter um endereço IP público atribuído a ela, embora a recomendação seja configurar uma conexão de ExpressRoute ou VPN site a site com sua rede local para acesso seguro. Além disso, somente SSH (porta 22, Linux) ou RDP (porta 3389, Windows Server) deve ser aberto para tráfego de entrada. Para alta disponibilidade, implante um host de bastiões em duas zonas de disponibilidade ou em um único conjunto de disponibilidade.

Você também pode habilitar o encaminhamento do agente SSH em suas VMs, o que permite que você acesse outras VMs na rede virtual encaminhando as credenciais do seu host bastião. Ou use o túnel SSH para acessar outras instâncias.

Aqui está um exemplo de encaminhamento de agente:

```
ssh -A -t user@BASTION_SERVER_IP ssh -A root@TARGET_SERVER_IP`
```

Esse comando conecta-se à bastiões e, em seguida `ssh` , é executado imediatamente, para que você obtenha um terminal na instância de destino. Talvez seja necessário especificar um usuário diferente da raiz na instância de destino se o cluster estiver configurado de forma diferente. O `-A` argumento encaminha a conexão do agente para que sua chave privada em seu computador local seja usada automaticamente. Observe que o encaminhamento do agente é uma cadeia, portanto `ssh` , o segundo `-A` comando também inclui para que todas as conexões SSH subsequentes iniciadas a partir da instância de destino também usem sua chave privada local.