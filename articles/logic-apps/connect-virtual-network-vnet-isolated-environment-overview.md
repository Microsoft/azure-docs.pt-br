---
title: Acessar redes virtuais do Azure a partir dos Aplicativos Lógicos do Azure com ISEs (Ambientes de Serviço de Integração)
description: Esta visão geral descreve como ISEs (ambientes de serviço de integração) ajudam os aplicativos lógicos a acessarem as VNETs (redes virtuais) do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 07/19/2019
ms.openlocfilehash: 3e14604955a64c7a146a947c5c320b42ea3ebcba
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325405"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Acessar recursos de rede virtual do Azure a partir dos Aplicativos Lógicos do Azure usando ISEs (Ambientes de Serviço de Integração)

Às vezes, seus aplicativos lógicos e contas de integração precisam acessar recursos protegidos, como VMs (máquinas virtuais) e outros sistemas ou serviços, que estão dentro de uma [rede virtual do Azure](../virtual-network/virtual-networks-overview.md). Para configurar esse acesso, você pode [criar um *ambiente do serviço de integração* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) , no qual você pode executar seus aplicativos lógicos e criar suas contas de integração.

Quando você cria um ISE, o Azure *injeta* esse ISE em sua rede virtual do Azure, que implanta uma instância privada e isolada do serviço de aplicativos lógicos em sua rede virtual do Azure. Essa instância privada usa recursos dedicados, como o armazenamento, sendo executada separadamente do serviço público "global" de Aplicativos Lógicos. Separar sua instância privada isolada e a instância global pública também ajuda a reduzir o impacto que outros locatários do Azure podem ter no desempenho de seus aplicativos, que também é conhecido como [efeito "vizinhos com ruído"](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors).

Depois de criar o ISE, quando você for criar seu aplicativo lógico ou conta de integração, poderá selecionar o ISE como o seu aplicativo lógico ou o local da conta de integração:

![Selecionar o ambiente de serviço de integração](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Seu aplicativo lógico agora pode acessar diretamente os sistemas que estão dentro ou conectados à sua rede virtual usando qualquer um destes itens:

* Um conector com rótulo de **ISE**para esse sistema, como SQL Server
* Um gatilho ou ação interna de rótulo de **núcleo**, como o gatilho ou a ação de http
* Um conector personalizado

Esta visão geral descreve mais detalhes sobre como um ISE fornece aos seus aplicativos lógicos e contas de integração acesso direto à sua rede virtual do Azure e compara as diferenças entre um ISE e o serviço de aplicativos lógicos globais.

> [!IMPORTANT]
> Os aplicativos lógicos, os gatilhos internos, as ações internas e os conectores executados no ISE usam um plano de preços diferente do plano de preços baseado em consumo. Para saber como o preço e a cobrança funcionam para o ISEs, consulte o [modelo de preços dos aplicativos lógicos](../logic-apps/logic-apps-pricing.md#fixed-pricing). Para obter taxas de preços, consulte [preços dos aplicativos lógicos](../logic-apps/logic-apps-pricing.md).
>
> O ISE também aumentou os limites de duração da execução, retenção de armazenamento, taxa de transferência, tempos limite de resposta e solicitação HTTP, tamanhos de mensagem e solicitações de conector personalizado. 
> Para obter mais informações, consulte [limites e configuração para aplicativos lógicos do Azure](logic-apps-limits-and-config.md).

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Isolado versus global

Ao criar um ambiente de serviço integrado (ISE) no Azure, você pode selecionar a rede virtual do Azure na qual deseja *injetar* o ISE. Em seguida, o Azure injeta ou implanta uma instância privada do serviço de aplicativos lógicos em sua rede virtual. Essa ação cria um ambiente isolado em que é possível criar e executar os aplicativos lógicos em recursos dedicados. Ao criar seu aplicativo lógico, você seleciona o ISE como o local do seu aplicativo, o que dá ao seu aplicativo lógico acesso direto à sua rede virtual e aos recursos nessa rede.

Os aplicativos lógicos em um ISE fornecem as mesmas experiências de usuário e funcionalidades semelhantes às do serviço global dos Aplicativos Lógicos. Você não só pode usar os mesmos gatilhos internos, ações internas e conectores do serviço de aplicativos lógicos globais, mas também pode usar conectores específicos do ISE. Por exemplo, aqui estão alguns conectores padrão que oferecem versões que são executadas em um ISE:

* Armazenamento de Blobs, Armazenamento de Arquivos e Armazenamento de Tabelas do Azure
* Filas do Azure, Barramento de Serviço do Azure, Hubs de Eventos do Azure e IBM MQ
* FTP e SFTP-SSH
* SQL Server, SQL Data Warehouse, Azure Cosmos DB
* AS2, X12 e EDIFACT

A diferença entre os conectores ISE e não ISE está nos locais em que os gatilhos e as ações são executados:

* No ISE, gatilhos e ações internas, como HTTP, sempre são executados no mesmo ISE que seu aplicativo lógico e exibem o rótulo **principal** .

  ![Selecione gatilhos e ações internas de "núcleo"](./media/connect-virtual-network-vnet-isolated-environment-overview/select-core-built-in-actions-triggers.png)

* Os conectores que são executados em um ISE têm versões hospedadas publicamente disponíveis no serviço de aplicativos lógicos globais. Para conectores que oferecem duas versões, os conectores com o rótulo do **ISE** sempre são executados no mesmo ISE que seu aplicativo lógico. Os conectores sem o rótulo do **ISE** são executados no serviço global dos Aplicativos Lógicos.

  ![Selecionar conectores ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

Um ISE também fornece limites maiores para duração da execução, retenção de armazenamento, taxa de transferência, tempos limite de resposta e solicitação HTTP, tamanhos de mensagem e solicitações de conector personalizado. Para obter mais informações, consulte [limites e configuração para aplicativos lógicos do Azure](logic-apps-limits-and-config.md).

<a name="ise-level"></a>

## <a name="ise-skus"></a>SKUs do ISE

Ao criar o ISE, você pode selecionar a SKU do desenvolvedor ou a SKU Premium. Estas são as diferenças entre estas SKUs:

* **Desenvolvedores**

  Fornece um ISE de menor custo que você pode usar para experimentação, desenvolvimento e teste, mas não para teste de produção ou de desempenho. A SKU do desenvolvedor inclui gatilhos e ações internos, conectores padrão, conectores corporativos e uma única conta de integração de [camada gratuita](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) para um preço mensal fixo. No entanto, esse SKU não inclui nenhum SLA (contrato de nível de serviço), opções para escalar verticalmente a capacidade ou redundância durante a reciclagem, o que significa que você pode enfrentar atrasos ou tempo de inatividade.

* **Premium**

  Fornece um ISE que você pode usar para produção e inclui suporte a SLA, gatilhos e ações internos, conectores padrão, conectores corporativos, uma única conta de integração de [camada Standard](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) , opções para escalar verticalmente a capacidade e redundância durante reciclagem de um preço mensal fixo.

Para obter taxas de preços, consulte [preços dos aplicativos lógicos](https://azure.microsoft.com/pricing/details/logic-apps/). Para saber como o preço e a cobrança funcionam para o ISEs, consulte o [modelo de preços dos aplicativos lógicos](../logic-apps/logic-apps-pricing.md#fixed-pricing).

<a name="on-premises"></a>

## <a name="access-to-on-premises-data-sources"></a>Acesso a fontes de dados locais

Para sistemas locais conectados a uma rede virtual do Azure, insira um ISE nessa rede para que seus aplicativos lógicos possam acessar diretamente esses sistemas usando qualquer um destes itens:

* ISE-conector de versão para esse sistema, por exemplo, SQL Server
* Ação HTTP
* Conector personalizado

  * Se você tiver conectores personalizados que exigem o gateway de dados local e tiver criado esses conectores fora de um ISE, os aplicativos lógicos em um ISE também poderão usar esses conectores.
  
  * Os conectores personalizados criados em um ISE não funcionam com o gateway de dados local. No entanto, esses conectores podem acessar diretamente fontes de dados locais que estão conectadas à rede virtual que hospeda o ISE. Portanto, os aplicativos lógicos em um ISE provavelmente não precisam do gateway de dados ao se comunicar com esses recursos.

Para sistemas locais que não estão conectados a uma rede virtual ou não têm conectores ISE-Version, primeiro você deve [Configurar o gateway de dados local](../logic-apps/logic-apps-gateway-install.md) antes que seus aplicativos lógicos possam se conectar a esses sistemas.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Contas de integração com ISE

É possível usar contas de integração com aplicativos lógicos dentro de um ISE (ambiente de serviço de integração). No entanto, essas contas de integração devem usar o *mesmo ISE* que os aplicativos lógicos vinculados. Os aplicativos lógicos em um ISE podem fazer referência somente às contas de integração que estão no mesmo ISE. Ao criar uma conta de integração, é possível selecionar o ISE como local para a conta de integração. Para saber como o preço e a cobrança funcionam para contas de integração com um ISE, consulte o [modelo de preços dos aplicativos lógicos](../logic-apps/logic-apps-pricing.md#fixed-pricing). Para obter taxas de preços, consulte [preços dos aplicativos lógicos](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="next-steps"></a>Próximas etapas

* Saiba como [conectar-se a redes virtuais do Azure de aplicativos lógicos isolados](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Saiba mais sobre a [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md)
* Saiba mais sobre a [integração de rede virtual para os serviços do Azure](../virtual-network/virtual-network-for-azure-services.md)
