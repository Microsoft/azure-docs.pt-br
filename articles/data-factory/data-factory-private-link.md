---
title: Link Privado do Azure para Azure Data Factory
description: Saiba mais sobre como funciona o link privado do Azure no Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/01/2020
ms.openlocfilehash: a1b5ba56d30124bea7a814c2ffcf0cfff28903aa
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99062181"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Link Privado do Azure para Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Usando o link privado do Azure, você pode se conectar a várias implantações de PaaS (plataforma como serviço) no Azure por meio de um ponto de extremidade privado. Um ponto de extremidade privado é um endereço IP privado em uma rede virtual e sub-rede específica. Para obter uma lista de implantações de PaaS que dão suporte à funcionalidade de link privado, consulte a [documentação do link privado](../private-link/index.yml). 

## <a name="secure-communication-between-customer-networks-and-azure-data-factory"></a>Comunicação segura entre redes do cliente e Azure Data Factory 
Você pode configurar uma rede virtual do Azure como uma representação lógica da sua rede na nuvem. Isso oferece os seguintes benefícios:
* Você ajuda a proteger seus recursos do Azure contra ataques em redes públicas.
* Você permite que as redes e Data Factory se comuniquem com segurança entre si. 

Você também pode conectar uma rede local à sua rede virtual Configurando uma conexão VPN (IPsec de protocolo de Internet) (site a site), ou uma conexão do Azure ExpressRoute (emparelhamento privado). 

Você também pode instalar um tempo de execução de integração auto-hospedado em um computador local ou em uma máquina virtual na rede virtual. Isso permite que você:
* Execute atividades de cópia entre um armazenamento de dados de nuvem e um armazenamento de dados em uma rede privada.
* Despache as atividades de transformação em relação aos recursos de computação em uma rede local ou em uma rede virtual do Azure. 

Vários canais de comunicação são necessários entre Azure Data Factory e a rede virtual do cliente, conforme mostrado na tabela a seguir:

| Domínio | Porta | Descrição |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | Um plano de controle, exigido pelo Data Factory criação e monitoramento. |
| `*.{region}.datafactory.azure.net` | 443 | Necessárias para que o runtime de integração auto-hospedada se conecte ao serviço do Data Factory. |
| `*.servicebus.windows.net` | 443 | Exigido pelo runtime de integração auto-hospedada para criação interativa. |
| `download.microsoft.com` | 443 | Exigido pelo runtime de integração auto-hospedada para fazer o download das atualizações. |

Com o suporte ao link privado para Azure Data Factory, você pode:
* Crie um ponto de extremidade privado em sua rede virtual.
* Habilite a conexão privada para uma instância específica de data factory. 

As comunicações para Azure Data Factory serviço passam pelo link privado e ajudam a fornecer conectividade privada segura. 

![Diagrama de link privado para a arquitetura de Azure Data Factory.](./media/data-factory-private-link/private-link-architecture.png)

Habilitar o serviço de vínculo privado para cada um dos canais de comunicação anteriores oferece a seguinte funcionalidade:
- **Com suporte**:
   - Você pode criar e monitorar o data factory em sua rede virtual, mesmo se você bloquear todas as comunicações de saída.
   - As comunicações de comando entre o tempo de execução de integração auto-hospedado e o serviço de Azure Data Factory podem ser executadas com segurança em um ambiente de rede privada. O tráfego entre o tempo de execução de integração auto-hospedado e o serviço de Azure Data Factory passa por um link privado. 
- **Não tem suporte no momento**:
   - A criação interativa que usa um tempo de execução de integração auto-hospedado, como conexão de teste, lista de pastas de pesquisa e lista de tabelas, obter esquema e Visualizar dados, passa por um link privado.
   - A nova versão do tempo de execução de integração auto-hospedado pode ser baixada automaticamente do centro de download da Microsoft se você habilitar a atualização automática.

   > [!NOTE]
   > Para a funcionalidade que não tem suporte no momento, você ainda precisa configurar o domínio e a porta mencionados anteriormente na rede virtual ou no firewall corporativo. 

   > [!NOTE]
   > Conectar-se a Azure Data Factory por meio do ponto de extremidade privado é aplicável somente ao tempo de execução de integração auto-hospedado no data factory. Não há suporte no Synapse.

> [!WARNING]
> Ao criar um serviço vinculado, verifique se suas credenciais estão armazenadas em um cofre de chaves do Azure. Caso contrário, as credenciais não funcionarão quando você habilitar o link privado no Azure Data Factory.

## <a name="dns-changes-for-private-endpoints"></a>Alterações de DNS para pontos de extremidade particulares
Quando você cria um ponto de extremidade privado, o registro de recurso DNS CNAME para o Data Factory é atualizado para um alias em um subdomínio com o prefixo ' privatelink '. Por padrão, também criamos uma [zona DNS privada](../dns/private-dns-overview.md), correspondente ao subdomínio ' privatelink ', com os registros de recurso de DNS a para os pontos de extremidade privados.

Quando você resolve a URL do ponto de extremidade data factory de fora da VNet com o ponto de extremidade privado, ele é resolvido para o ponto de extremidade público do serviço de data factory. Quando resolvido da VNet que hospeda o ponto de extremidade privado, a URL do ponto de extremidade de armazenamento é resolvida para o endereço IP do ponto de extremidade privado.

Para o exemplo ilustrado acima, os registros de recurso de DNS para o Data Factory ' datafactorya ', quando resolvidos de fora da VNet que hospeda o ponto de extremidade privado, serão:

| Nome | Tipo | Valor |
| ---------- | -------- | --------------- |
| Datafactorya. {região}. datafactory. Azure. net | CNAME   | Datafactorya. {região}. privatelink. datafactory. Azure. net |
| Datafactorya. {região}. privatelink. datafactory. Azure. net | CNAME   | Ponto de extremidade público do serviço de data factory < > |
| Ponto de extremidade público do serviço de data factory < >  | Um | < endereço IP público do serviço data factory > |

Os registros de recurso DNS para datafactorya, quando resolvidos na VNet que hospeda o ponto de extremidade privado, serão:

| Nome | Tipo | Valor |
| ---------- | -------- | --------------- |
| Datafactorya. {região}. datafactory. Azure. net | CNAME   | Datafactorya. {região}. privatelink. datafactory. Azure. net |
| Datafactorya. {região}. privatelink. datafactory. Azure. net   | Um | < endereço IP do ponto de extremidade privado > |

Se você estiver usando um servidor DNS personalizado em sua rede, os clientes deverão ser capazes de resolver o FQDN do ponto de extremidade de Data Factory para o endereço IP do ponto de extremidade privado. Você deve configurar o servidor DNS para delegar seu subdomínio de vínculo privado à zona DNS privada para a VNet ou configurar os registros a para ' datafactorya. {região}. privatelink. datafactory. Azure. net ' com o endereço IP do ponto de extremidade privado.

Para obter mais informações sobre como configurar seu próprio servidor DNS para dar suporte a pontos de extremidade privados, consulte os seguintes artigos:
- [Resolução de nomes para recursos em redes virtuais do Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [Configuração de DNS para pontos de extremidade privados](../private-link/private-endpoint-overview.md#dns-configuration)


## <a name="set-up-private-link-for-azure-data-factory"></a>Configurar link privado para Azure Data Factory
Você pode criar pontos de extremidade privados usando [o portal do Azure](../private-link/create-private-endpoint-portal.md).

Você pode escolher se deseja conectar seu tempo de execução de integração auto-hospedado para Azure Data Factory por meio de ponto de extremidade público ou ponto de extremidade privado. 

![Captura de tela do bloqueio de acesso público de Integration Runtime auto-hospedados.](./media/data-factory-private-link/disable-public-access-shir.png)


Você também pode ir para sua data factory do Azure no portal do Azure e criar um ponto de extremidade privado, como mostrado aqui:

![Captura de tela do painel "conexões de ponto de extremidade privado" para criar um ponto de extremidade privado.](./media/data-factory-private-link/create-private-endpoint.png)

Na etapa de **recurso**, selecione **Microsoft. datafactory/fábricas** como **tipo de recurso**. E se você quiser criar um ponto de extremidade privado para comunicações de comando entre o tempo de execução de integração auto-hospedado e o serviço de Azure Data Factory, selecione **DataFactory** como um **subrecurso de destino**.

![Captura de tela do painel "conexões de ponto de extremidade privado" para selecionar o recurso.](./media/data-factory-private-link/private-endpoint-resource.png)

> [!NOTE]
> A desabilitação do acesso à rede pública é aplicável somente ao tempo de execução de integração auto-hospedado, não a Azure Integration Runtime e SQL Server Integration Services (SSIS) Integration Runtime.

Se você quiser criar um ponto de extremidade privado para criação e monitoramento do data factory em sua rede virtual, selecione **portal** como **subrecurso de destino**.

> [!NOTE]
> Você ainda pode acessar o portal de Azure Data Factory por meio de uma rede pública depois de criar um ponto de extremidade privado para o Portal.

## <a name="next-steps"></a>Próximas etapas

- [Criar um data factory usando a interface do usuário do Azure Data Factory](quickstart-create-data-factory-portal.md)
- [Introdução ao Azure Data Factory](introduction.md)
- [Criação visual no Azure Data Factory](author-visually.md)