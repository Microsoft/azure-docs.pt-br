---
title: Requisitos de sistema do Microsoft Azure StorSimple Virtual Array | Microsoft Docs
description: Aprender sobre os requisitos de software e de rede para sua StorSimple Virtual Array
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ea1d3bca-e71b-453d-aa82-440d2638f5e3
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/11/2019
ms.author: alkohli
ms.openlocfilehash: a6bea2b5447435930cb0e1f80073a11007e80415
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60629298"
---
# <a name="storsimple-virtual-array-system-requirements"></a>Requisitos de sistema da matriz virtual StorSimple
## <a name="overview"></a>Visão geral
Este artigo descreve os requisitos de sistema importantes para o seu Microsoft Azure StorSimple Virtual Array e para os clientes de armazenamento que acessam a matriz. Recomendamos que você examine as informações com atenção antes de implantar o sistema StorSimple e consulte-as, quando necessário, durante a implantação e operação subsequente.

Os requisitos do sistema incluem:

* **Requisitos de software para clientes de armazenamento** - descreve as plataformas de virtualização, navegadores da Web, iniciadores iSCSI e clientes SMB para os quais há suporte, os requisitos mínimos de dispositivo virtual e quaisquer requisitos adicionais para esses sistemas operacionais.
* **Requisitos de rede para o dispositivo StorSimple** - fornece informações sobre as portas que precisam ser abertas no firewall para permitir o tráfego iSCSI, de nuvem ou de gerenciamento.

As informações de requisitos de sistema do StorSimple publicadas neste artigo se aplica somente a Matrizes Virtuais StorSimple.

* Para dispositivos série 8000, vá para [requisitos de sistema para o dispositivo StorSimple série 8000](storsimple-system-requirements.md).
* Para dispositivos série 7000, vá para [Requisitos de sistema para o dispositivo StorSimple série 5000-7000](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements).

## <a name="software-requirements"></a>Requisitos de software
Os requisitos de software incluem as informações sobre os navegadores da Web, versões SMB e plataformas de virtualização para os quais há suporte, além dos requisitos mínimos de dispositivo virtual.

### <a name="supported-virtualization-platforms"></a>Plataformas de virtualização com suporte
| **Hipervisor** | **Versão** |
| --- | --- |
| Hyper-V |Windows Server 2008 R2 SP1 e posterior |
| VMware ESXi |5.0, 5.5, 6.0 e 6.5. |

> [!IMPORTANT]
> Não instale as ferramentas do VMware no StorSimple Virtual Array. Isso resulta em uma configuração sem suporte.

### <a name="virtual-device-requirements"></a>Requisitos de dispositivo virtual
| **Componente** | **Requisito** |
| --- | --- |
| Número mínimo de processadores virtuais (núcleos) |4 |
| Mínimo de memória (RAM) |8 GB <br> Para um servidor de arquivos, 8 GB para menos de 2 milhões de arquivos e 16 GB para 2 a 4 milhões de arquivos|
| Espaço em disco<sup>1</sup> |Disco do sistema operacional - 80 GB  <br></br>Disco de dados - 500 GB a 8 TB |
| Número mínimo de interface(s) de rede |1 |
| Largura de banda de Internet<sup>2</sup> |Largura de banda mínima necessária: 5 Mbps <br> Largura de banda recomendada: 100 Mbps <br> A velocidade de transferência de dados é ajustada conforme a largura de banda de Internet. Por exemplo, 100 GB de dados levam dois dias para serem transferidos a 5 Mbps, o que pode levar a falhas de backup porque os backups diários não serão concluídos em um dia. Com uma largura de banda de 100 Mbps, 100 GB de dados podem ser transferido em 2,5 horas.   |

<sup>1</sup> - Com provisionamento dinâmico

<sup>2</sup> - Os requisitos de rede podem variar dependendo da taxa de alteração diária. Por exemplo, se um dispositivo precisa realizar backup de 10 GB ou mais de alterações durante um dia, o backup diário por uma conexão de 5 Mbps pode levar até 4,25 horas (caso não tenha sido possível comprimir ou eliminar a duplicação dos dados).

### <a name="supported-web-browsers"></a>Navegadores da Web com suporte
| **Componente** | **Versão** | **Requisitos/observações adicionais** |
| --- | --- | --- |
| Microsoft Edge |Última versão | |
| Internet Explorer |Última versão |Testado com o Internet Explorer 11 |
| Google Chrome |Última versão |Testado com o Chrome 46 |

### <a name="supported-storage-clients"></a>Clientes de armazenamento com suporte
Os requisitos de software a seguir referem-se aos iniciadores iSCSI que acessam o StorSimple Array Virtual (configurado como um servidor iSCSI).

| **Sistemas operacionais com suporte** | **Versão necessária** | **Requisitos/observações adicionais** |
| --- | --- | --- |
| Windows Server |2008R2 SP1, 2012, 2012R2 |O StorSimple pode criar volumes com provisionamento dinâmico e provisionamento completo. Ele não pode criar volumes com provisionamento parcial. Os volumes iSCSI do StorSimple têm suporte apenas em:  <ul><li>Volumes simples em discos básicos do Windows.</li><li>Windows NTFS para formatar um volume.</li> |

Os requisitos de software a seguir referem-se aos clientes SMB que acessam o StorSimple Array Virtual (configurado como um servidor de arquivos).

| **Versão do SMB** |
| --- |
| SMB 2.x |
| SMB 3.0 |
| SMB 3.02 |

> [!IMPORTANT]
> Não copie ou armazene arquivos protegidos pelo Windows Encrypting File System (EFS) para o servidor de arquivo StorSimple Virtual Array. Isso resultará em uma configuração sem suporte.


### <a name="supported-storage-format"></a>Formato de armazenamento com suporte
Há suporte para o armazenamento de blob de blocos do Azure. Blobs de página não têm suporte. Para obter mais informações [sobre blobs de blocos e blobs de página](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="networking-requirements"></a>Requisitos de rede
A tabela a seguir lista as portas que devem ser abertas no firewall para permitir o tráfego de gerenciamento, de nuvem, SMB ou iSCSI. Nesta tabela, *entrada* ou *de entrada* refere-se à direção a partir da qual as solicitações de cliente acessam o dispositivo. *Saída* ou *de saída* refere-se à direção na qual seu dispositivo StorSimple envia dados externamente, além da implantação: por exemplo, saída para a Internet.

| **Porta No.<sup>1</sup>** | **Entrada ou saída** | **Escopo da porta** | **Obrigatório** | **Observações** |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP) |Saída |WAN |Não  |A porta de saída é usada para acesso à Internet para recuperar atualizações. <br></br>O proxy Web de saída é configurável pelo usuário. |
| TCP 443 (HTTPS) |Saída |WAN |Sim |A porta de saída é usada para acessar dados na nuvem. <br></br>O proxy Web de saída é configurável pelo usuário. |
| UDP 53 (DNS) |Saída |WAN |Em alguns casos; consulte as observações. |Esta porta só será necessária se você estiver usando um servidor DNS baseado na Internet. <br></br> Observe que, caso esteja implantando um servidor de arquivos, recomendamos usar o servidor DNS local. |
| UDP 123 (NTP) |Saída |WAN |Em alguns casos; consulte as observações. |Esta porta é necessária apenas se você estiver usando um servidor NTP baseado na Internet.<br></br> Observe que, caso esteja implantando um servidor de arquivos, recomendamos sincronizar a hora com os controladores de domínio do Active Directory. |
| TCP 80 (HTTP) |No |LAN |Sim |Essa é a porta de entrada para a interface do usuário local no dispositivo StorSimple para gerenciamento local. <br></br> Observe que o acesso à interface do usuário local por HTTP será redirecionado automaticamente para HTTPS. |
| TCP 443 (HTTPS) |No |LAN |Sim |Essa é a porta de entrada para a interface do usuário local no dispositivo StorSimple para gerenciamento local. |
| TCP 3260 (iSCSI) |No |LAN |Não  |Esta porta é usada para acessar dados em iSCSI. |

<sup>1</sup> Nenhuma porta de entrada precisa estar aberta na Internet pública.

> [!IMPORTANT]
> Verifique se o firewall não modifica nem descriptografa nenhum tráfego SSL entre o dispositivo StorSimple e o Azure.
> 
> 

### <a name="url-patterns-for-firewall-rules"></a>Padrões de URL para regras de firewall
Os administradores de rede geralmente podem configurar regras avançadas de firewall com base nos padrões de URL para filtrar o tráfego de entrada e de saída. Sua matriz virtual e o serviço do Gerenciador de Dispositivos StorSimple dependem de outros aplicativos da Microsoft, como o Barramento de Serviço do Azure, o Controle de Acesso do Azure Active Directory, as contas de armazenamento e os servidores do Microsoft Update. Os padrões de URL associados a esses aplicativos podem ser usados para configurar regras de firewall. É importante entender que os padrões de URL associados a esses aplicativos podem ser alterados. Isso, por sua vez, exigirá que o administrador de rede monitore e atualize as regras de firewall para o StorSimple como e quando necessário. 

É recomendável que você defina suas regras de firewall para tráfego de saída, com base nos endereços IP fixos do StorSimple e, na maioria dos casos, de modo flexível. No entanto, você pode usar as informações a seguir para definir regras avançadas de firewall que são necessárias para criar ambientes seguros.

> [!NOTE]
> 
> * Os IPs do dispositivo (de origem) sempre devem estar configurados para todas as interfaces de rede habilitadas para a nuvem. 
> * Os IPs de destino devem ser definidos como [Azure datacenter IP ranges](https://www.microsoft.com/download/confirmation.aspx?id=41653)(Intervalos de IP do datacenter do Azure).
> 
> 

| Padrão de URL | Componente/funcionalidade |
| --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*` <br>`https://login.windows.net`|Serviço do Gerenciador de Dispositivos StorSimple<br>Access Control Service<br>Barramento de Serviço do Azure<br>Serviço de autenticação|
| `http://*.backup.windowsazure.com` |Registro de dispositivos |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Revogação de certificado |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Contas de armazenamento e monitoramento do Azure |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Servidores do Microsoft Update<br> |
| `http://*.deploy.akamaitechnologies.com` |CDN do Akamai |
| `https://*.partners.extranet.microsoft.com/*` |Pacote de suporte |
| `https://*.data.microsoft.com` |Serviço de telemetria no Windows, confira a [atualização para a experiência do cliente e a telemetria de diagnóstico](https://support.microsoft.com/en-us/kb/3068708) |

## <a name="next-steps"></a>Próximas etapas
* [Preparar o portal para implantar sua StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md)
