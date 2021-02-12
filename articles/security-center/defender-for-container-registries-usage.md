---
title: Como usar o Azure defender para registros de contêiner
description: Saiba como usar o Azure defender para registros de contêiner para verificar as imagens do Linux em seus registros hospedados no Linux
author: memildin
ms.author: memildin
ms.date: 10/21/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: a5d66e43485ec66b6297ef11ed382e8fb82b7cb3
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "96014560"
---
# <a name="use-azure-defender-for-container-registries-to-scan-your-images-for-vulnerabilities"></a>Usar o Azure Defender para registros de contêiner para verificar se há vulnerabilidades em suas imagens

Esta página explica como usar o verificador de vulnerabilidades interno para verificar as imagens de contêiner armazenadas em seu registro de contêiner do Azure baseado em Azure Resource Manager.

Quando o **Azure Defender para registros de contêiner** estiver habilitado, as imagens enviadas por push para o registro serão verificadas imediatamente. Além disso, qualquer imagem extraída nos últimos 30 dias também é verificada. 

Quando o verificador relata vulnerabilidades à central de segurança, a central de segurança apresenta as descobertas e as informações relacionadas como recomendações. Além disso, as descobertas incluem informações relacionadas, como etapas de correção, CVEs relevantes, pontuações de CVSS e muito mais. Você pode exibir as vulnerabilidades identificadas para uma ou mais assinaturas ou para um registro específico.

[!INCLUDE [Defender for container registries availability info](../../includes/security-center-availability-defender-for-container-registries.md)]


## <a name="identify-vulnerabilities-in-images-in-azure-container-registries"></a>Identificar as vulnerabilidades em imagens em registros de contêiner do Azure 

Para habilitar verificações de vulnerabilidade de imagens armazenadas em seu registro de contêiner do Azure baseado em Azure Resource Manager:

1. Habilite o **Azure defender para registros de contêiner** para sua assinatura. A central de segurança agora está pronta para verificar as imagens em seus registros.

    >[!NOTE]
    > Esse recurso é cobrado por imagem.

1. As verificações de imagem são disparadas em cada Push ou importação e, se a imagem tiver sido retirada nos últimos 30 dias. 

    Quando a verificação é concluída (normalmente após aproximadamente 2 minutos, mas pode ter até 15 minutos), as descobertas estão disponíveis como recomendações da central de segurança.

1. [Exiba e corrija as descobertas conforme explicado abaixo](#view-and-remediate-findings).

## <a name="identify-vulnerabilities-in-images-in-other-container-registries"></a>Identificar vulnerabilidades em imagens em outros registros de contêiner 

1. Use as ferramentas ACR para trazer imagens para o registro do Hub do Docker ou do registro de contêiner da Microsoft.  Quando a importação for concluída, as imagens importadas serão verificadas pelo Azure defender. 

    Saiba mais em [importar imagens de contêiner para um registro de contêiner](../container-registry/container-registry-import-images.md)

    Quando a verificação é concluída (normalmente após aproximadamente 2 minutos, mas pode ter até 15 minutos), as descobertas estão disponíveis como recomendações da central de segurança.

1. [Exiba e corrija as descobertas conforme explicado abaixo](#view-and-remediate-findings).


## <a name="view-and-remediate-findings"></a>Exibir e corrigir as descobertas

1. Para exibir as descobertas, vá para a página **recomendações** . Se forem encontrados problemas, você verá as **vulnerabilidades de recomendação nas imagens do registro de contêiner do Azure que devem ser corrigidas**

    ![Recomendação para corrigir problemas ](media/monitor-container-security/acr-finding.png)

1. Selecione a recomendação. 

    A página detalhes da recomendação é aberta com informações adicionais. Essas informações incluem a lista de registros com imagens vulneráveis ("recursos afetados") e as etapas de correção. 

1. Selecione um registro específico para ver os repositórios dentro dele que têm repositórios vulneráveis.

    ![Selecionar um registro](media/monitor-container-security/acr-finding-select-registry.png)

    A página detalhes do registro é aberta com a lista de repositórios afetados.

1. Selecione um repositório específico para ver os repositórios dentro dele que têm imagens vulneráveis.

    ![Selecionar um repositório](media/monitor-container-security/acr-finding-select-repository.png)

    A página detalhes do repositório é aberta. Ele lista as imagens vulneráveis junto com uma avaliação da gravidade das descobertas.

1. Selecione uma imagem específica para ver as vulnerabilidades.

    ![Selecionar imagens](media/monitor-container-security/acr-finding-select-image.png)

    A lista de conclusões para a imagem selecionada é aberta.

    ![Lista de conclusões](media/monitor-container-security/acr-findings.png)

1. Para saber mais sobre uma localização, selecione a localização. 

    O painel detalhes de conclusões é aberto.

    [![Painel de detalhes de conclusões](media/monitor-container-security/acr-finding-details-pane.png)](media/monitor-container-security/acr-finding-details-pane.png#lightbox)

    Esse painel inclui uma descrição detalhada do problema e links para recursos externos para ajudar a mitigar as ameaças.

1. Siga as etapas na seção correção deste painel.

1. Quando você executou as etapas necessárias para corrigir o problema de segurança, substitua a imagem no registro:

    1. Envie por push a imagem atualizada. Isso irá disparar uma verificação. 
    
    1. Marque a página recomendações para a recomendação "vulnerabilidades nas imagens do registro de contêiner do Azure devem ser corrigidas". 
    
        Se a recomendação ainda aparecer e a imagem que você tratou ainda aparecer na lista de imagens vulneráveis, verifique as etapas de correção novamente.

    1. Quando tiver certeza de que a imagem atualizada foi enviada por push, verificada e não está mais aparecendo na recomendação, exclua a imagem "antiga" vulnerável do registro.


## <a name="disable-specific-findings-preview"></a>Desabilitar descobertas específicas (versão prévia)

> [!NOTE]
> [!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]

Caso você tenha uma necessidade organizacional para ignorar uma descoberta, em vez de corrigi-la, você pode opcionalmente desabilitá-la. As descobertas desabilitadas não afetam sua classificação de segurança nem geram um ruído indesejado.

Quando uma descoberta corresponde aos critérios definidos nas regras de desabilitação, ela não será exibida na lista de descobertas. Os cenários típicos incluem:

- Desabilitar conclusões com severidade abaixo da média
- Desabilitar conclusões que não são patches
- Desabilitar conclusões com Pontuação CVSS abaixo de 6,5
- Desabilitar conclusões com texto específico na verificação ou categoria de segurança (por exemplo, "RedHat", "atualização de segurança do CentOS para sudo")

> [!IMPORTANT]
> Para criar uma regra, você precisa de permissões para editar uma política no Azure Policy.
>
> Saiba mais em [permissões de RBAC do Azure no Azure Policy](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy).

Você pode usar qualquer um dos seguintes critérios: 

- Localizando ID 
- Categoria
- Verificação de segurança 
- Pontuações do CVSS v3
- Severity 
- Status de patch 

Para criar uma regra:

1. Na página de detalhes de recomendações para **vulnerabilidades nas imagens do registro de contêiner do Azure devem ser corrigidas**, selecione **desabilitar regra**.
1. Selecione o escopo relevante.
1. Defina seus critérios.
1. Selecione **aplicar regra**.

    :::image type="content" source="./media/defender-for-container-registries-usage/new-disable-rule-for-registry-finding.png" alt-text="Criar uma regra de desabilitação para descobertas de VA no registro":::

1. Para exibir, substituir ou excluir uma regra: 
    1. Selecione **desabilitar regra**.
    1. Na lista escopo, as assinaturas com regras ativas são mostradas como **regra aplicada**.
        :::image type="content" source="./media/remediate-vulnerability-findings-vm/modify-rule.png" alt-text="Modificar ou excluir uma regra existente":::
    1. Para exibir ou excluir a regra, selecione o menu de reticências ("...").


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre o Azure Defender](azure-defender.md)