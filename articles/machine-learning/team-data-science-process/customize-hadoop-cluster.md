---
title: "Personalizar clusters Hadoop para o Processo de Ciência de Dados de Equipe | Microsoft Docs"
description: "Módulos de Python populares disponibilizados em clusters do Hadoop do Azure HDInsight personalizados."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 0c115dca-2565-4e7a-9536-6002af5c786a
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: hangzh;bradsev
ms.openlocfilehash: 522e33b399f2648427464b439bc4405e9e8097cd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="customize-azure-hdinsight-hadoop-clusters-for-the-team-data-science-process"></a>Personalizar clusters Hadoop do Azure HDInsight para o Processo de Ciência de Dados de Equipe
Este artigo descreve como personalizar um cluster Hadoop do HDInsight instalando o Anaconda de 64 bits (Python 2.7) em cada nó quando o cluster for provisionado como um serviço do HDInsight. Ele também mostra como acessar o nó principal para enviar trabalhos personalizados para o cluster. Essa personalização disponibiliza muitos módulos Python populares incluídos no Anaconda para uso em UDFs (Funções Definidas pelo Usuário) projetadas para processar registros Hive no cluster. Para obter instruções sobre os procedimentos usados neste cenário, veja [Como enviar consultas do Hive](move-hive-tables.md#submit).

Este menu leva a tópicos que descrevem como configurar os diversos ambientes de ciência de dados usados pelo [TDSP (Processo de Ciência de Dados de Equipe)](overview.md).

[!INCLUDE [data-science-environment-setup](../../../includes/cap-setup-environments.md)]

## <a name="customize"></a>Personalizar o cluster do Hadoop do Azure HDInsight
Para criar um cluster personalizado Hadoop do HDInsight, faça logon no [**Portal Clássico do Azure**](https://manage.windowsazure.com/), clique em **Novo** no canto inferior esquerdo e selecione SERVIÇOS DE DADOS -> HDINSIGHT -> **CRIAÇÃO PERSONALIZADA** para abrir a janela **Detalhes do Cluster**. 

![Criar espaço de trabalho](./media/customize-hadoop-cluster/customize-cluster-img1.png)

Insira o nome do cluster a ser criado na página de configuração 1 e aceite os valores padrão para os outros campos. Clique na seta para ir para a próxima página de configuração. 

![Criar espaço de trabalho](./media/customize-hadoop-cluster/customize-cluster-img1.png)

Na página de configuração 2, insira o número dos **NÓS DE DADOS**, selecione a **REGIÃO/REDE VIRTUAL** e selecione os tamanhos do **NÓ PRINCIPAL** e **NÓ DE DADOS**. Clique na seta para ir para a próxima página de configuração.

> [!NOTE]
> A **REGIÃO/REDE VIRTUAL** precisa ser a mesma que a região da conta de armazenamento que será usada para o cluster do Hadoop do HDInsight. Caso contrário, na quarta página de configuração, a conta de armazenamento não aparecerá na lista suspensa de **NOME DA CONTA**.
> 
> 

![Criar espaço de trabalho](./media/customize-hadoop-cluster/customize-cluster-img3.png)

Na página de configuração 3, forneça um nome de usuário e senha para o cluster do Hadoop do HDInsight. **Não** selecione *Inserir o Hive/Oozie Metastore*. Clique na seta para ir para a próxima página de configuração. 

![Criar espaço de trabalho](./media/customize-hadoop-cluster/customize-cluster-img4.png)

Na página de configuração 4, especifique o nome da conta de armazenamento, o contêiner padrão do cluster do Hadoop do HDInsight. Se você selecionar *Criar contêiner padrão* na lista suspensa **CONTÊINER PADRÃO**, será criado um contêiner com um nome igual ao do cluster. Clique na seta para ir para a última página de configuração.

![Criar espaço de trabalho](./media/customize-hadoop-cluster/customize-cluster-img5.png)

Na página de configuração **Ações de Script** final, clique no botão **adicionar ação de script** e preencha os campos de texto com os valores a seguir.

* **NOME** – qualquer cadeia de caracteres como o nome dessa ação de script
* **TIPO DE NÓ** – selecione **Todos os nós**
* **URI do SCRIPT** - *http://getgoing.blob.core.windows.net/publicscripts/Azure_HDI_Setup_Windows.ps1* 
  * *publicscripts* é um contêiner público na conta de armazenamento 
  * *getgoing* é usado para compartilhar arquivos de script do PowerShell a fim de facilitar o trabalho dos usuários no Azure
* **PARÂMETROS** - (deixe em branco)

Por fim, clique na marca de seleção para iniciar a criação do cluster personalizado do Hadoop do HDInsight. 

![Criar espaço de trabalho](./media/customize-hadoop-cluster/script-actions.png)

## <a name="headnode"></a> Acessar o nó principal do Cluster do Hadoop
É necessário habilitar o acesso remoto ao cluster do Hadoop no Azure para acessar o nó principal do cluster do Hadoop via RDP. 

1. Faça logon no [**Portal Clássico do Azure**](https://manage.windowsazure.com/), selecione **HDInsight** à esquerda, selecione o cluster Hadoop na lista de clusters, clique na guia **CONFIGURAÇÃO** e, em seguida, clique no ícone **HABILITAR CONEXÃO REMOTA** na parte inferior da página.
   
    ![Criar espaço de trabalho](./media/customize-hadoop-cluster/enable-remote-access-1.png)
2. Na janela **Configurar Área de Trabalho Remota** , insira os campos NOME DE USUÁRIO e SENHA e selecione a data de validade para o acesso remoto. Em seguida, clique na marca de seleção para habilitar o acesso remoto ao nó principal do cluster do Hadoop.
   
    ![Criar espaço de trabalho](./media/customize-hadoop-cluster/enable-remote-access-2.png)

> [!NOTE]
> O nome de usuário e senha para o acesso remoto não são os mesmos usados ao criar o cluster do Hadoop. Eles são um conjunto separado de credenciais. Além disso, a data de validade do acesso remoto deve estar dentro de 7 dias a partir da data atual.
> 
> 

Após habilitar o acesso remoto, clique em **CONECTAR** na parte inferior da página para iniciar a conexão remota com o nó principal. Faça logon no nó principal do cluster do Hadoop inserindo as credenciais do usuário de acesso remoto que você especificou anteriormente.

![Criar espaço de trabalho](./media/customize-hadoop-cluster/enable-remote-access-3.png)

As próximas etapas do processo de análise avançada são mapeadas no [TDSP (Processo de Ciência de Dados de Equipe)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) e podem incluir etapas que movem dados para o HDInsight, que os processa e que obtém amostras deles como parte da preparação para o aprendizado baseado em dados com o Azure Machine Learning.

Veja [Como enviar consultas do Hive](move-hive-tables.md#submit) para obter instruções sobre como acessar os módulos Python incluídos no Anaconda por meio do nó principal do cluster em UDFs (funções definidas pelo usuário) usadas para processar registros do Hive armazenados no cluster.

