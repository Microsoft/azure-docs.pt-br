---
title: Executar um pacote do SSIS com a atividade executar pacote do SSIS
description: Este artigo descreve como executar um pacote SQL Server Integration Services (SSIS) em um pipeline Azure Data Factory usando a atividade executar pacote do SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.date: 07/20/2020
ms.openlocfilehash: de9cd7e77e558c4d1a0aa62af17bc612eee5ec56
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98555824"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Executar um pacote SSIS com a atividade Executar pacote SSIS no Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve como executar um pacote SQL Server Integration Services (SSIS) em um pipeline Azure Data Factory usando a atividade executar pacote do SSIS. 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Crie um IR (tempo de execução de integração) do Azure-SSIS, se você ainda não tiver um, seguindo as instruções passo a passo no [tutorial: provisionando Azure-SSIS ir](./tutorial-deploy-ssis-packages-azure.md).

## <a name="run-a-package-in-the-azure-portal"></a>Executar um pacote no portal do Azure
Nesta seção, você usará o Data Factory interface do usuário ou o aplicativo para criar um pipeline Data Factory com uma atividade executar pacote do SSIS que executa seu pacote SSIS.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Criar um pipeline com uma atividade Executar Pacote do SSIS
Nesta etapa, você usa a interface do usuário do Data Factory ou o aplicativo para criar um pipeline. Adiciona uma atividade Executar pacote SSIS ao pipeline e configure-a para executar o pacote SSIS. 

1. Em sua visão geral do Data Factory ou home page no portal do Azure, selecione o bloco **criar & monitor** para iniciar a interface do usuário do data Factory ou o aplicativo em uma guia separada. 

   ![Home page do Data Factory](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   Na página **Introdução**, selecione **Criar pipeline**. 

   ![Página Introdução](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

1. Na caixa de ferramentas **atividades** , expanda **geral**. Em seguida, arraste uma atividade **executar pacote do SSIS** para a superfície do designer de pipeline. 

   ![Arraste uma atividade Executar pacote SSIS para a superfície do designer](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

   Selecione o objeto de atividade executar pacote SSIS para definir as guias **geral**, **configurações**, **parâmetros do SSIS**, **gerenciadores de conexões** e **substituições de propriedades** .

#### <a name="general-tab"></a>Guia Geral

Na guia **geral** da atividade executar pacote SSIS, conclua as etapas a seguir.

![Definir propriedades na guia Geral](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

   1. Para **nome**, insira o nome da atividade executar pacote SSIS.

   1. Para **Descrição**, insira a descrição da atividade executar pacote SSIS.

   1. Para **tempo limite**, insira a quantidade máxima de tempo que a atividade executar pacote SSIS pode ser executada. O padrão é 7 dias, o formato é D. HH: MM: SS.

   1. Para **tentar novamente**, insira o número máximo de tentativas de repetição para a atividade executar pacote SSIS.

   1. Para **intervalo de repetição**, insira o número de segundos entre cada tentativa de repetição para a atividade executar pacote SSIS. O padrão é 30 segundos.

   1. Marque a caixa de seleção **proteger saída** para escolher se deseja excluir a saída da atividade executar pacote SSIS do registro em log.

   1. Marque a caixa de seleção de **entrada segura** para escolher se deseja excluir a entrada da atividade executar pacote SSIS do registro em log.

#### <a name="settings-tab"></a>Guia Configurações

Na guia **configurações** da atividade executar pacote SSIS, conclua as etapas a seguir.

![Definir propriedades na guia Configurações — Automático](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   1. Para **Azure-SSIS ir**, selecione o Azure-SSIS ir designado para executar a atividade executar pacote SSIS.

   1. Para **Descrição**, insira a descrição da atividade executar pacote SSIS.

   1. Marque a caixa de seleção **autenticação do Windows** para escolher se deseja usar a autenticação do Windows para acessar armazenamentos de dados, como servidores SQL/compartilhamentos de arquivos locais ou arquivos do Azure.
   
      Se você marcar essa caixa de seleção, insira os valores para as credenciais de execução do pacote nas caixas **domínio**, **nome de usuário** e **senha** . Por exemplo, para acessar os arquivos do Azure, o domínio é `Azure` , o nome de usuário é `<storage account name>` , e a senha é `<storage account key>` .

      Como alternativa, você pode usar os segredos armazenados em seu Azure Key Vault como seus valores. Para fazer isso, marque a caixa de seleção **Azure Key Vault** ao lado delas. Selecione ou edite o serviço vinculado do cofre de chaves existente ou crie um novo. Em seguida, selecione o nome e a versão do segredo para seu valor. Ao criar ou editar o serviço vinculado do cofre de chaves, você pode selecionar ou editar o cofre de chaves existente ou criar um novo. Certifique-se de conceder Data Factory acesso de identidade gerenciada ao cofre de chaves se ainda não tiver feito isso. Você também pode inserir seu segredo diretamente no seguinte formato: `<key vault linked service name>/<secret name>/<secret version>` .
      
   1. Marque a caixa de seleção **tempo de execução de 32 bits** para escolher se o pacote precisa de tempo de execução de 32 bits para ser executado.

   1. Para **local do pacote**, **selecione SSISDB**, **sistema de arquivos (pacote)**, **sistema de arquivos (projeto)**, **pacote inserido** ou **repositório de pacotes**. 

##### <a name="package-location-ssisdb"></a>Local do pacote: SSISDB

O **SSISDB** como o local do pacote será selecionado automaticamente se o Azure-SSIS ir tiver sido provisionado com um catálogo do SSIS (SSISDB) hospedado pelo servidor/instância gerenciada do banco de dados SQL do Azure ou você mesmo pode selecioná-lo. Se estiver selecionado, conclua as etapas a seguir.

   1. Se o Azure-SSIS IR estiver em execução e a caixa de seleção **entradas manuais** estiver desmarcada, procure e selecione suas pastas, projetos, pacotes e ambientes existentes do SSISDB. Selecione **Atualizar** para buscar suas pastas, projetos, pacotes ou ambientes recém-adicionados do SSISDB, para que fiquem disponíveis para navegação e seleção. Para procurar e selecionar os ambientes para suas execuções de pacote, você deve configurar seus projetos com antecedência para adicionar esses ambientes como referências das mesmas pastas em SSISDB. Para obter mais informações, consulte [criar e mapear ambientes SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages).

   1. Em **Nível de registro**, selecione um escopo predefinido de registro em log para a execução do pacote. Marque a caixa de seleção **personalizado** se desejar inserir seu nome de log personalizado em vez disso. 

   1. Se o Azure-SSIS IR não estiver em execução ou se a caixa de seleção **entradas manuais** estiver marcada, insira os caminhos do pacote e do ambiente do SSISDB diretamente nos seguintes formatos: `<folder name>/<project name>/<package name>.dtsx` e `<folder name>/<environment name>` .

      ![Definir propriedades na guia Configurações — Manual](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

##### <a name="package-location-file-system-package"></a>Local do pacote: sistema de arquivos (pacote)

O **sistema de arquivos (pacote)** como o local do pacote é selecionado automaticamente se seu Azure-SSIS ir foi provisionado sem o SSISDB ou você mesmo pode selecioná-lo. Se estiver selecionado, conclua as etapas a seguir.

![Definir propriedades na guia Configurações – sistema de arquivos (pacote)](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)
   
   1. Especifique o pacote a ser executado fornecendo um caminho UNC (Convenção de nomenclatura universal) para o arquivo de pacote (com `.dtsx` ) na caixa **caminho do pacote** . Você pode procurar e selecionar seu pacote selecionando **procurar o armazenamento de arquivos** ou inserir seu caminho manualmente. Por exemplo, se você armazenar o pacote em arquivos do Azure, seu caminho será `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx` . 
   
   1. Se você configurar o pacote em um arquivo separado, também precisará fornecer um caminho UNC para o arquivo de configuração (com `.dtsConfig` ) na caixa **caminho de configuração** . Você pode procurar e selecionar sua configuração selecionando **procurar o armazenamento de arquivos** ou inserir seu caminho manualmente. Por exemplo, se você armazenar sua configuração em arquivos do Azure, seu caminho será `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig` .

   1. Especifique as credenciais para acessar o pacote e os arquivos de configuração. Se você tiver inserido os valores para suas credenciais de execução de pacote (para **autenticação do Windows**), poderá reutilizá-los marcando a caixa de seleção igual às credenciais de **execução de pacote** . Caso contrário, insira os valores para o pacote de credenciais de acesso nas caixas **domínio**, **nome de usuário** e **senha** . Por exemplo, se você armazenar o pacote e a configuração nos arquivos do Azure, o domínio será `Azure` , o nome de usuário será `<storage account name>` , e a senha será `<storage account key>` . 

      Como alternativa, você pode usar os segredos armazenados em seu Azure Key Vault como seus valores. Para fazer isso, marque a caixa de seleção **Azure Key Vault** ao lado delas. Selecione ou edite o serviço vinculado do cofre de chaves existente ou crie um novo. Em seguida, selecione o nome e a versão do segredo para seu valor. Ao criar ou editar o serviço vinculado do cofre de chaves, você pode selecionar ou editar o cofre de chaves existente ou criar um novo. Certifique-se de conceder Data Factory acesso de identidade gerenciada ao cofre de chaves se ainda não tiver feito isso. Você também pode inserir seu segredo diretamente no seguinte formato: `<key vault linked service name>/<secret name>/<secret version>` . 

      Essas credenciais também são usadas para acessar seus pacotes filho na tarefa executar pacote que são referenciadas por seu próprio caminho e outras configurações especificadas em seus pacotes. 

   1. Se você usou o nível de proteção **EncryptAllWithPassword** ou **EncryptSensitiveWithPassword** quando criou seu pacote via SQL Server Data Tools (SSDT), insira o valor para sua senha na caixa **senha de criptografia** . Como alternativa, você pode usar um segredo armazenado em seu Azure Key Vault como seu valor (veja acima).
      
      Se você usou o nível de proteção **EncryptSensitiveWithUserKey** , insira novamente seus valores confidenciais nos arquivos de configuração ou nas guias **parâmetros do SSIS**, **gerenciadores de conexões** ou **substituições de propriedades** (veja abaixo).
      
      Se você usou o nível de proteção **EncryptAllWithUserKey** , ele não tem suporte. Você precisa reconfigurar o pacote para usar outro nível de proteção por meio do SSDT ou do `dtutil` Utilitário de linha de comando. 

   1. Em **Nível de registro**, selecione um escopo predefinido de registro em log para a execução do pacote. Marque a caixa de seleção **personalizado** se desejar inserir seu nome de log personalizado em vez disso. 
   
   1. Se você quiser registrar em log as execuções de pacote além de usar os provedores de log padrão que podem ser especificados em seu pacote, especifique a pasta de log fornecendo seu caminho UNC na caixa **caminho de log** . Você pode procurar e selecionar a pasta de log selecionando **procurar o armazenamento de arquivos** ou inserir seu caminho manualmente. Por exemplo, se você armazenar os logs nos arquivos do Azure, o caminho de log será `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . Uma subpasta é criada neste caminho para cada execução de pacote individual, chamada após a execução da ID de execução de atividade do pacote SSIS e na qual os arquivos de log são gerados a cada cinco minutos. 
   
   1. Especifique as credenciais para acessar a pasta de log. Se você inseriu anteriormente os valores para as credenciais de acesso ao pacote (veja acima), poderá reutilizá-los selecionando a caixa de seleção o **mesmo que as credenciais de acesso ao pacote** . Caso contrário, insira os valores para suas credenciais de acesso de log nas caixas **domínio**, **nome de usuário** e **senha** . Por exemplo, se você armazenar os logs nos arquivos do Azure, o domínio será `Azure` , o nome de usuário será `<storage account name>` e a senha será `<storage account key>` . Como alternativa, você pode usar os segredos armazenados em seu Azure Key Vault como seus valores (veja acima).
   
Para todos os caminhos UNC mencionados anteriormente, o nome de arquivo totalmente qualificado deve ter menos de 260 caracteres. O nome do diretório deve ter menos de 248 caracteres.

##### <a name="package-location-file-system-project"></a>Local do pacote: sistema de arquivos (projeto)

Se você selecionar **sistema de arquivos (projeto)** como o local do pacote, conclua as etapas a seguir.

![Definir propriedades na guia Configurações – sistema de arquivos (projeto)](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   1. Especifique o pacote a ser executado fornecendo um caminho UNC para o arquivo de projeto (com `.ispac` ) na caixa **caminho do projeto** e um arquivo de pacote (com `.dtsx` ) do seu projeto na caixa **nome do pacote** . Você pode procurar e selecionar seu projeto selecionando **procurar o armazenamento de arquivos** ou inserir seu caminho manualmente. Por exemplo, se você armazenar seu projeto em arquivos do Azure, seu caminho será `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac` .

   1. Especifique as credenciais para acessar o projeto e os arquivos de pacote. Se você tiver inserido os valores para suas credenciais de execução de pacote (para **autenticação do Windows**), poderá reutilizá-los marcando a caixa de seleção igual às credenciais de **execução de pacote** . Caso contrário, insira os valores para o pacote de credenciais de acesso nas caixas **domínio**, **nome de usuário** e **senha** . Por exemplo, se você armazenar seu projeto e pacote em arquivos do Azure, o domínio será `Azure` , o nome de usuário será `<storage account name>` , e a senha será `<storage account key>` . 

      Como alternativa, você pode usar os segredos armazenados em seu Azure Key Vault como seus valores. Para fazer isso, marque a caixa de seleção **Azure Key Vault** ao lado delas. Selecione ou edite o serviço vinculado do cofre de chaves existente ou crie um novo. Em seguida, selecione o nome e a versão do segredo para seu valor. Ao criar ou editar o serviço vinculado do cofre de chaves, você pode selecionar ou editar o cofre de chaves existente ou criar um novo. Certifique-se de conceder Data Factory acesso de identidade gerenciada ao cofre de chaves se ainda não tiver feito isso. Você também pode inserir seu segredo diretamente no seguinte formato: `<key vault linked service name>/<secret name>/<secret version>` . 

      Essas credenciais também são usadas para acessar seus pacotes filho na tarefa executar pacote que são referenciados do mesmo projeto. 

   1. Se você usou o nível de proteção **EncryptAllWithPassword** ou **EncryptSensitiveWithPassword** quando criou seu pacote via SSDT, insira o valor para sua senha na caixa **senha de criptografia** . Como alternativa, você pode usar um segredo armazenado em seu Azure Key Vault como seu valor (veja acima).
      
      Se você usou o nível de proteção **EncryptSensitiveWithUserKey** , insira novamente seus valores confidenciais nas guias **parâmetros do SSIS**, **gerenciadores de conexões** ou **substituições de propriedades** (veja abaixo).
      
      Se você usou o nível de proteção **EncryptAllWithUserKey** , ele não tem suporte. Você precisa reconfigurar o pacote para usar outro nível de proteção por meio do SSDT ou do `dtutil` Utilitário de linha de comando. 

   1. Em **Nível de registro**, selecione um escopo predefinido de registro em log para a execução do pacote. Marque a caixa de seleção **personalizado** se desejar inserir seu nome de log personalizado em vez disso. 
   
   1. Se você quiser registrar em log as execuções de pacote além de usar os provedores de log padrão que podem ser especificados em seu pacote, especifique a pasta de log fornecendo seu caminho UNC na caixa **caminho de log** . Você pode procurar e selecionar a pasta de log selecionando **procurar o armazenamento de arquivos** ou inserir seu caminho manualmente. Por exemplo, se você armazenar os logs nos arquivos do Azure, o caminho de log será `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . Uma subpasta é criada neste caminho para cada execução de pacote individual, chamada após a execução da ID de execução de atividade do pacote SSIS e na qual os arquivos de log são gerados a cada cinco minutos. 
   
   1. Especifique as credenciais para acessar a pasta de log. Se você inseriu anteriormente os valores para as credenciais de acesso ao pacote (veja acima), poderá reutilizá-los selecionando a caixa de seleção o **mesmo que as credenciais de acesso ao pacote** . Caso contrário, insira os valores para suas credenciais de acesso de log nas caixas **domínio**, **nome de usuário** e **senha** . Por exemplo, se você armazenar os logs nos arquivos do Azure, o domínio será `Azure` , o nome de usuário será `<storage account name>` e a senha será `<storage account key>` . Como alternativa, você pode usar os segredos armazenados em seu Azure Key Vault como seus valores (veja acima).
   
Para todos os caminhos UNC mencionados anteriormente, o nome de arquivo totalmente qualificado deve ter menos de 260 caracteres. O nome do diretório deve ter menos de 248 caracteres.

##### <a name="package-location-embedded-package"></a>Local do pacote: pacote inserido

Se você selecionar **pacote inserido** como o local do pacote, conclua as etapas a seguir.

![Definir propriedades na guia Configurações-pacote inserido](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings5.png)

   1. Arraste e solte o arquivo de pacote (com `.dtsx` ) ou **carregue** -o de uma pasta de arquivo na caixa fornecida. Seu pacote será compactado e inserido automaticamente na carga da atividade. Uma vez inserido, você pode **baixar** seu pacote posteriormente para edição. Você também pode **parametrizar** seu pacote inserido atribuindo-o a um parâmetro de pipeline que pode ser usado em várias atividades, otimizando assim o tamanho da carga do pipeline. Atualmente, não há suporte para a inserção de arquivos de projeto (with `.ispac` ), portanto, você não pode usar os parâmetros/gerenciadores de conexões do SSIS com escopo de nível de projeto em seus pacotes inseridos.
   
   1. Se o pacote inserido não for todos criptografados e detectarmos o uso da tarefa Executar Pacote (EPT), a caixa de seleção **tarefa Executar Pacote** será automaticamente selecionada e seus pacotes filho que são referenciados pelo caminho do sistema de arquivos serão adicionados automaticamente e, portanto, você também poderá incorporá-los.
   
      Se não for possível detectar o uso de EPT, você precisará selecionar manualmente a caixa de seleção **tarefa Executar Pacote** e adicionar seus pacotes filho que são referenciados pelo caminho do sistema de arquivos, um por um, para que você também possa incorporá-los. Se os pacotes filho estiverem armazenados no banco de dados SQL Server (MSDB), você não poderá incorporá-los, portanto, precisará garantir que o Azure-SSIS IR possa acessar o MSDB para obtê-los usando suas referências de SQL Server. Atualmente, não há suporte para a inserção de arquivos de projeto (with `.ispac` ), portanto, você não pode usar referências baseadas em projeto para seus pacotes filho.
   
   1. Se você usou o nível de proteção **EncryptAllWithPassword** ou **EncryptSensitiveWithPassword** quando criou seu pacote via SSDT, insira o valor para sua senha na caixa **senha de criptografia** . 
   
      Como alternativa, você pode usar um segredo armazenado em seu Azure Key Vault como seu valor. Para fazer isso, marque a caixa de seleção **Azure Key Vault** ao lado dela. Selecione ou edite o serviço vinculado do cofre de chaves existente ou crie um novo. Em seguida, selecione o nome e a versão do segredo para seu valor. Ao criar ou editar o serviço vinculado do cofre de chaves, você pode selecionar ou editar o cofre de chaves existente ou criar um novo. Certifique-se de conceder Data Factory acesso de identidade gerenciada ao cofre de chaves se ainda não tiver feito isso. Você também pode inserir seu segredo diretamente no seguinte formato: `<key vault linked service name>/<secret name>/<secret version>` .
      
      Se você usou o nível de proteção **EncryptSensitiveWithUserKey** , insira novamente seus valores confidenciais nos arquivos de configuração ou nas guias **parâmetros do SSIS**, **gerenciadores de conexões** ou **substituições de propriedades** (veja abaixo).
      
      Se você usou o nível de proteção **EncryptAllWithUserKey** , ele não tem suporte. Você precisa reconfigurar o pacote para usar outro nível de proteção por meio do SSDT ou do `dtutil` Utilitário de linha de comando.

   1. Em **Nível de registro**, selecione um escopo predefinido de registro em log para a execução do pacote. Marque a caixa de seleção **personalizado** se desejar inserir seu nome de log personalizado em vez disso. 
   
   1. Se você quiser registrar em log as execuções de pacote além de usar os provedores de log padrão que podem ser especificados em seu pacote, especifique a pasta de log fornecendo seu caminho UNC na caixa **caminho de log** . Você pode procurar e selecionar a pasta de log selecionando **procurar o armazenamento de arquivos** ou inserir seu caminho manualmente. Por exemplo, se você armazenar os logs nos arquivos do Azure, o caminho de log será `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . Uma subpasta é criada neste caminho para cada execução de pacote individual, chamada após a execução da ID de execução de atividade do pacote SSIS e na qual os arquivos de log são gerados a cada cinco minutos. 
   
   1. Especifique as credenciais para acessar a pasta de log inserindo seus valores nas caixas **domínio**, **nome de usuário** e **senha** . Por exemplo, se você armazenar os logs nos arquivos do Azure, o domínio será `Azure` , o nome de usuário será `<storage account name>` e a senha será `<storage account key>` . Como alternativa, você pode usar os segredos armazenados em seu Azure Key Vault como seus valores (veja acima).
   
Para todos os caminhos UNC mencionados anteriormente, o nome de arquivo totalmente qualificado deve ter menos de 260 caracteres. O nome do diretório deve ter menos de 248 caracteres.

##### <a name="package-location-package-store"></a>Local do pacote: repositório de pacotes

Se você selecionar **repositório de pacotes** como o local do pacote, conclua as etapas a seguir.

![Defina propriedades na guia Configurações – repositório de pacotes](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings6.png)
   
   1. Para **nome do repositório de pacotes**, selecione um repositório de pacotes existente que esteja anexado à sua Azure-SSIS ir.

   1. Especifique o pacote a ser executado fornecendo seu caminho (sem `.dtsx` ) do repositório de pacotes selecionado na caixa **caminho do pacote** . Se o repositório de pacotes selecionado estiver na parte superior do sistema de arquivos/arquivos do Azure, você poderá procurar e selecionar seu pacote selecionando **procurar armazenamento de arquivos**, caso contrário, poderá inserir seu caminho no formato de `<folder name>\<package name>` . Você também pode importar novos pacotes para o repositório de pacotes selecionado via SQL Server Management Studio (SSMS) semelhante ao [armazenamento de pacotes SSIS herdado](/sql/integration-services/service/package-management-ssis-service). Para obter mais informações, consulte [Gerenciar pacotes SSIS com os repositórios de pacotes Azure-SSIS IR](./azure-ssis-integration-runtime-package-store.md).

   1. Se você configurar o pacote em um arquivo separado, será necessário fornecer um caminho UNC para o arquivo de configuração (com `.dtsConfig` ) na caixa **caminho de configuração** . Você pode procurar e selecionar sua configuração selecionando **procurar o armazenamento de arquivos** ou inserir seu caminho manualmente. Por exemplo, se você armazenar sua configuração em arquivos do Azure, seu caminho será `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig` .

   1. Marque a caixa de seleção **credenciais de acesso à configuração** para escolher se deseja especificar as credenciais para acessar o arquivo de configuração separadamente. Isso é necessário quando o repositório de pacotes selecionado está no topo do banco de dados do SQL Server (MSDB) hospedado pelo Instância Gerenciada do SQL do Azure ou também não armazena seu arquivo de configuração.
   
      Se você tiver inserido os valores para suas credenciais de execução de pacote (para **autenticação do Windows**), poderá reutilizá-los marcando a caixa de seleção igual às credenciais de **execução de pacote** . Caso contrário, insira os valores para suas credenciais de acesso de configuração nas caixas **domínio**, **nome de usuário** e **senha** . Por exemplo, se você armazenar sua configuração nos arquivos do Azure, o domínio será `Azure` , o nome de usuário será `<storage account name>` e a senha será `<storage account key>` . 

      Como alternativa, você pode usar os segredos armazenados em seu Azure Key Vault como seus valores. Para fazer isso, marque a caixa de seleção **Azure Key Vault** ao lado delas. Selecione ou edite o serviço vinculado do cofre de chaves existente ou crie um novo. Em seguida, selecione o nome e a versão do segredo para seu valor. Ao criar ou editar o serviço vinculado do cofre de chaves, você pode selecionar ou editar o cofre de chaves existente ou criar um novo. Certifique-se de conceder Data Factory acesso de identidade gerenciada ao cofre de chaves se ainda não tiver feito isso. Você também pode inserir seu segredo diretamente no seguinte formato: `<key vault linked service name>/<secret name>/<secret version>` .

   1. Se você usou o nível de proteção **EncryptAllWithPassword** ou **EncryptSensitiveWithPassword** quando criou seu pacote via SSDT, insira o valor para sua senha na caixa **senha de criptografia** . Como alternativa, você pode usar um segredo armazenado em seu Azure Key Vault como seu valor (veja acima).
      
      Se você usou o nível de proteção **EncryptSensitiveWithUserKey** , insira novamente seus valores confidenciais nos arquivos de configuração ou nas guias **parâmetros do SSIS**, **gerenciadores de conexões** ou **substituições de propriedades** (veja abaixo).
      
      Se você usou o nível de proteção **EncryptAllWithUserKey** , ele não tem suporte. Você precisa reconfigurar o pacote para usar outro nível de proteção por meio do SSDT ou do `dtutil` Utilitário de linha de comando. 

   1. Em **Nível de registro**, selecione um escopo predefinido de registro em log para a execução do pacote. Marque a caixa de seleção **personalizado** se desejar inserir seu nome de log personalizado em vez disso. 
   
   1. Se você quiser registrar em log as execuções de pacote além de usar os provedores de log padrão que podem ser especificados em seu pacote, especifique a pasta de log fornecendo seu caminho UNC na caixa **caminho de log** . Você pode procurar e selecionar a pasta de log selecionando **procurar o armazenamento de arquivos** ou inserir seu caminho manualmente. Por exemplo, se você armazenar os logs nos arquivos do Azure, o caminho de log será `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . Uma subpasta é criada neste caminho para cada execução de pacote individual, chamada após a execução da ID de execução de atividade do pacote SSIS e na qual os arquivos de log são gerados a cada cinco minutos. 
   
   1. Especifique as credenciais para acessar a pasta de log inserindo seus valores nas caixas **domínio**, **nome de usuário** e **senha** . Por exemplo, se você armazenar os logs nos arquivos do Azure, o domínio será `Azure` , o nome de usuário será `<storage account name>` e a senha será `<storage account key>` . Como alternativa, você pode usar os segredos armazenados em seu Azure Key Vault como seus valores (veja acima).
   
Para todos os caminhos UNC mencionados anteriormente, o nome de arquivo totalmente qualificado deve ter menos de 260 caracteres. O nome do diretório deve ter menos de 248 caracteres.

#### <a name="ssis-parameters-tab"></a>Guia de parâmetros do SSIS

Na guia **parâmetros SSIS** da atividade executar pacote SSIS, conclua as etapas a seguir.

![Definir propriedades na guia Parâmetros SSIS](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

   1. Se o Azure-SSIS IR estiver em execução, o **SSISDB** será selecionado como o local do pacote e a caixa de seleção **entradas manuais** na guia **configurações** será desmarcada, os parâmetros SSIS existentes no projeto e pacote selecionados do SSISDB serão exibidos para que você atribua valores a eles. Caso contrário, você pode inseri-los um a um para atribuir valores a eles manualmente. Verifique se eles existem e foram inseridos corretamente para que a execução do pacote seja bem sucedido. 
   
   1. Se você usou o nível de proteção **EncryptSensitiveWithUserKey** quando criou seu pacote por meio do SSDT e do **sistema de arquivos (pacote)**, do **sistema de arquivos (projeto), do** **pacote inserido** ou do pacote de **armazenamento** está selecionado como o local do pacote, também precisará reinserir os parâmetros confidenciais para atribuir valores a eles nessa guia. 
   
Ao atribuir valores aos parâmetros, você pode adicionar conteúdo dinâmico usando expressões, funções, Data Factory variáveis do sistema e Data Factory parâmetros ou variáveis de pipeline.

Como alternativa, você pode usar os segredos armazenados em seu Azure Key Vault como seus valores. Para fazer isso, marque a caixa de seleção **Azure Key Vault** ao lado delas. Selecione ou edite o serviço vinculado do cofre de chaves existente ou crie um novo. Em seguida, selecione o nome e a versão do segredo para seu valor. Ao criar ou editar o serviço vinculado do cofre de chaves, você pode selecionar ou editar o cofre de chaves existente ou criar um novo. Certifique-se de conceder Data Factory acesso de identidade gerenciada ao cofre de chaves se ainda não tiver feito isso. Você também pode inserir seu segredo diretamente no seguinte formato: `<key vault linked service name>/<secret name>/<secret version>` . 

#### <a name="connection-managers-tab"></a>Guia Gerenciadores de Conexões

Na guia **gerenciadores de conexões** da atividade executar pacote SSIS, conclua as etapas a seguir.

![Definir propriedades na guia Gerenciadores de Conexões](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

   1. Se o Azure-SSIS IR estiver em execução, o **SSISDB** será selecionado como o local do pacote e a caixa de seleção **entradas manuais** na guia **configurações** será desmarcada, os gerenciadores de conexões existentes no projeto e pacote selecionados do SSISDB serão exibidos para que você atribua valores às suas propriedades. Caso contrário, você pode inseri-las uma a uma para atribuir valores às suas propriedades manualmente. Verifique se eles existem e foram inseridos corretamente para que a execução do pacote seja bem sucedido. 
   
      Você pode obter o **escopo**, o **nome** e os nomes de **Propriedade** corretos para qualquer Gerenciador de conexões, abrindo o pacote que o contém em SSDT. Depois que o pacote for aberto, selecione o Gerenciador de conexões relevante para mostrar os nomes e valores de todas as suas propriedades na janela **Propriedades** de SSDT. Com essas informações, você pode substituir os valores de qualquer Propriedade do Gerenciador de conexões em tempo de execução. 

      ![Obter propriedades do Gerenciador de conexões do SSDT](media/how-to-invoke-ssis-package-ssis-activity/ssdt-connection-manager-properties.png)

      Por exemplo, sem modificar seu pacote original no SSDT, você pode converter seus fluxos de dados locais para locais em execução em SQL Server em fluxos de dados locais para a nuvem em execução no SSIS IR no ADF, substituindo os valores das propriedades **ConnectByProxy**, **ConnectionString** e **ConnectUsingManagedIdentity** em gerenciadores de conexões existentes em tempo de execução.
      
      Essas substituições de tempo de execução podem habilitar Self-Hosted IR (SHIR) como um proxy para o IR do SSIS ao acessar dados no local, consulte [Configurando o SHIR como um proxy para o ir do SSIS](./self-hosted-integration-runtime-proxy-ssis.md)e conexões de instância gerenciada/banco de dados SQL do Azure usando o driver MSOLEDBSQL mais recente que, por sua vez, habilita a [autenticação do Azure Active Directory](/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)(AAD) com a identidade gerenciada do ADF

      ![Definir propriedades de SSDT na guia gerenciadores de conexões](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers2.png)
   
   1. Se você usou o nível de proteção **EncryptSensitiveWithUserKey** quando criou seu pacote por meio do SSDT e do **sistema de arquivos (pacote)**, do **sistema de arquivos (projeto), do** **pacote inserido** ou do pacote de **armazenamento** está selecionado como o local do pacote, também precisará reinserir suas propriedades confidenciais do Gerenciador de conexões para atribuir valores a elas nessa guia. 

Ao atribuir valores às suas propriedades do Gerenciador de conexões, você pode adicionar conteúdo dinâmico usando expressões, funções, Data Factory variáveis do sistema e Data Factory parâmetros ou variáveis de pipeline. 

Como alternativa, você pode usar os segredos armazenados em seu Azure Key Vault como seus valores. Para fazer isso, marque a caixa de seleção **Azure Key Vault** ao lado delas. Selecione ou edite o serviço vinculado do cofre de chaves existente ou crie um novo. Em seguida, selecione o nome e a versão do segredo para seu valor. Ao criar ou editar o serviço vinculado do cofre de chaves, você pode selecionar ou editar o cofre de chaves existente ou criar um novo. Certifique-se de conceder Data Factory acesso de identidade gerenciada ao cofre de chaves se ainda não tiver feito isso. Você também pode inserir seu segredo diretamente no seguinte formato: `<key vault linked service name>/<secret name>/<secret version>` . 

#### <a name="property-overrides-tab"></a>Guia substituições de propriedade

Na guia **substituições de propriedade** da atividade executar pacote SSIS, conclua as etapas a seguir.

![Definir propriedades na guia Substituições de Propriedades](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   1. Insira os caminhos das propriedades existentes no pacote selecionado um a um para atribuir valores a elas manualmente. Verifique se eles existem e foram inseridos corretamente para que a execução do pacote seja bem sucedido. Por exemplo, para substituir o valor de sua variável de usuário, insira seu caminho no seguinte formato: `\Package.Variables[User::<variable name>].Value` . 

      Você pode obter o **caminho de propriedade** correto para qualquer propriedade de pacote abrindo o pacote que o contém em SSDT. Depois que o pacote for aberto, selecione sua propriedade fluxo de controle e **configurações** na janela **Propriedades** do SSDT. Em seguida, selecione o botão de reticências (**...**) ao lado de sua propriedade de **configurações** para abrir o **organizador de configurações de pacote** que normalmente é usado para [criar configurações de pacote no modelo de implantação de pacote](/sql/integration-services/packages/legacy-package-deployment-ssis#create-package-configurations). 

      ![Obter propriedades do pacote da propriedade SSDT-configurações](media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties.png)

      No **organizador de configurações do pacote**, marque a caixa de seleção **Habilitar configurações de pacote** e o botão **Adicionar...** para abrir o **Assistente de configuração de pacotes**. 
      
      No **Assistente de configuração de pacotes**, selecione o item arquivo de **configuração XML** no menu suspenso **tipo de configuração** e o botão **especificar definições de configuração diretamente** , insira o nome do arquivo de configuração e selecione o botão **Avançar >** . 

      ![Obter propriedades do pacote de SSDT – organizador de configurações](media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties2.png)

      Por fim, selecione as propriedades do pacote cujo caminho você deseja e o **próximo >** botão.  Agora você pode ver, copiar & colar os caminhos de Propriedade do pacote que deseja e salvá-los em seu arquivo de configuração. Com essas informações, você pode substituir os valores de qualquer propriedade de pacote em tempo de execução. 

      ![Obter propriedades do pacote do SSDT assistente de configuração](media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties3.png)
   
   1. Se você usou o nível de proteção **EncryptSensitiveWithUserKey** quando criou seu pacote por meio do SSDT e do **sistema de arquivos (pacote)**, do **sistema de arquivos (projeto), do** **pacote inserido** ou do **repositório de pacotes** estiver selecionado como o local do pacote, também precisará reinserir suas propriedades de pacote confidenciais para atribuir valores a elas nessa guia. 
   
Ao atribuir valores às suas propriedades de pacote, você pode adicionar conteúdo dinâmico usando expressões, funções, Data Factory variáveis de sistema e Data Factory parâmetros ou variáveis de pipeline.

Os valores atribuídos nos arquivos de configuração e na guia **parâmetros do SSIS** podem ser substituídos usando as guias **gerenciadores de conexões** ou **substituições de propriedades** . Os valores atribuídos na guia **gerenciadores de conexões** também podem ser substituídos usando a guia **substituições de propriedade** .

Para validar a configuração de pipeline, selecione **validar** na barra de ferramentas. Para fechar o **relatório de validação de pipeline**, selecione **>>** .

Para publicar o pipeline para Data Factory, selecione **publicar tudo**. 

### <a name="run-the-pipeline"></a>Executar o pipeline
Nesta etapa, você dispara uma execução de pipeline. 

1. Para disparar uma execução de pipeline, selecione **gatilho** na barra de ferramentas e selecione **disparar agora**. 

   ![Disparar agora](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. Na janela **Execução de Pipeline**, selecione **Concluir**. 

### <a name="monitor-the-pipeline"></a>Monitorar o Pipeline

1. Alterne para a guia **Monitorar** à esquerda. Você vê a execução do pipeline e seu status junto com outras informações, como a hora de **início da execução** . Para atualizar a exibição, selecione **Atualizar**.

   ![Execuções de pipeline](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Selecione o link **Exibir execuções de atividade** na coluna **Ações**. Você vê apenas uma execução de atividade porque o pipeline tem apenas uma atividade. É a atividade executar pacote SSIS.

   ![Execuções de atividade](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Execute a consulta a seguir no banco de dados SSISDB no SQL Server para verificar se o pacote foi executado. 

   ```sql
   select * from catalog.executions
   ```

   ![Verificar as execuções do pacote](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. Você também pode obter a ID de execução do SSISDB da saída da execução da atividade do pipeline e usar a ID para verificar logs de execução mais abrangentes e mensagens de erro no SQL Server Management Studio.

   ![Obter a ID de execução.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>Programar o pipeline com um gatilho

Você também pode criar um gatilho agendado para o pipeline para que o pipeline seja executado em uma agenda, como por hora ou por dia. Para um exemplo, consulte [Criar uma data factory - Interface do Usuário do Data Factory](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Executar um pacote com o PowerShell
Nesta seção, você usará Azure PowerShell para criar um pipeline de Data Factory com uma atividade executar pacote do SSIS que executa seu pacote SSIS. 

Instale os módulos mais recentes do Azure PowerShell seguindo as instruções passo a passo em [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="create-a-data-factory-with-azure-ssis-ir"></a>Criar um data factory com Azure-SSIS IR
Você pode usar um data factory existente que já tenha Azure-SSIS IR provisionado ou criar um novo data factory com Azure-SSIS IR. Siga as instruções detalhadas no [tutorial: implantar pacotes do SSIS no Azure por meio do PowerShell](./tutorial-deploy-ssis-packages-azure-powershell.md).

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Criar um pipeline com uma atividade Executar Pacote do SSIS 
Nesta etapa, você cria um pipeline com uma atividade Executar pacote SSIS. A atividade é executada em seu pacote SSIS. 

1. Crie um arquivo JSON chamado `RunSSISPackagePipeline.json` na `C:\ADF\RunSSISPackage` pasta com conteúdo semelhante ao exemplo a seguir.

   > [!IMPORTANT]
   > Substitua nomes de objeto, descrições e caminhos, valores de propriedade ou parâmetro, senhas e outros valores de variáveis antes de salvar o arquivo. 
    
   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "MySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "MyAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyExecutionDomain",
                       "username": "MyExecutionUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "MyExecutionPassword"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "type": "SSISDB",
                       "packagePath": "MyFolder/MyProject/MyPackage.dtsx"
                   },
                   "environmentPath": "MyFolder/MyEnvironment",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyProjectParameter",
                               "type": "Expression"
                           }
                       }
                   },
                   "packageParameters": {
                       "package_param_1": {
                           "value": "345"
                       },
                       "package_param_2": {
                           "value": {
                               "type": "AzureKeyVaultSecret",
                               "store": {
                                   "referenceName": "myAKV",
                                   "type": "LinkedServiceReference"
                               },
                               "secretName": "MyPackageParameter"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "username": {
                               "value": "MyConnectionUsername"
                           },
                           "password": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "MyConnectionPassword"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "username": {
                               "value": {
                                   "value": "@pipeline().parameters.MyConnectionUsername",
                                   "type": "Expression"
                               }
                           },
                           "password": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyConnectionPassword",
                                   "secretVersion": "MyConnectionPasswordVersion"
                               }
                           }
                       }
                   },
                   "propertyOverrides": {
                       "\\Package.MaxConcurrentExecutables": {
                           "value": 8,
                           "isSensitive": false
                       }
                   }
               },
               "policy": {
                   "timeout": "0.01:00:00",
                   "retry": 0,
                   "retryIntervalInSeconds": 30
               }
           }]
       }
   }
   ```

   Para executar pacotes armazenados em arquivos do sistema de arquivos/Azure, insira os valores para as propriedades do pacote e local do log da seguinte maneira:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "File",
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyPackage.dtsx",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   },
                   "logLocation": {
                       "type": "File",
                       "logPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyLogFolder",
                       "typeProperties": {
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   Para executar pacotes em projetos armazenados em arquivos do sistema de arquivos/Azure, insira os valores para as propriedades de local do pacote da seguinte maneira:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "File",
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyProject.ispac:MyPackage.dtsx",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   Para executar pacotes inseridos, insira os valores para as propriedades de local do pacote da seguinte maneira:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "InlinePackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "packageName": "MyPackage.dtsx",
                           "packageContent":"My compressed/uncompressed package content",
                           "packageLastModifiedDate": "YYYY-MM-DDTHH:MM:SSZ UTC-/+HH:MM"
                       }
                   }
               }
           }
       }
   }
   ```

   Para executar pacotes armazenados em repositórios de pacotes, insira os valores para as propriedades do pacote e do local de configuração da seguinte maneira:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "PackageStore",
                       "packagePath": "myPackageStore/MyFolder/MyPackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           },
                           "configurationPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyConfiguration.dtsConfig",
                           "configurationAccessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

2. No Azure PowerShell, mude para a pasta `C:\ADF\RunSSISPackage`.

3. Para criar o pipeline **RunSSISPackagePipeline**, execute o cmdlet **set-AzDataFactoryV2Pipeline** .

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   Aqui está a saída de exemplo:

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>Executar o pipeline
Use o cmdlet **Invoke-AzDataFactoryV2Pipeline** para executar o pipeline. O cmdlet retorna a ID da execução de pipeline para monitoramento futuro.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>Monitorar o Pipeline

Execute o script do PowerShell a seguir para verificar continuamente o status da execução de pipeline até que ela termine de copiar os dados. Copie ou cole o script a seguir na janela do PowerShell e selecione Enter. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

Você também pode monitorar o pipeline usando o portal do Azure. Para obter instruções passo a passo, consulte [Monitorar o pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="schedule-the-pipeline-with-a-trigger"></a>Programar o pipeline com um gatilho
Na etapa anterior, você executou o pipeline sob demanda. Você também pode criar um gatilho de agendamento para executar o pipeline em um agendamento, como por hora ou por dia.

1. Crie um arquivo JSON chamado `MyTrigger.json` na `C:\ADF\RunSSISPackage` pasta com o seguinte conteúdo: 
        
   ```json
   {
       "properties": {
           "name": "MyTrigger",
           "type": "ScheduleTrigger",
           "typeProperties": {
               "recurrence": {
                   "frequency": "Hour",
                   "interval": 1,
                   "startTime": "2017-12-07T00:00:00-08:00",
                   "endTime": "2017-12-08T00:00:00-08:00"
               }
           },
           "pipelines": [{
               "pipelineReference": {
                   "type": "PipelineReference",
                   "referenceName": "RunSSISPackagePipeline"
               },
               "parameters": {}
           }]
       }
   }    
   ```
    
1. No Azure PowerShell, mude para a pasta `C:\ADF\RunSSISPackage`.
1. Execute o cmdlet **set-AzDataFactoryV2Trigger** , que cria o gatilho. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
1. Por padrão, o gatilho está no estado interrompido. Inicie o gatilho executando o cmdlet **Start-AzDataFactoryV2Trigger** . 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
1. Confirme se o gatilho é iniciado executando o cmdlet **Get-AzDataFactoryV2Trigger** . 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
1. Execute o seguinte comando após a próxima hora. Por exemplo, se a hora atual for 15:25 UTC, execute o comando às 16:00 UTC. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Execute a consulta a seguir no banco de dados SSISDB no SQL Server para verificar se o pacote foi executado. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>Próximas etapas
Consulte a postagem blog a seguir:
- [Modernizar e estender seus fluxos de trabalho ETL/ELT com atividades do SSIS em pipelines de Azure Data Factory](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)