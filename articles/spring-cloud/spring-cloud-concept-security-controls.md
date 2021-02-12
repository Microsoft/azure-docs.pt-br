---
title: Conceito-controles de segurança para o serviço de nuvem do Azure Spring
description: Use controles de segurança internos no serviço de nuvem Spring do Azure.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 25636611795a18761a1fc7135efd4abba5af5fe3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501100"
---
# <a name="security-controls-for-azure-spring-cloud-service"></a>Controles de segurança para o serviço do Azure Spring Cloud

**Este artigo aplica-se a:** ✔️ Java ✔️ C#

Os controles de segurança são internos no serviço de nuvem Spring do Azure.

Um controle de segurança é uma qualidade ou um recurso de um serviço do Azure que contribui para a capacidade do serviço de prevenir, detectar e responder a vulnerabilidades de segurança.  Para cada controle, usamos *Sim* ou *não* para indicar se ele está em vigor no momento para o serviço.  Usamos *N/A* para um controle que não é aplicável ao serviço. 

**Controles de segurança de proteção de dados**

| Controle de segurança | Sim/Não | Observações | Documentação |
|:-------------|:-------|:-------------------------------|:----------------------|
| Criptografia no lado do servidor em repouso: chaves gerenciadas pela Microsoft | Sim | O usuário carregou a origem e os artefatos, as configurações do servidor de configuração, as configurações do aplicativo e os dados no armazenamento persistente são armazenados no armazenamento do Azure, o que criptografa automaticamente o conteúdo em repouso.<br><br>Cache do servidor de configuração, binários de tempo de execução criados a partir da origem carregada e os logs de aplicativo durante o tempo de vida do aplicativo são salvos no disco gerenciado do Azure, que criptografa automaticamente o conteúdo em repouso.<br><br>As imagens de contêiner criadas a partir da fonte carregada pelo usuário são salvas no registro de contêiner do Azure, que criptografa automaticamente o conteúdo da imagem em repouso. | [Criptografia do Armazenamento do Azure para dados em repouso](../storage/common/storage-service-encryption.md)<br><br>[Criptografia do lado do servidor dos discos gerenciados do Azure](../virtual-machines/disk-encryption.md)<br><br>[Armazenamento de imagens de contêiner no Registro de Contêiner do Azure](../container-registry/container-registry-storage.md) |
| Criptografia em transitório | Sim | Os pontos de extremidade públicos do aplicativo do usuário usam HTTPS para o tráfego de entrada por padrão. |  |
| Chamadas criptografadas à API | Sim | As chamadas de gerenciamento para configurar o serviço de nuvem Spring do Azure ocorrem por meio de chamadas Azure Resource Manager por HTTPS. | [Azure Resource Manager](../azure-resource-manager/index.yml) |

**Controles de segurança de acesso à rede**

| Controle de segurança | Sim/Não | Observações | Documentação |
|:-------------|:-------|:-------------------------------|:----------------------|
| Marca de serviço | Sim | Use a marca de serviço **AzureSpringCloud** para definir os controles de acesso à rede de saída em [grupos de segurança de rede](../virtual-network/network-security-groups-overview.md#security-rules) ou no [Firewall do Azure](../firewall/service-tags.md), para permitir o tráfego para aplicativos do Azure Spring Cloud.<br><br>*Observação:* Atualmente, somente a nova instância do serviço de nuvem do Azure Spring criada depois de 2020/07/14 dá suporte à marca de serviço **AzureSpringCloud** . | [Marcas de serviço](../virtual-network/service-tags-overview.md) |

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: Implantar seu primeiro aplicativo do Azure Spring Cloud](spring-cloud-quickstart.md)