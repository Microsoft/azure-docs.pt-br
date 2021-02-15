---
title: Notas de versão da atualização 1,2 do Microsoft Azure StorSimple virtual array | Microsoft Docs
description: Descreve problemas e resoluções críticos em aberto para a matriz virtual StorSimple que executa a atualização 1,2.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: 1118dfcec67a49365f1f6e5e522e98b97694d052
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94960182"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>Notas de versão da atualização 1,2 do StorSimple virtual array

As notas de versão a seguir identificam as questões críticas em aberto e os problemas resolvidos para as atualizações do Microsoft Azure StorSimple Virtual Array.

As notas de versão são atualizadas continuamente. Problemas críticos que exijam soluções alternativas serão adicionados à medida que forem descobertos. Antes de implantar o StorSimple Virtual Array, examine cuidadosamente as informações contidas nas notas de versão.

A atualização 1,2 corresponde à versão de software **10.0.10311.0**.

> [!IMPORTANT]
> - As atualizações causam interrupção e reiniciam seu dispositivo. Se processos de E/S estiverem em andamento, o dispositivo sofrerá tempo de inatividade. Para obter instruções detalhadas sobre os pacotes usados para aplicar essa atualização, acesse [baixar a atualização 1,2](#download-update-12).
> - A atualização 1,2 estará disponível por meio do portal do Azure somente se o dispositivo estiver executando a atualização 1,0 ou 1,1.

## <a name="whats-new-in-update-12"></a>Novidades na Atualização 1.2

Esta atualização contém as seguintes correções de bugs:

- Maior resiliência ao processar arquivos excluídos.
- Tratamento de exceções aprimorado no caminho de inicialização do código, levando a falhas reduzidas em backups, restauração, leituras de nuvem e recuperação de espaço automatizada.

## <a name="download-update-12"></a>Baixar a atualização 1,2

Para baixar essa atualização, vá para o servidor de [Catálogo de Microsoft Update](https://www.catalog.update.microsoft.com/Home.aspx) e baixe o pacote KB4502035. Este pacote contém os seguintes pacotes:

 - **KB4493446** que contém atualizações cumulativas do Windows para 2012 R2 até abril de 2019. Para obter mais informações sobre o que está incluído neste ROLLUP, vá para [ROLLUP de segurança mensal de abril](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446).
 - **KB3011067** , que é um arquivo de pacote autônomo Microsoft Update WINDOWSTH-KB3011067-x64. Esse arquivo é usado para atualizar o software do dispositivo.

Baixe o KB4502035 e siga estas instruções para [aplicar a atualização por meio da interface do usuário da Web local](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="issues-fixed-in-update-12"></a>Problemas corrigidos na Atualização 1.2

A tabela a seguir fornece um resumo dos problemas corrigidos nesta versão.

| Não. | Recurso | Problema |
| --- | --- | --- |
| 1 |Exclusão| Nas versões anteriores do software, houve um problema quando o uso do dispositivo não foi alterado, mesmo quando os arquivos foram excluídos. Esse problema foi corrigido nesta versão. O caminho do código de camadas foi tornado mais resiliente ao processar arquivos excluídos.|
| 2 |Tratamento de exceções| Nas versões anteriores do software, houve um problema após a reinicialização do sistema que poderia potencialmente levar a falhas em backups, restauração, leitura da nuvem e recuperação de espaço automatizada. Esta versão contém alterações sobre como as exceções foram tratadas no caminho de inicialização.|

## <a name="known-issues-in-update-12"></a>Problemas conhecidos na Atualização 1.2

Nenhum novo problema foi lançado na atualização 1,2. Todos os problemas indicados pela versão são transferidos de versões anteriores. Para ver o resumo dos problemas conhecidos incluídos nas versões anteriores, vá para [problemas conhecidos na atualização 1,1](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11).

## <a name="next-steps"></a>Próximas etapas

Baixe o KB4502035 e [aplique a atualização por meio da interface do usuário da Web local](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="references"></a>Referências

Procurando uma nota de versão mais antiga? Acesse:
* [Notas de versão da atualização 1,1 do StorSimple virtual array](storsimple-virtual-array-update-11-release-notes.md)
* [Notas de versão do Update 1.0 do Array Virtual StorSimple](storsimple-virtual-array-update-1-release-notes.md)
* [Notas de versão da atualização 0,6 do StorSimple virtual array](storsimple-virtual-array-update-06-release-notes.md)
* [Notas de versão da Atualização 0.5 do StorSimple Virtual Array](storsimple-virtual-array-update-05-release-notes.md)
* [Notas de versão da atualização 0,4 do StorSimple virtual array](storsimple-virtual-array-update-04-release-notes.md)
* [Notas de versão da atualização 0,3 do StorSimple virtual array](storsimple-ova-update-03-release-notes.md)
* [Notas de versão as Atualizações 0.1 e 0.2 do StorSimple Virtual Array](storsimple-ova-update-01-release-notes.md)
* [Notas de versão de disponibilidade geral do StorSimple Virtual Array](./storsimple-virtual-array-update-06-release-notes.md)