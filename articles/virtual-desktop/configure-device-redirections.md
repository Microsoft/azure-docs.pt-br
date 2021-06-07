---
title: Configurar redirecionamentos de dispositivo – Azure
description: Como configurar redirecionamentos de dispositivo para a área de trabalho virtual do Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 09/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b60a6e53e8fecd71885204690231776ff69fc08f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95018368"
---
# <a name="configure-device-redirections"></a>Configurar redirecionamentos de dispositivo

Configurar redirecionamentos de dispositivo para seu ambiente de área de trabalho virtual do Windows permite que você use impressoras, dispositivos USB, microfones e outros dispositivos periféricos na sessão remota. Alguns redirecionamentos de dispositivo exigem alterações nas propriedades protocolo RDP (RDP) e Política de Grupo configurações.

## <a name="supported-device-redirections"></a>Redirecionamentos de dispositivo com suporte

Cada cliente dá suporte a diferentes redirecionamentos de dispositivo. Confira [comparar os clientes](/windows-server/remote/remote-desktop-services/clients/remote-desktop-app-compare) para obter a lista completa de redirecionamentos de dispositivo com suporte para cada cliente.

## <a name="customizing-rdp-properties-for-a-host-pool"></a>Personalizando Propriedades RDP para um pool de hosts

Para saber mais sobre como personalizar as propriedades de RDP para um pool de hosts usando o PowerShell ou o portal do Azure, confira [Propriedades de RDP](customize-rdp-properties.md). Para obter a lista completa de propriedades RDP com suporte, consulte [configurações de arquivo RDP com suporte](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext).

## <a name="setup-device-redirections"></a>Configurar redirecionamentos de dispositivo

Você pode usar as seguintes propriedades de RDP e configurações de Política de Grupo para configurar redirecionamentos de dispositivo.

### <a name="audio-input-microphone-redirection"></a>Redirecionamento de entrada de áudio (microfone)

Defina a seguinte Propriedade RDP para configurar o redirecionamento de entrada de áudio:

- `audiocapturemode:i:1` habilita o redirecionamento de entrada de áudio.
- `audiocapturemode:i:0` desabilita o redirecionamento de entrada de áudio.

### <a name="audio-output-speaker-redirection"></a>Redirecionamento de saída de áudio (alto-falante)

Defina a seguinte Propriedade RDP para configurar o redirecionamento de saída de áudio:

- `audiomode:i:0` habilita o redirecionamento de saída de áudio.
- `audiomode:i:1` ou `audiomode:i:2` desabilite o redirecionamento de saída de áudio.

### <a name="camera-redirection"></a>Redirecionamento de câmera

Defina a seguinte Propriedade RDP para configurar o redirecionamento de câmera:

- `camerastoredirect:s:*` redireciona todas as câmeras.
- `camerastoredirect:s:` desabilita o redirecionamento de câmera.

>[!NOTE]
>Mesmo que a `camerastoredirect:s:` Propriedade esteja desabilitada, as câmeras locais podem ser redirecionadas por meio da `devicestoredirect:s:` propriedade. Para desabilitar completamente o conjunto de redirecionamento `camerastoredirect:s:` de câmera e definir `devicestoredirect:s:` ou definir algum subconjunto de dispositivos plug and Play que não incluam nenhuma câmera.

Você também pode redirecionar câmeras específicas usando uma lista delimitada por ponto-e-vírgula de interfaces de KSCATEGORY_VIDEO_CAMERA, como `camerastoredirect:s:\?\usb#vid_0bda&pid_58b0&mi` . 

### <a name="clipboard-redirection"></a>Redirecionamento de área de transferência

Defina a seguinte Propriedade RDP para configurar o redirecionamento de área de transferência:

- `redirectclipboard:i:1` habilita o redirecionamento da área de transferência.
- `redirectclipboard:i:0` desabilita o redirecionamento da área de transferência.

### <a name="com-port-redirections"></a>Redirecionamentos de porta COM

Defina a seguinte Propriedade RDP para configurar o redirecionamento de porta COM:

- `redirectcomports:i:1` habilita o redirecionamento de porta COM.
- `redirectcomports:i:0` desabilita o redirecionamento de porta COM.

### <a name="usb-redirection"></a>Redirecionamento de USB

Primeiro, defina a seguinte Propriedade RDP para habilitar o redirecionamento de dispositivo USB:

- `usbdevicestoredirect:s:*` habilita o redirecionamento de dispositivo USB.
- `usbdevicestoredirect:s:` desabilita o redirecionamento de dispositivo USB.

Em segundo lugar, defina o seguinte Política de Grupo no dispositivo local do usuário:

- Navegue até **configuração do computador**  >  **políticas** >  **modelos administrativos**  >  **componentes do Windows**  >  **serviços de área de trabalho remota**  >  **conexão de área de trabalho remota**  >  **redirecionamento de dispositivo USB RemoteFX** do cliente.
- Selecione **permite o redirecionamento de RDP de outros dispositivos USB RemoteFX com suporte deste computador**.
- Selecione a opção **habilitado** e, em seguida, selecione os **Administradores e usuários na caixa direitos de acesso de redirecionamento de USB do RemoteFX** .
- Selecione **OK**.

### <a name="plug-and-play-device-redirection"></a>Redirecionamento de dispositivo Plug and Play

Defina a seguinte Propriedade RDP para configurar o redirecionamento de dispositivo Plug and Play:

- `devicestoredirect:s:*` habilita o redirecionamento de todos os dispositivos plug and Play.
- `devicestoredirect:s:` desabilita o redirecionamento de dispositivos plug and Play.

Você também pode selecionar dispositivos plug and Play específicos usando uma lista delimitada por ponto-e-vírgula, como `devicestoredirect:s:root\*PNP0F08` .

### <a name="local-drive-redirection"></a>Redirecionamento de unidade local

Defina a seguinte Propriedade RDP para configurar o redirecionamento de unidade local:

- `drivestoredirect:s:*` habilita o redirecionamento de todas as unidades de disco.
- `drivestoredirect:s:` desabilita o redirecionamento de unidade local.

Você também pode selecionar unidades específicas usando uma lista delimitada por ponto-e-vírgula, como `drivestoredirect:s:C:;E:;` .

### <a name="printer-redirection"></a>Redirecionamento de impressora

Defina a seguinte Propriedade RDP para configurar o redirecionamento de impressora:

- `redirectprinters:i:1` habilita o redirecionamento de impressora.
- `redirectprinters:i:0` desabilita o redirecionamento de impressora.

### <a name="smart-card-redirection"></a>Redirecionamento de cartão inteligente

Defina a seguinte Propriedade RDP para configurar o redirecionamento de cartão inteligente:

- `redirectsmartcards:i:1` habilita o redirecionamento de cartão inteligente.
- `redirectsmartcards:i:0` desabilita o redirecionamento de cartão inteligente.