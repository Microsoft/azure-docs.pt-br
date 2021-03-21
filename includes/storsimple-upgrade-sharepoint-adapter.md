---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 3467a5d5daa300f82c7b81641ab7e262259d9285
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95553932"
---
### <a name="upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-storsomple-adapter-for-sharepoint"></a>Atualizar o SharePoint 2010 para SharePoint 2013 e instalar o Adaptador StorSimple para SharePoint
> [!IMPORTANT]
> Todos os arquivos que foram movidos anteriormente para o armazenamento externo via RBS não estarão disponíveis até que a atualização seja concluída e o recurso RBS esteja habilitado novamente. Para limitar o impacto ao usuário, execute qualquer atualização ou reinstalação durante uma janela de manutenção planejada.
> 
> 

#### <a name="to-upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-adapter"></a>Para atualizar o SharePoint 2010 para SharePoint 2013 e instalar o adaptador
1. No farm do SharePoint 2010, observe o caminho do armazenamento de BLOB para os BLOBs externalizados e os bancos de dados de conteúdo para os quais o RBS está habilitado. 
2. Instale e configure o novo farm do SharePoint 2013. 
3. Mova os bancos de dados, aplicativos e coleções de site do farm do SharePoint 2010 para o novo farm do SharePoint 2013. Para obter instruções, vá para [Visão geral do processo de atualização para o SharePoint 2013](/SharePoint/upgrade-and-update/overview-of-the-upgrade-process).
4. Instale o Adaptador StorSimple para SharePoint no novo farm. Para ver os procedimentos, vá para [Instalar o Adaptador do StorSimple para SharePoint](#install-the-storsimple-adapter-for-sharepoint) .
5. Usando as informações que você anotou na etapa 1, habilite o RBS para o mesmo conjunto de bancos de dados de conteúdo e forneça o mesmo caminho de armazenamento de BLOB que foi usado na instalação do SharePoint 2010. Para ver os procedimentos, vá para [Configurar RBS](#configure-rbs) . Depois de concluir essa etapa, os arquivos externalizados anteriormente devem poder ser acessados no novo farm. 

### <a name="upgrade-the-storsimple-adapter-for-sharepoint"></a>Atualizar o Adaptador StorSimple para SharePoint
> [!IMPORTANT]
> Você deve agendar essa atualização para ocorrer durante uma janela de manutenção planejada pelos seguintes motivos:
> 
> * O conteúdo externalizado anteriormente não estará disponível até que o adaptador seja reinstalado.
> * Qualquer conteúdo carregado no site depois da desinstalação da versão anterior do Adaptador StorSimple para SharePoint, mas antes da instalação da nova versão, será armazenado no banco de dados de conteúdo. Você precisará mover esse conteúdo para o dispositivo StorSimple depois de instalar o novo adaptador. Você pode usar o cmdlet do Microsoft `RBS Migrate()` PowerShell incluído com o SharePoint para migrar o conteúdo. Para saber mais, consulte [Migrar o conteúdo para dentro ou fora do RBS](/previous-versions/office/sharepoint-foundation-2010/ff628255(v=office.14)). 
> 
> 

#### <a name="to-upgrade-the-storsimple-adapter-for-sharepoint"></a>Para atualizar o Adaptador StorSimple para SharePoint
1. Desinstale a versão anterior do Adaptador StorSimple para SharePoint.
   
   > [!NOTE]
   > Isso desabilitará automaticamente o RBS nos bancos de dados de conteúdo. No entanto, os BLOBs existentes permanecerão no dispositivo StorSimple. Como o RBS está desabilitado e os BLOBs não migraram de volta para os bancos de dados de conteúdo, todas as solicitações para esses BLOBs falharão. 
   > 
   > 
2. Instale o novo Adaptador StorSimple para SharePoint. O novo adaptador reconhecerá automaticamente os bancos de dados de conteúdo habilitados ou desabilitados para RBS anteriormente e usará as configurações anteriores.