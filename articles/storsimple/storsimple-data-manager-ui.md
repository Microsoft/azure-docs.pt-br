---
title: "Interface do usuário do Gerenciador de Dados do Microsoft Azure StorSimple | Microsoft Docs"
description: "Descreve como usar a interface do usuário do serviço Gerenciador de Dados do StorSimple (visualização privada)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/22/2016
ms.author: vidarmsft
ms.openlocfilehash: 53a8599df2c647613122cd791b680e2e658586b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-using-the-storsimple-data-manager-service-ui-private-preview"></a>Gerenciar usando a interface do usuário do serviço Gerenciador de Dados do StorSimple (visualização privada)

Este artigo explica como você pode usar a interface do usuário do Gerenciador de Dados do StorSimple para executar a transformação em dados que residem em dispositivos das séries StorSimple 8000. Os dados transformados podem ser consumidos por outros serviços do Azure, como os Serviços de Mídia do Azure, Azure HDInsight, Azure Machine Learning e Azure Search. 


## <a name="use-storsimple-data-transformation"></a>Usar a transformação de dados do StorSimple

O Gerenciador de Dados do StorSimple é um recurso em que a Transformação de Dados pode ser instanciada. O serviço Transformação de Dados permite mover dados do dispositivo StorSimple local para blobs no armazenamento do Azure. Portanto, no fluxo de trabalho, você precisa especificar os detalhes sobre seu dispositivo StorSimple e os dados de interesse que você deseja mover para a conta de armazenamento.

### <a name="create-a-storsimple-data-manager-service"></a>Criar um serviço Gerenciador de Dados do StorSimple

Execute as etapas a seguir para criar um serviço Gerenciador de Dados do StorSimple.

1. Para criar um serviço Gerenciador de Dados do StorSimple Manager, vá para [https://aka.ms/HybridDataManager](https://aka.ms/HybridDataManager)

2. Clique no ícone **+** e procure o Gerenciador de Dados do StorSimple. Clique em seu serviço Gerenciador de Dados do StorSimple e então clique em **Criar**.

3. Se sua assinatura está habilitada para a criação desse serviço, você verá a folha a seguir.

    ![Criar um recurso Gerenciadores de Dados do StorSimple](./media/storsimple-data-manager-ui/create-new-data-manager-service.png)

4. Insira as entradas e clique em **Criar**. O local especificado deve ser aquele que hospeda suas contas de armazenamento e seu serviço StorSimple Manager. Atualmente, há suporte somente para as regiões Oeste dos EUA e Europa Ocidental. Portanto, seu serviço StorSimple Manager, o serviço Gerenciador de Dados e a conta de armazenamento devem estar na região com suporte anterior. Demora cerca de um minuto para criar o serviço.

### <a name="create-a-data-transformation-job-definition"></a>Criar uma definição de trabalho de transformação de dados

Em um serviço Gerenciador de Dados do StorSimple, você precisa criar uma definição de trabalho de transformação de dados. Uma definição de trabalho especifica os detalhes dos dados que você está interessado em mudar para uma conta de armazenamento no formato nativo. 

Execute as seguintes etapas para criar uma nova definição de trabalho de transformação de dados.

1.  Navegue até o serviço que você criou. Clique em **+ Definição de Trabalho**.

    ![Clique em +Definição de Trabalho](./media/storsimple-data-manager-ui/click-add-job-definition.png)

2. A folha da nova definição de trabalho é aberta. Dê um nome à sua definição de trabalho e clique em **Origem**. Na folha **Configurar fonte de dados**, especifique os detalhes do seu dispositivo StorSimple e os dados de interesse.

    ![Criar definição de trabalho](./media/storsimple-data-manager-ui//create-new-job-deifnition.png)

3. Como esse é um novo serviço Gerenciador de Dados, não há nenhum repositório de dados configurado. Para adicionar o StorSimple Manager como um repositório de dados, clique em **Adicionar novo** na lista suspensa do repositório de dados e clique em **Adicionar Repositório de Dados**.

4. Escolha **StorSimple série 8000 Manager** como o tipo de repositório e insira as propriedades de seu **StorSimple Manager**. Para o campo **Id de Recurso**, você precisa inserir o número antes do **:** na chave do registro do seu gerenciador do StorSimple.

    ![Criar a fonte de dados](./media/storsimple-data-manager-ui/create-new-data-source.png)

5.  Toque em **OK** quando terminar. Isso salva seu repositório de dados e o StorSimple Manager pode ser reutilizado em outras definições de trabalho sem a inserção desses parâmetros novamente. Demora alguns segundos depois de clicar em **OK** para que o StorSimple Manager apareça na lista suspensa.

6.  Na folha **Configurar fonte de dados**, insira o nome do dispositivo e o nome do volume que possui os dados de interesse.

7.  Na subseção **Filtro**, insira o diretório raiz que contém os dados de interesse (esse campo deve começar com um `\`). Você também pode adicionar qualquer filtro de arquivo aqui.

8.  O serviço de transformação de dados funciona nos dados que são passados para o Azure por meio de instantâneos. Ao executar esse trabalho, você pode optar por fazer um backup sempre que esse trabalho for executado (para trabalhar em dados mais recentes) ou usar o último backup existente na nuvem (se você estiver trabalhando em alguns dados arquivados).

    ![Novos detalhes da fonte de dados](./media/storsimple-data-manager-ui/new-data-source-details.png)

9. Em seguida, as configurações de destino precisarão ser configuradas. Há dois tipos de destinos com suporte – contas do Armazenamento do Azure e dos Serviços de Mídia do Azure. Escolha contas de armazenamento para colocar arquivos em blobs na conta. Escolha a conta dos serviços de mídia para colocar os arquivos nos ativos dessa conta. Novamente, precisamos adicionar um repositório. No menu suspenso, selecione **Adicionar novo** e **Definir configurações**.

    ![Criar coletor de dados](./media/storsimple-data-manager-ui/create-new-data-sink.png)

10. Aqui, você pode selecionar o tipo de repositório que deseja adicionar e os outros parâmetros associados ao repositório. Em ambos os casos, uma fila de armazenamento é criada quando o trabalho é executado. Essa fila é populada com mensagens sobre blobs transformados à medida que elas estejam prontas. O nome dessa fila é igual ao nome da definição do trabalho. Se você selecionar **Serviços de Mídia** como o tipo de repositório, também poderá inserir credenciais da conta de armazenamento onde a fila é criada.

    ![Novos detalhes do coletor de dados](./media/storsimple-data-manager-ui/new-data-sink-details.png)

11. Depois de adicionar o repositório de dados (que leva alguns segundos), você encontrará o repositório na lista suspensa no **Nome da conta de destino**.  Escolha o destino de que você precisa.

12. Clique em **OK** para criar a definição de trabalho. A definição de trabalho está configurada. Você pode usar essa definição de trabalho várias vezes por meio da interface do usuário.

    ![Adicionar nova definição de trabalho](./media/storsimple-data-manager-ui/add-new-job-definition.png)

### <a name="run-the-job-definition"></a>Executar definição de trabalho

Sempre que você precisar mover dados do StorSimple para a conta de armazenamento especificada na definição de trabalho, será necessário invocá-los. Há alguma flexibilidade na alteração dos parâmetros sempre que você invoca o trabalho. As etapas são as seguintes:

1. Selecione o serviço Gerenciador de Dados do StorSimple e vá para **Monitoramento**. Clique em **Executar Agora**.

    ![Definição de trabalho de gatilho](./media/storsimple-data-manager-ui/run-now.png)

2. Escolha a definição de trabalho que você deseja executar. Clique em **Configurações de execução** para modificar as configurações que você talvez queira alterar para esta execução do trabalho.

    ![Executar configurações do trabalho](./media/storsimple-data-manager-ui/run-settings.png)

3. Clique em **OK** e, em seguida, clique em **Executar** para iniciar seu trabalho. Para monitorar esse trabalho, vá para a página **Trabalhos** em seu Gerenciador de Dados do StorSimple.

    ![Status e a lista de trabalhos](./media/storsimple-data-manager-ui/jobs-list-and-status.png)

4. Além do monitoramento na folha **Trabalhos**, você também pode escutar a fila de armazenamento para onde uma mensagem é adicionada toda vez que um arquivo é movido do StorSimple para a conta de armazenamento.


## <a name="next-steps"></a>Próximas etapas

[Usar o SDK do .NET para iniciar trabalhos do Gerenciador de Dados do StorSimple](storsimple-data-manager-dotnet-jobs.md).