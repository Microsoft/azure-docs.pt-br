---
title: Como evitar configurações incorretas com a central de segurança do Azure
description: Saiba como usar as opções ' impor ' e ' negar ' da central de segurança nas páginas de detalhes de recomendações
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: a3da9cdea543894aa7aec66112e28658beac84b5
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99558184"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>Impedir configurações incorretas com as recomendações de Impor/Negar

Configurações incorretas de segurança são uma das principais causas de incidentes de segurança. Agora, a Central de Segurança pode ajudar a *impedir* configurações incorretas de novos recursos, com relação a recomendações específicas. 

Esse recurso pode ajudar a manter suas cargas de trabalho seguras e estabilizar sua classificação de segurança.

A imposição de uma configuração segura, com base em uma recomendação específica, é oferecida em dois modos:

- Usando o efeito **Negar** do Azure Policy, você pode interromper a criação de recursos não íntegros
- Usando a opção **Impor**, você pode aproveitar o efeito **DeployIfNotExist** da política do Azure e corrigir automaticamente os recursos fora de conformidade após a criação

Isso pode ser encontrado na parte superior da página de detalhes do recurso para obter as recomendações de segurança selecionadas (consulte [recomendações com as opções Deny/Force](#recommendations-with-denyenforce-options)).

## <a name="prevent-resource-creation"></a>Impedir a criação de recursos

1. Abra a recomendação que os novos recursos devem satisfazer e selecione o botão **negar** na parte superior da página.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-deny-button.png" alt-text="Página de recomendação com o botão negar realçado":::

    O painel de configuração é aberto listando as opções de escopo. 

1. Defina o escopo selecionando a assinatura ou o grupo de gerenciamento relevante.

    > [!TIP]
    > Você pode usar os três pontos no final da linha para alterar uma única assinatura ou usar as caixas de seleção para selecionar várias assinaturas ou grupos e, em seguida, selecionar **alterar para negar**.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-prevent-resource-creation.png" alt-text="Definindo o escopo para Azure Policy negar":::


## <a name="enforce-a-secure-configuration"></a>Impor uma configuração segura

1. Abra a recomendação na qual você implantará uma implantação de modelo se novos recursos não atenderem e selecione o botão **impor** na parte superior da página.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-button.png" alt-text="Página de recomendação com o botão impor realçado":::

    O painel de configuração é aberto com todas as opções de configuração de política. 

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-config.png" alt-text="Impor opções de configuração":::

1. Defina o escopo, o nome da atribuição e outras opções relevantes.

1. Selecione **Examinar + criar**.

## <a name="recommendations-with-denyenforce-options"></a>Recomendações com opções Deny/Force

Essas recomendações podem ser usadas com a opção **Deny** :

- O acesso às contas de armazenamento com configurações de firewall e de rede virtual deve ser restrito
- O Cache do Azure para Redis deve residir em uma rede virtual
- As contas do Azure Cosmos DB devem usar chaves gerenciadas pelo cliente para criptografar os dados inativos
- Os workspaces do Azure Machine Learning devem ser criptografados com uma CMK (chave gerenciada pelo cliente)
- O Azure Spring Cloud deve usar injeção de rede
- As contas dos Serviços Cognitivos devem habilitar a criptografia de dados com uma CMK (chave gerenciada pelo cliente)
- Deverão ser aplicados limites de memória e CPU ao contêiner
- As imagens de contêiner deverão ser implantadas somente se forem de registros confiáveis
- Os registros de contêiner devem ser criptografados com uma CMK (chave gerenciada pelo cliente)
- Os contêineres com elevação de privilégio deverão ser evitados
- Os contêineres que compartilham namespaces de host confidenciais deverão ser evitados
- Os contêineres deverão escutar somente em portas permitidas
- Um sistema de arquivos raiz imutável (somente leitura) deverá ser aplicado aos contêineres
- As chaves do Key Vault devem ter uma data de validade
- Os segredos do Key Vault devem ter uma data de validade
- Os cofres de chaves devem ter a proteção contra limpeza habilitada
- Os cofres de chaves devem ter a exclusão temporária habilitada
- Deverão ser aplicadas aos contêineres funcionalidades do Linux com privilégios mínimos
- Apenas conexões seguras com o Cache Redis devem ser habilitadas
- As ações para substituir ou desabilitar o perfil do AppArmor de contêineres deverão ser restritas
- Os contêineres com privilégios deverão ser evitados
- Executar contêineres como usuário raiz deverá ser evitado
- A transferência segura para contas de armazenamento deve ser habilitada
- A propriedade ClusterProtectionLevel dos clusters do Service Fabric deve ser definida como EncryptAndSign
- Os clusters do Service Fabric só devem usar o Azure Active Directory para autenticação de cliente
- Os serviços deverão escutar somente em portas permitidas
- As contas de armazenamento devem ser migradas para os novos recursos do Azure Resource Manager
- As contas de armazenamento devem restringir o acesso à rede usando regras de rede virtual
- Usar redes e portas do host deverá ser restrito
- O uso de montagens de volume do Pod HostPath deve ser restrito a uma lista conhecida para restringir o acesso ao nó de contêineres comprometidos
- O período de validade dos certificados armazenados no Azure Key Vault não deve exceder 12 meses
- As máquinas virtuais devem ser migradas para os novos recursos do Azure Resource Manager
- O WAF (Firewall do Aplicativo Web) deve ser habilitado para o Gateway de Aplicativo
- O WAF (Firewall de Aplicativo Web) deve ser habilitado para o serviço do Azure Front Door Service

Essas recomendações podem ser usadas com a opção **impor** :

- A auditoria no SQL Server deve ser habilitada
- O Backup do Azure deve ser habilitado para máquinas virtuais
- O Azure Defender para SQL deverá ser habilitado em servidores SQL
- Os logs de diagnóstico no Azure Stream Analytics devem ser habilitados
- Os logs de diagnóstico em contas do Lote devem ser habilitados
- Os logs de diagnóstico no Data Lake Analytics devem ser habilitados
- Os logs de diagnóstico no Hub de eventos devem ser habilitados
- Os logs de diagnóstico no Key Vault deve estar habilitados
- Os logs de diagnóstico nos Aplicativos Lógicos devem ser habilitados
- Os logs de diagnóstico nos serviços Pesquisa devem ser habilitados
- Os logs de diagnóstico no Barramento de Serviço devem ser habilitados