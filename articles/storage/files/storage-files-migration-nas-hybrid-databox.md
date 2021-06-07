---
title: Migração de NAS local para Sincronização de Arquivos do Azure por meio do Azure data Box
description: Saiba como migrar arquivos de um local de armazenamento conectado à rede local (NAS) para uma implantação de nuvem híbrida com Sincronização de Arquivos do Azure por meio do Azure data box.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/5/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 144b2f23e40f315441c3de2482ae8aeffe77ec75
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583690"
---
# <a name="use-databox-to-migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Use o data box para migrar do NAS (armazenamento anexado à rede) para uma implantação de nuvem híbrida com Sincronização de Arquivos do Azure

Este artigo de migração é um dos vários que envolvem as palavras-chave NAS, Sincronização de Arquivos do Azure e Azure data box. Verifique se este artigo se aplica ao seu cenário:

> [!div class="checklist"]
> * Fonte de dados: NAS (armazenamento anexado à rede)
> * Rota de migração: NAS &rArr; Data Box &rArr; &rArr; a sincronização de compartilhamento de arquivos do Azure com o Windows Server
> * Caching de arquivos locais: Sim, o objetivo final é uma implantação Sincronização de Arquivos do Azure.

Se seu cenário for diferente, examine a [tabela de guias de migração](storage-files-migration-overview.md#migration-guides).

Sincronização de Arquivos do Azure funciona em locais DAS (armazenamento anexado direto) e não oferece suporte à sincronização para os locais de NAS (armazenamento anexado à rede).
Esse fato faz uma migração dos arquivos necessários e este artigo o orienta durante o planejamento e a execução de tal migração.

## <a name="migration-goals"></a>Metas de migração

O objetivo é mover os compartilhamentos que você tem em seu dispositivo NAS para um Windows Server. Em seguida, utilize Sincronização de Arquivos do Azure para uma implantação de nuvem híbrida. Essa migração precisa ser feita de forma a garantir a integridade dos dados de produção e da disponibilidade durante a migração. O segundo requer um mínimo de tempo de inatividade, para que ele possa se ajustar ou ter apenas um pouco mais de uma janela de manutenção regular.

## <a name="migration-overview"></a>Visão geral da migração

O processo de migração consiste em várias fases. Você precisará implantar contas de armazenamento do Azure e compartilhamentos de arquivos, implantar um Windows Server local, Configurar Sincronização de Arquivos do Azure, migrar usando o RoboCopy e, por fim, fazer o recorte. As seções a seguir descrevem as fases do processo de migração em detalhes.

> [!TIP]
> Se você estiver retornando a este artigo, use a navegação no lado direito para ir para a fase de migração onde você parou.

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>Fase 1: identificar Quantos compartilhamentos de arquivos do Azure você precisa

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-deploy-azure-storage-resources"></a>Fase 2: implantar recursos de armazenamento do Azure

Nesta fase, consulte a tabela de mapeamento da fase 1 e use-a para provisionar o número correto de contas de armazenamento do Azure e compartilhamentos de arquivos dentro deles.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-3-determine-how-many-azure-databox-appliances-you-need"></a>Fase 3: determinar quantos dispositivos de data box do Azure você precisa

Iniciar esta etapa apenas, quando você tiver concluído a fase anterior. Os recursos de armazenamento do Azure (contas de armazenamento e compartilhamentos de arquivos) devem ser criados neste momento. Durante o seu pedido de data Box, você precisa especificar em quais contas de armazenamento o data Box está movendo dados.

Nesta fase, você precisa mapear os resultados do plano de migração da fase anterior para os limites das opções de data Box disponíveis. Essas considerações o ajudarão a fazer um plano para quais opções de data Box você deve escolher e quantos deles serão necessários para mover seus compartilhamentos de NAS para compartilhamentos de arquivos do Azure.

Para determinar quantos dispositivos de qual tipo você precisa, considere estes limites importantes:

* Qualquer data box do Azure pode mover dados para até 10 contas de armazenamento. 
* Cada opção de data Box vem em sua própria capacidade utilizável. Consulte [Data Box Options](#databox-options).

Consulte seu plano de migração para o número de contas de armazenamento que você decidiu criar e os compartilhamentos em cada uma delas. Em seguida, examine o tamanho de cada um dos compartilhamentos em seu NAS. A combinação dessas informações permitirá que você otimize e decida qual dispositivo deve enviar dados para quais contas de armazenamento. Você pode fazer com que dois dispositivos data Box movam arquivos para a mesma conta de armazenamento, mas não divida o conteúdo de um único compartilhamento de arquivo entre 2 caixas de entrada.

### <a name="databox-options"></a>Opções de data Box

Para uma migração padrão, uma ou uma combinação dessas três opções de data Box deve ser escolhida: 

* Discos Data Box a Microsoft enviará um e até cinco discos SSD com uma capacidade de 8 TiB cada, para um total máximo de 40 TiB. A capacidade utilizável é de cerca de 20% menos, devido à sobrecarga do sistema de arquivos e criptografia. Para obter mais informações, consulte a [documentação de discos do data Box](../../databox/data-box-disk-overview.md).
* Data Box essa é a opção mais comum. Um dispositivo data Box resistente, que funciona de forma semelhante a um NAS, será enviado para você. Ele tem uma capacidade utilizável de 80 TiB. Para obter mais informações, consulte a [documentação do data Box](../../databox/data-box-overview.md).
* Data Box pesado esta opção apresenta um dispositivo data Box resistente em rodas, que funciona de forma semelhante a um NAS, com uma capacidade de 1 PiB. A capacidade utilizável é de cerca de 20% menos, devido à sobrecarga do sistema de arquivos e criptografia. Para obter mais informações, consulte a [documentação pesada do data Box](../../databox/data-box-heavy-overview.md).

## <a name="phase-4-provision-a-suitable-windows-server-on-premises"></a>Fase 4: provisionar um Windows Server local adequado

Enquanto você aguarda até que seus data box do Azure cheguem, você já pode começar a revisar as necessidades de um ou mais servidores Windows que você usará com Sincronização de Arquivos do Azure.

* Crie um Windows Server 2019 – no mínimo 2012R2-como uma máquina virtual ou um servidor físico. Também há suporte para um cluster de failover do Windows Server.
* Provisione ou adicione armazenamento de conexão direta (DAS em comparação com o NAS, para o qual não há suporte).

A configuração de recurso (computação e RAM) do Windows Server que você implanta depende principalmente do número de itens (arquivos e pastas) que serão sincronizados. Uma configuração de desempenho mais alto é recomendada se você tiver alguma preocupação.

[Saiba como dimensionar um Windows Server com base no número de itens (arquivos e pastas) que você precisa sincronizar.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> O artigo vinculado anteriormente apresenta uma tabela com um intervalo para a memória do servidor (RAM). Você pode orientar em direção ao número menor para o servidor, mas esperar que a sincronização inicial possa levar muito mais tempo.

## <a name="phase-5-copy-files-onto-your-databox"></a>Fase 5: copiar arquivos para seu data Box

Quando seu data Box chegar, você precisará configurar seu data Box na linha de visão para seu dispositivo NAS. Siga a documentação de instalação para o tipo de data box que você solicitou.

* [Configurar o Data Box](../../databox/data-box-quickstart-portal.md)
* [Configurar o Data Box Disk](../../databox/data-box-disk-quickstart-portal.md)
* [Configurar o Data Box Heavy](../../databox/data-box-heavy-quickstart-portal.md)

Dependendo do tipo de data Box, talvez data Box ferramentas de cópia disponíveis para você. Neste ponto, eles não são recomendados para migrações para compartilhamentos de arquivos do Azure, pois não copiam seus arquivos com total fidelidade para o data box. Use o RoboCopy em vez disso.

Quando seu data Box chegar, ele terá compartilhamentos de SMB pré-configurados disponíveis para cada conta de armazenamento especificada no momento da sua ordenação.

* Se os arquivos entrarem em um compartilhamento de arquivos premium do Azure, haverá um compartilhamento SMB por conta de armazenamento de "armazenamento de arquivos" Premium.
* Se os arquivos entrarem em uma conta de armazenamento padrão, haverá três compartilhamentos SMB por conta de armazenamento Standard (GPv1 e GPv2). Somente os compartilhamentos de arquivos que terminam com `_AzFiles` são relevantes para sua migração. Ignorar quaisquer compartilhamentos de blob de bloco e de página.

Siga as etapas na documentação do Azure data Box:

1. [Conectar-se à caixa de dados](../../databox/data-box-deploy-copy-data.md)
1. Copiar dados para caixa de dados
1. [Preparar seu data box para a partida para o Azure](../../databox/data-box-deploy-picked-up.md)

A documentação vinculada do data Box especifica um comando RoboCopy. No entanto, o comando não é adequado para preservar a fidelidade completa de arquivo e pasta. Use este comando em vez disso:

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]


## <a name="phase-6-deploy-the-azure-file-sync-cloud-resource"></a>Fase 6: implantar o Sincronização de Arquivos do Azure recurso de nuvem

Antes de continuar com este guia, aguarde até que todos os arquivos tenham chegado nos compartilhamentos de arquivos do Azure corretos. O processo de envio e ingestão de dados de data Box levará tempo.

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-7-deploy-the-azure-file-sync-agent"></a>Fase 7: implantar o agente de Sincronização de Arquivos do Azure

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-8-configure-azure-file-sync-on-the-windows-server"></a>Fase 8: Configurar Sincronização de Arquivos do Azure no Windows Server

Seu Windows Server local registrado deve estar pronto e conectado à Internet para esse processo.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

Ative o recurso de disposição em camadas da nuvem e selecione "somente namespace" na seção de download inicial.

> [!IMPORTANT]
> A camada de nuvem é o recurso AFS que permite que o servidor local tenha menos capacidade de armazenamento do que o armazenado na nuvem, mas que tenha o namespace completo disponível. Dados interessantes localmente também são armazenados em cache localmente para desempenho rápido de acesso. A camada de nuvem é um recurso opcional por Sincronização de Arquivos do Azure "ponto de extremidade do servidor". Você precisará usar esse recurso se não tiver capacidade de disco local suficiente no Windows Server para manter todos os dados da nuvem e se desejar evitar o download de todos os dados da nuvem!

Repita as etapas da criação do grupo de sincronização e adição da pasta de servidor correspondente como um ponto de extremidade do servidor para todos os compartilhamentos de arquivos/locais de servidor do Azure que precisam ser configurados para sincronização. Aguarde até que a sincronização do namespace seja concluída. A seção a seguir detalhará como você pode garantir isso.

> [!NOTE]
> Após a criação de um ponto de extremidade do servidor, a sincronização está funcionando. No entanto, a sincronização precisa enumerar (descobrir) os arquivos e as pastas que você moveu via Data box para o compartilhamento de arquivos do Azure. Dependendo do tamanho do namespace, isso pode levar um tempo significativo antes que o namespace da nuvem comece a aparecer no servidor.

## <a name="phase-9-wait-for-the-namespace-to-fully-appear-on-the-server"></a>Fase 9: Aguarde até que o namespace apareça totalmente no servidor

É imperativo que você aguarde com as próximas etapas da sua migração que o servidor baixou completamente o namespace do compartilhamento de nuvem. Se você começar a mover os arquivos muito cedo para o servidor, poderá arriscar carregamentos desnecessários e até mesmo conflitos de sincronização de arquivos.

Para saber se o servidor concluiu a sincronização de download inicial, abra Visualizador de Eventos em seu servidor de sincronização do Windows Server e use o log de eventos Sincronização de Arquivos do Azure telemetria.
O log de eventos de telemetria está localizado em Visualizador de Eventos em aplicativos e Services\Microsoft\FileSync\Agent.

Pesquise o evento 9102 mais recente. A ID do evento 9102 é registrada quando uma sessão de sincronização é concluída. No texto do evento, há um campo para a direção de sincronização de download. ( `HResult` precisa ser zero e arquivos baixados também).
 
Você deseja ver dois eventos consecutivos desse tipo e conteúdo para informar que o servidor concluiu o download do namespace. OK, se houver eventos diferentes sendo acionados entre 2 9102 eventos.

## <a name="phase-10-catch-up-robocopy-from-your-nas"></a>Fase 10: acompanhar o RoboCopy de seu NAS

Depois que o servidor concluir a sincronização inicial de todo o namespace do compartilhamento de nuvem, você poderá continuar com esta etapa. É imperativo que esta etapa esteja concluída, antes de continuar com esta etapa. Verifique a seção anterior para obter detalhes.

Nesta etapa, você executará trabalhos do RoboCopy para acompanhar seus compartilhamentos de nuvem com as alterações mais recentes em seu NAS desde o momento em que você bifurcau seus compartilhamentos para o data box.
Esse RoboCopy de atualização pode terminar rapidamente ou demorar, dependendo da quantidade de variação que aconteceu em seus compartilhamentos de NAS.

> [!WARNING]
> Devido a um comportamento de RoboCopy regressivo no Windows Server 2019, a opção/MIR do RoboCopy não é compatível com um diretório de destino em camadas. Você não deve usar o Windows Server 2019 ou o cliente do Windows 10 para esta fase da migração. Use o RoboCopy em um Windows Server 2016 intermediário.

A abordagem de migração básica é um RoboCopy de seu dispositivo NAS para o Windows Server e Sincronização de Arquivos do Azure aos compartilhamentos de arquivos do Azure.

Execute a primeira cópia local em sua pasta de destino do Windows Server:

1. Identifique o primeiro local em seu dispositivo NAS.
1. Identifique a pasta correspondente no Windows Server, que já tem Sincronização de Arquivos do Azure configurada nela.
1. Iniciar a cópia usando o RoboCopy

O comando RoboCopy a seguir copiará apenas as diferenças (arquivos e pastas atualizados) do armazenamento NAS para a pasta de destino do Windows Server. Em seguida, o Windows Server irá sincronizá-los para os compartilhamentos de arquivos do Azure. 

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

Se você provisionou menos armazenamento no Windows Server do que seus arquivos ocupam no dispositivo NAS, você configurou a camada de nuvem. Como o volume do Windows Server local fica cheio, a disposição em [camadas da nuvem](storage-sync-cloud-tiering-overview.md) será iniciada e os arquivos de camada que já foram sincronizados com êxito. A disposição em camadas da nuvem gerará espaço suficiente para continuar a cópia do dispositivo NAS. As verificações de camadas de nuvem uma vez por hora para ver o que foi sincronizado e liberar espaço em disco para alcançar o espaço livre do volume de 99%.
É possível que o RoboCopy precise mover vários arquivos, mais do que você tem armazenamento local para o no Windows Server. Em média, você pode esperar que o RoboCopy se mova muito mais rápido do que Sincronização de Arquivos do Azure pode carregar seus arquivos e agrupá-los em seu volume local. O RoboCopy falhará. É recomendável que você trabalhe nos compartilhamentos em uma sequência que impede isso. Por exemplo, não iniciar trabalhos do RoboCopy para todos os compartilhamentos ao mesmo tempo ou mover somente compartilhamentos que caibam na quantidade atual de espaço livre no Windows Server, para mencionar alguns. A boa notícia é que a opção/MIR moverá apenas deltas e, uma vez que um Delta foi movido, um trabalho reiniciado não precisará mover esse arquivo novamente.

### <a name="user-cut-over"></a>Recorte do usuário

Quando você executa o comando RoboCopy pela primeira vez, seus usuários e aplicativos ainda estão acessando arquivos no NAS e potencialmente os alteram. É possível que o RoboCopy tenha processado um diretório, passe para o próximo e, em seguida, um usuário no local de origem (NAS) adicione, altere ou exclua um arquivo que agora não será processado nesta execução atual do RoboCopy. O comportamento é esperado.

A primeira execução é sobre a movimentação da massa dos dados com rotatividade para o Windows Server e para a nuvem por meio de Sincronização de Arquivos do Azure. Essa primeira cópia pode levar muito tempo, dependendo de:

* a largura de banda de upload
* a velocidade da rede local e o número de quão otimizado o número de threads RoboCopy corresponde a ele
* o número de itens (arquivos e pastas) que precisam ser processados pelo RoboCopy e Sincronização de Arquivos do Azure

Depois que a execução inicial for concluída, execute o comando novamente.

Uma segunda vez que você executa o RoboCopy para o mesmo compartilhamento, ele será concluído mais rapidamente, pois ele só precisa transportar alterações ocorridas desde a última execução. Você pode executar trabalhos repetidos para o mesmo compartilhamento.

Ao considerar o tempo de inatividade aceitável, você precisa remover o acesso do usuário aos seus compartilhamentos baseados em NAS. Você pode fazer isso por qualquer etapa que impeça os usuários de alterar o conteúdo e a estrutura de arquivos e pastas. Um exemplo é apontar seu DFS-Namespace para um local não existente ou alterar as ACLs raiz no compartilhamento.

Executar uma última rodada do RoboCopy. Ele escolherá todas as alterações, que podem ter sido perdidas.
Quanto tempo leva essa etapa final, depende da velocidade da verificação do RoboCopy. Você pode estimar o tempo (que é igual ao seu tempo de inatividade) medindo quanto tempo a execução anterior levou.

Crie um compartilhamento na pasta do Windows Server e, possivelmente, ajuste sua implantação do DFS-N para apontar para ele. Certifique-se de definir as mesmas permissões de nível de compartilhamento que em seu compartilhamento SMB de NAS. Se você tivesse um NAS de classe empresarial ingressado no domínio, os SIDs do usuário corresponderão automaticamente à medida que os usuários existirem em Active Directory e o RoboCopy copia arquivos e metadados com total fidelidade. Se você tiver usado usuários locais em seu NAS, precisará recriar esses usuários como usuários locais do Windows Server e mapear os SIDs RoboCopy existentes movidos para o Windows Server para os SIDs dos novos usuários locais do Windows Server.

Você concluiu a migração de um compartilhamento/grupo de compartilhamentos em um volume ou raiz comum. (Dependendo do mapeamento da fase 1)

Você pode tentar executar algumas dessas cópias em paralelo. É recomendável processar o escopo de um compartilhamento de arquivos do Azure por vez.

## <a name="troubleshoot"></a>Solucionar problemas

O problema mais provável que você pode encontrar é que o comando RoboCopy falha com *"volume cheio"* no lado do Windows Server. A camada de nuvem age uma vez a cada hora para evacuar o conteúdo do disco local do Windows Server, que foi sincronizado. Seu objetivo é atingir o espaço livre de 99% no volume.

Deixe o progresso da sincronização e a camada da nuvem liberar espaço em disco. Você pode observar isso no explorador de arquivos no seu Windows Server.

Quando o seu Windows Server tiver capacidade disponível suficiente, a reexecução do comando resolverá o problema. Nada é interrompido quando você entra nessa situação e pode avançar com confiança. A inconveniência de executar o comando novamente é a única consequência.

Verifique o link na seção a seguir para solucionar problemas de Sincronização de Arquivos do Azure.

## <a name="next-steps"></a>Próximas etapas

Há mais a descobrir sobre compartilhamentos de arquivos do Azure e Sincronização de Arquivos do Azure. Os artigos a seguir ajudam a entender opções avançadas, práticas recomendadas e também contêm ajuda para solução de problemas. Estes artigos se vinculam à [documentação do compartilhamento de arquivos do Azure](storage-files-introduction.md) , conforme apropriado.

* [Visão geral da migração](storage-files-migration-overview.md)
* [Visão geral de AFS](./storage-sync-files-planning.md)
* [Guia de implantação AFS](./storage-how-to-create-file-share.md)
* [Solução de problemas AFS](storage-sync-files-troubleshoot.md)
