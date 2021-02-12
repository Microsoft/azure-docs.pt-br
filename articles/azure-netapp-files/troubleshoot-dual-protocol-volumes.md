---
title: Solucionar problemas de volumes de protocolo duplo para Azure NetApp Files | Microsoft Docs
description: Descreve mensagens de erro e resoluções que podem ajudá-lo a solucionar problemas de protocolo duplo para Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/22/2021
ms.author: b-juche
ms.openlocfilehash: fb4233a87231dddb1e3cb2777ac2ef53a61f833e
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98726608"
---
# <a name="troubleshoot-dual-protocol-volumes"></a>Solucionar problemas de volumes de protocolo duplo

Este artigo descreve as resoluções para condições de erro que você pode ter ao criar ou gerenciar volumes de protocolo duplo.

## <a name="error-conditions-and-resolutions"></a>Condições de erro e resoluções

|     Condições de erro    |     Resolução    |
|-|-|
| O LDAP sobre TLS está habilitado e a criação de volume de protocolo duplo falha com o erro `This Active Directory has no Server root CA Certificate` .    |     Se esse erro ocorrer quando você estiver criando um volume de protocolo duplo, certifique-se de que o certificado de autoridade de certificação raiz seja carregado em sua conta do NetApp.    |
| A criação de volume de protocolo duplo falha com o erro `Failed to validate LDAP configuration, try again after correcting LDAP configuration` .    |  O registro de ponteiro (PTR) do computador host do AD pode estar ausente no servidor DNS. Você precisa criar uma zona de pesquisa inversa no servidor DNS e, em seguida, adicionar um registro PTR da máquina host do AD nessa zona de pesquisa inversa. <br> Por exemplo, suponha que o endereço IP do computador do AD seja `1.1.1.1` , o nome do host do computador do AD (como encontrado usando o `hostname` comando) é `AD1` , e que é `contoso.com` .  O registro PTR adicionado à zona de pesquisa inversa deve ser `1.1.1.1`  ->  `contoso.com` .   |
| A criação de volume de protocolo duplo falha com o erro `Failed to create the Active Directory machine account \\\"TESTAD-C8DD\\\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\\n [ 434] Loaded the preliminary configuration.\\n [ 537] Successfully connected to ip 1.1.1.1, port 88 using TCP\\n**[ 950] FAILURE` . |  Esse erro indica que a senha do AD está incorreta quando Active Directory é unida à conta do NetApp. Atualize a conexão do AD com a senha correta e tente novamente. |
| A criação de volume de protocolo duplo falha com o erro `Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available` . |   Esse erro indica que o DNS não está acessível. O motivo pode ser porque o IP do DNS está incorreto ou há um problema de rede. Verifique o IP do DNS inserido na conexão do AD e verifique se o IP está correto. <br> Além disso, certifique-se de que o AD e o volume estejam na mesma região e na mesma VNet. Se estiverem em VNETs diferentes, verifique se o emparelhamento VNet é estabelecido entre os dois VNets.|
| A permissão é negada ao montar um volume de protocolo duplo. | Um volume de protocolo duplo dá suporte aos protocolos NFS e SMB.  Quando você tenta acessar o volume montado no sistema UNIX, o sistema tenta mapear o usuário do UNIX que você usa para um usuário do Windows. Se nenhum mapeamento for encontrado, o erro "permissão negada" ocorrerá. <br> Essa situação se aplica também quando você usa o usuário ' raiz ' para o acesso. <br> Para evitar o problema de "permissão negada", verifique se o Windows Active Directory inclui `pcuser` antes de acessar o ponto de montagem. Se você adicionar `pcuser` depois de encontrar o problema de "permissão negada", Aguarde 24 horas para que a entrada do cache seja limpa antes de tentar acessar novamente. |

## <a name="next-steps"></a>Próximas etapas  

* [Criar um volume de protocolo duplo](create-volumes-dual-protocol.md)
* [Configurar um cliente NFS para o Azure NetApp Files](configure-nfs-clients.md)
