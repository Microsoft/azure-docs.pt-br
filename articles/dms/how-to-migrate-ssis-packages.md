---
title: Reimplantar pacotes SSIS em um banco de dados individual SQL
titleSuffix: Azure Database Migration Service
description: Saiba como migrar ou reimplantar SQL Server Integration Services pacotes e projetos no banco de dados SQL do Azure usando o serviço de migração de banco de dados do Azure e Assistente de Migração de Dados.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: e42b5ab2bbdebb2584b38531bd6458a98563ef42
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643746"
---
# <a name="redeploy-ssis-packages-to-azure-sql-database-with-azure-database-migration-service"></a>Reimplantar pacotes SSIS no banco de dados SQL do Azure com o serviço de migração de banco de dados Azure

Se você usar SQL Server Integration Services (SSIS) e quiser migrar seus projetos/pacotes do SSIS do SSISDB de origem hospedado por SQL Server para o SSISDB de destino armazenado pelo banco de dados SQL do Azure, você poderá reimplantá-los usando o assistente de implantação do Integration Services. Você pode iniciar o Assistente de dentro do SSMS (SQL Server Management Studio).

Se a versão do SSIS que você usa for anterior a 2012, antes de reimplantar os projetos/pacotes do SSIS no modelo de implantação de projeto, primeiro será necessário convertê-los usando o 	Assistente de Conversão de Projetos do Integration Services, que também pode ser iniciado pelo SSMS. Para obter mais informações, consulte o artigo [Converter projetos para o modelo de implantação de projeto](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#convert).

> [!NOTE]
> O serviço de migração de banco de dados do Azure (DMS) atualmente não oferece suporte à migração de um SSISDB de origem para o banco de dados SQL do Azure, mas você pode reimplantar seus projetos/pacotes do SSIS usando o processo a seguir.

Neste artigo, você aprenderá como:
> [!div class="checklist"]
>
> * Avaliar projetos/pacotes do SSIS de origem.
> * Migrar projetos/pacotes do SSIS para o Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir essas etapas, você precisa:

* SSMS versão 17.2 ou posterior.
* Uma instância de servidor de banco de dados de destino para hospedar o SSISDB. Se você ainda não tiver um, crie um [SQL Server lógico](../azure-sql/database/logical-servers.md) (sem um banco de dados) usando o portal do Azure navegando até o [formulário](https://ms.portal.azure.com/#create/Microsoft.SQLServer)SQL Server (somente servidor lógico).
* O SSIS deve ser provisionado em Azure Data Factory (ADF) contendo Azure-SSIS Integration Runtime (IR) com o arquivo de destino SSISDB hospedado pelo banco de dados SQL (conforme descrito no artigo [provisionar o Azure-SSIS Integration Runtime no Azure data Factory](../data-factory/tutorial-deploy-ssis-packages-azure.md)).

## <a name="assess-source-ssis-projectspackages"></a>Avaliar projetos/pacotes do SSIS de origem

Embora a avaliação do SSISDB de origem ainda não esteja integrada ao DMA (Assistente de Migração de banco de dados) ou ao serviço de migração de banco de dados do Azure (DMS), seus projetos/pacotes do SSIS serão avaliados/validados à medida que forem reimplantados no SSISDB de destino hospedado pelo banco de dados SQL do Azure.

## <a name="migrate-ssis-projectspackages"></a>Migrar projetos/pacotes do SSIS

Para migrar projetos/pacotes do SSIS para o banco de dados SQL do Azure, execute as etapas a seguir.

1. Abra o SSMS e, em seguida, selecione **Opções** para exibir a caixa de diálogo **Conectar ao Servidor**.

2. Na guia **logon** , especifique as informações necessárias para se conectar ao servidor que hospedará o SSISDB de destino.

    ![Guia Logon do SSIS](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)

3. Na guia **Propriedades da Conexão**, na caixa de texto **Conectar ao banco de dados**, selecione ou insira **SSISDB** e, em seguida, selecione **Conectar**.

    ![Guia Propriedades da Conexão do SSIS](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4. No Pesquisador de Objetos do SSMS, expanda o nó **Catálogo do Integration Services**, expanda **SSISDB** e, se não houver nenhuma pasta existente, clique com botão direito em **SSISDB** e crie uma nova pasta.

5. Em **SSISDB**, expanda qualquer pasta, clique com botão direito em **Projetos** e, em seguida, selecione **Implantar Projeto**.

    ![Nó SSISDB do SSIS expandido](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6. No Assistente de Implantação do Integration Services, na página **Introdução**, examine as informações e, em seguida, selecione **Avançar**.

    ![Página Introdução do Assistente de Implantação](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7. Na página **Selecionar Origem**, especifique o projeto do SSIS existente que você deseja implantar.

    Se o SSMS também está conectado ao SQL Server que hospeda o SSISDB de origem, selecione **Catálogo do Integration Services** e, em seguida, insira o nome do servidor e o caminho do projeto no catálogo para implantar o projeto diretamente.

    Como alternativa, selecione **Arquivo de implantação do projeto** e, em seguida, especifique o caminho para um arquivo de implantação do projeto existente (.ispac) para implantar o projeto.

    ![Página Selecionar Origem do Assistente de Implantação](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8. Selecione **Avançar**.
9. Na página **Selecionar Destino**, especifique o destino para o projeto.

    a. Na caixa de texto nome do servidor, insira o nome do servidor totalmente qualificado (<server_name>. database.windows.net).

    b. Forneça as informações de autenticação e, em seguida, selecione **Conectar**.

    ![Página Selecionar Destino do Assistente de Implantação](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. Selecione **procurar** para especificar a pasta de destino em SSISDB e, em seguida, selecione **Avançar**.

    > [!NOTE]
    > O botão **Avançar** será habilitado somente após você ter selecionado **Conectar**.

10. Na página **Validar**, exiba quaisquer erros/avisos e, se necessário, modifique os pacotes adequadamente.

    ![Página Validar do Assistente de Implantação](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. Selecione **Avançar**.

12. Na página **Examinar**, examine as configurações de implantação.

    > [!NOTE]
    > Você pode alterar suas configurações selecionando **anterior** ou selecionando qualquer um dos links de etapa no painel esquerdo.

13. Selecione **Implantar** para iniciar o processo de implantação.

14. Após a conclusão do processo de implantação, você pode exibir a página de resultados, que exibe o êxito ou a falha de cada ação de implantação.
    a. Se qualquer ação falhou, na coluna **Resultado**, selecione **Falhou** para exibir uma explicação do erro.
    b. Opcionalmente, selecione **Salvar relatório** para salvar os resultados em um arquivo XML.

15. Selecione **Fechar** para sair do Assistente de Implantação do Integration Services.

Se a implantação do seu projeto teve êxito sem falhas, é possível selecionar todos os pacotes contidos nele para executar no Azure-SSIS IR.

## <a name="next-steps"></a>Próximas etapas

* Verificar as diretrizes de migração no [Guia de Migração de Banco de Dados da Microsoft](https://datamigration.microsoft.com/).