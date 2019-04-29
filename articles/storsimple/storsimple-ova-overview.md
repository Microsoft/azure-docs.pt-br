---
title: Visão geral da Matriz Virtual do Microsoft Azure StorSimple | Microsoft Docs
description: Descreve a Matriz Virtual do StorSimple, uma solução de armazenamento integrado que gerencia as tarefas de armazenamento entre uma matriz virtual local e o armazenamento em nuvem do Microsoft Azure.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 169c639b-1124-46a5-ae69-ba9695525b77
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: e5713af737a6d9d190814b4155a8e772deea06bc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60630247"
---
# <a name="introduction-to-the-storsimple-virtual-array"></a>Introdução ao StorSimple Virtual Array

## <a name="overview"></a>Visão geral

A Matriz Virtual do Microsoft Azure StorSimple é uma solução de armazenamento integrado que gerencia tarefas de armazenamento entre uma matriz virtual local em execução em um hipervisor e o armazenamento em nuvem do Microsoft Azure. A matriz virtual é um servidor de arquivos eficiente, econômico e facilmente gerenciável, ou solução de servidor iSCSI, que elimina vários problemas e despesas associados à proteção de dados e ao armazenamento empresarial. A matriz virtual é particularmente adequada para o armazenamento de dados de arquivamento acessados com pouca frequência.

Este artigo apresenta uma visão geral da matriz virtual – confira aqui alguns outros recursos:

* Para conhecer as práticas recomendadas, confira [Práticas recomendadas da Matriz Virtual do StorSimple](storsimple-ova-best-practices.md).
* Para obter uma visão geral dos dispositivos StorSimple série 8000, acesse [StorSimple série 8000: uma solução de nuvem híbrida](storsimple-overview.md).
* Para obter informações sobre dispositivos StorSimple série 5000/7000, acesse [Ajuda online do StorSimple](http://onlinehelp.storsimple.com/).

A matriz virtual dá suporte ao protocolo SMB (bloco de mensagens de servidor) ou ao iSCSI. Ela é executada em sua infraestrutura existente do hipervisor e fornece camadas para a nuvem, backup na nuvem, restauração rápida, recuperação em nível de item e recursos de recuperação de desastre.

A tabela a seguir resume os recursos importantes da Matriz Virtual do StorSimple.

| Recurso | Matriz Virtual do StorSimple |
| --- | --- |
| Requisitos de instalação |Usa a infraestrutura de virtualização (Hyper-V ou VMware) |
| Disponibilidade |Nó único |
| Capacidade total (incluindo nuvem) |Até 64 TB de capacidade utilizável por matriz virtual |
| Capacidade local |De 390 GB a 6,4 TB de capacidade utilizável por matriz virtual (é necessário provisionar 500 GB a 8 TB de espaço em disco) |
| Protocolos nativos |iSCSI ou SMB |
| RTO (Objetivo de Tempo de Recuperação) |iSCSI: menos de 2 minutos, independentemente do tamanho |
| RPO (Objetivo de Ponto de Recuperação) |Backups diários e backups sob demanda |
| Disposição do armazenamento em camadas |Usa o mapeamento de calor para determinar quais dados devem ser colocados nas camadas ou removidos delas |
| Suporte |Infraestrutura de virtualização com suporte pelo fornecedor |
| Desempenho |Varia de acordo com a infraestrutura subjacente |
| Mobilidade de dados |É possível restaurar para o mesmo dispositivo ou realizar recuperação em nível de item (servidor de arquivos) |
| Camadas de armazenamento |Armazenamento local de hipervisor e nuvem |
| Tamanho do compartilhamento |Camadas: até 20 TB; localmente fixados: até 2 TB |
| Tamanho do volume |Em camadas: 500 GB a 5 TB; fixado localmente: 50 GB a 200 GB <br> A reserva local máxima para volumes em camadas é de 200 GB. |
| Instantâneos |Com controle de falhas |
| Recuperação em nível de item |Sim, os usuários podem fazer restauração de compartilhamentos |

## <a name="why-use-storsimple"></a>Por que usar StorSimple?

O StorSimple conecta usuários e servidores no armazenamento do Azure, em minutos, sem modificações em aplicativos.

A tabela a seguir descreve algumas das principais vantagens oferecidas pela solução Matriz Virtual do StorSimple.

| Recurso | Benefício |
| --- | --- |
| Integração transparente |A matriz virtual dá suporte ao protocolo SMB ou ao iSCSI. A movimentação de dados entre a camada de local e a camada de nuvem é contínua e transparente para o usuário. |
| Redução nos custos de armazenamento |Com o StorSimple, você provisiona armazenamento local suficiente para atender às demandas atuais pelos dados mais usados e de grande interesse. Conforme as necessidades de armazenamento crescem, o StorSimple distribui dados de menor interesse em camadas para um armazenamento em nuvem com bom custo/benefício. As duplicatas são removidas e os dados são comprimidos antes de serem enviados à nuvem para reduzir ainda mais os custos e os requisitos de armazenamento. |
| Gerenciamento simplificado do armazenamento |O StorSimple fornece gerenciamento centralizado na nuvem usando o Gerenciador de Dispositivos StorSimple para gerenciar vários dispositivos. |
| Aprimoramento da recuperação de desastres e conformidade |O StorSimple facilita uma recuperação de desastres mais rápida restaurando os metadados imediatamente e restaurando os dados conforme necessário. Isso significa que as operações normais podem continuar com um mínimo de interrupção. |
| Mobilidade de dados |Os dados carregados na nuvem podem ser acessados de outros locais para fins de recuperação e migração. Observe que você pode restaurar dados somente para a matriz virtual original. No entanto, use recursos de recuperação de desastre para restaurar toda a matriz virtual para outra matriz virtual. |

## <a name="storsimple-workload-summary"></a>Resumo de carga de trabalho do StorSimple

Confira abaixo um resumo das cargas de trabalho com suporte do StorSimple.

|Cenário     |Carga de trabalho     |Com suporte      |Restrições               | Versões aplicáveis|
|-------------|-------------|---------------|---------------------------|--------------------|
|Escritórios remotos/filiais (ROBO)  |Compartilhamento de arquivos     |Sim      |Consulte [limites máximos para o servidor de arquivos](storsimple-ova-limits.md).<br></br>Confira [requisitos do sistema para as versões suportadas por SMB](storsimple-ova-system-requirements.md).| Todas as versões     |
|Arquivamento de nuvem  |Compartilhamento de arquivos de arquivamento     |Sim      |Consulte [limites máximos para o servidor de arquivos](storsimple-ova-limits.md).<br></br>Confira [requisitos do sistema para as versões suportadas por SMB](storsimple-ova-system-requirements.md).| Todas as versões     |

A Matriz Virtual StorSimple é mais adequada para dados acessados com pouca frequência. Embora a matriz virtual tenha um cache local para melhorar o desempenho, os usuários devem supor que o dispositivo atende a arquivos no nível mais baixo de armazenamento (nuvem). Cada matriz virtual pode gravar e ler no armazenamento do Azure em aproximadamente 100 Mbps. Esse link é compartilhado entre todas as solicitações que chegam ao dispositivo e pode tornar-se um gargalo, conforme mostra o diagrama a seguir.

![Arquivamento de nuvem](./media/storsimple-ova-overview/cloud-archiving.png)

Quando vários usuários simultâneos acessam a matriz virtual, todos eles compartilham a conexão com o Azure, levando a um desempenho inferior. Não há garantia de desempenho por usuário e o dispositivo processa solicitações individuais conforme elas chegam.

A Matriz Virtual StorSimple não é adequada para cargas de trabalho que exigem alta disponibilidade. A matriz virtual é um dispositivo de nó único que apresenta o tempo de inatividade quando as atualizações de software são instaladas. Os administradores devem planejar uma janela de manutenção de 30 minutos de três a quatro vezes por ano.

## <a name="workflows"></a>Fluxos de trabalho

A StorSimple Virtual Array é particularmente adequada para os fluxos de trabalho a seguir:

* [Gerenciamento de armazenamento baseado em nuvem](#cloud-based-storage-management)
* [Backup independente de local](#location-independent-backup)
* [Recuperação de desastre e proteção de dados](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>Gerenciamento de armazenamento baseado em nuvem
Você pode usar o serviço Gerenciador de Dispositivos StorSimple em execução no Portal do Azure para gerenciar dados armazenados em vários dispositivos e em vários locais. Isso é particularmente útil em cenários de filiais distribuídas. Observe que você deve criar instâncias separadas do serviço Gerenciador de Dispositivos do StorSimple para gerenciar as matrizes virtuais e dispositivos físicos StorSimple. Observe também que a matriz virtual usa o novo Portal do Azure em vez do Portal clássico do Azure.

![Gerenciamento de armazenamento baseado em nuvem](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>Backup independente de local
Com a matriz virtual, os instantâneos de nuvem fornecem uma cópia pontual de um volume ou compartilhamento, independente de local. Instantâneos de nuvem são habilitados por padrão e não podem ser desabilitados. Todos os volumes e compartilhamentos de backup serão realizado ao mesmo tempo por meio de uma única política de backup diária, e você pode fazer backups de ad-hoc adicionais sempre que necessário.

### <a name="data-protection-and-disaster-recovery"></a>Recuperação de desastre e proteção de dados
A matriz virtual dá suporte aos seguintes cenários de proteção de dados e recuperação de desastre:

* **Restauração de volume ou compartilhamento** – use a restauração como um novo fluxo de trabalho para recuperar um volume ou compartilhamento. Use essa abordagem para recuperar o compartilhamento ou volume inteiro.
* **Recuperação no nível de item** – compartilhamentos permitem acesso simplificado aos backups recentes. Você pode recuperar facilmente um arquivo individual de uma pasta *.backup* especial disponível na nuvem. Essa funcionalidade de restauração é controlada pelo usuário e nenhuma intervenção administrativa é necessária.
* **Recuperação de desastre** – use a funcionalidade de failover para recuperar todos os volumes ou compartilhamentos para uma nova matriz virtual. Crie a nova matriz virtual e registre-a com o serviço Gerenciador de Dispositivos StorSimple, depois realize o failover da matriz virtual original. A nova matriz virtual assumirá os recursos provisionados.

## <a name="storsimple-virtual-array-components"></a>Componentes da Matriz Virtual do StorSimple

A matriz virtual inclui os seguintes componentes:

* [Matriz virtual](#virtual-array) – um dispositivo de armazenamento de nuvem híbrida baseado em uma máquina virtual provisionada em seu ambiente virtualizado ou hipervisor.
* [Serviço Gerenciador de Dispositivos do StorSimple](#storsimple-device-manager-service) – uma extensão do Portal do Azure que permite gerenciar um ou mais dispositivos StorSimple de uma única interface da Web, que pode ser acessada de diferentes localizações geográficas. Você pode usar o serviço Gerenciador de Dispositivos StorSimple para criar e gerenciar serviços, exibir e gerenciar dispositivos e alertas, gerenciar volumes, compartilhamentos e instantâneos existentes.
* [Interface de usuário da Web local](#local-web-user-interface) – uma interface do usuário baseada na Web usada para configurar o dispositivo para que ele se conecte à rede local e para registrar o dispositivo no serviço Gerenciador de Dispositivos StorSimple. 
* [Interface de linha de comando](#command-line-interface) – uma interface do Windows PowerShell que pode ser usada para iniciar uma sessão de suporte na matriz virtual.
  As seções a seguir descrevem cada um dos componentes com bastante detalhes e explicam como a solução organiza dados, aloca armazenamento e facilita o gerenciamento de armazenamento e a proteção de dados.

### <a name="virtual-array"></a>Matriz virtual

A matriz virtual é uma solução de armazenamento de nó único que oferece armazenamento primário, gerencia a comunicação com o armazenamento em nuvem e ajuda a garantir a segurança e a confidencialidade de todos os dados armazenados no dispositivo.

A matriz virtual está disponível em um modelo para download. A matriz virtual tem uma capacidade máxima de 6,4 TB no dispositivo (com um requisito de armazenamento subjacente de 8 TB) e 64 TB incluindo o armazenamento em nuvem.

A matriz virtual tem os seguintes recursos:

* Tem bom custo/benefício. Usa sua infraestrutura de virtualização existente e pode ser implantada em seu hipervisor Hyper-V ou VMware existente.
* Reside no datacenter e pode ser configurada como um servidor iSCSI ou um servidor de arquivos.
* É integrada à nuvem.
* Backups são armazenados na nuvem, que pode facilitar a recuperação de desastres e simplificar a ILR (Recuperação em Nível de Item).
* Você pode aplicar atualizações à matriz virtual, assim como você as aplicaria a um dispositivo físico.

> [!NOTE]
> Uma matriz virtual não pode ser expandida. Portanto, é importante provisionar um armazenamento adequado ao criar a matriz virtual.

### <a name="storsimple-device-manager-service"></a>Serviço Gerenciador de Dispositivos StorSimple

O Microsoft Azure StorSimple fornece uma interface de usuário baseada na Web, o serviço Gerenciador de Dispositivos StorSimple, que permite gerenciar de modo centralizado o armazenamento do StorSimple. Você pode usar o serviço Gerenciador de Dispositivos StorSimple para executar as seguintes tarefas:

* Gerenciar, de um único serviço, várias Matrizes Virtuais StorSimple.
* Configurar e gerenciar configurações de segurança para Matrizes Virtuais StorSimple. (Criptografia na nuvem é dependente de APIs do Microsoft Azure.)
* Configurar propriedades e credenciais da conta de armazenamento.
* Configurar e gerenciar volumes ou compartilhamentos.
* Fazer backup e restaurar dados em volumes ou compartilhamentos.
* Monitorar o desempenho.
* Revisar as configurações do sistema e identificar possíveis problemas.

Você pode usar o serviço Gerenciador de Dispositivo StorSimple para realizar a administração diária de sua matriz virtual.

Para saber mais, confira [Usar o serviço Gerenciador de Dispositivo StorSimple para administrar seu dispositivo StorSimple](storsimple-virtual-array-manager-service-administration.md).

### <a name="local-web-user-interface"></a>Interface do usuário da Web local

A matriz virtual inclui uma interface de usuário baseada na Web, que é usada para configuração e registro único do dispositivo, com o serviço Gerenciador de Dispositivo StorSimple. Você pode usá-la para desligar e reiniciar a matriz virtual, executar testes de diagnóstico, atualizar o software, alterar a senha de administrador do dispositivo, exibir logs do sistema e contatar o Suporte da Microsoft para registrar uma solicitação de serviço.

Para obter informações sobre como usar a interface do usuário baseada na Web, vá para [Usar a interface do usuário baseada na Web para administrar o Matriz Virutal do StorSimple](storsimple-ova-web-ui-admin.md).

### <a name="command-line-interface"></a>Interface de linha de comando

A interface incluída no Windows PowerShell permite iniciar uma sessão de suporte com o Suporte da Microsoft para que eles possam ajudá-lo a solucionar problemas e resolver problemas que podem ser encontrados em sua matriz virtual.

## <a name="storage-management-technologies"></a>Tecnologias de gerenciamento de armazenamento

Além da matriz virtual e outros componentes, a solução StorSimple usa as tecnologias de software a seguir para fornecer acesso rápido aos dados importantes, reduzir o consumo de armazenamento e proteger os dados armazenados em sua matriz virtual:

* [Camadas de armazenamento automático](#automatic-storage-tiering) 
* [Volumes e compartilhamentos fixados localmente](#locally-pinned-shares-and-volumes)
* Eliminação de duplicação e compactação para dados em camadas ou em backup na nuvem 
* [Backups agendados e sob demanda](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>Camadas de armazenamento automático
A matriz virtual usa um novo mecanismo de distribuição em camadas para gerenciar dados armazenados na matriz virtual e na nuvem. Há apenas duas camadas: a matriz virtual local e o armazenamento em nuvem do Azure. A StorSimple Virtual Array organiza os dados nas camadas com base em um mapa de calor, que controla o uso atual, idade e relações com outros dados. Dados mais ativos (mais quentes) são armazenados localmente, enquanto dados menos ativos e inativos são migrados automaticamente para a nuvem. (Todos os backups são armazenados na nuvem.) O StorSimple ajusta e reorganiza as atribuições de dados e armazenamento conforme os padrões de uso mudam. Por exemplo, algumas informações poderão ficar menos ativas ao longo do tempo. Conforme elas se tornam cada vez menos ativas, são removidas da camada e enviadas para a nuvem. Se esses mesmos dados ficarem ativos novamente, passarão por uma nova transferência de camada indo para a matriz de armazenamento.

Dados de um determinado compartilhamento em camadas ou o volume têm garantido seu próprio espaço de camada local (aproximadamente 10% do total de espaço configurado para esse compartilhamento ou volume). Enquanto isso reduz o armazenamento disponível na matriz virtual para esse compartilhamento ou volume, garante que camadas para um compartilhamento ou volume não serão afetadas pelas necessidades de distribuição em camadas de outros volumes ou compartilhamentos. Portanto, uma carga de trabalho muito ocupada em um compartilhamento ou volume não pode forçar todas as outras cargas de trabalho para a nuvem.

Volumes em camadas criados para iSCSI têm uma reserva local máxima de 200 GB, independentemente do tamanho do volume.

![Camadas de armazenamento automático](./media/storsimple-ova-overview/automatic-storage-tiering.png)

> [!NOTE]
> Você pode especificar um volume fixado localmente; neste caso, os dados permanecem na matriz virtual e nunca são distribuídos em camadas para a nuvem. Para obter mais informações, vá para [Volumes e compartilhamentos fixados localmente](#locally-pinned-shares-and-volumes).


### <a name="locally-pinned-shares-and-volumes"></a>Volumes e compartilhamentos fixados localmente

Você pode criar os compartilhamentos e volumes apropriados como fixados localmente. Essa funcionalidade garante que os dados necessários para aplicativos críticos permaneçam na matriz virtual e não sejam distribuídos em camadas na nuvem. Compartilhamentos e volumes fixados localmente têm as seguintes características:

* Não estão sujeitos a problemas de conectividade ou latências na nuvem.
* Eles ainda se beneficiam dos recursos de recuperação de desastre e backup em nuvem StorSimple.

Você pode restaurar um compartilhamento ou volume fixado localmente como em camadas, ou então um compartilhamento ou volume em camadas como fixado localmente. 

Para saber mais sobre volumes fixados localmente, acesse [Usar o serviço Gerenciador de Dispositivo StorSimple para gerenciar volumes](storsimple-virtual-array-manage-volumes.md).

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>Eliminação de duplicação e compactação para dados em camadas ou em backup na nuvem

O Microsoft Azure StorSimple usa eliminação de duplicação e compactação de dados para reduzir ainda mais os requisitos de armazenamento na nuvem. A eliminação de duplicação reduz a quantidade geral de dados armazenados, eliminando a redundância no conjunto de dados armazenado. Conforme as informações mudam, o StorSimple ignora os dados inalterados e captura apenas as alterações. Além disso, o StorSimple reduz a quantidade de dados armazenados ao identificar e remover informações duplicadas.

> [!NOTE]
> Os dados armazenados na matriz virtual não passaram por eliminação de duplicação ou compactação. Toda a eliminação de duplicação e compactação ocorrem imediatamente antes do envio dos dados para a nuvem.

### <a name="scheduled-and-on-demand-backups"></a>Backups agendados e sob demanda

Os recursos de proteção de dados StorSimple permitem que você crie backups sob demanda. Além disso, um agendamento padrão garante que o backup dos dados seja realizado diariamente. Backups são feitos na forma de instantâneos incrementais, que são armazenados na nuvem. Instantâneos, que registram apenas as alterações desde o último backup, podem ser criados e restaurados rapidamente. Esses instantâneos podem ser extremamente importantes em cenários de recuperação de desastres, pois substituem os sistemas de armazenamento secundário (como backup em fita) e permitem restaurar dados para o data center ou para locais alternativos, se necessário.

## <a name="managing-personal-information"></a>Gerenciando informações pessoais

O StorSimple Device Manager para séries virtuais coleta informações pessoais em duas instâncias principais:
 - Configurações de usuário de alerta em que os endereços de email dos usuários são configurados. Essas informações podem ser apagadas pelo administrador. 
 - Usuários que podem acessar os dados que residem nos compartilhamentos. Uma lista de usuários que podem acessar os dados de compartilhamento é exibida e pode ser exportada. Essa lista também será excluída quando os compartilhamentos são excluídos.

Para mais informações, revise a [Política de Privacidade da Microsoft na Central de Confiabilidade](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Próximas etapas

Saiba como [preparar o portal da matriz virtual](storsimple-virtual-array-deploy1-portal-prep.md).
