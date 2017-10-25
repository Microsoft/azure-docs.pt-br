---
title: "Notas de versão da atualização 0.4 StorSimple Virtual Array | Microsoft Docs"
description: "Descreve os problemas críticos em aberto e resoluções para a StorSimple Virtual Array que executa a Atualização 0.4."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/05/2017
ms.author: alkohli
ms.openlocfilehash: cc2b025b7f3e28954c7f95409ffab03e5cbcf13d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-virtual-array-update-04-release-notes"></a>Notas de versão da Atualização 0.4 da StorSimple Virtual Array

## <a name="overview"></a>Visão geral

As notas de versão a seguir identificam as questões críticas em aberto e os problemas resolvidos para as atualizações do Microsoft Azure StorSimple Virtual Array.

As notas de versão são continuamente atualizadas e, à medida que são descobertas questões críticas que exijam uma solução alternativa, elas são adicionadas. Antes de implantar o StorSimple Virtual Array, examine cuidadosamente as informações contidas nas notas de versão.

A Atualização 0.4 corresponde à versão de software **10.0.10289.0**.

> [!NOTE]
> As atualizações causam interrupção e reiniciam seu dispositivo. Se processos de E/S estiverem em andamento, o dispositivo sofrerá tempo de inatividade.


## <a name="whats-new-in-the-update-04"></a>Novidades na atualização 0.4
A atualização 0.4 é principalmente uma compilação de correção de bug juntamente com algumas melhorias. Nesta versão, diversos bugs resultando em falhas de backup na versão anterior foram resolvidos. As principais melhorias e correções de bugs são:

- **Melhorias do desempenho de backup** – esta versão fez várias melhorias importantes em relação ao desempenho do backup. Como resultado, os backups que envolvem um grande número de arquivos apresentam uma redução significativa no tempo de conclusão. Para backups incrementais e completos.

- **Desempenho da restauração melhorado** – esta versão contém melhorias que aumentam significativamente o desempenho de restauração ao usar um grande número de arquivos. Ao usar 2 a 4 milhões de arquivos, recomendamos que você forneça uma matriz virtual com 16 GB de RAM para ver as melhorias. Ao usar menos de 2 milhões de arquivos, o requisito mínimo para a máquina virtual continua a ser 8 GB de RAM.

- **Melhorias para o pacote de suporte** – as melhorias incluem o registro no log de estatísticas para o disco, a CPU, a memória, a rede e a nuvem no pacote de suporte que, por sua vez, melhora o processo de diagnóstico/depuração de problemas do dispositivo.

- **Limite dos volumes iSCSI fixados localmente em 200 GB** – para volumes fixos localmente, recomendamos que você limite a um volume iSCSI de 200 GB em sua matriz virtual StorSimple. A reserva de local para volumes em camadas continua a ser 10% do tamanho do volume provisionado, mas está limitada a 200 GB. 

- **Correções de bugs relacionados a backup** – nas versões anteriores do software, houve problemas relacionados a backups que podiam causar falhas de backup. Esses bugs foram resolvidos nesta versão.


## <a name="issues-fixed-in-the-update-04"></a>Problemas resolvidos na atualização 0.4

A tabela a seguir fornece um resumo dos problemas corrigidos nesta versão.

| Nº | Recurso | Problema |
| --- | --- | --- |
| 1 |Desempenho do backup|Em versões anteriores, os backups que envolviam um grande número de arquivos levavam muito tempo para concluir (na escala de dias). Nesta versão, os backups completos e incrementais tiveram uma redução significativa no tempo de conclusão. |
| 2 |Pacote de suporte|Disco, CPU, memória, rede e estatísticas de nuvem agora são registrados para os registros nos logs de suporte, melhorando a eficiência dos pacotes de suporte na solução de problemas no dispositivo.|
| 3 |Backup |Em versões anteriores, os backups de longa duração podiam resultar em uma situação de falta espaço no dispositivo, resultando em falhas de backup. Este bug foi resolvido nesta versão, permitindo no máximo 5 backups por vez em fila.|
| 4 |iSCSI | Em versões anteriores, a reserva de local para volumes em camadas ou localmente fixados era 10% do tamanho do volume provisionado. Nesta versão, a reserva de local para todos os volumes iSCSI (local fixo ou em camadas) é limitada a 10% com um máximo de 200 GB (para volumes em camadas maiores que 2 TB), liberando assim mais espaço no disco local. Recomendamos que os volumes fixados localmente nesta versão sejam limitados a 200 GB.|


## <a name="known-issues-in-the-update-04"></a>Problemas conhecidos na atualização 0.4

A tabela a seguir fornece um resumo dos problemas conhecidos para a StorSimple Virtual Array e inclui os problemas observados das versões anteriores. 

| Nº | Recurso | Problema | Solução alternativa/comentários |
| --- | --- | --- | --- |
| **1.** |Atualizações |Os dispositivos virtuais criados na versão de visualização não podem ser atualizados para uma versão de Disponibilidade Geral com suporte. |Esses dispositivos virtuais devem fazer failover para a versão de Disponibilidade Geral usando um fluxo de trabalho de DR (Recuperação de Desastre). |
| **2.** |Disco de dados provisionado |Após você ter provisionado um disco de dados de um determinado tamanho especificado e criado o dispositivo virtual StorSimple correspondente, você não deve expandir nem reduzir o disco de dados. A tentativa de fazer isso resulta na perda de todos os dados nas camadas locais do dispositivo. | |
| **3.** |Política de grupo |Quando um dispositivo é associado a um domínio, a aplicação de uma política de grupo pode afetar a operação do dispositivo. |Certifique-se de que a matriz virtual esteja em sua própria OU (unidade organizacional) do Active Directory e que nenhum GPO (objeto de política de grupo) seja aplicado a ela. |
| **4.** |Interface do Usuário da Web local |Se os recursos de segurança aprimorados estão habilitados no Internet Explorer (IE ESC), algumas páginas da interface do usuário da Web local, como Solução de Problemas ou Manutenção, podem não funcionar corretamente. Os botões nessas páginas também podem não funcionar. |Desligue os recursos de segurança reforçada do Internet Explorer. |
| **5.** |Interface do Usuário da Web local |Em uma máquina virtual de Hyper-V, as interfaces de rede na interface do usuário da Web são exibidas como interfaces de 10 Gbps. |Esse comportamento é um reflexo do Hyper-V. O Hyper-V sempre mostra 10 Gbps para adaptadores de rede virtual. |
| **6.** |Compartilhamentos ou volumes em camadas |Não há suporte para bloqueio de intervalo de bytes para aplicativos que funcionam com os volumes em camadas do StorSimple. Se o bloqueio de intervalo de bytes estiver habilitado, a disposição em camadas do StorSimple não funcionará. |As medidas recomendadas incluem:  <br></br>Desligar o bloqueio de intervalo de bytes em sua lógica de aplicativo.<br></br>Optar por colocar dados desse aplicativo em volumes localmente afixados em vez de volumes em camadas.<br></br>*Ressalva*: ao usar volumes fixados localmente e o bloqueio de intervalo de bytes estiver habilitado, esteja ciente de que o volume fixo local pode ficar online antes mesmo da restauração ser concluída. Nesses casos, se uma restauração está em andamento, você deve aguardar a restauração ser concluída. |
| **7.** |Compartilhamentos em camadas |Trabalhar com arquivos grandes pode resultar em uma divisão em camadas lenta. |Ao trabalhar com arquivos grandes, recomendamos que o maior arquivo seja inferior a 3% do tamanho do compartilhamento. |
| **8.** |Capacidade utilizada para compartilhamentos |Você pode ver o consumo de compartilhamento quando não houver dados no compartilhamento. Isso ocorre porque a capacidade utilizada para compartilhamentos inclui metadados. | |
| **9.** |Recuperação de desastre |Você só pode executar a recuperação de desastres de um servidor de arquivos no mesmo domínio que o dispositivo de origem. Não há suporte nesta versão para a recuperação de desastres para um dispositivo de destino em outro domínio. |Isso será implementado em uma versão posterior. |
| **10.** |Azure PowerShell |Os dispositivos virtuais StorSimple não podem ser gerenciados por meio do Azure PowerShell nesta versão. |Todo o gerenciamento dos dispositivos virtuais deve ser feito por meio do portal clássico do Azure e da interface do usuário da Web local. |
| **11.** |Alteração de senha |O console do dispositivo de matriz virtual só aceita a entrada no formato de teclado en-US. | |
| **12.** |CHAP |Não é possível remover as credenciais CHAP depois de criadas. Além disso, se você modificar as credenciais CHAP, precisará fazer com que os volumes fiquem offline e depois online novamente para que a alteração entre em vigor. |Esse problema será corrigido em uma versão futura. |
| **13.** |Servidor iSCSI |O 'Armazenamento usado' exibido para um volume iSCSI pode ser diferente no serviço StorSimple Manager e no host iSCSI. |O host iSCSI tem o modo de exibição do sistema de arquivos.<br></br>O dispositivo detecta os blocos atribuídos quando o volume está no tamanho máximo. |
| **14.** |Servidor de arquivos |Se um arquivo em uma pasta tiver um Fluxo de Dados Alternativo (ADS) associado a ele, o ADS não será copiado ou restaurado por meio da recuperação de desastres, clonagem e Recuperação no Nível do Item. | |
| **15.** |Servidor de arquivos |Não há suporte para links simbólicos. | |
| **16.** |Servidor de arquivos |Os arquivos protegidos pelo EFS (Encrypting File System) do Windows quando copiados ou armazenados no servidor de arquivos da Matriz Virtual StorSimple resultarão em uma configuração sem suporte.  | |

## <a name="next-step"></a>Próxima etapa
[Instale a atualização 0.4](storsimple-virtual-array-install-update-04.md) em sua StorSimple Virtual Array.

## <a name="references"></a>Referências
Procurando uma nota de versão mais antiga? Acesse: 

* [Notas de versão da atualização 0.3 da StorSimple Virtual Array](storsimple-ova-update-03-release-notes.md)
* [Notas de versão as Atualizações 0.1 e 0.2 do StorSimple Virtual Array](storsimple-ova-update-01-release-notes.md)
* [Notas de versão de disponibilidade geral do StorSimple Virtual Array](storsimple-ova-pp-release-notes.md)

