---
ms.date: 02/16/2021
ms.topic: reference-architecture
author: kriation
title: Arquitetura de referência do Azure Spring Cloud
ms.author: akaleshian
ms.service: spring-cloud
description: Essa arquitetura de referência é uma base que usa um design típico de Hub corporativo e spoke para o uso do Azure Spring Cloud.
ms.openlocfilehash: c87462c8d6ab86299b6202acaa23f93d19240e0e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735481"
---
# <a name="azure-spring-cloud-reference-architecture"></a>Arquitetura de referência do Azure Spring Cloud

Essa arquitetura de referência é uma base que usa um design típico de Hub corporativo e spoke para o uso do Azure Spring Cloud. No design, o Azure Spring Cloud é implantado em um único spoke dependente de serviços compartilhados hospedados no Hub. A arquitetura é criada com componentes para alcançar as filosofias no [Microsoft Azure Well-Architected Framework][16].

Para uma implementação dessa arquitetura, consulte o repositório da [arquitetura de referência da nuvem Spring do Azure][10] no github.

As opções de implantação para essa arquitetura incluem Azure Resource Manager (ARM), Terraform e CLI do Azure. Os artefatos neste repositório fornecem uma base que você pode personalizar para seu ambiente. Você pode agrupar recursos como o Firewall do Azure ou o gateway de aplicativo em diferentes grupos de recursos ou assinaturas. Esse agrupamento ajuda a manter diferentes funções separadas, como infraestrutura de ti, segurança, equipes de aplicativos de negócios e assim por diante.

## <a name="planning-the-address-space"></a>Planejando o espaço de endereço

O Azure Spring Cloud requer duas sub-redes dedicadas:

* Runtime de serviço
* Aplicativos Spring boot

Cada uma dessas sub-redes requer um cluster dedicado. Vários clusters não podem compartilhar as mesmas sub-redes. O tamanho mínimo de cada sub-rede é/28. O número de instâncias de aplicativo às quais o Azure Spring Cloud pode dar suporte varia de acordo com o tamanho da sub-rede. Você pode encontrar os requisitos de VNET (rede virtual) detalhadas na seção [requisitos de rede virtual][11] de [implantar o Azure Spring Cloud em uma rede virtual][17].

> [!WARNING]
> O tamanho de sub-rede selecionado não pode se sobrepor ao espaço de endereço de VNET existente e não deve se sobrepor a intervalos de endereços de sub-rede locais ou emparelhados.

## <a name="use-cases"></a>Casos de uso

Alguns usos típicos dessa arquitetura:

* Aplicativos internos implantados em ambientes de nuvem híbrida
* Aplicativos voltados para o público

Esses casos de uso são semelhantes, exceto para suas regras de segurança e de tráfego de rede. Essa arquitetura foi projetada para dar suporte às nuances de cada uma.

## <a name="private-applications"></a>Aplicativos privados

A lista a seguir descreve os requisitos de infraestrutura para aplicativos privados. Esses requisitos são típicos em ambientes altamente regulamentados.

* Nenhuma saída direta para a Internet pública, exceto para o tráfego do plano de controle.
* O tráfego de saída deve viajar por meio de uma NVA (solução de virtualização de rede central) (por exemplo, o Firewall do Azure).
* Os dados em repouso devem ser criptografados.
* Os dados em trânsito devem ser criptografados.
* Os pipelines de implantação DevOps podem ser usados (por exemplo, DevOps do Azure) e exigem conectividade de rede para o Azure Spring Cloud.
* A abordagem de segurança de confiança zero da Microsoft requer segredos, certificados e credenciais a serem armazenadas em um cofre seguro. O serviço recomendado é Azure Key Vault.
* Os registros DNS (serviço de nomes de domínio) do host de aplicativo devem ser armazenados no Azure DNS privado.
* A resolução de nomes de hosts locais e na nuvem deve ser bidirecional.
* A adesão a pelo menos um parâmetro de comparação de segurança deve ser imposta.
* As dependências de serviço do Azure devem se comunicar por meio de pontos de extremidade de serviço ou de link privado.
* Os grupos de recursos gerenciados pela implantação do Azure Spring Cloud não devem ser modificados.
* As sub-redes gerenciadas pela implantação do Azure Spring Cloud não devem ser modificadas.
* Uma sub-rede deve ter apenas uma instância do Azure Spring Cloud.
* Se o [servidor de configuração do Spring Cloud do Azure][8] for usado para carregar as propriedades de configuração de um repositório, o repositório deverá ser privado.

A lista a seguir mostra os componentes que compõem o design:

* Rede local
  * DNS (serviço de nomes de domínio)
  * Gateway
* Assinatura de Hub
  * Sub-rede de firewall do Azure
  * Sub-rede do gateway de aplicativo
  * Sub-rede de serviços compartilhados
* Assinatura conectada
  * Par de rede virtual
  * Sub-rede de bastiões do Azure

A lista a seguir descreve os serviços do Azure nesta arquitetura de referência:

* [Azure Spring Cloud][1]: um serviço gerenciado que é projetado e otimizado especificamente para aplicativos Spring boot baseados em Java e. Aplicativos [Steeltoe][9] baseados em rede.

* [Azure Key Vault][2]: um serviço de gerenciamento de credenciais com suporte de hardware que tem integração total com os serviços de identidade da Microsoft e recursos de computação.

* [Azure monitor][3]: um pacote completo de serviços de monitoramento para aplicativos que são implantados no Azure e no local.

* [Central de segurança do Azure][4]: um sistema de proteção de ameaças e gerenciamento de segurança unificado para cargas de trabalho entre locais, várias nuvens e o Azure.

* [Azure pipelines][5]: um serviço de CI/CD (integração contínua/desenvolvimento contínuo) completo que pode implantar automaticamente os aplicativos Spring boot atualizados no Azure Spring Cloud.

O diagrama a seguir representa um design de Hub e spoke bem arquitetado que aborda os requisitos acima:

![Diagrama de arquitetura de referência para aplicativos privados](./media/spring-cloud-reference-architecture/architecture-private.png)

## <a name="public-applications"></a>Aplicativos públicos

A lista a seguir descreve os requisitos de infraestrutura para aplicativos públicos. Esses requisitos são típicos em ambientes altamente regulamentados.

* O tráfego de entrada deve ser gerenciado por pelo menos o gateway de aplicativo ou a porta frontal do Azure.
* A proteção contra DDoS do Azure Standard deve ser habilitada.
* Nenhuma saída direta para a Internet pública, exceto para o tráfego do plano de controle.
* O tráfego de saída deve atravessar uma NVA (solução de virtualização de rede central) (por exemplo, o Firewall do Azure).
* Os dados em repouso devem ser criptografados.
* Os dados em trânsito devem ser criptografados.
* Os pipelines de implantação DevOps podem ser usados (por exemplo, DevOps do Azure) e exigem conectividade de rede para o Azure Spring Cloud.
* A abordagem de segurança de confiança zero da Microsoft requer segredos, certificados e credenciais a serem armazenadas em um cofre seguro. O serviço recomendado é Azure Key Vault.
* Os registros DNS do host de aplicativo devem ser armazenados no Azure DNS privado.
* Endereços roteáveis da Internet devem ser armazenados no DNS público do Azure.
* A resolução de nomes de hosts locais e na nuvem deve ser bidirecional.
* A adesão a pelo menos um parâmetro de comparação de segurança deve ser imposta.
* As dependências de serviço do Azure devem se comunicar por meio de pontos de extremidade de serviço ou de link privado.
* Os grupos de recursos gerenciados pela implantação do Azure Spring Cloud não devem ser modificados.
* As sub-redes gerenciadas pela implantação do Azure Spring Cloud não devem ser modificadas.
* Uma sub-rede deve ter apenas uma instância do Azure Spring Cloud.

A lista a seguir mostra os componentes que compõem o design:

* Rede local
  * DNS (serviço de nomes de domínio)
  * Gateway
* Assinatura de Hub
  * Sub-rede de firewall do Azure
  * Sub-rede do gateway de aplicativo
  * Sub-rede de serviços compartilhados
* Assinatura conectada
  * Par de rede virtual
  * Sub-rede de bastiões do Azure

A lista a seguir descreve os serviços do Azure nesta arquitetura de referência:

* [Azure Spring Cloud][1]: um serviço gerenciado que é projetado e otimizado especificamente para aplicativos Spring boot baseados em Java e. Aplicativos [Steeltoe][9] baseados em rede.

* [Azure Key Vault][2]: um serviço de gerenciamento de credenciais com suporte de hardware que tem integração total com os serviços de identidade da Microsoft e recursos de computação.

* [Azure monitor][3]: um pacote completo de serviços de monitoramento para aplicativos que são implantados no Azure e no local.

* [Central de segurança do Azure][4]: um sistema de proteção de ameaças e gerenciamento de segurança unificado para cargas de trabalho entre locais, várias nuvens e o Azure.

* [Azure pipelines][5]: um serviço de CI/CD (integração contínua/desenvolvimento contínuo) completo que pode implantar automaticamente os aplicativos Spring boot atualizados no Azure Spring Cloud.

* [Gateway de aplicativo Azure][6]: um balanceador de carga responsável pelo tráfego do aplicativo com a operação de descarregamento de TLS (segurança da camada de transporte) na camada 7.

* [Aplicativo Azure firewall][7]: um recurso do Gateway de aplicativo Azure que fornece proteção centralizada de aplicativos contra explorações e vulnerabilidades comuns.

O diagrama a seguir representa um design de Hub e spoke bem arquitetado que aborda os requisitos acima:

![Diagrama de arquitetura de referência para aplicativos públicos](./media/spring-cloud-reference-architecture/architecture-public.png)

## <a name="azure-spring-cloud-on-premises-connectivity"></a>Conectividade local do Azure Spring Cloud

Aplicativos em execução no Azure Spring Cloud podem se comunicar com vários recursos do Azure, locais e externos. Usando o design de Hub e spoke, os aplicativos podem rotear o tráfego externamente ou para a rede local usando a rota expressa ou a VPN (rede virtual privada) site a site.

## <a name="azure-well-architected-framework-considerations"></a>Considerações sobre o Azure Well-Architected Framework

O [Azure Well-Architected Framework][16] é um conjunto de filosofias de orientação a ser seguido no estabelecimento de uma base de infra-estrutura forte. A estrutura contém as seguintes categorias: otimização de custos, excelência operacional, eficiência de desempenho, confiabilidade e segurança.

### <a name="cost-optimization"></a>Otimização de custo

Devido à natureza do design do sistema distribuído, a expansão da infraestrutura é uma realidade. Essa realidade resulta em custos inesperados e não controláveis. O Azure Spring Cloud é criado usando componentes que dimensionam para que ele possa atender à demanda e otimizar o custo. O núcleo dessa arquitetura é o serviço de kubernetes do Azure (AKS). O serviço foi projetado para reduzir a complexidade e a sobrecarga operacional do gerenciamento de kubernetes, que inclui eficiências no custo operacional do cluster.

Você pode implantar diferentes aplicativos e tipos de aplicativos em uma única instância do Azure Spring Cloud. O serviço oferece suporte ao dimensionamento automático de aplicativos disparados por métricas ou agendas que podem melhorar a utilização e a eficiência de custos.

Você também pode usar Application Insights e Azure Monitor para reduzir o custo operacional. Com a visibilidade fornecida pela solução de registro em log abrangente, você pode implementar a automação para dimensionar os componentes do sistema em tempo real. Você também pode analisar dados de log para revelar ineficiências no código do aplicativo que você pode resolver para melhorar o custo e o desempenho geral do sistema.

### <a name="operational-excellence"></a>Excelência operacional

O Azure Spring Cloud aborda vários aspectos da excelência operacional. Você pode combinar esses aspectos para garantir que o serviço seja executado de forma eficiente em ambientes de produção, conforme descrito na lista a seguir:

* Você pode usar Azure Pipelines para garantir que as implantações sejam confiáveis e consistentes, ajudando a evitar erros humanos.
* Você pode usar Azure Monitor e Application Insights para armazenar dados de log e telemetria.
  Você pode avaliar os dados de log e métricas coletados para garantir a integridade e o desempenho de seus aplicativos. O APM (monitoramento do desempenho de aplicativos) é totalmente integrado ao serviço por meio de um agente Java. Esse agente fornece visibilidade de todos os aplicativos e dependências implantados sem a necessidade de código extra. Para obter mais informações, consulte a postagem de blog [monitore aplicativos e dependências com facilidade no Azure Spring Cloud][15].
* Você pode usar a central de segurança do Azure para garantir que os aplicativos mantenham a segurança fornecendo uma plataforma para analisar e avaliar os dados fornecidos.
* O serviço oferece suporte a vários padrões de implantação. Para obter mais informações, confira [Configurar um ambiente de preparo no Azure Spring Cloud][14].

### <a name="reliability"></a>Confiabilidade

O Azure Spring Cloud foi projetado com o AKS como um componente fundamental. Embora o AKS forneça um nível de resiliência por meio do clustering, essa arquitetura de referência incorpora serviços e considerações de arquitetura para aumentar a disponibilidade do aplicativo se houver falha de componente.

Ao se basear em um design de Hub e spoke bem definido, a base dessa arquitetura garante que você possa implantá-lo em várias regiões. Para o caso de uso do aplicativo privado, a arquitetura usa o Azure DNS privado para garantir a disponibilidade contínua durante uma falha geográfica. Para o caso de uso do aplicativo público, a porta frontal do Azure e o gateway de Aplicativo Azure garantem a disponibilidade.

### <a name="security"></a>Segurança

A segurança dessa arquitetura é abordada por sua adesão a controles definidos pelo setor e benchmarks. Os controles nessa arquitetura são da CCM ( [matriz de controle de nuvem][19] ) pela CSA ( [Cloud Security Alliance][18] ) e do [Microsoft Azure Foundations benchmark][20] (MAFB) pelo [Center for Internet Security][21] (CIS). Nos controles aplicados, o foco está nos princípios principais de design de segurança de governança, rede e segurança de aplicativos. É sua responsabilidade lidar com os princípios de design de identidade, gerenciamento de acesso e armazenamento que estejam relacionados à sua infraestrutura de destino.

#### <a name="governance"></a>Governança

O aspecto principal do controle que essa arquitetura aborda é a diferenciação do isolamento dos recursos de rede. No CCM, os DCS-08 recomendam o controle de entrada e saída para o datacenter. Para atender ao controle, a arquitetura usa um design de Hub e spoke usando NSGs (grupos de segurança de rede) para filtrar o tráfego leste-oeste entre os recursos. A arquitetura também filtra o tráfego entre os serviços centrais no Hub e os recursos no spoke. A arquitetura usa uma instância do firewall do Azure para gerenciar o tráfego entre a Internet e os recursos dentro da arquitetura.

A lista a seguir mostra o controle que aborda a segurança do datacenter nesta referência:

| ID de controle CCM de CSA | Domínio de controle CCM de CSA |
| :----------------- | :----------------------|
| DCS-08 | Entrada de pessoas não autorizadas de segurança do datacenter |

#### <a name="network"></a>Rede

O design de rede que dá suporte a essa arquitetura é derivado do Hub tradicional e do modelo de spoke. Essa decisão garante que o isolamento de rede seja uma construção básica. O controle CCM IVS-06 recomenda que o tráfego entre redes e máquinas virtuais seja restrito e monitorado entre ambientes confiáveis e não confiáveis. Essa arquitetura adota o controle por implementação do NSGs para o tráfego East-West e o Firewall do Azure para o tráfego norte-sul. O controle CCM IPY-04 recomenda que a infraestrutura use protocolos de rede seguros para a troca de dados entre serviços. Todos os serviços do Azure com suporte a essa arquitetura usam protocolos seguros padrão, como TLS para HTTP e SQL.

A lista a seguir mostra os controles CCM que abordam a segurança de rede nesta referência:

| ID de controle CCM de CSA | Domínio de controle CCM de CSA |
| :----------------- | :----------------------|
| IPY-04             | Protocolos de rede      |
| IVS-06             | Segurança de rede       |

A implementação de rede é ainda mais protegida por meio da definição de controles do MAFB. Os controles garantem que o tráfego no ambiente seja restrito da Internet pública.

A lista a seguir mostra os controles CIS que abordam a segurança de rede nesta referência:

| ID do controle CIS | Descrição do controle CIS |
| :------------- | :---------------------- |
| 6.2 | Verifique se o acesso SSH está restrito à Internet. |
| 6.3 | Certifique-se de que nenhum banco de dados SQL permita entrada 0.0.0.0/0 (qualquer IP). |
| 6.5 | Verifique se o observador de rede está ' habilitado '. |
| 6.6 | Verifique se a entrada que usa UDP está restrita à Internet. |

O Azure Spring Cloud exige o tráfego de gerenciamento para saída do Azure quando implantado em um ambiente seguro. Para fazer isso, você deve permitir as regras de rede e de aplicativo listadas nas [responsabilidades do cliente para executar o Azure Spring Cloud na VNET](./spring-cloud-vnet-customer-responsibilities.md).

#### <a name="application-security"></a>Segurança do aplicativo

Essa entidade de design abrange os componentes fundamentais de identidade, proteção de dados, gerenciamento de chaves e configuração de aplicativos. Por design, um aplicativo implantado no Azure Spring Cloud é executado com privilégios mínimos necessários para funcionar. O conjunto de controles de autorização está diretamente relacionado à proteção de dados ao usar o serviço. O gerenciamento de chaves reforça essa abordagem de segurança de aplicativos em camadas.

A lista a seguir mostra os controles CCM que abordam o gerenciamento de chaves nesta referência:

| ID de controle CCM de CSA | Domínio de controle CCM de CSA |
| :----------------- | :--------------------- |
| EKM-01 | Direitos de criptografia e gerenciamento de chaves |
| EKM-02 | Geração de chave de gerenciamento de chaves e criptografia |
| EKM-03 | Proteção de dados confidenciais de gerenciamento de chaves e criptografia |
| EKM-04 | Armazenamento e acesso de gerenciamento de chaves e criptografia |

Do CCM, o EKM-02 e o EKM-03 recomendam políticas e procedimentos para gerenciar chaves e usar protocolos de criptografia para proteger dados confidenciais. O EKM-01 recomenda que todas as chaves de criptografia tenham proprietários identificáveis para que possam ser gerenciadas. O EKM-04 recomenda o uso de algoritmos padrão.

A lista a seguir mostra os controles CIS que abordam o gerenciamento de chaves nesta referência:

| ID do controle CIS | Descrição do controle CIS |
| :------------- | :---------------------- |
| 8.1 | Verifique se a data de validade está definida em todas as chaves. |
| 8.2 | Verifique se a data de validade está definida em todos os segredos. |
| 8.4 | Verifique se o cofre de chaves é recuperável. |

Os controles CIS 8,1 e 8,2 recomendam que as datas de expiração sejam definidas para as credenciais para garantir que a rotação seja imposta. O controle CIS 8,4 garante que o conteúdo do cofre de chaves possa ser restaurado para manter a continuidade dos negócios.

Os aspectos da segurança de aplicativo definem uma base para o uso dessa arquitetura de referência para dar suporte a uma carga de trabalho Spring no Azure.

## <a name="next-steps"></a>Próximas etapas

Explore essa arquitetura de referência por meio das implantações ARM, Terraform e CLI do Azure disponíveis no repositório de [arquitetura de referência do Spring Cloud do Azure][10] .

<!-- Reference links in article -->
[1]: ./index.yml
[2]: ../key-vault/index.yml
[3]: ../azure-monitor/index.yml
[4]: ../security-center/index.yml
[5]: /azure/devops/pipelines/
[6]: ../application-gateway/index.yml
[7]: ../web-application-firewall/index.yml
[8]: ./spring-cloud-tutorial-config-server.md
[9]: https://steeltoe.io/
[10]: https://github.com/Azure/azure-spring-cloud-reference-architecture
[11]: ./spring-cloud-tutorial-deploy-in-azure-virtual-network.md#virtual-network-requirements
[12]: ./spring-cloud-vnet-customer-responsibilities.md#azure-spring-cloud-network-requirements
[13]: ./spring-cloud-vnet-customer-responsibilities.md#azure-spring-cloud-fqdn-requirements--application-rules
[14]: ./spring-cloud-howto-staging-environment.md
[15]: https://devblogs.microsoft.com/java/monitor-applications-and-dependencies-in-azure-spring-cloud/
[16]: /azure/architecture/framework/
[17]: ./spring-cloud-tutorial-deploy-in-azure-virtual-network.md#virtual-network-requirements
[18]: https://cloudsecurityalliance.org/
[19]: https://cloudsecurityalliance.org/research/working-groups/cloud-controls-matrix
[20]: https://azure.microsoft.com/resources/cis-microsoft-azure-foundations-security-benchmark/
[21]: https://www.cisecurity.org/