---
title: Criar certificados usando a ferramenta Verificador de preparação do Hub Microsoft Azure Stack | Microsoft Docs
description: Descreve como criar solicitações de certificado e obter e instalar certificados em seu dispositivo Azure Stack Edge pro GPU usando a ferramenta do verificador de prontidão do Hub Azure Stack.
services: Azure Stack Edge Pro
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: a04243093b89b6a2498efc48f80cbd7a47d57337
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102437716"
---
# <a name="create-certificates-for-your-azure-stack-edge-pro-using-azure-stack-hub-readiness-checker-tool"></a>Criar certificados para seu Azure Stack Edge pro usando a ferramenta Verificador de prontidão do Hub Azure Stack 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo descreve como criar certificados para seu Azure Stack Edge pro usando a ferramenta Verificador de prontidão do Hub Azure Stack. 

## <a name="using-azure-stack-hub-readiness-checker-tool"></a>Usando a ferramenta Verificador de preparação do Hub Azure Stack

Use a ferramenta Verificador de preparação do Hub Azure Stack para criar solicitações de assinatura de certificado (SACs) para uma implantação de dispositivo do Azure Stack Edge pro. Você pode criar essas solicitações depois de fazer um pedido para o Azure Stack dispositivo pro Edge e aguardar a chegada do dispositivo.

> [!NOTE]
> Use essa ferramenta somente para fins de teste ou desenvolvimento e não para dispositivos de produção. 

Você pode usar a AzsReadinessChecker (ferramenta Verificador de preparação do Hub) do Azure Stack para solicitar os seguintes certificados:

- Azure Resource Manager certificado
- Certificado de interface do usuário local
- Certificado do nó
- Certificado de BLOB
- Certificado VPN


## <a name="prerequisites"></a>Pré-requisitos

Para criar representantes do SAC para Azure Stack a implantação de dispositivo do Edge pro, verifique se: 

- Você tem um cliente que executa o Windows 10 ou o Windows Server 2016 ou posterior. 
- Você baixou a ferramenta Verificador de preparação do Hub Microsoft Azure Stack [do Galeria do PowerShell](https://aka.ms/AzsReadinessChecker) neste sistema.
- Você tem as seguintes informações para os certificados:
  - Nome do dispositivo
  - Número de série do nó
  - FQDN (nome de domínio totalmente qualificado) externo

## <a name="generate-certificate-signing-requests"></a>Gerar solicitações de assinatura de certificado

Use estas etapas para preparar os certificados de dispositivo do Azure Stack Edge pro:

1. Execute o PowerShell como administrador (5,1 ou posterior).
2. Instale a ferramenta Verificador de preparação do Hub Azure Stack. No prompt do PowerShell, digite: 

    ```azurepowershell
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker
    ```

    Para obter a versão instalada, digite:  

    ```azurepowershell
    Get-InstalledModule -Name Microsoft.AzureStack.ReadinessChecker  | ft Name, Version 
    ```

3. Crie um diretório para todos os certificados se você ainda não tiver um. Tipo: 
    
    ```azurepowershell
    New-Item "C:\certrequest" -ItemType Directory
    ``` 
    
4. Para criar uma solicitação de certificado, forneça as informações a seguir. Se você estiver gerando um certificado VPN, algumas dessas entradas não se aplicarão.
    
    |Entrada |Descrição  |
    |---------|---------|
    |`OutputRequestPath`|O caminho do arquivo no cliente local em que você deseja que as solicitações de certificado sejam criadas.        |
    |`DeviceName`|O nome do seu dispositivo na página **dispositivos** na interface do usuário da Web local do seu dispositivo. <br> Esse campo não é necessário para um certificado VPN.         |
    |`NodeSerialNumber`|O número de série do nó do dispositivo na página **rede** na interface do usuário da Web local do seu dispositivo. <br> Esse campo não é necessário para um certificado VPN.       |
    |`ExternalFQDN`|O valor DNSDomain na página **dispositivos** na interface do usuário da Web local do seu dispositivo.         |
    |`RequestType`|O tipo de solicitação pode ser para `MultipleCSR` certificados diferentes para os vários pontos de extremidade ou `SingleCSR` um único certificado para todos os pontos de extremidade. <br> Esse campo não é necessário para um certificado VPN.     |

    Para todos os certificados, exceto o certificado VPN, digite: 
    
    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeDEVICE'
        DeviceName = 'myTEA1'
        NodeSerialNumber = 'VM1500-00025'
        externalFQDN = 'azurestackedge.contoso.com'
        requestType = 'MultipleCSR'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    Se você estiver criando um certificado VPN, digite: 

    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeVPN'
        externalFQDN = 'azurestackedge.contoso.com'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    
5. Você encontrará os arquivos de solicitação de certificado no diretório especificado no parâmetro OutputRequestPath acima. Ao usar o `MultipleCSR` parâmetro, você verá os quatro arquivos a seguir com a `.req` extensão:

    
    |Nomes de arquivo  |Tipo de solicitação de certificado  |
    |---------|---------|
    |Começando com seu `DeviceName`     |Solicitação de certificado de interface do usuário da Web local      |
    |Começando com seu `NodeSerialNumber`     |Solicitação de certificado de nó         |
    |A partir do `login`     |Solicitação de certificado de ponto de extremidade Azure Resource Manager       |
    |A partir do `wildcard`     |Solicitação de certificado de armazenamento de BLOBs. Ele contém um caractere curinga porque abrange todas as contas de armazenamento que você pode criar no dispositivo.          |
    |A partir do `AzureStackEdgeVPNCertificate`     |Solicitação de certificado de cliente VPN.         |

    Você também verá uma pasta INF. Ele contém um arquivo de informações de Management. <Edge-DeviceName> em texto não criptografado, explicando os detalhes do certificado.  


6. Envie esses arquivos para sua autoridade de certificação (interna ou pública). Certifique-se de que sua autoridade de certificação gere certificados, usando a solicitação gerada, que atendam aos requisitos de certificado do Azure Stack Edge pro para [certificados de nó](azure-stack-edge-gpu-manage-certificates.md#node-certificates), certificados de [ponto de extremidade](azure-stack-edge-gpu-manage-certificates.md#endpoint-certificates)e [certificados de interface do usuário local](azure-stack-edge-gpu-manage-certificates.md#local-ui-certificates).

## <a name="prepare-certificates-for-deployment"></a>Preparar certificados para implantação

Os arquivos de certificado obtidos da autoridade de certificação (CA) devem ser importados e exportados com propriedades que correspondem aos requisitos de certificado do dispositivo pro Azure Stack Edge. Conclua as etapas a seguir no mesmo sistema em que você gerou as solicitações de assinatura de certificado.

- Para importar os certificados, siga as etapas em [importar certificados nos clientes que acessam seu dispositivo Azure Stack Edge pro](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).

- Para exportar os certificados, siga as etapas em [Exportar certificados do cliente que acessa o dispositivo pro Edge Azure Stack](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).


## <a name="validate-certificates"></a>Validar certificados

Primeiro, você gerará uma estrutura de pastas apropriada e colocará os certificados nas pastas correspondentes. Somente então você validará os certificados usando a ferramenta.

1. Execute o PowerShell como administrador.

2. Para gerar a estrutura de pastas apropriada, no prompt, digite:

    `New-AzsCertificateFolder -CertificateType AzureStackEdge -OutputPath "$ENV:USERPROFILE\Documents\AzureStackCSR"`

3. Converta a senha PFX em uma cadeia de caracteres segura. Tipo:       

    `$pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString` 

4. Em seguida, valide os certificados. Tipo:

    `Invoke-AzsCertificateValidation -CertificateType AzureStackEdge -DeviceName mytea1 -NodeSerialNumber VM1500-00025 -externalFQDN azurestackedge.contoso.com -CertificatePath $ENV:USERPROFILE\Documents\AzureStackCSR\AzureStackEdge -pfxPassword $pfxPassword`

## <a name="next-steps"></a>Próximas etapas

[Implantar o dispositivo pro Edge Azure Stack](azure-stack-edge-gpu-deploy-prep.md)
