---
title: Solucionar problemas de volume do NFSv 4.1 Kerberos para Azure NetApp Files | Microsoft Docs
description: Descreve mensagens de erro e resoluções que podem ajudar a solucionar problemas de volume do NFSv 4.1 Kerberos para Azure NetApp Files.
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
ms.date: 01/12/2021
ms.author: b-juche
ms.openlocfilehash: 638607da02b1db4842cdc04f86a4fed1860c243f
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98134306"
---
# <a name="troubleshoot-nfsv41-kerberos-volume-issues"></a>Solucionar problemas de volume do NFSv 4.1 Kerberos 

Este artigo descreve as resoluções para condições de erro que você pode ter ao criar ou gerenciar volumes Kerberos NFSv 4.1. 

## <a name="error-conditions-and-resolutions"></a>Condições de erro e resoluções

|     Condições de erro    |     Resoluções    |
|-|-|
|`Error allocating volume - Export policy rules does not match kerberosEnabled flag` | Azure NetApp Files não dá suporte a Kerberos para volumes NFSv3. O Kerberos tem suporte apenas para o protocolo NFSv 4.1.  |
|`This NetApp account has no configured Active Directory   connections`  |  Configure Active Directory para a conta do NetApp com campos de **IP KDC** e **nome do servidor AD**. Consulte [Configurar o portal do Azure](configure-kerberos-encryption.md#configure-the-azure-portal) para obter instruções. |
|`Mismatch between KerberosEnabled flag value and ExportPolicyRule's access type parameter values.`  | Azure NetApp Files não dá suporte à conversão de um volume NFSv 4.1 simples para o volume Kerberos NFSv 4.1 e vice-versa. |
|`mount.nfs: access denied by server when mounting volume <SMB_SERVER_NAME-XXX.DOMAIN_NAME>/<VOLUME_NAME>` <br>  Exemplo: `smb-test-64d9.contoso.com:/nfs41-vol101` | <ol><li> Verifique se os registros a/PTR estão configurados corretamente e se existem no Active Directory para o nome do servidor `smb-test-64d9.contoso.com` . <br> No cliente NFS, se `nslookup` `smb-test-64d9.contoso.com` for resolvido para o endereço IP IP1 (ou seja, `10.1.1.68` ), o `nslookup` IP1 deverá resolver para apenas um registro (ou seja, `smb-test-64d9.contoso.com` ). `nslookup` do IP1 não *deve* ser resolvido para vários nomes. </li>  <li>Defina o AES-256 para a conta do computador NFS do tipo `NFS-<Smb NETBIOS NAME>-<few random characters>` no AD usando o PowerShell ou a interface do usuário. <br> Exemplo de comandos: <ul><li>`Set-ADComputer <NFS_MACHINE_ACCOUNT_NAME> -KerberosEncryptionType AES256` </li><li>`Set-ADComputer NFS-SMB-TEST-64 -KerberosEncryptionType AES256` </li></ul> </li> <li>Verifique se o horário do cliente NFS, do AD e do software de armazenamento Azure NetApp Files está sincronizado entre si e está dentro de um intervalo de distorção de cinco minutos. </li>  <li>Obtenha o tíquete Kerberos no cliente NFS usando o comando `kinit <administrator>` .</li> <li>Reduza o nome do host do cliente NFS para menos de 15 caracteres e execute a junção de realm novamente. </li><li>Reinicie o cliente NFS e o `rpcgssd` serviço da seguinte maneira. O comando pode variar dependendo do sistema operacional.<br> RHEL 7: <br> `service nfs restart` <br> `service rpcgssd restart` <br> CentOS 8: <br> `systemctl enable nfs-client.target && systemctl start nfs-client.target` <br> Ubuntu: <br> (Reinicie o `rpc-gssd` serviço.) <br> `sudo systemctl start rpc-gssd.service` </ul>| 
|`mount.nfs: an incorrect mount option was specified`   | O problema pode estar relacionado ao problema do cliente NFS. Reinicialize o cliente NFS.    |
|`Hostname lookup failed`   | Você precisa criar uma zona de pesquisa inversa no servidor DNS e, em seguida, adicionar um registro PTR da máquina host do AD nessa zona de pesquisa inversa. <br> Por exemplo, suponha que o endereço IP do computador do AD seja `10.1.1.4` , o nome do host do computador do AD (como encontrado usando o comando hostname) é `AD1` , e o Name de domínio é `contoso.com` . O registro PTR adicionado à zona de pesquisa inversa deve ser `10.1.1.4 -> AD1.contoso.com` . |
|`Volume creation fails due to unreachable DNS server`  | Duas soluções possíveis estão disponíveis: <br> <ul><li> Esse erro indica que o DNS não está acessível. O motivo pode ser um IP DNS incorreto ou um problema de rede. Verifique o IP do DNS inserido na conexão do AD e verifique se o IP está correto. </li> <li> Certifique-se de que o AD e o volume estejam na mesma região e na mesma VNet. Se estiverem em VNets diferentes, verifique se o emparelhamento VNet é estabelecido entre os dois VNets. </li></ul> |
|A criação do volume Kerberos NFSv 4.1 falha com um erro semelhante ao exemplo a seguir: <br> `Failed to enable NFS Kerberos on LIF "svm_e719cde8d6d0413fbd6adac0636cdecb_7ad0b82e_73349613". Failed to bind service principal name on LIF "svm_e719cde8d6d0413fbd6adac0636cdecb_7ad0b82e_73349613". SecD Error: server create fail join user auth.` |O IP KDC está errado e o volume Kerberos foi criado. Atualize o IP KDC com um endereço correto. <br> Depois de atualizar o IP do KDC, o erro não vai desaparecer. Você precisa recriar o volume. |

## <a name="next-steps"></a>Próximas etapas  

* [Configurar a criptografia Kerberos do NFSv 4.1 para Azure NetApp Files](configure-kerberos-encryption.md)
