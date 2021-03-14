---
title: Conectar-se a dados nos serviços de armazenamento no Azure
titleSuffix: Azure Machine Learning
description: Crie armazenamentos e conjuntos de dados para conectar-se com segurança nos serviços de armazenamento no Azure com o Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 09/22/2020
ms.custom: how-to, data4ml
ms.openlocfilehash: 0e2230bdcf2b2f4358db4a0d600506b711f39c45
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508224"
---
# <a name="connect-to-data-with-the-azure-machine-learning-studio"></a>Conectar-se a dados com o Azure Machine Learning Studio

Neste artigo, saiba como acessar seus dados com o [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md). Conecte-se aos seus dados nos serviços de armazenamento no Azure com [Azure Machine Learning repositórios](how-to-access-data.md)de dados e, em seguida, empacote-os para as tarefas em seus fluxos de trabalho de AM com [conjuntos Azure Machine Learning](how-to-create-register-datasets.md).

A tabela a seguir define e resume os benefícios de armazenamentos e conjuntos de tabelas. 

|Objeto|Descrição| Benefícios|   
|---|---|---|
|Armazenamentos de dados| Conecte-se com segurança ao seu serviço de armazenamento no Azure, armazenando as informações de conexão, como sua ID de assinatura e autorização de token em seu [Key Vault](https://azure.microsoft.com/services/key-vault/) associado ao espaço de trabalho | Como suas informações são armazenadas com segurança, você <br><br> <li> Não &nbsp; Coloque &nbsp; &nbsp; as credenciais de autenticação &nbsp; ou as &nbsp; &nbsp; fontes de dados originais em risco. <li> Não é mais necessário embuti-los em código em seus scripts.
|Conjunto de dados| Ao criar um conjunto de dados, você cria uma referência para o local da fonte de dados com uma cópia de seus metadados. Com conjuntos de os, você pode, <br><br><li> Acesse dados durante o treinamento do modelo.<li> Compartilhe dados e colabore com outros usuários.<li> Aproveite as bibliotecas de software livre, como o pandas, para a exploração de dados. | Como os conjuntos de dados são avaliados lentamente, e eles permanecem em seu local existente, você <br><br><li>Mantenha uma única cópia de dados em seu armazenamento.<li> Não incorrer nenhum custo de armazenamento extra <li> Não arrisque a alteração acidental de suas fontes de dados originais.<li>Melhorar as velocidades de desempenho de fluxo de trabalho ML. 

Para entender onde os armazenamentos e conjuntos de dados se ajustam no fluxo de trabalho geral de acesso a data do Azure Machine Learning, consulte o artigo [dados de acesso seguro](concept-data.md#data-workflow) .

Para uma experiência de código inicial, consulte os seguintes artigos para usar o [SDK do Azure Machine Learning Python](/python/api/overview/azure/ml/) para:
* [Conecte-se aos serviços de armazenamento do Azure com armazenamentos de](how-to-access-data.md)Data. 
* [Crie Azure Machine Learning conjuntos de valores](how-to-create-register-datasets.md). 

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

- Acesso ao [Azure Machine Learning Studio](https://ml.azure.com/).

- Um Workspace do Azure Machine Learning. [Criar um workspace do Azure Machine Learning](how-to-manage-workspace.md).

    -  Quando você cria um espaço de trabalho, um contêiner de BLOBs do Azure e um compartilhamento de arquivos do Azure são automaticamente registrados como repositórios de armazenamento no espaço de trabalho. Seus nomes são `workspaceblobstore` e `workspacefilestore`, respectivamente. Se o armazenamento de BLOBs for suficiente para suas necessidades, o `workspaceblobstore` será definido como o repositório de armazenamento padrão e já estará configurado para uso. Caso contrário, você precisa de uma conta de armazenamento no Azure com um [tipo de armazenamento com suporte](how-to-access-data.md#matrix).
    

## <a name="create-datastores"></a>Criar repositórios de armazenamento

Você pode criar armazenamentos de dados [dessas soluções de armazenamento do Azure](how-to-access-data.md#matrix). **Para soluções de armazenamento sem suporte** e para salvar o custo de egresso de dados durante experimentos de ml, você deve [mover seus dados](how-to-access-data.md#move) para uma solução de armazenamento do Azure com suporte. [Saiba mais sobre repositórios de armazenamento](how-to-access-data.md). 

Crie um novo repositório de armazenamento em algumas etapas com o Azure Machine Learning Studio.

> [!IMPORTANT]
> Se sua conta de armazenamento de dados estiver em uma rede virtual, serão necessárias etapas de configuração adicionais para garantir que o estúdio tenha acesso aos seus dados. Consulte [isolamento de rede & privacidade](how-to-enable-studio-virtual-network.md) para garantir que as etapas de configuração apropriadas sejam aplicadas.

1. Entre no [Estúdio do Azure Machine Learning](https://ml.azure.com/).
1. Selecione **Armazenamentos de dados** no painel esquerdo em **Gerenciar**.
1. Selecione **+ Novo armazenamento de dados**.
1. Preencha o formulário para criar e registrar um novo repositório de armazenamento. O formulário se atualiza de forma inteligente com base nas seleções de tipo de armazenamento e tipo de autenticação do Azure. Consulte a [seção acesso e permissões de armazenamento](#access-validation) para entender onde encontrar as credenciais de autenticação de que você precisa para preencher este formulário.

O exemplo a seguir demonstra a aparência do formulário quando você cria um **repositório de armazenamento de BLOBs do Azure**:

![Formulário de um novo armazenamento de dados](media/how-to-connect-data-ui/new-datastore-form.png)

## <a name="create-datasets"></a>Criar conjuntos de dados

Depois de criar um datastore, crie um conjunto de dados para interagir com eles. Os conjuntos de dados embalam seu dado em um objeto de consumo avaliado lentamente para tarefas de aprendizado de máquina, como treinamento. [Saiba mais sobre conjuntos de dados](how-to-create-register-datasets.md).

Há dois tipos de conjuntos de dados, filedataset e TabularDataset. 
Os [DataSets](how-to-create-register-datasets.md#filedataset) criam referências a um ou vários arquivos ou a URLs públicas. Enquanto isso, [TabularDatasets](how-to-create-register-datasets.md#tabulardataset) representa seus dados em um formato tabular. 

As etapas e a animação a seguir mostram como criar um conjunto de um DataSet no [Azure Machine Learning Studio](https://ml.azure.com).

> [!Note]
> Os conjuntos de valores criados por meio do Azure Machine Learning Studio são automaticamente registrados no espaço de trabalho.

![Criar um conjunto de uma com a interface do usuário](./media/how-to-connect-data-ui/create-dataset-ui.gif)

Para criar um conjunto de um DataSet no estúdio:
1. Entre no [Azure Machine Learning Studio](https://ml.azure.com/).
1. Selecione **conjuntos** de itens na seção **ativos** do painel esquerdo.
1. Selecione **criar conjunto** de um para escolher a origem do conjunto de seus conjuntos de um. Essa fonte pode ser arquivos locais, um repositório de armazenamento, URLs públicas ou [conjuntos de arquivos abertos do Azure](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md).
1. Selecione **tabela** ou **arquivo** para o tipo de conjunto de texto.
1. Selecione **Avançar** para abrir o formulário **repositório de armazenamento e seleção de arquivo** . Neste formulário, você seleciona onde deseja manter o conjunto de dados após a criação, bem como selecionar quais arquivos que deseja usar para o conjunto.
    1. Habilite ignorar validação se os dados estiverem em uma rede virtual. Saiba mais sobre o [isolamento e a privacidade da rede virtual](how-to-enable-studio-virtual-network.md).
    1. Para conjuntos de tabelas de tabela, você pode especificar uma característica ' timeseries ' para habilitar as operações relacionadas ao tempo em seu conjunto de linhas. Saiba como [Adicionar a característica da série temporal ao seu conjunto](how-to-monitor-datasets.md#studio-dataset)de informações.
1. Selecione **Avançar** para popular as **configurações e** os formulários de visualização e **esquema** ; Eles são populados de forma inteligente com base no tipo de arquivo e você pode configurar ainda mais seu conjunto de informações antes da criação nesses formulários. 
1. Selecione **Avançar** para examinar o formulário **confirmar detalhes** . Verifique suas seleções e crie um perfil de dados opcional para seu conjunto de dados. Saiba mais sobre a [criação de perfil de dados](#profile).
1. Selecione **criar** para concluir a criação do conjunto de seus conjuntos de os.

<a name="profile"></a>

### <a name="data-profile-and-preview"></a>Perfil de dados e visualização

Depois de criar o conjunto de seus conjuntos de um, verifique se você pode exibir o perfil e a versão prévia no estúdio com as etapas a seguir. 

1. Entrar no [Azure Machine Learning Studio](https://ml.azure.com/)
1. Selecione **conjuntos** de itens na seção **ativos** do painel esquerdo.
1. Selecione o nome do conjunto de um que você deseja exibir. 
1. Selecione a guia **Explorar** . 
1. Selecione a guia **Visualização** ou **perfil** . 

![Exibir Perfil e visualização do conjunto de e](./media/how-to-connect-data-ui/dataset-preview-profile.gif)

É possível obter uma grande variedade de estatísticas de resumo em seu conjunto de dados para verificar se ele está pronto para o ML. Para colunas não numéricas, eles incluem apenas estatísticas básicas, como mínimo, máximo e contagem de erros. Para colunas numéricas, também é possível revisar os momentos estatísticos e os quantis estimados. 

Especificamente, o Azure Machine Learning perfil de dados do conjunto inclui:

>[!NOTE]
> Entradas em branco são exibidas para recursos com tipos irrelevantes.

|Estatística|Descrição
|------|------
|Recurso| Nome da coluna que está sendo resumida.
|Perfil| Visualização em linha baseada no tipo inferido. Por exemplo, cadeias de caracteres, boolianos e datas terão contagens de valor, enquanto decimais (numéricos) têm histogramas aproximados. Isso permite que você obtenha uma compreensão rápida da distribuição dos dados.
|Distribuição de tipos| Contagem de valor em linha de tipos dentro de uma coluna. Os nulos são do seu próprio tipo, portanto, essa visualização é útil para detectar valores ímpares ou ausentes.
|Type|Tipo inferido da coluna. Os valores possíveis incluem: cadeias de caracteres, boolianos, datas e decimais.
|Mín| Valor mínimo da coluna. Entradas em branco aparecem para recursos cujo tipo não tem uma ordenação inerente (como, Boolianos).
|Max| Valor máximo da coluna. 
|Contagem| Quantidade total de entradas ausentes e não ausentes na coluna.
|Sem contagem faltando| Quantidade de entradas na coluna que não estão ausentes. Cadeias de caracteres e erros vazios são tratados como valores, portanto, eles não contribuirão para a “contagem de não ausentes”.
|Quantis| Valores aproximados em cada quantil para fornecer uma noção da distribuição dos dados.
|Média| Média aritmética ou média da coluna.
|Desvio padrão| Medida da quantidade de dispersão ou variação dos dados dessa coluna.
|Variance| A medida de quão difundidos estão os dados dessa coluna em comparação ao valor médio. 
|Distorção| Medida de quão diferentes os dados dessa coluna são em comparação a uma distribuição normal.
|Curtose| Medida de quão profundamente conectados os dados dessa coluna estão em comparação a uma distribuição normal.

## <a name="storage-access-and-permissions"></a>Acesso e permissões de armazenamento

Para garantir que você se conecte com segurança ao serviço de armazenamento do Azure, Azure Machine Learning exige que você tenha permissão para acessar o armazenamento de dados correspondente. Esse acesso depende das credenciais de autenticação usadas para registrar o repositório de armazenamento.

### <a name="virtual-network"></a>Rede virtual

Se sua conta de armazenamento de dados estiver em uma **rede virtual**, serão necessárias etapas de configuração adicionais para garantir que Azure Machine Learning tenha acesso aos seus dados. Consulte [isolamento de rede & privacidade](how-to-enable-studio-virtual-network.md) para garantir que as etapas de configuração apropriadas sejam aplicadas quando você criar e registrar seu repositório de armazenamento.  

### <a name="access-validation"></a>Validação de acesso

**Como parte do processo inicial de criação e registro de armazenamento de datastore**, Azure Machine Learning valida automaticamente que o serviço de armazenamento subjacente existe e que a entidade de segurança fornecida pelo usuário (nome de usuário, entidade de serviço ou token SAS) tem acesso ao armazenamento especificado.

**Após a criação do repositório de armazenamento**, essa validação é executada somente para métodos que exigem acesso ao contêiner de armazenamento subjacente, e **não** sempre que os objetos de repositório de data são recuperados. Por exemplo, a validação ocorrerá se você quiser baixar arquivos do seu armazenamento de dados; mas se você quiser apenas alterar o armazenamento de dados padrão, a validação não ocorrerá.

Para autenticar seu acesso ao serviço de armazenamento subjacente, você pode fornecer sua chave de conta, tokens de SAS (assinaturas de acesso compartilhado) ou entidade de serviço de acordo com o tipo de repositório de armazenamento que você deseja criar. A [matriz de tipo de armazenamento](how-to-access-data.md#matrix) lista os tipos de autenticação com suporte que correspondem a cada tipo de repositório de armazenamento.

Você pode encontrar informações de chave de conta, token SAS e entidade de serviço em seu [portal do Azure](https://portal.azure.com).

* Se você planeja usar uma chave de conta ou um token SAS para autenticação, selecione **Contas de Armazenamento** no painel esquerdo e escolha a conta de armazenamento que deseja registrar.
  * A página **Visão Geral** fornece informações como o nome da conta, o contêiner e o nome do compartilhamento de arquivo.
      1. Para obter as chaves de conta, vá para **Chaves de acesso** no painel **Configurações**.
      1. Para obter os tokens SAS, vá para **Assinaturas de acesso compartilhado** no painel **Configurações**.

* Se você planeja usar uma [entidade de serviço](../active-directory/develop/howto-create-service-principal-portal.md) para autenticação, vá para o **registros de aplicativo** e selecione qual aplicativo você deseja usar.
    * Sua página de **visão geral** correspondente conterá informações necessárias, como ID do locatário e ID do cliente.

> [!IMPORTANT]
> * Se você precisar alterar suas chaves de acesso para uma conta de armazenamento do Azure (chave de conta ou token SAS), certifique-se de sincronizar as novas credenciais com seu espaço de trabalho e os repositórios de armazenamento conectados a ela. Saiba como [sincronizar suas credenciais atualizadas](how-to-change-storage-access-key.md). <br> <br>
> * Se você cancelar o registro e registrar novamente um repositório de armazenamento com o mesmo nome e falhar, o Azure Key Vault para seu espaço de trabalho pode não ter a exclusão reversível habilitada. Por padrão, a exclusão reversível está habilitada para a instância do cofre de chaves criada pelo seu espaço de trabalho, mas poderá não ser habilitada se você tiver usado um cofre de chaves existente ou ter um espaço de trabalho criado antes de outubro de 2020. Para obter informações sobre como habilitar a exclusão reversível, consulte [ativar a exclusão reversível para um cofre de chaves existente]( https://docs.microsoft.com/azure/key-vault/general/soft-delete-change#turn-on-soft-delete-for-an-existing-key-vault). "

### <a name="permissions"></a>Permissões

Para o contêiner de blob do Azure e o armazenamento Azure Data Lake Gen 2, verifique se suas credenciais de autenticação têm acesso ao **leitor de dados de blob de armazenamento** . Saiba mais sobre o [leitor de dados de blob de armazenamento](../role-based-access-control/built-in-roles.md#storage-blob-data-reader). Um token SAS de conta usa como padrão nenhuma permissão. 
* Para **acesso de leitura** de dados, suas credenciais de autenticação devem ter um mínimo de permissões de lista e leitura para contêineres e objetos. 

* Para **acesso de gravação** de dados, permissões de gravação e adição também são necessárias.

## <a name="train-with-datasets"></a>Treinar com conjuntos de dados

Use seus conjuntos de informações em seus experimentos de aprendizado de máquina para modelos de ML de treinamento. [Saiba mais sobre como treinar com conjuntos de os](how-to-train-with-datasets.md)

## <a name="next-steps"></a>Próximas etapas

* [Um exemplo passo a passo de treinamento com o TabularDatasets e o aprendizado de máquina automatizado](tutorial-first-experiment-automated-ml.md).

* [Treinar um modelo](how-to-set-up-training-targets.md).

* Para obter mais exemplos de treinamento de conjunto de informações, consulte os [blocos de anotações de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/).