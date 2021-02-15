---
title: Requisitos de certificados e solução de problemas com o Azure Stack Edge pro | Microsoft Docs
description: Descreve os requisitos de certificados e a solução de problemas de erros de certificado com o Azure Stack dispositivo pro Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/17/2020
ms.author: alkohli
ms.openlocfilehash: de41bd030ea73ac68bfac5fbfbd03ae14cf7980f
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874229"
---
# <a name="certificate-requirements"></a>Requisitos de certificado

Este artigo descreve os requisitos de certificado que devem ser atendidos antes que os certificados possam ser instalados em seu dispositivo Azure Stack Edge pro. Os requisitos estão relacionados a certificados PFX, autoridade emissora, nome da entidade do certificado e nome alternativo da entidade e algoritmos de certificado com suporte.

## <a name="certificate-issuing-authority"></a>Autoridade emissora de certificado

Os requisitos de emissão de certificado são os seguintes:

* Os certificados devem ser emitidos a partir de uma autoridade de certificação interna ou de uma autoridade de certificação pública.

* Não há suporte para o uso de certificados autoassinados.

* O campo *emitido para:* do certificado não deve ser o mesmo que o *emitido por:* campo, exceto para certificados de autoridade de certificação raiz.


## <a name="certificate-algorithms"></a>Algoritmos de certificado

Os algoritmos de certificado devem ter os seguintes requisitos:

* Os certificados devem usar o algoritmo de chave RSA.

* Somente certificados RSA com o provedor de criptografia RSA/Schannel da Microsoft têm suporte.

* O algoritmo de assinatura do certificado não pode ser SHA1.

* O tamanho mínimo da chave é 4096.

## <a name="certificate-subject-name-and-subject-alternative-name"></a>Nome da entidade do certificado e nome alternativo da entidade

Os certificados devem ter o seguinte nome de assunto e requisitos de nome alternativo da entidade:

* Você pode usar um único certificado que abrange todos os espaços de nome nos campos de SAN (nome alternativo da entidade) do certificado. Como alternativa, você pode usar certificados individuais para cada um dos namespaces. Ambas as abordagens exigem o uso de curingas para pontos de extremidade, quando necessário, como BLOB (objeto binário grande).

* Verifique se os nomes de entidade (nome comum no nome da entidade) fazem parte dos nomes alternativos da entidade na extensão de nome alternativo da entidade.

* Você pode usar um único certificado curinga abrangendo todos os espaços de nome nos campos de SAN do certificado.

* Use a tabela a seguir ao criar um certificado de ponto de extremidade:

    |Type |Nome da entidade (SN)  |SAN (nome alternativo da entidade)  |Exemplo de nome da entidade |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Armazenamento de blob|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |Interface do usuário local| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
    |Certificado único de várias SANs para ambos os pontos de extremidade|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |
    |Nó|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
    |VPN|`AzureStackEdgeVPNCertificate.<DnsDomain>`<br><br> * AzureStackEdgeVPNCertificate está embutido em código.  | `*.<DnsDomain>`<br><br>`<AzureStackVPN>.<DnsDomain>` | `edgevpncertificate.microsoftdatabox.com`|
    
## <a name="pfx-certificate"></a>Certificado PFX

Os certificados PFX instalados em seu dispositivo Azure Stack Edge pro devem atender aos seguintes requisitos:

* Quando você obtém seus certificados da autoridade SSL, o certifica-se de obter a cadeia de assinatura completa para os certificados.

* Ao exportar um certificado PFX, certifique-se de ter selecionado a opção **incluir todos os certificados na cadeia, se possível** .

* Use um certificado PFX para ponto de extremidade, interface do usuário local, nó, VPN e Wi-Fi, pois as chaves pública e privada são necessárias para Azure Stack o Edge pro. A chave privada deve ter o atributo de chave do computador local definido.

* A criptografia PFX do certificado deve ser 3DES. Essa é a criptografia padrão usada ao exportar de um cliente do Windows 10 ou do repositório de certificados do Windows Server 2016. Para obter mais informações relacionadas ao 3DES, consulte [Triple DES](https://en.wikipedia.org/wiki/Triple_DES).

* Os arquivos PFX de certificado devem ter uma *assinatura digital* válida e valores de *KeyEncipherment* no campo *uso de chave* .

* Os arquivos PFX de certificado devem ter valores de *autenticação de servidor (1.3.6.1.5.5.7.3.1)* e *autenticação de cliente (1.3.6.1.5.5.7.3.2)* no campo *uso avançado de chave* .

* As senhas para todos os arquivos PFX de certificado devem ser as mesmas no momento da implantação se você estiver usando a ferramenta Azure Stack Readiness Checker. Para obter mais informações, consulte [criar certificados para seu Azure Stack Edge pro usando a ferramenta Verificador de prontidão do Hub do Azure Stack](azure-stack-edge-j-series-create-certificates-tool.md).

* A senha para o PFX do certificado deve ser uma senha complexa. Anote essa senha porque ela é usada como um parâmetro de implantação.

* Use somente certificados RSA com o provedor de criptografia RSA/Schannel da Microsoft.

Para obter mais informações, consulte [Exportar certificados PFX com a chave privada](azure-stack-edge-j-series-manage-certificates.md#export-certificates-as-pfx-format-with-private-key).

## <a name="next-steps"></a>Próximas etapas

[Usar certificados com o Azure Stack Edge pro](azure-stack-edge-j-series-manage-certificates.md)

[Criar certificados para seu Azure Stack Edge pro usando a ferramenta Verificador de prontidão do Hub Azure Stack](azure-stack-edge-j-series-create-certificates-tool.md)

[Exportar certificados PFX com a chave privada](azure-stack-edge-j-series-manage-certificates.md#export-certificates-as-pfx-format-with-private-key)

[Solucionando problemas de erros de certificado](azure-stack-edge-j-series-certificate-troubleshooting.md)