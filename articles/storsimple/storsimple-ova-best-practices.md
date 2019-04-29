---
title: Práticas recomendadas para o StorSimple Virtual Array | Microsoft Docs
description: Descreve as práticas recomendadas para implantar e gerenciar o StorSimple Virtual Array.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 57ac6eeb-c47c-442d-a5f4-b360d81a76a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/08/2018
ms.author: alkohli
ms.openlocfilehash: b8e9f12a549f71971c2da3b9865f6a74dad58f61
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60630131"
---
# <a name="storsimple-virtual-array-best-practices"></a>Práticas recomendadas do StorSimple Virtual Array
## <a name="overview"></a>Visão geral
A Matriz Virtual Microsoft Azure StorSimple é uma solução de armazenamento integrado que gerencia as tarefas de armazenamento entre um dispositivo virtual local executando em um hipervisor e o armazenamento em nuvem do Microsoft Azure. O StorSimple Virtual Array é uma alternativa eficiente e econômica para a matriz física da série 8000. A matriz virtual pode ser executada em sua infraestrutura de hipervisor existente, oferece suporte ao iSCSI e aos protocolos SMB, além de ser adequada a cenários de filiais/escritórios remotos. Para saber mais sobre as soluções do StorSimple, vá para [Visão geral do Microsoft Azure StorSimple](https://www.microsoft.com/en-us/server-cloud/products/storsimple/overview.aspx).

Este artigo aborda as práticas recomendadas implementadas durante a configuração inicial, a implantação e o gerenciamento do StorSimple Virtual Array. Essas práticas recomendadas oferecem diretrizes validadas para a instalação e o gerenciamento da sua matriz virtual. Este artigo é destinado a administradores de TI que implantam e gerenciam as matrizes virtuais em seus datacenters.

É recomendável uma revisão periódica das práticas recomendadas para ajudar a garantir que seu dispositivo ainda esteja em conformidade quando forem feitas alterações no fluxo de instalação ou de operação. Se você encontrar problemas ao implementar essas práticas recomendadas em sua matriz virtual, [contate o Suporte da Microsoft](storsimple-virtual-array-log-support-ticket.md) para obter assistência.

## <a name="configuration-best-practices"></a>Práticas recomendadas de configuração
Essas práticas recomendadas abrangem as diretrizes que precisam ser seguidas durante a instalação inicial e a implantação das matrizes virtuais. As práticas recomendadas incluem aquelas relacionadas ao provisionamento da máquina virtual, às configurações da política de grupo, ao dimensionamento, à configuração da rede, à configuração de contas de armazenamento e à criação de compartilhamentos e volumes para a matriz virtual. 

### <a name="provisioning"></a>Provisionamento
O StorSimple Virtual Array é uma máquina virtual (VM) provisionada no hipervisor (Hyper-V ou VMware) do seu servidor host. Ao provisionar a máquina virtual, verifique se seu host é capaz de dedicar recursos suficientes. Para saber mais, vá para [requisitos mínimos de recurso](storsimple-virtual-array-deploy2-provision-hyperv.md#step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements) para provisionar uma matriz.

Implemente as seguintes práticas recomendadas ao provisionar a matriz virtual:

|  | Hyper-V | VMware |
| --- | --- | --- |
| **Tipo de máquina virtual** |**geração 2** a ser usada com o Windows Server 2012 ou posterior e uma imagem *.vhdx* . <br></br> **geração 1** a ser usada com um Windows Server 2008 ou posterior e uma imagem *.vhd* . |Utilize a máquina virtual versão 8 ao usar a imagem *.vmdk*. |
| **Tipo de memória** |Configurar como **memória estática**. <br></br> Não use a opção **memória dinâmica** . | |
| **Tipo de disco de dados** |Provisione como **expandindo dinamicamente**.<br></br> **tamanho fixo** leva muito tempo. <br></br> Não use a opção de **diferenciação** . |Use a opção **provisionamento dinâmico** . |
| **Modificação de disco de dados** |A expansão ou a redução não é permitida. Uma tentativa de fazer isso resulta na perda de todos os dados locais no dispositivo. |A expansão ou a redução não é permitida. Uma tentativa de fazer isso resulta na perda de todos os dados locais no dispositivo. |

### <a name="sizing"></a>Dimensionamento
Ao dimensionar seu StorSimple Virtual Array, considere os seguintes fatores:

* Reserva de local de volumes ou de compartilhamentos. Aproximadamente 12% do espaço é reservado na camada local para cada volume ou compartilhamento em camadas provisionado. Aproximadamente 10% do espaço também é reservado para um volume fixado localmente para o sistema de arquivos.
* Sobrecarga de instantâneo. Aproximadamente 15% do espaço na camada local é reservado para instantâneos.
* Necessidade de restaurações. Se a restauração for feita como uma nova operação, o dimensionamento deverá considerar o espaço necessário para a restauração. A restauração é feita em um compartilhamento ou um volume do mesmo tamanho.
* Parte do buffer deve ser alocada para qualquer crescimento inesperado.

Com base nos fatores citados, os requisitos de dimensionamento podem ser representados pela seguinte equação:

`Total usable local disk size = (Total provisioned locally pinned volume/share size including space for file system) + (Max (local reservation for a volume/share) for all tiered volumes/share) + (Local reservation for all tiered volumes/shares)`

`Data disk size = Total usable local disk size + Snapshot overhead + buffer for unexpected growth or new share or volume`

Os exemplos a seguir ilustram como você pode dimensionar uma matriz virtual com base em seus requisitos.

#### <a name="example-1"></a>Exemplo 1:
Em sua matriz virtual, você deseja ser capaz de

* provisionar um compartilhamento ou volume em camadas de 2 TB.
* provisionar um compartilhamento ou volume em camadas de 1 TB.
* provisionar um compartilhamento ou volume localmente fixado de 300 GB.

Para os volumes ou os compartilhamentos citados, vamos calcular os requisitos de espaço na camada local.

Primeiro, para cada volume/compartilhamento em camadas, a reserva local seria igual a 12% do tamanho do volume/compartilhamento. Para o volume/compartilhamento localmente fixados, a reserva local é 10% do tamanho do volume/compartilhamento localmente fixado (além do tamanho provisionado). Nesse exemplo, você precisa de

* reserva local de 240 GB (para um compartilhamento/volume em camadas de 2 TB)
* reserva local de 120 GB (para um compartilhamento/volume em camadas de 1 TB)
* 330 GB para compartilhamento ou volume localmente fixado (adicionando 10% de reserva local ao tamanho de 300 GB provisionado)

O espaço total necessário na camada local até o momento é: 240 GB + 120 GB + 330 GB = 690 GB.

Em segundo lugar, precisamos de pelo menos um espaço na camada local do tamanho da maior reserva única. Esse valor extra será usado caso você precise restaurar de um instantâneo de nuvem. Neste exemplo, a maior reserva local é 330 GB (incluindo a reserva para o sistema de arquivos) e, portanto, você adicionaria isso aos 690 GB: 690 GB + 330 GB = 1020 GB.
Se executamos as restaurações adicionais subsequentes, sempre será possível liberar o espaço da operação de restauração anterior.

Em terceiro lugar, precisamos de 15% do espaço total local até o momento para armazenar os instantâneos locais, de modo que apenas 85% disso esteja disponível. Neste exemplo, isso seria em torno de 1020 GB = 0,85&ast;TB de disco de dados provisionado. Portanto, o disco de dados provisionado seria (1020&ast;(1/0,85))= 1200 GB = 1,20 TB ~ 1,25 TB (arredondamento para o quartil mais próximo)

Incluindo o crescimento inesperado e novas restaurações, você deverá provisionar um disco local de aproximadamente 1,25 a 1,5 TB.

> [!NOTE]
> Também é recomendável que o disco local seja escassamente provisionado. Isso porque o espaço de restauração será necessário apenas quando você quiser restaurar dados com mais de cinco dias. A recuperação no nível do item permite que você restaure os dados dos últimos cinco dias sem exigir espaço extra para a restauração.


#### <a name="example-2"></a>Exemplo 2:
Em sua matriz virtual, você deseja ser capaz de

* provisionar um volume em camadas de 2 TB
* provisionar um volume localmente fixado de 300 GB

Com base em 12% de reserva do espaço local para volumes/compartilhamentos em camadas e 10% para volumes/compartilhamentos fixados localmente, precisamos de

* uma reserva local de 240 GB (para um compartilhamento/volume em camadas de 2 TB)
* 330 GB para compartilhamento ou volume localmente fixado (adicionando 10% de reserva local ao espaço provisionado de 300 GB)

Espaço total necessário na camada local é: 240 GB + 330 GB = 570 GB

O espaço local mínimo necessário para restauração é de 330 GB.

15% do disco total é usado para armazenar instantâneos, de forma que somente 0,85 esteja disponível. Portanto, o tamanho do disco é (900&ast;(1/0,85)) = 1,06 TB ~ 1,25 TB (arredondamento para o quartil mais próximo)

Incluindo qualquer crescimento inesperado, você poderá provisionar um disco local de 1,25-1,5 TB.

### <a name="group-policy"></a>Política de grupo
A Política de Grupo é uma infraestrutura que permite a você implementar configurações específicas para usuários e computadores. As configurações de Política de Grupo estão contidas em GPOs (objetos de Política de Grupo), vinculados aos seguintes contêineres do AD DS (Serviços de Domínio do Active Directory): sites, domínios ou UOs (unidades organizacionais). 

Se a matriz virtual estiver associada a um domínio, os GPOs poderão ser aplicados a ela. Esses GPOs podem instalar aplicativos como um software antivírus que possa afetar negativamente a operação do StorSimple Virtual Array.

Portanto, recomendamos que você:

* Certifique-se de que a matriz virtual esteja em sua própria OU (unidade organizacional) do Active Directory.
* Certifique-se de que nenhum GPO (objeto de política de grupo) seja aplicado à matriz virtual. Você pode bloquear a herança para garantir que a matriz virtual (nó filho) não herde automaticamente qualquer GPO do pai. Para saber mais, acesse [bloquear herança](https://technet.microsoft.com/library/cc731076.aspx).

### <a name="networking"></a>Rede
A configuração de rede para sua matriz virtual é feita por meio da interface do usuário da Web local. Uma interface de rede virtual é habilitada por meio do hipervisor em que a matriz virtual é provisionada. Use a página [Configurações de Rede](storsimple-virtual-array-deploy3-fs-setup.md) para configurar o endereço IP da interface de rede virtual, a sub-rede e o gateway.  Você também pode configurar o servidor DNS primário e secundário, as configurações de hora e as configurações de proxy opcionais para o seu dispositivo. A maior parte da configuração de rede é uma configuração única. Examine os [requisitos de rede do StorSimple](storsimple-ova-system-requirements.md#networking-requirements) antes de implantar a matriz virtual.

Ao implantar sua matriz virtual, recomendamos que você siga estas práticas recomendadas:

* Certifique-se de que a rede na qual a matriz virtual está implantada sempre tenha a capacidade de dedicar uma largura de banda de Internet de 5 Mbps (ou mais).
  
  * A largura de banda da Internet necessária varia de acordo com as características da carga de trabalho e com a taxa de alteração de dados.
  * A alteração de dados que pode ser tratada é diretamente proporcional à sua largura de banda da Internet. Como um exemplo, ao fazer um backup, uma largura de banda de 5 Mbps pode acomodar uma alteração de dados de cerca de 18 GB em 8 horas. Com quatro vezes mais largura de banda (20 Mbps), você poderá lidar com uma alteração de dados quatro vezes maior (72 GB).
* Verifique se a conectividade com a Internet está sempre disponível. As conexões da Internet esporádicas ou não confiáveis com os dispositivos podem resultar em perda de acesso aos dados na nuvem e podem resultar em uma configuração sem suporte.
* Se você planeja implantar o dispositivo como um servidor iSCSI:
  
  * É recomendável desabilitar a opção **Obter endereço IP automaticamente** (DHCP).
  * Configure endereços IP estáticos. Você deve configurar um servidor DNS principal e um secundário.
  * Se você estiver definindo várias interfaces de rede na matriz virtual, apenas a primeira interface de rede (por padrão, essa interface é **Ethernet**) poderá acessar a nuvem. Para controlar o tipo de tráfego, você poderá criar várias interfaces de rede virtual na matriz virtual (configurada como um servidor iSCSI) e conectá-las a sub-redes diferentes.
* Para limitar somente a largura de banda da nuvem (usada pela matriz virtual), configure a limitação no roteador ou no firewall. Se você definir a limitação em seu hipervisor, ele limitará todos os protocolos, incluindo iSCSI e SMB, em vez de apenas a largura de banda de nuvem.
* Verifique se a sincronização de hora para hipervisores está ativada. Se estiver usando o Hyper-V, escolha a matriz virtual no Gerenciador do Hyper-V, vá para **Configurações &gt; Serviços de Integração** e verifique se a **Sincronização de data/hora** está marcada.

### <a name="storage-accounts"></a>Contas de armazenamento
O StorSimple Virtual Array pode ser associado a uma única conta de armazenamento. Essa conta de armazenamento pode ser uma conta de armazenamento gerada automaticamente, uma conta na mesma assinatura que o serviço ou uma conta de armazenamento relacionada a outra assinatura. Para saber mais, confira como [gerenciar contas de armazenamento para sua matriz virtual](storsimple-virtual-array-manage-storage-accounts.md).

Use as seguintes recomendações para contas de armazenamento associadas à sua matriz virtual.

* Ao vincular várias matrizes virtuais a uma única conta de armazenamento, inclua a capacidade máxima (64 TB) em uma matriz virtual e o tamanho máximo (500 TB) de uma conta de armazenamento. Isso limita o número de matrizes virtuais de tamanho máximo que podem ser associadas à conta de armazenamento a cerca de 7.
* Ao criar uma nova conta de armazenamento
  
  * É recomendável que você a crie na região mais próxima ao escritório remoto/filial onde o StorSimple Virtual Array foi implantado para minimizar as latências.
  * Tenha em mente que você não pode mover uma conta de armazenamento entre regiões diferentes. Também é possível mover um serviço entre assinaturas.
  * Use uma conta de armazenamento que implemente a redundância entre os data centers. O GRS (Armazenamento com Redundância Geográfica), o ZRS (Armazenamento com Redundância Local) e o LRS (Armazenamento Localmente Redundante) têm suporte para serem usados com a matriz virtual. Para saber mais sobre os diferentes tipos de contas de armazenamento, vá para [replicação de armazenamento do Azure](../storage/common/storage-redundancy.md).

### <a name="shares-and-volumes"></a>Compartilhamentos e volumes
Para seu StorSimple Virtual Array, você pode provisionar compartilhamentos quando ele estiver configurado como um servidor de arquivos e volumes como um servidor iSCSI. As práticas recomendadas para a criação de compartilhamentos e volumes estão relacionadas ao tamanho e ao tipo configurados.

#### <a name="volumeshare-size"></a>Tamanho de volume/compartilhamento
Em sua matriz virtual, você pode provisionar compartilhamentos quando ele estiver configurado como um servidor de arquivos e volumes quando ele estiver configurado como um servidor iSCSI. As práticas recomendadas para a criação de compartilhamentos e volumes estão relacionadas ao tamanho e ao tipo configurados. 

Tenha em mente as seguintes práticas recomendadas ao provisionar compartilhamentos ou volumes em seu dispositivo virtual.

* Os tamanhos de arquivo relativos ao tamanho provisionado de um compartilhamento em camadas pode afetar o desempenho em camadas. Trabalhar com arquivos grandes pode resultar em uma divisão lenta em camadas. Ao trabalhar com arquivos grandes, recomendamos que o maior arquivo seja inferior a 3% do tamanho do compartilhamento.
* Um máximo de 16 volumes/compartilhamentos pode ser criado na matriz virtual. Para obter os limites de tamanho dos volumes/compartilhamentos localmente fixados e em camadas, sempre consulte [Limites da Matriz Virtual StorSimple](storsimple-ova-limits.md).
* Ao criar um volume, inclua o consumo de dados esperado, bem como o crescimento futuro. O volume não pode ser expandido posteriormente.
* Assim que o volume tiver sido criado, não será possível reduzir o tamanho do volume no StorSimple.
* Ao gravar em um volume em camadas no StorSimple, quando os dados do volume atingirem um certo limite (em relação ao espaço local reservado para o volume), a E/S será restringida. Continuar gravando nesse volume reduz significativamente a E/S. Embora seja possível gravar em um volume em camadas além da sua capacidade provisionada (nós não impedimos ativamente o usuário de gravar além da capacidade provisionada), você verá uma notificação de alerta informando que há um excesso de assinaturas. Quando receber o alerta, é fundamental que você tome medidas corretivas, como excluir dados do volume (atualmente, não há suporte para a expansão do volume).
* Para casos de uso da recuperação de desastre, como o número de compartilhamentos/volumes permitidos é 16 e o número máximo de compartilhamentos/volumes que podem ser processados em paralelo também é 16, o número de compartilhamentos/volumes não influenciará o RPO e os RTOs.

#### <a name="volumeshare-type"></a>Tipo de volume/compartilhamento
O StorSimple oferece suporte a dois tipos de volume/compartilhamento baseados no uso: fixados localmente e em camadas. Os volumes/compartilhamentos localmente fixados são fortemente provisionados, enquanto os volumes/compartilhamentos em camadas têm provisionamento dinâmico. Não é possível converter um volume/compartilhamento localmente afixado em camadas ou *vice-versa* depois de criado.

É recomendável que você implemente as seguintes práticas recomendadas ao configurar volumes/compartilhamentos do StorSimple:

* Identifique o tipo de volume com base em cargas de trabalho que você pretende implantar antes de criar um volume. Use volumes localmente fixos para cargas de trabalho que exigem garantias locais de dados (mesmo durante uma interrupção de nuvem) e que exigem latências de nuvem baixas. Ao criar um volume em sua matriz virtual, você não pode alterar o tipo de volume de fixado localmente para em camadas ou *vice-versa*. Por exemplo, crie volumes localmente fixos ao implantar cargas de trabalho do SQL ou cargas de trabalho que hospedam máquinas virtuais (VMs); use volumes em camadas para cargas de trabalho de compartilhamento de arquivo.


#### <a name="volume-format"></a>Formato de volume
Depois de criar volumes do StorSimple em seu servidor iSCSI, você precisa inicializar, montar e formatar os volumes. Essa operação é executada no host conectado ao seu dispositivo StorSimple. As práticas recomendadas a seguir são recomendadas ao montar e formatar volumes no host do StorSimple.

* Execute uma formatação rápida em todos os volumes do StorSimple.
* Ao formatar um volume do StorSimple, use um tamanho de unidade de alocação (AUS) de 64 KB (o padrão é 4 KB). O AUS de 64 KB baseia-se em testes realizados internamente para cargas de trabalho comuns do StorSimple e outras cargas de trabalho.
* Ao usar o StorSimple Virtual Array configurado como um servidor iSCSI, não use volumes estendidos ou discos dinâmicos, pois eles não são suportados pelo StorSimple.

#### <a name="share-access"></a>Acesso ao compartilhamento
Ao criar compartilhamentos em seu servidor de arquivo de matriz virtual, siga estas diretrizes:

* Ao criar um compartilhamento, atribua um grupo de usuários como um administrador de compartilhamento em vez de um único usuário.
* Você pode gerenciar as permissões de NTFS depois que o compartilhamento for criado por meio da edição dos compartilhamentos no Windows Explorer.

#### <a name="volume-access"></a>Acesso ao volume
Ao configurar os volumes iSCSI no Array Virtual StorSimple, é importante controlar o acesso sempre que necessário. Para determinar quais servidores host podem acessar volumes, crie e associe ACRs (registros de controle de acesso) aos volumes do StorSimple.

Use as seguintes práticas recomendadas ao configurar ACRs para volumes do StorSimple:

* Sempre associe pelo menos um ACR a um volume.

* Ao atribuir mais de um ACR a um volume, verifique se o volume não está exposto de uma maneira em que possa ser acessado simultaneamente por mais de um host não clusterizado. Se vários ACRs tiverem sido atribuídos a um volume, uma mensagem de aviso será exibida para você verificar sua configuração.

### <a name="data-security-and-encryption"></a>Segurança e criptografia de dados
O StorSimple Virtual Array tem recursos de segurança e criptografia de dados que garantem a confidencialidade e a integridade dos dados. Ao usar esses recursos, é recomendável que você siga estas práticas recomendadas: 

* Defina uma chave de criptografia de armazenamento de nuvem para gerar a criptografia AES-256 antes que os dados sejam enviados da sua matriz virtual para a nuvem. Essa chave não será necessária se seus dados forem criptografados inicialmente. A chave pode ser gerada e mantida segura usando um sistema de gerenciamento de chaves como o [Cofre de Chaves do Azure](../key-vault/key-vault-whatis.md).
* Ao configurar a conta de armazenamento por meio do serviço StorSimple Manager, habilite o modo SSL para criar um canal seguro para comunicação de rede entre seu dispositivo StorSimple e a nuvem.
* Regenere as chaves para suas contas de armazenamento (acessando o serviço de armazenamento do Azure) periodicamente para ser responsável por todas as alterações no acesso com base na lista de administradores alterada.
* Os dados em sua matriz virtual são compactados e a eliminação de duplicação acontece antes que os dados sejam enviados para o Azure. Não recomendamos usar o serviço de função Eliminação de Duplicação de Dados no host do Windows Server.

## <a name="operational-best-practices"></a>Práticas recomendadas operacionais
As práticas recomendadas operacionais são diretrizes que devem ser seguidas durante a operação da matriz virtual ou no gerenciamento diário. Essas práticas incluem tarefas de gerenciamento específicas, como fazer backups, restaurar de um conjunto de backups, realizar um failover, desativar e excluir a matriz, monitorar o uso e a integridade do sistema e executar verificações de vírus na matriz virtual.

### <a name="backups"></a>Backups
É feito backup dos dados em sua matriz virtual para a nuvem de duas maneiras: um backup padrão diário automatizado do dispositivo inteiro começando às 22:30 ou por meio de um backup manual sob demanda. Por padrão, o dispositivo cria automaticamente instantâneos de nuvem diários de todos os dados que residem nela. Para saber mais, acesse [fazer backup do seu StorSimple Virtual Array](storsimple-virtual-array-backup.md).

A frequência e a retenção associadas aos backups padrão não podem ser alteradas, mas você pode configurar a hora em que os backups diários são iniciados todos os dias. Ao configurar a hora de início para os backups automatizados, recomendamos que:

* Agende os backups fora dos horários de pico. A hora de início de backup não deve coincidir com várias E/S do host.
* Inicie um backup manual sob demanda ao planejar a execução de um failover de dispositivo ou antes da janela de manutenção para proteger os dados em sua matriz virtual.

### <a name="restore"></a>Restaurar
Você pode restaurar de um conjunto de backup de duas maneiras: restaure para outro volume ou compartilhamento ou realize uma recuperação no nível de item (disponível apenas em uma matriz virtual configurada como um servidor de arquivos). A recuperação no nível do item permite fazer uma recuperação granular de arquivos e pastas de um backup de nuvem de todos os compartilhamentos no dispositivo StorSimple. Para saber mais, vá para [restaurar de um backup](storsimple-virtual-array-clone.md).

Ao realizar uma restauração, lembre-se das seguintes diretrizes:

* Seu StorSimple Virtual Array não oferece suporte à restauração no local. Entretanto, isso pode ser facilmente obtido em um processo de duas etapas: abra espaço na matriz virtual e restaure em outro volume/compartilhamento.
* Ao restaurar de um volume local, tenha em mente que a restauração será uma operação demorada. Embora o volume possa ficar online rapidamente, os dados continuam sendo alimentados no plano de fundo.
* O tipo de volume permanece o mesmo durante o processo de restauração. Um volume em camadas é restaurado para outro volume em camadas e um volume fixado localmente é restaurado para outro volume fixado localmente.
* Ao tentar restaurar um volume ou um compartilhamento de um conjunto de backup, se o trabalho de restauração falhar, um volume de destino ou compartilhamento ainda poderá ser criado no portal. É importante excluir este volume de destino não utilizado ou compartilhá-lo no portal para minimizar quaisquer problemas futuros causado por esse elemento.

### <a name="failover-and-disaster-recovery"></a>Failover e recuperação de desastres
Um failover de dispositivo permitirá que você migre os dados de um dispositivo de *origem* no datacenter para outro dispositivo de *destino* localizado no mesmo local geográfico ou em outro. O failover de dispositivo é para todo o dispositivo. Durante o failover, a propriedade dos dados de nuvem para o dispositivo de origem é alterada para aquela do dispositivo de destino.

Para seu StorSimple Virtual Array, só será possível fazer failover para outra matriz virtual gerenciada pelo mesmo serviço do StorSimple Manager. Não é permitido um failover para um dispositivo da série 8000 ou para uma matriz gerenciada por um serviço do StorSimple Manager diferente (daquele para o dispositivo de origem). Para saber mais sobre as considerações de failover, acesse os [pré-requisitos para failover de dispositivo](storsimple-virtual-array-failover-dr.md).

Ao fazer um failover para sua matriz virtual, tenha o seguinte em mente:

* Para um failover planejado, é uma prática recomendada colocar todos os volumes/compartilhamentos offline antes de iniciar o failover. Siga as instruções específicas do sistema operacional para colocar primeiro os volumes/compartilhamentos offline no host e depois colocá-los offline em seu dispositivo virtual.
* Para uma DR (recuperação de desastre) de um servidor de arquivos, é recomendável ingressar o dispositivo de destino no mesmo domínio que o dispositivo de origem para que as permissões de compartilhamento sejam resolvidas automaticamente. Nesta versão, há suporte apenas para o failover de um dispositivo de destino no mesmo domínio.
* Depois que a recuperação de desastre for concluída com êxito, o dispositivo de origem será automaticamente excluído. Embora o dispositivo não esteja mais disponível, a máquina virtual que você provisionou no sistema host ainda está consumindo recursos. É recomendável que você exclua esta máquina virtual do sistema host para impedir o acúmulo de todos os encargos.
* Observe que, mesmo se o failover não for bem-sucedido, **os dados estarão sempre seguros na nuvem**. Considere os três cenários a seguir em que o failover não foi concluído com êxito:
  
  * Ocorreu uma falha nos estágios iniciais do failover, como quando as pré-verificações de DR estão sendo executadas. Nessa situação, o dispositivo de destino ainda é utilizável. Você pode repetir o failover no mesmo dispositivo de destino.
  * Ocorreu uma falha durante o processo de failover real. Nesse caso, o dispositivo de destino é marcado como inutilizável. Você deve provisionar e configurar outra matriz virtual de destino e usá-la para failover.
  * O failover foi concluído e depois dele o dispositivo de origem foi excluído, mas o dispositivo de destino tem problemas e não é possível acessar os dados. Os dados ainda estão seguros na nuvem e podem ser facilmente recuperados por meio da criação de outra matriz virtual ; depois eles serão usados como um dispositivo de destino para a recuperação de desastre.

### <a name="deactivate"></a>Desativar
Quando você desativar uma Matriz Virtual StorSimple, você dividirá a conexão entre o dispositivo e o serviço StorSimple Manager correspondente. A desativação é uma operação **permanente** e não pode ser desfeita. Um dispositivo desativado não pode ser registrado com o serviço StorSimple Manager novamente. Para saber mais, acesse [desativar e excluir um dispositivo StorSimple](storsimple-virtual-array-deactivate-and-delete-device.md).

Lembre-se das seguintes práticas recomendadas ao desativar sua matriz virtual:

* Tire um instantâneo de nuvem de todos os dados antes de desativar um dispositivo virtual. Quando você desativa uma matriz virtual, todos os dados do dispositivo local são perdidos. Tirar um instantâneo de nuvem permitirá que você recupere os dados em um estágio posterior.
* Antes de desativar um StorSimple Virtual Array, certifique-se de interromper ou excluir clientes e hosts que dependem desse dispositivo virtual.
* Exclua um dispositivo desativado se ele não estiver sendo usado para que ele não acumule cobranças.

### <a name="monitoring"></a>Monitoramento
Para garantir que seu StorSimple Virtual Array esteja em um estado de integridade contínuo, você precisará monitorar a matriz e garantir que receberá informações do sistema, incluindo alertas. Para monitorar a integridade geral da matriz virtual, implemente as seguintes práticas recomendadas:

* Configure o monitoramento para acompanhar o uso do disco de dados da matriz virtual, bem como o disco do sistema operacional. Se estiver executando o Hyper-V, você poderá usar uma combinação do SCVMM (System Center Virtual Machine Manager) e do System Center Operations Manager para monitorar os hosts de virtualização.
* Configure notificações por email em sua matriz virtual para enviar alertas em determinados níveis de uso.                                                                                                                                                                                                

### <a name="index-search-and-virus-scan-applications"></a>Aplicativos de pesquisa de índice e de verificação de vírus
Um StorSimple Virtual Array pode colocar automaticamente os dados em camadas da camada local para a nuvem do Microsoft Azure. Quando um aplicativo como a pesquisa de índice ou uma verificação de vírus é usado para varrer os dados armazenados no StorSimple, você precisará tomar cuidado para que os dados de nuvem não sejam acessados e obtidos na camada local.

É recomendável que você implemente as práticas recomendadas a seguir ao configurar a verificação de vírus ou a pesquisa de índice em sua matriz virtual:

* Desabilite todas as operações de verificação completa configuradas automaticamente.
* Para os volumes em camadas, configure a pesquisa de índice ou o aplicativo de verificação de vírus para executar uma verificação incremental. Isso examinaria apenas os novos dados, provavelmente residentes na camada local. Os dados que estão em camadas na nuvem não são acessados durante uma operação incremental.
* Verifique se as configurações e os filtros de pesquisa corretos foram definidos de forma que somente os tipos de arquivo sejam verificados. Por exemplo, os arquivos de imagem (JPEG, GIF e TIFF) e os desenhos de engenharia não devem ser verificados durante a recriação de índice completo ou incremental.

Se você estiver usando o processo de indexação do Windows, siga estas diretrizes:

* Não use o Indexador do Windows para volumes em camadas, já que isso cancela grandes quantidades de dados (TBs) da nuvem caso o índice tenha de ser recriado com frequência. A recriação do índice poderia recuperar todos os tipos de arquivo para indexar seu conteúdo.
* Use o processo de indexação do Windows para volumes fixados localmente, já que eles só acessariam dados nas camadas locais para criar os índices (os dados da nuvem não serão acessados).

### <a name="byte-range-locking"></a>Bloqueio de intervalo de bytes
Os aplicativos podem bloquear um intervalo de bytes especificado nos arquivos. Se o bloqueio de intervalos de bytes for habilitado nos aplicativos que estejam gravando no StorSimple, as camadas não funcionarão em sua matriz virtual. Para que as camadas funcionem, todas as áreas dos arquivos acessados deverão estar desbloqueadas. Não há suporte para o bloqueio de intervalo de bytes com volumes em camadas em sua matriz virtual.

As medidas recomendadas para aliviar o bloqueio de intervalos de bytes incluem:

* Desligar o bloqueio de intervalo de bytes em sua lógica de aplicativo.
* Usar volumes fixados localmente (em vez de em camadas) para os dados associados a este aplicativo. Os volumes fixados localmente não ficam em camadas na nuvem.
* Ao usar volumes fixados localmente com o bloqueio de intervalos de bytes habilitado, o volume poderá ficar online antes da conclusão da restauração. Nestas instâncias, você deverá aguardar a conclusão da restauração.

## <a name="multiple-arrays"></a>Várias matrizes
Talvez seja necessário implantar várias matrizes virtuais para responder por um crescente conjunto de dados de trabalho que poderia derramar para a nuvem, afetando o desempenho do dispositivo. Nesses casos, é melhor dimensionar dispositivos à medida que o conjunto de trabalho aumentar. Isso exige que um ou mais dispositivos sejam adicionados ao data center local. Ao adicionar os dispositivos, você poderia:

* Dividir o conjunto de dados atual.
* Implantar novas cargas de trabalho em novos dispositivos.
* Se você estiver implantando várias matrizes, recomendamos que, da perspectiva do balanceamento de carga, distribua a matriz por vários hosts de hipervisor diferentes.
* Várias matrizes virtuais (quando configuradas como um servidor de arquivos ou como um servidor iSCSI) podem ser implantadas em um Namespace de Sistema de Arquivos Distribuído . Para ver as etapas detalhadas, acesse [Distributed File System Namespace Solution with Hybrid Cloud Storage Deployment Guide (Guia de solução de namespace de sistema de arquivos distribuído com armazenamento em nuvem híbrida)](https://www.microsoft.com/download/details.aspx?id=45507). Atualmente, a replicação DFS não é recomendada para uso com a matriz virtual. 

## <a name="see-also"></a>Consulte também
Saiba como [administrar o StorSimple Virtual Array](storsimple-virtual-array-manager-service-administration.md) por meio do serviço StorSimple Manager.

