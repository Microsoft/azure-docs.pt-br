---
title: Arquivos de novidades na Central de Segurança do Azure
description: Uma descrição do que há de novo e do que mudou na Central de Segurança do Azure de seis meses atrás e antes.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: a00c11924d2c0f6860c297ab7e58da21da5e1975
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "102634695"
---
# <a name="archive-for-whats-new-in-azure-security-center"></a>Arquivos de novidades na Central de Segurança do Azure?

A página principal [Novidades na Central de Segurança do Azure?](release-notes.md) contém atualizações dos últimos seis meses, enquanto essa página contém itens mais antigos.

Esta página apresenta informações sobre:

- Novos recursos
- Correções de bug
- Funcionalidades preteridas



## <a name="september-2020"></a>Setembro de 2020

As atualizações de setembro incluem:
- [A Central de Segurança ganha uma nova aparência!](#security-center-gets-a-new-look)
- [Lançamento do Azure Defender](#azure-defender-released)
- [Azure Defender para Key Vault em disponibilidade geral](#azure-defender-for-key-vault-is-generally-available)
- [Proteção do Azure Defender para Armazenamento para os Arquivos e o ADLS Gen2 em disponibilidade geral](#azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available)
- [Ferramentas de inventário de ativos em disponibilidade geral](#asset-inventory-tools-are-now-generally-available)
- [Desabilitar uma descoberta de vulnerabilidade específica nas verificações de registros de contêiner e máquinas virtuais](#disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines)
- [Isentar um recurso de uma recomendação](#exempt-a-resource-from-a-recommendation)
- [Os conectores da AWS e do GCP na Central de Segurança trazem uma experiência de várias nuvens](#aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience)
- [Pacote de recomendações da proteção de cargas de trabalho do Kubernetes](#kubernetes-workload-protection-recommendation-bundle)
- [Disponibilidade das descobertas da avaliação de vulnerabilidades na exportação contínua](#vulnerability-assessment-findings-are-now-available-in-continuous-export)
- [Impedir configurações incorretas de segurança impondo recomendações durante a criação de recursos](#prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources)
- [Aprimoramento das recomendações de grupo de segurança de rede](#network-security-group-recommendations-improved)
- [Reprovação da recomendação da versão prévia do AKS "As Políticas de Segurança de Pods devem ser definidas nos Serviços de Kubernetes"](#deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services)
- [Aprimoramento nas notificações por email da Central de Segurança do Azure](#email-notifications-from-azure-security-center-improved)
- [A classificação de segurança não inclui recomendações de versão prévia](#secure-score-doesnt-include-preview-recommendations)
- [As recomendações agora incluem um indicador de severidade e o intervalo de atualização](#recommendations-now-include-a-severity-indicator-and-the-freshness-interval)


### <a name="security-center-gets-a-new-look"></a>A Central de Segurança ganha uma nova aparência!

Lançamos uma interface do usuário atualizada para as páginas do portal da Central de Segurança. As novas páginas incluem uma nova página de visão geral e painéis para a classificação de segurança, o inventário de ativos e o Azure Defender.

A página de visão geral remodelada agora traz um bloco para acessar os painéis da classificação de segurança, do inventário de ativos e do Azure Defender. Também traz um bloco vinculado ao painel de conformidade regulatória.

Saiba mais sobre a [página de visão geral](overview-page.md).


### <a name="azure-defender-released"></a>Lançamento do Azure Defender

O **Azure Defender** é a PPCTN (plataforma de proteção de cargas de trabalho na nuvem) integrada à Central de Segurança para proteção avançada e inteligente das suas cargas de trabalho híbridas e do Azure. Ele substitui a opção de tipo de preço Standard da Central de Segurança. 

Quando você habilita o Azure Defender na área **Preços e configurações** da Central de Segurança do Azure, os seguintes planos do Defender são todos habilitados simultaneamente e fornecem proteções abrangentes para as camadas de computação, dados e serviço do seu ambiente:

- [Azure Defender para servidores](defender-for-servers-introduction.md)
- [Azure Defender for Serviço de Aplicativo](defender-for-app-service-introduction.md)
- [Azure Defender para Armazenamento](defender-for-storage-introduction.md)
- [Azure Defender para SQL](defender-for-sql-introduction.md)
- [Azure Defender para Key Vault](defender-for-key-vault-introduction.md)
- [Azure Defender para Kubernetes](defender-for-kubernetes-introduction.md)
- [Azure Defender para registros de contêiner](defender-for-container-registries-introduction.md)

Cada um desses planos é explicado separadamente na documentação da Central de Segurança.

Com um painel dedicado, o Azure Defender fornece alertas de segurança e Proteção Avançada contra Ameaças para máquinas virtuais, bancos de dados SQL, contêineres, aplicativos Web, sua rede, entre outros.

[Saiba mais sobre o Azure Defender](azure-defender.md)

### <a name="azure-defender-for-key-vault-is-generally-available"></a>Azure Defender para Key Vault em disponibilidade geral

O Azure Key Vault é um serviço de nuvem que protege segredos e chaves de criptografia, como certificados, cadeias de conexão e senhas. 

O **Azure Defender para Key Vault** fornece proteção avançada e nativa do Azure contra ameaças para o Azure Key Vault, oferecendo uma camada adicional de inteligência de segurança. Por extensão, o Azure Defender para Key Vault está, consequentemente, protegendo muitos dos recursos dependentes das suas contas do Key Vault.

Agora, o plano opcional é GA. Esse recurso estava em versão prévia como "Proteção Avançada contra Ameaças para Azure Key Vault".

Além disso, as páginas do Key Vault no portal do Azure agora incluem uma página **Segurança** dedicada para as recomendações e os alertas da **Central de Segurança**.

Saiba mais em [Azure Defender para Key Vault](defender-for-key-vault-introduction.md).


### <a name="azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available"></a>Proteção do Azure Defender para Armazenamento para os Arquivos e o ADLS Gen2 em disponibilidade geral 

O **Azure Defender para Armazenamento** detecta atividades potencialmente prejudiciais nas contas do Armazenamento do Azure. Seus dados podem ser protegidos independentemente de estarem armazenados como contêineres de blob, compartilhamentos de arquivos ou data lakes.

O suporte para os [Arquivos do Azure](../storage/files/storage-files-introduction.md) e o [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) já está em disponibilidade geral.

Desde 1º de outubro de 2020, começamos a cobrar pela proteção de recursos nesses serviços.

Saiba mais em [Azure Defender para Armazenamento](defender-for-storage-introduction.md).


### <a name="asset-inventory-tools-are-now-generally-available"></a>Ferramentas de inventário de ativos em disponibilidade geral

A página de inventário de ativos da Central de Segurança do Azure fornece uma só página para exibição da postura de segurança dos recursos que você conectou à Central de Segurança.

A Central de Segurança analisa periodicamente o estado de segurança dos seus recursos do Azure para identificar possíveis vulnerabilidades na segurança. Em seguida, ela fornece recomendações sobre como corrigir essas vulnerabilidades.

Quando qualquer recurso tiver recomendações pendentes, eles serão exibidos no inventário.

Saiba mais em [Explorar e gerenciar recursos usando um inventário de ativos](asset-inventory.md).



### <a name="disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines"></a>Desabilitar uma descoberta de vulnerabilidade específica nas verificações de registros de contêiner e máquinas virtuais

O Azure Defender inclui verificadores de vulnerabilidade para examinar imagens no seu Registro de Contêiner do Azure e nas suas máquinas virtuais.

Caso você tenha uma necessidade organizacional para ignorar uma descoberta, em vez de corrigi-la, você pode opcionalmente desabilitá-la. As descobertas desabilitadas não afetam sua classificação de segurança nem geram um ruído indesejado.

Quando uma descoberta corresponde aos critérios definidos nas regras de desabilitação, ela não será exibida na lista de descobertas.

Essa opção está disponível nas páginas de detalhes de recomendações para:

- **As vulnerabilidades nas imagens do Registro de Contêiner do Azure devem ser corrigidas**
- **As vulnerabilidades nas suas máquinas virtuais devem ser corrigidas**

Saiba mais em [Desabilitar descobertas específicas nas imagens de contêiner](defender-for-container-registries-usage.md#disable-specific-findings-preview) e [Desabilitar descobertas específicas nas máquinas virtuais](remediate-vulnerability-findings-vm.md#disable-specific-findings-preview).


### <a name="exempt-a-resource-from-a-recommendation"></a>Isentar um recurso de uma recomendação

Ocasionalmente, um recurso será listado como não íntegro em relação a uma recomendação específica (e, portanto, reduzindo sua classificação de segurança) mesmo que você ache que ele não deveria ser. Ele pode ter sido corrigido por um processo não acompanhado pela Central de Segurança. Ou, talvez, sua organização tenha decidido aceitar o risco para esse recurso específico. 

Nesses casos, você pode criar uma regra de isenção e garantir que o recurso não seja listado entre os recursos não íntegros no futuro. Essas regras podem incluir justificações documentadas, conforme descrito abaixo.

Saiba mais em [Isentar um recurso das recomendações e da classificação de segurança](exempt-resource.md).


### <a name="aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience"></a>Os conectores da AWS e do GCP na Central de Segurança trazem uma experiência de várias nuvens

Com as cargas de trabalho de nuvem normalmente abrangendo plataformas de várias nuvens, os serviços de segurança de nuvem precisam fazer o mesmo.

A Central de Segurança do Azure agora protege as cargas de trabalho no Azure, a AWS (Amazon Web Services) e o GCP (Google Cloud Platform).

A integração das suas contas da AWS e do GCP à Central de Segurança integra o AWS Security Hub, o GCP Security Command e a Central de Segurança do Azure. 

Saiba mais em [Conectar suas contas da AWS à Central de Segurança do Azure](quickstart-onboard-aws.md) e [Conectar suas contas do GCP à Central de Segurança do Azure](quickstart-onboard-gcp.md).


### <a name="kubernetes-workload-protection-recommendation-bundle"></a>Pacote de recomendações da proteção de cargas de trabalho do Kubernetes

Para garantir que as cargas de trabalho do Kubernetes sejam seguras por padrão, a Central de Segurança está adicionando recomendações de proteção no nível do Kubernetes, incluindo opções de imposição com o controle de admissão do Kubernetes.

Quando você instalar o complemento do Azure Policy para Kubernetes no seu cluster do AKS, todas as solicitações para o servidor de API do Kubernetes serão monitoradas em relação ao conjunto predefinido de melhores práticas antes de serem persistidas no cluster. Em seguida, você pode configurá-lo para impor as melhores práticas e exigir o uso dele em cargas de trabalho futuras.

Por exemplo, você poderá proibir a criação de contêineres privilegiados, e todas as futuras solicitações para fazer isso serão bloqueadas.

Saiba mais em [Melhores práticas de proteção de cargas de trabalho usando o controle de admissão do Kubernetes](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control).


### <a name="vulnerability-assessment-findings-are-now-available-in-continuous-export"></a>Disponibilidade das descobertas da avaliação de vulnerabilidades na exportação contínua

Use a exportação contínua para transmitir alertas e recomendações em tempo real para os Hubs de Eventos do Azure, os workspaces do Log Analytics ou o Azure Monitor. Neles, você pode integrar esses dados aos SIEMs (como o Azure Sentinel, o Power BI, o Azure Data Explorer, entre outros).

As ferramentas integradas de avaliação de vulnerabilidades da Central de Segurança retornam descobertas sobre seus recursos como recomendações práticas em uma recomendação "pai", por exemplo, "As vulnerabilidades nas suas máquinas virtuais devem ser corrigidas". 

As descobertas de segurança já estão disponíveis para exportação por meio da exportação contínua quando você seleciona as recomendações e habilita a opção **Incluir descobertas de segurança**.

:::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Incluir a alternância de descobertas de segurança na configuração de exportação contínua" :::

Páginas relacionadas:

- [Solução de avaliação de vulnerabilidades integrada da Central de Segurança para máquinas virtuais do Azure](deploy-vulnerability-assessment-vm.md)
- [Solução de avaliação de vulnerabilidades integrada da Central de Segurança para imagens do Registro de Contêiner do Azure](defender-for-container-registries-usage.md)
- [Exportação contínua](continuous-export.md)

### <a name="prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources"></a>Impedir configurações incorretas de segurança impondo recomendações durante a criação de recursos

Configurações incorretas de segurança são uma das principais causas de incidentes de segurança. Agora a Central de Segurança pode ajudar a *impedir* configurações incorretas de novos recursos, com relação a recomendações específicas. 

Esse recurso pode ajudar a manter suas cargas de trabalho seguras e estabilizar sua classificação de segurança.

A imposição de uma configuração segura, com base em uma recomendação específica, é oferecida em dois modos:

- Usando o efeito **Negar** do Azure Policy, você pode interromper a criação de recursos não íntegros

- Usando a opção **Impor**, você pode aproveitar o efeito **DeployIfNotExist** da política do Azure e corrigir automaticamente os recursos fora de conformidade após a criação
 
Isso está disponível para as recomendações de segurança selecionadas e pode ser encontrado na parte superior da página de detalhes do recurso.

Saiba mais em [Impedir configurações incorretas com as recomendações de Impor/Negar](prevent-misconfigurations.md).

###  <a name="network-security-group-recommendations-improved"></a>Aprimoramento das recomendações de grupo de segurança de rede

As recomendações de segurança a seguir relacionadas aos grupos de segurança de rede foram aprimoradas para reduzir algumas instâncias de falsos positivos.

- Todas as portas de rede devem ser restritas no NSG associado à sua VM
- Portas de gerenciamento devem ser fechadas nas máquinas virtuais
- As máquinas virtuais para a Internet devem ser protegidas com Grupos de Segurança de Rede
- As sub-redes devem ser associadas a um Grupo de Segurança de Rede


### <a name="deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services"></a>Reprovação da recomendação da versão prévia do AKS "As Políticas de Segurança de Pods devem ser definidas nos Serviços de Kubernetes"

A recomendação de versão prévia "As Políticas de Segurança de Pods devem ser definidas nos Serviços de Kubernetes" está sendo preterida, conforme descrito na documentação do [Serviço de Kubernetes do Azure](../aks/use-pod-security-policies.md).

O recurso da política de segurança de pods (versão prévia) foi configurado para ser substituído e não está mais disponível desde 15 de outubro de 2020 a fim de beneficiar o Azure Policy para AKS.

Depois que a política de segurança de pods (versão prévia) for preterida, você precisará desabilitar o recurso em todos os clusters existentes usando o recurso preterido para realizar futuras atualizações de cluster e permanecer dentro do suporte do Azure.


### <a name="email-notifications-from-azure-security-center-improved"></a>Aprimoramento nas notificações por email da Central de Segurança do Azure

As seguintes áreas dos emails relacionadas a alertas de segurança foram aprimoradas: 

- Adição da capacidade de enviar notificações por email sobre alertas para todos os níveis de severidade
- Adição da capacidade de notificar os usuários com diferentes funções do Azure na assinatura
- Por padrão, estamos notificando de maneira proativa os proprietários da assinatura em alertas de alta severidade (que têm uma alta probabilidade de serem violações autênticas)
- Removemos o campo de número de telefone da página de configuração das notificações de email

Saiba mais em [Configurar notificações por email para alertas de segurança](security-center-provide-security-contact-details.md).


### <a name="secure-score-doesnt-include-preview-recommendations"></a>A classificação de segurança não inclui recomendações de versão prévia 

A Central de Segurança avalia continuamente seus recursos, suas assinaturas e a organização em busca de problemas de segurança. Em seguida, ele agrega todas as conclusões em uma única pontuação para que você possa ver, rapidamente, sua situação de segurança atual: quanto maior a pontuação, menor o nível de risco identificado.

À medida que novas ameaças são descobertas, novas orientações de segurança são disponibilizadas na Central de Segurança por meio de novas recomendações. Para evitar alterações de surpresa na sua classificação de segurança e fornecer um período de cortesia no qual você possa explorar novas recomendações antes que elas afetem suas pontuações, as recomendações sinalizadas como **Versão prévia** não serão mais incluídas nos cálculos da sua classificação de segurança. Elas ainda deverão ser corrigidas sempre que possível, para que, quando o período de versão prévia terminar, elas contribuam para a sua classificação.

Além disso, as recomendações de **Versão prévia** não renderizam um recurso "Não íntegro".

Um exemplo de recomendação de versão prévia:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Recomendação com o sinalizador de versão prévia":::

[Saiba mais sobre a classificação de segurança](secure-score-security-controls.md).


### <a name="recommendations-now-include-a-severity-indicator-and-the-freshness-interval"></a>As recomendações agora incluem um indicador de severidade e o intervalo de atualização

A página de detalhes de recomendações agora inclui um indicador de intervalo de atualização (quando relevante) e uma exibição clara da severidade da recomendação.

:::image type="content" source="./media/release-notes/recommendations-severity-freshness-indicators.png" alt-text="Página de recomendações mostrando a atualização e a severidade":::


## <a name="august-2020"></a>Agosto de 2020

As atualizações de agosto incluem:

- [Inventário de ativos: nova exibição avançada da postura de segurança dos ativos](#asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets)
- [Adição de suporte para padrões de segurança do Azure Active Directory (para autenticação multifator)](#added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication)
- [Adição da recomendação de entidades de serviço](#service-principals-recommendation-added)
- [Avaliação de vulnerabilidades em VMs – recomendações e políticas consolidadas](#vulnerability-assessment-on-vms---recommendations-and-policies-consolidated)
- [Adição de novas políticas de segurança do AKS à iniciativa ASC_default – para uso somente dos clientes da versão prévia privada](#new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only)


### <a name="asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets"></a>Inventário de ativos: nova exibição avançada da postura de segurança dos ativos

O inventário de ativos da Central de Segurança (atualmente em versão prévia) fornece uma forma de exibir a postura de segurança dos recursos que você conectou à Central de Segurança.

A Central de Segurança analisa periodicamente o estado de segurança dos seus recursos do Azure para identificar possíveis vulnerabilidades na segurança. Em seguida, ela fornece recomendações sobre como corrigir essas vulnerabilidades. Quando qualquer recurso tiver recomendações pendentes, eles serão exibidos no inventário.

Use a exibição e os respectivos filtros para explorar seus dados de postura de segurança e realizar ações adicionais com base nas descobertas.

Saiba mais sobre o [inventário de ativos](asset-inventory.md).


### <a name="added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication"></a>Adição de suporte para padrões de segurança do Azure Active Directory (para autenticação multifator)

A Central de Segurança adicionou suporte completo para os [padrões de segurança](../active-directory/fundamentals/concept-fundamentals-security-defaults.md), as proteções gratuitas de segurança de identidade da Microsoft.

Os padrões de segurança fornecem configurações de segurança de identidade pré-configuradas para defender sua organização contra ataques comuns relacionados à identidade. Os padrões de segurança já estão protegendo mais de 5 milhões de locatários em geral; 50 mil locatários também estão protegidos pela Central de Segurança.

Agora, a Central de Segurança fornece uma recomendação de segurança sempre que identifica uma assinatura do Azure sem os padrões de segurança habilitados. Até agora, a Central de Segurança recomenda habilitar a autenticação multifator usando o acesso condicional, que faz parte da licença Premium do Azure AD (Active Directory). Para os clientes que usam o Azure AD gratuito, agora recomendamos habilitar os padrões de segurança. 

Nossa meta é encorajar mais clientes a proteger ambientes de nuvem com a MFA e atenuar um dos maiores riscos que também é o mais impactante para a sua [classificação de segurança](secure-score-security-controls.md).

Saiba mais sobre segurança os [padrões de segurança](../active-directory/fundamentals/concept-fundamentals-security-defaults.md).


### <a name="service-principals-recommendation-added"></a>Adição da recomendação de entidades de serviço

Uma nova recomendação foi adicionada a fim de aconselhar os clientes da Central de Segurança que usam certificados de gerenciamento a gerenciar as assinaturas alternem para as entidades de serviço.

A recomendação **As entidades de serviço devem ser usadas para proteger suas assinaturas em vez dos certificados de gerenciamento** aconselha você a usar as entidades de serviço ou o Azure Resource Manager para gerenciar suas assinaturas com mais segurança. 

Saiba mais sobre os [Objetos de entidade de serviço e aplicativo no Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object).


### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>Avaliação de vulnerabilidades em VMs – recomendações e políticas consolidadas

A Central de Segurança inspeciona suas VMs para detectar se estão executando uma solução de avaliação de vulnerabilidades. Se nenhuma solução de avaliação de vulnerabilidades for encontrada, a Central de Segurança fornecerá uma recomendação para simplificar a implantação.

Quando as vulnerabilidades são encontradas, a Central de Segurança fornece uma recomendação resumindo as descobertas para você investigar e corrigir, conforme necessário.

Para garantir uma experiência consistente para todos os usuários, independentemente do tipo de verificador que estão usando, unificamos quatro recomendações nas duas seguintes:

|Recomendação unificada|Descrição das alterações|
|----|:----|
|**Uma solução de avaliação de vulnerabilidade deve ser habilitada nas máquinas virtuais**|Substitui as duas seguintes recomendações:<br> **•** Habilitar a solução interna de avaliação de vulnerabilidades em máquinas virtuais (ativada pela Qualys) (agora preterida) (incluída na camada Standard)<br> **•** A solução de avaliação de vulnerabilidades deve ser instalada nas máquinas virtuais (agora preterida) (incluída nas camadas Standard e Gratuita)|
|**As vulnerabilidades nas suas máquinas virtuais devem ser corrigidas**|Substitui as duas seguintes recomendações:<br>**•** Corrija as vulnerabilidades encontradas nas máquinas virtuais (ativada pela Qualys) (agora preterida)<br>**•** As vulnerabilidades devem ser corrigidas por uma solução de avaliação de vulnerabilidades (agora preterida)|
|||

Agora, você usará a mesma recomendação para implantar a extensão de avaliação de vulnerabilidades da Central de Segurança ou uma solução de licença privada ("BYOL") de um parceiro, como a Qualys ou a Rapid7.

Além disso, quando as vulnerabilidades forem encontradas e relatadas à Central de Segurança, uma só recomendação alertará você sobre as descobertas, independentemente da solução de avaliação de vulnerabilidades que as identificou.

#### <a name="updating-dependencies"></a>Como atualizar dependências

Se você tiver scripts, consultas ou automações referentes às recomendações ou aos nomes/às chaves de política anteriores, use as tabelas abaixo para atualizar as referências:

##### <a name="before-august-2020"></a>Antes de agosto de 2020

|Recomendação|Escopo|
|----|:----|
|**Habilitar a solução interna de avaliação de vulnerabilidades nas máquinas virtuais (ativada pela Qualys)**<br>Chave: 550e890b-e652-4d22-8274-60b3bdb24c63|Interno|
|**Corrija as vulnerabilidades encontradas em suas máquinas virtuais (da plataforma Qualys)**<br>Chave: 1195afff-c881-495e-9bc5-1486211ae03f|Interno|
|**A solução de avaliação de vulnerabilidades deve ser instalada nas suas máquinas virtuais**<br>Chave: 01b1ed4c-b733-4fee-b145-f23236e70cf3|BYOL|
|**As vulnerabilidades devem ser corrigidas por uma solução de Avaliação de Vulnerabilidades**<br>Chave: 71992a2a-d168-42e0-b10e-6b45fa2ecddb|BYOL|
||||


|Política|Escopo|
|----|:----|
|**A avaliação de vulnerabilidades deve estar habilitada nas máquinas virtuais**<br>ID da política: 501541f7-f7e7-4cd6-868c-4190fdad3ac9|Interno|
|**As vulnerabilidades devem ser corrigidas por uma solução de avaliação de vulnerabilidades**<br>ID da política: 760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
||||


##### <a name="from-august-2020"></a>A partir de agosto de 2020

|Recomendação|Escopo|
|----|:----|
|**Uma solução de avaliação de vulnerabilidade deve ser habilitada nas máquinas virtuais**<br>Chave: ffff0522-1e88-47fc-8382-2a80ba848f5d|Interno + BYOL|
|**As vulnerabilidades nas suas máquinas virtuais devem ser corrigidas**<br>Chave: 1195afff-c881-495e-9bc5-1486211ae03f|Interno + BYOL|
||||

|Política|Escopo|
|----|:----|
|[**A avaliação de vulnerabilidades deve estar habilitada nas máquinas virtuais**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>ID da política: 501541f7-f7e7-4cd6-868c-4190fdad3ac9 |Interno + BYOL|
||||


### <a name="new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only"></a>Adição de novas políticas de segurança do AKS à iniciativa ASC_default – para uso somente dos clientes da versão prévia privada

Para garantir que as cargas de trabalho do Kubernetes sejam seguras por padrão, a Central de Segurança está adicionando recomendações de proteção e políticas no nível do Kubernetes, incluindo opções de imposição com o controle de admissão do Kubernetes.

A fase inicial deste projeto inclui uma versão prévia privada e a adição de novas políticas (desabilitadas por padrão) à iniciativa ASC_default.

Você poderá ignorar essas políticas com segurança e não haverá nenhum impacto no seu ambiente. Se você quiser habilitá-las, inscreva-se na versão prévia em https://aka.ms/SecurityPrP e escolha uma das seguintes opções:

1. **Versão prévia única**: para ingressar somente nessa versão prévia privada. Mencione explicitamente “Verificação contínua do ASC” como a versão prévia na qual deseja ingressar.
1. **Programas em andamento** – para ser adicionado a esta e às futuras versões prévias privadas. Você precisará preencher um perfil e assinar um contrato de privacidade.


## <a name="july-2020"></a>Julho de 2020

As atualizações de julho incluem:
- [A avaliação de vulnerabilidades para máquinas virtuais já está disponível para imagens que não são do marketplace](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [Expansão da proteção contra ameaças para o Armazenamento do Azure para incluir os Arquivos do Azure e o Azure Data Lake Storage Gen2 (versão prévia)](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [Oito novas recomendações para habilitar os recursos de proteção contra ameaças](#eight-new-recommendations-to-enable-threat-protection-features)
- [Aprimoramentos de segurança do contêiner – verificação mais rápida de registro e atualização da documentação](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [Atualização de controles de aplicativos adaptáveis com uma nova recomendação e suporte para curingas nas regras de caminho](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [Seis políticas para a reprovação da Segurança de Dados Avançada do SQL](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>A avaliação de vulnerabilidades para máquinas virtuais já está disponível para imagens que não são do marketplace

Ao implantar uma solução de avaliação de vulnerabilidades, a Central de Segurança realizou anteriormente uma verificação de validação antes da implantação. O objetivo da verificação era confirmar um SKU do marketplace da máquina virtual de destino. 

Desta atualização em diante, a verificação foi removida e agora você pode implantar ferramentas de avaliação de vulnerabilidades em computadores Windows e Linux 'personalizados'. As imagens personalizadas são aquelas que você modificou dos padrões do marketplace.

Embora você possa implantar a extensão integrada de avaliação de vulnerabilidades (ativada pela Qualys) em muitos outros computadores, o suporte só estará disponível se você estiver usando um sistema operacional listado em [Implantar o verificador de vulnerabilidades integrado em VMs da camada Standard](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines)

Saiba mais sobre o [verificador de vulnerabilidades integrado para máquinas virtuais (exige o Azure Defender)](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).

Saiba mais sobre como usar sua solução de avaliação de vulnerabilidades de licença privada da Qualys ou da Rapid7 em [Como implantar uma solução de verificação de vulnerabilidades do parceiro](deploy-vulnerability-assessment-vm.md).


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Expansão da proteção contra ameaças para o Armazenamento do Azure para incluir os Arquivos do Azure e o Azure Data Lake Storage Gen2 (versão prévia)

A proteção contra ameaças para o Armazenamento do Azure detecta atividades potencialmente prejudiciais nas suas contas do Armazenamento do Azure. A Central de Segurança exibe alertas quando detecta tentativas de acessar ou explorar suas contas de armazenamento. 

Seus dados podem ser protegidos independentemente de estarem armazenados como contêineres de blob, compartilhamentos de arquivos ou data lakes.




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>Oito novas recomendações para habilitar os recursos de proteção contra ameaças

Oito novas recomendações foram adicionadas para fornecer uma forma simples de habilitar os recursos de proteção contra ameaças da Central de Segurança do Azure para os seguintes tipos de recursos: máquinas virtuais, Planos do Serviço de Aplicativo, servidores do Banco de Dados SQL do Azure, SQL Servers em computadores, contas do Armazenamento do Azure, clusters do Serviço de Kubernetes do Azure, registros do Registro de Contêiner do Azure e cofres do Azure Key Vault.

As novas recomendações são:

- **A Segurança de Dados Avançada deve ser habilitada nos servidores do Banco de Dados SQL do Azure**
- **A Segurança de Dados Avançada deve ser habilitada nos servidores SQL em computadores**
- **A Proteção Avançada contra Ameaças deve ser habilitada nos planos do Serviço de Aplicativo do Azure**
- **A Proteção Avançada contra Ameaças deve ser habilitada nos registros do Registro de Contêiner do Azure**
- **A Proteção Avançada contra Ameaças deve ser habilitada nos cofres do Azure Key Vault**
- **A Proteção Avançada contra Ameaças deve ser habilitada nos clusters do Serviço de Kubernetes do Azure**
- **A Proteção Avançada contra Ameaças deve ser habilitada em contas de Armazenamento do Azure**
- **A Proteção Avançada contra Ameaças deve estar habilitada nas máquinas virtuais**

Essas novas recomendações pertencem ao controle de segurança **Habilitar o Azure Defender**.

As recomendações também incluem a funcionalidade de correção rápida. 

> [!IMPORTANT]
> A correção de uma dessas recomendações resultará em custos para proteger os recursos relevantes. Esses custos serão iniciados imediatamente se você tiver recursos relacionados na assinatura atual. Ou, no futuro, se você adicioná-los em uma data posterior.
> 
> Por exemplo, se você não tiver nenhum cluster do Serviço de Kubernetes do Azure na sua assinatura e habilitar a proteção contra ameaças, nenhum custo será cobrado. Se, no futuro, você adicionar um cluster na mesma assinatura, ela será automaticamente protegida e os custos serão iniciados nesse momento.

Saiba mais sobre cada um deles na [página de referência de recomendações de segurança](recommendations-reference.md).

Saiba mais sobre a [proteção contra ameaças na Central de Segurança do Azure](azure-defender.md).




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>Aprimoramentos de segurança do contêiner – verificação mais rápida de registro e atualização da documentação

Como parte dos investimentos contínuos no domínio de segurança do contêiner, temos o prazer em compartilhar um aprimoramento significativo de desempenho nas verificações dinâmicas da Central de Segurança de imagens de contêiner armazenadas no Registro de Contêiner do Azure. Agora, as verificações normalmente são concluídas em aproximadamente dois minutos. Em alguns casos, elas podem levar até 15 minutos.

Para aprimorar a clareza e as diretrizes sobre as funcionalidades de segurança do contêiner da Central de Segurança do Azure, também atualizamos as páginas de documentação de segurança do contêiner. 

Saiba mais sobre a segurança de contêiner da Central de Segurança nos seguintes artigos:

- [Visão geral de recursos de segurança de contêiner da Central de Segurança](container-security.md)
- [Detalhes da integração ao Registro de Contêiner do Azure](defender-for-container-registries-introduction.md)
- [Detalhes da integração ao Serviço de Kubernetes do Azure](defender-for-kubernetes-introduction.md)
- [Como verificar os registros e proteger os hosts do Docker](container-security.md)
- [Alertas de segurança dos recursos de proteção contra ameaças para clusters do Serviço de Kubernetes do Azure](alerts-reference.md#alerts-akscluster)
- [Alertas de segurança dos recursos de proteção contra ameaças para hosts do Serviço de Kubernetes do Azure](alerts-reference.md#alerts-containerhost)
- [Recomendações de segurança para contêineres](recommendations-reference.md#recs-compute)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>Atualização de controles de aplicativos adaptáveis com uma nova recomendação e suporte para curingas nas regras de caminho

O recurso de controles de aplicativos adaptáveis recebeu duas atualizações significativas:

* Uma nova recomendação identifica o comportamento potencialmente legítimo que não era permitido antes. A nova recomendação, **As regras de lista de permissões na sua política de controle de aplicativos adaptáveis deve ser atualizada**, solicita que você adicione novas regras à política existente para reduzir o número de falsos positivos em alertas de violação de controles de aplicativos adaptáveis.

* Agora, as regras de caminho dão suporte a curingas. A partir dessa atualização, você pode configurar as regras de caminho permitidas usando curingas. Há dois cenários compatíveis:

    * Usando um curinga no final de um caminho para permitir todos os executáveis dentro desta pasta e das subpastas

    * Usando um curinga no meio de um caminho para habilitar um nome executável conhecido com um nome de pasta alterado (por exemplo, pastas de usuário pessoais com um executável conhecido, nomes de pasta gerados automaticamente etc.).


[Saiba mais sobre controles de aplicativo adaptáveis](security-center-adaptive-application.md).



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>Seis políticas para a reprovação da Segurança de Dados Avançada do SQL

Seis políticas relacionadas à Segurança de Dados Avançada para computadores SQL estão sendo preteridas:

- Os tipos de Proteção Avançada contra Ameaças devem estar definidos como 'Todos' nas configurações da Segurança de Dados Avançada da Instância Gerenciada de SQL
- Os tipos de Proteção Avançada contra Ameaças devem estar definidos como 'Todos' nas configurações da Segurança de Dados Avançada do SQL Server
- As configurações da Segurança de Dados Avançada para a instância gerenciada do SQL devem conter um endereço de email para receber alertas de segurança
- As configurações da Segurança de Dados Avançada para o SQL Server devem conter um endereço de email para receber alertas de segurança
- As notificações por email para administradores e proprietários de assinatura devem ser habilitadas nas configurações da Segurança de Dados Avançada da instância gerenciada do SQL
- As notificações por email para os administradores e proprietários de assinaturas devem ser habilitadas nas configurações da Segurança de Dados Avançada do SQL Server

Saiba mais sobre as [políticas internas](./policy-reference.md).



## <a name="june-2020"></a>Junho de 2020

As atualizações de junho incluem:

- [API de classificação de segurança (versão prévia)](#secure-score-api-preview)
- [Segurança de dados avançada para máquinas SQL (Azure, outras nuvens e locais) (visualização)](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview)
- [Duas novas recomendações para implantar o agente do Log Analytics em computadores do Azure Arc (versão prévia)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [Novas políticas para criar configurações de exportação contínua e automação de fluxo de trabalho em escala](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [Nova recomendação de uso de NSGs para proteger máquinas virtuais que não são voltadas para a Internet](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [Novas políticas para habilitar a proteção contra ameaças e a Segurança de Dados Avançada](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>API de classificação de segurança (versão prévia)

Agora você pode acessar sua classificação por meio da [API de classificação de segurança](/rest/api/securitycenter/securescores/) (atualmente em versão prévia). Os métodos de API oferecem a flexibilidade para consultar os dados e criar seu mecanismo de relatório das suas classificações de segurança ao longo do tempo. Por exemplo, você pode usar a API **Classificações de Segurança** para obter a classificação de uma assinatura específica. Além disso, você pode usar a API **Controles de Classificação de Segurança** para listar os controles de segurança e a classificação atual das suas assinaturas.

Para obter exemplos de ferramentas externas possibilitadas com a API de classificação de segurança, confira [a área de classificação de segurança da nossa comunidade do GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score).

Saiba mais sobre os [controles de segurança e classificação de segurança na Central de Segurança do Azure](secure-score-security-controls.md).



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview"></a>Segurança de dados avançada para máquinas SQL (Azure, outras nuvens e locais) (visualização)

A Segurança de Dados Avançada da Central de Segurança do Azure para computadores SQL agora protege os SQL Servers hospedados no Azure, em outros ambientes de nuvem e, até mesmo, em computadores locais. Isso estende as proteções para que os SQL Servers nativos do Azure deem suporte completo a ambientes híbridos.

A Segurança de Dados Avançada fornece avaliação de vulnerabilidades e Proteção Avançada contra Ameaças para computadores SQL onde quer que estejam.

A configuração envolve duas etapas:

1. Implantar o agente do Log Analytics no computador host do SQL Server para fornecer a conexão à conta do Azure.

1. Habilitar o pacote opcional na página de preços e configurações da Central de Segurança.

Saiba mais sobre a [Segurança de Dados Avançada para computadores SQL](defender-for-sql-usage.md).



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>Duas novas recomendações para implantar o agente do Log Analytics em computadores do Azure Arc (versão prévia)

Duas novas recomendações foram adicionadas para ajudar a implantar o [agente do Log Analytics](../azure-monitor/agents/log-analytics-agent.md) nos computadores do Azure Arc e garantir que eles estejam protegidos pela Central de Segurança do Azure:

- **O agente do Log Analytics deve ser instalado nos computadores do Azure Arc baseados no Windows (versão prévia)**
- **O agente do Log Analytics deve ser instalado nos computadores do Azure Arc baseados em Linux (versão prévia)**

Essas novas recomendações serão exibidas nos mesmos quatro controles de segurança da recomendação existente (relacionada), **O agente de monitoramento deve ser instalado nos computadores**: corrigir as configurações de segurança, aplicar o controle de aplicativo adaptável, aplicar atualizações do sistema e habilitar a proteção de ponto de extremidade.

As recomendações também incluem a capacidade de correção rápida para ajudar a acelerar o processo de implantação. 

Saiba mais sobre essas duas novas recomendações na tabela [Recomendações de computação e aplicativo](recommendations-reference.md#recs-compute).

Saiba mais sobre como a Central de Segurança do Azure usa o agente em [O que é o agente do Log Analytics?](faq-data-collection-agents.md#what-is-the-log-analytics-agent).

Saiba mais sobre as [extensões para computadores do Azure Arc](../azure-arc/servers/manage-vm-extensions.md).


### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>Novas políticas para criar configurações de exportação contínua e automação de fluxo de trabalho em escala

A automatização dos processos de monitoramento e resposta a incidentes da sua organização pode aprimorar significativamente o tempo necessário para investigar e atenuar incidentes de segurança.

Para implantar suas configurações de automação na sua organização, use essas políticas internas do Azure 'DeployIfdNotExist' para criar e configurar procedimentos de [exportação contínua](continuous-export.md) e [automação de fluxo de trabalho](workflow-automation.md):

As políticas podem ser encontradas na política do Azure:


|Goal  |Política  |ID da Política  |
|---------|---------|---------|
|Exportação contínua para o hub de eventos|[Implantar a exportação para o Hub de Eventos para os alertas e as recomendações Central de Segurança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|Exportação contínua para o workspace do Log Analytics|[Implantar a exportação para o workspace do Log Analytics para os alertas e as recomendações da Central de Segurança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|Automação de fluxo de trabalho para alertas de segurança|[Implantar a Automação de Fluxo de Trabalho para alertas da Central de Segurança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Automação de fluxo de trabalho para recomendações de segurança|[Implantar a Automação de Fluxo de Trabalho para recomendações da Central de Segurança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

Introdução aos [modelos de automação de fluxo de trabalho](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

Saiba mais sobre como usar as duas políticas de exportação em [Configurar a automação de fluxo de trabalho em escala usando as políticas fornecidas](workflow-automation.md#configure-workflow-automation-at-scale-using-the-supplied-policies) e [Configurar uma exportação contínua](continuous-export.md#set-up-a-continuous-export).


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>Nova recomendação de uso de NSGs para proteger máquinas virtuais que não são voltadas para a Internet

O controle de segurança "Implementar melhores práticas de segurança" agora inclui a seguinte nova recomendação:

- **Máquinas virtuais não voltadas para a Internet devem ser protegidas com grupos de segurança de rede**

Uma recomendação existente, **As máquinas virtuais voltadas para a Internet devem ser protegidas com grupos de segurança de rede**, não distingue entre as VMs voltadas para a Internet e aquelas não voltadas para a Internet. Para ambas, uma recomendação de alta severidade foi gerada se uma VM não foi atribuída a um grupo de segurança de rede. Essa nova recomendação separa os computadores não voltados para a Internet para reduzir os falsos positivos e evitar alertas de alta severidade desnecessários.

Saiba mais na tabela [Recomendações de rede](recommendations-reference.md#recs-networking).




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>Novas políticas para habilitar a proteção contra ameaças e a Segurança de Dados Avançada

As novas políticas abaixo foram adicionadas à iniciativa Padrão ASC e foram projetadas para ajudar a habilitar a proteção contra ameaças ou a Segurança de Dados Avançada para os tipos de recursos relevantes.

As políticas podem ser encontradas na política do Azure:


| Política                                                                                                                                                                                                                                                                | ID da Política                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| [A Segurança de Dados Avançada deve ser habilitada nos servidores do Banco de Dados SQL do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7fe3b40f-802b-4cdd-8bd4-fd799c948cc2)     | 7fe3b40f-802b-4cdd-8bd4-fd799c948cc2 |
| [A Segurança de Dados Avançada deve ser habilitada nos servidores SQL em computadores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6581d072-105e-4418-827f-bd446d56421b) | 6581d072-105e-4418-827f-bd446d56421b |
| [A Proteção Avançada contra Ameaças deve ser habilitada em contas de Armazenamento do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f308fbb08-4ab8-4e67-9b29-592e93fb94fa)           | 308fbb08-4ab8-4e67-9b29-592e93fb94fa |
| [A Proteção Avançada contra Ameaças deve ser habilitada nos cofres do Azure Key Vault](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)           | 0e6763cc-5078-4e64-889d-ff4d9a839047 |
| [A Proteção Avançada contra Ameaças deve ser habilitada nos planos do Serviço de Aplicativo do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2913021d-f2fd-4f3d-b958-22354e2bdbcb)                | 2913021d-f2fd-4f3d-b958-22354e2bdbcb |
| [A Proteção Avançada contra Ameaças deve ser habilitada nos registros do Registro de Contêiner do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4)   | c25d9a16-bc35-4e15-a7e5-9db606bf9ed4 |
| [A Proteção Avançada contra Ameaças deve ser habilitada nos clusters do Serviço de Kubernetes do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f523b5cd1-3e23-492f-a539-13118b6d1e3a)   | 523b5cd1-3e23-492f-a539-13118b6d1e3a |
| [A proteção avançada contra ameaças deve ser habilitada nas Máquinas Virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da35fc9-c9e7-4960-aec9-797fe7d9051d)           | 4da35fc9-c9e7-4960-aec9-797fe7d9051d |
|                                                                                                                                                                                                                                                                       |                                      |

Saiba mais sobre a [Proteção contra ameaças na Central de Segurança do Azure](azure-defender.md).


## <a name="may-2020"></a>Maio de 2020

As atualizações de maio incluem:
- [Regras de supressão de alertas (versão prévia)](#alert-suppression-rules-preview)
- [A avaliação de vulnerabilidades de máquinas virtuais já está em disponibilidade geral](#virtual-machine-vulnerability-assessment-is-now-generally-available)
- [Alterações no acesso à VM (máquina virtual) JIT (Just-In-Time)](#changes-to-just-in-time-jit-virtual-machine-vm-access)
- [As recomendações personalizadas foram movidas para um controle de segurança separado](#custom-recommendations-have-been-moved-to-a-separate-security-control)
- [Adicionada alternância para exibir recomendações em controles ou como uma lista simples](#toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list)
- [Controle de segurança expandido "Implementar melhores práticas de segurança"](#expanded-security-control-implement-security-best-practices)
- [Políticas personalizadas com metadados personalizados agora estão em disponibilidade geral](#custom-policies-with-custom-metadata-are-now-generally-available)
- [Migração das funcionalidades de análise de despejo de memória para a detecção de ataque sem arquivos](#crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection)


### <a name="alert-suppression-rules-preview"></a>Regras de supressão de alertas (versão prévia)

Esse novo recurso (atualmente em versão prévia) ajuda a reduzir a fadiga causada por alertas. Use regras para ocultar automaticamente os alertas que são conhecidos como inócuos ou relacionados a atividades normais em sua organização. Isso permite que você se concentre nas ameaças mais relevantes. 

Os alertas que corresponderem às regras de supressão habilitadas ainda serão gerados, mas o estado deles será definido como ignorado. Você poderá ver o estado no portal do Azure ou de qualquer outra maneira que você acessar os alertas de segurança da Central de Segurança.

As regras de supressão definem os critérios para os quais os alertas devem ser automaticamente ignorados. Normalmente, você usaria uma regra de supressão para:

- suprimir alertas que você identificou como falsos positivos

- suprimir alertas que estão sendo disparados com frequência demais para serem úteis

Saiba mais sobre a [supressão de alertas da Proteção contra Ameaças da Central de Segurança do Azure](alerts-suppression-rules.md).


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>A avaliação de vulnerabilidades de máquinas virtuais já está em disponibilidade geral

A camada Standard da Central de Segurança agora inclui uma avaliação de vulnerabilidade integrada para máquinas virtuais sem cobrança de nenhum valor adicional. Essa extensão é da plataforma Qualys, mas relata as descobertas dela diretamente para a Central de Segurança. Você não precisa de uma licença do Qualys nem de uma conta do Qualys: tudo é tratado diretamente na Central de Segurança.

A nova solução pode verificar continuamente suas máquinas virtuais para encontrar vulnerabilidades e apresentar as descobertas na Central de Segurança. 

Para implantar a solução, use a nova recomendação de segurança:

"Habilitar a solução de avaliação de vulnerabilidades interna nas máquinas virtuais (da plataforma Qualys)"

Saiba mais sobre [avaliação de vulnerabilidade integrada da Central de Segurança para máquinas virtuais](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Alterações no acesso à VM (máquina virtual) JIT (Just-In-Time)

A Central de Segurança inclui um recurso opcional para proteger as portas de gerenciamento de suas VMs. Isso fornece uma defesa contra a forma mais comum de ataques de força bruta.

Essa atualização traz as seguintes alterações para esse recurso:

- A recomendação que aconselha você a habilitar o JIT em uma VM foi renomeada. O que anteriormente era "O controle de acesso à rede Just-In-Time deve ser aplicado em máquinas virtuais" agora é: "As portas de gerenciamento de máquinas virtuais devem ser protegidas com o controle de acesso à rede Just-In-Time".

- A recomendação é disparada somente se há portas de gerenciamento abertas.

Saiba mais sobre o [recurso de acesso JIT](security-center-just-in-time.md).


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>As recomendações personalizadas foram movidas para um controle de segurança separado

Um dos controles de segurança introduzidos com a classificação de segurança aprimorada foi "Implementar melhores práticas de segurança". Todas as recomendações personalizadas criadas para suas assinaturas foram colocadas automaticamente nesse controle. 

Para facilitar a localização de suas recomendações personalizadas, nós as movemos para um controle de segurança dedicado, "Recomendações personalizadas". Este controle não tem impacto sobre sua classificação de segurança.

Saiba mais sobre os controles de segurança em [Classificação de segurança aprimorada (versão prévia) na Central de Segurança do Azure](secure-score-security-controls.md).


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>Adicionada alternância para exibir recomendações em controles ou como uma lista simples

Os controles de segurança são grupos lógicos de recomendações de segurança relacionadas. Eles refletem suas superfícies de ataque vulneráveis. Um controle é um conjunto de recomendações de segurança, com instruções que ajudam a implementar essas recomendações.

Para ver imediatamente como sua organização está protegendo cada superfície de ataque individual, examine as pontuações de cada controle de segurança.

Por padrão, suas recomendações são mostradas nos controles de segurança. Nesta atualização em diante, você também pode vê-las como uma lista. Para vê-las como uma lista simples classificada pelo status de integridade dos recursos afetados, use a nova alternância 'Agrupar por controles'. A alternância está acima da lista no portal.

Os controles de segurança (e essa alternância) são parte da nova experiência de classificação de segurança. Lembre-se de enviar seus comentários de dentro do portal.

Saiba mais sobre os controles de segurança em [Classificação de segurança aprimorada (versão prévia) na Central de Segurança do Azure](secure-score-security-controls.md).

:::image type="content" source="./media/secure-score-security-controls/recommendations-group-by-toggle.gif" alt-text="Alternância Agrupar por controles para recomendações":::

### <a name="expanded-security-control-implement-security-best-practices"></a>Controle de segurança expandido "Implementar melhores práticas de segurança" 

Um dos controles de segurança introduzidos com a classificação de segurança aprimorada é "Implementar melhores práticas de segurança". Quando uma recomendação está nesse controle, ela não afeta a classificação de segurança. 

Com essa atualização, três recomendações foram movidas dos controles nos quais foram originalmente colocadas e transferidas para esse controle de práticas recomendadas. Realizamos esta etapa porque determinamos que o risco dessas três recomendações é menor do que se imaginava inicialmente.

Além disso, duas novas recomendações foram introduzidas e adicionadas a esse controle.

As três recomendações que foram movidas são:

- **A MFA deve ser habilitada em contas com permissões de leitura em sua assinatura** (originalmente, no controle "Habilitar MFA")
- **Contas externas com permissões de leitura devem ser removidas de sua assinatura** (originalmente no controle "Gerenciar acesso e permissões")
- **Um máximo de três proprietários deve ser designado para sua assinatura** (originalmente no controle "Gerenciar acesso e permissões")

As duas novas recomendações adicionadas ao controle são:

- **O agente de configuração convidado deve ser instalado em máquinas virtuais do Windows (versão prévia)** : o uso da [Configuração de Convidado do Azure Policy](../governance/policy/concepts/guest-configuration.md) fornece visibilidade nas máquinas virtuais das configurações de servidor e de aplicativo (somente Windows).

- **O Microsoft Defender Exploit Guard deve ser habilitado nos computadores (versão prévia)** : o Microsoft Defender Exploit Guard utiliza o agente de Configuração de Convidado do Azure Policy. O Exploit Guard tem quatro componentes que são projetados para bloquear dispositivos contra uma ampla variedade de vetores de ataque e comportamentos de bloqueio comumente usados em ataques de malware, permitindo que cada empresa encontre um equilíbrio entre os respectivos requisitos de produtividade e o risco de segurança que enfrenta (somente Windows).

Saiba mais sobre o Microsoft Defender Exploit Guard em [Criar e implantar uma política do Exploit Guard](/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy).

Saiba mais sobre os controles de segurança em [Classificação de segurança aprimorada (versão prévia)](secure-score-security-controls.md).



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>Políticas personalizadas com metadados personalizados agora estão em disponibilidade geral

As políticas personalizadas agora fazem parte da experiência de recomendações da Central de Segurança, da classificação de segurança e do painel de padrões de conformidade regulatória. Esse recurso agora está em disponibilidade geral e permite que você estenda a cobertura de avaliação de segurança da sua organização na Central de Segurança. 

Crie uma iniciativa personalizada no Azure Policy, adicione políticas a ela, integre-a à Central de Segurança do Azure e visualize-a como recomendações.

Agora também adicionamos a opção para editar os metadados de recomendação personalizados. As opções de metadados incluem severidade, etapas de correção, informações sobre ameaças e muito mais.  

Saiba mais sobre [como aprimorar as recomendações personalizadas com informações detalhadas](custom-security-policies.md#enhance-your-custom-recommendations-with-detailed-information).



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>Migração das funcionalidades de análise de despejo de memória para a detecção de ataque sem arquivos 

Estamos integrando as funcionalidades de detecção do CDA (análise de despejo de memória) do Windows à [detecção de ataque sem arquivos](defender-for-servers-introduction.md#what-are-the-benefits-of-azure-defender-for-servers). A análise de detecção de ataque sem arquivos acompanha versões aprimoradas dos seguintes alertas de segurança para computadores Windows: Injeção de código descoberta, Módulo do Windows simulado detectado, Shellcode descoberto e Segmento de código suspeito detectado.

Alguns dos benefícios dessa transição:

- **Detecção de malware proativa e oportuna**: a abordagem da CDA envolvia a espera da ocorrência de uma falha e a posterior execução da análise para encontrar artefatos mal-intencionados. O uso da detecção de ataque sem arquivos introduz a identificação proativa de ameaças na memória enquanto elas estão em execução. 

- **Alertas aprimorados** – os alertas de segurança da detecção de ataque sem arquivos incluem aprimoramentos que não estão disponíveis no CDA, como as informações de conexões de rede ativas. 

- **Agregação de alerta** – quando o CDA detecta vários padrões de ataque em um despejo de memória, ele disparou vários alertas de segurança. A detecção de ataque sem arquivos combina todos os padrões de ataque identificados do mesmo processo em um alerta, eliminando a necessidade de correlacionar vários alertas.

- **Requisitos reduzidos em seu workspace do Log Analytics** – despejos de memória que contêm dados potencialmente confidenciais não serão mais carregados em seu workspace do Log Analytics.






## <a name="april-2020"></a>Abril de 2020

As atualizações de abril incluem:
- [Os pacotes de conformidade dinâmica agora estão em disponibilidade geral](#dynamic-compliance-packages-are-now-generally-available)
- [Recomendações de identidade agora incluídas na camada gratuita da Central de Segurança do Azure](#identity-recommendations-now-included-in-azure-security-center-free-tier)


### <a name="dynamic-compliance-packages-are-now-generally-available"></a>Os pacotes de conformidade dinâmica agora estão em disponibilidade geral

O painel de conformidade regulatória da Central de Segurança do Azure agora inclui **pacotes de conformidade dinâmica** (agora em disponibilidade geral) para acompanhar padrões adicionais do setor e regulatórios.

Os pacotes de conformidade dinâmica podem ser adicionados à sua assinatura ou grupo de gerenciamento na página política de segurança da Central de Segurança. Quando você tiver integrado um padrão ou um parâmetro de comparação, o padrão será exibido em seu painel de conformidade regulatória com todos os dados de conformidade associados mapeados como avaliações. Um relatório de resumo para qualquer um dos padrões que foram integrados estará disponível para download.

Agora, você pode adicionar padrões como:

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **UK Official e UK NHS**
- **PBMM Federal do Canadá**
- **Azure CIS 1.1.0 (novo)** (que é uma representação mais completa do Azure CIS 1.1.0)

Além disso, adicionamos recentemente o **Azure Security Benchmark**, as diretrizes específicas do Azure criadas pela Microsoft para melhores práticas de segurança e conformidade baseadas em estruturas de conformidade comuns. Outros padrões se tornarão compatíveis com o painel à medida que forem disponibilizados.  
 
Saiba mais sobre [como personalizar o conjunto de padrões em seu painel de conformidade regulatória](update-regulatory-compliance-packages.md).


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Recomendações de identidade agora incluídas na camada gratuita da Central de Segurança do Azure

As recomendações de segurança para identidade e acesso na camada gratuita da Central de Segurança do Azure agora estão em disponibilidade geral. Isso faz parte do esforço para tornar gratuitos os recursos do CSPM (Gerenciamento da situação de segurança na nuvem). Até agora, essas recomendações só estavam disponíveis no tipo de preço Standard.

Exemplos de recomendações de identidade e acesso incluem:

- "A MFA deve ser habilitada em contas com permissões de proprietário em sua assinatura."
- "Um máximo de três proprietários deve ser designado para sua assinatura."
- "As contas preteridas devem ser removidas de sua assinatura."

Se você tiver assinaturas no tipo de preço gratuito, suas classificações de segurança serão afetadas por essa alteração, pois essas assinaturas nunca foram avaliadas quanto à identidade e segurança de acesso.

Saiba mais sobre [recomendações de identidade e acesso](recommendations-reference.md#recs-identityandaccess).

Saiba mais sobre como [gerenciar a imposição de MFA (autenticação multifator) em suas assinaturas](security-center-identity-access.md).



## <a name="march-2020"></a>Março de 2020

As atualizações de março incluem:

- [A automação do fluxo de trabalho já está em disponibilidade geral](#workflow-automation-is-now-generally-available)
- [Integração da Central de Segurança do Azure ao Windows Admin Center](#integration-of-azure-security-center-with-windows-admin-center)
- [Proteção para o Serviço de Kubernetes do Azure](#protection-for-azure-kubernetes-service)
- [Experiência Just-In-Time aprimorada](#improved-just-in-time-experience)
- [Duas recomendações de segurança para aplicativos Web preteridas](#two-security-recommendations-for-web-applications-deprecated)


### <a name="workflow-automation-is-now-generally-available"></a>A automação do fluxo de trabalho já está em disponibilidade geral

O recurso de automação de fluxo de trabalho da Central de Segurança do Azure agora está em disponibilidade geral. Use-o para disparar automaticamente Aplicativos Lógicos em alertas de segurança e recomendações. Além disso, os gatilhos manuais estão disponíveis para alertas e todas as recomendações que têm a opção de correção rápida disponível.

Cada programa de segurança inclui vários fluxos de trabalho para resposta a incidentes. Esses processos podem incluir a notificação de stakeholders relevantes, a inicialização de um processo de gerenciamento de alterações e a aplicação de etapas de correção específicas. Os especialistas em segurança recomendam que você automatize o máximo possível de etapas desses procedimentos. A automação reduz a sobrecarga e pode aprimorar sua segurança, garantindo que as etapas do processo sejam feitas de maneira rápida, consistente e de acordo com seus requisitos predefinidos.

Para obter mais informações sobre as funcionalidades automáticas e manuais da Central de Segurança do Azure para executar seus fluxos de trabalho, confira [automação de fluxo de trabalho](workflow-automation.md).

Saiba mais sobre a [criação de Aplicativos Lógicos](../logic-apps/logic-apps-overview.md).


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Integração da Central de Segurança do Azure ao Windows Admin Center

Agora é possível mover seus servidores Windows locais do Windows Admin Center diretamente para a Central de Segurança do Azure. A Central de Segurança se torna seu único painel de controle para ver informações de segurança para todos os seus recursos do Windows Admin Center, incluindo servidores locais, máquinas virtuais e cargas de trabalho de PaaS adicionais.

Depois de mover um servidor do Windows Admin Center para a Central de Segurança do Azure, você poderá:

- Exibir alertas de segurança e recomendações na extensão da Central de Segurança do Windows Admin Center.
- Exibir a postura de segurança e recuperar informações detalhadas adicionais dos servidores gerenciados do Windows Admin Center na Central de Segurança dentro do portal do Azure (ou por meio de uma API).

Saiba mais sobre [como integrar a Central de Segurança do Azure ao Windows Admin Center](windows-admin-center-integration.md).


### <a name="protection-for-azure-kubernetes-service"></a>Proteção para o Serviço de Kubernetes do Azure

A Central de Segurança do Azure está expandindo os respectivos recursos de segurança de contêiner para proteger o AKS (Serviço de Kubernetes do Azure).

A popular plataforma de software livre do Kubernetes foi adotada tão amplamente que ela agora é um padrão do setor para orquestração de contêineres. Apesar dessa implementação generalizada, ainda há uma falta de compreensão sobre como proteger um ambiente do Kubernetes. A defesa das superfícies de ataque de um aplicativo em contêineres exige experiência para garantir que a infraestrutura seja configurada de modo seguro e constante para possíveis ameaças.

A defesa da Central de Segurança inclui:

- **Descoberta e visibilidade** – descoberta contínua de instâncias gerenciadas do AKS nas assinaturas registradas na Central de Segurança.
- **Recomendações de segurança** – recomendações acionáveis para ajudá-lo a manter a conformidade com as melhores práticas de segurança para o AKS. Essas recomendações estão incluídas na sua classificação de segurança para garantir que elas sejam exibidas como parte da postura de segurança de sua organização. Um exemplo de uma recomendação relacionada ao AKS que você pode ver é "O controle de acesso baseado em função deve ser usado para restringir o acesso a um cluster do serviço de Kubernetes".
- **Proteção contra ameaças** – por meio da análise contínua de sua implantação do AKS, a Central de Segurança alerta você sobre ameaças e atividades mal-intencionadas detectadas no nível de cluster do host e do AKS.

Saiba mais sobre a [Integração do Serviço de Kubernetes do Azure à Central de Segurança](defender-for-kubernetes-introduction.md).

Saiba mais sobre os [recursos de segurança de contêiner na Central de Segurança](container-security.md).


### <a name="improved-just-in-time-experience"></a>Experiência Just-In-Time aprimorada

Os recursos, a operação e a interface do usuário das ferramentas Just-In-Time da Central de Segurança do Azure que protegem suas portas de gerenciamento foram aprimoradas da seguinte maneira: 

- **Campo de justificativa** – ao solicitar acesso a uma VM (máquina virtual) por meio da página Just-In-Time do portal do Azure, um novo campo opcional estará disponível para inserir uma justificativa para a solicitação. As informações inseridas nesse campo podem ser acompanhadas no log de atividades. 
- **Limpeza automática de regras JIT (Just-In-Time) redundantes** – sempre que você atualiza uma política JIT, uma ferramenta de limpeza é executada automaticamente para verificar a validade de todo o conjunto de regras. A ferramenta procura incompatibilidades entre as regras em sua política e as regras no NSG. Se a ferramenta de limpeza encontrar uma incompatibilidade, ela determinará a causa e, quando for seguro, removerá as regras internas que não forem mais necessárias. O limpador nunca exclui regras que você criou. 

Saiba mais sobre o [recurso de acesso JIT](security-center-just-in-time.md).


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>Duas recomendações de segurança para aplicativos Web preteridas

Duas recomendações de segurança relacionadas a aplicativos Web estão sendo preteridas: 

- As regras para aplicativos Web nos NSGs da IaaS devem ser fortalecidas.
    (Política relacionada: as regras de NSGs para aplicativos Web em IaaS devem ser fortalecidas)

- O acesso aos Serviços de Aplicativos deve ser restrito.
    (Política relacionada: o acesso aos Serviços de Aplicativos deve ser restrito [Versão Prévia])

Essas recomendações não serão mais exibidas na lista de recomendações da Central de Segurança. As políticas relacionadas não serão mais incluídas na iniciativa denominada "Padrão da Central de Segurança".

Saiba mais sobre [recomendações de segurança](recommendations-reference.md).




## <a name="february-2020"></a>Fevereiro de 2020

### <a name="fileless-attack-detection-for-linux-preview"></a>Detecção de ataque sem arquivos para Linux (versão prévia)

Já que os invasores usam métodos cada vez mais difíceis de detectar, a Central de Segurança do Azure está estendendo a detecção de ataque sem arquivos para o Linux, além do Windows. Ataques sem arquivos exploram vulnerabilidades de software, injetam cargas mal-intencionadas em processos de sistema benignos e ficam ocultos na memória. Essas técnicas:

- minimizam ou eliminam sinais de malware no disco
- reduzem significativamente as chances de detecção por soluções de verificação de malware baseadas em disco

Para combater essa ameaça, a Central de Segurança do Azure liberou a detecção de ataque sem arquivos para Windows em outubro de 2018 e agora ampliou a detecção de ataque sem arquivos para o Linux também. 



## <a name="january-2020"></a>Janeiro de 2020

### <a name="enhanced-secure-score-preview"></a>Classificação de segurança aprimorada (versão prévia)

Uma versão aprimorada do recurso de classificação de segurança da Central de Segurança do Azure agora está disponível em versão prévia. Nesta versão, várias recomendações são agrupadas em Controles de Segurança que refletem melhor suas superfícies de ataque vulneráveis (por exemplo, restringir o acesso às portas de gerenciamento).

Familiarize-se com as alterações classificação de segurança durante a fase de versão prévia e determine outras correções que o ajudarão a proteger ainda mais seu ambiente.

Saiba mais sobre a [Pontuação segura aprimorada (visualização)](secure-score-security-controls.md).



## <a name="november-2019"></a>Novembro de 2019

As atualizações de novembro incluem:
 - [Proteção contra ameaças para Azure Key Vault em regiões América do Norte (visualização)](#threat-protection-for-azure-key-vault-in-north-america-regions-preview)
 - [A Proteção contra Ameaças para o Armazenamento do Microsoft Azure inclui Triagem de Reputação de Malware](#threat-protection-for-azure-storage-includes-malware-reputation-screening)
 - [Automação de fluxo de trabalho com Aplicativos Lógicos (versão preliminar)](#workflow-automation-with-logic-apps-preview)
 - [Correção rápida para recursos em massa disponíveis para o público geral](#quick-fix-for-bulk-resources-generally-available)
 - [Verificar se há vulnerabilidades em imagens de contêiner (versão preliminar)](#scan-container-images-for-vulnerabilities-preview)
 - [Padrões adicionais de conformidade regulatória (versão preliminar)](#additional-regulatory-compliance-standards-preview)
 - [Proteção contra ameaças para o Serviço de Kubernetes do Azure (versão preliminar)](#threat-protection-for-azure-kubernetes-service-preview)
 - [Avaliação de vulnerabilidades de máquinas virtuais (versão preliminar)](#virtual-machine-vulnerability-assessment-preview)
 - [Segurança de Dados Avançada para o SQL Server em Máquinas Virtuais do Microsoft Azure (versão preliminar)](#advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview)
 - [Compatibilidade com políticas personalizadas (versão preliminar)](#support-for-custom-policies-preview)
 - [Estender a cobertura da Central de Segurança do Azure com plataforma para comunidade e parceiros](#extending-azure-security-center-coverage-with-platform-for-community-and-partners)
 - [Integrações avançadas com a exportação de recomendações e alertas (versão preliminar)](#advanced-integrations-with-export-of-recommendations-and-alerts-preview)
 - [Carregar servidores locais na Central de Segurança do Centro de Administração do Windows (versão preliminar)](#onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview)

### <a name="threat-protection-for-azure-key-vault-in-north-america-regions-preview"></a>Proteção contra ameaças para Azure Key Vault em regiões América do Norte (visualização)

O Azure Key Vault é um serviço essencial para proteger dados e melhorar o desempenho de aplicativos na nuvem, oferecendo a capacidade de gerenciar centralmente chaves, segredos, chaves de criptografia e políticas na nuvem. Como o Azure Key Vault armazena dados confidenciais e críticos para os negócios, ele exige segurança máxima para os cofres de chaves e os dados armazenados neles.

O suporte da Central de Segurança do Azure para proteção contra ameaças para Azure Key Vault oferece uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar cofres de chaves. Essa nova camada de proteção permite que os clientes resolvam ameaças contra seus cofres de chaves sem serem especialistas em segurança ou gerenciar sistemas de monitoramento de segurança. O recurso está em versão preliminar pública em regiões da América do Norte.


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>A Proteção contra Ameaças para o Armazenamento do Microsoft Azure inclui Triagem de Reputação de Malware

A proteção contra ameaças para o Armazenamento do Azure oferece novas detecções alimentadas pela Inteligência contra Ameaças da Microsoft para detectar carregamentos de malware no Armazenamento do Azure usando análise de reputação de hash e acesso suspeito de um nó de saída de Tor ativo (um proxy de anonimato). Agora você pode ver o malware detectado em contas de armazenamento usando a Central de Segurança do Azure.


### <a name="workflow-automation-with-logic-apps-preview"></a>Automação de fluxo de trabalho com Aplicativos Lógicos (versão preliminar)

As organizações com segurança gerenciada centralmente e TI/operações implementam processos de fluxo de trabalho integrados para impulsionar a ação necessária na organização quando as discrepâncias são descobertas em seus ambientes. Em muitos casos, esses fluxos de trabalho são processos repetíveis, e a automação pode simplificar muito os processos na organização.

Hoje, estamos introduzindo um novo recurso na Central de Segurança que permite que os clientes criem configurações de automação aproveitando os Aplicativos Lógicos do Azure e criem políticas que vão dispará-los automaticamente com base em descobertas de ASC específicas, como Recomendações ou Alertas. O Aplicativo Lógico do Azure pode ser configurado para realizar qualquer ação personalizada com suporte pela vasta comunidade de conectores de Aplicativos Lógicos ou usar um dos modelos fornecidos pela Central de Segurança, como enviar um email ou abrir um tíquete de do ServiceNow™.

Para obter mais informações sobre as funcionalidades automáticas e manuais da Central de Segurança do Azure para executar seus fluxos de trabalho, confira [automação de fluxo de trabalho](workflow-automation.md).

Para saber mais sobre a criação de Aplicativos Lógicos, consulte [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md).


### <a name="quick-fix-for-bulk-resources-generally-available"></a>Correção rápida para recursos em massa disponíveis para o público geral

Com as muitas tarefas que um usuário recebe como parte da Classificação de Segurança, a capacidade de corrigir efetivamente os problemas em um grande frota pode se tornar desafiador.

Para simplificar a correção de configurações incorretas de segurança e conseguir corrigir rapidamente as recomendações em uma grande quantidade de recursos e melhorar sua Classificação de Segurança, use a Correção Rápida.

Essa operação permitirá selecionar os recursos para os quais você deseja aplicar a correção e iniciar uma ação de correção que definirá a configuração em seu nome.

A Correção Rápida geralmente está disponível para clientes atuais como parte da página de recomendações da Central de Segurança.

Veja quais recomendações têm Correção Rápida habilitada no [guia de referência para recomendações de segurança](recommendations-reference.md).


### <a name="scan-container-images-for-vulnerabilities-preview"></a>Verificar se há vulnerabilidades em imagens de contêiner (versão preliminar)

A Central de Segurança do Azure agora pode verificar as imagens de contêiner no Registro de Contêiner do Azure em busca de vulnerabilidades.

A verificação de imagem funciona analisando o arquivo de imagem de contêiner e, em seguida, verificando se há alguma vulnerabilidade conhecida (da plataforma Qualys).

A varredura em si é disparada automaticamente ao enviar por push novas imagens de contêiner para o Registro de Contêiner do Azure. As vulnerabilidades encontradas surgirão como recomendações da Central de Segurança e incluídas na Classificação de Segurança do Azure, com informações sobre como corrigi-las para reduzir a superfície de ataque permitida.


### <a name="additional-regulatory-compliance-standards-preview"></a>Padrões adicionais de conformidade regulatória (versão preliminar)

O painel de Conformidade Regulatória mostra informações sobre sua postura de conformidade com base nas avaliações da Central de Segurança. O painel mostra como o seu ambiente está em conformidade com os controles e requisitos designados por padrões regulatórios específicos e benchmarks do setor e fornece recomendações prescritivas sobre como lidar com esses requisitos.

O painel de conformidade regulatória oferece, até o momento, suporte a quatro padrões integrados:  Azure CIS 1.1.0, PCI-DSS, ISO 27001 e SOC-TSP. Agora estamos anunciando a versão preliminar de padrões adicionais compatíveis: NIST SP 800-53 R4, SWIFT CSP CSCF v2020, Canada Federal PBMM e UK Official com UK NHS. Também estamos lançando uma versão atualizada do Azure CIS 1.1.0, abrangendo mais controles do padrão e aprimorando a extensibilidade.

Saiba mais sobre [como personalizar o conjunto de padrões em seu painel de conformidade regulatória](update-regulatory-compliance-packages.md).


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>Proteção contra ameaças para o Serviço de Kubernetes do Azure (versão preliminar)

O Kubernetes está se tornando rapidamente o novo padrão para implantar e gerenciar software na nuvem. Poucas pessoas têm ampla experiência com Kubernetes e muitos se concentram apenas em engenharia e administração geral e ignoram o aspecto de segurança. O ambiente Kubernetes precisa ser configurado com cuidado para ser seguro, garantindo que nenhuma das portas da superfície de ataque focadas no contêiner seja deixada aberta é exposta para invasores. A Central de Segurança está expandindo seu suporte no espaço do contêiner para um dos serviços de crescimento mais rápido no Azure: AKS (Serviço de Kubernetes do Azure).

Os novos recursos dessa versão preliminar pública incluem:

- **Descoberta e visibilidade**: descoberta contínua de instâncias do AKS gerenciadas nas assinaturas registradas da Central de Segurança.
- **Recomendações de Pontuação segura** -itens acionáveis para ajudar os clientes a cumprir as práticas recomendadas de segurança para AKs e aumentar sua pontuação segura. As recomendações incluem itens como "o controle de acesso baseado em função deve ser usado para restringir o acesso a um cluster do serviço kubernetes".
- **Detecção de ameaças**: análise baseada em cluster e host, como "um contêiner privilegiado detectado".


### <a name="virtual-machine-vulnerability-assessment-preview"></a>Avaliação de vulnerabilidades de máquinas virtuais (versão preliminar)

Os aplicativos instalados em máquinas virtuais geralmente podem ter vulnerabilidades que poderiam levar a uma violação da máquina virtual. Estamos anunciando que a camada Standard da central de segurança inclui a avaliação de vulnerabilidades interna para máquinas virtuais sem nenhuma taxa adicional. A avaliação de vulnerabilidade, fornecida pela Qualys na versão preliminar pública, permitirá que você examine continuamente todos os aplicativos instalados em uma máquina virtual para encontrar aplicativos vulneráveis e apresente as descobertas na experiência do portal da Central de Segurança. A Central de Segurança cuida de todas as operações de implantação para que não seja necessário nenhum trabalho extra do usuário. No futuro, estamos planejando fornecer opções de avaliação de vulnerabilidade para dar suporte às necessidades de negócios únicas de nossos clientes.

[Saiba mais sobre avaliações de vulnerabilidades para suas máquinas virtuais do Azure](deploy-vulnerability-assessment-vm.md).


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Segurança de Dados Avançada para o SQL Server em Máquinas Virtuais do Microsoft Azure (versão preliminar)

O suporte da Central de Segurança do Azure para proteção contra ameaças e a avaliação de vulnerabilidade para bancos de dados SQL em execução em VMs de IaaS agora está em versão preliminar.

A [Avaliação de vulnerabilidade ](../azure-sql/database/sql-vulnerability-assessment.md) é um serviço fácil de ser configurado que pode descobrir, acompanhar e ajudar a corrigir possíveis vulnerabilidades do banco de dados. Ele fornece visibilidade da sua postura de segurança como parte da Classificação de Segurança do Azure e inclui as etapas para resolver problemas de segurança e aprimorar as fortificações do banco de dados.

A [Proteção Avançada contra Ameaças](../azure-sql/database/threat-detection-overview.md) detecta atividades anômalas, indicando tentativas incomuns e potencialmente prejudiciais de acessar ou explorar seu SQL Server. Monitora continuamente seu banco de dados em busca de atividades suspeitas e fornece alertas de segurança orientados a ações em padrões anormais de acesso de banco de dados. Esses alertas mostram os detalhes da atividade suspeita e as ações recomendadas para investigar e atenuar a ameaça.


### <a name="support-for-custom-policies-preview"></a>Compatibilidade com políticas personalizadas (versão preliminar)

A Central de Segurança do Azure agora permite políticas personalizadas (em versão preliminar).

Nossos clientes querem estender sua cobertura atual de avaliações de segurança na Central de Segurança com suas próprias avaliações, com base nas políticas que elas criam no Azure Policy. Com o suporte a políticas personalizadas, isso agora é possível.

As políticas personalizadas agora serão parte da experiência de recomendações da Central de Segurança, da Classificação de Segurança e do painel de padrões de conformidade regulatória. Com o suporte a políticas personalizadas, agora você pode criar uma iniciativa personalizada no Azure Policy e, em seguida, adicioná-la como uma política na Central de Segurança e visualizá-la como uma recomendação.


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>Estender a cobertura da Central de Segurança do Azure com plataforma para comunidade e parceiros

Use a Central de Segurança para receber recomendações não apenas da Microsoft, mas também de soluções existentes de parceiros como Check Point, Tenable e CyberArk, com muito mais integrações chegando.  O fluxo de integração simples da Central de Segurança pode conectar suas soluções existentes à Central de Segurança, permitindo que você exiba suas recomendações de postura de segurança em um único lugar, execute relatórios unificados e aproveite todos os recursos da Central de Segurança em relação às recomendações integradas e de parceiros. Você também pode exportar recomendações da Central de Segurança para produtos de parceiros.

[Saiba mais sobre a associação de segurança inteligente da Microsoft](https://www.microsoft.com/security/partnerships/intelligent-security-association).



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>Integrações avançadas com a exportação de recomendações e alertas (versão preliminar)

Para habilitar cenários de nível empresarial na Central de Segurança, agora é possível consumir alertas e recomendações da Central de Segurança em locais adicionais, exceto o portal do Azure ou a API. Eles podem ser exportados diretamente para um hub de eventos e para workspaces do Log Analytics. Aqui estão alguns fluxos de trabalho que você pode criar com base nesses novos recursos:

- Com a exportação para workspaces do Log Analytics, você pode criar painéis personalizados com o Power BI.
- Com a exportação para o hub de eventos, você poderá exportar alertas e recomendações da Central de Segurança para seus SIEMs de terceiros, para uma solução de terceiros em tempo real ou o Data Explorer do Azure.


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>Carregar servidores locais na Central de Segurança do Centro de Administração do Windows (versão preliminar)

O Centro de Administração do Windows é um portal de gerenciamento para servidores Windows que não estão implantados no Azure, oferecendo vários recursos de gerenciamento do Azure, como atualizações de sistema e backup. Recentemente, adicionamos um recurso para carregar esses servidores não Azure para serem protegidos pelo Certificado do Serviço de Aplicativo diretamente do Centro de Administração do Windows.

Com essa nova experiência, os usuários poderão integrar um servidor WAC à Central de Segurança do Azure e habilitar a exibição de seus alertas de segurança e recomendações diretamente no Centro de Administração do Windows.


## <a name="september-2019"></a>Setembro de 2019

As atualizações de setembro incluem:

 - [Gerenciar regras com aprimoramentos de controles de aplicativos adaptáveis](#managing-rules-with-adaptive-application-controls-improvements)
 - [Controlar a recomendação de segurança do contêiner usando Azure Policy](#control-container-security-recommendation-using-azure-policy)

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>Gerenciar regras com aprimoramentos de controles de aplicativos adaptáveis

A experiência de gerenciar regras para máquinas virtuais usando controles de aplicativos adaptáveis foi aprimorada. Os controles de aplicativo adaptáveis da Central de Segurança do Azure ajudam a controlar quais aplicativos podem ser executados em suas máquinas virtuais. Além de uma melhoria geral no gerenciamento de regras, um novo benefício permite que você controle quais tipos de arquivo serão protegidos quando você adicionar uma nova regra.

[Saiba mais sobre controles de aplicativo adaptáveis](security-center-adaptive-application.md).


### <a name="control-container-security-recommendation-using-azure-policy"></a>Controlar a recomendação de segurança do contêiner usando Azure Policy

A recomendação da Central de Segurança do Azure para corrigir vulnerabilidades em segurança de contêiner agora pode ser habilitada ou desabilitada pelo Azure Policy.

Para exibir as políticas de segurança habilitadas, na Central de Segurança, abra a página Política de Segurança.


## <a name="august-2019"></a>Agosto de 2019

As atualizações de agosto incluem:

 - [Acesso à VM just-in-time (JIT) para o Firewall do Azure](#just-in-time-jit-vm-access-for-azure-firewall)
 - [Correção com um só clique para impulsionar sua postura de segurança (versão preliminar)](#single-click-remediation-to-boost-your-security-posture-preview)
 - [Gerenciamento entre locatários](#cross-tenant-management)

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Acesso à VM just-in-time (JIT) para o Firewall do Azure 

O acesso à VM just-in-time (JIT) para o Firewall do Azure já está disponível para o público geral. Use-o para proteger seus ambientes protegidos por Firewall do Azure, além de seus ambientes protegidos por grupo de segurança de rede.

O acesso à VM JIT reduz a exposição a ataques volumétricos de rede, fornecendo acesso controlado às VMs somente quando necessário, usando suas regras de grupo de segurança de rede e Firewall do Azure.

Quando você habilita o JIT para suas VMs, cria uma política que determina as portas a serem protegidas, por quanto tempo as portas devem permanecer abertas e os endereços IP aprovados de onde essas portas podem ser acessadas. Essa política ajuda você a manter o controle do que os usuários podem fazer quando solicitam acesso.

As solicitações são registradas no log de atividades do Azure, para que você possa monitorar e auditar facilmente o acesso. A página just-in-time também ajuda a identificar rapidamente as VMs existentes que têm o JIT habilitado e as VMs em que o JIT é recomendado.

[Saiba mais sobre o Firewall do Azure](../firewall/overview.md).


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>Correção com um só clique para impulsionar sua postura de segurança (versão preliminar)

A pontuação segura é uma ferramenta que ajuda a avaliar sua postura de segurança de carga de trabalho. Ela analisa suas recomendações de segurança e as prioriza para você, para que você saiba quais recomendações devem ser executadas primeiro. Isso ajuda a encontrar as vulnerabilidades de segurança mais sérias para que você possa priorizar a investigação.

Para simplificar a correção de configurações incorretas de segurança e ajudá-lo a melhorar rapidamente sua classificação de segurança, adicionamos um novo recurso que permite que você corrija uma recomendação em uma grande quantidade de recursos em um único clique.

Essa operação permitirá selecionar os recursos para os quais você deseja aplicar a correção e iniciar uma ação de correção que definirá a configuração em seu nome.

Veja quais recomendações têm Correção Rápida habilitada no [guia de referência para recomendações de segurança](recommendations-reference.md).


### <a name="cross-tenant-management"></a>Gerenciamento entre locatários

A Central de Segurança agora dá suporte a cenários de gerenciamento entre locatários como parte do Azure Lighthouse. Isso permite que você tenha visibilidade e gerencie a postura de segurança de vários locatários na Central de Segurança. 

[Saiba mais sobre as experiências de gerenciamento entre locatários](security-center-cross-tenant-management.md).


## <a name="july-2019"></a>Julho de 2019

### <a name="updates-to-network-recommendations"></a>Atualizações das recomendações de rede

A Central de Segurança do Azure (ASC) lançou novas recomendações de rede e melhorou algumas delas. Agora, o uso da Central de Segurança garante uma proteção de rede ainda maior para seus recursos. 

[Saiba mais sobre as recomendações de rede](recommendations-reference.md#recs-networking).


## <a name="june-2019"></a>Junho de 2019

### <a name="adaptive-network-hardening---generally-available"></a>Proteção de rede adaptável: disponível para o público geral

Uma das maiores superfícies de ataque para cargas de trabalho executadas na nuvem pública são as conexões de e para a Internet pública. Nossos clientes acham difícil saber quais regras do Grupo de Segurança de Rede (NSG) devem estar em vigor para garantir que as cargas de trabalho do Azure estejam disponíveis apenas para os intervalos de origem necessários. Com esse recurso, a Central de Segurança aprende o tráfego de rede e os padrões de conectividade das cargas de trabalho do Azure e mostra as recomendações de regras do grupo de segurança de rede para máquinas virtuais voltadas para a Internet. Isso ajuda nossos clientes a configurar melhor suas políticas de acesso à rede e limitar sua exposição a ataques. 

[Saiba mais sobre a proteção de rede adaptável](security-center-adaptive-network-hardening.md).