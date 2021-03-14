---
title: Migração de NAS local para Sincronização de Arquivos do Azure
description: Saiba como migrar arquivos de um local de armazenamento conectado à rede local (NAS) para uma implantação de nuvem híbrida com Sincronização de Arquivos do Azure e compartilhamentos de arquivos do Azure.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 86e79302716fa502d8562dd563b0a5c5fb220a67
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102547537"
---
# <a name="migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Migre do NAS (armazenamento anexado à rede) para uma implantação de nuvem híbrida com Sincronização de Arquivos do Azure

Este artigo de migração é um dos vários que envolvem as palavras-chave NAS e Sincronização de Arquivos do Azure. Verifique se este artigo se aplica ao seu cenário:

> [!div class="checklist"]
> * Fonte de dados: NAS (armazenamento anexado à rede)
> * Rota de migração: o &rArr; servidor do Windows Server é &rArr; carregado e sincronizado com os compartilhamentos de arquivos do Azure
> * Caching de arquivos locais: Sim, o objetivo final é uma implantação Sincronização de Arquivos do Azure.

Se seu cenário for diferente, examine a [tabela de guias de migração](storage-files-migration-overview.md#migration-guides).

Sincronização de Arquivos do Azure funciona em locais DAS (armazenamento anexado direto) e não oferece suporte à sincronização para os locais de NAS (armazenamento anexado à rede).
Esse fato faz uma migração dos arquivos necessários e este artigo o orienta durante o planejamento e a execução de tal migração.

## <a name="migration-goals"></a>Metas de migração

O objetivo é mover os compartilhamentos que você tem em seu dispositivo NAS para um Windows Server. Em seguida, utilize Sincronização de Arquivos do Azure para uma implantação de nuvem híbrida. Em geral, as migrações precisam ser feitas de uma maneira que Guaranty a integridade dos dados de produção e sua disponibilidade durante a migração. O segundo requer um mínimo de tempo de inatividade, para que ele possa se ajustar ou ter apenas um pouco mais de uma janela de manutenção regular.

## <a name="migration-overview"></a>Visão geral da migração

Conforme mencionado no [artigo Visão geral da migração](storage-files-migration-overview.md)de arquivos do Azure, é importante usar a ferramenta de cópia correta e a abordagem. Seu dispositivo NAS está expondo compartilhamentos SMB diretamente em sua rede local. O RoboCopy, interno do Windows Server, é a melhor maneira de mover seus arquivos nesse cenário de migração.

- Fase 1: [identificar Quantos compartilhamentos de arquivos do Azure você precisa](#phase-1-identify-how-many-azure-file-shares-you-need)
- Fase 2: [provisionar um Windows Server local adequado](#phase-2-provision-a-suitable-windows-server-on-premises)
- Fase 3: [implantar o sincronização de arquivos do Azure recurso de nuvem](#phase-3-deploy-the-azure-file-sync-cloud-resource)
- Fase 4: [implantar recursos de armazenamento do Azure](#phase-4-deploy-azure-storage-resources)
- Fase 5: [implantar o agente de sincronização de arquivos do Azure](#phase-5-deploy-the-azure-file-sync-agent)
- Fase 6: [configurar sincronização de arquivos do Azure no Windows Server](#phase-6-configure-azure-file-sync-on-the-windows-server)
- Fase 7: [Robocopy](#phase-7-robocopy)
- Fase 8: [recortar o usuário](#phase-8-user-cut-over)

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>Fase 1: identificar Quantos compartilhamentos de arquivos do Azure você precisa

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-on-premises"></a>Fase 2: provisionar um Windows Server local adequado

* Crie um Windows Server 2019 – no mínimo 2012R2-como uma máquina virtual ou um servidor físico. Também há suporte para um cluster de failover do Windows Server.
* Provisione ou adicione armazenamento de conexão direta (DAS em comparação com o NAS, para o qual não há suporte).

    A quantidade de armazenamento que você provisiona pode ser menor do que o que você está usando atualmente em seu dispositivo NAS. Essa opção de configuração requer que você também use o recurso de camadas de [nuvem](storage-sync-cloud-tiering-overview.md) de sincronizações de arquivos do Azure.
    No entanto, quando você copiar os arquivos do espaço de NAS maior para o volume menor do Windows Server em uma fase posterior, será necessário trabalhar em lotes:

    1. Mover um conjunto de arquivos que se ajustam ao disco
    2. permitir que a sincronização de arquivos e a camada de nuvem se envolvam
    3. Quando mais espaço livre for criado no volume, prossiga com o próximo lote de arquivos. Como alternativa, examine o comando RoboCopy na próxima [seção Robocopy](#phase-7-robocopy) para usar o novo `/LFSM` comutador. `/LFSM`O uso do pode simplificar significativamente seus trabalhos do Robocopy, mas não é compatível com algumas outras opções do Robocopy nas quais você pode depender.
    
    Você pode evitar essa abordagem de envio em lote Provisionando o espaço equivalente no Windows Server que os arquivos ocupam no dispositivo NAS. Considere a eliminação de duplicação no NAS/Windows. Se você não quiser confirmar permanentemente essa grande quantidade de armazenamento para o Windows Server, poderá reduzir o tamanho do volume após a migração e antes de ajustar as políticas de camadas de nuvem. Isso cria um cache local menor de seus compartilhamentos de arquivos do Azure.

A configuração de recurso (computação e RAM) do Windows Server que você implanta depende principalmente do número de itens (arquivos e pastas) que serão sincronizados. É recomendável usar uma configuração de desempenho maior se você tiver alguma preocupação.

[Saiba como dimensionar um Windows Server com base no número de itens (arquivos e pastas) que você precisa sincronizar.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> O artigo vinculado anteriormente apresenta uma tabela com um intervalo para a memória do servidor (RAM). Você pode orientar em direção ao número menor para o servidor, mas esperar que a sincronização inicial possa levar muito mais tempo.

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>Fase 3: implantar o Sincronização de Arquivos do Azure recurso de nuvem

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>Fase 4: implantar recursos de armazenamento do Azure

Nesta fase, consulte a tabela de mapeamento da fase 1 e use-a para provisionar o número correto de contas de armazenamento do Azure e compartilhamentos de arquivos dentro deles.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>Fase 5: implantar o agente de Sincronização de Arquivos do Azure

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server"></a>Fase 6: Configurar Sincronização de Arquivos do Azure no Windows Server

Seu Windows Server local registrado deve estar pronto e conectado à Internet para esse processo.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> A camada de nuvem é o recurso AFS que permite que o servidor local tenha menos capacidade de armazenamento do que o armazenado na nuvem, mas que tenha o namespace completo disponível. Dados interessantes localmente também são armazenados em cache localmente para desempenho rápido de acesso. A camada de nuvem é um recurso opcional por Sincronização de Arquivos do Azure "ponto de extremidade do servidor".

> [!WARNING]
> Se você provisionou menos armazenamento em seus volumes do Windows Server do que os dados usados no dispositivo NAS, a camada de nuvem é obrigatória. Se você não ativar a disposição em camadas de nuvem, o servidor não liberará espaço para armazenar todos os arquivos. Defina a política de camadas, temporariamente para a migração, para 99% de espaço livre no volume. Certifique-se de retornar às configurações de camadas de nuvem após a conclusão da migração e defina-a para um nível útil de mais longo prazo.

Repita as etapas da criação do grupo de sincronização e adição da pasta de servidor correspondente como um ponto de extremidade do servidor para todos os compartilhamentos de arquivos/locais de servidor do Azure que precisam ser configurados para sincronização.

Após a criação de todos os pontos de extremidade do servidor, a sincronização está funcionando. Você pode criar um arquivo de teste e vê-lo sincronizar do local do servidor com o compartilhamento de arquivos do Azure conectado (conforme descrito pelo ponto de extremidade de nuvem no grupo de sincronização).

Ambos os locais, as pastas de servidor e os compartilhamentos de arquivos do Azure estão vazios e aguardando dados em qualquer local. Na próxima etapa, você começará a copiar arquivos no Windows Server para Sincronização de Arquivos do Azure para movê-los para a nuvem. Caso você tenha habilitado camadas de nuvem, o servidor começará a hierarquizar os arquivos, caso você fique sem capacidade no volume (s) local (es).

## <a name="phase-7-robocopy"></a>Fase 7: RoboCopy

A abordagem de migração básica é um RoboCopy de seu dispositivo NAS para o Windows Server e Sincronização de Arquivos do Azure aos compartilhamentos de arquivos do Azure.

Execute a primeira cópia local em sua pasta de destino do Windows Server:

* Identifique o primeiro local em seu dispositivo NAS.
* Identifique a pasta correspondente no Windows Server, que já tem Sincronização de Arquivos do Azure configurada nela.
* Iniciar a cópia usando o RoboCopy

O comando RoboCopy a seguir copiará os arquivos do armazenamento NAS para a pasta de destino do Windows Server. O Windows Server irá sincronizá-lo para os compartilhamentos de arquivos do Azure. 

Se você provisionou menos armazenamento no Windows Server do que seus arquivos ocupam no dispositivo NAS, você configurou a camada de nuvem. Como o volume do Windows Server local fica cheio, a disposição em [camadas da nuvem](storage-sync-cloud-tiering-overview.md) será iniciada e os arquivos de camada que já foram sincronizados com êxito. A disposição em camadas da nuvem gerará espaço suficiente para continuar a cópia do dispositivo NAS. As verificações de camadas de nuvem uma vez por hora para ver o que foi sincronizado e liberar espaço em disco para alcançar o espaço livre do volume de 99%.
É possível que o RoboCopy mova arquivos mais rápido do que você pode sincronizar com a nuvem e a camada localmente, ficando sem espaço em disco local. O RoboCopy falhará. É recomendável que você trabalhe nos compartilhamentos em uma sequência que impede isso. Por exemplo, não iniciar trabalhos do RoboCopy para todos os compartilhamentos ao mesmo tempo ou mover somente compartilhamentos que caibam na quantidade atual de espaço livre no Windows Server, para mencionar alguns.

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

## <a name="phase-8-user-cut-over"></a>Fase 8: recortar o usuário

Quando você executa o comando RoboCopy pela primeira vez, seus usuários e aplicativos ainda estão acessando arquivos no NAS e potencialmente os alteram. É possível que o RoboCopy tenha processado um diretório, passe para o próximo e, em seguida, um usuário no local de origem (NAS) adicione, altere ou exclua um arquivo que agora não será processado nesta execução atual do RoboCopy. O comportamento é esperado.

A primeira execução é sobre a movimentação da massa dos dados para o Windows Server e para a nuvem por meio de Sincronização de Arquivos do Azure. Essa primeira cópia pode levar muito tempo, dependendo de:

* sua largura de banda de download
* a largura de banda de upload
* a velocidade da rede local e o número de quão otimizado o número de threads RoboCopy corresponde a ele
* o número de itens (arquivos e pastas) que precisam ser processados pelo RoboCopy e Sincronização de Arquivos do Azure

Depois que a execução inicial for concluída, execute o comando novamente.

A segunda vez que terminará mais rapidamente, porque ele só precisa transportar alterações ocorridas desde a última execução. Durante essa segunda execução, ainda assim, novas alterações podem ser acumuladas.

Repita esse processo até estar convencido de que o tempo necessário para concluir um RoboCopy para um local específico está dentro de uma janela aceitável para tempo de inatividade.

Ao considerar o tempo de inatividade aceitável, você precisa remover o acesso do usuário aos seus compartilhamentos baseados em NAS. Você pode fazer isso por qualquer etapa que impeça os usuários de alterar o conteúdo e a estrutura de arquivos e pastas. Um exemplo é apontar seu DFS-Namespace para um local não existente ou alterar as ACLs raiz no compartilhamento.

Executar uma última rodada do RoboCopy. Ele escolherá todas as alterações, que podem ter sido perdidas.
Quanto tempo leva essa etapa final, depende da velocidade da verificação do RoboCopy. Você pode estimar o tempo (que é igual ao seu tempo de inatividade) medindo quanto tempo a execução anterior levou.

Crie um compartilhamento na pasta do Windows Server e, possivelmente, ajuste sua implantação do DFS-N para apontar para ele. Certifique-se de definir as mesmas permissões de nível de compartilhamento que em seu compartilhamento SMB de NAS. Se você tivesse um NAS de classe empresarial ingressado no domínio, os SIDs do usuário corresponderão automaticamente à medida que os usuários existirem em Active Directory e o RoboCopy copia arquivos e metadados com total fidelidade. Se você tiver usado usuários locais em seu NAS, precisará recriar esses usuários como usuários locais do Windows Server e mapear os SIDs RoboCopy existentes movidos para o Windows Server para os SIDs dos novos usuários locais do Windows Server.

Você concluiu a migração de um compartilhamento/grupo de compartilhamentos em um volume ou raiz comum. (Dependendo do mapeamento da fase 1)

Você pode tentar executar algumas dessas cópias em paralelo. É recomendável processar o escopo de um compartilhamento de arquivos do Azure por vez.

> [!WARNING]
> Depois de mover todos os dados do NAS para o Windows Server e sua migração estiver concluída: retorne a ***todos os***  grupos de sincronização na portal do Azure e ajuste o valor de porcentagem de espaço livre do volume de camadas da nuvem para algo mais adequado para a utilização do cache, digamos 20%. 

A política de espaço livre do volume de camadas de nuvem age em um nível de volume com potencialmente vários pontos de extremidade de servidor sincronizando a partir dele. Se você se esquecer de ajustar o espaço livre em um ponto de extremidade de servidor, a sincronização continuará a aplicar a regra mais restritiva e tentará manter 99% de espaço livre em disco, tornando o cache local não funcionando como você poderia esperar. A menos que seja o objetivo de ter apenas o namespace para um volume que contenha apenas dados de arquivamento raramente acessados e você esteja reservando o restante do espaço de armazenamento para outro cenário.

## <a name="troubleshoot"></a>Solucionar problemas

O problema mais provável que você pode encontrar é que o comando RoboCopy falha com *"volume cheio"* no lado do Windows Server. A camada de nuvem age uma vez a cada hora para evacuar o conteúdo do disco local do Windows Server, que foi sincronizado. Seu objetivo é atingir o espaço livre de 99% no volume.

Deixe o progresso da sincronização e a camada da nuvem liberar espaço em disco. Você pode observar isso no explorador de arquivos no seu Windows Server.

Quando o seu Windows Server tiver capacidade disponível suficiente, a reexecução do comando resolverá o problema. Nada é interrompido quando você entra nessa situação e pode avançar com confiança. A inconveniência de executar o comando novamente é a única consequência.

Verifique o link na seção a seguir para solucionar problemas de Sincronização de Arquivos do Azure.

## <a name="next-steps"></a>Próximas etapas

Há mais a descobrir sobre compartilhamentos de arquivos do Azure e Sincronização de Arquivos do Azure. Os artigos a seguir ajudam a entender opções avançadas, práticas recomendadas e também contêm ajuda para solução de problemas. Estes artigos se vinculam à [documentação do compartilhamento de arquivos do Azure](storage-files-introduction.md) , conforme apropriado.

* [Visão geral de AFS](./storage-sync-files-planning.md)
* [Guia de implantação AFS](./storage-how-to-create-file-share.md)
* [Solução de problemas AFS](storage-sync-files-troubleshoot.md)