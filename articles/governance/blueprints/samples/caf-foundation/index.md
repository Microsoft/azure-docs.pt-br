---
title: Visão Geral do exemplo de blueprint da Base do CAF
description: Visão geral e arquitetura do exemplo de blueprint do CAF (Cloud Adoption Framework) para o Azure Foundation.
ms.date: 09/14/2020
ms.topic: sample
ms.openlocfilehash: 77e8b79ec7cf217161099808cee4364e31c6d6dd
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91950271"
---
# <a name="overview-of-the-microsoft-cloud-adoption-framework-for-azure-foundation-blueprint-sample"></a>Visão geral do exemplo de blueprint do Cloud Adoption Framework da Microsoft para o Azure Foundation

O blueprint do CAF (Cloud Adoption Framework da Microsoft para o Azure) Foundation implanta um conjunto de recursos de infraestrutura e controles de política essenciais necessários para seu primeiro aplicativo do Azure de nível de produção. Esse blueprint de base é baseado no padrão recomendado encontrado no CAF.

## <a name="architecture"></a>Arquitetura

O exemplo de blueprint da Base do CAF implanta recursos de infraestrutura recomendados no Azure que podem ser usados pelas organizações para implementar os controles de base necessários para gerenciar a propriedade de nuvem delas. Este exemplo implantará e aplicará recursos, políticas e modelos que permitirão às organizações começar a usar o Azure com confiança.

:::image type="complex" source="../../media/caf-blueprints/caf-foundation-architecture.png" alt-text="Base do C A F, a imagem descreve o que é instalado como parte das diretrizes do C A F para criar uma base sobre a qual começar a usar o Azure." border="false":::
   Descreve uma arquitetura do Azure obtida implantando o blueprint da Base do C A F.  Ela é aplicável a uma assinatura com grupos de recursos que são compostos por uma conta de armazenamento para armazenar logs e o Log Analytics configurado para ser armazenado na conta de armazenamento. Ela também ilustra o Azure Key Vault definido com uma configuração padrão da Central de Segurança do Azure. Todas essas infraestruturas principais são acessadas usando o Azure Active Directory e impostas usando o Azure Policy.     
:::image-end:::

Essa implementação incorpora vários serviços do Azure usados para oferecer uma base segura, totalmente monitorada e pronta para empresas. Esse ambiente é composto de:

- Uma instância do [Azure Key Vault](../../../../key-vault/general/overview.md) usada para hospedar segredos usados para as VMs implantadas no ambiente de serviços compartilhados
- Implantar o [Log Analytics](../../../../azure-monitor/overview.md) garante que todas as ações e os serviços serão registrados em um local central desde o momento em que você iniciar sua implantação segura nas [Contas de Armazenamento](../../../../storage/common/storage-introduction.md) para log de diagnósticos
- Implantar a [Central de Segurança do Azure](../../../../security-center/security-center-introduction.md) (versão padrão) fornece proteção contra ameaças para suas cargas de trabalho migradas
- O blueprint também define e implanta as definições do [Azure Policy](../../../policy/overview.md):
  - Definições de política:
    - Marcação (CostCenter) aplicada a grupos de recursos
    - Acrescentar recursos no grupo de recursos com a marca CostCenter
    - Região do Azure permitida para recursos e grupos de recursos
    - SKUs de conta de armazenamento permitidas (escolha durante a implantação)
    - SKUs de VM do Azure permitidas (escolha durante a implantação)
    - Exigir a implantação do Observador de Rede 
    - Exigir criptografia de transferência segura na Conta de Armazenamento do Microsoft Azure
    - Negar tipos de recursos (escolher durante a implantação)  
  - Iniciativas da política:
    - Habilitar o monitoramento na Central de Segurança do Azure (mais de 100 definições de política)

Todos esses elementos cumprem as práticas comprovadas publicadas no [Centro de Arquitetura do Azure – Arquiteturas de Referência](/azure/architecture/reference-architectures/).

> [!NOTE]
> A Base do CAF apresenta uma arquitetura de base para as cargas de trabalho.
> Ainda é necessário implantar cargas de trabalho por trás dessa arquitetura de base.

Para obter mais informações, confira o [Cloud Adoption Framework da Microsoft para o Azure – Pronta](/azure/cloud-adoption-framework/ready/).

## <a name="next-steps"></a>Próximas etapas

Você revisou a visão geral e a arquitetura do exemplo de blueprint da Base do CAF.

> [!div class="nextstepaction"]
> [Blueprint da Base do CAF – Etapas de implantação](./deploy.md)

Outros artigos sobre blueprints e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../../how-to/update-existing-assignments.md).