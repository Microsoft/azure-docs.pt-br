---
title: Migrar dados no StorSimple série 5000-7000 para um dispositivo série 8000 | Microsoft Docs
description: Fornece uma visão geral e os pré-requisitos do recurso de migração.
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/23/2018
ms.author: alkohli
ms.openlocfilehash: 967c03f3c4201bdcf1529fdda93717b6eb74e771
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60631604"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-8000-series-device"></a>Migrar dados do StorSimple série 5000-7000 para um dispositivo série 8000

> [!IMPORTANT]
> - Em 31 de julho de 2019, a série StorSimple 5000/7000 chegará ao status EOS (fim do suporte). É recomendável que os clientes da série StorSimple 5000/7000 migrem para uma das alternativas descritas no documento.
> - A migração atualmente é uma operação assistida. Se você pretende migrar dados de seu dispositivo StorSimple série 5000-7000 para um dispositivo da série 8000, precisa agendar a migração com o Suporte da Microsoft. O Suporte da Microsoft habilitará a migração para sua assinatura. Para saber mais, confira como [Abrir um tíquete de suporte](storsimple-8000-contact-microsoft-support.md).
> - Depois de emitir a solicitação de serviço, pode levar algumas semanas para executar o plano de migração e iniciá-la de verdade.
> - Antes de entrar em contato com o Suporte da Microsoft, reveja e conclua os [pré-requisitos de migração](#migration-prerequisites) indicados no artigo.

## <a name="overview"></a>Visão geral

Este artigo apresenta o recurso de migração que permite que os clientes da série StorSimple 5000-7000 migrem seus dados para o dispositivo físico StorSimple da série 8000 ou um dispositivo de nuvem 8010/8020. Este artigo também apresenta um link para um passo a passo, que pode ser baixado, das etapas necessárias para migrar dados de um dispositivo herdado da série 5000-7000 a um dispositivo físico da série 8000 ou de nuvem.

Este artigo é aplicável ao dispositivo da série 8000 local e ao Dispositivo de Nuvem StorSimple.


## <a name="migration-feature-versus-host-side-migration"></a>Recurso de migração comparado com a migração no lado do host

Você pode mover os dados usando o recurso de migração ou executando uma migração no lado do host. Esta seção descreve as especificações de cada método, incluindo os prós e contras. Use essas informações para descobrir qual método deve adotar para migrar seus dados.

O recurso de migração simula um processo de DR (recuperação de desastre) da série 7000/5000 para a série 8000. Esse recurso permite migrar os dados do formato da série 5000/7000 para o formato da série 8000 no Azure. O processo de migração é iniciado usando a ferramenta de migração do StorSimple. A ferramenta inicia o download e a conversão de metadados de backup no dispositivo da série 8000 e usa o backup mais recente para expor os volumes no dispositivo.

|      | Prós                                                                                                                                     |Contras                                                                                                                                                              |
|------|-------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | O processo de migração preserva o histórico de backups feitos na série 5000/7000.                                               | Quando os usuários tentam acessar os dados, a migração baixa os dados do Azure, incorrendo em custos de download de dados.                                     |
| 2.   | Nenhum dado é migrado no lado do host.                                                                                                     | O processo precisa de tempo de inatividade entre o início do backup e o backup mais recente que está sendo exposto na série 8000 (pode ser previsto usando a ferramenta de migração). |
| 3.   | Esse processo preserva todas as políticas, modelos de largura de banda, criptografia e outras configurações nos dispositivos da série 8000.                      | O acesso de usuário traz apenas os dados acessados pelos usuários e não realimenta todo o conjunto de dados.                                                  |
| 4.   | Esse processo exige tempo adicional para converter todos os backups mais antigos no Azure, que é feito de forma assíncrona sem afetar a produção | A migração somente pode ser feita no nível de configuração de nuvem.  Os volumes individuais em uma configuração de nuvem não podem ser migrados separadamente                       |

Uma migração do host permite a configuração da série 8000 independentemente e a cópia dos dados do dispositivo da série 5000/7000 para o dispositivo da série 8000. Isso equivale a migrar dados de um dispositivo de armazenamento para outro. Várias ferramentas, como Diskboss, robocopy, são usadas para copiar os dados.

|      | Prós                                                                                                                      |Contras                                                                                                                                                                                                      |
|------|---------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | A migração pode ser abordada em fases ou com base em cada volume.                                               | Os backups anteriores (obtidos na série 5000/7000) não estão disponíveis no dispositivo da série 8000.                                                                                                       |
| 2.   | Permite a consolidação de dados em uma conta de armazenamento do Azure.                                                       | O primeiro backup na nuvem na série 8000 levará mais tempo porque todos os dados na série 8000 precisam ter o backup feito no Azure.                                                                     |
| 3.   | Após a migração bem-sucedida, todos os dados ficam localmente no dispositivo. Não há nenhuma latência ao acessar os dados. | O consumo de armazenamento do Azure aumentará até que os dados sejam excluídos do dispositivo 5000/7000.                                                                                                        |
| 4.   |                                                                                                                           | Se o dispositivo da série 5000/7000 tem uma grande quantidade de dados, durante a migração os dados precisam ser baixados do Azure, o que vai gerar custos e latências relativos ao download de dados do Azure |

Este artigo se concentra somente no recurso de migração do dispositivo da série 5000/7000 para a série 8000. Para saber mais sobre a migração do lado do host, acesse [Migração de outros dispositivos de armazenamento](https://download.microsoft.com/download/9/4/A/94AB8165-CCC4-430B-801B-9FD40C8DA340/Migrating%20Data%20to%20StorSimple%20Volumes_09-02-15.pdf).

## <a name="migration-prerequisites"></a>Pré-requisitos de migração

Estes são os pré-requisitos de migração para o dispositivo da série 5000 ou 7000 herdado e o dispositivo StorSimple série 8000.

> [!IMPORTANT]
> Revise e conclua os pré-requisitos de migração antes de emitir uma solicitação de serviço para o Suporte da Microsoft.

### <a name="for-the-50007000-series-device-source"></a>Para o dispositivo série 5000/7000 (origem)

Antes de começar a migração, verifique se:

* Você tem o dispositivo de origem da série 5000 ou 7000; o dispositivo pode está ligado ou desligado.

    > [!IMPORTANT]
    > Recomendamos que você tenha acesso de série ao dispositivo durante o processo de migração. Caso haja problemas no dispositivo, o acesso de série pode ajudar a solucionar o problema.

* Seu dispositivo de origem da série 5000 ou 7000 está executando a versão 2.1.1.518 ou posterior do software. Não há suporte para versões anteriores.
* Para verificar a versão em execução na série 5000 ou 7000, veja o canto superior direito da interface de usuário da Web. Isso deve exibir a versão do software que o dispositivo está executando. Para a migração, a série 5000 ou 7000 deve estar executando a versão 2.1.1.518.

    ![Verificar a versão do software no dispositivo herdado](media/storsimple-8000-migrate-from-5000-7000/check-version-legacy-device1.png)

    * Se o seu dispositivo ligado não está executando a versão 2.1.1.518 ou posterior, atualize o sistema para a versão mínima necessária. Para obter instruções detalhadas, consulte [Atualizar seu sistema para v2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518).
    * Se você estiver executando a versão 2.1.1.518, acesse a interface da Web para ver se há notificações sobre falhas de restauração do registro. Se a restauração do registro falhou, execute a restauração do registro. Para saber mais, vá para [Como executar a restauração do registro](http://onlinehelp.storsimple.com/111_Appliance/2_User_Guides/1_Current_(v2.1.1)/1_Web_UI_User_Guide_WIP/2_Configuration/4_Cloud_Accounts/1_Cloud_Credentials#Restoring_Backup_Registry).
    * Se você tiver um dispositivo desligado que não estava executando a versão 2.1.1.518, faça failover em um dispositivo de substituição que esteja executando a versão 2.1.1.518. Para obter instruções detalhadas, consulte a recuperação de desastre do seu dispositivo StorSimple série 5000/7000.
    * Faça backup dos dados para seu dispositivo usando um instantâneo de nuvem.
    * Verifique se há outros trabalhos de backup ativos em execução no dispositivo de origem. Isso inclui trabalhos no host do Console de Proteção de Dados do StorSimple. Aguarde até que os trabalhos atuais sejam concluídos.


### <a name="for-the-8000-series-physical-device-target"></a>Para o dispositivo físico da série 8000 (destino)

Antes de começar a migração, verifique se:

* O dispositivo da série 8000 de destino está registrado e em execução. Para saber mais, confira [Como implantar seu dispositivo StorSimple com o serviço StorSimple Manager](storsimple-8000-deployment-walkthrough-u2.md).
* O dispositivo da série 8000 tem a Atualização 5 do StorSimple série 8000 instalada e executa a versão 6.3.9600.17845 ou posterior. Se o dispositivo não tem as últimas atualizações instaladas, você precisa instalá-las antes de prosseguir com a migração. Para saber mais, veja como [Instalar a atualização mais recente no dispositivo da série 8000](storsimple-8000-install-update-5.md).
* Sua assinatura do Azure está habilitada para a migração. Se sua assinatura não estiver habilitada, entre em contato com o Suporte da Microsoft para habilitar a migração na sua assinatura.

### <a name="for-the-80108020-cloud-appliance-target"></a>Para o dispositivo de nuvem 8010/8020 (destino)

Antes de começar a migração, verifique se:

* Seu dispositivo de destino de nuvem está registrado e em execução. Para saber mais, confira como [Implantar e gerenciar um Dispositivo de Nuvem StorSimple](storsimple-8000-cloud-appliance-u2.md).
* Seu dispositivo de nuvem está executando a Atualização 5 do StorSimple série 8000, versão de software 5 6.3.9600.17845. Se sua solução de nuvem não está executando a Atualização 5, crie um novo dispositivo de nuvem com a Atualização 5 antes de prosseguir com a migração. Para saber mais, confira como [Criar um dispositivo de nuvem 8010/8020](storsimple-8000-cloud-appliance-u2.md).

### <a name="for-the-computer-running-storsimple-migration-tool"></a>Para o computador que executa a ferramenta de migração do StorSimple

A ferramenta de migração do StorSimple é uma ferramenta de interface do usuário que permite migrar dados de um StorSimple série 5000 7000 para um dispositivo da série 8000. Para instalar a ferramenta de migração de StorSimple, use um computador que atenda aos requisitos a seguir.

O computador tem conectividade com a Internet e:

* Está executando o sistema operacional a seguir
    * Windows 10.
    * Windows Server 2012 R2 (ou superior) para instalar a ferramenta de migração do StorSimple.
* O .NET 4.5.2 foi instalado.
* Tem um mínimo de 5 GB de espaço livre para instalar e usar a ferramenta.

> [!TIP]
> Se seu dispositivo StorSimple está conectado a um host do Windows Server, você pode instalar a ferramenta de migração no computador host do Windows Server.

#### <a name="to-install-storsimple-migration-tool"></a>Para instalar a ferramenta de migração do StorSimple

Execute as etapas a seguir para instalar a ferramenta de migração do StorSimple em seu computador.

1. Copie a pasta _StorSimple8000SeriesMigrationTool_ em seu computador Windows. Verifique se a unidade em que o software foi copiado tem espaço suficiente.

    Abra o arquivo de configuração da ferramenta _StorSimple8000SeriesMigrationTool.exe.config_ na pasta. Aqui está o snippet de código do arquivo.
    
    ```xml
        <add key="UserName" value="username@xyz.com" />
        <add key="SubscriptionName" value="YourSubscriptionName" />
        <add key="SubscriptionId" value="YourSubscriptionId" />
        <add key="TenantId" value="YourTenantId" />
        <add key="ResourceName" value="YourResourceName" />
        <add key="ResourceGroupName" value="YourResourceGroupName" />

    ```
2. Edite os valores correspondentes às chaves e substitua por:

    * `UserName`: nome de usuário para fazer logon no portal do Azure.
    * `SubscriptionName and SubscriptionId`: nome e ID de assinatura do Azure. Na página inicial do seu serviço de Gerenciador de Dispositivos do StorSimple, em **Geral**, clique em **Propriedades**. Copie o nome e a ID da assinatura associados ao serviço.
    * `ResourceName`: nome do seu serviço do Gerenciador de Dispositivos StorSimple. Também aparece nas propriedades do serviço.
    * `ResourceGroup`: nome do grupo de recursos associado ao serviço do Gerenciador de Dispositivos do StorSimple no portal do Azure. Também aparece nas propriedades do serviço.
    ![Verifique as propriedades do serviço para o dispositivo de destino](media/storsimple-8000-migrate-from-5000-7000/check-service-properties1.png)
    * `TenantId`: ID de locatário do azure Active Directory no portal do Azure. Faça logon Microsoft Azure como administrador. No portal do Microsoft Azure, clique em **Azure Active Directory**. Em **Gerenciar**, clique em **Propriedades**. A ID de locatário é mostrada na caixa **ID do diretório**.
    ![Verificar a ID de locatário do Azure Active Directory](media/storsimple-8000-migrate-from-5000-7000/check-tenantid-aad.png)

3.  Salve as alterações feitas no arquivo de configuração.
4.  Execute o _StorSimple8000SeriesMigrationTool.exe_ para iniciar a ferramenta. Quando solicitado a fornecer credenciais, forneça as credenciais associadas à sua assinatura no portal do Azure. 
5.  A interface do usuário da ferramenta de migração do StorSimple é exibida.
  

## <a name="next-steps"></a>Próximas etapas
Baixe as instruções passo a passo sobre como [migrar dados de um StorSimple série 5000-7000 para um dispositivo da série 8000](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b).
