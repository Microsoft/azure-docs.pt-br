---
title: Migração do StorSimple 1200 para a Sincronização de Arquivos do Azure
description: Saiba como migrar um dispositivo virtual da série StorSimple 1200 para Sincronização de Arquivos do Azure.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 8562d63bf227fff665c70674c7fe66922bce9992
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98882273"
---
# <a name="storsimple-1200-migration-to-azure-file-sync"></a>Migração do StorSimple 1200 para a Sincronização de Arquivos do Azure

O StorSimple 1200 Series é uma solução de virtualização que é executada em um data center local. É possível migrar os dados desse dispositivo para um ambiente Sincronização de Arquivos do Azure. Sincronização de Arquivos do Azure é o serviço do Azure de longo prazo padrão e estratégico para o qual os dispositivos StorSimple podem ser migrados.

A série StorSimple 1200 atingirá o [fim da vida útil](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%201200%20Series) em 2022 de dezembro.  É importante começar a planejar sua migração assim que possível. Este artigo fornece as etapas de conhecimento e migrações em segundo plano necessárias para uma migração bem-sucedida para o Sincronização de Arquivos do Azure. 

## <a name="azure-file-sync"></a>Sincronização de Arquivos do Azure

> [!IMPORTANT]
> A Microsoft está comprometida em auxiliar os clientes em sua migração. Email AzureFilesMigration@microsoft . com para um plano de migração personalizado, bem como assistência durante a migração.

Sincronização de Arquivos do Azure é um serviço de nuvem da Microsoft, com base em dois componentes principais:

* Sincronização de arquivos e camadas de nuvem.
* Compartilhamentos de arquivos como armazenamento nativo no Azure, que podem ser acessados por meio de vários protocolos, como SMB e REST de arquivo. Um compartilhamento de arquivos do Azure é comparável a um compartilhamento de arquivos em um Windows Server, que você pode montar nativamente como uma unidade de rede. Ele dá suporte a aspectos de fidelidade de arquivo importantes, como atributos, permissões e carimbos de data/hora. Ao contrário do StorSimple, nenhum aplicativo/serviço é necessário para interpretar os arquivos e as pastas armazenados na nuvem. A abordagem ideal e mais flexível para armazenar dados de servidor de arquivos de uso geral, bem como alguns dados de aplicativo, na nuvem.

Este artigo se concentra nas etapas de migração. Se antes de migrar você gostaria de saber mais sobre Sincronização de Arquivos do Azure, recomendamos os seguintes artigos:

* [Sincronização de Arquivos do Azure-visão geral](./storage-sync-files-planning.md "Visão geral")
* [Sincronização de Arquivos do Azure-guia de implantação](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>Metas de migração

O objetivo é garantir a integridade dos dados de produção, bem como garantir a disponibilidade. O segundo requer um mínimo de tempo de inatividade, para que ele possa se ajustar ou ter apenas um pouco mais de uma janela de manutenção regular.

## <a name="storsimple-1200-migration-path-to-azure-file-sync"></a>Caminho de migração do StorSimple 1200 para Sincronização de Arquivos do Azure

Um servidor Windows local é necessário para executar um agente de Sincronização de Arquivos do Azure. O Windows Server pode ser, no mínimo, um servidor 2012R2, mas o ideal é o Windows Server 2019.

Há inúmeros caminhos de migração alternativos e criaria um artigo muito longo para documentar todos eles e ilustrar por que eles têm risco ou desvantagens na rota que recomendamos como prática recomendada neste artigo.

:::image type="content" source="media/storage-files-migration-storsimple-shared/storsimple-1200-migration-overview.png" alt-text="Visão geral da rota de migração das etapas abaixo neste artigo.":::

A imagem anterior descreve as etapas que correspondem às seções neste artigo.

### <a name="step-1-provision-your-on-premises-windows-server-and-storage"></a>Etapa 1: provisionar o Windows Server e o armazenamento no local

1. Crie um Windows Server 2019 – no mínimo 2012R2-como uma máquina virtual ou um servidor físico. Também há suporte para um cluster de failover do Windows Server.
2. Provisione ou adicione armazenamento de conexão direta (DAS em comparação com o NAS, para o qual não há suporte). O tamanho do armazenamento do Windows Server deve ser igual ou maior que o tamanho da capacidade disponível do seu dispositivo virtual StorSimple 1200.

### <a name="step-2-configure-your-windows-server-storage"></a>Etapa 2: configurar o armazenamento do Windows Server

Nesta etapa, você mapeia sua estrutura de armazenamento do StorSimple (volumes e compartilhamentos) para sua estrutura de armazenamento do Windows Server.
Se você planeja fazer alterações em sua estrutura de armazenamento, o que significa o número de volumes, a associação de pastas de dados a volumes ou a estrutura de subpastas acima ou abaixo de seus compartilhamentos SMB/NFS atuais, agora é o momento em que essas alterações serão levadas em consideração.
Alterar a estrutura de arquivos e pastas depois que Sincronização de Arquivos do Azure é configurado, é complicado e deve ser evitado.
Este artigo pressupõe que você está mapeando 1:1, portanto, você deve levar em consideração as alterações de mapeamento ao seguir as etapas neste artigo.

* Nenhum dos dados de produção deve terminar no volume do Windows Server System. Não há suporte para camadas de nuvem em volumes do sistema. No entanto, esse recurso é necessário para a migração, bem como para operações contínuas como uma substituição do StorSimple.
* Provisione o mesmo número de volumes no seu Windows Server que você tem em seu dispositivo virtual StorSimple 1200.
* Configure quaisquer funções, recursos e configurações do Windows Server necessárias. Recomendamos que você opte pelas atualizações do Windows Server para manter seu so seguro e atualizado. Da mesma forma, recomendamos optar por Microsoft Update manter os aplicativos da Microsoft atualizados, incluindo o agente de Sincronização de Arquivos do Azure.
* Não configure pastas ou compartilhamentos antes de ler as etapas a seguir.

### <a name="step-3-deploy-the-first-azure-file-sync-cloud-resource"></a>Etapa 3: implantar a primeira Sincronização de Arquivos do Azure recurso de nuvem

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

### <a name="step-4-match-your-local-volume-and-folder-structure-to-azure-file-sync-and-azure-file-share-resources"></a>Etapa 4: corresponder o volume local e a estrutura de pastas para Sincronização de Arquivos do Azure e recursos de compartilhamento de arquivos do Azure

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="step-5-provision-azure-file-shares"></a>Etapa 5: provisionar compartilhamentos de arquivos do Azure

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

### <a name="step-6-configure-windows-server-target-folders"></a>Etapa 6: configurar pastas de destino do Windows Server

Nas etapas anteriores, você considerou todos os aspectos que determinarão os componentes de suas topologias de sincronização. Agora é hora, preparar o servidor para receber arquivos para carregamento.

Crie **todas as** pastas, que sincronizarão cada uma com seu próprio compartilhamento de arquivos do Azure.
É importante que você siga a estrutura de pastas que você documentou anteriormente. Se, por exemplo, você decidiu sincronizar vários compartilhamentos SMB locais juntos em um único compartilhamento de arquivos do Azure, precisará colocá-los em uma pasta raiz comum no volume. Crie essa pasta raiz de destino no volume agora.

O número de compartilhamentos de arquivos do Azure que você provisionou deve corresponder ao número de pastas que você criou nesta etapa + o número de volumes que você sincronizará no nível raiz.

### <a name="step-7-deploy-the-azure-file-sync-agent"></a>Etapa 7: implantar o agente de Sincronização de Arquivos do Azure

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="step-8-configure-sync"></a>Etapa 8: configurar a sincronização

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **Certifique-se de ativar a disposição em camadas de nuvem!** Isso será necessário se o servidor local não tiver espaço suficiente para armazenar o tamanho total dos dados no armazenamento em nuvem do StorSimple. Defina a política de camadas, temporariamente para a migração, para 99% de espaço livre no volume.

Repita as etapas da criação do grupo de sincronização e adição da pasta de servidor correspondente como um ponto de extremidade do servidor para todos os compartilhamentos de arquivos/locais de servidor do Azure que precisam ser configurados para sincronização.

### <a name="step-9-copy-your-files"></a>Etapa 9: copiar os arquivos

A abordagem de migração básica é um RoboCopy de seu dispositivo virtual StorSimple para o Windows Server e Sincronização de Arquivos do Azure aos compartilhamentos de arquivos do Azure.

Execute a primeira cópia local em sua pasta de destino do Windows Server:

* Identifique o primeiro local em seu dispositivo virtual StorSimple.
* Identifique a pasta correspondente no Windows Server, que já tem Sincronização de Arquivos do Azure configurada nela.
* Iniciar a cópia usando o RoboCopy

O comando RoboCopy a seguir irá recuperar os arquivos do armazenamento do Azure do StorSimple para o StorSimple local e, em seguida, movê-los para a pasta de destino do Windows Server. O Windows Server irá sincronizá-lo para os compartilhamentos de arquivos do Azure. Como o volume do Windows Server local fica cheio, a disposição em camadas da nuvem será iniciada e os arquivos de camada que já foram sincronizados com êxito. A camada de nuvem irá gerar espaço suficiente para continuar a cópia do dispositivo virtual StorSimple. As verificações de camadas de nuvem uma vez por hora para ver o que foi sincronizado e liberar espaço em disco para alcançar o espaço livre do volume de 99%.

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Plano de fundo:

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Permite que o RoboCopy seja executado em vários threads. O padrão é 8, o máximo é 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      Gera o status do arquivo de LOG como UNICODE (Substitui o log existente).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Saídas para a janela do console. Usado em conjunto com a saída para um arquivo de log.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Executa o RoboCopy no mesmo modo que um aplicativo de backup usaria. Ele permite que o RoboCopy mova arquivos aos quais o usuário atual não tem permissões.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Permite executar esse comando RoboCopy várias vezes, sequencialmente no mesmo destino/destino. Ele identifica o que foi copiado antes e o omite. Somente as alterações, adições e "*exclusões*" serão processadas, ocorridas desde a última execução. Se o comando não for executado antes, nada será omitido. Essa é uma opção excelente para os locais de origem que ainda são usados e alterados ativamente.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      fidelidade da cópia de arquivo (o padrão é/COPY: DAT), sinalizadores de cópia: D = dados, A = atributos, T = carimbos de data/hora, S = segurança = ACLs de NTFS, O = informações de proprietário, U = informações de auditoria
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      Copie todas as informações do arquivo (equivalente a/COPY: DATSOU)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      fidelidade para a cópia de diretórios (o padrão é/DCOPY: DA), sinalizadores de cópia: D = data, A = atributos, T = carimbos de hora
   :::column-end:::
:::row-end:::

Quando você executa o comando RoboCopy pela primeira vez, os usuários e os aplicativos ainda estão acessando os arquivos e pastas do StorSimple e, potencialmente, alterá-lo. É possível que o RoboCopy tenha processado um diretório, passe para o próximo e, em seguida, um usuário no local de origem (StorSimple) adicione, altere ou exclua um arquivo que agora não será processado nesta execução atual do RoboCopy. Tudo bem.

A primeira execução é sobre a movimentação da massa dos dados de volta para o local, para o Windows Server e para o backup na nuvem por meio de Sincronização de Arquivos do Azure. Isso pode levar muito tempo, dependendo de:

* sua largura de banda de download
* a velocidade de recuperação do serviço de nuvem do StorSimple
* a largura de banda de upload
* o número de itens (arquivos e pastas) que precisam ser processados por qualquer serviço

Depois que a execução inicial for concluída, execute o comando novamente.

A segunda vez que terminará mais rapidamente, porque ele só precisa transportar alterações ocorridas desde a última execução. Essas alterações provavelmente são locais para o StorSimple, já que elas são recentes. Isso reduz ainda mais o tempo, pois a necessidade de recuperação da nuvem é reduzida. Durante essa segunda execução, ainda assim, novas alterações podem ser acumuladas.

Repita esse processo até que você esteja satisfeito com o tempo que leva para concluir é um inatividade aceitável.

Quando você considera o tempo de inatividade aceitável e está preparado para colocar o local do StorSimple offline, faça isso agora: por exemplo, remova o compartilhamento SMB para que nenhum usuário possa acessar a pasta ou execute qualquer outra etapa apropriada que impeça o conteúdo a ser alterado nessa pasta no StorSimple.

Executar uma última rodada do RoboCopy. Isso selecionará todas as alterações, que podem ter sido perdidas.
Quanto tempo leva essa etapa final, depende da velocidade da verificação do RoboCopy. Você pode estimar o tempo (que é igual ao seu tempo de inatividade) medindo quanto tempo a execução anterior levou.

Crie um compartilhamento na pasta do Windows Server e, possivelmente, ajuste sua implantação do DFS-N para apontar para ele. Certifique-se de definir as mesmas permissões de nível de compartilhamento que no compartilhamento SMB do StorSimple.

Você concluiu a migração de um compartilhamento/grupo de compartilhamentos em um volume ou raiz comum. (Dependendo do que você mapeou e decidiu que precisava entrar no mesmo compartilhamento de arquivos do Azure.)

Você pode tentar executar algumas dessas cópias em paralelo. É recomendável processar o escopo de um compartilhamento de arquivos do Azure por vez.

> [!WARNING]
> Depois de mover todos os dados do seu StorSimple para o Windows Server e sua migração estiver concluída: retorne a ***todos os***  grupos de sincronização na portal do Azure e ajuste o valor de porcentagem de espaço livre do volume de camadas da nuvem para algo mais adequado para a utilização do cache, digamos 20%. 

A política de espaço livre do volume de camadas de nuvem age em um nível de volume com potencialmente vários pontos de extremidade de servidor sincronizando a partir dele. Se você se esquecer de ajustar o espaço livre em um ponto de extremidade de servidor, a sincronização continuará a aplicar a regra mais restritiva e tentará manter 99% de espaço livre em disco, tornando o cache local não funcionando como você poderia esperar. A menos que seja o objetivo de ter apenas o namespace para um volume que contenha apenas dados de arquivamento raramente acessados.

## <a name="troubleshoot"></a>Solucionar problemas

O problema mais provável que você pode encontrar é que o comando RoboCopy falha com *"volume cheio"* no lado do Windows Server. Se esse for o caso, a velocidade de download provavelmente será melhor do que a velocidade de carregamento. A camada de nuvem age uma vez a cada hora para evacuar o conteúdo do disco local do Windows Server, que foi sincronizado.

Deixe o progresso da sincronização e a camada da nuvem liberar espaço em disco. Você pode observar isso no explorador de arquivos no seu Windows Server.

Quando o seu Windows Server tiver capacidade disponível suficiente, a reexecução do comando resolverá o problema. Nada é interrompido quando você entra nessa situação e pode avançar com confiança. A inconveniência de executar o comando novamente é a única consequência.

Você também pode encontrar outros problemas de Sincronização de Arquivos do Azure.
Como é improvável que possa ser, se isso acontecer, dê uma olhada no **LINK sincronização de arquivos do Azure guia de solução de problemas**.

## <a name="relevant-links"></a>Links relevantes

Conteúdo de migração:

* [Guia de migração da série StorSimple 8000](storage-files-migration-storsimple-8000.md)

Sincronização de Arquivos do Azure conteúdo:

* [Visão geral de AFS](./storage-sync-files-planning.md)
* [Guia de implantação AFS](./storage-how-to-create-file-share.md)
* [Solução de problemas AFS](storage-sync-files-troubleshoot.md)