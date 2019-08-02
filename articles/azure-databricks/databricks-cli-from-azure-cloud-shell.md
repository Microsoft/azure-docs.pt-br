---
title: 'Usar Databricks CLI do Azure Cloud Shell '
description: Saiba como usar a CLI do Databricks do Azure Cloud Shell.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: mamccrea
ms.openlocfilehash: dae481fb477223f149404c6a09cad024bc15cd90
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204953"
---
# <a name="use-databricks-cli-from-azure-cloud-shell"></a>Usar Databricks CLI do Azure Cloud Shell

Saiba como usar a CLI do Databricks do Azure Cloud Shell para executar operações em Databricks.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster e workspace do Azure Databricks. Para obter instruções, consulte [Introdução ao Azure Databricks](quickstart-create-databricks-workspace-portal.md). 

* Configurar um token de acesso pessoal no Databricks. Para obter instruções, consulte o [Gerenciamento de Token](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management).

## <a name="use-the-azure-cloud-shell"></a>Usar o Azure Cloud Shell

1. Faça logon no [Portal do Azure](https://portal.azure.com).
 
2. No canto superior direito, clique no ícone do **Cloud Shell**.

   ![Inicializar Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/launch-azure-cloud-shell.png "Inicializar Azure Cloud Shell")

3. Certifique-se de selecionar **Bash** para o ambiente do Cloud Shell. É possível selecionar pela opção da lista suspensa, como mostrado na captura de tela a seguir.

   ![Selecione Bash para o ambiente do Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/select-bash-for-shell.png "Selecionar Bash") 

4. Crie um ambiente virtual no qual você possa instalar a CLI do Databricks. No snippet de código abaixo, você cria um ambiente virtual chamado `databrickscli`.

       virtualenv -p /usr/bin/python2.7 databrickscli

5. Alterne para o ambiente virtual criado.

       source databrickscli/bin/activate

6. Instale a CLI do Databricks.

       pip install databricks-cli

7. Configure a autenticação com o Databricks utilizando o token de acesso que você criou e listado como parte dos pré-requisitos. Use o seguinte comando:

       databricks configure --token

    Você receberá os seguintes prompts:

    * Primeiro, você será solicitado a inserir o host do Databricks. Digite o valor no formato `https://eastus2.azuredatabricks.net`. Aqui, **Leste dos EUA 2** é a região do Azure onde o workspace do Azure Databricks foi criado.

    * Em seguida, você será solicitado a inserir um token. Insira o token que você criou anteriormente.

Após concluir essas etapas, será possível iniciar usando a CLI do Databricks do Azure Cloud Shell.

## <a name="use-databricks-cli"></a>Usar CLI do Databricks

Agora, você pode iniciar usando a CLI do Databricks. Por exemplo, execute o comando a seguir para listar todos os clusters do Databricks que existirem no workspace.

    databricks clusters list

Também é possível usar o comando a seguir para acessar o DBFS (Sistema de Arquivos Databricks).

    databricks fs ls


Para obter uma referência completa nos comandos, consulte [CLI do Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html).


## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre a CLI do Azure, consulte [Visão geral da CLI do Azure](../cloud-shell/overview.md)
* Para ver uma lista de comandos da CLI do Azure, consulte [Referência da CLI do Azure CLI](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest)
* Para ver uma lista de comandos da CLI do Databricks, consulte [CLI do Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)


