---
title: Notas de versão da Matriz Virtual do StorSimple Atualização 0.5 | Microsoft Docs
description: Descreve os problemas críticos em aberto e as resoluções para a Matriz Virtual do StorSimple que executa a Atualização 0.5.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/08/2017
ms.author: alkohli
ms.openlocfilehash: 385d9126d578250064659153f6f0f54eec696790
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60870665"
---
# <a name="storsimple-virtual-array-update-05-release-notes"></a>Notas de versão da Matriz Virtual do StorSimple Atualização 0.5

## <a name="overview"></a>Visão geral

As notas de versão a seguir identificam as questões críticas em aberto e os problemas resolvidos para as atualizações do Microsoft Azure StorSimple Virtual Array.

As notas de versão são continuamente atualizadas e, à medida que são descobertas questões críticas que exijam uma solução alternativa, elas são adicionadas. Antes de implantar o StorSimple Virtual Array, examine cuidadosamente as informações contidas nas notas de versão.

A Atualização 0.5 corresponde à versão de software **10.0.10290.0**.

> [!NOTE]
> As atualizações causam interrupção e reiniciam seu dispositivo. Se processos de E/S estiverem em andamento, o dispositivo sofrerá tempo de inatividade. Para obter instruções detalhadas sobre como aplicar a atualização, acesse [Instalar a Atualização 0.5](storsimple-virtual-array-install-update-05.md).


## <a name="whats-new-in-the-update-05"></a>Novidades na Atualização 0.5
A Atualização 0.5 é, basicamente, um build de correção de bug. As principais melhorias e correções de bugs são:

- **Melhorias na resiliência de backup** – essa versão traz correções que melhoram a resiliência de backup. Em versões anteriores, os backups eram repetidos apenas em determinadas exceções. Essa versão tenta todas as exceções de backup novamente e torna os backups mais resilientes.

- **Atualizações para o monitoramento de uso do armazenamento** – A partir de 30 de junho de 2017, o monitoramento de uso do armazenamento para a Série de Dispositivos Virtuais StorSimple será desativado. Isso se aplica aos gráficos de monitoramento em todas as matrizes virtuais que executam a Atualização 0.4 ou anterior. Essa atualização contém as alterações necessárias para que você possa continuar usando o monitoramento de uso do armazenamento no portal do Azure. Instale essa atualização crítica antes de 30 de junho de 2017 para continuar usando o recurso de monitoramento.


## <a name="issues-fixed-in-the-update-05"></a>Problemas resolvidos na Atualização 0.5

A tabela a seguir fornece um resumo dos problemas corrigidos nesta versão.

| Nº | Recurso | Problema |
| --- | --- | --- |
| 1 |Resiliência de backup| Em versões anteriores, os backups eram repetidos apenas em determinadas exceções. Essa versão contém uma correção para tornar os backups mais resilientes com a repetição de todas as exceções de backup.|
| 2 |Monitoramento| O monitoramento de uso do armazenamento da Série de Dispositivos Virtuais StorSimple será preterido a partir de 30 de junho de 2017. Essa ação afeta os gráficos de monitoramento do serviço Gerenciador de Dispositivos do StorSimple em execução nas Matrizes Virtuais do StorSimple (modelo 1200). Essa versão traz atualizações que permitem ao usuário continuar usando o monitoramento de uso do armazenamento em matrizes virtual após 30 de junho de 2017.|
| 3 |Servidor de arquivos| Em versões anteriores, um usuário podia copiar arquivos criptografados por engano para a matriz virtual. Essa versão contém uma correção que não permite a cópia de arquivos criptografados para a matriz virtual. Se o dispositivo tiver arquivos criptografados existentes antes da atualização, os backups continuarão falhando até que todos os arquivos criptografados sejam excluídos do sistema. |


## <a name="known-issues-in-the-update-05"></a>Problemas conhecidos na Atualização 0.5

A tabela a seguir fornece um resumo dos problemas conhecidos para a StorSimple Virtual Array e inclui os problemas observados das versões anteriores.

| Não. | Recurso | Problema | Solução alternativa/comentários |
| --- | --- | --- | --- |
| **1.** |Atualizações |Os dispositivos virtuais criados na versão de visualização não podem ser atualizados para uma versão de Disponibilidade Geral com suporte. |Esses dispositivos virtuais devem fazer failover para a versão de Disponibilidade Geral usando um fluxo de trabalho de DR (Recuperação de Desastre). |
| **2.** |Disco de dados provisionado |Após você ter provisionado um disco de dados de um determinado tamanho especificado e criado o dispositivo virtual StorSimple correspondente, você não deve expandir nem reduzir o disco de dados. A tentativa de fazer isso resulta na perda de todos os dados nas camadas locais do dispositivo. | |
| **3.** |Política de grupo |Quando um dispositivo é associado a um domínio, a aplicação de uma política de grupo pode afetar a operação do dispositivo. |Certifique-se de que a matriz virtual esteja em sua própria OU (unidade organizacional) do Active Directory e que nenhum GPO (objeto de política de grupo) seja aplicado a ela. |
| **4.** |Interface do Usuário da Web local |Se os recursos de segurança aprimorados estão habilitados no Internet Explorer (IE ESC), algumas páginas da interface do usuário da Web local, como Solução de Problemas ou Manutenção, podem não funcionar corretamente. Os botões nessas páginas também podem não funcionar. |Desligue os recursos de segurança reforçada do Internet Explorer. |
| **5.** |Interface do Usuário da Web local |Em uma máquina virtual de Hyper-V, as interfaces de rede na interface do usuário da Web são exibidas como interfaces de 10 Gbps. |Esse comportamento é um reflexo do Hyper-V. O Hyper-V sempre mostra 10 Gbps para adaptadores de rede virtual. |
| **6.** |Compartilhamentos ou volumes em camadas |Não há suporte para bloqueio de intervalo de bytes para aplicativos que funcionam com os volumes em camadas do StorSimple. Se o bloqueio de intervalo de bytes estiver habilitado, a disposição em camadas do StorSimple não funcionará. |As medidas recomendadas incluem:  <br></br>Desligar o bloqueio de intervalo de bytes em sua lógica de aplicativo.<br></br>Optar por colocar dados desse aplicativo em volumes localmente afixados em vez de volumes em camadas.<br></br>*Limitação*: ao usar volumes fixados localmente quando o bloqueio de intervalo de bytes estiver habilitado, esteja ciente de que o volume fixo local pode ficar online antes mesmo da restauração ser concluída. Nesses casos, se uma restauração está em andamento, você deve aguardar a restauração ser concluída. |
| **7.** |Compartilhamentos em camadas |Trabalhar com arquivos grandes pode resultar em uma divisão em camadas lenta. |Ao trabalhar com arquivos grandes, recomendamos que o maior arquivo seja inferior a 3% do tamanho do compartilhamento. |
| **8.** |Capacidade utilizada para compartilhamentos |Você pode ver o consumo de compartilhamento quando não houver dados no compartilhamento. Esse consumo ocorre porque a capacidade utilizada para compartilhamentos inclui metadados. | |
| **9.** |Recuperação de desastre |Você só pode executar a recuperação de desastres de um servidor de arquivos no mesmo domínio que o dispositivo de origem. Não há suporte nesta versão para a recuperação de desastres para um dispositivo de destino em outro domínio. |Isso será implementado em uma versão posterior. Para obter mais informações, acesse [Failover e recuperação de desastre da Matriz Virtual do StorSimple](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |Os dispositivos virtuais StorSimple não podem ser gerenciados por meio do Azure PowerShell nesta versão. |Todo o gerenciamento dos dispositivos virtuais deve ser feito por meio do portal do Azure e da interface do usuário da Web local. |
| **11.** |Alteração de senha |O console do dispositivo de matriz virtual aceita apenas a entrada no formato de teclado en-us. | |
| **12.** |CHAP |Não é possível remover as credenciais CHAP depois de criadas. Além disso, se você modificar as credenciais CHAP, precisará fazer com que os volumes fiquem offline e depois online novamente para que a alteração entre em vigor. |Esse problema será corrigido em uma versão futura. |
| **13.** |Servidor iSCSI |O “Armazenamento usado” exibido para um volume iSCSI pode ser diferente no serviço Gerenciador de Dispositivos do StorSimple e no host iSCSI. |O host iSCSI tem o modo de exibição do sistema de arquivos.<br></br>O dispositivo detecta os blocos atribuídos quando o volume está no tamanho máximo. |
| **14.** |Servidor de arquivos |Se um arquivo em uma pasta tiver um Fluxo de Dados Alternativo (ADS) associado a ele, o ADS não será copiado ou restaurado por meio da recuperação de desastres, clonagem e Recuperação no Nível do Item. | |
| **15.** |Servidor de arquivos |Não há suporte para links simbólicos. | |
| **16.** |Servidor de arquivos |Os arquivos protegidos pelo EFS (Encrypting File System) do Windows quando copiados ou armazenados no servidor de arquivos da Matriz Virtual StorSimple resultarão em uma configuração sem suporte.  | |

## <a name="next-step"></a>Próxima etapa
[Instale a Atualização 0.5](storsimple-virtual-array-install-update-05.md) na Matriz Virtual do StorSimple.

## <a name="references"></a>Referências
Procurando uma nota de versão mais antiga? Acesse:

* [Notas de versão da Matriz Virtual do StorSimple Atualização 0.4](storsimple-virtual-array-update-04-release-notes.md)
* [Notas de versão da atualização 0.3 da StorSimple Virtual Array](storsimple-ova-update-03-release-notes.md)
* [Notas de versão as Atualizações 0.1 e 0.2 do StorSimple Virtual Array](storsimple-ova-update-01-release-notes.md)
* [Notas de versão de disponibilidade geral do StorSimple Virtual Array](storsimple-ova-pp-release-notes.md)

