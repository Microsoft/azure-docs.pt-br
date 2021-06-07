---
title: Traga seu próprio ML para o Azure Sentinel | Microsoft Docs
description: Este artigo explica como criar e usar seus próprios algoritmos de aprendizado de máquina para análise de dados no Azure Sentinel.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: yelevin
ms.openlocfilehash: f8d795dcf64230140e1dd07e3f30ca3aa6825ab4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99806996"
---
# <a name="bring-your-own-machine-learning-ml-into-azure-sentinel"></a>Traga seu próprio Machine Learning (ML) para o Azure Sentinel

O Machine Learning (ML) é um dos principais subfixes do Azure Sentinel e um dos principais atributos que o configuram. O Azure Sentinel oferece ML em várias experiências: o mecanismo de correlação de [fusão](fusion.md) e os notebooks Jupyter e a plataforma de ml (BYO ml) recentemente disponível. 

Os modelos de detecção de ML podem se adaptar a ambientes individuais e a alterações no comportamento do usuário, para reduzir falsos positivos e identificar ameaças que não seriam encontradas com uma abordagem tradicional. Muitas organizações de segurança entendem o valor de ML para segurança, embora muitas delas tenham o luxo de profissionais que têm experiência em segurança e ML. Nós projetamos a estrutura apresentada aqui para que as organizações e os profissionais de segurança cresçam conosco em sua jornada de ML. As organizações novas para o ML ou sem a experiência necessária podem obter um valor de proteção significativo dos recursos de ML internos do Sentinela do Azure.

:::image type="content" source="./media/bring-your-own-ml/machine-learning-framework.png" alt-text="estrutura do Machine Learning":::

## <a name="what-is-the-bring-your-own-machine-learning-byo-ml-platform"></a>O que é a plataforma traga sua própria Machine Learning (BYO-ML)?

Para organizações que têm recursos de ML e gostaria de criar modelos de ML personalizados para suas necessidades de negócios exclusivas, oferecemos a **plataforma BYO-ml**. A plataforma usa o ambiente de [](/azure/databricks/scenarios/what-is-azure-databricks) / [Apache Spark](http://spark.apache.org/) Azure Databricks e notebooks Jupyter para produzir o ambiente de ml. Ele fornece os seguintes componentes:

- um pacote BYO-ML, que inclui bibliotecas para ajudá-lo a acessar dados e enviar os resultados por push para Log Analytics (LA), para que você possa integrar os resultados com sua detecção, investigação e busca. 

- Modelos de algoritmo ML para que você personalize o para se ajustar a problemas de segurança específicos em sua organização. 

- blocos de anotações de exemplo para treinar o modelo e agendar a Pontuação do modelo. 

Além de tudo isso, você pode trazer seus próprios modelos de ML e/ou seu próprio ambiente Spark para integrar com o Azure Sentinel.

Com a plataforma BYO-ML, você pode começar a criar seus próprios modelos de ML: 

- O notebook com dados de exemplo ajuda você a ter uma experiência prática de ponta a ponta, sem se preocupar com o tratamento de dados de produção.

- O pacote integrado ao ambiente Spark reduz os desafios e os conflitos no gerenciamento da infraestrutura.

- As bibliotecas dão suporte a movimentações de dados. Os notebooks de treinamento e pontuação demonstram a experiência de ponta a ponta e servem como modelo para que você se adapte ao seu ambiente.

### <a name="use-cases"></a>Casos de uso
 
A plataforma e o pacote do BYO-ML reduzem significativamente o tempo e o esforço necessários para criar suas próprias detecções de ML e eles liberam a capacidade de resolver problemas de segurança específicos no Azure Sentinel. A plataforma dá suporte aos seguintes casos de uso:

**Treine um algoritmo ml para obter um modelo personalizado:** Você pode pegar um algoritmo ML existente (compartilhado pela Microsoft ou pela comunidade de usuários) e treiná-lo facilmente em seus próprios dados para obter um modelo de ML personalizado que se adapte melhor aos seus dados e ao seu ambiente.

**Modifique um modelo de algoritmo ml para obter o modelo personalizado:** Você pode modificar um modelo de algoritmo ML (compartilhado pela Microsoft ou pela comunidade de usuários) e treinar o algoritmo modificado em seus próprios dados, para derivar um modelo personalizado para se ajustar ao seu problema específico.

**Crie seu próprio modelo:** Crie seu próprio modelo do zero usando a plataforma e os utilitários BYO-ML do Azure Sentinel.

**Integre seu ambiente databricks/Spark:** Integre seu ambiente databricks/Spark existente ao Azure sentinela e use bibliotecas e modelos BYO-ML para criar modelos de ML para suas situações exclusivas.

**Importe seu próprio modelo de ml:** Você pode importar seus próprios modelos de ML e usar a plataforma e os utilitários do BYO-ML para integrá-los ao Azure Sentinel.

**Compartilhar um algoritmo ml:** Compartilhe um algoritmo ML para a Comunidade adotar e adaptar.

**Use ml para capacitar o SecOps:** use seu próprio modelo de ml personalizado e os resultados para busca, detecções, investigação e resposta.

Este artigo mostra os componentes da plataforma BYO-ML e como aproveitar a plataforma e o algoritmo de acesso a recursos anormais para fornecer uma detecção personalizada de ML com o Azure Sentinel.

## <a name="azure-databricksspark-environment"></a>Ambiente Azure Databricks/Spark

[Apache Spark™](http://spark.apache.org/) fez um avanço para simplificar o Big data fornecendo uma estrutura unificada para a criação de pipelines de dados. Azure Databricks aproveita ainda mais o fornecimento de uma plataforma de nuvem de gerenciamento zero criada com base no Spark. É recomendável que você use o databricks para sua plataforma BYO-ML, para que você possa se concentrar em encontrar respostas que façam um impacto imediato em seus negócios, em vez de abordar os pipelines de dados e os problemas de plataforma.
Se você já tiver databricks ou qualquer outro ambiente do Spark e preferir usar a configuração existente, o pacote BYO-ML também funcionará bem neles. 

## <a name="byo-ml-package"></a>Pacote BYO-ML

O pacote ML BYO inclui as práticas recomendadas e a pesquisa da Microsoft no front-end do ML para segurança. Neste pacote, fornecemos a lista de utilitários, notebooks e modelos de algoritmos a seguir para problemas de segurança.

| Nome do arquivo | Descrição |
| --------- | ----------- |
| azure_sentinel_utilities. WHL | Contém utilitários para a leitura de BLOBs do Azure e gravação no Log Analytics. |
| AnomalousRASampleData | O notebook demonstra o uso do modelo de acesso a recursos anormais no Azure Sentinel com dados de exemplo de treinamento e teste gerados. |
| AnomalousRATraining. ipynb | Notebook para treinar o algoritmo, criar e salvar os modelos. |
| AnomalousRAScoring. ipynb | Bloco de anotações para agendar o modelo a ser executado, Visualizar o resultado e gravar a pontuação de volta no Azure Sentinel. |
|

O primeiro modelo de algoritmo ML que oferecemos é para [detecção de acesso a recursos anormais](https://github.com/Azure/Azure-Sentinel/tree/master/BYOML). Ele é baseado em um algoritmo de filtragem colaborativa e é treinado com logs de acesso de compartilhamento de arquivos do Windows (eventos de segurança com a ID de evento 5140). As principais informações necessárias para esse modelo no log são o emparelhamento de usuários e recursos acessados. 

## <a name="example-walkthrough-anomalous-file-share-access-detection"></a>Exemplo de explicação: detecção de acesso a compartilhamento de arquivos anormal 

Agora que você está familiarizado com os principais componentes da plataforma BYO-ML, aqui está um exemplo para mostrar como usar a plataforma e os componentes para fornecer uma detecção personalizada de ML.

### <a name="setup-the-databricksspark-environment"></a>Configurar o ambiente do databricks/Spark

Você precisará configurar seu próprio ambiente do databricks se ainda não tiver um. Consulte o documento de [início rápido do databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal?tabs=azure-portal) para obter instruções.

### <a name="auto-export-instruction"></a>Instrução de exportação automática

Para criar modelos de ML personalizados com base em seus próprios dados no Azure Sentinel, você precisará exportar seus dados do Log Analytics para um armazenamento de BLOBs ou recurso do hub de eventos, para que o modelo ML possa acessá-lo do databricks. Saiba como [incluir dados no Azure Sentinel](connect-data-sources.md).

Para este exemplo, você precisa ter seus dados de treinamento para o log de acesso de compartilhamento de arquivos no armazenamento de BLOBs do Azure. O formato dos dados é documentado no bloco de anotações e nas bibliotecas.

Você pode exportar seus dados automaticamente de Log Analytics usando a [CLI (interface de linha de comando) do Azure](/cli/azure/monitor/log-analytics). 

Você deve ser atribuído à função **colaborador** em seu espaço de trabalho log Analytics, sua conta de armazenamento e seu recurso de EventHub para executar os comandos. 

Aqui está um conjunto de comandos de exemplo para configurar a exportação automática:

```azurecli

az –version

# Login with Azure CLI
 az login

# List all Log Analytics clusters
 az monitor log-analytics cluster list

# Set to specific subscription
 az account set --subscription "SUBSCRIPTION_NAME"
 
# Export to Storage - all tables
 az monitor log-analytics workspace data-export create --resource-group "RG_NAME" --workspace-name "WS_NAME" -n LAExportCLIStr --destination “DESTINATION_NAME" --enable "true" --tables SecurityEvent
 
# Export to EventHub - all tables
 az monitor log-analytics workspace data-export create --resource-group "RG_NAME" --workspace-name "WS_NAME" -n LAExportCLIEH --destination “DESTINATION_NAME" --enable "true" --tables SecurityEvent Heartbeat"]

# List export settings
az monitor log-analytics workspace data-export list --resource-group "RG_NAME" --workspace-name "WS_NAME"

# Delete export setting
 az monitor log-analytics workspace data-export delete --resource-group "RG_NAME" --workspace-name "WS_NAME" --name "NAME"
```

### <a name="export-custom-data"></a>Exportar dados personalizados

Para dados personalizados que não têm suporte pelo Log Analytics exportação automática, você pode usar o aplicativo lógico ou outras soluções para mover seus dados. Você pode consultar o blog [Exportando log Analytics dados para o repositório de blob](https://www.borninthecloud.com/exporting-log-analytics-data-to-blob-store/?preview=true) e o script.

### <a name="correlate-with-data-outside-of-azure-sentinel"></a>Correlacione-se com dados fora do Azure Sentinel

Você também pode trazer dados de fora do Azure Sentinel para o armazenamento de BLOBs ou Hub de eventos e correlacioná-los com os dados do Azure Sentinel para criar seus modelos de ML. 
 
### <a name="copy-and-install-the-related-packages"></a>Copiar e instalar os pacotes relacionados

Copie o pacote BYO-ML do repositório GitHub do Azure Sentinel mencionado acima para seu ambiente do databricks. Em seguida, abra os notebooks e siga as instruções no bloco de anotações para instalar as bibliotecas necessárias em seus clusters.

### <a name="model-training-and-scoring"></a>Treinamento e Pontuação do modelo

Siga as instruções nos dois blocos de anotações para alterar as configurações de acordo com seu próprio ambiente e recursos, siga as etapas para treinar e compilar seu modelo e, em seguida, agende o modelo para pontuar os logs de acesso de compartilhamento de arquivos recebidos.

### <a name="write-results-to-log-analytics"></a>Gravar resultados em Log Analytics

Depois de obter a pontuação agendada, você poderá usar o módulo no bloco de anotações de Pontuação para gravar os resultados da pontuação no espaço de trabalho Log Analytics associado à instância do Sentinela do Azure.

### <a name="check-results-in-azure-sentinel"></a>Verificar resultados no Azure Sentinel

Para ver os resultados pontuados junto com os detalhes de log relacionados, volte para o portal do Azure Sentinel. Em **logs** > logs personalizados, você verá os resultados na tabela **AnomalousResourceAccessResult_CL** (ou seu próprio nome de tabela personalizado). Você pode usar esses resultados para aprimorar suas experiências de investigação e busca.

:::image type="content" source="./media/bring-your-own-ml/anomalous-resource-access-logs.png" alt-text="logs de acesso a recursos anormais":::

### <a name="build-custom-analytics-rule-with-ml-results"></a>Criar regra de análise personalizada com resultados de ML

Depois de confirmar que os resultados de ML estão na tabela de logs personalizados e você estiver satisfeito com a fidelidade das pontuações, poderá criar uma detecção com base nos resultados. Vá para **análise** no portal do Azure Sentinel e [crie uma nova regra de detecção](tutorial-detect-threats-custom.md). Veja abaixo um exemplo que mostra a consulta usada para criar a detecção.

:::image type="content" source="./media/bring-your-own-ml/create-byo-ml-analytics-rule.png" alt-text="Criar regra de análise personalizada para as detecções de B s M L":::

### <a name="view-and-respond-to-incidents"></a>Exibir e responder a incidentes
Depois de configurar a regra de análise com base nos resultados de ML, se houver resultados acima do limite definido na consulta, um incidente será gerado e exibido na página **incidentes** no Azure Sentinel. 

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a usar a plataforma BYO-ML do Azure Sentinel para criar ou importar seus próprios algoritmos de aprendizado de máquina para analisar dados e detectar ameaças.

- Veja postagens sobre o aprendizado de máquina e muitos outros tópicos relevantes no [blog do Azure Sentinel](https://aka.ms/azuresentinelblog).