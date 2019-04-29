---
title: Requisitos do sistema do StorSimple série 8000 | Microsoft Docs
description: Descreve os requisitos e as práticas recomendadas para software, alta disponibilidade e rede para uma solução do Microsoft Azure StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: f05e3e85d36ffc23a193a6771a0271c71b2f8544
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60631896"
---
# <a name="storsimple-8000-series-software-high-availability-and-networking-requirements"></a>Requisitos de software, de alta disponibilidade e de rede do StorSimple série 8000

## <a name="overview"></a>Visão geral

Bem-vindo ao Microsoft Azure StorSimple. Este artigo descreve os requisitos do sistema importantes e as práticas recomendadas para seu dispositivo StorSimple e para os clientes de armazenamento acessarem o dispositivo. Recomendamos que você examine as informações com atenção antes de implantar o sistema StorSimple e consulte-as, quando necessário, durante a implantação e operação subsequente.

Os requisitos do sistema incluem:

* **Requisitos de software para clientes de armazenamento** - descreve os sistemas operacionais com suporte e quaisquer requisitos adicionais para esses sistemas operacionais.
* **Requisitos de rede para o dispositivo StorSimple** - fornece informações sobre as portas que precisam ser abertas no firewall para permitir o tráfego iSCSI, de nuvem ou de gerenciamento.
* **Requisitos de alta disponibilidade para o StorSimple** - descreve os requisitos de alta disponibilidade e as práticas recomendadas para o dispositivo StorSimple e o computador host.

## <a name="software-requirements-for-storage-clients"></a>Requisitos de software para clientes de armazenamento

Os requisitos de software a seguir são para os clientes de armazenamento que acessam seu dispositivo StorSimple.

| Sistemas operacionais com suporte | Versão necessária | Requisitos/observações adicionais |
| --- | --- | --- |
| Windows Server |2008 R2 SP1, 2012, 2012 R2, 2016 |Os volumes iSCSI do StorSimple  são permitidos para o uso somente nos seguintes tipos de disco do Windows:<ul><li>Volume simples em disco básico</li><li>Volume simples e espelhado em disco dinâmico</li></ul>Há suporte apenas para software iniciadores iSCSI presentes nativamente no sistema operacional. Não há suporte para iniciadores iSCSI de hardware.<br></br>O provisionamento dinâmico do Windows Server 2012 e 2016, bem como de recursos ODX terá suporte se você estiver usando um volume iSCSI do StorSimple.<br><br>O StorSimple pode criar volumes com provisionamento dinâmico e provisionamento completo. Não é possível criar volumes parcialmente provisionados.<br><br>Reformatar um volume de provisionamento dinâmico pode levar muito tempo. É recomendável excluir o volume e criar um novo em vez de reformatar. No entanto, se você ainda preferir reformatar um volume:<ul><li>Execute o comando a seguir antes de reformatar para evitar atrasos de recuperação de espaço: <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>Depois que a formatação for concluída, use o seguinte comando para reabilitar a recuperação de espaço:<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Aplique o hotfix do Windows Server 2012, conforme descrito em [KB 2878635](https://support.microsoft.com/kb/2870270), em seu computador Windows Server.</li></ul></li></ul></ul> Se você estiver configurando o Gerenciador de Instantâneos do StorSimple ou o Adaptador do StorSimple para SharePoint, vá para [Requisitos de software para os componentes opcionais](#software-requirements-for-optional-components). |
| VMware ESX |5.5 e 6.0 |Compatível com o VMware vSphere como cliente iSCSI. O recurso de bloco VAAI é compatível com o VMware vSphere nos dispositivos StorSimple. |
| Linux RHEL/CentOS |5, 6 e 7 |Suporte para os clientes Linux iSCSI com o iniciador open-iSCSI versões 5, 6 e 7. |
| Linux |SUSE Linux 11 | |

> [!NOTE]
> O IBM AIX não é suportado atualmente com o StorSimple.


## <a name="software-requirements-for-optional-components"></a>Requisitos de software para os componentes opcionais

Os seguintes requisitos de software são para os componentes opcionais do StorSimple (Gerenciador de Instantâneos do StorSimple e Adaptador do StorSimple para SharePoint).

| Componente | Plataforma de host | Requisitos/observações adicionais |
| --- | --- | --- |
| StorSimple Snapshot Manager |Windows Server 2008 R2 SP1, 2012, 2012 R2 |O uso do Gerenciador de Instantâneos do StorSimple no Windows Server é necessário para fazer backup/restauração dos discos dinâmicos espelhados e quaisquer backups consistentes com o aplicativo.<br> Há suporte para o StorSimple Snapshot Manager apenas no Windows Server 2008 R2 SP1 (64 bits), no Windows Server 2012 R2 e no Windows Server 2012.<ul><li>Se você estiver usando o Windows Server 2012, instale o .NET 3.5 – 4.5 antes de instalar o StorSimple Snapshot Manager.</li><li>Se você estiver usando o Windows Server 2008 R2 SP1, você deve instalar o Windows Management Framework 3.0 antes de instalar o StorSimple Snapshot Manager.</li></ul> |
| StorSimple Adapter for SharePoint |Windows Server 2008 R2 SP1, 2012, 2012 R2 |<ul><li>Há suporte para o adaptador StorSimple para SharePoint somente no SharePoint 2010 e no SharePoint 2013.</li><li>O RBS exige o SQL Server Enterprise Edition, versão 2008 R2 ou 2012.</li></ul> |

## <a name="networking-requirements-for-your-storsimple-device"></a>Requisitos de rede para seu dispositivo StorSimple

Seu dispositivo StorSimple é um dispositivo bloqueado. No entanto, é preciso abrir portas no firewall para permitir o tráfego de gerenciamento, de nuvem e iSCSI. A tabela a seguir lista as portas que precisam estar abertas no firewall. Nesta tabela, *entrada* ou *de entrada* refere-se à direção a partir da qual as solicitações de cliente acessam o dispositivo. *Saída* ou *de saída* refere-se à direção na qual seu dispositivo StorSimple envia dados externamente, além da implantação: por exemplo, saída para a Internet.

| Nº da porta<sup>1,2</sup> | Entrada ou saída | Escopo da porta | Obrigatório | Observações |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP)<sup>3</sup> |Saída |WAN |Não  |<ul><li>A porta de saída é usada para acesso à Internet para recuperar atualizações.</li><li>O proxy Web de saída é configurável pelo usuário.</li><li>Para permitir atualizações do sistema, esta porta também deve estar aberta para o IPs fixos do controlador.</li></ul> |
| TCP 443 (HTTPS)<sup>3</sup> |Saída |WAN |Sim |<ul><li>A porta de saída é usada para acessar dados na nuvem.</li><li>O proxy Web de saída é configurável pelo usuário.</li><li>Para permitir atualizações do sistema, esta porta também deve estar aberta para o IPs fixos do controlador.</li><li>Esta porta também é usada em ambos os controladores para coleta de lixo.</li></ul> |
| UDP 53 (DNS) |Saída |WAN |Em alguns casos; consulte as observações. |Esta porta só será necessária se você estiver usando um servidor DNS baseado na Internet. |
| UDP 123 (NTP) |Saída |WAN |Em alguns casos; consulte as observações. |Esta porta é necessária apenas se você estiver usando um servidor NTP baseado na Internet. |
| TCP 9354 |Saída |WAN |Sim |A porta de saída é usada pelo dispositivo StorSimple para se comunicar com o serviço StorSimple Device Manager. |
| 3260 (iSCSI) |No |LAN |Não  |Esta porta é usada para acessar dados em iSCSI. |
| 5985 |No |LAN |Não  |A porta de entrada é usada pelo StorSimple Snapshot Manager para se comunicar com o dispositivo do StorSimple.<br>Essa porta também é usada quando você se conecta remotamente ao Windows PowerShell para o StorSimple via HTTP. |
| 5986 |No |LAN |Não  |Esta porta é usada quando você se conecta remotamente ao Windows PowerShell para StorSimple via HTTPS. |

<sup>1</sup> Nenhuma porta de entrada precisa estar aberta na Internet pública.

<sup>2</sup> Se várias portas tiverem uma configuração de gateway, a ordem do tráfego de saída roteado será determinada com base na ordem de roteamento da porta descrita em [Roteamento de porta](#routing-metric) abaixo.

<sup>3</sup> Os IPs fixos do controlador no dispositivo StorSimple devem ser roteáveis e conseguir se conectarem à Internet diretamente ou pelo proxy Web configurado. Os endereços IP fixos são usados para atender às atualizações do dispositivo e para a coleta de lixo. Se os controladores de dispositivo não puderem se conectar à Internet através de IPs fixa, não será possível atualizar o dispositivo StorSimple e a coleta de lixo não funcionará adequadamente.

> [!IMPORTANT]
> Verifique se o firewall não modifica nem descriptografa nenhum tráfego SSL entre o dispositivo StorSimple e o Azure.


### <a name="url-patterns-for-firewall-rules"></a>Padrões de URL para regras de firewall

Os administradores de rede geralmente podem configurar regras avançadas de firewall com base nos padrões de URL para filtrar o tráfego de entrada e de saída. O dispositivo StorSimple e o serviço StorSimple Device Manager dependem de outros aplicativos da Microsoft, como Barramento de Serviço do Azure, Controle de Acesso do Azure Active Directory, contas de armazenamento e servidores do Microsoft Update. Os padrões de URL associados a esses aplicativos podem ser usados para configurar regras de firewall. É importante entender que os padrões de URL associados a esses aplicativos podem ser alterados. Isso, por sua vez, exigirá que o administrador de rede monitore e atualize as regras de firewall para o StorSimple como e quando necessário.

É recomendável que você defina suas regras de firewall para tráfego de saída, com base nos endereços IP fixos do StorSimple e, na maioria dos casos, de modo flexível. No entanto, você pode usar as informações a seguir para definir regras avançadas de firewall que são necessárias para criar ambientes seguros.

> [!NOTE]
> Os IPs do dispositivo (de origem) sempre devem ser configurados para todas as interfaces de rede habilitadas. Os IPs de destino devem ser configurados como [Intervalos de IP do datacenter do Azure](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653).


#### <a name="url-patterns-for-azure-portal"></a>Padrões de URL para o Portal do Azure

| Padrão de URL | Componente/funcionalidade | IPs de dispositivo |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`<br>`https://login.windows.net` |Serviço do Gerenciador de Dispositivos StorSimple<br>Access Control Service<br>Barramento de Serviço do Azure<br>Serviço de autenticação |Interfaces de rede habilitadas para nuvem |
| `https://*.backup.windowsazure.com` |Registro de dispositivos |Somente DATA 0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Revogação de certificado |Interfaces de rede habilitadas para nuvem |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Contas de armazenamento e monitoramento do Azure |Interfaces de rede habilitadas para nuvem |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Servidores do Microsoft Update<br> |IPs fixados pelo controlador somente |
| `http://*.deploy.akamaitechnologies.com` |CDN do Akamai |IPs fixados pelo controlador somente |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Pacote de suporte |Interfaces de rede habilitadas para nuvem |

#### <a name="url-patterns-for-azure-government-portal"></a>Padrões de URL para o portal Azure Governamental

| Padrão de URL | Componente/funcionalidade | IPs de dispositivo |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*`<br>`https://login.microsoftonline.us` |Serviço do Gerenciador de Dispositivos StorSimple<br>Access Control Service<br>Barramento de Serviço do Azure<br>Serviço de autenticação |Interfaces de rede habilitadas para nuvem |
| `https://*.backup.windowsazure.us` |Registro de dispositivos |Somente DATA 0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Revogação de certificado |Interfaces de rede habilitadas para nuvem |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Contas de armazenamento e monitoramento do Azure |Interfaces de rede habilitadas para nuvem |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Servidores do Microsoft Update<br> |IPs fixados pelo controlador somente |
| `http://*.deploy.akamaitechnologies.com` |CDN do Akamai |IPs fixados pelo controlador somente |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Pacote de suporte |Interfaces de rede habilitadas para nuvem |

### <a name="routing-metric"></a>Métrica de roteamento

Uma métrica de roteamento é associada às interfaces e ao gateway que encaminha os dados para as redes especificadas. A métrica de roteamento é usada pelo protocolo de roteamento para calcular o melhor caminho para um determinado destino, se ela detecta que existem vários caminhos para o mesmo destino. Quanto menor a métrica de roteamento, maior será a preferência.

No contexto do StorSimple, se vários gateways e interfaces de rede estiverem configurados para encaminhar o tráfego, a métrica de roteamento entrará em ação para determinar a ordem relativa em que as interfaces serão usadas. A métrica de roteamento não pode ser alterada pelo usuário. No entanto, você pode usar o cmdlet `Get-HcsRoutingTable` para imprimir a tabela de roteamento (e as métricas) em seu dispositivo do StorSimple. Mais informações sobre o cmdlet Get-HcsRoutingTable em [Troubleshooting StorSimple deployment](storsimple-troubleshoot-deployment.md)(Solucionando problemas de implantação do StorSimple).

O algoritmo da métrica de roteamento usado para a Atualização 2 e versões posteriores pode ser explicado da seguinte maneira.

* Um conjunto de valores predeterminados foi atribuído a interfaces de rede.
* Considere uma tabela de exemplo mostrada abaixo, com valores atribuídos às várias interfaces de rede quando são habilitadas ou desabilitadas para a nuvem, mas com um gateway configurado. Observe que os valores atribuídos aqui são apenas exemplos.

    | interface de rede | Habilitado para nuvem | Desabilitado para a nuvem com o gateway |
    |-----|---------------|---------------------------|
    | Dados 0  | 1            | -                        |
    | Dados 1  | 2            | 20                       |
    | Dados 2  | 3            | 30                       |
    | Dados 3  | 4            | 40                       |
    | Dados 4  | 5            | 50                       |
    | Dados 5  | 6            | 60                       |


* A ordem na qual o tráfego da nuvem será roteado pelas interfaces de rede é:
  
    *Dados 0 > Dados 1 > Dados 2 > Dados 3 > Dados 4 > Dados 5*
  
    Isso pode ser explicado pelo exemplo a seguir.
  
    Considere um dispositivo do StorSimple com duas interfaces de rede habilitadas para a nuvem, Data 0 e Data 5. Data 1 a 4 são desabilitados para a nuvem, mas têm um gateway configurado. A ordem na qual o tráfego será roteado para este dispositivo será:
  
    *Dados 0 (1) > Dados 5 (6) > Dados 1 (20) > Dados 2 (30) > Dados 3 (40) > Dados 4 (50)*
  
    *Os números entre parênteses indicam as respectivas métricas de roteamento.*
  
    Se Data 0 falhar, o tráfego de nuvem será roteado por meio de Data 5. Considerando que um gateway é configurado em todas as outras redes, se Data 0 e Data 5 falharem, o tráfego de nuvem passará por Data 1.
* Se uma interface de rede habilitada para a nuvem falhar, serão três tentativas com um atraso de 30 segundos para se conectar à interface. Se todas as tentativas falharem, o tráfego será roteado para a próxima interface habilitada para a nuvem disponível, conforme determinado pela tabela de roteamento. Se todas as interfaces de rede habilitadas para a nuvem falharem, o dispositivo falhará no outro controlador (sem reinicialização, neste caso).
* Se houver uma falha de VIP para uma interface de rede habilitada para iSCSI, haverá três tentativas com um atraso de 2 segundos. Esse comportamento permanece o mesmo em relação às versões anteriores. Se todas as interfaces de rede iSCSI falharem, ocorrerá um failover de controlador (acompanhado por uma reinicialização).
* Um alerta também será gerado no dispositivo do StorSimple quando houver uma falha de VIP. Para saber mais, acesse a [referência rápida de alerta](storsimple-8000-manage-alerts.md).
* Quanto às repetições, o iSCSI terá precedência sobre a nuvem.
  
    Considere o exemplo a seguir: Um dispositivo do StorSimple tem duas interfaces de rede habilitadas, Data 0 e Data 1. Data 0 é habilitado para a nuvem, enquanto Data 1 é habilitado para a nuvem e para iSCSI. Nenhuma outra interface de rede neste dispositivo é habilitada para a nuvem ou para iSCSI.
  
    Se Data 1 falhar, por ser a última interface de rede do iSCSI, isso resultará em um failover do controlador para Data 1 no outro controlador.

### <a name="networking-best-practices"></a>Práticas recomendadas de rede

Além dos requisitos de rede acima, para obter o desempenho ideal de sua solução StorSimple, atenda às seguintes práticas recomendadas:

* Verifique se o dispositivo StorSimple tem uma largura de banda dedicada de 40 Mbps (ou mais) disponível sempre. Essa largura de banda não deve ser compartilhada (ou a alocação deve ser garantida pelo uso de políticas de QoS) com outros aplicativos.
* Verifique a conectividade de rede com a Internet está disponível sempre. Conexões de Internet esporádicas ou não confiáveis com os dispositivos, sem incluir qualquer conectividade com a Internet, resultarão em uma configuração sem suporte.
* Isole o tráfego iSCSI e o tráfego de nuvem com interfaces de rede dedicadas em seu dispositivo para acesso iSCSI e à nuvem. Para obter mais informações, veja como [modificar as interfaces de rede](storsimple-8000-modify-device-config.md#modify-network-interfaces) em seu dispositivo StorSimple.
* Não use uma configuração do LACP (Protocolo de controle de agregação de links) para as suas interfaces de rede. Essa é uma configuração sem suporte.

## <a name="high-availability-requirements-for-storsimple"></a>Requisitos de alta disponibilidade para o StorSimple

A plataforma de hardware incluída com a solução StorSimple possui os recursos de disponibilidade e confiabilidade que fornecem uma base para uma infraestrutura de armazenamento altamente disponível e tolerante a falhas em seu datacenter. No entanto, há requisitos e práticas recomendadas que você deve seguir para ajudar a garantir a disponibilidade de sua solução do StorSimple. Antes de implantar o StorSimple, examine cuidadosamente os requisitos e práticas recomendadas a seguir para o dispositivo do StorSimple e os computadores host conectados.

Para obter mais informações sobre como monitorar e manter os componentes de hardware do dispositivo StorSimple, acesse [Usar o serviço StorSimple Device Manager para monitorar componentes de hardware e o status](storsimple-8000-monitor-hardware-status.md) e [Substituição de componentes de hardware do StorSimple](storsimple-8000-hardware-component-replacement.md).

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>Requisitos de alta disponibilidade e procedimentos para seu dispositivo StorSimple

Examine as seguintes informações com atenção para garantir a alta disponibilidade do seu dispositivo StorSimple.

#### <a name="pcms"></a>PCMs

Os dispositivos StorSimple incluem módulos redundantes, intercambiáveis e de refrigeração (PCMs). Cada PCM tem capacidade suficiente para atender ao chassi inteiro. Para garantir a alta disponibilidade, os dois PCMs devem estar instalados.

* Conecte seus PCMs a fontes de alimentação diferentes a fim de fornecer disponibilidade de uma fonte de alimentação falhar.
* Se um PCM falhar, solicite uma substituição imediatamente.
* Remova um PCM com falha somente quando tiver a reposição e estiver pronto para instalá-lo.
* Não remova os dois PCMs simultaneamente. O módulo PCM inclui o módulo de bateria de backup. Remover os dois PCMs resultará em um desligamento sem proteção da bateria e o estado do dispositivo não será salvo. Para obter mais informações sobre a bateria, vá para [Manutenção do módulo de bateria de backup](storsimple-8000-battery-replacement.md#maintain-the-backup-battery-module).

#### <a name="controller-modules"></a>Módulos do controlador

Os dispositivos StorSimple incluem módulos de controlador redundantes e intercambiáveis. Os módulos do controlador operam de modo ativo/passivo. A qualquer momento, um módulo do controlador está ativo e fornecendo o serviço, enquanto o outro módulo do controlador está passivo. O módulo do controlador passivo está ligado e se tornará operacional se o módulo do controlador ativo falhar ou for removido. Cada módulo do controlador tem capacidade suficiente para atender ao chassi inteiro. Os dois módulos do controlador devem ser instalados para garantir a alta disponibilidade.

* Certifique-se de que os dois módulos do controlador estejam sempre instalados.
* Se um módulo do controlador falhar, solicite uma substituição imediatamente.
* Remova um módulo de controlador com falha somente quando tiver a reposição e estiver pronto para instalá-lo. A remoção de um módulo para períodos prolongados afetará o fluxo de ar e, portanto, o resfriamento do sistema.
* Certifique-se de que as conexões de rede para ambos os módulos de controlador sejam idênticas, e as interfaces da rede conectada tenham uma configuração de rede idêntica.
* Se um módulo de controlador falhar ou precisar de substituição, certifique-se de que o outro módulo de controlador esteja em um estado ativo antes de substituir o módulo de controlador com falha. Para verificar se um controlador está ativo, vá para [Identificar o controlador ativo em seu dispositivo](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Não remova os dois módulos de controlador ao mesmo tempo. Se estiver ocorrendo um failover do controlador, não desligue o módulo do controlador em espera ou o remova do chassi.
* Após o failover do controlador, aguarde pelo menos cinco minutos antes de remover um dos módulos de controlador.

#### <a name="network-interfaces"></a>Interfaces de rede

Cada módulo de controlador do dispositivo StorSimple tem quatro interfaces de rede Ethernet de 1 Gigabit e duas de 10 Gigabits.

* Certifique-se de que as conexões de rede para os dois módulos de controlador sejam idênticas, e que as interfaces de rede as quais as interfaces do módulo de controlador estão conectadas tenham uma configuração de rede idêntica.
* Quando possível, implante as conexões de rede entre opções diferentes para garantir a disponibilidade do serviço no caso de falhas do dispositivo de rede.
* Ao desconectar a única, ou a última, interface habilitada para iSCSI (com IPs atribuídos), desabilite primeiro a interface e, em seguida, desconecte os cabos. Se a interface for desconectada primeiro, o controlador ativo passará por failover para o controlador passivo. Se as interfaces correspondentes do controlador passivo também forem desconectadas, os dois controladores serão reiniciados várias vezes antes da definição de um controlador.
* Conecte pelo menos duas interfaces DATA com a rede de cada controlador de módulo.
* Se você habilitou as duas interfaces de 10 GbE, implante-as em opções diferentes.
* Quando possível, use o MPIO em servidores para garantir que os servidores possam tolerar um link, rede ou falha de interface.

Para saber mais sobre como colocar o dispositivo em rede para proporcionar alta disponibilidade e desempenho, acesse [Instalar o dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) ou [Instalar o dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

#### <a name="ssds-and-hdds"></a>SSDs e HDDs

Dispositivos StorSimple incluem discos de estado sólido (SSDs) e unidades de disco rígido (HDDs) protegidos com espaços espelhados. O uso de espaços espelhados garante que o dispositivo seja capaz de tolerar a falha de um ou mais SSDs ou HDDs.

* Certifique-se de que todos os módulos SSD e HDD estejam instalados.
* Se um SSD ou HDD falhar, solicite uma substituição imediatamente.
* Se um SSD ou HDD falhar ou exigir substituição, remova somente o SSD ou HDD que exige a substituição.
* Não remova mais de um SSD ou HDD do sistema a qualquer momento.
  Uma falha de dois ou mais discos de determinado tipo (HDD, SSD) ou uma falha consecutiva em um curto período pode resultar em mau funcionamento do sistema e possível perda de dados. Se isso ocorrer, [entre em contato com o Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md) para obter assistência.
* Durante a substituição, monitore os **Componentes compartilhados** na folha **Integridade de hardware** das unidades nos SSDs e HDDs. Um status de marca de verificação verde indica que os discos estão íntegros ou OK, enquanto que um ponto de exclamação vermelho indica um SSD ou HDD com falha.
* Recomendamos a configuração de instantâneos em nuvem para todos os volumes que você precisa proteger em caso de falha do sistema.

#### <a name="ebod-enclosure"></a>Compartimento EBOD

O modelo do dispositivo StorSimple 8600 inclui um compartimento EBOD (Extended Bunch of Disks) além do compartimento primário. Um EBOD contém controladores EBOD e HDDs (unidades de disco rígido) protegidos por espaços espelhados. O uso de espaços espelhados garante que o dispositivo seja capaz de tolerar a falha de um ou mais HDDs. O compartimento EBOD está conectado ao compartimento primário por meio de cabos SAS redundantes.

* Verifique se ambos os módulos do controlador de compartimento EBOD, os cabos SAS e todos os discos rígidos estão sempre instalados.
* Se um módulo do controlador de compartimento EBOD falhar, solicite uma substituição imediatamente.
* Se um módulo do controlador de compartimento EBOD falhar, certifique-se de que o outro módulo do controlador esteja ativo antes de substituir o módulo com falha. Para verificar se um controlador está ativo, vá para [Identificar o controlador ativo em seu dispositivo](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Durante uma substituição do módulo do controlador EBOD, monitore continuamente o status do componente no serviço StorSimple Device Manager acessando **Monitorar** > **Integridade de hardware**.
* Se um cabo SAS falha ou exigir substituição (o Suporte da Microsoft deve participar dessa decisão), remova apenas o cabo SAS que exige a substituição.
* Não remova simultaneamente os dois cabos SAS do sistema em nenhum momento.

### <a name="high-availability-recommendations-for-your-host-computers"></a>Recomendações de alta disponibilidade para os computadores de host

Leia com atenção essas práticas recomendadas para garantir a alta disponibilidade dos hosts conectados ao dispositivo StorSimple.

* Defina o StorSimple com as [configurações de cluster de servidores de arquivos com dois nós][1]. Ao remover os pontos individuais de falha e criando redundância no lado do host, a solução inteira se torna altamente disponível.
* Use compartilhamentos CA (disponíveis continuamente) disponíveis no Windows Server 2012 (SMB 3.0) para alta disponibilidade durante o failover dos controladores de armazenamento. Para obter informações adicionais sobre a configuração de clusters de servidor de arquivos e compartilhamentos Disponíveis Continuamente com o Windows Server 2012, consulte esta [demonstração em vídeo](https://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares).

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre os limites do sistema StorSimple](storsimple-8000-limits.md).
* [Saiba como implantar sua solução StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

<!--Reference links-->
[1]: https://technet.microsoft.com/library/cc731844(v=WS.10).aspx
