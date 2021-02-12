---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/04/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a1fb1c1be8a0203d9f36712fda8e30f0f9354091
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576084"
---
Você pode implantar perfis para clientes de VPN do Azure (Windows 10) usando Microsoft Intune. Este artigo ajuda você a criar um perfil do Intune usando configurações personalizadas.

## <a name="prerequisites"></a>Pré-requisitos

* Os dispositivos já estão registrados com o MDM do Intune.
* O cliente VPN do Azure para Windows 10 já está implantado no computador cliente.
* Somente o Windows versão 19H2 ou superior tem suporte.

## <a name="modify-xml"></a><a name="xml"></a>Modificar XML

Nas etapas a seguir, usamos um XML de exemplo para um perfil de OMA-URI personalizado para o Intune com as seguintes configurações:

* Conectar automaticamente
* Detecção de rede confiável habilitada.

Para outras opções com suporte, consulte o artigo [CSP VPNv2](https://docs.microsoft.com/windows/client-management/mdm/vpnv2-csp) .

1. Baixe o perfil de VPN do portal do Azure e extraia o arquivo de *azurevpnconfig.xml* do pacote.
1. Copie e cole o texto abaixo em um novo arquivo do editor de texto.

   ```xml-interactive
    <VPNProfile>
      <!--<EdpModeId>corp.contoso.com</EdpModeId>-->
      <RememberCredentials>true</RememberCredentials>
      <AlwaysOn>true</AlwaysOn>
      <TrustedNetworkDetection>contoso.com,test.corp.contoso.com</TrustedNetworkDetection>
      <DeviceTunnel>false</DeviceTunnel>
      <RegisterDNS>false</RegisterDNS>
      <PluginProfile>
        <ServerUrlList>azuregateway-7cee0077-d553-4323-87df-069c331f58cb-053dd0f6af02.vpn.azure.com</ServerUrlList> 
        <CustomConfiguration>
        </CustomConfiguration>
        <PluginPackageFamilyName>Microsoft.AzureVpn_8wekyb3d8bbwe</PluginPackageFamilyName>
      </PluginProfile>
    </VPNProfile>
   ```
1. Modifique a entrada entre ```<ServerUrlList>``` e ```</ServerUrlList>``` com a entrada de seu perfil baixado (azurevpnconfig.xml). Altere o FQDN "TrustedNetworkDetection" para se ajustar ao seu ambiente.
1. Abra o perfil baixado do Azure (azurevpnconfig.xml) e copie o conteúdo para a área de transferência realçando o texto e pressionando <ctrl> + C. Copie tudo entre as seguintes linhas AzVpnProfile, mas não copie as linhas AzVpnProfile em si:

   ```
   <AzVpnProfile xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.datacontract.org/2004/07/">
     <any xmlns:d2p1="http://schemas.datacontract.org/2004/07/System.Xml"
       i:nil="true" />
   For example - copy the text in your xml that is located here.
   </AzVpnProfile>
   ```
1. Cole o texto copiado da etapa anterior no arquivo criado na etapa 2 entre as ```<CustomConfiguration>  </CustomConfiguration>``` marcas. Salve o arquivo com uma extensão XML.
1. Anote o valor nas ```<name>  </name>``` marcas. Este é o nome do perfil. Você precisará desse nome ao criar o perfil no Intune. Feche o arquivo e lembre-se do local onde ele foi salvo.

## <a name="create-intune-profile"></a>Criar perfil do Intune

Nesta seção, você criará um perfil de Microsoft Intune com configurações personalizadas.

1. Entre no Intune e navegue até **dispositivos – > perfis de configuração**. Selecione **+ Criar perfil**.

   :::image type="content" source="./media/vpn-gateway-virtual-wan-vpn-profile-intune/configuration-profile.png" alt-text="Perfis de configuração":::
1. Em **Plataforma**, selecione **Windows 10 e posteriores**. Para **perfil**, selecione **personalizado**. Em seguida, selecione **Criar**.
1. Dê um nome e uma descrição ao perfil e, em seguida, selecione **Avançar**.
1. Na guia **definições de configuração** , selecione **Adicionar**.

    * **Nome:** Insira um nome para a configuração.
    * **Descrição:** Descrição opcional.
    * **OMA-URI:** ```./User/Vendor/MSFT/VPNv2/<name of your connection>/ProfileXML``` (essas informações podem ser encontradas no arquivo de azurevpnconfig.xml na <name></name> marca).
    * **Tipo de dados:** Cadeia de caracteres (arquivo XML).

   Selecione o ícone de pasta e escolha o arquivo que você salvou na etapa 6 nas etapas de [XML](#xml) . Selecione **Adicionar**.

   :::image type="content" source="./media/vpn-gateway-virtual-wan-vpn-profile-intune/configuration-settings.png" alt-text="Definições de configuração" lightbox="./media/vpn-gateway-virtual-wan-vpn-profile-intune/configuration-settings.png":::
1. Selecione **Avançar**.
1. Em **atribuições**, selecione o grupo para o qual você deseja enviar a configuração por push. Em seguida, selecione **Avançar**.
1. As regras de aplicabilidade são opcionais. Defina todas as regras, se necessário, e selecione **Avançar**.
1. Na página **Examinar + criar** escolha **Criar**.

    :::image type="content" source="./media/vpn-gateway-virtual-wan-vpn-profile-intune/create-profile.png" alt-text="Criar perfil":::
1. Seu perfil personalizado agora é criado. Para as Microsoft Intune etapas para implantar esse perfil, consulte [atribuir perfis de usuário e de dispositivo](https://docs.microsoft.com/mem/intune/configuration/device-profile-assign).
