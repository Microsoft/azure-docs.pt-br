---
title: Paridade entre regiões públicas e soberanas
titleSuffix: Azure Machine Learning
description: Este artigo lista a paridade de recursos entre a nuvem pública e as regiões do Azure governamental, do Azure Alemanha e do Azure China 21Vianet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: andzha
author: Anurzeuii
ms.date: 12/21/2020
ms.custom: references_regions
ms.openlocfilehash: 88240f9b46997d11f1e7c2d93fa880b004615a11
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97725013"
---
# <a name="azure-machine-learning-sovereign-cloud-parity"></a>Azure Machine Learning a paridade de nuvem do soberanas

Saiba quais recursos de Azure Machine Learning estão disponíveis em regiões de nuvem soberanas. 

Na lista de regiões globais do Azure, há várias regiões "soberanas" que atendem a mercados específicos. Por exemplo, o Azure governamental e as regiões da 21Vianet do Azure na China. Atualmente Azure Machine Learning é implantado nas seguintes regiões de nuvem soberanas:

* Regiões do Azure governamental **dos EUA-Arizona** e **EUA-Virgínia**.
* Região do Azure China 21Vianet **China-leste-2**.

> [!TIP]
> Para diferenciar entre regiões soberanas e não soberanas, este artigo usará o termo __nuvem pública__ para se referir a regiões não soberanas.

Nós visamos fornecer a paridade máxima entre nossa nuvem pública e regiões soberanass. Todos os recursos de Azure Machine Learning estarão disponíveis nessas regiões dentro **de 30 dias do GA** (disponibilidade geral) em nossa nuvem pública. Também habilitamos um número selecionado de recursos de visualização nessas regiões. Abaixo, exiba as diferenças de paridade atuais entre nossas soberanas e nuvens públicas.

## <a name="azure-government"></a>Azure Government 

| Recurso | Status da nuvem pública  | US-Virginia | US-Arizona| 
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|:-------------:|
| **Machine learning automatizado** | | | |
| Criar e executar experimentos em notebooks                                    | GA                   | YES                | YES         |
| Criar e executar experimentos na experiência Web do estúdio                        | Visualização pública       | YES                | YES         |
| Recursos de previsão líderes do setor                                  | GA                   | YES                | YES         |
| Suporte para aprendizado profundo e outros aprendizes avançados                      | GA                   | YES                | YES         |
| Suporte a dados grandes (até 100 GB)                                          | Visualização pública       | YES                | YES         |
| Integração do Azure Databricks                                              | GA                   | Não                 | Não          |
| Integrações do SQL, CosmosDB e HDInsight                                   | GA                   | YES                | YES         |
| **Pipelines de Machine Learning** |   |  | | 
| Criar, executar e publicar pipelines usando o SDK do Azure ML                   | GA                   | YES                | YES         |
| Criar pontos de extremidade de pipeline usando o SDK do Azure ML                           | GA                   | YES                | YES         |
| Criar, editar e excluir execuções agendadas de pipelines usando o SDK do Azure ML | GA                   | Ok               | Ok        |
| Exibir detalhes de execução de pipeline no estúdio                                        | GA                   | YES                | YES         |
| Criar, executar, Visualizar e publicar pipelines no Azure ML designer          | GA      | YES                | YES         |
| Integração de Azure Databricks com o pipeline ML                             | GA                   | Não                 | Não          |
| Criar pontos de extremidade de pipeline no Azure ML designer                             | GA      | YES                | YES         |
| **Blocos de anotações integrados** |   |  | | 
| Bloco de anotações de espaço de trabalho e compartilhamento de arquivos                                        | GA                   | YES                | YES         |
| Suporte a R e Python                                                       | GA                   | YES                | YES         |
| Suporte para Rede Virtual                                                    | Visualização pública       | Não                 | Não          |
| **Instância de computação** |   |  | | 
| Instâncias de computação gerenciadas para blocos de anotações integrados                         | GA                   | YES                | YES         |
| Integração do Jupyter, JupyterLab                                            | GA                   | YES                | YES         |
| Suporte de rede virtual (VNet)                                             | Visualização pública       | YES                | YES         |
| **Suporte a SDK** |  |  | | 
| Suporte do SDK do R                                                              | Visualização pública       | YES                | YES         |
| Suporte ao SDK do Python                                                         | GA                   | YES                | YES         |
| **Segurança** |   | | | 
| Suporte de rede virtual (VNet) para treinamento                                | GA                   | YES                | YES         |
| Suporte à VNet (rede virtual) para inferência                               | GA                   | YES                | YES         |
| Autenticação de ponto de extremidade de Pontuação                                            | Visualização pública       | YES                | YES         |
| Link privado do local de trabalho                                                     | Visualização pública       | Não                 | Não          |
| ACI por trás da VNet                                                            | Visualização pública       | Não                 | Não          |
| ACR por trás da VNet                                                            | Visualização pública       | Não                 | Não          |
| IP privado do cluster AKS                                                  | Visualização pública       | Não                 | Não          |
| **Computação** |   | | |
| gerenciamento de cotas entre espaços de trabalho                                         | GA                   | YES                | YES         |
| **Dados para aprendizado de máquina** |   | | |
| Criar, exibir ou editar conjuntos de itens e repositórios de armazenamento do SDK                  | GA                   | YES                | YES         |
| Criar, exibir ou editar conjuntos de itens e repositórios de armazenamento da interface do usuário                   | GA                   | YES                | YES         |
| Exibir, editar ou excluir monitores de descompasso de DataSet do SDK                   | Visualização pública       | YES                | YES         |
| Exibir, editar ou excluir monitores de descompasso de conjunto de banco de interface do usuário                    | Visualização pública       | YES                | YES         |
| **Ciclo de vida do Machine Learning** |   | | |
| Criação de perfil de modelo                                                            | GA                   | YES                | PARTIAL     |
| A extensão DevOps do Azure para Machine Learning & o CLI do Azure ML         | GA                   | YES                | YES         |
| Modelos de Aceleração de Hardware com base em FPGA                                     | GA                   | Não                 | Não          |
| Integração do Visual Studio Code                                             | Visualização pública       | Não                 | Não          |
| Integração da Grade de Eventos                                                     | Visualização pública       | Não                 | Não          |
| Integrar o Azure Stream Analytics com o Azure Machine Learning               | Visualização pública       | Não                 | Não          |
| **Rotulagem** |   | | |
| Rotulando Portal de Gerenciamento de projeto                                        | GA                   | YES                | YES         |
| Portal do Labeler                                                            | GA                   | YES                | YES         |
| Rotulando usando a força de funcionários privada                                          | GA                   | YES                | YES         |
| Rotulagem assistida de ML (classificação de imagem e detecção de objeto)           | Visualização pública       | YES                | YES         |
| **ML responsável** |   | | |
| Explicabilidade na interface do usuário                                                       | Visualização pública       | Não                 | Não          |
| SmartNoise Toolkit de privacidade diferencial                                    | SOS                  | Não                 | Não          |
| marcas personalizadas no Azure Machine Learning para implementar folhas de os              | GA                   | Não                 | Não          |
| Integração do AzureML de imparcialidade                                               | Visualização pública       | Não                 | Não          |
| SDK de interpretação                                                      | GA                   | YES                | YES         |
| **Treinamento** |   | | |
| Streaming de log de experimentação                                              | GA                   | YES                | YES         |
| Aprendizado de reforço                                                     | Visualização pública       | Não                 | Não          |
| IU de experimentação                                                         | GA                   | YES                | YES         |
| Integração do .NET ML.NET 1,0                                                | GA                   | YES                | YES         |
| **Inferência** |   | | |
| Inferência do lote                                                          | GA                   | YES                | YES         |
| Data Box Edge com FPGA                                                    | Visualização pública       | Não                 | Não          |
| **Outras** |   | | |
| Conjunto de Dados em Aberto no Azure                                                              | Visualização pública       | YES                | YES         |
| Pesquisa Cognitiva personalizado                                                    | Visualização pública       | YES                | YES         |
| Muitos modelos                                                                | Visualização pública       | Não                 | Não          |


### <a name="azure-government-scenarios"></a>Cenários do Azure governamental

| Cenário                                                    | US-Virginia | US-Arizona| Limitações  |
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|-------------|
| **Configuração de segurança geral** |   | | |
| Comunicação de rede privada entre serviços                                     | Não | Não | Nenhum link privado no momento | 
| Desabilitar/controlar o acesso à Internet (entrada e saída) e VNet específica | PARTIAL| PARTIAL   | O ACR por trás da VNet não está disponível no Azure governamental – verificação dupla em ACI | 
| Posicionamento para todos os recursos/serviços associados  | YES | YES |  |
| Criptografia em repouso e em trânsito.                                                 | YES | YES |  |
| Acesso de raiz e SSH para recursos de computação.                                          | YES | YES |  |
| Manter a segurança dos sistemas implantados (instâncias, pontos de extremidade, etc.), incluindo o Endpoint Protection, aplicação de patch e registro em log |  PARTIAL|  PARTIAL |ACI atrás da VNet e do ponto de extremidade privado atualmente não disponível |                                  
| Controlar (desabilitar/limitar/restringir) o uso da integração de ACI/AKS                    | PARTIAL| PARTIAL |ACI atrás da VNet e do ponto de extremidade privado atualmente não disponível|
| Controle de acesso baseado em função do Azure (RBAC do Azure) – criações de função personalizadas                           | YES | YES |  |
| Controlar o acesso a imagens ACR usadas pelo serviço ML (Azure fornecido/mantido versus personalizado)  |PARTIAL|  PARTIAL | Não há suporte para ACR por trás do ponto de extremidade privado e VNet no Azure governamental |
| **Uso geral do serviço de Machine Learning** |  | | |
| Capacidade de ter um ambiente de desenvolvimento para criar um modelo, treinar esse modelo, hospedá-lo como um ponto de extremidade e consumi-lo por meio de um webapp     | YES | YES |  |
| Capacidade de efetuar pull de dados do ADLS (Data Lake Storage)                                 |YES | YES |  |
| Capacidade de efetuar pull de dados do armazenamento de BLOBs do Azure                                       |YES | YES |  |



### <a name="additional-azure-government-limitations"></a>Limitações adicionais do Azure governamental

* Para Azure Machine Learning instâncias de computação, a capacidade de atualizar um token com duração de mais de 24 horas não está disponível no Azure governamental.
* A criação de perfil de modelo não dá suporte a 4 CPUs na região de US-Arizona.   
* Os notebooks de exemplo podem não funcionar no Azure governamental se precisarem de acesso aos dados públicos.
* Endereços IP: o comando da CLI usado na [VNet e instruções de túnel forçado](how-to-secure-training-vnet.md#forced-tunneling) não retorna intervalos de IP. Em vez disso, use os [intervalos de IP e as marcas de serviço do Azure para o Azure governamental](https://www.microsoft.com/download/details.aspx?id=57063) .
* Para pipelines agendados, também fornecemos um mecanismo de gatilho baseado em BLOB. Não há suporte para esse mecanismo em espaços de trabalho do CMK. Para habilitar um gatilho baseado em blob para espaços de trabalho do CMK, você precisa fazer uma configuração adicional. Para obter mais informações, consulte [disparar uma execução de um pipeline de Machine Learning de um aplicativo lógico](how-to-trigger-published-pipeline.md).
* Firewalls: ao usar uma região do Azure governamental, adicione os seguintes hosts adicionais à sua configuração de firewall:

    * Para uso do Arizona: `usgovarizona.api.ml.azure.us`
    * Para uso da Virgínia: `usgovvirginia.api.ml.azure.us`
    * Para ambos: `graph.windows.net` 


## <a name="azure-china-21vianet"></a>Azure China 21Vianet 

| Recurso                                       | Status da nuvem pública | CH-leste-2 | CH-norte-3 |
|----------------------------------------------------------------------------|:------------------:|:--------------------:|:-------------:|
| **Machine learning automatizado** |    | | |
| Criar e executar experimentos em notebooks                                    | GA               | YES       | N/D        |
| Criar e executar experimentos na experiência Web do estúdio                        | Visualização pública   | YES       | N/D        |
| Recursos de previsão líderes do setor                                  | GA               | YES       | N/D        |
| Suporte para aprendizado profundo e outros aprendizes avançados                      | GA               | YES       | N/D        |
| Suporte a dados grandes (até 100 GB)                                          | Visualização pública   | YES       | N/D        |
| Integração do Azure Databricks                                              | GA               | Não        | N/D        |
| Integrações do SQL, CosmosDB e HDInsight                                   | GA               | YES       | N/D        |
| **Pipelines de Machine Learning** |    | | |
| Criar, executar e publicar pipelines usando o SDK do Azure ML                   | GA               | YES       | N/D        |
| Criar pontos de extremidade de pipeline usando o SDK do Azure ML                           | GA               | YES       | N/D        |
| Criar, editar e excluir execuções agendadas de pipelines usando o SDK do Azure ML | GA               | YES       | N/D        |
| Exibir detalhes de execução de pipeline no estúdio                                        | GA               | YES       | N/D        |
| Criar, executar, Visualizar e publicar pipelines no Azure ML designer          | GA  | YES       | N/D        |
| Integração de Azure Databricks com o pipeline ML                             | GA               | Não        | N/D        |
| Criar pontos de extremidade de pipeline no Azure ML designer                             | GA   | YES       | N/D        |
| **Blocos de anotações integrados** |   | | |
| Bloco de anotações de espaço de trabalho e compartilhamento de arquivos                                        | GA               | YES       | N/D        |
| Suporte a R e Python                                                       | GA               | YES       | N/D        |
| Suporte para Rede Virtual                                                    | Visualização pública   | Não        | N/D        |
| **Instância de computação** |    | | |
| Instâncias de computação gerenciadas para blocos de anotações integrados                         | GA               | Não        | N/D        |
| Integração do Jupyter, JupyterLab                                            | GA               | YES       | N/D        |
| Suporte de rede virtual (VNet)                                             | Visualização pública   | YES       | N/D        |
| **Suporte a SDK** |    | | |
| Suporte do SDK do R                                                              | Visualização pública   | YES       | N/D        |
| Suporte ao SDK do Python                                                         | GA               | YES       | N/D        |
| **Segurança** |   | | |
| Suporte de rede virtual (VNet) para treinamento                                | GA               | YES       | N/D        |
| Suporte à VNet (rede virtual) para inferência                               | GA               | YES       | N/D        |
| Autenticação de ponto de extremidade de Pontuação                                            | Visualização pública   | YES       | N/D        |
| Link privado do local de trabalho                                                     | Visualização pública   | Não        | N/D        |
| ACI por trás da VNet                                                            | Visualização pública   | Não        | N/D        |
| ACR por trás da VNet                                                            | Visualização pública   | Não        | N/D        |
| IP privado do cluster AKS                                                  | Visualização pública   | Não        | N/D        |
| **Computação** |   | | |
| gerenciamento de cotas entre espaços de trabalho                                         | GA               | YES       | N/D        |
| **Dados para aprendizado de máquina** | | | |
| Criar, exibir ou editar conjuntos de itens e repositórios de armazenamento do SDK                  | GA               | YES       | N/D        |
| Criar, exibir ou editar conjuntos de itens e repositórios de armazenamento da interface do usuário                   | GA               | YES       | N/D        |
| Exibir, editar ou excluir monitores de descompasso de DataSet do SDK                   | Visualização pública   | YES       | N/D        |
| Exibir, editar ou excluir monitores de descompasso de conjunto de banco de interface do usuário                    | Visualização pública   | YES       | N/D        |
| **Ciclo de vida do Machine Learning** |    | | |
| Criação de perfil de modelo                                                            | GA               | PARTIAL   | N/D        |
| A extensão DevOps do Azure para Machine Learning & o CLI do Azure ML         | GA               | YES       | N/D        |
| Modelos de Aceleração de Hardware com base em FPGA                                     | GA               | Não        | N/D        |
| Integração do Visual Studio Code                                             | Visualização pública   | Não        | N/D        |
| Integração da Grade de Eventos                                                     | Visualização pública   | YES       | N/D        |
| Integrar o Azure Stream Analytics com o Azure Machine Learning               | Visualização pública   | Não        | N/D        |
| **Rotulagem** |    | | |
| Rotulando Portal de Gerenciamento de projeto                                        | GA               | YES       | N/D        |
| Portal do Labeler                                                            | GA               | YES       | N/D        |
| Rotulando usando a força de funcionários privada                                          | GA               | YES       | N/D        |
| Rotulagem assistida de ML (classificação de imagem e detecção de objeto)           | Visualização pública   | YES       | N/D        |
| **ML responsável** |    | | |
| Explicabilidade na interface do usuário                                                       | Visualização pública   | Não        | N/D        |
| SmartNoise Toolkit de privacidade diferencial                                    | SOS              | Não        | N/D        |
| marcas personalizadas no Azure Machine Learning para implementar folhas de os              | GA               | Não        | N/D        |
| Integração do AzureML de imparcialidade                                               | Visualização pública   | Não        | N/D        |
| SDK de interpretação                                                      | GA               | YES       | N/D        |
| **Treinamento** |    | | |
| Streaming de log de experimentação                                              | GA               | YES       | N/D        |
| Aprendizado de reforço                                                     | Visualização pública   | Não        | N/D        |
| IU de experimentação                                                         | GA               | YES       | N/D        |
| Integração do .NET ML.NET 1,0                                                | GA               | YES       | N/D        |
| **Inferência** |   | | |
| Inferência do lote                                                          | GA               | YES       | N/D        |
| Data Box Edge com FPGA                                                    | Visualização pública   | Não        | N/D        |
| **Outras** |    | | |
| Conjunto de Dados em Aberto no Azure                                                              | Visualização pública   | YES       | N/D        |
| Pesquisa Cognitiva personalizado                                                    | Visualização pública   | YES       | N/D        |
| Muitos modelos                                                                | Visualização pública   | Não        | N/D        |



### <a name="additional-azure-china-limitations"></a>Limitações adicionais do Azure China

* O Azure China tem SKU de VM limitada, especialmente para SKU de GPU. Ele tem apenas V100 (família NCv3).
* Os pontos de extremidade da API REST são diferentes do Azure global. Use a tabela a seguir para localizar o ponto de extremidade da API REST para as regiões do Azure China:

    | Ponto de extremidade REST                 | Azure Global                                 | China-Government                           |
    |------------------|--------------------------------------------|--------------------------------------------|
    | Plano de gerenciamento | `https://management.azure.com/`              | `https://management.chinacloudapi.cn/`       |
    | Plano de dados       | `https://{location}.experiments.azureml.net` | `https://{location}.experiments.ml.azure.cn` |
    | Azure Active Directory              | `https://login.microsoftonline.com`          | `https://login.chinacloudapi.cn`             |

* O bloco de anotações de exemplo pode não funcionar, se precisar de acesso a dados públicos.
* Intervalos de endereços IP: o comando da CLI usado nas instruções de [túnel forçado da VNet](how-to-secure-training-vnet.md#forced-tunneling) não retorna intervalos de IP. Em vez disso, use os [intervalos de IP e as marcas de serviço do Azure para o Azure China](https://www.microsoft.com//download/details.aspx?id=57062) .
* Não há suporte para a visualização de instâncias de computação Azure Machine Learning em um espaço de trabalho em que o link privado está habilitado por enquanto, mas o CI terá suporte na próxima implantação para a expansão de serviço para todas as regiões AML.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as regiões em que o Azure Machine Learning está disponível, consulte [produtos por região](https://azure.microsoft.com/global-infrastructure/services/).
