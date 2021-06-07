---
title: Usar perfis de Wi-Fi com Azure Stack dispositivos mini R de borda
description: Descreve como criar perfis de Wi-Fi para dispositivos de Azure Stack Edge de borda em redes corporativas de alta segurança e redes pessoais.
services: databox
author: v-dalc@microsoft.com
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/24/2021
ms.author: alkohli
ms.openlocfilehash: 90c7c238cef104eae78618e51fa4b284adcc8f42
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105050419"
---
# <a name="use-wi-fi-profiles-with-azure-stack-edge-mini-r-devices"></a>Usar perfis de Wi-Fi com Azure Stack dispositivos mini R de borda

Este artigo descreve como usar perfis de rede sem fio (Wi-Fi) com seus dispositivos de borda de Azure Stack mini R.

A maneira como você prepara o perfil de Wi-Fi depende do tipo de rede sem fio:

- Em uma rede pessoal do Wi-Fi Protected Access 2 (WPA2), como uma rede doméstica ou Wi-Fi hotspot aberto, você poderá baixar e usar um perfil sem fio existente com a mesma senha usada com outros dispositivos.

- Em um ambiente corporativo de alta segurança, você acessará seu dispositivo em uma rede WPA2-Enterprise. Nesse tipo de rede, cada computador cliente terá um perfil de Wi-Fi distinto e será autenticado por meio de certificados. Você precisará trabalhar com o administrador da rede para determinar a configuração necessária.

Abordaremos os requisitos de perfil para os dois tipos de rede mais tarde.

Em ambos os casos, é muito importante verificar se o perfil atende aos requisitos de segurança da sua organização antes de testar ou usar o perfil com seu dispositivo.

## <a name="about-wi-fi-profiles"></a>Sobre perfis de Wi-Fi

Um perfil de Wi-Fi contém o SSID (identificador do conjunto de serviços ou **nome da rede**), a chave de senha e as informações de segurança necessárias para conectar seu dispositivo de borda do Azure Stack o mini R a uma rede sem fio.

O exemplo de código a seguir mostra as configurações básicas de um perfil a ser usado com uma rede sem fio típica:

* `SSID` é o nome da rede.

* `name` é o nome amigável para a conexão de Wi-Fi. Esse é o nome que os usuários verão quando procurarem as conexões disponíveis em seu dispositivo.

* O perfil é configurado para conectar automaticamente o computador à rede sem fio quando ele está dentro do intervalo da rede ( `connectionMode`  =  `auto` ).

```html
<?xml version="1.0"?>
<WLANProfile xmlns="http://www.contoso.com/networking/WLAN/profile/v1">
    <name>ContosoWIFICORP</name>
    <SSIDConfig>
        <SSID>
            <hex>1A234561234B5012</hex>
        </SSID>
        <nonBroadcast>false</nonBroadcast>
    </SSIDConfig>
    <connectionType>ESS</connectionType>
    <connectionMode>auto</connectionMode>
    <autoSwitch>false</autoSwitch>
```

Para obter mais informações sobre Wi-Fi configurações de perfil, consulte **Enterprise Profile** em [Add Wi-Fi Settings for Windows 10 e dispositivos mais recentes](/mem/intune/configuration/wi-fi-settings-windows#enterprise-profile)e consulte [Configure Cisco Wi-Fi Profile](azure-stack-edge-mini-r-manage-wifi.md#configure-cisco-wi-fi-profile).

Para habilitar conexões sem fio em um dispositivo de borda Azure Stack mini R, configure a porta Wi-Fi em seu dispositivo e, em seguida, adicione os perfis de Wi-Fi ao dispositivo. Em uma rede corporativa, você também carregará certificados para o dispositivo. Em seguida, você pode se conectar a uma rede sem fio da interface do usuário da Web local para o dispositivo. Para obter mais informações, consulte [gerenciar a conectividade sem fio no seu Azure Stack Edge mini R](./azure-stack-edge-mini-r-manage-wifi.md).

## <a name="profile-for-wpa2---personal-network"></a>Perfil da rede WPA2-Pessoal

Em um WPA2 (Wi-Fi Protected Access 2) – rede pessoal, como uma rede doméstica ou Wi-Fi hotspot aberto, vários dispositivos podem usar o mesmo perfil e a mesma senha. Em sua rede doméstica, seu telefone celular e laptop usam o mesmo perfil e senha sem fio para se conectar à rede.

Por exemplo, um cliente do Windows 10 pode gerar um perfil de tempo de execução para você. Quando você entrar na rede sem fio, será solicitada a Wi-Fi senha e, depois de fornecer essa senha, você estará conectado. Nenhum certificado é necessário neste ambiente.

Nesse tipo de rede, você pode exportar um perfil de Wi-Fi do seu laptop e, em seguida, adicioná-lo ao dispositivo de borda do Azure Stack mini R. Para obter instruções, consulte [exportar um perfil de Wi-Fi](#export-a-wi-fi-profile), abaixo.

> [!IMPORTANT]
> Antes de criar um perfil de Wi-Fi para o dispositivo de mini-R do Azure Stack Edge, entre em contato com o administrador da rede para descobrir os requisitos de segurança da organização para a rede sem fio. Você não deve testar ou usar nenhum perfil de Wi-Fi no seu dispositivo até saber que a rede sem fio atende aos requisitos.

## <a name="profiles-for-wpa2---enterprise-network"></a>Perfis para a rede WPA2-Enterprise

Em uma rede do WPA2 (Wireless Protected Access 2)-Enterprise, você precisará trabalhar com o administrador da rede para obter o perfil de Wi-Fi necessário e o certificado para conectar o dispositivo de borda do Azure Stack mini R à rede.

Para redes altamente seguras, o dispositivo do Azure pode usar o protocolo PEAP com autenticação extensível Protocol-Transport segurança de camada (EAP-TLS). O PEAP com EAP-TLS usa a autenticação de máquina: o cliente e o servidor usam certificados para verificar suas identidades entre si.

> [!NOTE]
> * Não há suporte para a autenticação de usuário usando o protocolo PEAP MSCHAPv2 (Challenge Handshake Authentication Protocol versão 2) do PEAP no Azure Stack dispositivos mini R de borda.
> * A autenticação EAP-TLS é necessária para acessar Azure Stack funcionalidade de mini R do Edge. Uma conexão sem fio que você configura usando Active Directory não funcionará.

O administrador de rede irá gerar um perfil de Wi-Fi exclusivo e um certificado de cliente para cada computador. O administrador de rede decide se deve usar um certificado separado para cada dispositivo ou um certificado compartilhado.

Se você trabalhar em mais de uma localização física no local de trabalho, o administrador de rede poderá precisar fornecer mais de um perfil de Wi-Fi específico do site e um certificado para suas conexões sem fio.

Em uma rede corporativa, recomendamos que você não altere as configurações nos perfis de Wi-Fi que o administrador de rede fornece. O único ajuste que você pode querer fazer é nas configurações de conexão automática. Para obter mais informações, consulte [perfil básico](/mem/intune/configuration/wi-fi-settings-windows#basic-profile) em configurações de Wi-Fi para dispositivos Windows 10 e mais recentes.

Em um ambiente corporativo de alta segurança, você poderá usar um perfil de rede sem fio existente como um modelo:

* Você pode baixar o perfil de rede sem fio corporativa do seu computador de trabalho. Para obter instruções, consulte [exportar um perfil de Wi-Fi](#export-a-wi-fi-profile), abaixo.

* Se outras pessoas em sua organização já estiverem se conectando a seus dispositivos de borda Azure Stack mini R por meio de uma rede sem fio, eles poderão baixar o perfil de Wi-Fi de seu dispositivo. Para obter instruções, consulte [baixar o perfil de Wi-Fi](azure-stack-edge-mini-r-manage-wifi.md#download-wi-fi-profile).

## <a name="export-a-wi-fi-profile"></a>Exportar um perfil de Wi-Fi

Para exportar um perfil para a interface de Wi-Fi em seu computador, siga estas etapas:

1. Para ver os perfis sem fio no seu computador, no menu **Iniciar** , abra o **prompt de comando** (cmd.exe) e digite este comando:

   `netsh wlan show profiles`

   A saída será parecida com esta:

   ```dos
   Profiles on interface Wi-Fi:

   Group policy profiles (read only)
   ---------------------------------
       <None>

   User profiles
   -------------
       All User Profile     : ContosoCORP
       All User Profile     : ContosoFTINET
       All User Profile     : GusIS2809
       All User Profile     : GusGuests
       All User Profile     : SeaTacGUEST
       All User Profile     : Boat
   ```

2. Para exportar um perfil, digite o seguinte comando:

   `netsh wlan export profile name=”<profileName>” folder=”<path>\<profileName>"`

   Por exemplo, o comando a seguir salva o perfil ContosoFTINET no formato XML na pasta downloads para o usuário chamado `gusp` .

   ```dos
   C:\Users\gusp>netsh wlan export profile name="ContosoFTINET" folder=c:Downloads

   Interface profile "ContosoFTINET" is saved in file "c:Downloads\ContosoFTINET.xml" successfully.
   ```

## <a name="add-certificate-wi-fi-profile-to-device"></a>Adicionar certificado, Wi-Fi perfil ao dispositivo

Quando você tiver os perfis de Wi-Fi e os certificados necessários, siga estas etapas para configurar seu dispositivo de borda de Azure Stack mini R para conexões sem fio:

1. Para uma rede WPA2-Enterprise, carregue os certificados necessários para o dispositivo seguindo as orientações em [carregar certificados](./azure-stack-edge-gpu-manage-certificates.md#upload-certificates).

1. Carregue os perfis de Wi-Fi para o dispositivo de mini R e, em seguida, conecte-se a ele seguindo as orientações em [Adicionar, conectar-se ao perfil de Wi-Fi](./azure-stack-edge-mini-r-manage-wifi.md#add-connect-to-wi-fi-profile).

## <a name="next-steps"></a>Próximas etapas

- Saiba como [Configurar a rede para o Azure Stack o mini R Edge](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md).
- Saiba como [gerenciar Wi-Fi em seu Azure Stack borda mini R](azure-stack-edge-mini-r-manage-wifi.md).
