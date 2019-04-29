---
title: Instalar o adaptador StorSimple para SharePoint | Microsoft Docs
description: Descreve como instalar e configurar ou remover o Adaptador StorSimple para SharePoint em um farm de servidores do SharePoint.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 36c20b75-f2e5-4184-a6b5-9c5e618f79b2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/06/2017
ms.author: v-sharos
ms.openlocfilehash: a2f8e75578e396085e7d80f43c1180e158967061
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60633049"
---
# <a name="install-and-configure-the-storsimple-adapter-for-sharepoint"></a>Instalar e configurar o Adaptador StorSimple para o SharePoint
## <a name="overview"></a>Visão geral
O Adaptador StorSimple para SharePoint é um componente que permite fornecer armazenamento flexível do Microsoft Azure StorSimple e proteção de dados para farms de servidores do SharePoint. Você pode usar o adaptador para mover o conteúdo do BLOB (objeto binário grande) de bancos de dados de conteúdo do SQL Server para o dispositivo de armazenamento de nuvem híbrida do Microsoft Azure StorSimple.

O adaptador StorSimple para SharePoint funciona como um provedor de RBS (armazenamento de BLOB remoto) e usa o recurso de armazenamento de BLOB remoto do SQL Server para armazenar o conteúdo do SharePoint não estruturado (na forma de BLOBs) em um servidor de arquivos apoiado por um dispositivo StorSimple.

> [!NOTE]
> O adaptador StorSimple para SharePoint dá suporte ao RBS (Remote BLOB Storage) do SharePoint Server 2010 . Ele não dá suporte a EBS (External BLOB Storage) do SharePoint Server 2010.


* Para baixar o Adaptador StorSimple para SharePoint, acesse [Adaptador StorSimple para SharePoint][1] no Centro de Download da Microsoft.
* Para obter informações sobre como planejar RBS e suas limitações, acesse [Decidindo usar RBS no SharePoint 2013][2] ou [Planejamento para RBS (SharePoint Server 2010)][3].

O restante desta visão geral descreve resumidamente a função do Adaptador StorSimple para SharePoint e os limites de desempenho e capacidade do SharePoint que você deve conhecer antes de instalar e configurar o adaptador. Depois de analisar essas informações, vá para [Instalação do Adaptador StorSimple para SharePoint](#storsimple-adapter-for-sharepoint-installation) para começar a configurar o adaptador.

### <a name="storsimple-adapter-for-sharepoint-benefits"></a>Benefícios do Adaptador StorSimple para SharePoint
Em um site do SharePoint, o conteúdo é armazenado como dados BLOB não estruturados em um ou mais bancos de dados. Por padrão, esses bancos de dados são hospedados em computadores que executam o SQL Server e estão localizados no farm de servidores do SharePoint. Os BLOBs podem aumentar rapidamente de tamanho, consumindo grandes quantidades de armazenamento local. Por esse motivo, convém encontrar outra solução de armazenamento de menor custo. O SQL Server fornece uma tecnologia chamada RBS (Remote Blob Storage), que permite armazenar o conteúdo do BLOB no sistema de arquivos fora do banco de dados do SQL Server. Com o RBS, os BLOBs podem residir no sistema de arquivos no computador que está executando o SQL Server, ou ser armazenados no sistema de arquivos em outro computador do servidor.

O RBS requer que você use um provedor RBS, como o Adaptador StorSimple para SharePoint, para habilitar o RBS no SharePoint. O adaptador StorSimple para SharePoint funciona com o RBS, permitindo que você mova os BLOBs para um servidor apoiado pelo sistema Microsoft Azure StorSimple. O Microsoft Azure StorSimple então armazena os dados BLOB localmente ou na nuvem, com base no uso. BLOBs que são muito ativos (normalmente conhecidos como Camada 1 ou dados ativos) residem localmente. Os dados menos ativos e dados de arquivamento residem na nuvem. Depois de habilitar RBS em um banco de dados de conteúdo, todo novo conteúdo do BLOB criado no SharePoint é armazenado no dispositivo StorSimple, e não no banco de dados.

A implementação de RBS do Microsoft Azure StorSimple fornece os seguintes benefícios:

* Ao mover o conteúdo do BLOB para um servidor separado, você pode reduzir a carga de consulta no SQL Server, o que pode melhorar a capacidade de resposta do SQL Server. 
* O Azure StorSimple usa eliminação de duplicação e compactação para reduzir o tamanho dos dados.
* O Azure StorSimple fornece proteção de dados na forma de instantâneos locais e em nuvem. Além disso, se você colocar o banco de dados no dispositivo StorSimple, pode fazer backup do banco de dados de conteúdo e BLOBs juntos de uma forma consistente de falha. (Mover o banco de dados de conteúdo para o dispositivo tem suporte apenas para o dispositivo de StorSimple 8000 Series. Esse recurso não tem suporte para a série 5000 ou 7000.)
* O Azure StorSimple inclui recursos de recuperação de desastres, inclusive failover, recuperação de arquivo e volume (incluindo recuperação de teste) e restauração rápida de dados.
* Você pode usar o software de recuperação de dados, como Kroll Ontrack PowerControls, com os instantâneos do StorSimple de dados BLOB para executar a recuperação no nível do item de conteúdo do SharePoint. (Esse software de recuperação de dados é uma compra separada.)
* O adaptador StorSimple para SharePoint conecta-se ao portal de Administração Central do SharePoint, permitindo gerenciar toda a solução do SharePoint em um local central.

Mover o conteúdo BLOB para o sistema de arquivos pode proporcionar outras economias de custos e benefícios. Por exemplo, usar o RBS pode reduzir a necessidade de armazenamento de camada 1 e, porque reduz o banco de dados de conteúdo, o RBS pode reduzir o número de bancos de dados necessários no farm de servidores do SharePoint. No entanto, outros fatores, como limites de tamanho do banco de dados e a quantidade de conteúdo não RBS, também podem afetar os requisitos de armazenamento. Para obter mais informações sobre os custos e os benefícios de usar o RBS, consulte [Planejamento para RBS (SharePoint Foundation 2010)][4] e [Decidindo usar o RBS no SharePoint 2013][5].

### <a name="capacity-and-performance-limits"></a>Limites de capacidade e desempenho
Antes de considerar o uso de RBS em sua solução do SharePoint, conheça os limites de desempenho e capacidade testados do SharePoint Server 2010 e do SharePoint Server 2013 e como esses limites se relacionam com desempenho aceitável. Para saber mais, consulte [Limites de software e limites para o SharePoint 2013](https://technet.microsoft.com/library/cc262787.aspx).

Examina o seguinte antes de configurar o RBS:

* Certifique-se de que o tamanho total do conteúdo (o tamanho de um banco de dados de conteúdo) mais o tamanho de todos os BLOBs externalizados associados não exceda o limite de tamanho RBS compatível com o SharePoint. Esse limite é de 200 GB. 
  
    **Para medir o tamanho do BLOB e do banco de dados de conteúdo**
  
  1. Execute esta consulta no WFE de Administração Central. Inicie o Shell de Gerenciamento do SharePoint e, em seguida, digite o seguinte comando do Windows PowerShell para obter o tamanho dos bancos de dados de conteúdo:
     
     `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`
     
      Esta etapa obtém o tamanho do banco de dados de conteúdo no disco.
  2. Execute uma das seguintes consultas SQL no SQL Management Studio na caixa do SQL Server em cada banco de dados de conteúdo e adicione o resultado ao número obtido na etapa 1.
     
     Nos bancos de dados de conteúdo do SharePoint 2013, digite:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`
     
     Nos bancos de dados de conteúdo do SharePoint 2010, digite:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`
     
     Esta etapa obtém o tamanho dos BLOBs que foram externalizados.
* É recomendável armazenar todo o conteúdo do BLOB e do banco de dados localmente no dispositivo StorSimple. O dispositivo StorSimple é um cluster de dois nós para alta disponibilidade. Colocar os bancos de dados de conteúdo e BLOBs no dispositivo StorSimple fornece alta disponibilidade.
  
    Use práticas recomendadas de migração de SQL Server tradicionais para mover o banco de dados de conteúdo para o dispositivo StorSimple. Mova o banco de dados somente depois de todo o conteúdo do BLOB do banco de dados ter sido movido para o compartilhamento de arquivos via RBS. Caso opte por mover o banco de dados de conteúdo para o dispositivo StorSimple, é recomendável configurar o armazenamento de banco de dados de conteúdo no dispositivo como um volume primário.
* No Microsoft Azure StorSimple, se estiver usando volumes em camadas, não haverá nenhuma maneira de assegurar que o conteúdo armazenado localmente no dispositivo StorSimple não será colocado em camadas no armazenamento em nuvem do Microsoft Azure. Portanto, é recomendável usar volumes do StorSimple localmente fixados em conjunto com o SharePoint RBS. Isso garantirá que todo o conteúdo de BLOB permanecerá localmente no dispositivo StorSimple e não será movido para o Microsoft Azure.
* Se você não armazenar os bancos de dados de conteúdo no dispositivo StorSimple, use as práticas recomendadas tradicionais de alta disponibilidade do SQL Server que ofereçam suporte a RBS. O clustering do SQL Server dá suporte a RBS, enquanto o espelhamento do SQL Server, não. 

> [!WARNING]
> Se você não tiver habilitado RBS, não é recomendável mover o banco de dados de conteúdo para o dispositivo StorSimple. Essa é uma configuração não testada.

## <a name="storsimple-adapter-for-sharepoint-installation"></a>Instalação do Adaptador StorSimple para SharePoint
Antes de instalar o adaptador StorSimple para SharePoint, você deve configurar o dispositivo StorSimple e certificar-se de que o farm do SharePoint Server e a instanciação do SQL Server atendam a todos os pré-requisitos. Este tutorial descreve os requisitos de configuração, bem como os procedimentos para instalar e atualizar o Adaptador StorSimple para SharePoint.

## <a name="configure-prerequisites"></a>Configurar pré-requisitos
Antes de instalar o adaptador StorSimple para SharePoint, garanta que o dispositivo StorSimple, o farm do SharePoint Server e a instanciação do SQL Server atendam a todos os pré-requisitos.

### <a name="system-requirements"></a>Requisitos do sistema
O Adaptador StorSimple para SharePoint funciona com os produtos de hardware e software a seguir:

* Sistema operacional com suporte – Windows Server 2008 R2 SP1, Windows Server 2012 ou Windows Server 2012 R2
* Versões do SharePoint com suporte – SharePoint Server 2010 ou SharePoint Server 2013
* Versões do SQL Server com suporte – SQL Server 2008 Enterprise Edition, SQL Server 2008 R2 Enterprise Edition ou SQL Server 2012 Enterprise Edition
* Dispositivos do StorSimple com suporte –  StorSimple 8000 Series, StorSimple 7000 Series ou StorSimple 5000 Series.

### <a name="storsimple-device-configuration-prerequisites"></a>Pré-requisitos de configuração do dispositivo StorSimple
O dispositivo StorSimple é um dispositivo de bloco e, como tal, requer um servidor de arquivos no qual os dados possam ser hospedados. É recomendável usar um servidor separado, em vez de um servidor existente do farm do SharePoint. Esse servidor de arquivos deve estar na mesma rede local (LAN) que o computador SQL Server que hospeda os bancos de dados de conteúdo.

> [!TIP]
> * Se você configurar seu farm do SharePoint para alta disponibilidade, deverá implantar o servidor de arquivos para alta disponibilidade também.
> * Se você não armazenar o banco de dados de conteúdo no dispositivo StorSimple, use práticas recomendadas tradicionais de alta disponibilidade que ofereçam suporte a RBS. O clustering do SQL Server dá suporte a RBS, enquanto o espelhamento do SQL Server, não. 


Certifique-se de que seu dispositivo StorSimple esteja configurado corretamente e que os volumes apropriados para dar suporte à implantação do SharePoint estão configurados e acessíveis do computador do SQL Server. Vá para [Implantar seu dispositivo StorSimple local](storsimple-8000-deployment-walkthrough-u2.md) se ainda não tiver implantado e configurado seu dispositivo StorSimple. Anote o endereço IP do dispositivo StorSimple; você precisará dele durante a instalação do Adaptador StorSimple para SharePoint.

Além disso, certifique-se de que o volume a ser usado para externalização do BLOB cumpra os seguintes requisitos:

* O volume deve ser formatado com um tamanho de unidade de alocação de 64 KB.
* O front-end da web (WFE) e os servidores de aplicativos devem poder acessar o volume por meio de um caminho de UNC (convenção de nomenclatura Universal).
* O farm de servidores do SharePoint deve ser configurado para gravar no volume.

> [!NOTE]
> Depois de instalar e configurar o adaptador, toda externalização do BLOB deve passar pelo dispositivo StorSimple (o dispositivo apresentará os volumes ao SQL Server e gerenciará os níveis de armazenamento). Você não pode usar nenhum outro destino para externalização do BLOB.


Se você planeja usar o Gerenciador de instantâneos StorSimple para capturar instantâneos dos dados do BLOB e do banco de dados, instale o Gerenciador de instantâneos StorSimple no servidor de banco de dados para que ele possa usar o Serviço Gravador SQL para implementar o Windows Volume Shadow Copy Service (VSS).

> [!IMPORTANT]
> O Gerenciador de instantâneos StorSimple não tem suporte para o Gravador VSS do SharePoint e não é possível tirar instantâneos consistentes com aplicativos de dados do SharePoint. Em um cenário do SharePoint, o Gerenciador de instantâneos StorSimple fornece somente backups consistentes com falhas.


## <a name="sharepoint-farm-configuration-prerequisites"></a>Pré-requisitos de configuração de farm do SharePoint
Certifique-se de que seu farm do SharePoint Server esteja configurado corretamente da seguinte maneira:

* Verifique se seu farm de servidores do SharePoint está em um estado íntegro e verifique o seguinte:
* Todos os servidores de aplicativos e WFE do SharePoint registrados no farm estão em execução e podem ser alcançados pelo ping do servidor no qual você instalará o Adaptador StorSimple para SharePoint.
* O serviço de Timer do SharePoint (SPTimerV3 ou SPTimerV4) está em execução em cada servidor WFE e servidor de aplicativos.
* O serviço de Timer do SharePoint e o pool de aplicativos do IIS em que o site de Administração Central do SharePoint está em execução tem privilégios administrativos.
* Certifique-se de que o IE ESC (Internet Explorer Enhanced Security Context) esteja desabilitado. Siga estas etapas para desabilitar o IE ESC:
  
  1. Feche todas as instâncias do Internet Explorer.
  2. Inicie o Gerenciador do Servidor.
  3. No painel esquerdo, clique em **Servidor Local**.
  4. No painel direito, ao lado de **Configuração de Segurança Aprimorada do IE**, clique em **Ativado**.
  5. Em **Administradores**, clique em **Desabilitado**.
  6. Clique em **OK**.

## <a name="remote-blob-storage-rbs-prerequisites"></a>Pré-requisitos do RBS (Remote BLOB Storage)
Certifique-se de que você esteja usando uma versão com suporte do SQL Server. Somente as versões a seguir têm suporte e podem usar RBS:

* SQL Server 2008 Enterprise Edition
* SQL Server 2008 R2 Enterprise Edition
* SQL Server 2012 Enterprise Edition

BLOBs podem ser externalizados somente nos volumes que o dispositivo StorSimple apresentar ao SQL Server. Não há suporte para outros destinos para externalização de BLOB.

Quando você tiver concluído todas as etapas de configuração de pré-requisitos, vá para [Instalar o Adaptador StorSimple para SharePoint](#install-the-storsimple-adapter-for-sharepoint).

## <a name="install-the-storsimple-adapter-for-sharepoint"></a>Instalar o Adaptador StorSimple para SharePoint
Use as seguintes etapas para instalar o Adaptador StorSimple para SharePoint. Se você estiver reinstalando o software, consulte [Atualizar ou reinstalar o Adaptador StorSimple para SharePoint](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint). O tempo necessário para a instalação depende do número total de bancos de dados do SharePoint no farm do SharePoint Server.

[!INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]

## <a name="configure-rbs"></a>Configurar RBS
Depois de instalar o Adaptador StorSimple para SharePoint, configure RBS conforme descrito no procedimento a seguir.

> [!TIP]
> O Adaptador StorSimple para SharePoint conecta-se à página Administração Central do SharePoint, permitindo ao RBS ser habilitado ou desabilitado em cada banco de dados de conteúdo no farm do SharePoint. No entanto, habilitar ou desabilitar RBS no banco de dados provoca uma redefinição do IIS, que, dependendo da configuração do farm, pode momentaneamente interromper a disponibilidade do WFE (front-end da Web) do SharePoint. (Fatores como o uso de um balanceador de carga de front-end, a carga de trabalho atual do servidor e assim por diante podem limitar ou eliminar essa interrupção). Para proteger os usuários contra uma interrupção, recomendamos habilitar ou desabilitar RBS somente durante uma janela de manutenção planejada.


[!INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]

## <a name="configure-garbage-collection"></a>Configurar a coleta de lixo
Quando objetos são excluídos de um site do SharePoint, eles não são automaticamente excluídos do volume de armazenamento RBS. Em vez disso, um programa assíncrono de manutenção em segundo plano exclui os BLOBs órfãos do repositório de arquivo. Os administradores do sistema podem agendar esse processo para ser executado periodicamente ou iniciá-lo sempre que necessário.

Este programa de manutenção (Microsoft.Data.SqlRemoteBlobs.Maintainer.exe) é instalado automaticamente em todos os servidores SharePoint WFE e servidores de aplicativos quando você habilita RBS. O programa é instalado no seguinte local: *unidade de inicialização*:\Program Files\Microsoft SQL Remote Blob Storage 10.50\Maintainer\

Para saber mais sobre como configurar e usar o programa de manutenção, consulte [Manter RBS no SharePoint Server 2013][8].

> [!IMPORTANT]
> O programa de manutenção do RBS utiliza muitos recursos. Você deve agendá-lo para execução apenas durante períodos de atividade leve no farm do SharePoint.


### <a name="delete-orphaned-blobs-immediately"></a>Excluir BLOBs órfãos imediatamente
Se você precisar excluir BLOBs órfãos imediatamente, pode usar as instruções a seguir. Observe que essas instruções são um exemplo de como isso pode ser feito em um ambiente do SharePoint 2013 com os seguintes componentes:

* O nome do banco de dados de conteúdo é WSS_Content.
* O nome do SQL Server é SHRPT13-SQL12\SHRPT13.
* O nome do aplicativo Web é SharePoint-80.

[!INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]

## <a name="upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint"></a>Atualizar ou reinstalar o Adaptador StorSimple para SharePoint
Use o procedimento a seguir para atualizar o servidor do SharePoint e, em seguida, reinstalar o adaptador StorSimple para SharePoint ou para simplesmente atualizar ou reinstalar o adaptador em um farm do SharePoint Server existente.

> [!IMPORTANT]
> Examine as seguintes informações antes de tentar atualizar o software do SharePoint e/ou atualizar ou reinstalar o Adaptador StorSimple para SharePoint:
> 
> * Todos os arquivos que foram movidos anteriormente para o armazenamento externo via RBS não estarão disponíveis até que a reinstalação seja concluída e o recurso RBS esteja habilitado novamente. Para limitar o impacto ao usuário, execute qualquer atualização ou reinstalação durante uma janela de manutenção planejada.
> * O tempo necessário para atualizar/reinstalar pode variar conforme o número total de bancos de dados do SharePoint no farm de servidores do SharePoint.
> * Após a conclusão da reinstalação/atualização, você precisa habilitar o RBS para os bancos de dados de conteúdo. Consulte [Configurar RBS](#configure-rbs) para saber mais.
> * Se você estiver configurando um RBS para um farm do SharePoint que tenha um número muito grande de bancos de dados (mais de 200), a página **Administração Central do SharePoint** poderá atingir o tempo limite. Se isso ocorrer, atualize a página. Isso não afeta o processo de configuração.


[!INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]

## <a name="storsimple-adapter-for-sharepoint-removal"></a>Remoção do Adaptador StorSimple para SharePoint
Os procedimentos a seguir descrevem como mover os BLOBs de volta para os bancos de dados de conteúdo do SQL Server e desinstalar o Adaptador StorSimple para SharePoint. 

> [!IMPORTANT]
> Você precisa mover os BLOBs de volta para os bancos de dados de conteúdo antes de desinstalar o software do adaptador.


### <a name="before-you-begin"></a>Antes de começar
Colete as seguintes informações antes de mover os dados de volta para os bancos de dados de conteúdo do SQL Server e iniciar o processo de remoção do adaptador:

* Os nomes de todos os bancos de dados para os quais o RBS está habilitado
* O caminho UNC do armazenamento de BLOB configurado

### <a name="move-the-blobs-back-to-the-content-databases"></a>Mover os BLOBs de volta para os bancos de dados de conteúdo
Antes de desinstalar o software Adaptador StorSimple para SharePoint, é necessário migrar todos os BLOBs que foram externalizados de volta para os bancos de dados de conteúdo do SQL Server. Se você tentar desinstalar o Adaptador StorSimple para SharePoint antes de mover todos os BLOBs de volta para os bancos de dados de conteúdo, a mensagem de aviso a seguir será exibida.

![Mensagem de aviso](./media/storsimple-adapter-for-sharepoint/sasp1.png)

#### <a name="to-move-the-blobs-back-to-the-content-databases"></a>Para mover os BLOBs de volta para os bancos de dados de conteúdo
1. Baixe cada um dos objetos externalizados.
2. Abra a página **Administração Central do SharePoint** e navegue até **Configurações do Sistema**.
3. Em **Azure StorSimple**, clique em **Configurar Adaptador StorSimple**.
4. Na página **Configurar Adaptador StorSimple**, clique no botão **Desabilitar** abaixo de cada um dos bancos de dados de conteúdo que deseja remover do armazenamento de BLOBs externo. 
5. Exclua os objetos do SharePoint e depois os carregue novamente.

Como alternativa, você pode usar o Microsoft `RBS Migrate()` cmdlet do PowerShell incluído com o SharePoint. Para saber mais, consulte [Migrar o conteúdo para dentro ou fora do RBS](https://technet.microsoft.com/library/ff628255.aspx).

Depois de mover os BLOBs de volta para o banco de dados de conteúdo, vá para a próxima etapa: [Desinstalar o adaptador](#uninstall-the-adapter).

### <a name="uninstall-the-adapter"></a>Desinstalar o adaptador
Depois de mover os BLOBs de volta para os bancos de dados de conteúdo do SQL Server, use uma das opções a seguir para desinstalar o Adaptador StorSimple para SharePoint.

#### <a name="to-use-the-installation-program-to-uninstall-the-adapter"></a>Para usar o programa de instalação para desinstalar o adaptador
1. Use uma conta com privilégios de administrador para fazer logon no servidor WFE (front-end da Web).
2. Clique duas vezes no instalador do Adaptador StorSimple para SharePoint. O Assistente de Instalação é iniciado.
   
    ![Assistente de instalação](./media/storsimple-adapter-for-sharepoint/sasp2.png)
3. Clique em **Avançar**. A página a seguir será exibida.
   
    ![Página de remoção do assistente de instalação](./media/storsimple-adapter-for-sharepoint/sasp3.png)
4. Clique em **Remover** para selecionar o processo de remoção. A página a seguir será exibida.
   
    ![Página de confirmação do assistente de instalação](./media/storsimple-adapter-for-sharepoint/sasp4.png)
5. Clique em **Remover** para confirmar a remoção. A página de andamento a seguir é exibida.
   
    ![Página de progresso do assistente de instalação](./media/storsimple-adapter-for-sharepoint/sasp5.png)
6. Quando a remoção estiver concluída, a página de conclusão será exibida. Clique em **Concluir** para fechar o Assistente de Instalação.

#### <a name="to-use-the-control-panel-to-uninstall-the-adapter"></a>Para usar o Painel de Controle de modo a desinstalar o adaptador
1. Abra o Painel de Controle e clique em **Programas e Recursos**.
2. Selecione **Adaptador StorSimple para SharePoint** e clique em **Desinstalar**.

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre o StorSimple](storsimple-overview.md).

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[3]: https://technet.microsoft.com/library/ff628583(v=office.14).aspx
[4]: https://technet.microsoft.com/library/ff628569(v=office.14).aspx
[5]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[8]: https://technet.microsoft.com/library/ff943565.aspx
