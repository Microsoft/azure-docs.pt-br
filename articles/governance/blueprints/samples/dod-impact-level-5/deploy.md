---
title: Modelo do blueprint do Nível de Impacto 5 do DoD
description: Etapas de implantação do modelo de blueprint do Nível de Impacto 5 do DoD, incluindo detalhes do parâmetro do artefato de blueprint.
ms.date: 04/02/2021
ms.topic: sample
ms.openlocfilehash: d9292a9033a3c0b92e1a00c63d74bd1fb6023794
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106386708"
---
# <a name="deploy-the-dod-impact-level-5-blueprint-sample"></a>Implantar o modelo de blueprint do Nível de Impacto 5 do DoD

Para implantar o modelo do blueprint do Departamento de Defesa do Azure Blueprints DoD IL5 (Nível de Impacto 5 do DoD), as seguintes etapas precisam ser seguidas:

> [!div class="checklist"]
> - Criar um blueprint com base na amostra
> - Marcar sua cópia do exemplo como **Publicado**
> - Atribuir a sua cópia do blueprint a uma assinatura existente

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-blueprint-from-sample"></a>Criar um blueprint com base na amostra

Primeiro, implemente a amostra de blueprint criando um blueprint no ambiente usando a amostra como ponto de partida.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Na página **Introdução** à esquerda, selecione o botão **Criar** em _Criar um blueprint_.

1. Encontre o modelo de blueprint **Nível de Impacto 5 do DoD** em _Outros Exemplos_ e selecione **Usar este exemplo**.

1. Insira as informações _Básicas_ do exemplo de blueprint:

   - **Nome do blueprint**: Forneça um nome para a sua cópia do modelo de blueprint Nível de Impacto 5 do DoD.
   - **Localização da definição**: Use as reticências e selecione o grupo de gerenciamento em que deseja salvar a cópia da amostra.

1. Selecione a guia _Artefatos_ na parte superior da página ou clique em **Avançar: Artefatos** na parte inferior da página.

1. Examine a lista de artefatos que compõem o exemplo de blueprint. Muitos dos artefatos têm parâmetros que definiremos mais tarde. Selecione **Salvar Rascunho** quando terminar de examinar o exemplo de blueprint.

## <a name="publish-the-sample-copy"></a>Publicar a cópia do exemplo

Agora a cópia do exemplo de blueprint foi criada em seu ambiente. Ela é criada no modo **Rascunho** e deve ser **Publicada** antes de ser atribuída e implantada. A cópia do exemplo de blueprint pode ser personalizada de acordo com seu ambiente e suas necessidades, mas essa modificação poderá desviá-lo dos controles Nível de Impacto 5 do DoD.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione a página **Definições de Blueprint** à esquerda. Use os filtros para localizar a cópia da amostra de blueprint e, em seguida, selecione-a.

1. Selecione **Publicar blueprint** na parte superior da página. Na nova página à direita, informe a **Versão** da sua cópia da amostra de blueprint. Essa propriedade será útil se você fizer uma modificação mais tarde. Forneça **Notas de alterações**, como "Primeira versão publicada do modelo de blueprint do DoD IL5". Em seguida, selecione **Publicar** na parte inferior da página.

## <a name="assign-the-sample-copy"></a>Atribuir a cópia de exemplo

Quando a cópia do exemplo de blueprint for **Publicada** com êxito, ele poderá ser atribuído a uma assinatura do grupo de gerenciamento em que ele foi salvo. Esta é a etapa em que os parâmetros são fornecidos para tornar exclusiva cada implantação da cópia do exemplo de blueprint.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

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

|Nome do artefato|Tipo de artefato|Nome do parâmetro|Descrição|
|-|-|-|-|
|Nível de Impacto 5 do DoD|Atribuição de política|Lista de usuários que devem ser incluídos no grupo de Administradores de VM do Windows|Uma lista separada por ponto e vírgula de usuários que devem ser incluídos no grupo local de Administradores, por exemplo: Administrador; myUser1; myUser2|
|Nível de Impacto 5 do DoD|Atribuição de política|Lista de usuários excluídos do grupo de Administradores de VM do Windows|Uma lista separada por ponto e vírgula de usuários que devem ser excluídos do grupo local de Administradores, por exemplo: Administrador; myUser1; myUser2|
|Nível de Impacto 5 do DoD|Atribuição de política|Lista de tipos de recurso que devem ter os logs de diagnóstico habilitados||
|Nível de Impacto 5 do DoD|Atribuição de política|ID do workspace do Log Analytics para relatórios do agente de VM|ID (GUID) do workspace do Log Analytics em que os agentes de VMs devem relatar|
|Nível de Impacto 5 do DoD|Atribuição de política|Lista de regiões em que o Observador de Rede deve ser habilitado|Para ver uma lista completa de regiões, use Get-AzLocation|
|Nível de Impacto 5 do DoD|Atribuição de política|Versão mínima do TLS para servidores Web do Windows|A versão mínima do protocolo TLS que deve ser habilitada nos servidores Web do Windows|
|Nível de Impacto 5 do DoD|Atribuição de política|Última versão do PHP|Versão mais recente do PHP com suporte para Serviços de Aplicativos|
|Nível de Impacto 5 do DoD|Atribuição de política|Última versão do Java|Versão mais recente do Java com suporte para Serviços de Aplicativos|
|Nível de Impacto 5 do DoD|Atribuição de política|Última versão do Python do Windows|Versão mais recente do Python com suporte para Serviços de Aplicativos|
|Nível de Impacto 5 do DoD|Atribuição de política|Versão mais recente do Python do Linux|Versão mais recente do Python com suporte para Serviços de Aplicativos|
|Nível de Impacto 5 do DoD|Atribuição de política|Opcional: lista de imagens de VM do Windows que dão suporte ao agente do Log Analytics para adicionar ao escopo de auditoria|Uma lista de imagens separadas por ponto e vírgula. Por exemplo: /subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage|
|Nível de Impacto 5 do DoD|Atribuição de política|Opcional: lista de imagens da VM do Linux que dão suporte ao agente do Log Analytics para adicionar ao escopo de auditoria|Uma lista de imagens separadas por ponto e vírgula. Por exemplo: /subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: Deve haver mais de um proprietário atribuído à sua assinatura|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: A criptografia de disco deve ser aplicada em máquinas virtuais|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: A notificação por email para o proprietário da assinatura para alertas de severidade alta deve ser habilitada|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: A depuração remota deve ser desativada para o aplicativos de funções|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: Verificar se a versão do '.NET Framework' é a última, se usada como parte do aplicativo de funções|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: A Transparent Data Encryption em bancos de dados SQL deve ser habilitada|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: A avaliação de vulnerabilidades deve ser habilitada nas instâncias gerenciadas do SQL|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: Garantir que a 'versão do PHP' seja a última, se usada como parte do aplicativo de API|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: Um administrador do Azure Active Directory deve ser provisionado para servidores SQL|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: Apenas conexões seguras com o Cache Redis devem ser habilitadas|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: A solução de proteção de ponto de extremidade deve ser instalada nos conjuntos de dimensionamento de máquinas virtuais|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: Auditar o acesso irrestrito à rede para contas de armazenamento|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: As configurações da Segurança de Dados Avançada para a instância gerenciada do SQL devem conter um endereço de email para receber alertas de segurança|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: As vulnerabilidades da configuração de segurança nos conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: A transferência segura para contas de armazenamento deve ser habilitada|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: Os controles de aplicativos adaptáveis devem ser habilitados em máquinas virtuais|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para PostgreSQL|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: Garantir que a 'versão do Java' seja a última, se usada como parte do aplicativo Web|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: Um máximo de três proprietários deve ser designado para sua assinatura|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: Um endereço de email de contato de segurança deve ser fornecido para a sua assinatura|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: O CORS não deve permitir que todos os recursos acessem seus aplicativos Web|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: As contas externas com permissões de gravação devem ser removidas de sua assinatura|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: As contas externas com permissões de leitura devem ser removidas de sua assinatura|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: As contas preteridas devem ser removidas de sua assinatura|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: O aplicativo de funções deve ser acessível apenas por HTTPS|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: Garantir que a 'versão do Python' seja a última, se usada como parte do aplicativo Web|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: Garantir que a 'versão do Python' seja a última, se usada como parte do aplicativo de funções|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: Garantir que a 'versão do PHP' seja a última, se usada como parte do aplicativo Web|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: Garantir que a 'versão do Python' seja a última, se usada como parte do aplicativo de API|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: As vulnerabilidades devem ser corrigidas por uma solução de Avaliação de Vulnerabilidades|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para MySQL|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: Verificar se a versão do '.NET Framework' é a última, se usada como parte do aplicativo Web|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: As atualizações do sistema devem ser instaladas em suas máquinas|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: Garantir que a 'versão do Java' seja a última, se usada como parte do aplicativo de API|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: Garantir que a 'Versão do HTTP' seja a última, se usada para executar o aplicativo Web|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: A última versão do TLS deve ser usada no aplicativo de API|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: A MFA deve ser habilitada nas contas com permissões de gravação na sua assinatura|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: As configurações da Segurança de Dados Avançada para o SQL Server devem conter um endereço de email para receber alertas de segurança|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: Garantir que a 'Versão do HTTP' seja a última, se usada para executar o aplicativo de API|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: A extensão IaaSAntimalware da Microsoft deve ser implantada em servidores do Windows|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: Garantir que a "versão do Java" seja a mais recente, se usada como parte do aplicativo de funções|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: O acesso pelo ponto de extremidade para a Internet deve ser restrito|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: O tipo de preço Standard da Central de Segurança deve ser selecionado|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: Auditar o uso de regras personalizadas do RBAC|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: Aplicativo Web deve ser acessível somente por HTTPS|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: A auditoria no SQL Server deve ser habilitada|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: O agente do Log Analytics deve ser instalado nas máquinas virtuais|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: A Proteção contra DDoS Standard deve ser habilitada|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: O MFA deve ser habilitado em contas com permissões de proprietário em sua assinatura|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: Garantir que a 'versão do PHP' seja a última, se usada como parte do aplicativo de funções|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: A Segurança de Dados Avançada deve ser habilitada nos servidores SQL|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: A Segurança de Dados Avançada deve ser habilitada nas instâncias gerenciadas do SQL|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: As notificações por email para administradores e proprietários de assinatura devem ser habilitadas nas configurações da Segurança de Dados Avançada da instância gerenciada do SQL|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: monitora o Endpoint Protection ausente na Central de Segurança do Azure|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: O controle de acesso à rede just-in-time deve ser aplicado em máquinas virtuais|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: Um número de telefone de contato de segurança deve ser fornecido para a sua assinatura|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: Os clusters do Service Fabric só devem usar o Azure Active Directory para autenticação de cliente|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: O aplicativo de API só deve estar acessível via HTTPS|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: Os tipos de Proteção Avançada contra Ameaças devem estar definidos como “Todos” nas configurações de Segurança de Dados Avançada da instância gerenciada de SQL|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: O armazenamento com redundância geográfica deve ser habilitado para Contas de Armazenamento|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: Verificar se a versão do '.NET Framework' é a última, se usada como parte do aplicativo de API|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: As atualizações do sistema nos conjuntos de dimensionamento de máquinas virtuais devem ser instaladas|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: As notificações por email para os administradores e proprietários de assinaturas devem ser habilitadas nas configurações da Segurança de Dados Avançada do SQL Server|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: A depuração remota deve ser desativada para aplicativos Web|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: O backup com redundância geográfica de longo prazo deve ser habilitado para os Bancos de Dados SQL do Azure|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: As vulnerabilidades da configuração de segurança nas máquinas devem ser corrigidas|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: Garantir que a 'Versão do HTTP' seja a última, se usada para executar o aplicativo de funções|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: O MFA deve ser habilitado em contas com permissões de leitura em sua assinatura|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: Os tipos de Proteção Avançada contra Ameaças devem estar definidos como “Todos” nas configurações de Segurança de Dados Avançada do SQL Server|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: As vulnerabilidades nas configurações de segurança do contêiner devem ser corrigidas|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: A depuração remota deve ser desligada para aplicativos de API|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: A avaliação da vulnerabilidade deve ser habilitada nos servidores SQL|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: O agente do Log Analytics deve ser instalado nos Conjuntos de Dimensionamento de Máquinas Virtuais|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: A última versão do TLS deve ser usada no aplicativo Web|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: As contas externas com permissões de proprietário devem ser removidas de sua assinatura|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: A última versão do TLS deve ser usada no aplicativo de funções|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: [Versão prévia]: Os Serviços de Kubernetes devem ser atualizados para uma versão não vulnerável do Kubernetes|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|
|Nível de Impacto 5 do DoD|Atribuição de política|Efeito para a política: As vulnerabilidades nos bancos de dados SQL devem ser corrigidas|Efeito do Azure Policy para essa política. Para obter mais informações sobre efeitos, visite https://aka.ms/policyeffects|

## <a name="next-steps"></a>Próximas etapas

Agora que você examinou as etapas para implantar o exemplo do blueprint do Nível de Impacto 5 do DoD, leia os seguintes artigos para saber mais sobre o blueprint e o mapeamento de controle:

> [!div class="nextstepaction"]
> [Blueprint do Nível de Impacto 5 do DoD – Visão Geral](./index.md)
> [Blueprint do Nível de Impacto 5 do DoD – Mapeamento de controle](./control-mapping.md)

Outros artigos sobre blueprints e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../../how-to/update-existing-assignments.md).