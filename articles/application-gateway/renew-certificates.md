---
title: Renovar um certificado do Gateway do Aplicativo do Azure
description: Saiba como renovar um certificado associado a um ouvinte de gateway do aplicativo.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 01/20/2021
ms.author: victorh
ms.openlocfilehash: f0c06a94498f4d2481a6e953b959d766c60415fb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98622173"
---
# <a name="renew-application-gateway-certificates"></a>Renovar certificados de Gateway do Aplicativo

Em algum momento, você precisará renovar seus certificados se tiver configurado o gateway de aplicativo para criptografia TLS/SSL.

Você pode renovar um certificado associado a um ouvinte usando o portal do Azure, o Azure PowerShell ou a CLI do Azure:

## <a name="azure-portal"></a>Portal do Azure

Para renovar um certificado de ouvinte do portal, navegue para os ouvinte de gateway do aplicativo. Selecione o ouvinte que tem um certificado que precisa ser renovado e, em seguida, selecione **renovar ou editar certificado selecionado**.

:::image type="content" source="media/renew-certificate/ssl-cert.png" alt-text="Renovar o certificado":::

Carregue seu novo certificado PFX, dê a ele um nome, digite a senha e, em seguida, selecione **salvar**.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para renovar seu certificado usando o Azure PowerShell, use o seguinte script:

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName <ResourceGroup> `
  -Name <AppGatewayName>

$password = ConvertTo-SecureString `
  -String "<password>" `
  -Force `
  -AsPlainText

set-AzApplicationGatewaySSLCertificate -Name <oldcertname> `
-ApplicationGateway $appgw -CertificateFile <newcertPath> -Password $password

Set-AzApplicationGateway -ApplicationGateway $appgw
```
## <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
az network application-gateway ssl-cert update \
  -n "<CertName>" \
  --gateway-name "<AppGatewayName>" \
  -g "ResourceGroupName>" \
  --cert-file <PathToCerFile> \
  --cert-password "<password>"
```

## <a name="next-steps"></a>Próximas etapas

Para saber como configurar o descarregamento de TLS com o gateway de Aplicativo Azure, consulte [Configurar o descarregamento de TLS](./create-ssl-portal.md)