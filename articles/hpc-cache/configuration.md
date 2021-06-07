---
title: Definir configurações de cache do HPC do Azure
description: Explica como definir configurações adicionais para o cache, como MTU, configuração de DNS e NTP personalizado, e como acessar os instantâneos Express de destinos do armazenamento de BLOBs do Azure.
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/17/2021
ms.author: v-erkel
ms.openlocfilehash: 6e1e1283cb82dcb900da6473de65ef087a5cea82
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773225"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>Definir configurações adicionais de cache do HPC do Azure

A página **rede** na portal do Azure tem opções para personalizar várias configurações. A maioria dos usuários não precisa alterar essas configurações de seus valores padrão.

Este artigo também descreve como usar o recurso de instantâneo para destinos do armazenamento de BLOBs do Azure. O recurso de instantâneo não tem configurações configuráveis.

Para ver as configurações, abra a página **rede** do cache no portal do Azure.

![captura de tela da página rede no portal do Azure](media/networking-page.png)

> [!NOTE]
> Uma versão anterior desta página incluía uma configuração de comprimi raiz no nível do cache, mas essa configuração foi movida para [as políticas de acesso do cliente](access-policies.md).

<!-- >> [!TIP]
> The [Managing Azure HPC Cache video](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) shows the networking page and its settings. -->

## <a name="adjust-mtu-value"></a>Ajustar o valor de MTU
<!-- linked from troubleshoot-nas article -->

Você pode selecionar o tamanho máximo da unidade de transmissão para o cache usando o menu suspenso denominado **MTU tamanho**.

O valor padrão é 1500 bytes, mas você pode alterá-lo para 1400.

> [!NOTE]
> Se você reduzir o tamanho de MTU do cache, verifique se os clientes e os sistemas de armazenamento que se comunicam com o cache têm a mesma configuração de MTU ou um valor mais baixo.

Reduzir o valor de MTU do cache pode ajudá-lo a solucionar as restrições de tamanho do pacote no restante da rede do cache. Por exemplo, algumas VPNs não podem transmitir pacotes de 1500 bytes de tamanho completo com êxito. A redução do tamanho dos pacotes enviados pela VPN pode eliminar esse problema. No entanto, observe que uma configuração de MTU de cache inferior significa que qualquer outro componente que se comunica com o cache, incluindo clientes e sistemas de armazenamento, também deve ter uma configuração de MTU inferior para evitar problemas de comunicação.

Se você não quiser alterar as configurações de MTU em outros componentes do sistema, não deverá diminuir a configuração de MTU do cache. Há outras soluções para solucionar as restrições de tamanho do pacote VPN. Leia [ajustar restrições de tamanho de pacote VPN](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) no artigo de solução de problemas de nas para saber mais sobre como diagnosticar e resolver esse problema.

Saiba mais sobre as configurações de MTU em redes virtuais do Azure lendo o [ajuste de desempenho de TCP/IP para VMs do Azure](../virtual-network/virtual-network-tcpip-performance-tuning.md).

## <a name="customize-ntp"></a>Personalizar o NTP

O cache usa o time.microsoft.com de servidor de horário baseado no Azure por padrão. Se você quiser que o cache use um servidor NTP diferente, especifique-o na seção de **configuração de NTP** . Use um nome de domínio totalmente qualificado ou um endereço IP.

## <a name="set-a-custom-dns-configuration"></a>Definir uma configuração de DNS personalizada

> [!CAUTION]
> Não altere a configuração do DNS do cache se você não precisar. Erros de configuração podem ter consequências Dires. Se sua configuração não puder resolver os nomes de serviço do Azure, a instância de cache do HPC ficará permanentemente inacessível.

O cache HPC do Azure é configurado automaticamente para usar o sistema DNS do Azure seguro e conveniente. No entanto, algumas configurações incomuns exigem que o cache use um sistema DNS local separado em vez do sistema do Azure. A seção **configuração de DNS** da página **rede** é usada para especificar esse tipo de sistema.

Verifique com seus representantes do Azure ou consulte o serviço e suporte da Microsoft para determinar se você precisa ou não usar uma configuração de DNS de cache personalizado.

Se você configurar seu próprio sistema DNS local para o cache HPC do Azure usar, certifique-se de que a configuração possa resolver nomes de ponto de extremidade do Azure para serviços do Azure. Você deve configurar seu ambiente DNS personalizado para encaminhar determinadas solicitações de resolução de nomes para o DNS do Azure ou para outro servidor, conforme necessário.

Verifique se a configuração de DNS pode resolver esses itens com êxito antes de usá-lo para um cache do HPC do Azure:

* ``*.core.windows.net``
* Download da CRL (lista de certificados revogados) e serviços de verificação do protocolo de status de certificado online (OCSP). Uma lista parcial é fornecida no [Item regras de firewall](../security/fundamentals/tls-certificate-changes.md#will-this-change-affect-me) no final deste artigo do [Azure TLS](../security/fundamentals/tls-certificate-changes.md), mas você deve consultar um representante técnico da Microsoft para entender todos os requisitos.
* O nome de domínio totalmente qualificado do seu servidor NTP (time.microsoft.com ou um servidor personalizado)

Se você precisar definir um servidor DNS personalizado para seu cache, use os campos fornecidos:

* **Domínio de pesquisa DNS** (opcional) – Insira seu domínio de pesquisa, por exemplo, ``contoso.com`` . Um único valor é permitido ou você pode deixá-lo em branco.
* **Servidor (es) DNS** – insira até três servidores DNS. Especifique-os por endereço IP.

<!-- 
  > [!NOTE]
  > The cache will use only the first DNS server it successfully finds. -->

Considere usar um cache de teste para verificar e refinar a configuração do DNS antes de usá-lo em um ambiente de produção.

### <a name="refresh-storage-target-dns"></a>Atualizar DNS de destino de armazenamento

Se o servidor DNS atualizar endereços IP, os destinos de armazenamento NFS associados ficarão temporariamente indisponíveis. Leia como atualizar os endereços IP do sistema DNS personalizados em [Editar destinos de armazenamento](hpc-cache-edit-storage.md#update-ip-address-custom-dns-configurations-only).

## <a name="view-snapshots-for-blob-storage-targets"></a>Exibir instantâneos para destinos de armazenamento de BLOBs

O cache HPC do Azure salva automaticamente instantâneos de armazenamento para destinos do armazenamento de BLOBs do Azure. Os instantâneos fornecem um ponto de referência rápida para o conteúdo do contêiner de armazenamento de back-end.

Os instantâneos não são uma substituição para backups de dados e não incluem nenhuma informação sobre o estado dos dados armazenados em cache.

> [!NOTE]
> Esse recurso de instantâneo é diferente do recurso de instantâneo incluído no NetApp ou no software de armazenamento Isilon. Essas implementações de instantâneo liberam as alterações do cache para o sistema de armazenamento de back-end antes de tirar o instantâneo.
>
> Para a eficiência, o instantâneo do cache HPC do Azure não libera as alterações primeiro e registra apenas os dados que foram gravados no contêiner de BLOB. Esse instantâneo não representa o estado dos dados armazenados em cache, portanto, ele pode não incluir alterações recentes.

Esse recurso está disponível somente para destinos do armazenamento de BLOBs do Azure e sua configuração não pode ser alterada.

Os instantâneos são feitos a cada oito horas, em UTC 0:00, 08:00 e 16:00.

O cache HPC do Azure armazena instantâneos diários, semanais e mensais até que sejam substituídos por novos. Os limites de retenção de instantâneo são:

* Até 20 instantâneos diários
* Até 8 instantâneos semanais
* Até 3 instantâneos mensais

Acesse os instantâneos do `.snapshot` diretório na raiz do seu destino de armazenamento de BLOBs montado.
