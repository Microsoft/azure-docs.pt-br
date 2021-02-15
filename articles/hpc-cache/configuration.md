---
title: Definir configurações de cache do HPC do Azure
description: Explica como definir configurações adicionais para o cache, como MTU e não-raiz-comprimir, e como acessar os instantâneos Express de destinos do armazenamento de BLOBs do Azure.
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 12/21/2020
ms.author: v-erkel
ms.openlocfilehash: 02bf862cdc3b20ef3e5fdb024f474267efa0c70d
ms.sourcegitcommit: 6cca6698e98e61c1eea2afea681442bd306487a4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/24/2020
ms.locfileid: "97760496"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>Definir configurações adicionais de cache do HPC do Azure

A página de **configuração** no portal do Azure tem opções para personalizar várias configurações. A maioria dos usuários não precisa alterar essas configurações de seus valores padrão.

Este artigo também descreve como usar o recurso de instantâneo para destinos do armazenamento de BLOBs do Azure. O recurso de instantâneo não tem configurações configuráveis.

Para ver as configurações, abra a página de **configuração** do cache no portal do Azure.

![captura de tela da página de configuração no portal do Azure](media/configuration.png)

> [!TIP]
> O [vídeo Gerenciando o cache HPC do Azure](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) mostra a página de configuração e suas configurações.

## <a name="adjust-mtu-value"></a>Ajustar o valor de MTU
<!-- linked from troubleshoot-nas article -->

Você pode selecionar o tamanho máximo da unidade de transmissão para o cache usando o menu suspenso denominado **MTU tamanho**.

O valor padrão é 1500 bytes, mas você pode alterá-lo para 1400.

> [!NOTE]
> Se você reduzir o tamanho de MTU do cache, verifique se os clientes e os sistemas de armazenamento que se comunicam com o cache têm a mesma configuração de MTU ou um valor mais baixo.

Reduzir o valor de MTU do cache pode ajudá-lo a solucionar as restrições de tamanho do pacote no restante da rede do cache. Por exemplo, algumas VPNs não podem transmitir pacotes de 1500 bytes de tamanho completo com êxito. A redução do tamanho dos pacotes enviados pela VPN pode eliminar esse problema. No entanto, observe que uma configuração de MTU de cache inferior significa que qualquer outro componente que se comunica com o cache, incluindo clientes e sistemas de armazenamento, também deve ter uma configuração de MTU inferior para evitar problemas de comunicação.

Se você não quiser alterar as configurações de MTU em outros componentes do sistema, não deverá diminuir a configuração de MTU do cache. Há outras soluções para solucionar as restrições de tamanho do pacote VPN. Leia [ajustar restrições de tamanho de pacote VPN](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) no artigo de solução de problemas de nas para saber mais sobre como diagnosticar e resolver esse problema.

Saiba mais sobre as configurações de MTU em redes virtuais do Azure lendo o [ajuste de desempenho de TCP/IP para VMs do Azure](../virtual-network/virtual-network-tcpip-performance-tuning.md).

## <a name="configure-root-squash"></a>Configurar o comprimir raiz
<!-- linked from troubleshoot and from access policies -->

A configuração **habilitar a raiz de comprimir** controla como o cache HPC do Azure trata as solicitações do usuário raiz em computadores cliente.

Quando o comprimir raiz está habilitado, os usuários raiz de um cliente são mapeados automaticamente para o usuário "ninguém" quando enviam solicitações por meio do cache do HPC do Azure. Ele também impede solicitações de cliente de usar bits de permissão set-UID.

Se o comprimir raiz estiver desabilitado, uma solicitação do usuário raiz do cliente (UID 0) será passada para um sistema de armazenamento NFS de back-end como raiz. Essa configuração pode permitir o acesso impróprio a arquivos.

A configuração de comprimir raiz no cache pode ajudar a compensar a ``no_root_squash`` configuração necessária em sistemas nas que são usados como destinos de armazenamento. (Leia mais sobre os [pré-requisitos de destino de armazenamento NFS](hpc-cache-prerequisites.md#nfs-storage-requirements).) Ele também pode melhorar a segurança quando usado com destinos do armazenamento de BLOBs do Azure.

A configuração padrão é **Sim**. (Os caches criados antes de abril de 2020 podem ter a configuração padrão **não**.)

> [!TIP]
> Você também pode definir o comprimir raiz para exportações de armazenamento específicas Personalizando as [políticas de acesso do cliente](access-policies.md#root-squash).

## <a name="view-snapshots-for-blob-storage-targets"></a>Exibir instantâneos para destinos de armazenamento de BLOBs

O cache HPC do Azure salva automaticamente instantâneos de armazenamento para destinos do armazenamento de BLOBs do Azure. Os instantâneos fornecem um ponto de referência rápida para o conteúdo do contêiner de armazenamento de back-end.

Os instantâneos não são uma substituição para backups de dados e não incluem nenhuma informação sobre o estado dos dados armazenados em cache.

> [!NOTE]
> Esse recurso de instantâneo é diferente do recurso de instantâneo incluído no NetApp ou no software de armazenamento Isilon. Essas implementações de instantâneo liberam as alterações do cache para o sistema de armazenamento de back-end antes de tirar o instantâneo.
>
> Para a eficiência, o instantâneo do cache HPC do Azure não libera as alterações primeiro e registra apenas os dados que foram gravados no contêiner de BLOB. Esse instantâneo não representa o estado dos dados armazenados em cache, portanto, ele pode não incluir alterações recentes.

Esse recurso está disponível somente para destinos do armazenamento de BLOBs do Azure e sua configuração não pode ser alterada.

Os instantâneos são feitos a cada oito horas, em UTC 0:00, 08:00 e 16:00.

O cache HPC do Azure armazena instantâneos diários, semanais e mensais até que sejam substituídos por novos. Os limites são:

* até 20 instantâneos diários
* até 8 instantâneos semanais
* até 3 instantâneos mensais

Acesse os instantâneos do `.snapshot` diretório no namespace do destino do armazenamento de BLOBs.
