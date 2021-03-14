---
title: Habilitar o Azure Machine Learning Studio em uma rede virtual
titleSuffix: Azure Machine Learning
description: Saiba como configurar o Azure Machine Learning Studio para acessar dados armazenados dentro de uma rede virtual.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 10/21/2020
ms.custom: contperf-fy20q4, tracking-python
ms.openlocfilehash: ca3957563f7c7a7021ad994cc323823763c2936e
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102171536"
---
# <a name="use-azure-machine-learning-studio-in-an-azure-virtual-network"></a>Usar o Azure Machine Learning Studio em uma rede virtual do Azure

Neste artigo, você aprenderá a usar o Azure Machine Learning Studio em uma rede virtual. O estúdio inclui recursos como AutoML, o designer e o rotulamento de dados. Para usar esses recursos em uma rede virtual, você deve seguir as etapas neste artigo.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> - Conceda ao estúdio acesso aos dados armazenados dentro de uma rede virtual.
> - Acesse o estúdio de um recurso dentro de uma rede virtual.
> - Entenda como o estúdio afeta a segurança do armazenamento.

Este artigo é a parte cinco de uma série de cinco partes que orienta você pela proteção de um fluxo de trabalho Azure Machine Learning. É altamente recomendável que você leia as partes anteriores para configurar um ambiente de rede virtual.

Consulte os outros artigos desta série:

[1. visão geral da VNet](how-to-network-security-overview.md)  >  [2. Proteja o espaço de trabalho](how-to-secure-workspace-vnet.md)  >  [3. Proteja o ambiente de treinamento](how-to-secure-training-vnet.md)  >  [4. Proteja o ambiente do inferência](how-to-secure-inferencing-vnet.md)  >  **5. Habilitar a funcionalidade do estúdio**


> [!IMPORTANT]
> Se o seu espaço de trabalho estiver em uma __nuvem soberanas__, como Azure governamental ou Azure China 21vianet, os notebooks integrados _não_ dão suporte ao uso de armazenamento que esteja em uma rede virtual. Em vez disso, você pode usar Jupyter Notebooks em uma instância de computação. Para obter mais informações, consulte a seção [acessar dados em um notebook da instância de computação](how-to-secure-training-vnet.md#access-data-in-a-compute-instance-notebook) .

## <a name="prerequisites"></a>Pré-requisitos

+ Leia a [visão geral de segurança de rede](how-to-network-security-overview.md) para entender cenários e arquitetura de rede virtual comum.

+ Uma rede virtual e sub-rede pré-existentes a serem usadas.

+ Um [espaço de trabalho Azure Machine Learning existente com o link privado habilitado](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint).

+ Uma [conta de armazenamento do Azure existente adicionou sua rede virtual](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints).

## <a name="configure-data-access-in-the-studio"></a>Configurar o acesso a dados no estúdio

Alguns dos recursos do estúdio estão desabilitados por padrão em uma rede virtual. Para reabilitar esses recursos, você deve habilitar a identidade gerenciada para contas de armazenamento que pretende usar no estúdio. 

As operações a seguir estão desabilitadas por padrão em uma rede virtual:

* Visualizar dados no estúdio.
* Visualize dados no designer.
* Implante um modelo no designer ([conta de armazenamento padrão](#enable-managed-identity-authentication-for-default-storage-accounts)).
* Envie um experimento do AutoML ([conta de armazenamento padrão](#enable-managed-identity-authentication-for-default-storage-accounts)).
* Inicie um projeto de rotulagem.

O estúdio dá suporte à leitura de dados dos seguintes tipos de repositório de armazenamento em uma rede virtual:

* Blob do Azure
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Banco de Dados SQL do Azure

### <a name="configure-datastores-to-use-workspace-managed-identity"></a>Configurar os repositórios de armazenamento para usar identidade gerenciada pelo espaço de trabalho

Depois de adicionar uma conta de armazenamento do Azure à sua rede virtual com um [ponto de extremidade de serviço](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints) ou [ponto de extremidade particular](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-private-endpoints), você deve configurar seu repositório de armazenamento para usar a autenticação de [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) . Isso permite que o estúdio acesse dados em sua conta de armazenamento.

Azure Machine Learning usa [repositórios](concept-data.md#datastores) de armazenamento para se conectar a contas de armazenamento. Use as etapas a seguir para configurar um repositório de armazenamento para usar a identidade gerenciada:

1. No estúdio, selecione __repositórios de armazenamento__.

1. Para atualizar um repositório de armazenamento existente, selecione o repositório de armazenamento e selecione __Atualizar credenciais__.

    Para criar um novo repositório de armazenamento, selecione __+ novo repositório de armazenamento__.

1. Nas configurações do armazenamento de dados, selecione __Sim__ para  __usar a identidade gerenciada do espaço de trabalho para visualização e criação de perfil no Azure Machine Learning Studio__.

    ![Captura de tela mostrando como habilitar a identidade do espaço de trabalho gerenciado](./media/how-to-enable-studio-virtual-network/enable-managed-identity.png)

Essas etapas adicionam a identidade gerenciada pelo espaço de trabalho como um __leitor__ ao serviço de armazenamento usando o Azure RBAC. O acesso de __leitor__ permite que o espaço de trabalho recupere as configurações de firewall para garantir que os dados não saiam da rede virtual. As alterações podem levar até 10 minutos para entrar em vigor.

### <a name="enable-managed-identity-authentication-for-default-storage-accounts"></a>Habilitar a autenticação de identidade gerenciada para contas de armazenamento padrão

Cada espaço de trabalho Azure Machine Learning tem duas contas de armazenamento padrão, uma conta de armazenamento de BLOBs padrão e uma conta de armazenamento de arquivo padrão, que são definidas quando você cria seu espaço de trabalho. Você também pode definir novos padrões na página de gerenciamento do **repositório de armazenamento** .

![Captura de tela mostrando onde os repositórios de armazenamento padrão podem ser encontrados](./media/how-to-enable-studio-virtual-network/default-datastores.png)

A tabela a seguir descreve porque você deve habilitar a autenticação de identidade gerenciada para suas contas de armazenamento padrão do espaço de trabalho.

|Conta de armazenamento  | Observações  |
|---------|---------|
|Armazenamento de BLOBs padrão do espaço de trabalho| Armazena ativos de modelo do designer. Você deve habilitar a autenticação de identidade gerenciada nessa conta de armazenamento para implantar modelos no designer. <br> <br> Você pode visualizar e executar um pipeline de designer se ele usa um repositório de armazenamento não padrão que foi configurado para usar a identidade gerenciada. No entanto, se você tentar implantar um modelo treinado sem identidade gerenciada habilitada no repositório de armazenamento padrão, a implantação falhará independentemente de quaisquer outros repositórios de armazenamento em uso.|
|Repositório de arquivos padrão do espaço de trabalho| Armazena ativos de teste do AutoML. Você deve habilitar a autenticação de identidade gerenciada nessa conta de armazenamento para enviar experimentos de AutoML. |

> [!WARNING]
> Há um problema conhecido em que o repositório de arquivos padrão não cria automaticamente a `azureml-filestore` pasta, que é necessária para enviar experimentos AutoML. Isso ocorre quando os usuários trazem um filestore existente para definir como o repositório de armazenamento padrão durante a criação do espaço de trabalho.
> 
> Para evitar esse problema, você tem duas opções: 1) usar o repositório de armazenamento padrão que é criado automaticamente para a criação do espaço de trabalho. 2) para colocar seu próprio repositório de armazenamento, verifique se o filestore está fora da VNet durante a criação do espaço de trabalho. Depois que o espaço de trabalho for criado, adicione a conta de armazenamento à rede virtual.
>
> Para resolver esse problema, remova a conta de armazenamento de filestore da rede virtual e adicione-a novamente à rede virtual.

### <a name="grant-workspace-managed-identity-__reader__-access-to-storage-private-link"></a>Conceder acesso do __leitor__ de identidade gerenciada do espaço de trabalho ao link privado do armazenamento

Se sua conta de armazenamento do Azure usa um ponto de extremidade privado, você deve conceder ao **leitor** de identidade gerenciado por espaço de trabalho acesso ao link privado. Para obter mais informações, consulte a função interna do [leitor](../role-based-access-control/built-in-roles.md#reader) . 

Se sua conta de armazenamento usar um ponto de extremidade de serviço, você poderá ignorar esta etapa.

## <a name="access-the-studio-from-a-resource-inside-the-vnet"></a>Acessar o estúdio de um recurso dentro da VNet

Se você estiver acessando o Studio de um recurso dentro de uma rede virtual (por exemplo, uma instância de computação ou máquina virtual), deverá permitir o tráfego de saída da rede virtual para o estúdio. 

Por exemplo, se você estiver usando NSG (grupos de segurança de rede) para restringir o tráfego de saída, adicione uma regra a um destino de __marca de serviço__ de __AzureFrontDoor. frontend__.

## <a name="technical-notes-for-managed-identity"></a>Notas técnicas para identidade gerenciada

Usar identidade gerenciada para acessar serviços de armazenamento afeta as considerações de segurança. Esta seção descreve as alterações para cada tipo de conta de armazenamento. 

Essas considerações são exclusivas para o __tipo de conta de armazenamento__ que você está acessando.

### <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

Para o __armazenamento de BLOBs do Azure__, a identidade gerenciada por espaço de trabalho também é adicionada como um [leitor de dados de blob](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) para que possa ler dados do armazenamento de BLOBs.

### <a name="azure-data-lake-storage-gen2-access-control"></a>Controle de acesso Azure Data Lake Storage Gen2

Você pode usar as listas de controle de acesso (ACLs) do RBAC do Azure e do estilo POSIX para controlar o acesso a dados dentro de uma rede virtual.

Para usar o RBAC do Azure, adicione a identidade gerenciada pelo espaço de trabalho à função de [leitor de dados de blob](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) . Para saber mais, confira [Controle de acesso baseado em função no Azure](../storage/blobs/data-lake-storage-access-control-model.md#role-based-access-control).

Para usar ACLs, a identidade gerenciada por espaço de trabalho pode ser atribuída ao acesso, assim como qualquer outro princípio de segurança. Para obter mais informações, consulte [listas de controle de acesso em arquivos e diretórios](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="azure-data-lake-storage-gen1-access-control"></a>Controle de acesso Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 oferece suporte apenas a listas de controle de acesso em estilo POSIX. Você pode atribuir o acesso de identidade gerenciada por espaço de trabalho a recursos, assim como qualquer outro princípio de segurança. Para obter mais informações, consulte [controle de acesso em Azure data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

### <a name="azure-sql-database-contained-user"></a>O banco de dados SQL do Azure continha o usuário

Para acessar os dados armazenados em um Azure SQL Database usando a identidade gerenciada, você deve criar um usuário contido no SQL que mapeia para a identidade gerenciada. Para obter mais informações sobre como criar um usuário de um provedor externo, consulte [criar usuários independentes mapeados para identidades do Azure ad](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).

Depois de criar um usuário do SQL continha, conceda permissões a ele usando o [comando Grant T-SQL](/sql/t-sql/statements/grant-object-permissions-transact-sql).

### <a name="azure-machine-learning-designer-intermediate-module-output"></a>Saída do módulo intermediário do Azure Machine Learning designer

Você pode especificar o local de saída para qualquer módulo no designer. Use isso para armazenar conjuntos de registros intermediários em local separado para fins de auditoria, segurança e log. Para especificar a saída:

1. Selecione o módulo cuja saída você gostaria de especificar.
1. No painel configurações do módulo que aparece à direita, selecione **configurações de saída**.
1. Especifique o repositório de armazenamento que você deseja usar para cada saída de módulo.
 
Verifique se você tem acesso às contas de armazenamento intermediários em sua rede virtual. Caso contrário, o pipeline falhará.

Você também deve [habilitar a autenticação de identidade gerenciada](#configure-datastores-to-use-workspace-managed-identity) para contas de armazenamento intermediárias para visualizar os dados de saída.

## <a name="next-steps"></a>Próximas etapas

Este artigo é a parte cinco de uma série de redes virtuais de cinco partes. Consulte o restante dos artigos para saber como proteger uma rede virtual:

* [Parte 1: visão geral da rede virtual](how-to-network-security-overview.md)
* [Parte 2: proteger os recursos do espaço de trabalho](how-to-secure-workspace-vnet.md)
* [Parte 3: proteger o ambiente de treinamento](how-to-secure-training-vnet.md)
* [Parte 4: proteger o ambiente inferência](how-to-secure-inferencing-vnet.md)