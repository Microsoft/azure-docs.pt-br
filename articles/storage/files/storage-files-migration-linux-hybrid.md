---
title: Migração do Linux para Sincronização de Arquivos do Azure
description: Saiba como migrar arquivos de um local do servidor Linux para uma implantação de nuvem híbrida com Sincronização de Arquivos do Azure e compartilhamentos de arquivos do Azure.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: ff26318cafdf493579961fc718643f831ae9efeb
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564247"
---
# <a name="migrate-from-linux-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Migre do Linux para uma implantação de nuvem híbrida com Sincronização de Arquivos do Azure

Este artigo de migração é um dos vários que envolvem as palavras-chave NFS e Sincronização de Arquivos do Azure. Verifique se este artigo se aplica ao seu cenário:

> [!div class="checklist"]
> * Fonte de dados: NAS (armazenamento anexado à rede)
> * Rota de migração: servidor Linux com o &rArr; Windows Server 2012R2 do samba ou &rArr; sincronização posterior com compartilhamento (s) de arquivos do Azure
> * Caching de arquivos locais: Sim, o objetivo final é uma implantação Sincronização de Arquivos do Azure.

Se seu cenário for diferente, examine a [tabela de guias de migração](storage-files-migration-overview.md#migration-guides).

Sincronização de Arquivos do Azure funciona em instâncias do Windows Server com DAS (armazenamento com conexão direta). Ele não oferece suporte à sincronização de e para clientes Linux, ou a um compartilhamento de protocolo SMB ou compartilhamentos de NFS (sistema de arquivos de rede).

Como resultado, transformar os serviços de arquivo em uma implantação híbrida faz uma migração para o Windows Server necessária. Este artigo orienta você pelo planejamento e pela execução de tal migração.

## <a name="migration-goals"></a>Metas de migração

O objetivo é mover os compartilhamentos que você tem em seu servidor Samba do Linux para uma instância do Windows Server. Em seguida, use Sincronização de Arquivos do Azure para uma implantação de nuvem híbrida. Essa migração precisa ser feita de forma a garantir a integridade dos dados de produção e da disponibilidade durante a migração. O segundo requer um mínimo de tempo de inatividade, para que ele possa se ajustar ou ter apenas um pouco mais de uma janela de manutenção regular.

## <a name="migration-overview"></a>Visão geral da migração

Conforme mencionado no [artigo Visão geral da migração](storage-files-migration-overview.md)de arquivos do Azure, é importante usar a ferramenta de cópia correta e a abordagem. Seu servidor do Linux Samba está expondo compartilhamentos SMB diretamente em sua rede local. O Robocopy, criado no Windows Server, é a melhor maneira de mover seus arquivos nesse cenário de migração.

Se você não estiver executando o samba em seu servidor Linux e, em vez disso, quiser migrar pastas para uma implantação híbrida no Windows Server, poderá usar as ferramentas de cópia do Linux em vez do Robocopy. Esteja atento aos recursos de fidelidade da sua ferramenta de cópia. Examine a [seção noções básicas de migração](storage-files-migration-overview.md#migration-basics) no artigo Visão geral da migração para saber o que procurar em uma ferramenta de cópia.

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>Fase 1: identificar Quantos compartilhamentos de arquivos do Azure você precisa

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-instance-on-premises"></a>Fase 2: provisionar uma instância do Windows Server local adequada

* Crie uma instância do Windows Server 2019 como uma máquina virtual ou um servidor físico. O Windows Server 2012 R2 é o requisito mínimo. Também há suporte para um cluster de failover do Windows Server.
* Provisione ou adicione o DAS (armazenamento anexado direto). Não há suporte para Armazenamento NAS (Network Attached Storage).

  A quantidade de armazenamento que você provisiona pode ser menor do que o que você está usando atualmente em seu servidor do Linux Samba, se você usar o recurso de [camadas de nuvem](storage-sync-cloud-tiering-overview.md) sincronização de arquivos do Azure. 

A quantidade de armazenamento que você provisiona pode ser menor do que o que você está usando atualmente em seu servidor do Linux Samba. Essa opção de configuração requer que você também use o recurso de camadas de [nuvem](storage-sync-cloud-tiering-overview.md) de sincronizações de arquivos do Azure. No entanto, ao copiar os arquivos do espaço do servidor Samba do Linux maior para o volume menor do Windows Server em uma fase posterior, você precisará trabalhar em lotes:

  1. Mova um conjunto de arquivos que caibam no disco.
  2. Permita que a sincronização de arquivos e a camada de nuvem se envolvam.
  3. Quando mais espaço livre for criado no volume, prossiga com o próximo lote de arquivos. Como alternativa, examine o comando RoboCopy na próxima [seção Robocopy](#phase-7-robocopy) para usar o novo `/LFSM` comutador. `/LFSM`O uso do pode simplificar significativamente seus trabalhos do Robocopy, mas não é compatível com algumas outras opções do Robocopy nas quais você pode depender.
    
  Você pode evitar essa abordagem de envio em lote Provisionando o espaço equivalente na instância do Windows Server que os arquivos ocupam no servidor do Linux Samba. Considere habilitar a eliminação de duplicação no Windows. Se você não quiser confirmar permanentemente essa grande quantidade de armazenamento para sua instância do Windows Server, poderá reduzir o tamanho do volume após a migração e antes de ajustar as políticas de camadas de nuvem. Isso cria um cache local menor de seus compartilhamentos de arquivos do Azure.

A configuração de recurso (computação e RAM) da instância do Windows Server que você implanta depende principalmente do número de itens (arquivos e pastas) que você estará sincronizando. É recomendável usar uma configuração de alto desempenho se você tiver alguma preocupação.

[Saiba como dimensionar uma instância do Windows Server com base no número de itens (arquivos e pastas) que você precisa sincronizar.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> O artigo vinculado anteriormente apresenta uma tabela com um intervalo para a memória do servidor (RAM). Você pode orientar em direção ao número menor para o servidor, mas esperar que a sincronização inicial possa levar muito mais tempo.

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>Fase 3: implantar o Sincronização de Arquivos do Azure recurso de nuvem

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>Fase 4: implantar recursos de armazenamento do Azure

Nesta fase, consulte a tabela de mapeamento da fase 1 e use-a para provisionar o número correto de contas de armazenamento do Azure e compartilhamentos de arquivos dentro deles.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>Fase 5: implantar o agente de Sincronização de Arquivos do Azure

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server-deployment"></a>Fase 6: Configurar Sincronização de Arquivos do Azure na implantação do Windows Server

Sua instância do Windows Server local registrada deve estar pronta e conectada à Internet para esse processo.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> A camada de nuvem é o recurso Sincronização de Arquivos do Azure que permite que o servidor local tenha menos capacidade de armazenamento do que o armazenado na nuvem, mas que tenha o namespace completo disponível. Dados interessantes localmente também são armazenados em cache localmente para desempenho rápido de acesso. A camada de nuvem é um recurso opcional para cada ponto de extremidade do Sincronização de Arquivos do Azure Server.

> [!WARNING]
> Se você provisionou menos armazenamento em volumes do Windows Server do que os dados usados no servidor do Linux Samba, a disposição em camadas da nuvem é obrigatória. Se você não ativar a disposição em camadas de nuvem, o servidor não liberará espaço para armazenar todos os arquivos. Defina a política de camadas, temporariamente para a migração, para 99% de espaço livre para um volume. Certifique-se de retornar às configurações de camadas de nuvem após a conclusão da migração e defina a política para um nível mais útil para o longo prazo.

Repita as etapas de criação do grupo de sincronização e a adição da pasta de servidor correspondente como um ponto de extremidade do servidor para todos os compartilhamentos de arquivos do Azure e locais de servidor que precisam ser configurados para sincronização.

Após a criação de todos os pontos de extremidade do servidor, a sincronização está funcionando. Você pode criar um arquivo de teste e vê-lo sincronizar do local do servidor com o compartilhamento de arquivos do Azure conectado (conforme descrito pelo ponto de extremidade de nuvem no grupo de sincronização).

Os dois locais, as pastas de servidor e os compartilhamentos de arquivos do Azure, estão vazios e aguardando dados. Na próxima etapa, você começará a copiar arquivos na instância do Windows Server para Sincronização de Arquivos do Azure para movê-los para a nuvem. Se você habilitou a camada de nuvem, o servidor começará a hierarquizar os arquivos se você ficar sem capacidade nos volumes locais.

## <a name="phase-7-robocopy"></a>Fase 7: Robocopy

A abordagem de migração básica é usar o Robocopy para copiar arquivos e usar Sincronização de Arquivos do Azure para fazer a sincronização.

Execute a primeira cópia local em sua pasta de destino do Windows Server:

1. Identifique o primeiro local em seu servidor do Linux Samba.
1. Identifique a pasta correspondente na instância do Windows Server que já tem Sincronização de Arquivos do Azure configurada.
1. Inicie a cópia usando o Robocopy.

O comando Robocopy a seguir copiará arquivos do armazenamento do servidor do Linux Samba para a pasta de destino do Windows Server. O Windows Server irá sincronizá-lo para os compartilhamentos de arquivos do Azure. 

Se você provisionou menos armazenamento em sua instância do Windows Server do que seus arquivos ocupam no servidor do Linux Samba, você configurou a camada de nuvem. Como o volume do Windows Server local fica cheio, a disposição em [camadas da nuvem](storage-sync-cloud-tiering-overview.md) será iniciada e os arquivos de camada que já foram sincronizados com êxito. A disposição em camadas da nuvem gerará espaço suficiente para continuar a cópia do servidor do Linux Samba. As verificações de camadas de nuvem uma vez por hora para ver o que foi sincronizado e liberar espaço em disco para alcançar a política de 99% de espaço livre para um volume.

É possível que o Robocopy mova arquivos mais rápido do que você pode sincronizar com a nuvem e a camada localmente, fazendo com que você fique sem espaço em disco local. O Robocopy então falhará. Recomendamos que você trabalhe nos compartilhamentos em uma sequência que impede o problema. Por exemplo, considere não iniciar trabalhos de Robocopy para todos os compartilhamentos ao mesmo tempo. Ou considere mover compartilhamentos que caibam na quantidade atual de espaço livre na instância do Windows Server. Se o trabalho do Robocopy falhar, você sempre poderá executar novamente o comando, desde que use a seguinte opção de espelhamento/limpeza:

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

## <a name="phase-8-user-cut-over"></a>Fase 8: recortar o usuário

Quando você executa o comando Robocopy pela primeira vez, seus usuários e aplicativos ainda estão acessando arquivos no servidor Linux Samba e potencialmente alterá-los. É possível que o Robocopy tenha processado um diretório e passe para o próximo e, em seguida, um usuário no local de origem (Linux) adicione, altere ou exclua um arquivo que agora não será processado nesta execução atual do Robocopy. O comportamento é esperado.

A primeira execução é sobre a movimentação da massa dos dados para a instância do Windows Server e para a nuvem via Sincronização de Arquivos do Azure. Essa primeira cópia pode levar muito tempo, dependendo de:

* Sua largura de banda de download.
* A largura de banda de upload.
* A velocidade da rede local e o número de quão otimizado o número de threads Robocopy corresponde a ele.
* O número de itens (arquivos e pastas) que o Robocopy e o Sincronização de Arquivos do Azure precisam processar.

Depois que a execução inicial for concluída, execute o comando novamente.

Ele termina mais rapidamente na segunda vez, porque precisa transportar somente as alterações ocorridas desde a última execução. Durante esse segundo, executar novas alterações ainda poderá ser acumulado.

Repita esse processo até estar convencido de que o tempo necessário para concluir uma operação Robocopy para um local específico está dentro de uma janela aceitável para tempo de inatividade.

Quando você considera o tempo de inatividade aceitável e está preparado para colocar o local do Linux offline, pode alterar as ACLs na raiz do compartilhamento, de modo que os usuários não possam mais acessar o local. Ou você pode executar qualquer outra etapa apropriada que impeça o conteúdo de ser alterado nessa pasta no servidor Linux.

Executar uma última rodada do Robocopy. Ele escolherá todas as alterações que possam ter sido perdidas. O quanto tempo leva essa etapa final depende da velocidade da verificação do Robocopy. Você pode estimar o tempo (que é igual ao seu tempo de inatividade) medindo quanto tempo a execução anterior levou.

Crie um compartilhamento na pasta do Windows Server e, possivelmente, ajuste sua implantação do DFS-N para apontar para ele. Certifique-se de definir as mesmas permissões de nível de compartilhamento que em seus compartilhamentos SMB do servidor do Linux Samba. Se você tiver usado usuários locais em seu servidor do Linux Samba, precisará recriar esses usuários como usuários locais do Windows Server. Você também precisa mapear os SIDs existentes que o Robocopy moveu para sua instância do Windows Server para os SIDs de seus novos usuários locais do Windows Server. Se você usou Active Directory contas e ACLs, o Robocopy as moverá como está, e nenhuma ação adicional será necessária.

Você concluiu a migração de um compartilhamento ou de um grupo de compartilhamentos em um volume ou raiz comum (dependendo do mapeamento da fase 1).

Você pode tentar executar algumas dessas cópias em paralelo. É recomendável processar o escopo de um compartilhamento de arquivos do Azure por vez.

> [!WARNING]
> Depois de mover todos os dados do seu servidor do Linux Samba para a instância do Windows Server e a migração estiver concluída, retorne a *todos os*  grupos de sincronização na portal do Azure. Ajuste a porcentagem de espaço livre para o volume de camadas de nuvem para algo mais adequado para a utilização de cache, como 20%. 

A política de espaço livre no volume de camadas de nuvem atua em um nível de volume com potencialmente vários pontos de extremidade de servidor sincronizando a partir dele. Se você se esquecer de ajustar o espaço livre em um ponto de extremidade de servidor, a sincronização continuará a aplicar a regra mais restritiva e tentará manter o espaço livre em disco em 99%. O cache local pode não ser executado conforme o esperado. O desempenho poderá ser aceitável se o objetivo for ter o namespace para um volume que contenha apenas dados de arquivamento acessados raramente, e você estiver reservando o restante do espaço de armazenamento para outro cenário.

## <a name="troubleshoot"></a>Solucionar problemas

O problema mais comum é que o comando Robocopy falha com **volume cheio** no lado do Windows Server. A camada de nuvem age uma vez a cada hora para evacuar o conteúdo do disco do Windows Server local que foi sincronizado. Seu objetivo é atingir o espaço livre de 99 por cento no volume.

Deixe o progresso da sincronização e a camada da nuvem liberar espaço em disco. Você pode observar isso no explorador de arquivos no Windows Server.

Quando a instância do Windows Server tiver capacidade disponível suficiente, a reexecução do comando resolverá o problema. Nada é interrompido quando você entra nessa situação, e você pode avançar com confiança. O inconveniente da execução do comando novamente é a única consequência.

Verifique o link na seção a seguir para solucionar problemas de Sincronização de Arquivos do Azure.

## <a name="next-steps"></a>Próximas etapas

Há mais a descobrir sobre compartilhamentos de arquivos do Azure e Sincronização de Arquivos do Azure. Os artigos a seguir contêm opções avançadas, práticas recomendadas e ajuda para solução de problemas. Estes artigos se vinculam à [documentação do compartilhamento de arquivos do Azure](storage-files-introduction.md) , conforme apropriado.

* [Visão geral de Sincronização de Arquivos do Azure](./storage-sync-files-planning.md)
* [Guia de implantação do Sincronização de Arquivos do Azure](./storage-how-to-create-file-share.md)
* [Solução de problemas da Sincronização de Arquivos do Azure](storage-sync-files-troubleshoot.md)