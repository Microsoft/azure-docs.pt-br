---
title: "Painel do Power BI sobre a integridade do veículo e hábitos de condução - Azure | Microsoft Docs"
description: "Use os recursos do Cortana Intelligence para obter informações preditivas em tempo real sobre a integridade do veículo e hábitos de condução."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: aaeb29a5-4a13-4eab-bbf1-885690d86c56
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: bradsev
ms.openlocfilehash: 39be936520d62cb1c1c28de9bd72f8f489166082
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="vehicle-telemetry-analytics-solution-template-power-bi-dashboard-setup-instructions"></a>Instruções de instalação do Painel de Power BI do modelo de solução de análise de telemetria do veículo
Este **menu** fornece links para os capítulos deste manual. 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

A solução de análise de telemetria do veículo demonstra como revendedores de carros, fabricantes de automóveis e seguradoras podem aproveitar os recursos do Cortana Intelligence para obter uma visão em tempo real e uma previsão sobre a integridade do veículo e os hábitos de direção para promover melhorias na área do cliente, em pesquisa e desenvolvimento e em campanhas de marketing. Este documento contém instruções passo a passo de como você pode configurar os painéis e relatórios do Power BI depois que a solução é implantada na sua assinatura. 

## <a name="prerequisites"></a>Pré-requisitos
1. Implantar a solução de [Análise de Telemetria](https://gallery.cortanaintelligence.com/Solution/5bdb23f3abb448268b7402ab8907cc90)  
2. [Instalar o Microsoft Power BI Desktop](http://www.microsoft.com/download/details.aspx?id=45331)
3. Uma [assinatura do Azure](https://azure.microsoft.com/pricing/free-trial/). Se você não tiver uma assinatura do Azure, comece com uma assinatura gratuita do Azure
4. Conta do Microsoft Power BI

## <a name="cortana-intelligence-suite-components"></a>Componentes do Cortana Intelligence Suite
Como parte do modelo de solução de análise de telemetria de veículo, os serviços a seguir do Cortana Intelligence são implantados em sua assinatura.

* **Hubs de Eventos** para ingerir milhões de eventos de telemetria do veículos no Azure.
* **Stream Analytics** para obter informações em tempo real sobre a integridade do veículo e persistir esses dados no armazenamento de longo prazo para uma análise de lote mais avançada.
* **Machine Learning** para detecção de anomalias em tempo real e processamento em lote para obter previsões.
* **HDInsight** é utilizado para transformar dados em escala
* **Data Factory** lida com a orquestração, o agendamento, o gerenciamento de recursos e o monitoramento do pipeline de processamento em lote.

**PowerBI** fornece essa solução a um painel avançado para os dados em tempo real e as visualizações da análise preditiva. 

A solução usa duas fontes de dados diferentes: **Sinais de veículo simulados e conjunto de dados de diagnóstico** e **catálogo de veículo**.

Um simulador de telemática do veículo é incluído como parte desta solução. Ele emite informações de diagnóstico e sinais correspondentes ao estado do veículo e ao padrão de condução em um determinado ponto no tempo. 

O Catálogo do veículo é um conjunto de dados de referência que contém um VIN para o mapeamento do modelo.

## <a name="power-bi-dashboard-preparation"></a>Preparação do Painel do Power BI
### <a name="setup-power-bi-real-time-dashboard"></a>Instalação do Painel do Power BI em tempo real

**Iniciar o aplicativo de painel em tempo real** Depois que a implantação for concluída, você deve seguir as instruções de operação manual

* Baixe o aplicativo de painel em tempo real RealtimeDashboardApp.zip e descompacte-o.
*  Na pasta descompactada, abra o arquivo de configuração do aplicativo “RealtimeDashboardApp.exe.config”, substitua appSettings por conexões de serviço de Eventhub, Armazenamento de Blobs e AM pelos valores nas instruções de operação manual e salve suas alterações.
* Execute o aplicativo RealtimeDashboardApp.exe. Uma janela de logon será mostrada, forneça suas credenciais válidas do Power BI e clique no botão **Aceitar**. Em seguida, a execução do aplicativo começará.

   ![Entre no Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
   
   ![Permissões do Painel do Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)

* Faça logon no site do Power BI e crie um painel em tempo real.

Agora, você está pronto para configurar o painel do Power BI com visualizações avançadas para obter uma visão em tempo real e previsões sobre a integridade do veículo e os hábitos de direção. Demora aproximadamente 45 minutos a uma hora para a criação de todos os relatórios e configuração do painel. 

### <a name="configure-power-bi-reports"></a>Configurar relatórios do Power BI
Os relatórios em tempo real e o painel demoram cerca de 30 a 45 minutos para serem concluídos. Navegue até [http://powerbi.com](http://powerbi.com) e faça logon.

![Entre no Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-1-powerbi-signin.png)

Um novo conjunto de dados é gerado no Power BI. Clique no conjunto de dados **ConnectedCarsRealtime** .

![Selecionar conjunto de dados de carros conectados em tempo real](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

Salve o relatório em branco usando **Ctrl + s**.

![Salvar relatório em branco](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

Forneça o nome de relatório *Análise em tempo real de telemetria do veículo - Relatórios*.

![Forneça o nome do relatório](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)

## <a name="real-time-reports"></a>Relatórios em tempo real
Há três relatórios em tempo real nesta solução:

1. Veículos em operação
2. Veículos que precisam de manutenção
3. Estatísticas de integridade de veículos

Você pode escolher configurar todos os três relatórios em tempo real ou parar após qualquer estágio e prosseguir para a próxima seção da configuração dos relatórios em lote. Recomendamos que você crie todos os três relatórios para visualizar todas as informações do caminho da solução em tempo real.  

### <a name="1-vehicles-in-operation"></a>1. Veículos em operação
Clique duas vezes na **Página 1** e a renomeie como "Veículos em operação"  
    ![Carros conectados - Veículos em operação](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)  

Selecione o campo **vin** em **Campos** e escolha o tipo de visualização como **"Placa"**.  

A visualização de placa é criada como mostrado na figura.  
    ![Carros conectados - Selecione vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

Clique na área em branco para adicionar a nova visualização.  

Selecione **Cidade** e **vin** nos campos. Altere a visualização para **"Mapa"**. Arraste o **vin** na área de valores. Arraste **cidade** de campos para a área **Legenda**.   
    ![Carros conectados - Visualização da placa](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)

Selecione a seção **formato** em **Visualizações**, clique em **Título** e altere o **Texto** para **"Veículos em operação por cidade"**.  
    ![Carros conectados - Veículos em operação por cidade](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)   

A visualização final será como mostrado na figura.    
    ![Carros conectados - Visualização final](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

Clique na área em branco para adicionar a nova visualização.  

Selecione **Cidade** e **vin**, altere o tipo de visualização para **Gráfico de coluna clusterizada**. Certifique-se de que o campo **Cidade** está na **Área do eixo** e o **vin** está na **Área do valor**  

Classificar gráfico por **"Contagem de vin"**  
    ![Carros conectados - Contagem de vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)  

Alterar **Título** do gráfico para **"Veículos em operação por cidade"**  

Clique na seção **Formato**, selecione **Cores dos Dados**, clique em **"Ativada"** para **Mostrar Tudo**  
    ![Carros conectados - Mostrar todas as cores de dados](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)  

Altere a cor de uma cidade específica, clicando no ícone de cor.  
    ![Carros conectados - Alterar cores](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)  

Clique na área em branco para adicionar a nova visualização.  

Selecione a visualização **Gráfico de Coluna Clusterizada** nas visualizações, arraste o campo **cidade** para a área do **Eixo**, **Modelo** para a área de **Legenda** e **vin** para a área **Valor**.  
    ![Carros conectados - Gráfico de colunas agrupadas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png)  
    ![Carros conectados - Renderização](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)

Reorganize todas as visualizações nesta página conforme mostrado na figura.  
    ![Carros conectados - Visualizações](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

Você configurou com êxito o relatório em tempo real "Veículos em operação". Você pode continuar e criar o próximo relatório em tempo real ou parar aqui e configurar o painel. 

### <a name="2-vehicles-requiring-maintenance"></a>2. Veículos que precisam de manutenção
Clique em ![Adicionar](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) para adicionar um novo relatório, renomeie-o como **"Veículos que precisam de manutenção"**

![Carros conectados - Veículos que precisam de manutenção](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)  

Selecione o campo **vin** e altere o tipo de visualização para **Placa**.  
    ![Carros conectados - Visualização da placa do vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)  

Temos um campo chamado "MaintenanceLabel", no conjunto de dados. Esse campo pode ter um valor “0” ou “1”. Ele é definido pelo modelo de Azure Machine Learning provisionado como parte da solução e integrado ao caminho em tempo real. O valor "1" indica que um veículo requer manutenção. 

Para adicionar um filtro de **Página** para mostrar os dados de veículos que necessitam de manutenção: 

1. Arraste o campo **"MaintenanceLabel"** para dentro dos **Filtros de página**.  
   ![Carros conectados - Filtros de página](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)  
2. Clique no menu **Filtragem Básica** presente na parte inferior do Filtro de página MaintenanceLabel.  
   ![Carros conectados - Filtragem básica](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png)  
3. Defina o valor do filtro como **"1"**    
   ![Carros conectados - Valor do filtro](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)  

Clique na área em branco para adicionar a nova visualização.  

Selecione o **Gráfico de Coluna Clusterizada** das visualizações  
![Carros conectados - Visualização da placa do vind](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png)  
![Carros conectados - Gráfico de colunas agrupadas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

Arraste o campo **Modelo** para a área de **Eixo**, **Vin** para a área de **Valor**. Em seguida, classifique visualização por **Contagem de vin**.  Alterar o **Título** do gráfico **"Veículos que exigem manutenção pelo modelo"**  

Arraste os campos **vin** para a **Saturação da cor** presente na seção **Campos**![Fields](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png) da guia **Visualização**  
![Carros conectados - Saturação de cor](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)  

Alterar **Cores dos Dados** nas visualizações na seção **Formato**  
Alterar a cor do Mínimo para: **F2C812**  
Alterar a cor do Máximo para: **FF6300**  
![Carros conectados - Alterações de cor](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png)  
![Carros conectados - Novas cores de visualização](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)  

Clique na área em branco para adicionar a nova visualização.  

Selecione **Gráfico de colunas clusterizado** das visualizações, arraste o campo **vin** para a área **Valor**, arraste o campo **Cidade** para a área **Eixo**. Classificar gráfico por **"Contagem de vin"**. Altere o **Título** do gráfico **"Veículos que exigem manutenção por cidade"**   
![Carros conectados - Veículos que precisam de manutenção por cidade](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)  

Clique na área em branco para adicionar a nova visualização.  

Selecione a visualização **Placa com várias linhas** em visualizações, arraste **Modelo** e **vin** para a área **Campos**.  
![Carros conectados - Placa com várias linhas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)    

Reorganizando toda a visualização, o relatório final terá esta aparência:   
![Carros conectados - Placa com várias linhas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)  

Você configurou com êxito o relatório em tempo real "Veículos que precisam de manutenção". Você pode continuar e criar o próximo relatório em tempo real ou parar aqui e configurar o painel. 

### <a name="3-vehicles-health-statistics"></a>3. Estatísticas de integridade de veículos
Clique em ![Adicionar](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) para adicionar um novo relatório, renomeie-o para **"Estatísticas de integridade de veículos"**  

Selecione a visualização **Medidor** em visualizações, arraste o campo **Velocidade** para as áreas **Valor, Valor Mínimo, Valor Máximo**.  
![Carros conectados - Placa com várias linhas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)  

Alterar a agregação padrão de **velocidade** na **Área de valor** para **Média** 

Alterar a agregação padrão de **velocidade** na **Área mínima** para **Mínima**

Alterar a agregação padrão de **velocidade** na **Área máxima** para **Máxima**

![Carros conectados - Placa com várias linhas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)  

Renomeie o **Título do Medidor** para **"Velocidade média"** 

![Carros conectados - Medidor](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)  

Clique na área em branco para adicionar a nova visualização.  

Da mesma forma, adicione um **Medidor** para **média de óleo do motor**, **média de combustível** e **temperatura média do motor**.  

Altere a agregação padrão dos campos em cada medidor conforme as etapas acima no medidor de **"Velocidade média"** .

![Carros conectados - Medidores](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

Clique na área em branco para adicionar a nova visualização.

Selecione **Gráfico de coluna e linha clusterizado** em visualizações, arraste o campo **Cidade** para o **Eixo compartilhado**, arraste os campos **velocidade**, **pressãodopneu** e **óleodomotor** para a área Valores de Coluna e altere o tipo de agregação para **Média**. 

Arraste o campo **temperaturadoMotor** para a área **Valores de Linha**, altere o tipo de agregação para **Média**. 

![Carros conectados - Campos de visualizações](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

Altere o **Título** do gráfico para **"Velocidade média, pressão do pneu, óleo do motor e temperatura do motor"**.  

![Carros conectados - Campos de visualizações](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

Clique na área em branco para adicionar a nova visualização.

Selecione a visualização **Treemap** nas visualizações, arraste o campo **Modelo** para a área **Grupo** e arraste o campo **ProbabilidadeDeManutenção** para a área de **Valores**.

Altere o **Título do gráfico** para **"Modelos de veículo que exigem manutenção"**.

![Carros conectados - Alterar título do gráfico](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

Clique na área em branco para adicionar a nova visualização.

Selecione **Gráfico de barras empilhadas 100%** na visualização, arraste o campo **cidade** para a área de **Eixo** e arraste os campos **ProbabilidadeDeManutenção**, **ProbabilidadeDeRecall** para a área **Valor**.

![Carros conectados - Adicionar nova visualização](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

Clique em **Formato**, selecione **Cores dos Dados**, altere a cor **ProbabilidadeDeManutenção** para o valor **"F2C80F"**.

Altere o **Título** do gráfico para **"Probabilidade de manutenção e recall do veículo por cidade"**.

![Carros conectados - Adicionar nova visualização](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

Clique na área em branco para adicionar a nova visualização.

Selecione o **Gráfico de Área** na visualização em visualizações, arraste o campo **Modelo** para a área do **Eixo** e arraste os campos **óleodoMotor, pressãodopneu, velocidade e ProbabilidadeDeManutenção** para a área **Valores**. Altere o tipo de agregação para **"Média"**. 

![Carros conectados - Alterar tipo de agregação](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

Altere o Título do gráfico para **"Média do óleo do motor, pressão do pneu, velocidade e probabilidade de manutenção por modelo"**.

![Carros conectados - Alterar título do gráfico](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

Clique na área em branco para adicionar a nova visualização:

1. Selecione a visualização **Gráfico de Dispersão** em visualizações.
2. Arraste o campo **Modelo** para a área de **Detalhes** e **Legenda**.
3. Arraste o campo **combustível** para a área **Eixo X**, altere a agregação para **Média**.
4. Arraste **temperaturadoMotor** para a área **Eixo Y**, altere a agregação para **Média**
5. Arraste o campo **vin** para a área **Tamanho**.

![Carros conectados - Adicionar nova visualização](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

Altere o **Título** do gráfico para **"Médias de combustível, temperatura do motor por modelo"**.

![Carros conectados - Alterar título do gráfico](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

O relatório final será semelhante ao mostrado abaixo.

![Carros conectados-Relatório final](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### <a name="pin-visualizations-from-the-reports-to-the-real-time-dashboard"></a>Fixar visualizações de relatórios para o painel em tempo real
Crie um painel em branco clicando no sinal de adição ao lado dos Painéis. Você pode chamá-lo de "Painel de análise de telemetria do veículo"

![Carros conectados-Painel](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.5.png)

Fixe a visualização dos relatórios acima no painel. 

![Carros conectados-Painel](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.6.png)

O painel deve ter a aparência a seguir quando todos os três relatórios tiverem sido criados e as visualizações correspondentes estiverem fixadas no painel. Se você não tiver criado todos os relatórios, o painel poderá parecer diferente . 

![Carros conectados-Painel](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)

Parabéns! Você criou o painel em tempo real. Durante a execução de CarEventGenerator.exe e RealtimeDashboardApp.exe, você verá atualizações dinâmicas no painel. Deve demorar cerca de 10 a 15 minutos para concluir as etapas a seguir.

## <a name="setup-power-bi-batch-processing-dashboard"></a>Instalação do painel de processamento em lote do Power BI
> [!NOTE]
> Demora cerca de duas horas (após a conclusão bem-sucedida da implantação) para concluir a execução da pipeline de processamento em lote de ponta a ponta e processar um ano de dados gerados. Aguarde até que o processamento seja concluído antes de prosseguir para as próximas etapas. 
> 
> 

**Baixe o arquivo de designer do Power BI**

* Um arquivo de designer pré-configurado do Power BI é incluído como parte das instruções de operação manual de implantação
* Procure por 2. Instalar o painel de processamento em lote do PowerBI Baixe o modelo do Power BI para o painel de processamento em lote chamado **ConnectedCarsPbiReport.pbix**.
* Salve localmente

**Configurar relatórios do Power BI**

* Abra o arquivo de designer “**ConnectedCarsPbiReport.pbix**” usando o Power BI Desktop. Caso ainda não tenha feito isso, instale o Power BI Desktop de [Instalar Power BI Desktop](http://www.microsoft.com/download/details.aspx?id=45331). 
* Clique em **Editar Consultas**.

![Editar consulta do Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

* Clique duas vezes na **Origem**.

![Definir origem do Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

* Atualize a cadeia de conexão do servidor com o servidor do SQL Azure provisionado como parte da implantação.  Examine as instruções de operação manual em 

    4. Banco de Dados SQL do Azure
    
    * Servidor: somethingsrv.database.windows.net
    * Banco de dados: connectedcar
    * Nome de usuário: username
    * Senha: gerencie sua senha do SQL Server no Portal do Azure

* Deixe **Banco de dados** como *connectedcar*.

![Definir banco de dados do Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

* Clique em **OK**.
* Você verá a guia **Credencial do Windows** selecionada por padrão, altere-a para **Credenciais de banco de dados** clicando na guia **Banco de Dados** no lado direito.
* Forneça o **Nome de usuário** e a **Senha** de seu Banco de Dados SQL do Azure especificado durante a configuração da implantação.

![Forneça as credenciais do banco de dados](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

* Clique em **Conectar**
* Repita as etapas acima para cada uma das 3 consultas restantes presentes no painel direito e atualize os detalhes de conexão da fonte de dados.
* Clique em **Fechar e Carregar**. Os conjuntos de dados de arquivo do Power BI Desktop são conectados às tabelas de banco de dados do SQL Azure.
* **Fechar** arquivo do Power BI Desktop.

![Fechar o Power BI Desktop](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

* Use o botão **Salvar** para salvar as alterações. 

Você configurou todos os relatórios correspondentes ao caminho de processamento em lotes na solução. 

## <a name="upload-to-powerbicom"></a>Carregar no *powerbi.com*
1. Navegue até o portal da Web do Power BI em http://powerbi.com e faça logon.
2. Clique em **Obter Dados**  
3. Carregue o arquivo do Power BI Desktop.  
4. Para carregar, clique em **Obter Dados -> Obter Arquivos -> Arquivo local**  
5. Navegue para o **“**ConnectedCarsPbiReport.pbix**”**  
6. Depois que o arquivo é carregado, você será direcionado para o espaço de trabalho do Power BI.  

Um conjunto de dados, relatórios e um painel em branco serão criados para você.  

Fixe os gráficos no novo painel chamado **Painel de Análise de Telemetria do Veículo** no **Power BI**. Clique no painel em branco criado acima e navegue até a seção **Relatórios** e clique no relatório que acabou de ser carregado.  

![Telemetria do veículo Power BI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png) 

**Observe que o relatório tem seis páginas:**  
Página 1: densidade do veículo  
Página 2: integridade do veículo em tempo real  
Página 3: veículos com direção agressiva   
Página 4: veículos que sofreram recall  
Página 5: veículos com uso eficiente de combustível  
Página 6: logotipo da Contoso  

![Carros conectados Power BI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)

**Na Página 3**, fixe o seguinte:  

1. Contagem de vin  
   ![Carros conectados Power BI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png) 
2. Veículos de direção agressiva por modelo – Gráfico de cascata   
   ![Telemetria de veículo - Fixar gráficos 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

**Na Página 5**, fixe o seguinte: 

1. Contagem de vin    
   ![Telemetria de veículo - Fixar gráficos 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png)  
2. Veículos com eficiência de combustível: Gráfico de colunas agrupadas   
   ![Telemetria de veículo - Fixar gráficos 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

**Na Página 4**, fixe o seguinte:  

1. Contagem de vin  
   ![Telemetria de veículo - Fixar gráficos 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 
2. Veículos que tiveram recall por cidade, modelo : Treemap   
   ![Telemetria de veículo - Fixar gráficos 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)  

**Na Página 6**, fixe o seguinte:  

1. Logotipo da Contoso Motors   
   ![Telemetria de veículo - Fixar gráficos 9](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

**Organizar o painel**  

1. Navegue até o painel
2. Passe a seta do mouse sobre cada gráfico e renomeie-com base na nomenclatura fornecida na imagem do painel completo abaixo. Mova os gráficos conforme o painel abaixo.  
   ![Telemetria de veículo - Organizar painel 2](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png)  
   ![Telemetria do veículo Power BI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)
3. O painel final preenchido deve ser semelhante ao mostrado abaixo se você tiver criado todos os relatórios, como mencionadas neste documento. 

![Telemetria de veículo - Organizar painel 2](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard3.png)

Parabéns! Você criou com êxito os relatórios e o painel para obter informações preditivas, em lote e em tempo real sobre a integridade do veículo e hábitos de condução.  
