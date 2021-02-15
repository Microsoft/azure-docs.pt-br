---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/08/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f3eb2d9469ab3a3d2c1d09e4adc3ee2cb1f86e6e
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979055"
---
## <a name="extract-the-zip-file"></a>Extrair o arquivo zip

Extraia o arquivo zip. O arquivo contém as seguintes pastas:

* AzureVPN
* Genérico
* OpenVPN (se você tiver habilitado o OpenVPN com o **certificado do Azure** ou com as configurações de **autenticação RADIUS** no gateway). Selecione o artigo apropriado que corresponde à sua configuração para criar um locatário.

  * [Gateway de VPN-criar um locatário](../articles/vpn-gateway/openvpn-azure-ad-tenant.md).
  * [Wan virtual-criar um locatário](../articles/virtual-wan/openvpn-azure-ad-tenant.md).

## <a name="retrieve-information"></a>Recuperar informações

Na pasta **AzureVPN** , navegue até o arquivo de **_azurevpnconfig.xml_** e abra-o com o bloco de notas. Anote o texto entre as marcas a seguir.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Detalhes do perfil

Ao adicionar uma conexão, use as informações coletadas na etapa anterior na página de detalhes do perfil. Os campos correspondem às seguintes informações:

* **Público-alvo:** Identifica o recurso de destinatário para o qual o token se destina.
* **Emissor:** Identifica o serviço de token de segurança (STS) que emitiu o token, bem como o locatário do Azure AD.
* **Locatário:** Contém um identificador exclusivo e imutável do locatário do diretório que emitiu o token.
* **FQDN:** O FQDN (nome de domínio totalmente qualificado) no gateway de VPN do Azure.
* **ServerSecret:** A chave pré-compartilhada do gateway de VPN.

## <a name="folder-contents"></a>Conteúdo da pasta

* O **pasta genérica** contém o certificado do servidor público e o arquivo VpnSettings.xml. O arquivo VpnSettings.xml contém as informações necessárias para configurar um cliente genérico.

* O arquivo zip baixado também pode conter as pastas **WindowsAmd64** e **WindowsX86**. Essas pastas contêm o instalador para SSTP e IKEv2 para clientes Windows. Você precisa de direitos de administrador no cliente para instalá-los.
