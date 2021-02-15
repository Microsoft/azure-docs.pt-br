---
title: Recursos de segurança usados com VMs do Azure
titleSuffix: Azure security
description: Este artigo fornece uma visão geral dos recursos de segurança centrais do Azure que podem ser usados com máquinas virtuais do Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: 467b2c83-0352-4e9d-9788-c77fb400fe54
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/2/2019
ms.author: terrylan
ms.openlocfilehash: ed2cb967b24ce5abdebadfe29847ae1a16b4e745
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498737"
---
# <a name="azure-virtual-machines-security-overview"></a>Visão geral de segurança de máquinas virtuais do Azure
Este artigo fornece uma visão geral dos principais recursos de segurança do Azure que podem ser usados com máquinas virtuais.

Use as Máquinas Virtuais do Azure para implantar uma ampla gama de soluções de computação com agilidade. O serviço dá suporte ao Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP e Serviços BizTalk do Azure. Portanto, você pode implantar qualquer carga de trabalho e qualquer linguagem em praticamente qualquer sistema operacional.

Uma máquina virtual do Azure oferece a flexibilidade da virtualização sem a necessidade de comprar e manter o hardware físico que executa a máquina virtual. Crie e implante aplicativos com a garantia de que seus dados estarão protegidos e seguros em datacenters altamente seguros.

Com o Azure, você pode compilar soluções compatíveis, com segurança avançada, que:

* Protegem suas máquinas virtuais contra vírus e malware.
* Criptografam seus dados confidenciais.
* Protegem o tráfego de rede.
* Identificam e detectam ameaças.
* Atendem aos requisitos de conformidade.  

## <a name="antimalware"></a>Antimalware

Com o Azure, você pode usar um software antimalware de fornecedores de segurança, como a Microsoft, Symantec, Trend Micro e Kaspersky. Esse software ajuda a proteger suas máquinas virtuais contra arquivos mal-intencionados, adware e outras ameaças.

O Microsoft Antimalware para Serviços de Nuvem e Máquinas Virtuais do Azure é uma funcionalidade de proteção em tempo real que ajuda a identificar e remover vírus, spyware e outros softwares mal-intencionados.  O Microsoft Antimalware para Azure fornece alertas configuráveis quando um software mal-intencionado ou indesejado conhecido tenta se instalar ou ser executado nos sistemas do Azure.

O Microsoft Antimalware para Azure é uma solução de agente único para aplicativos e ambientes de locatário. Foi projetado para execução em segundo plano sem intervenção humana. Você pode implantar a proteção baseada nas necessidades de suas cargas de trabalho do aplicativo, com configuração básica padronizada ou personalizada avançada, incluindo monitoramento de antimalware.

Saiba mais sobre o [Microsoft antimalware para Azure](antimalware.md) e os principais recursos disponíveis.

Saiba mais sobre o software antimalware para ajudar a proteger suas máquinas virtuais:

* [Implantando soluções antimalware em máquinas virtuais do Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Como instalar e configurar o Trend Micro Deep Security as a Service em uma VM do Windows](../../virtual-machines/extensions/trend.md)
* [Como instalar e configurar o Symantec Endpoint Protection em uma VM do Windows](../../virtual-machines/extensions/symantec.md)
* [Soluções de segurança no Azure Marketplace](https://azure.microsoft.com/marketplace/?term=security)

Para uma proteção ainda mais poderosa, considere o uso da [Proteção Avançada contra Ameaças do Windows Defender](/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection). Com o Windows Defender ATP, você obtém:

* [Redução da superfície de ataque](/windows/security/threat-protection/windows-defender-atp/overview-attack-surface-reduction)  
* [Proteção de próxima geração](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)  
* [Resposta e a proteção de ponto de extremidade](/windows/security/threat-protection/windows-defender-atp/overview-endpoint-detection-response)
* [Correção e investigação automatizada](/windows/security/threat-protection/windows-defender-atp/automated-investigations-windows-defender-advanced-threat-protection)
* [Pontuação segura](/windows/security/threat-protection/microsoft-defender-atp/tvm-microsoft-secure-score-devices)
* [Procura avançada](/windows/security/threat-protection/windows-defender-atp/overview-hunting-windows-defender-advanced-threat-protection)
* [Gerenciamento e APIs](/windows/security/threat-protection/windows-defender-atp/management-apis)
* [Proteção contra Ameaças da Microsoft](/windows/security/threat-protection/windows-defender-atp/threat-protection-integration)

Saiba mais:

* [Introdução ao WDATP](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)  
* [Visão geral dos recursos WDATP](/windows/security/threat-protection/windows-defender-atp/overview)  

## <a name="hardware-security-module"></a>Módulo de segurança de hardware

A melhoria da segurança das chaves pode aprimorar as proteções de criptografia e de autenticação. Você pode simplificar o gerenciamento e a segurança dos seus principais segredos e chaves armazenando-os no Cofre de Chaves do Azure.

O Cofre de Chaves oferece a opção de armazenar as chaves em HSMs (módulos de segurança de hardware) certificados para os padrões FIPS 140-2 nível 2. Suas chaves de criptografia do SQL Server para backup ou [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) podem ser armazenadas no Cofre de Chaves com quaisquer chaves ou segredos dos seus aplicativos. As permissões e o acesso a esses itens protegidos são gerenciados pelo [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

Saiba mais:

* [O que é o Cofre da Chave do Azure?](../../key-vault/general/overview.md)
* [Blog do Cofre de Chaves do Azure](/archive/blogs/kv/)

## <a name="virtual-machine-disk-encryption"></a>Criptografia de disco da máquina virtual

O Azure Disk Encryption é uma nova funcionalidade para criptografia de discos de máquinas virtuais do Windows e Linux. O Azure Disk Encryption usa o recurso [BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) padrão do setor do Windows e o recurso [DM-cript](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer criptografia de volume para o sistema operacional e os discos de dados.

A solução é integrada ao Azure Key Vault para ajudá-lo a controlar e gerenciar os segredos e as chaves de criptografia de disco em sua assinatura do cofre de chaves. Ela também garante que todos os dados nos discos da máquina virtual sejam criptografados em repouso no Armazenamento do Azure.

Saiba mais:

* [Azure Disk Encryption para VMs de IaaS](./azure-disk-encryption-vms-vmss.md)
* [Início Rápido: Criptografar uma VM de IaaS do Windows com o Azure PowerShell](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)

## <a name="virtual-machine-backup"></a>Backup de máquinas virtuais

O Backup do Azure é uma solução escalonável que ajuda a proteger os dados de seu aplicativo com zero investimento de capital e custos operacionais mínimos. Erros de aplicativo podem corromper seus dados e erros humanos podem introduzir bugs em seus aplicativos. Com o Backup do Azure, suas máquinas virtuais executando Windows e Linux estão protegidas.

Saiba mais:

* [O que é o Backup do Azure?](../../backup/backup-overview.md)
* [Perguntas frequentes do serviço de backup do Azure](../../backup/backup-azure-backup-faq.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery

Uma parte importante da estratégia de BCDR de sua organização é descobrir como manter cargas de trabalho e aplicativos corporativos em execução durante interrupções planejadas e não planejadas. O Azure Site Recovery ajuda a orquestrar a replicação, o failover e a recuperação de cargas de trabalho e aplicativos, de modo que eles estejam disponíveis em um local secundário, caso o local primário fique inativo.

Recuperação de Site:

* **Simplifica sua estratégia de BCDR**: o Site Recovery facilita lidar com a replicação, o failover e a recuperação de várias cargas de trabalho e aplicativos de negócios em um só local. O Site Recovery orquestra a replicação e o failover, mas não intercepta os dados do aplicativo nem mantém informações sobre ele.
* **Fornece uma replicação flexível**: usando o Site Recovery, você pode replicar cargas de trabalho em execução em máquinas virtuais do Hyper-V e do VMware e em servidores físicos do Windows/Linux.
* **Dá suporte a failover e recuperação**: o Site Recovery fornece failovers de teste para dar suporte a simulações de recuperação de desastre sem afetar os ambientes de produção. Você também pode executar failovers planejados sem perder qualquer dado durante interrupções esperadas, ou failovers não planejados com perda mínima de dados (dependendo da frequência de replicação) para desastres inesperados. Após o failover, faça failback para os sites primários. A Recuperação de Site fornece planos de recuperação que podem incluir scripts e pastas de trabalho de automação do Azure, para que você possa personalizar o failover e a recuperação de aplicativos de várias camadas.
* **Elimina datacenters secundários**: você pode fazer a replicação para um site local secundário ou para o Azure. Usar o Azure como um destino de recuperação de desastres elimina o custo e a complexidade de manter um site secundário. Os dados replicados são colocados no Armazenamento do Azure.
* **Integra-se às tecnologias de BCDR existentes**: o Site Recovery faz uma parceria com outros recursos de BCDR dos aplicativos. Por exemplo, você pode usar o Site Recovery para ajudar a proteger o back-end do SQL Server de cargas de trabalho corporativas. Isso inclui o suporte nativo ao SQL Server Always On para gerenciar o failover de grupos de disponibilidade.

Saiba mais:

* [O que é Azure Site Recovery?](../../site-recovery/site-recovery-overview.md)
* [Como funciona Azure Site Recovery?](../../site-recovery/azure-to-azure-architecture.md)
* [Quais cargas de trabalho são protegidas por Azure Site Recovery?](../../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>Rede Virtual

As máquinas virtuais precisam de conectividade de rede. Para dar suporte a esse requisito, o Azure exige que as máquinas virtuais estejam conectadas a uma Rede Virtual do Azure.

Uma Rede Virtual do Azure é um constructo lógico criado na malha de rede física do Azure. Cada rede virtual lógica do Azure é isolada de todas as outras redes virtuais do Azure. Esse isolamento ajuda a garantir que o tráfego da rede nas implantações não seja acessível para os outros clientes do Microsoft Azure.

Saiba mais:

* [Visão geral da segurança de rede do Azure](network-overview.md)
* [Visão geral da Rede Virtual](../../virtual-network/virtual-networks-overview.md)
* [Recursos de rede e parcerias para cenários empresariais](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Gerenciamento de política de segurança e emissão de relatórios

A Central de Segurança do Azure ajuda você a evitar, detectar e responder a ameaças. A Central de Segurança proporciona a você maior visibilidade e controle da segurança de seus recursos do Azure. Ela fornece monitoramento de segurança integrado e gerenciamento de políticas em suas assinaturas do Azure. Ela ajuda a detectar ameaças que poderiam não ser notadas de outra forma e funciona com um amplo ecossistema de soluções de segurança.

A Central de Segurança ajuda você a otimizar e monitorar a segurança de suas máquinas virtuais:

* Fornecendo [recomendações de segurança](../../security-center/security-center-recommendations.md) para as máquinas virtuais. Entre os exemplos de recomendações estão a aplicação de atualizações do sistema, a configuração de pontos de extremidade de ACLs, a habilitação de antimalware, a habilitação de grupos de segurança de rede e a aplicação da criptografia de disco.
* Monitorando o estado das máquinas virtuais.

Saiba mais:

* [Introdução à Central de Segurança do Azure](../../security-center/security-center-introduction.md)
* [Perguntas frequentes sobre a Central de Segurança do Azure](../../security-center/faq-general.md)
* [Planejamento e operações da Central de Segurança do Azure](../../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>Conformidade

As Máquinas Virtuais do Azure são certificados para FISMA, FedRAMP, HIPAA, PCI DSS Nível 1 e outros programas de conformidade de chaves. Essa certificação facilita que seus próprios aplicativos do Azure atendam aos requisitos de conformidade e que sua empresa enderece uma ampla variedade de requisitos normativos nacionais e internacionais.

Saiba mais:

* [Microsoft Trust Center: Compliance (Central de Confiabilidade da Microsoft: conformidade)](https://www.microsoft.com/en-us/trustcenter/compliance)
* [Trusted Cloud: Microsoft Azure Security, Privacy, and Compliance (A nuvem confiável: segurança, privacidade e conformidade do Microsoft Azure)](https://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)

## <a name="confidential-computing"></a>Computação Confidencial

Embora a computação confidencial não seja tecnicamente parte da segurança da máquina virtual, o tópico da segurança da máquina virtual pertence ao assunto de "computação" de nível superior. A computação confidencial pertence dentro da categoria de segurança de "computação".

A computação confidencial garante que quando os dados estiverem "em claro", o que é necessário para um processamento eficiente, os dados serão protegidos dentro de um ambiente de execução confiável  https://en.wikipedia.org/wiki/Trusted_execution_environment (também conhecido como enclave), um exemplo do que é mostrado na figura abaixo.  

Os TEEs garantem que não há como visualizar os dados ou as operações internas de fora, mesmo com um depurador. Eles até garantem que apenas o código autorizado tenha permissão para acessar os dados. Se o código for alterado ou adulterado, as operações serão negadas e o ambiente desativado. O TEE aplica essas proteções durante a execução do código dentro dele.

Saiba mais:

* [Apresentando a computação confidencial do Azure](https://azure.microsoft.com/blog/introducing-azure-confidential-computing/)  
* [Computação confidencial do Azure](https://azure.microsoft.com/blog/azure-confidential-computing/)  

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [as práticas recomendadas de segurança](iaas.md) para VMs e sistemas operacionais.