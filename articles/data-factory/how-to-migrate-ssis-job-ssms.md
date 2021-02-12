---
title: Migrar trabalhos do SSIS (SQL Server Integration Services local) para Azure Data Factory
description: Este artigo descreve como migrar trabalhos do SQL Server Integration Services (SSIS) para Azure Data Factory pipelines/atividades/gatilhos usando SQL Server Management Studio.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 4/7/2020
ms.openlocfilehash: 1072f915c5d8483676874422703820dc75719256
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98555739"
---
# <a name="migrate-sql-server-agent-jobs-to-adf-with-ssms"></a>Migrar trabalhos do SQL Server Agent para o ADF com o SSMS

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ao [migrar cargas de trabalho do SSIS (SQL Server Integration Services locais) para o SSIS no ADF](scenario-ssis-migration-overview.md), depois que os pacotes SSIS são migrados, você pode fazer a migração em lote de trabalhos de SQL Server Agent com o tipo de etapa de trabalho do pacote de SQL Server Integration Services para Azure data Factory (ADF) pipelines/atividades/agendamentos de agendamento por meio do **Assistente de migração de trabalho SSIS** do SQL Server Management Studio (SSMS).

Em geral, para trabalhos selecionados do SQL Agent com tipos de etapa de trabalho aplicáveis, o **Assistente de migração de trabalho do SSIS** pode:

- Mapeie a localização do pacote SSIS local para o qual os pacotes são migrados, que podem ser acessados pelo SSIS no ADF.
    > [!NOTE]
    > Só há suporte para o local do pacote do sistema de arquivos.
- Migre trabalhos aplicáveis com etapas de trabalho aplicáveis para os recursos do ADF correspondentes, como mostrado abaixo:

|Objeto de trabalho do SQL Agent  |Recurso do ADF  |Observações|
|---------|---------|---------|
|Trabalho do SQL Agent|pipeline     |O nome do pipeline será *gerado para \<job name>*. <br> <br> Os trabalhos internos do agente não são aplicáveis: <li> Trabalho de manutenção do servidor SSIS <li> syspolicy_purge_history <li> collection_set_ * <li> mdw_purge_data_ * <li> sysutility_ *|
|Etapa de trabalho do SSIS|Atividade executar pacote SSIS|<li> O nome da atividade será \<step name> . <li> A conta proxy usada na etapa de trabalho será migrada como autenticação do Windows desta atividade. <li> *As opções de execução* , exceto *usar tempo de execução de 32 bits* definidos na etapa de trabalho, serão ignoradas na migração. <li> A *verificação* definida na etapa de trabalho será ignorada na migração.|
|schedule      |agendar o gatilho        |Nome do gatilho de agendamento que será *gerado para \<schedule name>*. <br> <br> As opções abaixo na agenda de trabalho do SQL Agent serão ignoradas na migração: <li> Intervalo de segundo nível. <li> *Iniciar automaticamente quando o SQL Server Agent for iniciado* <li> *Iniciar sempre que as CPUs estiverem ociosas* <li> dia *útil* e fim de *semana*<time zone> <br> Abaixo estão as diferenças após o agendamento de trabalho do SQL Agent ser migrado para o gatilho de agenda do ADF: <li> O gatilho de agendamento do ADF de execução subsequente é independente do estado de execução da execução disparada por Antecedent. <li> A configuração de recorrência do gatilho de agendamento do ADF difere da frequência diária no trabalho do SQL Agent.|

- gere modelos de Azure Resource Manager (ARM) na pasta de saída local e implante em data factory diretamente ou mais tarde manualmente. Para obter mais informações sobre os modelos do Gerenciador de recursos do ADF, consulte [tipos de recurso Microsoft. datafactory](/azure/templates/microsoft.datafactory/allversions).

## <a name="prerequisites"></a>Pré-requisitos

O recurso descrito neste artigo requer SQL Server Management Studio versão 18,5 ou superior. Para obter a versão mais recente do SSMS, confira [Baixar o SSMS (SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms).

## <a name="migrate-ssis-jobs-to-adf"></a>Migrar trabalhos do SSIS para o ADF

1. No SSMS, no Pesquisador de objetos, selecione SQL Server Agent, selecione trabalhos, clique com o botão direito do mouse e selecione **migrar trabalhos do SSIS para o ADF**.
![A captura de tela mostra SQL Server Management Studio pesquisador de objetos, no qual você pode selecionar trabalhos e migrar os trabalhos s s para um D F.](media/how-to-migrate-ssis-job-ssms/menu.png)

1. Entre no Azure, selecione assinatura do Azure, Data Factory e Integration Runtime. O armazenamento do Azure é opcional, que será usado na etapa de mapeamento de local do pacote se os trabalhos do SSIS a serem migrados tiverem pacotes do sistema de arquivos SSIS.
![AdicionarMenu](media/how-to-migrate-ssis-job-ssms/step1.png)

1. Mapeie os caminhos de pacotes do SSIS e arquivos de configuração em trabalhos do SSIS para caminhos de destino em que os pipelines migrados podem acessar. Nesta etapa de mapeamento, você pode:

    1. Selecione uma pasta de origem e, em seguida, **Adicionar mapeamento**.
    1. Caminho da pasta de origem da atualização. Caminhos válidos são caminhos de pasta ou caminhos de pasta pai de pacotes.
    1. Caminho da pasta de destino da atualização. O padrão é o caminho relativo para a conta de armazenamento padrão, que é selecionada na etapa 1.
    1. Exclua um mapeamento selecionado por meio de **mapeamento de exclusão**.
![Captura de tela mostra a página Mapear pacote S I S e caminhos de configuração, onde você pode adicionar mapeamento. ](media/how-to-migrate-ssis-job-ssms/step2.png)
 ![ Captura de tela mostra a página Mapear pacotes s I S e caminhos de configuração, onde você pode atualizar os caminhos da pasta de origem e de destino.](media/how-to-migrate-ssis-job-ssms/step2-1.png)

1. Selecione os trabalhos aplicáveis a serem migrados e defina as configurações da *atividade de pacote SSIS executada* correspondente.

    - A *configuração padrão* aplica-se a todas as etapas selecionadas por padrão. Para obter mais informações sobre cada propriedade, consulte a *guia Configurações* da [atividade executar pacote SSIS](how-to-invoke-ssis-package-ssis-activity.md) quando o local *do pacote é sistema de arquivos (pacote)*.
    ![Captura de tela mostra a página Select S I S Jobs, em que é possível definir as configurações da atividade de pacote SSIS executada correspondente.](media/how-to-migrate-ssis-job-ssms/step3-1.png)
    - *Configuração de etapa*, defina a configuração para uma etapa selecionada.
        
        **Aplicar configuração padrão**: o padrão é selecionado. Desmarque para definir a configuração somente para a etapa selecionada.  
        Para obter mais informações sobre outras propriedades, consulte a *guia Configurações* da [atividade executar pacote SSIS](how-to-invoke-ssis-package-ssis-activity.md) quando o local *do pacote é sistema de arquivos (pacote)*.
    ![Captura de tela mostra a página Select s I S Jobs, em que é possível aplicar as configurações padrão.](media/how-to-migrate-ssis-job-ssms/step3-2.png)

1. Gere e implante o modelo ARM.
    1. Selecione ou insira o caminho de saída para os modelos ARM dos pipelines do ADF migrados. A pasta será criada automaticamente se não existir.
    2. Selecione a opção de **implantar modelos de ARM em seu data Factory**:
        - O padrão é desmarcado. Você pode implantar modelos ARM gerados mais tarde manualmente.
        - Selecione para implantar modelos ARM gerados para data factory diretamente.
    ![Captura de tela mostra a página Configurar migração, na qual você pode selecionar ou inserir o caminho de saída para os modelos ARM dos pipelines do ADF migrados e selecionar a opção de implantar modelos de ARM em seu data factory.](media/how-to-migrate-ssis-job-ssms/step4.png)

1. Migre e, em seguida, verifique os resultados.
![Captura de tela mostra a página resultado da migração, que exibe o progresso da migração.](media/how-to-migrate-ssis-job-ssms/step5.png)

## <a name="next-steps"></a>Próximas etapas

[Executar e monitorar o pipeline](how-to-invoke-ssis-package-ssis-activity.md)