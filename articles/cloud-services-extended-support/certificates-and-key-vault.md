---
title: Armazenar e usar certificados nos Serviços de Nuvem do Azure (suporte estendido)
description: Processos para armazenar e usar certificados nos serviços de nuvem do Azure (suporte estendido)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 7357703af41afc913ef63dff6ecae3d230c9eca0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104583285"
---
# <a name="use-certificates-with-azure-cloud-services-extended-support"></a>Usar certificados com os serviços de nuvem do Azure (suporte estendido)

O Key Vault é usado para armazenar certificados associados aos Serviços de Nuvem (suporte estendido). Os cofres de chaves podem ser criados por meio do [portal do Azure](../key-vault/general/quick-create-portal.md) e do [PowerShell](../key-vault/general/quick-create-powershell.md). Adicione os certificados a Key Vault e, em seguida, referencie as impressões digitais do certificado no arquivo de configuração de serviço. Você também precisa habilitar o Key Vault para as permissões adequadas para que o recurso dos Serviços de Nuvem (suporte estendido) possa recuperar o certificado armazenado como segredos do Key Vault.  

## <a name="upload-a-certificate-to-key-vault"></a>Carregar um certificado para Key Vault 

1.  Entre no portal do Azure e navegue até a Key Vault. Se você não tiver uma Key Vault configurada, poderá optar por criar uma nessa mesma janela.

2. Selecionar **políticas de acesso**

    :::image type="content" source="media/certs-and-key-vault-1.png" alt-text="Imagem mostra a seleção de políticas de acesso na folha do cofre de chaves.":::

3. Verifique se as políticas de acesso incluem as seguintes propriedades:
    - **Habilitar o acesso às máquinas virtuais do Azure para implantação**
    - **Habilitar o acesso ao Azure Resource Manager para implantação de modelo** 

    :::image type="content" source="media/certs-and-key-vault-2.png" alt-text="Imagem mostra a janela políticas de acesso na portal do Azure.":::
 
4.  Selecionar **certificados** 

    :::image type="content" source="media/certs-and-key-vault-3.png" alt-text="Imagem mostra a seleção da opção certificados na janela políticas de folha do cofre de chaves na portal do Azure.":::

5. Selecionar **gerar/importar**

    :::image type="content" source="media/certs-and-key-vault-4.png" alt-text="Imagem mostra a seleção da opção gerar/importar":::

4.  Conclua as informações necessárias para concluir o carregamento do certificado. O certificado precisa estar no **. Formato PFX** .

    :::image type="content" source="media/certs-and-key-vault-5.png" alt-text="Imagem mostra a janela de importação no portal do Azure.":::

5.  Adicione os detalhes do certificado à sua função no arquivo de configuração do serviço (. cscfg). Verifique se a impressão digital do certificado no portal do Azure corresponde à impressão digital no arquivo de configuração do serviço (. cscfg). 
    
    ```json
    <Certificate name="<your cert name>" thumbprint="<thumbprint in key vault" thumbprintAlgorithm="sha1" /> 
    ```

## <a name="next-steps"></a>Próximas etapas 
- Examine os [pré-requisitos de implantação](deploy-prerequisite.md) para serviços de nuvem (suporte estendido).
- Examine as [perguntas frequentes](faq.md) sobre os Serviços de Nuvem (suporte estendido).
- Implante um Serviço de Nuvem (suporte estendido) usando o [portal do Azure](deploy-portal.md), o [PowerShell](deploy-powershell.md), o [Modelo](deploy-template.md) ou o [Visual Studio](deploy-visual-studio.md).
