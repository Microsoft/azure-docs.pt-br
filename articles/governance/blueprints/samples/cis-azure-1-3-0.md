---
title: Amostra de blueprint do CIS Microsoft Azure Foundations Benchmark v1.3.0
description: Visão geral da amostra de blueprint do CIS Microsoft Azure Foundations Benchmark v1.3.0. Este exemplo de blueprint ajuda os clientes a avaliar controles específicos.
ms.date: 03/11/2021
ms.topic: sample
ms.openlocfilehash: 7030b4e9e9ddd3d5bbf1a5a7deaf1e1aa241b38e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105565989"
---
# <a name="cis-microsoft-azure-foundations-benchmark-v130-blueprint-sample"></a>Amostra de blueprint do CIS Microsoft Azure Foundations Benchmark v1.3.0

A amostra de blueprint do CIS Microsoft Azure Foundations Benchmark v1.3.0 fornece proteções de governança usando o [Azure Policy](../../policy/overview.md) que ajudam você a avaliar recomendações específicas do CIS Microsoft Azure Foundations Benchmark v1.3.0. Esse blueprint ajuda os clientes a implantar um conjunto principal de políticas para qualquer arquitetura implantada pelo Azure que precise implementar as recomendações do CIS Microsoft Azure Foundations Benchmark v1.3.0.

## <a name="recommendation-mapping"></a>Mapeamento de recomendação

O [mapeamento de recomendação do Azure Policy](../../policy/samples/cis-azure-1-3-0.md) fornece detalhes sobre as definições de política incluídas neste blueprint e como elas são mapeadas para as **recomendações** no CIS Microsoft Azure Foundations Benchmark v1.3.0. Quando atribuídos a uma arquitetura, os recursos são avaliados pelo Azure Policy para verificar a não conformidade com definições de política atribuídas. Para saber mais, veja [Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Implantar

Para implantar a amostra de blueprint do CIS Microsoft Azure Foundations Benchmark v1.3.0 do Azure Blueprints, é necessário executar as seguintes etapas:

> [!div class="checklist"]
> - Criar um blueprint com base na amostra
> - Marcar sua cópia do exemplo como **Publicado**
> - Atribuir a sua cópia do blueprint a uma assinatura existente

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

### <a name="create-blueprint-from-sample"></a>Criar um blueprint com base na amostra

Primeiro, implemente a amostra de blueprint criando um blueprint no ambiente usando a amostra como ponto de partida.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Na página **Introdução** à esquerda, selecione o botão **Criar** em _Criar um blueprint_.

1. Encontre a amostra de blueprint **CIS Microsoft Azure Foundations Benchmark v1.3.0** em _Outras Amostras_ e selecione **Usar esta amostra**.

1. Insira as informações _Básicas_ do exemplo de blueprint:

   - **Nome do blueprint**: Forneça um nome para sua cópia da amostra de blueprint do CIS Microsoft Azure Foundations Benchmark.
   - **Localização da definição**: Use as reticências e selecione o grupo de gerenciamento em que deseja salvar a cópia da amostra.

1. Selecione a guia _Artefatos_ na parte superior da página ou clique em **Avançar: Artefatos** na parte inferior da página.

1. Examine a lista de artefatos que estão incluídos no exemplo de blueprint. Muitos dos artefatos têm parâmetros que definiremos mais tarde. Selecione **Salvar Rascunho** quando terminar de examinar o exemplo de blueprint.

### <a name="publish-the-sample-copy"></a>Publicar a cópia do exemplo

Agora a cópia do exemplo de blueprint foi criada em seu ambiente. Ela é criada no modo **Rascunho** e deve ser **Publicada** antes de ser atribuída e implantada. A cópia da amostra de blueprint pode ser personalizada de acordo com seu ambiente e suas necessidades, mas essa transferência poderá desviar você do que é recomendado pelo CIS Microsoft Azure Foundations Benchmark v1.3.0.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione a página **Definições de Blueprint** à esquerda. Use os filtros para localizar a cópia da amostra de blueprint e, em seguida, selecione-a.

1. Selecione **Publicar blueprint** na parte superior da página. Na nova página à direita, informe a **Versão** da sua cópia da amostra de blueprint. Essa propriedade será útil se você fizer uma modificação mais tarde. Forneça **Notas de alterações**, como "Primeira versão publicada da amostra de blueprint do CIS Microsoft Azure Foundations Benchmark". Em seguida, selecione **Publicar** na parte inferior da página.

### <a name="assign-the-sample-copy"></a>Atribuir a cópia de exemplo

Quando a cópia do exemplo de blueprint for **Publicada** com êxito, ele poderá ser atribuído a uma assinatura do grupo de gerenciamento em que ele foi salvo. Esta é a etapa em que os parâmetros são fornecidos para tornar exclusiva cada implantação da cópia do exemplo de blueprint.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione a página **Definições de Blueprint** à esquerda. Use os filtros para localizar a cópia da amostra de blueprint e, em seguida, selecione-a.

1. Selecione **Atribuir blueprint** na parte superior da página de definição de blueprint.

1. Forneça os valores de parâmetro para a atribuição de blueprint:

   - Noções básicas

     - **Assinaturas**: Selecione uma ou mais das assinaturas que estão no grupo de gerenciamento em que você salvou a cópia do exemplo de blueprint. Se você selecionar mais de uma assinatura, será criada uma atribuição para cada uma, usando os parâmetros inseridos.
     - **Nome da atribuição**: O nome é pré-preenchido para você com base no nome do blueprint.
       Altere-o conforme necessário ou mantenha-o como está.
     - **Localização**: Selecione uma região para a identidade gerenciada a ser criada. O Blueprint do Azure usa essa identidade gerenciada para implantar todos os artefatos no blueprint atribuído. Para saber mais, veja [identidades gerenciadas para recursos do Azure](../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Versão de definição de blueprint**: Escolha uma versão **Publicada** da cópia da amostra de blueprint.

   - Bloquear atribuição

     Selecione a configuração de bloqueio de blueprint para o seu ambiente. Para obter mais informações, consulte [bloqueio de recursos de projetos](../concepts/resource-locking.md).

   - Identidade Gerenciada

     Deixe a opção de identidade gerenciada _atribuída ao sistema_ padrão.

   - Parâmetros do artefato

     Os parâmetros definidos nesta seção se aplicam ao artefato sob o qual ele está definido. Esses são [parâmetros dinâmicos](../concepts/parameters.md#dynamic-parameters), pois são definidos durante a atribuição do blueprint. Para obter uma lista completa ou parâmetros de artefato e suas descrições, confira a [Tabela de parâmetros de artefato](#artifact-parameters-table).

1. Depois que todos os parâmetros forem inseridos, selecione **Atribuir** na parte inferior da página. A atribuição de blueprint é criada, e a implantação de artefato é iniciada. A implantação leva aproximadamente uma hora. Para verificar o status da implantação, abra a atribuição de blueprint.

> [!WARNING]
> As amostras internas de blueprint e o serviço Azure Blueprints são **gratuitos**. Os recursos do Azure são [precificados por produto](https://azure.microsoft.com/pricing/). Use a [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator/) para estimar o custo da execução de recursos implantados por essa amostra de blueprint.

### <a name="artifact-parameters-table"></a>Tabela de parâmetros de artefato

A seguinte tabela fornece uma lista dos parâmetros de artefato de blueprint:

|Nome do artefato|Tipo de artefato|Nome do parâmetro|Descrição|
|-|-|-|-|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Lista de extensões da máquina virtual aprovadas para uso|Uma lista separada por ponto e vírgula das extensões da máquina virtual. Para ver uma lista completa de extensões, use o comando Get-AzVMExtensionImage do Azure PowerShell|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: as instâncias gerenciadas de SQL devem usar chaves gerenciadas pelo cliente para criptografar dados inativos|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: as configurações de Avaliação de Vulnerabilidade do servidor SQL devem conter um endereço de email para receber os relatórios de exame|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: os logs de diagnóstico no Azure Data Lake Store devem ser habilitados|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: A criptografia de disco deve ser aplicada em máquinas virtuais|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: o cofre de chaves deve ter a proteção contra limpeza habilitada|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: verificar se o aplicativo de API tem a opção 'Certificados do Cliente (Certificados do cliente de entrada)' definida como 'Ativado'|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: os servidores SQL devem usar chaves gerenciadas pelo cliente para criptografar dados inativos|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: a identidade gerenciada deve ser usada no Aplicativo de Funções|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: o Azure Defender para o Key Vault deve ser habilitado|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: não devem existir funções personalizadas de proprietário da assinatura|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: as chaves devem ter datas de validade definidas|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: A Transparent Data Encryption em bancos de dados SQL deve ser habilitada|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: a avaliação de vulnerabilidades deve ser habilitada na Instância Gerenciada de SQL|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: garantir que a 'versão do PHP' seja a última, se usada como parte do aplicativo de API|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: Um administrador do Azure Active Directory deve ser provisionado para servidores SQL|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: o Azure Defender para o Serviço de Aplicativo deve ser habilitado|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: contas de armazenamento devem restringir o acesso à rede usando regras da rede virtual|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: a identidade gerenciada deve ser usada no Aplicativo Web|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: o acesso SSH da Internet deve ser bloqueado|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: discos desanexados devem ser criptografados|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: o Azure Defender para Armazenamento deve ser habilitado|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: contas de armazenamento devem restringir o acesso à rede|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: os logs de diagnóstico nos Aplicativos Lógicos devem ser habilitados|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: os logs de diagnóstico no Hub IoT devem ser habilitados|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: o FTPS deve ser exigido somente no Aplicativo de Funções|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: um alerta do log de atividades deve existir para operações de segurança específicas (Microsoft.Security/securitySolutions/excluir)|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: um alerta do log de atividades deve existir para operações de segurança específicas (Microsoft.Security/securitySolutions/gravar)|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: A transferência segura para contas de armazenamento deve ser habilitada|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: os logs de diagnóstico em contas do Lote devem ser habilitados|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: o provisionamento automático do agente do Log Analytics deve ser habilitado em sua assinatura|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: Garantir que a 'versão do Java' seja a última, se usada como parte do aplicativo Web|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: o FTPS deve ser exigido no Aplicativo Web|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: o Azure Defender para servidores deve ser habilitado|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: as assinaturas devem ter um endereço de email de contato para problemas de segurança|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: o acesso público da conta de armazenamento não deve ser permitido|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: o Azure Defender para Kubernetes deve ser habilitado|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: a limitação da conexão deve ser habilitada para os servidores de banco de dados PostgreSQL|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: verificar se o aplicativo Web tem a opção 'Certificados do Cliente (Certificados do cliente de entrada)' definida como 'Ativado'|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: As contas externas com permissões de gravação devem ser removidas de sua assinatura|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: As contas externas com permissões de leitura devem ser removidas de sua assinatura|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: o Azure Defender para SQL Servers deve ser habilitado nos computadores|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: a notificação por email de alertas de severidade alta deve ser habilitada|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: a conta de armazenamento deve usar uma chave gerenciada pelo cliente para criptografia|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: Garantir que a 'versão do Python' seja a última, se usada como parte do aplicativo Web|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: Garantir que a 'versão do Python' seja a última, se usada como parte do aplicativo de funções|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: Garantir que a 'versão do PHP' seja a última, se usada como parte do aplicativo Web|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: Garantir que a 'versão do Python' seja a última, se usada como parte do aplicativo de API|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: os logs de diagnóstico nos Conjuntos de Dimensionamento de Máquinas Virtuais devem ser habilitados|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: o Azure Defender para servidores do Banco de Dados SQL do Azure deve ser habilitado|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: os logs de diagnóstico no Hub de Eventos devem ser habilitados|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: As atualizações do sistema devem ser instaladas em suas máquinas|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: Garantir que a 'versão do Java' seja a última, se usada como parte do aplicativo de API|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: os servidores SQL devem ser configurados com 90 dias ou mais de retenção de auditoria.|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: Garantir que a 'Versão do HTTP' seja a última, se usada para executar o aplicativo Web|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: A última versão do TLS deve ser usada no aplicativo de API|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: A MFA deve ser habilitada nas contas com permissões de gravação na sua assinatura|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: a autenticação deve ser habilitada no aplicativo Web|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: os segredos devem ter datas de vencimento definidas|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: Garantir que a 'Versão do HTTP' seja a última, se usada para executar o aplicativo de API|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: o FTPS deve ser exigido somente no Aplicativo de API|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: Garantir que a "versão do Java" seja a mais recente, se usada como parte do aplicativo de funções|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: Aplicativo Web deve ser acessível somente por HTTPS|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: A auditoria no SQL Server deve ser habilitada|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: O MFA deve ser habilitado em contas com permissões de proprietário em sua assinatura|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: A Segurança de Dados Avançada deve ser habilitada nos servidores SQL|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: a Segurança de Dados Avançada deve ser habilitada na Instância Gerenciada de SQL|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: o RBAC (Controle de Acesso Baseado em Função) deve ser usado nos Serviços de Kubernetes|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: monitora o Endpoint Protection ausente na Central de Segurança do Azure|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: os logs de diagnóstico nos serviços Pesquisa devem ser habilitados|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: os logs de diagnóstico nos Serviços de Aplicativos devem ser habilitados|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: um alerta do log de atividades deve existir para Operações administrativas específicas (Microsoft.Network/networkSecurityGroups/excluir)|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: um alerta do log de atividades deve existir para Operações administrativas específicas (Microsoft.Network/networkSecurityGroups/securityRules/excluir)|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: um alerta do log de atividades deve existir para Operações administrativas específicas (Microsoft.Network/networkSecurityGroups/securityRules/gravar)|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: um alerta do log de atividades deve existir para Operações administrativas específicas (Microsoft.Network/networkSecurityGroups/gravar)|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: um alerta do log de atividades deve existir para Operações administrativas específicas (Microsoft.Sql/servidores/firewallRules/excluir)|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: um alerta do log de atividades deve existir para Operações administrativas específicas (Microsoft.Sql/servidores/firewallRules/gravar)|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: somente as extensões VM aprovadas devem ser instaladas|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: o Azure Defender para Registros de contêiner deve ser habilitado|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: a identidade gerenciada deve ser usada no Aplicativo de API|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: a autenticação deve ser habilitada no aplicativo de API|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: um alerta do log de atividades deve existir para Operações de política específicas (Microsoft.Authorization/policyAssignments/excluir)|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: um alerta do log de atividades deve existir para Operações de política específicas (Microsoft.Authorization/policyAssignments/gravar)|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: a autenticação deve ser habilitada no aplicativo de Funções|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: os logs de diagnóstico no Data Lake Analytics devem ser habilitados|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: as contas de armazenamento devem permitir o acesso de serviços confiáveis da Microsoft|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: os logs de diagnóstico no Key Vault devem estar habilitados|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: a imposição da conexão SSL deve ser habilitada para os servidores de banco de dados PostgreSQL|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: Garantir que a 'Versão do HTTP' seja a última, se usada para executar o aplicativo de funções|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: O MFA deve ser habilitado em contas com permissões de leitura em sua assinatura|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: o acesso RDP da Internet deve ser bloqueado|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: a imposição da conexão SSL deve ser habilitada para os servidores de banco de dados MySQL|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: verificar se o Aplicativo de funções tem a opção 'Certificados do Cliente (Certificados do cliente de entrada)' definida como 'Ativado'|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: os pontos de verificação de log devem ser habilitados para os servidores de banco de dados PostgreSQL|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: as conexões de log devem ser habilitadas para os servidores de banco de dados PostgreSQL|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: as desconexões devem ser registradas para os servidores de banco de dados PostgreSQL.|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: A avaliação da vulnerabilidade deve ser habilitada nos servidores SQL|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: A última versão do TLS deve ser usada no aplicativo Web|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: As contas externas com permissões de proprietário devem ser removidas de sua assinatura|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: os logs de diagnóstico no Barramento de Serviço devem ser habilitados|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: os logs de diagnóstico no Azure Stream Analytics devem ser habilitados|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: A última versão do TLS deve ser usada no aplicativo de funções|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Efeito para a política: conta de armazenamento que contém o contêiner com os logs de atividades precisa ser criptografada com BYOK|Para obter mais informações sobre efeitos, confira [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Incluir os clusters do AKS ao auditar se os logs de diagnóstico do conjunto de dimensionamento de máquinas virtuais estão habilitados||
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Última versão do Java para os Serviços de Aplicativos|Versão mais recente do Java com suporte para Serviços de Aplicativos|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Última versão do Python para Linux para os Serviços de Aplicativos|Versão mais recente do Python com suporte para Serviços de Aplicativos|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Lista de regiões em que o Observador de Rede deve ser habilitado|Para ver uma lista completa de regiões, execute o comando Get-AzLocation do PowerShell|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Última versão do PHP para os Serviços de Aplicativos|Versão mais recente do PHP com suporte para Serviços de Aplicativos|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Período de retenção necessário (dias) para os logs de recurso|Para obter mais informações sobre os logs de recursos, visite [https://aka.ms/resourcelogs](../../../azure-monitor/essentials/resource-logs.md) |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Nome do grupo de recursos do Observador de Rede|Nome do grupo de recursos no qual os Observadores de Rede estão localizados|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Atribuição de política|Configuração de auditoria necessária para os SQL Servers||

## <a name="next-steps"></a>Próximas etapas

Outros artigos sobre blueprints e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../how-to/update-existing-assignments.md).