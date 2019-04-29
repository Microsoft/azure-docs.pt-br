---
title: Notas de versão da Matriz Virtual do StorSimple Atualização 1.0 | Microsoft Docs
description: Descreve os problemas críticos em aberto e as resoluções para a Matriz Virtual do StorSimple que executa a Atualização 1.0.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: fdf37a8360ec69017458fabee2a9e16aa2c160aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60789664"
---
# <a name="storsimple-virtual-array-update-10-release-notes"></a>Notas de versão da atualização 1.0 da StorSimple Virtual Array

## <a name="overview"></a>Visão geral

As notas de versão a seguir identificam as questões críticas em aberto e os problemas resolvidos para as atualizações do Microsoft Azure StorSimple Virtual Array.

As notas de versão são continuamente atualizadas e, à medida que são descobertas questões críticas que exijam uma solução alternativa, elas são adicionadas. Antes de implantar o StorSimple Virtual Array, examine cuidadosamente as informações contidas nas notas de versão.

A Atualização 1.0 corresponde à versão de software **10.0.10296.0**.

> [!IMPORTANT]
> - As atualizações causam interrupção e reiniciam seu dispositivo. Se processos de E/S estiverem em andamento, o dispositivo sofrerá tempo de inatividade. Para obter instruções detalhadas sobre como aplicar a atualização, acesse [Instalar a Atualização 1.0](storsimple-virtual-array-install-update-1.md).
>
> - A Atualização 1 estará disponível para você por meio do Portal do Azure apenas se o dispositivo estiver executando a Atualização 0.6.

## <a name="whats-new-in-update-10"></a>Novidades na Atualização 1.0

**A Atualização 1.0 contém alterações relacionadas à autenticação do serviço do Gerenciador de Dispositivos StorSimple e deve ser implantada assim que possível.** Esta atualização contém os seguintes aprimoramentos e correções de bug:

 - **Uso do AAD (Azure Active Directory) para autenticar com o serviço do Gerenciador de Dispositivos do StorSimple** – da Atualização 1.0 em diante, o Azure Active Directory será usado para autenticar com o serviço do Gerenciador de Dispositivos do StorSimple. O mecanismo de autenticação antigo será preterido até dezembro de 2017. Todos os usuários devem incluir novas URLs de autenticação em suas regras de firewall. Para obter mais informações, acesse as [URLs de autenticação listadas nos requisitos de rede para sua Matriz Virtual StorSimple](storsimple-ova-system-requirements.md).
 
    Se a URL de autenticação não estiver incluída nas regras de firewall, os usuários verão um alerta crítico, informando que não foi possível autenticar o dispositivo StorSimple no serviço. Se os usuários virem esse alerta, será necessário incluir a nova URL de autenticação. Para obter mais informações, acesse [Alertas de rede do StorSimple](storsimple-virtual-array-manage-alerts.md).

 - **Melhoria no desempenho** – várias correções de bugs foram feitas para melhorar as velocidades de leituras de nuvem, entradas e saídas de nuvem. Como resultado, o desempenho de backup e restauração melhorou para dispositivos iSCSI e de servidor de arquivos.

 - **Aperfeiçoamento de coleta de lixo** – esta versão contém correções de bugs que melhoram o desempenho do ciclo de coleta de lixo quando a conta de armazenamento e de dispositivo estão em duas regiões distantes.

 - **Aperfeiçoamento de registro em log** – esta versão contém aprimoramentos para registro em log relacionadas à coleta de lixo e ao caminho de E/S.


## <a name="issues-fixed-in-update-10"></a>Problemas corrigidos na Atualização 1.0

A tabela a seguir fornece um resumo dos problemas corrigidos nesta versão.

| Nº | Recurso | Problema |
| --- | --- | --- |
| 1 |Autenticação baseada no AAD| Esta versão contém alterações que permitem que o AAD autentique com o Gerenciador de Dispositivos do StorSimple.|
| 2 |Coleta de lixo| Esse problema foi relatado no local de um cliente em que as contas de armazenamento e de dispositivo estão em regiões diferentes e o cliente relatou erros de rede intermitentes impactando assim a cobrança. Nessa versão, esse problema foi corrigido. |
| 3 |Desempenho| Esta versão contém alterações que resultam na melhoria do desempenho na restauração/leituras de nuvem/entrada de nuvem/saída de nuvem.|
| 4 |Atualizar| Houve um problema com a atualização na versão anterior que resultava em falhas de backup no local do cliente. Esse problema foi corrigido nesta versão.|

## <a name="known-issues-in-update-10"></a>Problemas conhecidos na Atualização 1.0

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
[Instalar a Atualização 1.0](storsimple-virtual-array-install-update-1.md) em seu StorSimple Virtual Array.

## <a name="references"></a>Referências
Procurando uma nota de versão mais antiga? Acesse:
*  [Notas de versão da atualização 0.6 da StorSimple Virtual Array](storsimple-virtual-array-update-06-release-notes.md)
* [Notas de versão da Atualização 0.5 do StorSimple Virtual Array](storsimple-virtual-array-update-05-release-notes.md)
* [Notas de versão da Matriz Virtual do StorSimple Atualização 0.4](storsimple-virtual-array-update-04-release-notes.md)
* [Notas de versão da atualização 0.3 da StorSimple Virtual Array](storsimple-ova-update-03-release-notes.md)
* [Notas de versão as Atualizações 0.1 e 0.2 do StorSimple Virtual Array](storsimple-ova-update-01-release-notes.md)
* [Notas de versão de disponibilidade geral do StorSimple Virtual Array](storsimple-ova-pp-release-notes.md)
