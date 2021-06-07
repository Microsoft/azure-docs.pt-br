---
title: Solucionar problemas em volumes LDAP do Azure NetApp Files | Microsoft Docs
description: Descreve as resoluções para erros que podem surgir ao configurar volumes LDAP para o Azure NetApp Files.
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
ms.date: 04/05/2021
ms.author: b-juche
ms.openlocfilehash: eea3f691bc6d91948dc73b4a02c89abfac12d384
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106498847"
---
# <a name="troubleshoot-ldap-volume-issues"></a>Solucionar problemas de volumes LDAP

Este artigo descreve as resoluções para erros que podem surgir ao configurar volumes LDAP.

## <a name="errors-and-resolutions-for-ldap-volumes"></a>Erros e resoluções para volumes LDAP

|     Condições de erro    |     Resoluções    |
|-|-|
| Erro ao criar um volume SMB com ldapEnabled como verdadeiro: <br> `Error Message: ldapEnabled option is only supported with NFS protocol volume. ` | Não é possível criar um volume SMB com o LDAP habilitado. <br> Crie volumes SMB com o LDAP desabilitado. |
| Erro ao alterar o valor do parâmetro ldapEnabled de um volume: <br> `Error Message: ldapEnabled parameter is not allowed to update` |  Não é possível modificar a configuração de opção LDAP após criar o volume. <br> Não atualize a configuração de opção LDAP em um volume já criado. Veja mais detalhes em [Configurar o ADDS LDAP com grupos estendidos para acesso ao volume NFS](configure-ldap-extended-groups.md). |
| Erro ao criar um volume NFS habilitado para LDAP: <br> `Could not query DNS server` <br> `Sample error message:` <br> `"log": time="2020-10-21 05:04:04.300" level=info msg=Res method=GET url=/v2/Volumes/070d0d72-d82c-c893-8ce3-17894e56cea3  x-correlation-id=9bb9e9fe-abb6-4eb5-a1e4-9e5fbb838813 x-request-id=c8032cb4-2453-05a9-6d61-31ca4a922d85 xresp="200:  {\"created\":\"2020-10-21T05:02:55.000Z\",\"lifeCycleState\":\"error\",\"lifeCycleStateDetails\":\"Error when creating - Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available.\",\"name\":\"smb1\",\"ownerId\ \":\"8c925a51-b913-11e9-b0de-9af5941b8ed0\",\"region\":\"westus2stage\",\"volumeId\":\"070d0d72-d82c-c893-8ce3-` |  O erro ocorre porque o DNS está inacessível. <br> <ul><li> Verifique se você configurou o site correto (escopo do site) para o Azure NetApp Files. </li><li> O motivo pelo qual o DNS está inacessível pode ser um erro no endereço IP do DNS ou problemas de rede. Verifique se o endereço IP do DNS inserido na conexão do AD está correto. </li><li> O AD e o volume precisam estar na mesma região e na mesma VNet. Caso eles estejam em VNets diferentes, estabeleça um emparelhamento entre as duas VNets.</li></ul> |
| Erro ao criar o volume usando um instantâneo: <br> `Aggregate does not exist` | No Azure NetApp Files, não é possível usar o instantâneo de um volume com o LDAP desabilitado para provisionar um novo volume com o LDAP habilitado. <br> Crie um volume com o LDAP desabilitado usando o instantâneo fornecido. |

## <a name="next-steps"></a>Próximas etapas  

* [Configurar o ADDS LDAP com grupos estendidos para acesso ao volume NFS](configure-ldap-extended-groups.md)
* [Criar um volume NFS para o Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Criar um volume de protocolo duplo (NFSv3 e SMB) para o Azure NetApp Files](create-volumes-dual-protocol.md)