---
title: Criar Datadog – soluções de parceiros do Azure
description: Este artigo descreve como usar o portal do Azure para criar uma instância do Datadog.
ms.service: partner-services
ms.topic: quickstart
ms.date: 02/19/2021
author: tfitzmac
ms.author: tomfitz
ms.custom: references_regions
ms.openlocfilehash: 7af8b82c5da6c60527b45b6e8e292b9f067016ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101747270"
---
# <a name="quickstart-get-started-with-datadog"></a>Início Rápido: Introdução ao Datadog

Neste guia de início rápido, você criará uma instância de Datadog. Você pode criar uma organização do Datadog ou vincular-se a uma organização do Datadog existente.

## <a name="pre-requisites"></a>Pré-requisitos

Para configurar a integração do Datadog do Azure, você precisa ter acesso de **Proprietário** na assinatura do Azure. Verifique se você tem o acesso apropriado antes de iniciar a configuração.

Para usar o recurso de SSO (Logon Único) de SAML (Security Assertion Markup Language) dentro do recurso de Datadog, você precisa configurar um aplicativo empresarial. Para adicionar um aplicativo empresarial, você precisa de uma destas funções: Administrador global, Administrador de aplicativos de nuvem, Administrador de aplicativos ou proprietário da entidade de serviço.

Use as seguintes etapas para configurar o aplicativo empresarial:

1. Vá para o [portal do Azure](https://portal.azure.com). Selecione **Azure Active Directory**.
1. No painel esquerdo, selecione **Aplicativos Empresariais**.
1. Selecione **Novo Aplicativo**.
1. Em **Adicionar por meio da galeria**, procure *Datadog*. Selecione o resultado da pesquisa e, em seguida, selecione **Adicionar**.

   :::image type="content" source="media/create/datadog-azure-ad-app-gallery.png" alt-text="Aplicativo Datadog na galeria empresarial do AAD." border="true":::

1. Depois que o aplicativo for criado, acesse as propriedades no painel lateral. Defina **Atribuição de usuário necessária?** como **Não** e selecione **Salvar**.

   :::image type="content" source="media/create/user-assignment-required.png" alt-text="Definir propriedades para o aplicativo Datadog" border="true":::

1. Acesse **Logon único** no painel lateral. Em seguida, selecione **SAML**.

   :::image type="content" source="media/create/saml-sso.png" alt-text="Autenticação SAML." border="true":::

1. Selecione **Sim** quando for solicitado para **Salvar as configurações de logon único**.

   :::image type="content" source="media/create/save-sso.png" alt-text="Salvar logon único para o aplicativo Datadog" border="true":::

1. A configuração do logon único agora está concluída.

## <a name="find-offer"></a>Localizar oferta

Use o portal do Azure para encontrar o Datadog.

1. Acesse o [portal do Azure](https://portal.azure.com/) e entre.

1. Se você acessou o **Marketplace** em uma sessão recente, selecione o ícone nas opções disponíveis. Caso contrário, pesquise _Marketplace_.

    :::image type="content" source="media/create/marketplace.png" alt-text="Ícone do Marketplace.":::

1. No Marketplace, pesquise **Datadog**.

1. Na tela de visão geral do plano, selecione **Configurar + assinar**.

   :::image type="content" source="media/create/datadog-app.png" alt-text="Aplicativo Datadog no Azure Marketplace.":::

## <a name="create-a-datadog-resource-in-azure"></a>Criar um recurso do Datadog no Azure

O portal exibe um formulário para criar o recurso do Datadog.

:::image type="content" source="media/create/datadog-create-resource.png" alt-text="Criar recurso do Datadog" border="true":::

Forneça os valores a seguir.

|Propriedade | Descrição
|:-----------|:-------- |
| Subscription | Selecione a assinatura do Azure que você deseja usar para criar o recurso do Datadog. Você precisa ter acesso de proprietário. |
| Resource group | Especifique se deseja criar um novo grupo de recursos ou usar um existente. Um [grupo de recursos](../../azure-resource-manager/management/overview.md#resource-groups) é um contêiner que armazena os recursos relacionados de uma solução do Azure. |
| Nome do recurso | Especifique um nome para o recurso do Datadog. Esse nome será o nome da nova organização do Datadog ao criar uma organização do Datadog. |
| Local | Selecione Oeste dos EUA 2. No momento, Oeste dos EUA 2 é a única região com suporte. |
| Organização do Datadog | Para criar uma organização do Datadog, selecione **Novo**. Para vincular-se a uma organização do Datadog existente, selecione **Existente**. |
| Plano de preços | Ao criar uma organização, selecione-a na lista de planos do Datadog disponíveis. |
| Prazo de cobrança | Mensalmente. |

Se você estiver se vinculando a uma organização do Datadog existente, confira a próxima seção. Caso contrário, selecione **Avançar: métricas e logs** e ignore a próxima seção.

## <a name="link-to-existing-datadog-organization"></a>Link para a organização do Datadog existente

Você pode vincular seu novo recurso do Datadog no Azure a uma organização do Datadog existente.

Selecione **Existente** para a organização de dados e, em seguida, selecione **Vincular-se uma organização do Datadog**.

:::image type="content" source="media/create/link-to-existing.png" alt-text="Link para a organização do Datadog existente." border="true":::

O link abre uma janela de autenticação do Datadog. Entre no Datadog.

Por padrão, o Azure vincula sua organização do Datadog atual ao recurso do Datadog. Se você quiser vincular-se a uma organização diferente, selecione a organização apropriada na janela de autenticação, conforme mostrado abaixo.

:::image type="content" source="media/create/select-datadog-organization.png" alt-text="Selecione a organização do Datadog adequada a ser vinculada" border="true":::

## <a name="configure-metrics-and-logs"></a>Configurar métricas e logs

Use as marcas de recurso do Azure para configurar quais métricas e logs são enviados para o Datadog. Você pode incluir ou excluir métricas e logs para recursos específicos.

As regras de marca para enviar **métricas** são:

- Por padrão, as métricas são coletadas para todos os recursos, exceto para máquinas virtuais, conjuntos de dimensionamento de máquinas virtuais e planos do serviço de aplicativo.
- Máquinas virtuais, conjuntos de dimensionamento de máquinas virtuais e planos do serviço de aplicativo com marcas de *inclusão* enviam métricas para o Datadog.
- Máquinas virtuais, conjuntos de dimensionamento de máquinas virtuais e planos do serviço de aplicativo com marcas de *exclusão* não enviam métricas para o Datadog.
- Se houver um conflito entre as regras de inclusão e exclusão, a de exclusão terá prioridade

As regras de marca para enviar **logs** são:

- Por padrão, os logs são coletados para todos os recursos.
- Os recursos do Azure com marcas de *inclusão* enviam logs para o Datadog.
- Os recursos do Azure com marcas de *exclusão* não enviam logs para o Datadog.
- Se houver um conflito entre as regras de inclusão e exclusão, a de exclusão terá prioridade.

Por exemplo, a captura de tela abaixo mostra uma regra de marca em que somente as máquinas virtuais, os conjuntos de dimensionamento de máquinas virtuais e os planos do serviço de aplicativo marcados como *Datadog = True* enviam métricas para o Datadog.

:::image type="content" source="media/create/config-metrics-logs.png" alt-text="Configurar Logs e Métricas." border="true":::

Há dois tipos de logs que podem ser emitidos do Azure para o Datadog.

1. **Logs de nível de assinatura** – forneça informações sobre as operações em seus recursos no [painel de controle](../../azure-resource-manager/management/control-plane-and-data-plane.md). As atualizações sobre eventos de integridade de serviço também estão incluídas. Use o log de atividades para determinar o quê, quem e quando para qualquer operação de gravação (PUT, POST, DELETE). Há um único log de atividades para cada assinatura do Azure.

1. **Logs de recursos do Azure** – fornecem informações sobre as operações que foram realizadas em um recurso do Azure no [plano de dados](../../azure-resource-manager/management/control-plane-and-data-plane.md). Por exemplo, obter um segredo de um Key Vault é uma operação do plano de dados. Ou fazer uma solicitação para um banco de dados também é uma operação de plano de dados. O conteúdo de logs de recursos varia de acordo com o tipo de recurso e serviço do Azure.

Para enviar logs de nível de assinatura para o Datadog, selecione **Enviar logs de atividade de assinatura**. Se essa opção ficar desmarcada, nenhum dos logs de nível de assinatura será enviado para o Datadog.

Para enviar logs de recursos do Azure para o Datadog, selecione **Enviar logs de recursos do Azure para todos os recursos definidos**. Os tipos de logs de recursos do Azure são listados em [Categorias de log de recursos do Azure Monitor](../../azure-monitor/essentials/resource-logs-categories.md).  Para filtrar o conjunto de recursos do Azure que envia logs para o Datadog, use as marcas de recurso do Azure.  

Os logs enviados para o Datadog serão cobrados pelo Azure. Para obter mais informações, confira os [preços dos logs da plataforma](https://azure.microsoft.com/pricing/details/monitor/) enviados aos parceiros do Azure Marketplace.

Depois de concluir a configuração de métricas e logs, selecione **Avançar: logon único**.

## <a name="configure-single-sign-on"></a>Configurar o logon único

Se sua organização usa o Azure Active Directory como provedor de identidade, você pode estabelecer o logon único do portal do Azure no Datadog. Se sua organização usar um provedor de identidade diferente ou você não desejar estabelecer o logon único no momento, você poderá ignorar esta seção.

Se estiver vinculando o recurso do Datadog a uma organização existente do Datadog, você não poderá configurar o logon único nesta etapa. Em vez disso, você vai configurar o logon único depois de criar o recurso do Datadog. Para saber mais, confira [Reconfigurar o logon único](manage.md#reconfigure-single-sign-on).

:::image type="content" source="media/create/linking-sso.png" alt-text="Logon único para vinculação à organização do Datadog existente." border="true":::

Para estabelecer o logon único por meio do Azure Active Directory, marque a caixa de seleção **Habilitar logon único por meio do Azure Active Directory**.

O portal do Azure recupera o aplicativo Datadog apropriado do Azure Active Directory. O aplicativo corresponde ao aplicativo empresarial que você forneceu em uma etapa anterior.

Selecione o nome do aplicativo Datadog.

:::image type="content" source="media/create/sso.png" alt-text="Habilite o logon único para o Datadog." border="true":::

Selecione **Avançar: Marcas**.

## <a name="add-custom-tags"></a>Adicionar marcas personalizadas

Você pode especificar marcas personalizadas para o novo recurso do Datadog. Forneça pares de nome e valor para as marcas a serem aplicadas ao recurso do Datadog.

:::image type="content" source="media/create/tags.png" alt-text="Adicione marcas personalizadas para o recurso do Datadog." border="true":::

Quando terminar de adicionar marcas, selecione **Avançar: Examinar + criar**.

## <a name="review--create-datadog-resource"></a>Recurso Examinar + Criar do Datadog

Examine suas seleções e os termos de uso. Depois de concluir a validação, selecione **Criar**.

:::image type="content" source="media/create/review-create.png" alt-text="Recurso do Datadog Examinar + Criar." border="true":::

O Azure implanta o recurso do Datadog.

Quando o processo for concluído, selecione **Ir para o recurso** para ver o recurso do Datadog.

:::image type="content" source="media/create/go-to-resource.png" alt-text="Implantação de recursos do Datadog." border="true":::

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Gerenciar o recurso do Datadog](manage.md)
