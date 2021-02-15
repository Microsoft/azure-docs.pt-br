---
title: Interface do usuário do Microsoft Azure StorSimple Gerenciador de Dados
description: Saiba como você pode usar a interface do usuário do Gerenciador de Dados do StorSimple para transformar os dados que residem nos dispositivos da série StorSimple 8000.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 802f732e8d62f5df861be525316b3c31ab4d0655
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94957921"
---
# <a name="manage-the-storsimple-data-manager-service-in-azure-portal"></a>Gerenciar o serviço do Gerenciador de Dados do StorSimple no portal do Azure

Este artigo explica como você pode usar a interface do usuário do Gerenciador de Dados do StorSimple para transformar os dados que residem em dispositivos das séries StorSimple 8000. Os dados transformados podem ser consumidos por outros serviços do Azure, como os serviços de mídia do Azure, o Azure HDInsight, o Azure Machine Learning e o Azure Pesquisa Cognitiva.


## <a name="use-storsimple-data-transformation"></a>Usar a transformação de dados do StorSimple

O Gerenciador de Dados do StorSimple é um recurso onde a Transformação de Dados é instanciada. O serviço de transformação de dados permite transformar os dados do formato do StorSimple para o formato nativo em blobs ou arquivos do Azure. Para transformar os dados de formato nativo do StorSimple, você precisa especificar os detalhes sobre o dispositivo da série StorSimple 8000 e os dados de interesse que você deseja transformar.

### <a name="create-a-storsimple-data-manager-service"></a>Criar um serviço Gerenciador de Dados do StorSimple

Execute as etapas a seguir para criar um serviço Gerenciador de Dados do StorSimple.

1. Use suas credenciais de conta da Microsoft para fazer logon no [portal do Azure](https://portal.azure.com/).

2. Clique em **+ Criar um recurso** e pesquise o Gerenciador de Dados do StorSimple.

    ![Criar um serviço Gerenciador de Dados do StorSimple 1](./media/storsimple-data-manager-ui/create-service-1.png)

3. Clique no Gerenciador de Dados do StorSimple e clique em **Criar**.
    
    ![Criar um serviço Gerenciador de Dados do StorSimple 2](./media/storsimple-data-manager-ui/create-service-3.png)

3. Para o novo serviço, especifique o seguinte:

   1. Fornecer um **nome de serviço** único para o seu Gerenciador de Dados do StorSimple. Este é um nome amigável que pode ser usado para identificar o serviço. O nome pode ter entre 3 e 24 caracteres que podem ser letras, números e hífens. O nome deve começar e terminar com uma letra ou um número.

   2. Escolha uma **assinatura** na lista suspensa. A assinatura está vinculada à sua conta de cobrança. Este campo é preenchido automaticamente (e não é selecionável) se você tiver apenas uma assinatura.

   3. Escolha um grupo de recursos existente ou crie um novo grupo. Para obter mais informações, veja [Grupos de recursos do Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).

   4. Especifique o **Local** para o serviço que hospeda as contas de armazenamento e o seu serviço do Gerenciador de Dados do StorSimple. O seu serviço do Gerenciador de Dispositivos do StorSimple, serviço do Gerenciador de Dados, e a conta de armazenamento associada devem ficar nas regiões com suporte.
    
   5. Para obter um link para esse serviço no seu painel, selecione **Fixar no painel**.
    
   6. Clique em **Criar**.

      ![Criar um serviço Gerenciador de Dados do StorSimple 3](./media/storsimple-data-manager-ui/create-service-4.png)

A criação do serviço leva alguns minutos. Você vê uma notificação depois que o serviço é criado com êxito e o novo serviço é exibido.

### <a name="create-a-data-transformation-job-definition"></a>Criar uma definição de trabalho de transformação de dados

Em um serviço Gerenciador de Dados do StorSimple, você precisa criar uma definição de trabalho de transformação de dados. Uma definição de trabalho especifica os detalhes dos dados do StorSimple que você está interessado em mudar para uma conta de armazenamento no formato nativo. Depois de criar uma definição de trabalho, você pode executar o trabalho novamente com configurações diferentes de runtime.

Execute as etapas a seguir para criar uma definição de trabalho.

1. Navegue até o serviço que você criou. Acesse **Gerenciamento > Definições de tarefa**.

2. Clique em **+ definição de trabalho**.

    ![Clique em +Definição de Trabalho](./media/storsimple-data-manager-ui/create-job-definition-1.png)

3. Forneça um nome para a sua definição de trabalho. O nome deve ter entre 3 e 63 caracteres. O nome pode conter letras maiúsculas e minúsculas, números e hifens.

4. Especifique um local onde o seu trabalho seja executado. Esse local pode ser diferente do local onde o serviço é implantado.

5. Clique em **Fonte** para especificar o repositório dos dados de origem.

    ![Configurar repositório dos dados de origem](./media/storsimple-data-manager-ui/create-job-definition-2.png)

6. Como esse é um novo serviço Gerenciador de Dados, não há nenhum repositório de dados configurado. Em **Configurar fonte de dados**, especifique os detalhes do dispositivo da série StorSimple 8000 e os dados de interesse.

   Para adicionar o Gerenciador de Dispositivos do StorSimple como um repositório de dados, clique em **Adicionar novo** na lista suspensa do repositório de dados e clique em **Adicionar Repositório de Dados**.

    ![Adicionar novo repositório de dados](./media/storsimple-data-manager-ui/create-job-definition-3.png)
  
   1. Escolha o **Gerenciador do StorSimple série 8000** como o tipo de repositório de dados.
    
   2. Insira um nome amigável para o seu repositório de dados de origem.
    
   3. Na lista suspensa, escolha uma assinatura associada ao seu serviço de Gerenciador de Dispositivos do StorSimple.
    
   4. Forneça o nome do Gerenciador de Dispositivos do StorSimple para o **Recurso**.

   5. Insira a chave de **Criptografia dos dados de serviço** para o serviço do Gerenciador de Dispositivos do StorSimple. 

      ![Configurar repositório dos dados de origem 1](./media/storsimple-data-manager-ui/create-job-definition-4.png)

      Toque em **OK** quando terminar. Isso economiza seu repositório de dados. Reutilize esse Gerenciador de Dispositivos do StorSimple em outras definições de trabalho sem digitar esses parâmetros novamente. Levará alguns segundos depois de clicar em **Ok** para o repositório de dados de origem recém-criado aparecer no menu suspenso.

7. Na lista suspensa para **Repositório de dados**, selecione o repositório de dados que você criou. 

   1. Digite o nome do dispositivo StorSimple 8000 series que contém os dados de interesse.

   2. Especifique o nome do volume que reside no dispositivo do StorSimple com seus dados de interesse.

   3. Na subseção do **Filtro**, insira o diretório raiz que contém os dados de interesse no formato _\MyRootDirectory\Data_. Letras de unidade como _\C:\Data_ não têm suporte. Você também pode adicionar qualquer filtro de arquivo aqui.

   4. O serviço de transformação de dados só funciona no instantâneo mais recente dos dados que são enviados para o Azure.

   5. Clique em **OK**.

      ![Configurar repositório dos dados de origem 2](./media/storsimple-data-manager-ui/create-job-definition-8.png)

8. Em seguida, o repositório de dados de destino precisa ser configurado. Escolha contas de armazenamento para colocar arquivos em blobs na conta. No menu suspenso, selecione **Adicionar novo** e **Definir configurações**.

9. Selecione o tipo de repositório que deseja adicionar e os outros parâmetros associados ao repositório.

    Se você selecionar um destino de tipo de conta de armazenamento, você pode especificar um nome amigável, assinatura (escolha a mesma do serviço ou outro) e uma conta de armazenamento.
        ![Configurar o repositório dos dados de destino 1](./media/storsimple-data-manager-ui/create-job-definition-10.png)

    Uma fila de armazenamento é criada quando o trabalho é executado. Essa fila é populada com mensagens sobre blobs transformados à medida que elas estejam prontas. O nome dessa fila é igual ao nome da definição do trabalho.
    
10. Depois de adicionar o repositório de dados, aguarde alguns minutos.
    
    1. Selecione o repositório que você criou como o destino na lista suspensa no **Nome da conta de destino**.

    2. Escolha o tipo de armazenamento como blobs ou arquivos. Especifique o nome do contêiner de armazenamento onde residem os dados transformados. Clique em **OK**.

        ![Configure a conta de armazenamento do repositório de dados de destino](./media/storsimple-data-manager-ui/create-job-definition-16.png)

11. Você também pode verificar a opção de apresentar uma estimativa de duração do trabalho antes de executá-lo. Clique em **OK** para criar a definição de trabalho. A definição de trabalho agora está completa. Você pode usar essa definição de trabalho várias vezes por meio da interface do usuário com diferentes configurações de execução.

    ![Concluir a definição de trabalho](./media/storsimple-data-manager-ui/create-job-definition-13.png)

    A definição de trabalho recém-criada é adicionada à lista de definições de trabalho para este serviço.

### <a name="run-the-job-definition"></a>Executar definição de trabalho

Sempre que você precisar mover dados do StorSimple para a conta de armazenamento especificada na definição de trabalho, você precisará executá-los. Em runtime, alguns parâmetros podem ser especificados de forma diferente. As etapas são as seguintes:

1. Selecione o serviço do Gerenciador de Dados do StorSimple e acesse **Gerenciamento > Definições de trabalho**. Selecione e clique na definição de trabalho que você deseja executar.
     
     ![Iniciar execução de trabalho 1](./media/storsimple-data-manager-ui/start-job-run1.png)

2. Clique em **executar agora**.
     
     ![Iniciar execução de trabalho 2](./media/storsimple-data-manager-ui/start-job-run2.png)

3. Clique em **Configurações de execução** para modificar as configurações que você talvez queira alterar para esta execução do trabalho. Clique em **OK** e, em seguida, clique em **Executar** para iniciar seu trabalho.

    ![Iniciar execução de trabalho 3](./media/storsimple-data-manager-ui/start-job-run3.png)

4. Para monitorar esse trabalho, acesse **Trabalhos** no Gerenciador de Dados do StorSimple. Além do monitoramento na folha **Trabalhos**, você também pode escutar a fila de armazenamento para onde uma mensagem é adicionada toda vez que um arquivo é movido do StorSimple para a conta de armazenamento.

    ![Iniciar execução de trabalho 4](./media/storsimple-data-manager-ui/start-job-run4.png)

### <a name="view-logs-after-job-completion"></a>Exibir logs após a conclusão do trabalho

Após a conclusão de um trabalho, você poderá exibir o status do trabalho. O status do trabalho pode ser **bem-sucedido**, **parcialmente bem-sucedido** e **com falha**. Você pode exibir a lista de arquivos que foram copiados com êxito e os arquivos que falharam ao serem copiados. Essas listas estão disponíveis em um contêiner chamado **"storsimple-Data-Manager-joblogs"** em sua conta de armazenamento de destino. Nesse contêiner, você pode procurar uma pasta com o mesmo nome que a definição de trabalho. Dentro disso, uma pasta será criada para cada execução de trabalho que conterá suas listas. O nome dessa pasta será o GUID do trabalho, que pode ser obtido na página de detalhes do trabalho. Como alternativa, na maioria dos casos você verá um link para os logs de cópia na própria página de trabalhos.
Há dois conjuntos de arquivos CSV que serão exibidos nessa pasta. Todos os arquivos que começam com **copiedfilelist...** conterão a lista de arquivos copiados com êxito. Todos os arquivos que começam com **failedfilelist...** contêm arquivos que não puderam ser copiados, juntamente com uma mensagem de erro.


## <a name="next-steps"></a>Próximas etapas

[Usar o SDK do .NET para iniciar trabalhos do Gerenciador de Dados do StorSimple](storsimple-data-manager-dotnet-jobs.md).