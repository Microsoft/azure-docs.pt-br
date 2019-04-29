---
title: Notas de versão do StorSimple Virtual Array Update 1.1 | Microsoft Docs
description: Descreve problemas abertos críticos e resoluções para o StorSimple Virtual Array executando a Atualização 1.1.
services: storsimple
documentationcenter: ''
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2018
ms.author: alkohli
ms.openlocfilehash: 55103d6307614f8796c41c35d6345e1fc3aca261
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60789630"
---
# <a name="storsimple-virtual-array-update-11-release-notes"></a>Notas de versão do StorSimple Virtual Array Update 1.1

## <a name="overview"></a>Visão geral

As notas de versão a seguir identificam as questões críticas em aberto e os problemas resolvidos para as atualizações do Microsoft Azure StorSimple Virtual Array.

As notas de versão são continuamente atualizadas e, à medida que são descobertas questões críticas que exijam uma solução alternativa, elas são adicionadas. Antes de implantar o StorSimple Virtual Array, examine cuidadosamente as informações contidas nas notas de versão.

A atualização 1.1 corresponde à versão do software **10.0.10307.0**.

> [!IMPORTANT]
> - As atualizações causam interrupção e reiniciam seu dispositivo. Se processos de E/S estiverem em andamento, o dispositivo sofrerá tempo de inatividade. Para obter instruções detalhadas sobre como aplicar a atualização, vá para [Instalar Atualização 1.1](storsimple-virtual-array-install-update-11.md).
>
> - A atualização 1.1 está disponível para você por meio do portal do Azure somente se o dispositivo estiver executando a Atualização 1.0.

## <a name="whats-new-in-update-11"></a>Novidades na atualização 1.1

Esta atualização contém os seguintes aprimoramentos e correções de bugs:

 - **Falhas de backup** - foram aprimoradas aumentando a resiliência a falhas na nuvem e alto consumo de CPU.
 - **Registro em log** - Foram feitas alterações no registro em modo detalhado quando o dispositivo está na sessão Suporte.


## <a name="issues-fixed-in-update-11"></a>Problemas corrigidos na atualização 1.1

A tabela a seguir fornece um resumo dos problemas corrigidos nesta versão.

| Nº | Recurso | Problema |
| --- | --- | --- |
| 1 |Backups| Esta versão contém alterações que melhoraram as falhas de backup, aumentando a resiliência a falhas na nuvem e o alto uso da CPU.|
| 2 |Registro em log| Esta versão contém alterações no registro enquanto o dispositivo está na sessão Suporte no modo detalhado.|


## <a name="known-issues-in-update-11"></a>Problemas conhecidos na atualização 1.1

A tabela a seguir fornece um resumo dos problemas conhecidos para a StorSimple Virtual Array e inclui os problemas observados das versões anteriores.

| Não. | Recurso | Problema | Solução alternativa/comentários |
| --- | --- | --- | --- |
| **1.** |Atualizações |As matrizes virtuais criadas na versão de visualização não podem ser atualizados para uma versão de Disponibilidade Geral com suporte. |Essas matrizes virtuais devem fazer failover para a versão de Disponibilidade Geral usando um fluxo de trabalho de DR (Recuperação de Desastre). |
| **2.** |Disco de dados provisionado |Após você ter provisionado um disco de dados de um determinado tamanho especificado e criado a Matriz Virtual StorSimple correspondente, você não deve expandir nem reduzir o disco de dados. A tentativa de fazer isso resulta na perda de todos os dados nas camadas locais do dispositivo. | |
| **3.** |Política de grupo |Quando um dispositivo é associado a um domínio, a aplicação de uma política de grupo pode afetar a operação do dispositivo. |Certifique-se de que a matriz virtual esteja em sua própria OU (unidade organizacional) do Active Directory e que nenhum GPO (objeto de política de grupo) seja aplicado a ela. |
| **4.** |Interface do Usuário da Web local |Se os recursos de segurança aprimorados estão habilitados no Internet Explorer (IE ESC), algumas páginas da interface do usuário da Web local, como Solução de Problemas ou Manutenção, podem não funcionar corretamente. Os botões nessas páginas também podem não funcionar. |Desligue os recursos de segurança reforçada do Internet Explorer. |
| **5.** |Interface do Usuário da Web local |Em uma máquina virtual de Hyper-V, as interfaces de rede na interface do usuário da Web são exibidas como interfaces de 10 Gbps. |Esse comportamento é um reflexo do Hyper-V. O Hyper-V sempre mostra 10 Gbps para adaptadores de rede virtual. |
| **6.** |Compartilhamentos ou volumes em camadas |Não há suporte para bloqueio de intervalo de bytes para aplicativos que funcionam com os volumes em camadas do StorSimple. Se o bloqueio de intervalo de bytes estiver habilitado, a disposição em camadas do StorSimple não funcionará. |As medidas recomendadas incluem:  <br></br>Desligar o bloqueio de intervalo de bytes em sua lógica de aplicativo.<br></br>Optar por colocar dados desse aplicativo em volumes localmente afixados em vez de volumes em camadas.<br></br>*Limitação*: ao usar volumes fixados localmente quando o bloqueio de intervalo de bytes estiver habilitado, esteja ciente de que o volume fixo local pode ficar online antes mesmo da restauração ser concluída. Nesses casos, se uma restauração está em andamento, você deve aguardar a restauração ser concluída. |
| **7.** |Compartilhamentos em camadas |Trabalhar com arquivos grandes pode resultar em uma divisão em camadas lenta. |Ao trabalhar com arquivos grandes, recomendamos que o maior arquivo seja inferior a 3% do tamanho do compartilhamento. |
| **8.** |Capacidade utilizada para compartilhamentos |Você pode ver o consumo de compartilhamento quando não houver dados no compartilhamento. Esse consumo ocorre porque a capacidade utilizada para compartilhamentos inclui metadados. | |
| **9.** |Recuperação de desastre |Você só pode executar a recuperação de desastres de um servidor de arquivos no mesmo domínio que o dispositivo de origem. Não há suporte nesta versão para a recuperação de desastres para um dispositivo de destino em outro domínio. |Isso será implementado em uma versão posterior. Para obter mais informações, acesse [Failover e recuperação de desastre da Matriz Virtual do StorSimple](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |As Matrizes Virtuais StorSimple não podem ser gerenciadas por meio do Azure PowerShell nesta versão. |Todo o gerenciamento dos dispositivos virtuais deve ser feito por meio do portal do Azure e da interface do usuário da Web local. |
| **11.** |Alteração de senha |O console do dispositivo de matriz virtual aceita apenas a entrada no formato de teclado en-us. | |
| **12.** |CHAP |Não é possível remover as credenciais CHAP depois de criadas. Além disso, se você modificar as credenciais CHAP, precisará fazer com que os volumes fiquem offline e depois online novamente para que a alteração entre em vigor. |Esse problema será corrigido em uma versão futura. |
| **13.** |Servidor iSCSI |O “Armazenamento usado” exibido para um volume iSCSI pode ser diferente no serviço Gerenciador de Dispositivos do StorSimple e no host iSCSI. |O host iSCSI tem o modo de exibição do sistema de arquivos.<br></br>O dispositivo detecta os blocos atribuídos quando o volume está no tamanho máximo. |
| **14.** |Servidor de arquivos |Se um arquivo em uma pasta tiver um Fluxo de Dados Alternativo (ADS) associado a ele, o ADS não será copiado ou restaurado por meio da recuperação de desastres, clonagem e Recuperação no Nível do Item. | |
| **15.** |Servidor de arquivos |Não há suporte para links simbólicos. | |
| **16.** |Servidor de arquivos |Os arquivos protegidos pelo EFS (Encrypting File System) do Windows quando copiados ou armazenados no servidor de arquivos da Matriz Virtual StorSimple resultarão em uma configuração sem suporte.  | |
| **17.** |Atualizações |Se você vir o erro código: 2359302 (0x240006 hex) ao tentar instalar um hotfix por meio da interface do usuário local, em seguida, isso implica que o hotfix já está instalado em seu dispositivo.   | |
| **18.** |Atualizações |Se você usar a interface do usuário da Web local para instalar a Atualização 1 na matriz virtual, deverá garantir que está executando a Atualização 0.6. Se você estiver executando uma versão inferior à Atualização 0.6, deverá instalar a Atualização 0.6 primeiro e, em seguida, aplicar a Atualização 1. Se você instalar diretamente a Atualização 1.0 de uma versão anterior à Atualização 0.6, perderá algumas atualizações e os gráficos de monitoramento não funcionarão.   | |


## <a name="next-steps"></a>Próximas etapas
[Instale a Atualização 1.1](storsimple-virtual-array-install-update-11.md) no seu StorSimple Virtual Array.

## <a name="references"></a>Referências
Procurando uma nota de versão mais antiga? Acesse:
* [Notas de versão do Update 1.0 do Array Virtual StorSimple](storsimple-virtual-array-update-1-release-notes.md)
* [Notas de versão da atualização 0.6 da StorSimple Virtual Array](storsimple-virtual-array-update-06-release-notes.md)
* [Notas de versão da Atualização 0.5 do StorSimple Virtual Array](storsimple-virtual-array-update-05-release-notes.md)
* [Notas de versão da Matriz Virtual do StorSimple Atualização 0.4](storsimple-virtual-array-update-04-release-notes.md)
* [Notas de versão da atualização 0.3 da StorSimple Virtual Array](storsimple-ova-update-03-release-notes.md)
* [Notas de versão as Atualizações 0.1 e 0.2 do StorSimple Virtual Array](storsimple-ova-update-01-release-notes.md)
* [Notas de versão de disponibilidade geral do StorSimple Virtual Array](storsimple-ova-pp-release-notes.md)
