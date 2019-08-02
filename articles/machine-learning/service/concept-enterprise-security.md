---
title: Segurança do Enterprise
titleSuffix: Azure Machine Learning service
description: 'Usar o serviço Azure Machine Learning com segurança: autenticação, autorização, segurança de rede, criptografia de dados e monitoramento.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/10/2019
ms.openlocfilehash: 8682baa961ca3270e76614702b51ac50f197e847
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795482"
---
# <a name="enterprise-security-for-azure-machine-learning-service"></a>Segurança corporativa para o serviço Azure Machine Learning

Neste artigo, você aprenderá sobre os recursos de segurança disponíveis com o serviço de aprendizado de máquina do Azure.

Ao usar um serviço de nuvem, é uma prática recomendada para restringir o acesso somente aos usuários que precisam dela. Isso começa com a compreensão do modelo de autenticação e autorização usado pelo serviço. Você talvez também queira restringir o acesso à rede ou ingressar com segurança recursos em sua rede local com aqueles na nuvem. Criptografia de dados também é essencial em repouso e enquanto os dados se movem entre os serviços. Por fim, você precisa ser capaz de monitorar o serviço e produzir um log de auditoria de todas as atividades.

## <a name="authentication"></a>Autenticação
Autenticação multifator é suportada se o Azure Active Directory (Azure AD) está configurado para o mesmo.
* Cliente faz logon no Azure AD e obtém o token do Azure Resource Manager.  Os usuários e entidades de serviço são totalmente compatíveis.
* Cliente apresenta o token para o Azure Resource Manager e todos os serviços do Azure Machine Learning
* Serviço de Machine Learning do Azure fornece um token do Azure Machine Learning para a computação do usuário. Por exemplo, a computação do Machine Learning. Esse token é usado pelo usuário do Azure Machine Learning de computação para o retorno de chamada no serviço de Azure Machine Learning (limita o escopo ao espaço de trabalho) após a execução for concluída.

![Captura de tela mostrando como funciona a autenticação no serviço de Azure Machine Learning](./media/enterprise-readiness/authentication.png)

### <a name="authentication-keys-for-web-service-deployment"></a>Chaves de autenticação de implantação do serviço Web

Ao habilitar a autenticação para uma implantação, você cria automaticamente as chaves de autenticação.

* A autenticação é ativada por padrão ao implantar no Serviço de Kubernetes do Azure.
* A autenticação é desabilitada por padrão durante a implantação nas Instâncias de Contêiner do Azure.

Para controlar a autenticação, use o parâmetro `auth_enabled` ao criar ou atualizar uma implantação.

Se a autenticação estiver ativada, você poderá usar o método `get_keys` para recuperar uma chave de autenticação primária e secundária:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Se você precisar regenerar uma chave, use [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)


## <a name="authorization"></a>Autorização

Você pode criar vários workspaces, e cada workspace pode ser compartilhado por várias pessoas. Ao compartilhar um workspace, controle o acesso ao atribuir as funções a seguir para os usuários:
* Proprietário
* Colaborador
* Leitor
    
A tabela a seguir lista algumas das principais operações de serviço de Azure Machine Learning e as funções que podem executá-las:

| Operação de serviço de Azure Machine Learning | Proprietário | Colaborador | Leitor |
| ---- |:----:|:----:|:----:|
| Criar espaço de trabalho | ✓ | ✓ | |
| Compartilhar o espaço de trabalho | ✓ | |  |
| Criar de computação | ✓ | ✓ | |
| Anexar a computação | ✓ | ✓ | |
| Anexar a armazenamentos de dados | ✓ | ✓ | |
| Executar um experimento | ✓ | ✓ | |
| Exibir/métricas de execuções | ✓ | ✓ | ✓ |
| Registrar modelo | ✓ | ✓ | |
| Criar imagem | ✓ | ✓ | |
| Implantar serviço Web | ✓ | ✓ | |
| Modelos de exibição/imagens | ✓ | ✓ | ✓ |
| Chamar o serviço web | ✓ | ✓ | ✓ |

Se as funções internas não forem suficientes para suas necessidades, você também pode criar funções personalizadas. São as funções personalizadas somente que oferecemos suporte para operações no espaço de trabalho e computação do Machine Learning. As funções personalizadas podem ter ler, gravar ou excluir as permissões no espaço de trabalho e o recurso de computação no espaço de trabalho. Você pode disponibilizar a função em um nível de espaço de trabalho específico, um nível de grupo de recursos específico ou um nível de assinatura específica. Para obter mais informações, consulte [gerenciar usuários e funções em um espaço de trabalho do Azure Machine Learning](how-to-assign-roles.md)

### <a name="securing-compute-and-data"></a>Protegendo dados e computação
Proprietários e colaboradores podem usar todos os repositórios de dados e destinos de computação que estão anexados ao espaço de trabalho.  
Cada espaço de trabalho também tem um atribuído pelo sistema gerenciado identidade associado (com o mesmo nome que o espaço de trabalho) com as seguintes permissões em recursos anexados usados no espaço de trabalho:

Para obter mais informações sobre identidades gerenciadas, consulte [identidades para recursos do Azure gerenciadas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

| Recurso | Permissões |
| ----- | ----- |
| Workspace | Colaborador | 
| Conta de armazenamento | Colaborador de dados de Blob de armazenamento | 
| Key Vault | Acesso a todos os certificados de chaves, segredos, | 
| Registro de Contêiner do Azure | Colaborador | 
| Grupo de recursos que contém o espaço de trabalho | Colaborador | 
| Grupo de recursos que contém o Cofre de chaves (se for diferente daquele que contém o espaço de trabalho) | Colaborador | 

É recomendável que os administradores não revogam o acesso de identidade gerenciada para os recursos mencionados acima. O acesso pode ser restaurado com a operação de ressincronização de chaves.

Serviço de Machine Learning do Azure cria um aplicativo adicional (nome começa com `aml-`) com o acesso de nível de Colaborador em sua assinatura para cada região do espaço de trabalho. Para ex. Se você tiver um espaço de trabalho no Leste dos EUA e outro espaço de trabalho na Europa Setentrional na mesma assinatura, você verá dois aplicativos. Isso é necessário para que o Azure Machine Learning, o serviço pode ajudar a gerenciar os recursos de computação.


## <a name="network-security"></a>Segurança de rede

O Serviço do Azure Machine Learning depende de outros serviços do Azure para recursos de computação. Os recursos de computação (destinos de computação) são usados para treinar e implantar modelos. Esses destinos de computação podem ser criados dentro de uma rede virtual. Por exemplo, é possível usar a Máquina Virtual de Ciência de Dados para treinar um modelo e implantar o modelo no AKS (Serviço de Kubernetes do Azure).  

Para obter mais informações, consulte [como executar testes e Inferência de tipos em uma rede virtual](how-to-enable-virtual-network.md).

## <a name="data-encryption"></a>Criptografia de dados

### <a name="encryption-at-rest"></a>Criptografia em repouso
#### <a name="azure-blob-storage"></a>Armazenamento do Blobs do Azure
Serviço de Machine Learning do Azure armazena instantâneos, saídas e logs na conta de armazenamento de BLOBs do Azure que está vinculada ao espaço de trabalho de serviço do Azure Machine Learning e mora na assinatura do usuário. Todos os dados armazenados no armazenamento de BLOBs do Azure são criptografados em repouso usando chaves Microsoft-Managed.

Para obter mais informações sobre como trazer suas próprias chaves para os dados armazenados no armazenamento de BLOBs do Azure, consulte [criptografia do serviço de armazenamento usando chaves gerenciadas pelo cliente no Azure Key Vault](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).

Os dados de treinamento normalmente também é armazenado no armazenamento de BLOBs do Azure para que ele seja acessível para computação de treinamento. Esse armazenamento não é gerenciado pelo Azure Machine Learning mas montado para computação como um sistema de arquivos remoto.

Para obter informações sobre a regeneração das chaves de acesso para as contas de armazenamento do Azure usadas com seu espaço de trabalho, consulte o [regenerar chaves de acesso de armazenamento](how-to-change-storage-access-key.md) artigo.

#### <a name="cosmos-db"></a>Cosmos DB
Serviço de Machine Learning do Azure armazena as métricas e metadados para o Cosmos DB que reside em uma assinatura gerenciada pelo serviço de Azure Machine Learning da Microsoft. Todos os dados armazenados no Cosmos DB são criptografados em repouso usando chaves gerenciadas da Microsoft.

#### <a name="azure-container-registry-acr"></a>Registro de contêiner do Azure (ACR)
Todas as imagens de contêiner no registro (ACR) são criptografadas em repouso. Azure criptografa uma imagem antes de armazená-la e descriptografa-a em interrupções ao serviço Azure Machine Learning puxa a imagem automaticamente.

#### <a name="machine-learning-compute"></a>Computação do Machine Learning
O disco do sistema operacional para cada nó de computação é armazenado no armazenamento do Azure é criptografado usando chaves gerenciadas da Microsoft em contas de armazenamento do serviço de Azure Machine Learning. Essa computação é efêmera e clusters são geralmente reduzidos quando não houver nenhuma execução na fila. A máquina virtual subjacente é desconfigurada e excluído do disco do sistema operacional. Não há suporte para criptografia de disco do Azure para o disco do sistema operacional.
Cada máquina virtual também tem um disco local temporário para operações de sistema operacional. Esse disco também pode ser usado opcionalmente para transferir os dados de treinamento. Esse disco não está criptografado. Para obter mais informações sobre como funciona a criptografia em repouso no Azure, consulte [dados do Azure com criptografia em repouso](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest). 

### <a name="encryption-in-transit"></a>Criptografia em trânsito
Ambos os comunicação interna entre vários microsserviços do Azure Machine Learning e comunicação externa de chamar o ponto de extremidade de pontuação são suportados usando SSL. Todo o acesso de armazenamento do Azure também está em um canal seguro. Para obter mais informações, consulte [serviços de web seguro do Azure Machine Learning usando o SSL](https://docs.microsoft.com/azure/machine-learning/service/how-to-secure-web-service).

### <a name="using-azure-key-vault"></a>Usando o Azure Key Vault
Instância de cofre da chave associada com o espaço de trabalho é usada pelo serviço do Azure Machine Learning para armazenar as credenciais de vários tipos:
* A cadeia de conexão da conta de armazenamento associado
* Senhas para instâncias do repositório de contêiner do Azure
* Cadeias de Conexão aos dados de armazenamentos. 

Senhas SSH e chaves para os destinos, como o HDInsight do HDI e a VM de computação são armazenadas em um cofre de chaves separado que é associado à assinatura da Microsoft. O serviço do Azure Machine Learning armazenar todas as senhas ou chaves fornecido pelo usuário em vez disso, ele gera, autoriza e armazena suas próprias chaves SSH para se conectar a VM/HDInsight para executar os testes. Cada espaço de trabalho tem uma associado atribuído pelo sistema gerenciado identidade (com o mesmo nome que o espaço de trabalho) que tem acesso a todas as chaves, segredos e certificados no cofre de chaves.

 
## <a name="monitoring"></a>Monitoramento

Os usuários podem ver o log de atividades no espaço de trabalho para ver várias operações executadas no espaço de trabalho e obter as informações básicas, como o nome da operação, o evento iniciado por, timestamp etc.

Captura de tela a seguir mostra o log de atividades para um espaço de trabalho:

![Captura de tela mostrando log de atividades em um espaço de trabalho](./media/enterprise-readiness/workspace-activity-log.png)


Detalhes da solicitação de pontuação são armazenados em AppInsights, que é criado na assinatura do usuário ao criar o espaço de trabalho. Isso inclui campos como HTTPMethod, UserAgent, ComputeType, RequestUrl, StatusCode, RequestId, duração, etc.


## <a name="data-flow-diagram"></a>Diagrama de fluxo de dados

### <a name="create-workspace"></a>Criar espaço de trabalho
O diagrama a seguir mostra o fluxo de trabalho do espaço de trabalho de criar.
Usuário faz logon no AD do Azure de qualquer um dos clientes de serviço com suporte do Azure Machine Learning (portal do Azure da CLI, SDK do Python) e solicita o token do Azure Resource Manager apropriado.  Usuário, em seguida, chama o Azure Resource Manager para criar o espaço de trabalho.  Provedor de recursos para provisionar o espaço de trabalho de serviço do Azure Resource Manager contatos do Azure Machine Learning.  Recursos adicionais são criados na assinatura do cliente durante a criação do espaço de trabalho:
* KeyVault (para armazenar segredos)
* Uma conta de armazenamento do Azure (incluindo Blob & compartilhamento de arquivos)
* Registro de contêiner do Azure (para armazenar imagens do docker para inferência/pontuação e experimentação)
* Application Insights (para armazenar telemetria)

Outros serviços de computação anexados a um espaço de trabalho (serviço Kubernetes do Azure, VM etc.) também podem ser provisionados pelos clientes, conforme necessário. 

![Captura de tela mostrando cria fluxo de trabalho do espaço de trabalho](./media/enterprise-readiness/create-workspace.png)

### <a name="save-source-code-training-scripts"></a>Salve o código-fonte (scripts de treinamento)
O diagrama a seguir mostra o fluxo de trabalho de instantâneo do código.
Associado a um Azure Machine Learning espaço de trabalho de serviço são diretórios (experimentos), que contém o código-fonte (scripts de treinamento).  Eles são armazenados no computador de local do cliente e na nuvem (no armazenamento de BLOBs do Azure na assinatura do cliente). Esses instantâneos de código são usados para execução ou inspeção de auditoria históricos.

![Captura de tela mostrando cria fluxo de trabalho do espaço de trabalho](./media/enterprise-readiness/code-snapshot.png)

### <a name="training"></a>Treinamento
O diagrama a seguir mostra o fluxo de trabalho de treinamento.
* O serviço do Azure Machine Learning é chamado com a ID do instantâneo para o instantâneo do código salvo acima
* O Azure Machine Learning, o serviço cria executar ID (opcional) e o token de serviço do Azure Machine Learning, que será usada posteriormente pelos destinos de computação como o aprendizado de máquina computação/VM para responder ao serviço Azure Machine Learning
* Você pode escolher uma computação gerenciada (ex. Computação de aprendizado de máquina) ou não gerenciado de computação (por exemplo, VM) para executar seus trabalhos de treinamento. Fluxo de dados é explicado para ambos os cenários a seguir:
* (VM/HDInsight – acessado usando credenciais SSH no Key Vault na assinatura da Microsoft) O serviço do Azure Machine Learning executa código de gerenciamento no destino de computação que:
    1.  Prepara o ambiente (Observação: O docker é uma opção para VM/Local também. Consulte as etapas de computação do Machine Learning abaixo entender o experimento como em execução no works de contêiner do docker)
    2.  Baixa o código
    3.  Configura as variáveis de ambiente/configurações
    4.  Executa o script de usuário (instantâneo de código mencionado acima)
* (Computação do machine Learning – acessada usando a identidade do espaço de trabalho gerenciado) Observe que como computação do Machine Learning é uma computação gerenciada ou seja, ele é gerenciado pela Microsoft, assim ele é executado sob a assinatura do Microsoft.
    1.  Construção do Docker remota é iniciada, se necessário
    2.  Grava o código de gerenciamento ao usuário o compartilhamento de arquivos do Azure
    3.  Inicia o contêiner com inicial de comando, ou seja, código de gerenciamento na etapa anterior


#### <a name="querying-runs--metrics"></a>Consultar & métricas de execuções
Esta etapa é mostrada no fluxo de onde o treinamento computação gravações a *métricas executar* volta para o serviço de Azure Machine Learning de onde ele é armazenado no Cosmos DB. Os clientes poderão chamar o serviço de Azure Machine Learning que por sua vez extrairá as métricas do Cosmos DB e retorná-lo de volta ao cliente.

![Captura de tela mostrando cria fluxo de trabalho do espaço de trabalho](./media/enterprise-readiness/training-and-metrics.png)

### <a name="creating-web-services"></a>Criando serviços web
O diagrama a seguir mostra o fluxo de trabalho de inferência de tipos. Inferência de tipos ou modelo de pontuação, é a fase em que o modelo implantado é usado para previsão, mais comumente em dados de produção.
Consulte os detalhes abaixo:
* Usuário registra um modelo usando um cliente, como o SDK do Azure ML
* Usuário cria a imagem usando o modelo, o arquivo de pontuação e outras dependências de modelo
* A imagem do Docker é criada e armazenada no ACR
* Serviço Web é implantada para o destino de computação (ACI/AKS) usando a imagem criada acima
* Detalhes da solicitação de pontuação são armazenados em AppInsights, que está na assinatura do usuário
* Telemetria também é enviada para a assinatura do Microsoft/Azure

![Captura de tela mostrando cria fluxo de trabalho do espaço de trabalho](./media/enterprise-readiness/inferencing.png)

## <a name="next-steps"></a>Próximas etapas

* [Proteger serviços Web do Azure Machine Learning com SSL](how-to-secure-web-service.md)
* [Consumir um modelo de ML implantado como um serviço Web](how-to-consume-web-service.md)
* [Como executar previsões em lotes](how-to-run-batch-predictions.md)
* [Monitorar seus modelos do Azure Machine Learning com o Application Insights](how-to-enable-app-insights.md)
* [Coletar dados para modelos em produção](how-to-enable-data-collection.md)
* [SDK do Serviço do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Usar o Serviço do Azure Machine Learning com redes virtuais do Azure](how-to-enable-virtual-network.md)
* [Melhores práticas para criar sistemas de recomendação](https://github.com/Microsoft/Recommenders)
* [Compilar uma API de recomendação em tempo real no Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
