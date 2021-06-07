---
title: Solucionar problemas de backup lento de arquivos e pastas
description: Fornece orientação para solução de problemas para ajudá-lo a diagnosticar a causa dos problemas de desempenho de Backup do Azure
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: b3f2ac343ef4a703f347ec8a57f242a636bb32d2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88824008"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Solução de problemas de lentidão de backup de arquivos e pastas no Backup do Azure

Este artigo fornece orientação para solução de problemas para ajudá-lo a diagnosticar a causa do baixo desempenho de backup de arquivos e pastas quando você usa o Backup do Azure. Quando você usa o agente do Backup do Azure para fazer backup de arquivos, o processo de backup pode demorar mais do que o esperado. Esse atraso pode ser causado por um ou mais dos seguintes itens:

* [Há gargalos de desempenho no computador do qual está sendo feito o backup.](#cause1)
* [Outro processo ou software antivírus está interferindo com o processo de Backup do Azure.](#cause2)
* [O agente do Backup está em execução em uma VM (máquina virtual) do Azure.](#cause3)  
* [Você está fazendo backup de um grande número de arquivos (milhões).](#cause4)

Antes de começar a solucionar o problema, recomendamos o download e a instalação do [agente mais recente do Backup do Azure](https://aka.ms/azurebackup_agent). Fazemos atualizações frequentes do agente de Backup para corrigir vários problemas, adicionar recursos e melhorar o desempenho.

Também recomendamos que você revise as [Perguntas frequentes do serviço Backup do Azure](backup-azure-backup-faq.md) para ter certeza de que você não está enfrentando problemas comuns de configuração.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause-backup-job-running-in-unoptimized-mode"></a>Causa: Trabalho de backup em execução no modo não otimizado

* O agente MARS pode executar o trabalho de backup no **modo otimizado** usando o diário de alterações USN (número de sequência de atualização) ou o **modo não otimizado** verificando as alterações em diretórios ou arquivos examinando o volume inteiro.
* O modo não otimizado é lento porque o agente precisa verificar cada arquivo no volume e compará-lo com os metadados para determinar os arquivos alterados.
* Para verificar isso, abra **Detalhes do Trabalho** no console do agente MARS e verifique o status para ver se ele diz **Transferindo dados (não otimizado, pode levar mais tempo)** conforme mostrado abaixo:

    ![Executando no modo não otimizado](./media/backup-azure-troubleshoot-slow-backup-performance-issue/unoptimized-mode.png)

* As seguintes condições podem fazer com que o trabalho de backup seja executado no modo não otimizado:
  * O primeiro backup (também conhecido como Replicação Inicial) sempre será executado no modo não otimizado
  * Se o trabalho de backup anterior falhar, o próximo trabalho de backup agendado será executado como não otimizado.

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Causa: gargalos de desempenho no computador

Gargalos no computador do qual está sendo feito backup podem causar atrasos. Por exemplo, a capacidade do computador de ler ou gravar em disco ou a largura de banda disponível para enviar dados pela rede podem causar gargalos.

O Windows fornece uma ferramenta interna chamada [Monitor de Desempenho](https://techcommunity.microsoft.com/t5/ask-the-performance-team/windows-performance-monitor-overview/ba-p/375481) (Perfmon) para detectar esses gargalos.

Veja alguns contadores de desempenho e intervalos que podem ser úteis para diagnosticar gargalos para obter o backup ideal.

| Contador | Status |
| --- | --- |
| Disco Lógico(Disco Físico) – %ocioso |<li> 100% de ociosidade a 50% ocioso = íntegro</br><li> 49% de ociosidade a 20% ociosos = aviso ou monitor</br><li> 19% ociosos a 0% ocioso = crítico ou fora de especificação |
| Disco Lógico(Disco Físico) -- %média Leitura ou Gravação do Disco por S |<li> 0, 1 ms a 0, 15 ms = íntegro</br><li> 0, 15 ms a 0, 25 MS = aviso ou monitor</br><li> 0, 26 MS ou maior = crítico ou fora de especificação |
| Disco Lógico(Disco Físico) -- Comprimento da Fila do Disco Atual (para todas as instâncias) |80 solicitações por mais de seis minutos |
| Memória--Bytes de Pool não Pagináveis |<li> Menos de 60% do pool consumido = íntegro<br><li> 61% a 80% do pool consumido = aviso ou monitor</br><li> Mais de 80% de pool consumidos = crítico ou fora de especificação |
| Memória--Bytes de Pool Pagináveis |<li> Menos de 60% do pool consumido = íntegro</br><li> 61% a 80% do pool consumido = aviso ou monitor</br><li> Mais de 80% de pool consumidos = crítico ou fora de especificação |
| Memória--Megabytes disponíveis |<li> 50% de memória livre disponível ou mais = íntegro</br><li> 25% de memória livre disponível = monitor</br><li>10% de memória livre disponível = aviso</br><li> Menos de 100 MB ou 5% de memória livre disponível = crítico ou fora de especificação |
| Processor--\%Tempo do Processor (todas as instâncias) |<li> Menos de 60% consumidas = íntegro</br><li> 61% a 90% consumidas = monitor ou cuidado</br><li> 91% a 100% consumido = crítico |

> [!NOTE]
> Se você determinar que a infraestrutura é o motivo, é recomendável desfragmentar os discos regularmente para melhorar o desempenho.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Causa: outro processo ou software antivírus está interferindo com o Backup do Azure

Vimos várias instâncias nas quais outros processos no sistema do Windows afetou negativamente o desempenho do processo do agente de Backup do Azure. Por exemplo, se você usar o agente de Backup do Azure e outro programa para fazer backup de dados, ou se um software antivírus estiver em execução e bloquear os arquivos dos quais o backup deverá ser feito, os vários bloqueios nos arquivos poderão causar contenção. Nessa situação, o backup pode falhar ou o trabalho pode levar mais tempo do que o esperado.

A melhor recomendação nesse cenário é desativar o outro programa backup para ver se o tempo de backup do agente de Backup do Azure muda. Geralmente, certificar-se de que não haja vários trabalhos de backup em execução simultaneamente é suficiente para impedir que eles afetem um ao outro.

Para programas antivírus, recomendamos a exclusão dos seguintes arquivos e locais:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe como um processo
* C:\Program Files\Microsoft Azure Recovery Services Agent\ pastas
* Local de rascunho (se você não estiver usando o local padrão)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Causa: Agente de backup em execução em uma máquina virtual do Azure

Se você estiver executando o agente de Backup em uma máquina virtual, o desempenho será mais lento do que quando você executá-lo em uma máquina física. Isso é esperado devido a limitações de IOPS.  No entanto, você pode otimizar o desempenho alternando as unidades de dados que estão passando por backup no Armazenamento Premium do Azure. Estamos trabalhando para corrigir esse problema, e a correção estará disponível em uma versão futura.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Causa: backup de um grande número de arquivos (milhões)

Mover um grande volume de dados levará mais tempo do que mover um menor volume de dados. Em alguns casos, o tempo de backup está relacionado não apenas ao tamanho dos dados, mas também ao número de arquivos ou pastas. Isso é particularmente verdadeiro quando está sendo feito backup de milhões de arquivos pequenos (alguns bytes ou poucos kilobytes).

Esse comportamento ocorre porque, enquanto você faz backup dos dados e os move para o Azure, o Azure simultaneamente cataloga seus arquivos. Em algumas situações raras, a operação de catálogo pode demorar mais do que o esperado.

Os seguintes indicadores podem ajudá-lo a entender o gargalo e funcionam adequadamente nas próximas etapas:

* **A interface do usuário está mostrando o progresso para a transferência de dados**. Os dados ainda estão sendo transferidos. A largura de banda de rede ou o tamanho dos dados podem estar causando atrasos.
* **A interface do usuário não está mostrando o progresso da transferência de dados**. Abra os logs localizados em C:\Arquivos de Programas\Agente de Serviços de Recuperação do Microsoft Azure\Temp e, em seguida, verifique a entrada FileProvider::EndData nos logs. Essa entrada significa que a transferência de dados foi concluída e a operação de catálogo está ocorrendo. Não cancele os trabalhos de backup. Em vez disso, espere um pouco mais até a conclusão da operação de catálogo. Se o problema persistir, contate o [suporte do Azure](https://portal.azure.com/#create/Microsoft.Support).

Se você estiver tentando fazer backup de discos grandes, é recomendável usar [Azure data Box](./offline-backup-azure-data-box.md) para o primeiro backup (replicação inicial).  Se você não puder usar o Data Box, problemas de rede transitórios que estejam ocorrendo em seu ambiente durante transferências de dados longas pela rede poderão causar falhas de backup.  Para proteger-se contra essas falhas, você pode adicionar algumas pastas ao backup inicial e adicionar de modo incremental mais pastas até que tenha sido feito o backup bem-sucedido de todas as pastas no Azure.  Os backups incrementais posteriores serão relativamente mais rápidos.

## <a name="next-steps"></a>Próximas etapas

* [Perguntas frequentes sobre como fazer backup de arquivos e pastas](backup-azure-file-folder-backup-faq.md)
