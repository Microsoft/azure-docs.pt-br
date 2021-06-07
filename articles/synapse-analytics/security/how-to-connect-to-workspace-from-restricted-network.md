---
title: Conectar-se a recursos de espaço de trabalho no Azure Synapse Analytics Studio de uma rede restrita
description: Este artigo ensinará como se conectar aos recursos do espaço de trabalho de uma rede restrita
author: xujxu
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 10/25/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: de7c5dba5a4868b7a8fdb390f974134cfaef7395
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100384513"
---
# <a name="connect-to-workspace-resources-from-a-restricted-network"></a>Conectar-se a recursos de espaço de trabalho de uma rede restrita

Suponha que você seja um administrador de ti que esteja gerenciando a rede restrita de sua organização. Você deseja habilitar a conexão de rede entre o Azure Synapse Analytics Studio e uma estação de trabalho dentro dessa rede restrita. Este artigo mostra como fazer isso.

## <a name="prerequisites"></a>Pré-requisitos

* **Assinatura do Azure**: Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* **Espaço de trabalho do Azure Synapse Analytics**: você pode criar um da análise de Synapse do Azure. Você precisa do nome do espaço de trabalho na etapa 4.
* **Uma rede restrita**: o administrador de ti mantém a rede restrita para a organização e tem permissão para configurar a política de rede. Você precisa do nome da rede virtual e sua sub-rede na etapa 3.


## <a name="step-1-add-network-outbound-security-rules-to-the-restricted-network"></a>Etapa 1: adicionar regras de segurança de saída de rede à rede restrita

Você precisará adicionar quatro regras de segurança de saída de rede com quatro marcas de serviço. 
* AzureResourceManager
* AzureFrontDoor.Frontend
* AzureActiveDirectory
* AzureMonitor (esse tipo de regra é opcional. Adicione-o somente quando desejar compartilhar os dados com a Microsoft.)

A captura de tela a seguir mostra detalhes para a regra de saída Azure Resource Manager.

![Captura de tela de Azure Resource Manager detalhes da marca de serviço.](./media/how-to-connect-to-workspace-from-restricted-network/arm-servicetag.png)

Quando você estiver criando as outras três regras, substitua o valor da **marca de serviço de destino** por **AzureFrontDoor. frontend**, **AzureActiveDirectory** ou **AzureMonitor** da lista.

Para obter mais informações, consulte [visão geral das marcas de serviço](../../virtual-network/service-tags-overview.md).

## <a name="step-2-create-private-link-hubs"></a>Etapa 2: criar hubs de link privado

Em seguida, crie hubs de link privados no portal do Azure. Para encontrá-lo no portal, pesquise por *análise de Synapse do Azure (hubs de link privado)* e preencha as informações necessárias para criá-lo. 

![Captura de tela de criar Hub de link privado Synapse.](./media/how-to-connect-to-workspace-from-restricted-network/private-links.png)

## <a name="step-3-create-a-private-endpoint-for-your-synapse-studio"></a>Etapa 3: criar um ponto de extremidade privado para seu Synapse Studio

Para acessar o Azure Synapse Analytics Studio, você deve criar um ponto de extremidade privado do portal do Azure. Para encontrá-lo no portal, procure por um *link privado*. No **centro de links privado**, selecione **criar ponto de extremidade privado** e preencha as informações necessárias para criá-lo. 

> [!Note]
> Verifique se o valor da **região** é o mesmo que o espaço de trabalho do Azure Synapse Analytics.

![Captura de tela de criar um ponto de extremidade privado, guia básico.](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-1.png)

Na guia **recurso** , escolha o Hub de link privado, que você criou na etapa 2.

![Captura de tela de criar um ponto de extremidade privado, guia de recursos.](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-2.png)

Na guia **configuração** : 
* Para **rede virtual**, selecione o nome da rede virtual restrita.
* Para **sub-rede**, selecione a sub-rede da rede virtual restrita. 
* Para **integrar com a zona DNS privada**, selecione **Sim**.

![Captura de tela de criar um ponto de extremidade privado, guia de configuração.](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-3.png)

Depois que o ponto de extremidade do link privado for criado, você poderá acessar a página de entrada da ferramenta da Web para o Azure Synapse Analytics Studio. No entanto, você ainda não pode acessar os recursos dentro do seu espaço de trabalho. Para isso, você precisa concluir a próxima etapa.

## <a name="step-4-create-private-endpoints-for-your-workspace-resource"></a>Etapa 4: criar pontos de extremidade privados para o recurso de espaço de trabalho

Para acessar os recursos dentro do recurso de espaço de trabalho do Azure Synapse Analytics Studio, você precisa criar o seguinte:

- Pelo menos um ponto de extremidade de link privado com um tipo de **subrecurso de destino** de **desenvolvimento**.
- Dois outros pontos de extremidade de link privado opcionais com tipos de **SQL** ou **OnDemand**, dependendo de quais recursos no espaço de trabalho você deseja acessar.

Criá-los é semelhante a como você cria o ponto de extremidade na etapa anterior.  

Na guia **recurso** :

* Para **tipo de recurso**, selecione **Microsoft. Synapse/Workspaces**.
* Para **recurso**, selecione o nome do espaço de trabalho que você criou anteriormente.
* Para o **subrecurso de destino**, selecione o tipo de ponto de extremidade:
  * O **SQL** é para a execução da consulta SQL no pool do SQL.
  * **OnDemand** é para a execução de consulta interna do SQL.
  * O **dev** é para acessar tudo o mais nos espaços de trabalho do Azure Synapse Analytics Studio. Você precisa criar pelo menos um ponto de extremidade de link privado deste tipo.

![Captura de tela de criar um ponto de extremidade privado, guia de recursos, espaço de trabalho.](./media/how-to-connect-to-workspace-from-restricted-network/plinks-endpoint-ws-1.png)


## <a name="step-5-create-private-endpoints-for-workspace-linked-storage"></a>Etapa 5: criar pontos de extremidade privados para o armazenamento vinculado do espaço de trabalho

Para acessar o armazenamento vinculado com o Gerenciador de armazenamento no espaço de trabalho do Azure Synapse Analytics Studio, você deve criar um ponto de extremidade privado. As etapas para isso são semelhantes às da etapa 3. 

Na guia **recurso** :
* Para **tipo de recurso**, selecione **Microsoft. Synapse/storageAccounts**.
* Para **recurso**, selecione o nome da conta de armazenamento que você criou anteriormente.
* Para o **subrecurso de destino**, selecione o tipo de ponto de extremidade:
  * o **blob** é para o armazenamento de BLOBs do Azure.
  * o **DFS** é para Azure data Lake Storage Gen2.

![Captura de tela de criar um ponto de extremidade privado, guia de recursos, armazenamento.](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-storage.png)

Agora, você pode acessar o recurso de armazenamento vinculado. Em sua rede virtual, no espaço de trabalho do Azure Synapse Analytics Studio, você pode usar o Gerenciador de armazenamento para acessar o recurso de armazenamento vinculado.

Você pode habilitar uma rede virtual gerenciada para seu espaço de trabalho, conforme mostrado nesta captura de tela:

![Captura de tela de criar espaço de trabalho Synapse, com a opção Habilitar rede virtual gerenciada realçada.](./media/how-to-connect-to-workspace-from-restricted-network/ws-network-config.png)

Se você quiser que seu bloco de anotações acesse os recursos de armazenamento vinculados em uma determinada conta de armazenamento, adicione pontos de extremidade privados gerenciados em seu Azure Synapse Analytics Studio. O nome da conta de armazenamento deve ser aquele que seu bloco de anotações precisa acessar. Para obter mais informações, consulte [criar um ponto de extremidade privado gerenciado para sua fonte de dados](./how-to-create-managed-private-endpoints.md).

Depois de criar esse ponto de extremidade, o estado de aprovação mostrará o status **pendente**. Solicite a aprovação do proprietário desta conta de armazenamento, na guia **conexões do ponto de extremidade privado** dessa conta de armazenamento na portal do Azure. Depois de aprovado, seu notebook pode acessar os recursos de armazenamento vinculados nessa conta de armazenamento.

Agora, tudo definido. Você pode acessar o recurso de espaço de trabalho do Azure Synapse Analytics Studio.

## <a name="appendix-dns-registration-for-private-endpoint"></a>Apêndice: registro de DNS para o ponto de extremidade privado

Se a "integração com a zona DNS privada" não estiver habilitada durante a criação do ponto de extremidade privado como captura de tela abaixo, você deverá criar a "**zona de DNS privado**" para cada um dos seus pontos de extremidade privados.
![Captura de tela de criar Synapse de zona DNS privada 1.](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-1.png)

Para localizar a **zona de DNS privado** no portal, pesquise *DNS privado zona*. Na **zona de DNS privado**, preencha as informações necessárias abaixo para criá-la.

* Para **nome**, insira o nome dedicado da zona DNS privada para um ponto de extremidade particular específico, como abaixo:
  * **`privatelink.azuresynapse.net`** é para o ponto de extremidade privado de acessar o gateway do Azure Synapse Analytics Studio. Consulte esse tipo de criação de ponto de extremidade privado na etapa 3.
  * **`privatelink.sql.azuresynapse.net`** é para esse tipo de ponto de extremidade privado da execução da consulta SQL no pool do SQL e no pool interno. Consulte a criação do ponto de extremidade na etapa 4.
  * **`privatelink.dev.azuresynapse.net`** é para esse tipo de ponto de extremidade privado do acesso a todo o resto nos espaços de trabalho do Azure Synapse Analytics Studio. Consulte esse tipo de criação de ponto de extremidade privado na etapa 4.
  * **`privatelink.dfs.core.windows.net`** é para o ponto de extremidade privado do acesso a Azure Data Lake Storage Gen2 vinculadas de espaço de trabalho. Consulte esse tipo de criação de ponto de extremidade privado na etapa 5.
  * **`privatelink.blob.core.windows.net`** é para o ponto de extremidade privado do acesso ao armazenamento de BLOBs vinculado do espaço de trabalho. Consulte esse tipo de criação de ponto de extremidade privado na etapa 5.

![Captura de tela de criar zona DNS privada Synapse 2.](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-2.png)

Depois que a **zona de DNS privado** criada, insira a zona DNS privada criada e selecione os links de **rede virtual** para adicionar o link à sua rede virtual. 

![Captura de tela da criação da zona Synapse privada do DNS 3.](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-3.png)

Preencha os campos obrigatórios abaixo:
* Para **nome do link**, insira o nome do link.
* Para **rede virtual**, selecione sua rede virtual.

![Captura de tela de criar Synapse de zona DNS privada 4.](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-4.png)

Depois que o link de rede virtual for adicionado, você precisará adicionar o conjunto de registros DNS na **zona de DNS privado** que você criou anteriormente.

* Para **nome**, insira as cadeias de caracteres de nome dedicado para um ponto de extremidade particular diferente: 
  * a **Web** é para o ponto de extremidade privado do acesso ao Azure Synapse Analytics Studio.
  * "***YourWorkSpaceName***" é para o ponto de extremidade privado da execução da consulta SQL no pool do SQL e também para o ponto de extremidade privado do acesso a todo o resto nos espaços de trabalho do Azure Synapse Analytics Studio.
  * "***YourWorkSpaceName *-OnDemand**" é para o ponto de extremidade privado da execução da consulta SQL no pool interno.
* Para **tipo**, selecione registro DNS tipo apenas **um** . 
* Para **endereço IP**, insira o endereço IP correspondente de cada ponto de extremidade privado. Você pode obter o endereço IP na **interface de rede** de sua visão geral do ponto de extremidade privado.

![Captura de tela de criar Synapse de zona DNS privada 5.](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-5.png)


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [rede virtual de espaço de trabalho gerenciado](./synapse-workspace-managed-vnet.md).

Saiba mais sobre [pontos de extremidade privados gerenciados](./synapse-workspace-managed-private-endpoints.md).
