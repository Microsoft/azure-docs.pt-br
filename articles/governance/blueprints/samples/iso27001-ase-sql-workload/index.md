---
title: Visão Geral do exemplo de blueprint de carga de trabalho do ASE/SQL ISO 27001
description: Visão geral e arquitetura do exemplo de blueprint da carga de trabalho do Ambiente do Serviço de Aplicativo/Banco de Dados SQL ISO 27001.
ms.date: 02/05/2021
ms.topic: sample
ms.openlocfilehash: 61e62c9a9099b0c84a98f1840743f05469e46b62
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627509"
---
# <a name="overview-of-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Visão geral do exemplo de blueprint de carga de trabalho do Ambiente do Serviço de Aplicativo/Banco de Dados SQL do ISO 27001

O exemplo de blueprint da carga de trabalho do Ambiente do Serviço de Aplicativo/Banco de Dados SQL ISO 27001 oferece mais infraestrutura para o exemplo de blueprint dos [Serviços Compartilhados ISO 27001](../iso27001-shared/index.md).
Este blueprint ajuda os clientes a implantar arquiteturas baseadas em nuvem que oferecem soluções para os cenários com requisitos de conformidade ou de credenciamento.

Há dois exemplos de blueprint ISO 27001, este exemplo e o exemplo de blueprint dos [Serviços Compartilhados ISO 27001](../iso27001-shared/index.md).

> [!IMPORTANT]
> Este exemplo depende da infraestrutura implantada pelo exemplo de blueprint dos [Serviços Compartilhados ISO 27001](../iso27001-shared/index.md). Ele precisa ser implantado primeiro.

## <a name="architecture"></a>Arquitetura

O exemplo de blueprint do Ambiente do Serviço de Aplicativo/Banco de Dados SQL ISO 27001 implanta um ambiente Web baseado em plataforma como serviço. O ambiente pode ser usado para hospedar vários aplicativos Web, APIs Web e instâncias do Banco de Dados SQL que seguem os padrões ISO 27001. Este exemplo de blueprint depende do exemplo de blueprint dos [Serviços Compartilhados ISO 27001](../iso27001-shared/index.md).

:::image type="content" source="../../media/sample-iso27001-ase-sql-workload/iso27001-ase-sql-workload-blueprint-sample-design.png" alt-text="Design de exemplo de blueprint de carga de trabalho do ASE/SQL ISO 27001" border="false":::

Este ambiente é composto de vários serviços do Azure usados para oferecer uma infraestrutura de carga de trabalho segura, totalmente monitorada e pronta para empresas, baseada nas normas ISO 27001. Esse ambiente é composto de:

- [Função do Azure](../../../../role-based-access-control/overview.md) chamada DevOps que tem o direito de implantar e gerenciar recursos em um [Ambiente do Serviço de Aplicativo do Azure](../../../../app-service/environment/intro.md) implantado pelo exemplo de blueprint
- Definições do [Azure Policy](../../../policy/overview.md) para bloquear os serviços que podem ser implantados no ambiente e negar a criação de recursos de PIP (endereço IP público)
- Uma rede virtual que contém uma sub-rede e emparelha com um ambiente de [serviços compartilhados](../iso27001-shared/index.md) já existente e força a passagem de todo o tráfego pelo firewall dos [serviços compartilhados](../iso27001-shared/index.md). A rede virtual hospeda os recursos a seguir:
  - Um [Ambiente do Serviço de Aplicativo do Azure](../../../../app-service/environment/intro.md) que pode ser usado para hospedar um ou mais aplicativos Web, APIs Web ou funções
  - Uma instância do [Azure Key Vault](../../../../key-vault/general/overview.md) que usa um ponto de extremidade de serviço de VNet para armazenar segredos usados pelos aplicativos em execução no ambiente de carga de trabalho
  - Uma instância de servidor do [Banco de Dados SQL do Azure](../../../../azure-sql/database/sql-database-paas-overview.md) que usa um ponto de extremidade de serviço de VNet para hospedar bancos de dados usados para aplicativos no ambiente de carga de trabalho

## <a name="next-steps"></a>Próximas etapas

Você revisou a visão geral e a arquitetura do exemplo de blueprint da carga de trabalho do Ambiente do Serviço de Aplicativo/Banco de Dados SQL ISO 27001. Em seguida, visite os seguintes artigos para saber mais sobre o mapeamento de controle e como implantar este exemplo:

> [!div class="nextstepaction"]
> [Blueprint da carga de trabalho do Ambiente do Serviço de Aplicativo/Banco de Dados SQL ISO 27001 – Mapeamento de controle](./control-mapping.md)
> [Etapas do blueprint da carga de trabalho do Ambiente do Serviço de Aplicativo/Banco de Dados SQL ISO 27001](./deploy.md)

Outros artigos sobre blueprints e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../../how-to/update-existing-assignments.md).
