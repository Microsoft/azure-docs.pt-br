---
title: Exemplo - diagrama de PCI-DSS v3.2.1 - as etapas de implantação
description: Implante as etapas do exemplo de plano gráfico de v3.2.1 Payment Card Industry Data Security Standard.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: b4e9435e5f569a076fc5beb6441b9da935b87f3a
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561375"
---
# <a name="deploy-the-pci-dss-v321-blueprint-sample"></a>Implantar o exemplo de especificações técnicas do v3.2.1 de PCI DSS

Para implantar o exemplo de especificações técnicas do Azure BluePrint PCI-DSS v3.2.1, as etapas a seguir devem ser executadas:

> [!div class="checklist"]
> - Criar um blueprint com base na amostra
> - Marcar sua cópia do exemplo como **Publicado**
> - Atribuir a sua cópia do blueprint a uma assinatura existente

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-blueprint-from-sample"></a>Criar um blueprint com base na amostra

Primeiro, implemente a amostra de blueprint criando um blueprint no ambiente usando a amostra como ponto de partida.

1. Selecione **Todos os serviços** e pesquise e selecione **Política** no painel esquerdo. Na página **Política**, selecione **Blueprints**.

1. Na página **Introdução** à esquerda, selecione o botão **Criar** em _Criar um blueprint_.

1. Localizar o **PCI-DSS v3.2.1** exemplo de plano gráfico em _outras amostras_ e selecione **usar este exemplo**.

1. Insira as informações _Básicas_ do exemplo de blueprint:

   - **Nome do blueprint**: Forneça um nome para a sua cópia do exemplo de plano gráfico de v3.2.1 PCI-DSS.
   - **Localização da definição**: Use as reticências e selecione o grupo de gerenciamento em que deseja salvar a cópia da amostra.

1. Selecione a guia _Artefatos_ na parte superior da página ou clique em **Avançar: Artefatos** na parte inferior da página.

1. Examine a lista de artefatos que compõem o exemplo de blueprint. Muitos dos artefatos têm parâmetros que definiremos mais tarde. Selecione **Salvar Rascunho** quando terminar de examinar o exemplo de blueprint.

## <a name="publish-the-sample-copy"></a>Publicar a cópia do exemplo

Agora a cópia do exemplo de blueprint foi criada em seu ambiente. Ela é criada no modo **Rascunho** e deve ser **Publicada** antes de ser atribuída e implantada. A cópia do exemplo de especificação técnica pode ser personalizada ao seu ambiente e as necessidades, mas que modificação pode movê-lo para fora o v3.2.1 de PCI-DSS padrão.

1. Selecione **Todos os serviços** e pesquise e selecione **Política** no painel esquerdo. Na página **Política**, selecione **Blueprints**.

1. Selecione a página **Definições de Blueprint** à esquerda. Use os filtros para localizar a cópia da amostra de blueprint e, em seguida, selecione-a.

1. Selecione **Publicar blueprint** na parte superior da página. Na nova página à direita, informe a **Versão** da sua cópia da amostra de blueprint. Essa propriedade será útil se você fizer uma modificação mais tarde. Fornecer **alterar notas** , como "primeira versão publicada do exemplo de especificações técnicas do PCI-DSS v3.2.1." Em seguida, selecione **Publicar** na parte inferior da página.

## <a name="assign-the-sample-copy"></a>Atribuir a cópia de exemplo

Quando a cópia do exemplo de blueprint for **Publicada** com êxito, ele poderá ser atribuído a uma assinatura do grupo de gerenciamento em que ele foi salvo. Esta é a etapa em que os parâmetros são fornecidos para tornar exclusiva cada implantação da cópia do exemplo de blueprint.

1. Selecione **Todos os serviços** e pesquise e selecione **Política** no painel esquerdo. Na página **Política**, selecione **Blueprints**.

1. Selecione a página **Definições de Blueprint** à esquerda. Use os filtros para localizar a cópia da amostra de blueprint e, em seguida, selecione-a.

1. Selecione **Atribuir blueprint** na parte superior da página de definição de blueprint.

1. Forneça os valores de parâmetro para a atribuição de blueprint:

   - Noções básicas

     - **Assinaturas**: Selecione uma ou mais das assinaturas que estão no grupo de gerenciamento em que você salvou a cópia do exemplo de blueprint. Se você selecionar mais de uma assinatura, será criada uma atribuição para cada uma, usando os parâmetros inseridos.
     - **Nome da atribuição**: O nome é pré-preenchido para você com base no nome do blueprint.
       Altere-o conforme necessário ou mantenha-o como está.
     - **Localização**: Selecione uma região para a identidade gerenciada a ser criada. O Blueprint do Azure usa essa identidade gerenciada para implantar todos os artefatos no blueprint atribuído. Para saber mais, veja [identidades gerenciadas para recursos do Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Versão de definição de blueprint**: Escolha uma versão **Publicada** da cópia da amostra de blueprint.

   - Bloquear atribuição

     Selecione a configuração de bloqueio de blueprint para o seu ambiente. Para obter mais informações, consulte [bloqueio de recursos de projetos](../../concepts/resource-locking.md).

   - Identidade Gerenciada

     Deixe a opção de identidade gerenciada _atribuída ao sistema_ padrão.

   - Parâmetros do artefato

     Os parâmetros definidos nesta seção se aplicam ao artefato sob o qual ele está definido. Esses são [parâmetros dinâmicos](../../concepts/parameters.md#dynamic-parameters), pois são definidos durante a atribuição do blueprint. Para obter uma lista completa ou parâmetros de artefato e suas descrições, confira a [Tabela de parâmetros de artefato](#artifact-parameters-table).

1. Depois que todos os parâmetros forem inseridos, selecione **Atribuir** na parte inferior da página. A atribuição de blueprint é criada, e a implantação de artefato é iniciada. A implantação leva aproximadamente uma hora. Para verificar o status da implantação, abra a atribuição de blueprint.

> [!WARNING]
> As amostras internas de blueprint e o serviço Azure Blueprints são **gratuitos**. Os recursos do Azure são [precificados por produto](https://azure.microsoft.com/pricing/). Use a [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator/) para estimar o custo da execução de recursos implantados por essa amostra de blueprint.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros de artefato

A seguinte tabela fornece uma lista dos parâmetros de artefato de blueprint:

|Nome do artefato|Tipo de artefato|Nome do parâmetro|DESCRIÇÃO|
|-|-|-|-|
|[Versão prévia]: PCI v3.2.1:2018 controles de auditoria e implantar extensões de VM específicas para dar suporte a requisitos de auditoria|Atribuição de política|Lista de tipos de recursos | Auditar configuração de diagnóstico para os tipos de recursos selecionado. Valor padrão é que todos os recursos são selecionados| 
|Locais permitidos|Atribuição de política|Lista de locais permitidos|Lista de locais de centro de dados permitido para qualquer recurso a ser implantada. Esta lista é personalizável para locais do Azure desejados globalmente. Selecione os locais que deseja permitir.| 
|Locais permitidos para grupos de recursos|Atribuição de política |Local permitido |Essa política permite que você restrinja os locais de que sua organização pode criar grupos de recursos no. Use para impor seus requisitos de conformidade geográfica.| 
|Implantar auditoria em servidores SQL|Atribuição de política|Dias de retenção|Retenção de dados em número de dias. Valor padrão é 180, mas PCI requer 365.| 
|Implantar auditoria em servidores SQL|Atribuição de política|Nome do grupo de recursos para a conta de armazenamento|Log de auditoria grava eventos de banco de dados a uma auditoria em sua conta de armazenamento do Azure (uma conta de armazenamento será criada em cada região em que um SQL Server é criado que será compartilhado por todos os servidores nessa região).| 

## <a name="next-steps"></a>Próximas etapas

Agora que você tiver revisado as etapas para implantar o exemplo de especificações técnicas do PCI-DSS v3.2.1, visite os seguintes artigos para saber mais sobre a visão geral e o mapeamento de controle:

> [!div class="nextstepaction"]
> [Diagrama de v3.2.1 de PCI DSS - visão geral](./index.md)
> [blueprint v3.2.1 de PCI DSS - mapeamento de controle](./control-mapping.md)

Outros artigos sobre blueprints e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../../how-to/update-existing-assignments.md).
