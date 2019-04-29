---
title: Notas de versão das Atualizações do StorSimple Virtual Array | Microsoft Docs
description: Descreve os problemas em aberto críticos e resoluções para o StorSimple Virtual Array que executa as Atualizações 0.1 e 0.2.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 3993864d-2ddd-4302-a2f1-8d737fba6eab
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/16/2016
ms.author: alkohli
ms.openlocfilehash: aad60024187ca180c002f119f4b975e8f69796e5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60629281"
---
# <a name="storsimple-virtual-array-update-02-and-01-release-notes"></a>Notas de versão as Atualizações 0.1 e 0.2 do StorSimple Virtual Array
## <a name="overview"></a>Visão geral
As notas de versão a seguir identificam as questões críticas em aberto e os problemas resolvidos para as atualizações do Microsoft Azure StorSimple Virtual Array. (A Microsoft Azure StorSimple Virtual Array também é conhecida como dispositivo virtual local StorSimple ou dispositivo virtual StorSimple.) 

As notas de versão são continuamente atualizadas e, à medida que são descobertas questões críticas que exijam uma solução alternativa, elas são adicionadas. Examine cuidadosamente as informações contidas nas notas de versão antes de implantar seu dispositivo virtual StorSimple.

A Atualização 0.2 corresponde à versão de software **10.0.10280.0** e a Atualização 0.1 corresponde à versão **10.0.10279.0**. As seções a seguir listam as alterações em cada atualização. 

> [!NOTE]
> As atualizações causam interrupção e reiniciarão seu dispositivo. Se processos de E/S estiverem em andamento, o dispositivo sofrerá tempo de inatividade.
> 
> 

## <a name="issues-fixed-in-the-update-02"></a>Problemas resolvidos na Atualização 0.2
A Atualização 0.2 inclui todas as alterações da Atualização 0.1, além da correção descrita na tabela a seguir:

| Recurso | Problema |
| --- | --- |
| Atualizações |Na última versão, as atualizações não eram detectadas automaticamente no portal clássico do Azure, de modo que era necessário usar a IU da Web local para instalar atualizações. Esse problema foi corrigido nesta versão. Depois de instalar a Atualização 0.2, você pode instalar atualizações futuras usando o portal clássico do Azure. |

## <a name="whats-new-in-the-update-01"></a>Novidades na Atualização 0.1
A Atualização 0.1 contém as seguintes correções de bug e aprimoramentos. 

* **Melhor resiliência a interrupções de nuvem**: Esta versão tem várias correções de bugs em torno de recuperação de desastres, backup, restauração e camadas no caso de uma interrupção de conectividade de nuvem. 
* **Melhor desempenho de restauração**: Esta versão tem correções de bug que reduziram-se significativamente o tempo de conclusão dos trabalhos de restauração.
* **Automatizada de otimização de reclamação de espaço**: Quando dados são excluídos em volumes com provisionamento dinâmico, os blocos de armazenamento não utilizados deverão ser recuperados. Esta versão aprimorou o processo de recuperação de espaço da nuvem, o que resultou na disponibilização mais rápida do espaço não utilizado, em comparação com versões anteriores.
* **Novas imagens de disco virtual**: Novo VHD, VHDX e VMDK agora estão disponíveis por meio do portal clássico do Azure. Você pode baixar essas imagens para provisionar novos dispositivos da Atualização 0.1.
* **Melhorar a precisão de status de trabalhos no portal do**: Na versão anterior do software, os relatórios no portal do status do trabalho não eram granulares. Esse problema foi corrigido nesta versão.
* **Experiência de ingresso de domínio**: Correções de bugs relacionadas ao domínio e renomeação do dispositivo.

## <a name="issues-fixed-in-the-update-01"></a>Problemas resolvidos na Atualização 0.1
A tabela a seguir fornece um resumo dos problemas corrigidos nesta versão.

| Nº | Recurso | Problema |
| --- | --- | --- |
| 1 |VMDK |Em algumas versões do VMware, o disco do sistema operacional era visto como esparso, causando alertas e interrompendo as operações normais. Isso foi corrigido nesta versão. |
| 2 |Servidor iSCSI |Na última versão, o usuário foi solicitado a especificar um gateway para cada interface de rede habilitada do seu dispositivo virtual StorSimple. Esse comportamento mudou nesta versão para que o usuário tenha que configurar pelo menos um gateway para todas as interfaces de rede habilitadas. |
| 3 |Pacote de suporte |Na versão anterior do software, a coleção de pacotes de suporte falhou quando os tamanhos dos pacote foram maiores que 1 GB. Esse problema foi corrigido nesta versão. |
| 4 |Acesso à nuvem |Na última versão, se a StorSimple Virtual Array não tivesse conectividade de rede e fosse reiniciada, a interface do usuário local teria problemas de conectividade. Esse problema foi corrigido nesta versão. |
| 5 |Gráficos de monitoramento |Na versão anterior, após um failover de dispositivo, os gráficos de utilização de capacidade da nuvem exibiam valores incorretos no portal clássico do Azure. Isso foi corrigido na versão atual. |

## <a name="known-issues-in-the-update-01"></a>Problemas conhecidos na Atualização 0.1
A tabela a seguir fornece um resumo dos problemas conhecidos para a StorSimple Virtual Array e inclui os problemas observados das versões anteriores. **Os problemas observados nesta versão estão marcados com um asterisco. Quase todos os problemas nesta lista foram herdados da versão GA do StorSimple Virtual Array.**

| Não. | Recurso | Problema | Solução alternativa/comentários |
| --- | --- | --- | --- |
| **1.** |Atualizações |Os dispositivos virtuais criados na versão de visualização não podem ser atualizados para uma versão de Disponibilidade Geral com suporte. |Esses dispositivos virtuais devem fazer failover para a versão de Disponibilidade Geral usando um fluxo de trabalho de DR (Recuperação de Desastre). |
| **2.** |Disco de dados provisionado |Após você ter provisionado um disco de dados de um determinado tamanho especificado e criado o dispositivo virtual StorSimple correspondente, você não deve expandir nem reduzir o disco de dados. A tentativa de fazer isso resultará na perda de todos os dados nas camadas locais do dispositivo. | |
| **3.** |Política de grupo |Quando um dispositivo é associado a um domínio, a aplicação de uma política de grupo pode afetar a operação do dispositivo. |Certifique-se de que a matriz virtual esteja em sua própria OU (unidade organizacional) do Active Directory e que nenhum GPO (objeto de política de grupo) seja aplicado a ela. |
| **4.** |Interface do Usuário da Web local |Se os recursos de segurança aprimorados estão habilitados no Internet Explorer (IE ESC), algumas páginas da interface do usuário da Web local, como Solução de Problemas ou Manutenção, podem não funcionar corretamente. Os botões nessas páginas também podem não funcionar. |Desligue os recursos de segurança reforçada do Internet Explorer. |
| **5.** |Interface do Usuário da Web local |Em uma máquina virtual de Hyper-V, as interfaces de rede na interface do usuário da Web são exibidas como interfaces de 10 Gbps. |Esse comportamento é um reflexo do Hyper-V. O Hyper-V sempre mostra 10 Gbps para adaptadores de rede virtual. |
| **6.** |Compartilhamentos ou volumes em camadas |Não há suporte para bloqueio de intervalo de bytes para aplicativos que funcionam com os volumes em camadas do StorSimple. Se o bloqueio de intervalo de bytes estiver habilitado, a disposição em camadas do StorSimple não funcionará. |As medidas recomendadas incluem:  <br></br>Desligar o bloqueio de intervalo de bytes em sua lógica de aplicativo.<br></br>Optar por colocar dados desse aplicativo em volumes localmente afixados em vez de volumes em camadas.<br></br>*Limitação*: Se usar volumes fixados localmente e bloqueio de intervalo de bytes estiver habilitado, esteja ciente de que o volume localmente afixado pode ficar online antes mesmo da restauração ser concluída. Nesses casos, se uma restauração está em andamento, você deve aguardar a restauração ser concluída. |
| **7.** |Compartilhamentos em camadas |Trabalhar com arquivos grandes pode resultar em uma divisão em camadas lenta. |Ao trabalhar com arquivos grandes, recomendamos que o maior arquivo seja inferior a 3% do tamanho do compartilhamento. |
| **8.** |Capacidade utilizada para compartilhamentos |Você pode ver o consumo de compartilhamento na ausência de quaisquer dados no compartilhamento. Isso ocorre porque a capacidade utilizada para compartilhamentos inclui metadados. | |
| **9.** |Recuperação de desastre |Você só pode executar a recuperação de desastres de um servidor de arquivos no mesmo domínio que o dispositivo de origem. Não há suporte nesta versão para a recuperação de desastres para um dispositivo de destino em outro domínio. |Isso será implementado em uma versão posterior. |
| **10.** |Azure PowerShell |Os dispositivos virtuais StorSimple não podem ser gerenciados por meio do Azure PowerShell nesta versão. |Todo o gerenciamento dos dispositivos virtuais deve ser feito por meio do portal clássico do Azure e da interface do usuário da Web local. |
| **11.** |Alteração de senha |O console do dispositivo de matriz virtual só aceita a entrada no formato de teclado en-US. | |
| **12.** |CHAP |Não é possível remover as credenciais CHAP depois de criadas. Além disso, se você modificar as credenciais CHAP, precisará colocar os volumes offline e colocá-los novamente online para que a alteração tenha efeito. |Isso será corrigido em uma versão futura. |
| **13.** |Servidor iSCSI |O 'Armazenamento usado' exibido para um volume iSCSI pode ser diferente no serviço StorSimple Manager e no host iSCSI. |O host iSCSI tem o modo de exibição do sistema de arquivos.<br></br>O dispositivo detecta os blocos atribuídos quando o volume está no tamanho máximo. |
| **14.** |Servidor de arquivos\* |Se um arquivo em uma pasta tiver um Fluxo de Dados Alternativo (ADS) associado a ele, o ADS não será copiado ou restaurado por meio da recuperação de desastres, clonagem e Recuperação no Nível do Item. | |

## <a name="next-step"></a>Próxima etapa
[Instale as Atualizações](storsimple-ova-install-update-01.md) em seu StorSimple Virtual Array.

