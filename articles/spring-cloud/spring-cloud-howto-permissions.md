---
title: Como usar permissões no Azure Spring Cloud
description: Este artigo mostra como criar funções personalizadas para permissões no Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: 0c0b3bd81e0f73a7879382e28516378bd722bc17
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498635"
---
# <a name="how-to-use-permissions-in-azure-spring-cloud"></a>Como usar permissões no Azure Spring Cloud
Este artigo mostra como criar funções personalizadas que delegam permissões para recursos de nuvem do Azure Spring. As funções personalizadas estendem as [funções internas do Azure](../role-based-access-control/built-in-roles.md) com várias permissões de ações.

Implementaremos as seguintes funções personalizadas:

* **Função de desenvolvedor**: 
    * Implantar
    * Teste
    * Reiniciar aplicativos
    * Pode aplicar e fazer alterações nas configurações de aplicativo no repositório git
    * Pode obter o fluxo de log
* **Ops-engenharia de confiabilidade do site**: 
    * Reiniciar aplicativos
    * Obter fluxos de log
    * Não é possível fazer alterações em aplicativos ou configurações
* **Função de ações Azure pipelines/Jenkins/GitHub**:
    * Pode executar operações de criação, leitura, atualização e exclusão
    * Pode criar e configurar tudo no Azure Spring Cloud e aplicativos na instância de serviço: ações de Azure Pipelines, Jenkins ou GitHub, usando modelos Terraform ou ARM

## <a name="define-developer-role"></a>Definir função de desenvolvedor

A função de desenvolvedor inclui permissões para reiniciar aplicativos e ver seus fluxos de log, mas não pode fazer alterações em aplicativos, configuração.

### <a name="navigate-subscription-and-resource-group-access-control-iam"></a>Navegue pela assinatura e pelo IAM (controle de acesso do grupo de recursos)

Siga estas etapas para começar a definir uma função.

1. No portal do Azure, abra a assinatura e o grupo de recursos em que você deseja que a função personalizada seja atribuível.
2. **Controle de acesso aberto (iam)**.
3. Clique em **+ Adicionar**.
4. Clique em **Adicionar função personalizada**.
5. Clique em **Próximo**.

   ![Criar função personalizada](media/spring-cloud-permissions/create-custom-role.png)

6. Clique em **Adicionar permissões**.

   ![Adicionar início de permissões](media/spring-cloud-permissions/add-permissions.png)

### <a name="search-for-azure-spring-cloud-permissions"></a>Procure permissões do Azure Spring Cloud:
7. Na caixa de pesquisa, procure *Microsoft. app*.
Selecione *Microsoft Azure Spring Cloud*.

   ![Selecione Azure Spring Cloud](media/spring-cloud-permissions/spring-cloud-permissions.png)

8. Selecione as permissões para a função de desenvolvedor:

Em **Microsoft. AppPlatform/Spring**, selecione:
* Gravação: criar ou atualizar instância do serviço de nuvem do Azure Spring
* Leitura: obter instância do serviço de nuvem do Azure Spring
* Outros: listar chaves de teste da instância do serviço de nuvem do Azure Spring

Em **Microsoft. AppPlatform/Spring/apps**, selecione:
* Leitura: ler Microsoft Azure aplicativo Spring Cloud
* Outros: obter Microsoft Azure URL de carregamento de recursos de aplicativo do Spring Cloud

Em **Microsoft. AppPlatform/Spring/apps/bindings**, selecione:
* Leitura: leitura Microsoft Azure Associação de aplicativo Spring Cloud

Em **Microsoft. AppPlatform/Spring/aplicativos/implantações**, selecione:
* Gravação: gravação Microsoft Azure implantação de aplicativo Spring Cloud
* Leitura: Leia Microsoft Azure implantação de aplicativo Spring Cloud
* Outro: iniciar Microsoft Azure implantação de aplicativo do Spring Cloud
* Outro: parar Microsoft Azure implantação de aplicativo do Spring Cloud
* Outro: reiniciar Microsoft Azure implantação de aplicativo do Spring Cloud
* Outro: obter Microsoft Azure URL do arquivo de log de implantação de aplicativo do Spring Cloud

Em **Microsoft. AppPlatform/Spring/apps/Domains**, selecione:
* Leitura: ler Microsoft Azure domínio personalizado do aplicativo Spring Cloud

Em **Microsoft. AppPlatform/Spring/Certificates**, selecione:
* Leitura: Leia Microsoft Azure certificado do Spring Cloud

Em **Microsoft. AppPlatform/Locations/operationResults/Spring**, selecione:
* Leitura: resultado da operação de leitura

Em **Microsoft. AppPlatform/Locations/operationStatus/operationId**, selecione:
* Leitura: status da operação de leitura

    [![Criar permissões ](media/spring-cloud-permissions/developer-permissions-box.png) Developler](media/spring-cloud-permissions/developer-permissions-box.png#lightbox)

9. Clique em **Adicionar**.

10. Examine as permissões.

11. Clique em **Examinar e criar**.

## <a name="define-devops-engineer-role"></a>Definir função de engenheiro de DevOps
Este procedimento define uma função com permissões para implantar, testar e reiniciar aplicativos de nuvem Spring do Azure.

1. Repita o procedimento para navegar na assinatura, no grupo de recursos e no controle de acesso de acesso (IAM).
2. Selecione as permissões para a função de engenheiro de DevOps:

Em **Microsoft. AppPlatform/Spring**, selecione:
* Gravação: criar ou atualizar instância do serviço de nuvem do Azure Spring
* Excluir: excluir instância do serviço de nuvem Spring do Azure
* Leitura: obter instância do serviço de nuvem do Azure Spring
* Outro: habilitar o ponto de extremidade de teste da instância do serviço de nuvem do Azure Spring
* Outro: desabilitar o ponto de extremidade de teste da instância do serviço de nuvem do Azure Spring
* Outros: listar chaves de teste da instância do serviço de nuvem do Azure Spring
* Outro: regenerar a chave de teste da instância do serviço de nuvem Spring do Azure

Em **Microsoft. AppPlatform/Spring/apps**, selecione:
* Gravação: gravação Microsoft Azure aplicativo Spring Cloud
* Excluir: excluir Microsoft Azure aplicativo Spring Cloud
* Leitura: ler Microsoft Azure aplicativo Spring Cloud
* Outros: obter Microsoft Azure URL de carregamento de recursos de aplicativo do Spring Cloud
* Outro: validar Microsoft Azure domínio personalizado do aplicativo Spring Cloud

Em **Microsoft. AppPlatform/Spring/apps/bindings**, selecione:
* Gravação: gravação Microsoft Azure Associação de aplicativo Spring Cloud
* Excluir: excluir Microsoft Azure Associação de aplicativo do Spring Cloud
* Leitura: leitura Microsoft Azure Associação de aplicativo Spring Cloud

Em **Microsoft. AppPlatform/Spring/aplicativos/implantações**, selecione:
* Gravação: gravação Microsoft Azure implantação de aplicativo Spring Cloud
* Excluir: excluir implantação de aplicativo do Azure Spring Cloud
* Leitura: Leia Microsoft Azure implantação de aplicativo Spring Cloud
* Outro: iniciar Microsoft Azure implantação de aplicativo do Spring Cloud
* Outro: parar Microsoft Azure implantação de aplicativo do Spring Cloud
* Outro: reiniciar Microsoft Azure implantação de aplicativo do Spring Cloud
* Outro: obter Microsoft Azure URL do arquivo de log de implantação de aplicativo do Spring Cloud

Em **Microsoft. AppPlatform/Spring/apps/Implantations/SKUs**, selecione:
* Leia: listar SKUs disponíveis de implantação de aplicativo

Em **Microsoft. AppPlatform/Locations**, selecione:
* Outro: verificar a disponibilidade do nome

De Microsoft. AppPlatform/Locations/operationResults/Spring Select: leitura: resultado da operação de leitura

Em **Microsoft. AppPlatform/Locations/operationStatus/operationId**, selecione:
* Leitura: status da operação de leitura

Em **Microsoft. AppPlatform/SKUs**, selecione:
* Leitura: listar SKUs disponíveis

   [![Permissões ](media/spring-cloud-permissions/dev-ops-permissions.png) de dev/op](media/spring-cloud-permissions/dev-ops-permissions.png#lightbox)

3. Clique em **Adicionar**.

4. Examine as permissões.

5. Clique em **Examinar e criar**.

## <a name="define-ops---site-reliability-engineering-role"></a>Definir o Ops-função de engenharia de confiabilidade do site
Este procedimento define uma função com permissões para implantar, testar e reiniciar aplicativos de nuvem Spring do Azure.

1. Repita o procedimento para navegar na assinatura, no grupo de recursos e no controle de acesso de acesso (IAM).

2. Selecione as permissões para a função de engenharia de confiabilidade do site do Ops:

Em **Microsoft. AppPlatform/Spring**, selecione:
* Leitura: obter instância do serviço de nuvem do Azure Spring
* Outros: listar chaves de teste da instância do serviço de nuvem do Azure Spring

Em **Microsoft. AppPlatform/Spring/apps**, selecione:
* Leitura: ler Microsoft Azure aplicativo Spring Cloud

Em **Microsoft. AppPlatform/aplicativos/implantações**, selecione:
* Leitura: Leia Microsoft Azure implantação de aplicativo Spring Cloud
* Outro: iniciar Microsoft Azure implantação de aplicativo do Spring Cloud
* Outro: parar Microsoft Azure implantação de aplicativo do Spring Cloud
* Outro: reiniciar Microsoft Azure implantação de aplicativo do Spring Cloud

Em **Microsoft. AppPlatform/Locations/operationResults/Spring**, selecione:
* Leitura: resultado da operação de leitura

Em **Microsoft. AppPlatform/Locations/operationStatus/operationId**, selecione:
* Leitura: status da operação de leitura

   [![Permissões ](media/spring-cloud-permissions/ops-sre-permissions.png) Ops/SRE](media/spring-cloud-permissions/ops-sre-permissions.png#lightbox)

3. Clique em **Adicionar**.

4. Examine as permissões.

5. Clique em **Examinar e criar**.

## <a name="define-azure-pipelinesprovisioning-role"></a>Definir Azure Pipelines/função de provisionamento
Essa função de ações do Jenkins/GitHub pode criar e configurar tudo no Azure Spring Cloud e aplicativos com uma instância de serviço. Essa função destina-se à liberação ou implantação de código.

1. Repita o procedimento para navegar na assinatura, no grupo de recursos e no controle de acesso de acesso (IAM).

2. Abra as opções de **permissões** .

3. Selecione as permissões para a função de Azure Pipelines/provisionamento:
  
Em **Microsoft. AppPlatform/Spring**, selecione:
* Gravação: criar ou atualizar instância do serviço de nuvem do Azure Spring
* Excluir: excluir instância do serviço de nuvem Spring do Azure
* Leitura: obter instância do serviço de nuvem do Azure Spring
* Outro: habilitar o ponto de extremidade de teste da instância do serviço de nuvem do Azure Spring
* Outro: desabilitar o ponto de extremidade de teste da instância do serviço de nuvem do Azure Spring
* Outros: listar chaves de teste da instância do serviço de nuvem do Azure Spring
* Outro: regenerar a chave de teste da instância do serviço de nuvem Spring do Azure

Em **Microsoft. AppPlatform/Spring/apps**, selecione:
* Gravação: gravação Microsoft Azure aplicativo Spring Cloud
* Excluir: excluir Microsoft Azure aplicativo Spring Cloud
* Leitura: ler Microsoft Azure aplicativo Spring Cloud
* Outros: obter Microsoft Azure URL de carregamento de recursos de aplicativo do Spring Cloud
* Outro: validar Microsoft Azure domínio personalizado do aplicativo Spring Cloud

Em **Microsoft. AppPlatform/Spring/apps/bindings**, selecione:
* Gravação: gravação Microsoft Azure Associação de aplicativo Spring Cloud
* Excluir: excluir Microsoft Azure Associação de aplicativo do Spring Cloud
* Leitura: leitura Microsoft Azure Associação de aplicativo Spring Cloud

Em **Microsoft. AppPlatform/Spring/aplicativos/implantações**, selecione:
* Gravação: gravação Microsoft Azure implantação de aplicativo Spring Cloud
* Excluir: excluir implantação de aplicativo do Azure Spring Cloud
* Leitura: Leia Microsoft Azure implantação de aplicativo Spring Cloud
* Outro: iniciar Microsoft Azure implantação de aplicativo do Spring Cloud
* Outro: parar Microsoft Azure implantação de aplicativo do Spring Cloud
* Outro: reiniciar Microsoft Azure implantação de aplicativo do Spring Cloud
* Outro: obter Microsoft Azure URL do arquivo de log de implantação de aplicativo do Spring Cloud

Em **Microsoft. AppPlatform/SKUs**, selecione:
* Leitura: listar SKUs disponíveis

Em **Microsoft. AppPlatform/Locations**, selecione:
* Outro: verificar a disponibilidade do nome

Em **Microsoft. AppPlatform/Locations/operationResults/Spring**, selecione:
* Leitura: resultado da operação de leitura

Em **Microsoft. AppPlatform/Locations/operationStatus/operationId**, selecione:
* Leitura: status da operação de leitura

Em **Microsoft. AppPlatform/SKUs**, selecione:
* Leitura: listar SKUs disponíveis

   [![Permissões ](media/spring-cloud-permissions/pipelines-permissions-box.png) de pipelines](media/spring-cloud-permissions/pipelines-permissions-box.png#lightbox)  

4. Clique em **Adicionar**.

5. Examine as permissões.

6. Clique em **Examinar e criar**.


## <a name="see-also"></a>Confira também
* [Criar ou atualizar as funções personalizadas do Azure usando o portal do Azure](../role-based-access-control/custom-roles-portal.md)

Para obter mais informações sobre três métodos que definem permissões personalizadas, consulte:
* [Clonar uma função](../role-based-access-control/custom-roles-portal.md#clone-a-role)
* [Começar do zero](../role-based-access-control/custom-roles-portal.md#start-from-scratch)
* [Iniciar do JSON](../role-based-access-control/custom-roles-portal.md#start-from-json)