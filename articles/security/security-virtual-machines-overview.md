---
title: "Recursos de segurança do Azure usados nas máquinas virtuais do Azure | Microsoft Docs"
description: " Uma visão geral dos recursos de segurança centrais do Azure que podem ser usados com máquinas virtuais do Azure. As VMs do Azure oferecem a flexibilidade da virtualização sem a necessidade de comprar e manter o hardware físico que executa a VM. "
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 467b2c83-0352-4e9d-9788-c77fb400fe54
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2017
ms.author: terrylan
ms.openlocfilehash: f1fb7f876c7dc010c03f01a4f6698ddc18da1100
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-virtual-machines-security-overview"></a>Visão geral de segurança de máquinas virtuais do Azure
As máquinas virtuais do Azure permitem que você implante uma ampla gama de soluções de computação de forma ágil. Com suporte para o Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP e Serviços BizTalk do Azure, você pode implantar qualquer carga de trabalho e qualquer linguagem em praticamente qualquer sistema operacional.

Uma máquina virtual do Azure oferece a flexibilidade da virtualização sem a necessidade de comprar e manter o hardware físico que executa a máquina virtual.  Você pode compilar e implantar seus aplicativos com a garantia de que os dados estão protegidos e seguros em nossos datacenters altamente seguros.

Com o Azure, você pode compilar soluções compatíveis, com segurança avançada, que:

* Protegem suas máquinas virtuais contra vírus e malwares
* Criptografam seus dados confidenciais
* Protegem o tráfego de rede
* Identificam e detectam ameaças
* Atendem os requisitos de conformidade

O objetivo deste artigo é fornecer uma visão geral dos recursos de segurança centrais do Azure que podem ser usados com máquinas virtuais. Também fornecemos links para artigos que dão os detalhes de cada recurso para que você possa saber mais.  

As funcionalidades de segurança de máquina virtual centrais do Azure a serem abordadas nesse artigo:

* Antimalware
* Módulos de segurança de hardware
* Criptografia de disco da máquina virtual
* Backup de máquinas virtuais
* Azure Site Recovery
* Rede Virtual
* Gerenciamento de política de segurança e emissão de relatórios
* Conformidade

## <a name="antimalware"></a>Antimalware
Com o Azure, você pode usar o software antimalware dos fornecedores de segurança como Microsoft, Symantec, Trend Micro e Kaspersky para proteger suas máquinas virtuais contra arquivos maliciosos, adware e outras ameaças. Consulte a seção Saiba mais abaixo para encontrar artigos sobre soluções de parceiros.

O Microsoft Antimalware para Serviços de Nuvem e Máquinas Virtuais do Azure é uma funcionalidade de proteção em tempo real que ajuda a identificar e remover vírus, spyware e outros softwares mal-intencionados.  O Microsoft Antimalware fornece alertas configuráveis quando um software mal-intencionado ou indesejado conhecido tenta se instalar ou executar nos sistemas do Azure.

O Microsoft Antimalware é uma solução de agente único para aplicativos e ambientes de locatário, projetada para ser executada em segundo plano sem intervenção humana. Você pode implantar a proteção baseada nas necessidades de suas cargas de trabalho do aplicativo, com configuração básica padronizada ou personalizada avançada, incluindo monitoramento de antimalware.

Quando você implanta e habilita o Microsoft Antimalware, os seguintes recursos principais ficam disponíveis:

* Proteção em tempo real: monitora a atividade em Serviços de Nuvem e em máquinas virtuais para detectar e bloquear a execução de malware.
* Varredura programada: executa periodicamente uma varredura direcionada para detectar malware, incluindo programas ativamente em execução.
* Remediação de Malware: atua automaticamente sobre o malware detectado, por exemplo, excluindo ou colocando arquivos mal-intencionados em quarentena e limpando entradas de Registro mal-intencionadas.
* Atualizações de assinatura: instala automaticamente as últimas assinaturas de proteção (definições de vírus) para garantir que a proteção seja atualizada em uma frequência predeterminada.
* Atualizações de mecanismo Antimalware: atualiza automaticamente o mecanismo Microsoft Antimalware.
* Atualizações da plataforma Antimalware: atualiza automaticamente a plataforma Microsoft Antimalware.
* Proteção ativa: relatórios para os metadados de telemetria do Azure sobre as ameaças detectadas e recursos suspeitos para garantir uma resposta rápida e permite a entrega de assinatura síncrona em tempo real por meio do Microsoft Active Protection System (MAPS).
* Relatórios de exemplos: fornece e relata exemplos para o serviço Microsoft Antimalware para ajudar a aprimorar o serviço e a habilitar a solução de problemas.
* Exclusões: permite que os administradores de serviço e aplicativo configurem determinados arquivos, processos e unidades para excluí-los da proteção e da verificação por motivos de desempenho e outros.
* Coleta de eventos de Antimalware: registra a integridade do serviço de antimalware, atividades suspeitas e ações de remediação realizadas no log de eventos do sistema operacional e os coleta na conta de armazenamento do Azure do cliente.

Saiba mais: para saber mais sobre o software antimalware para proteger as máquinas virtuais, consulte:

* [Microsoft Antimalware para Serviços de Nuvem do Azure e máquinas virtuais](azure-security-antimalware.md)
* [Implantando soluções antimalware em máquinas virtuais do Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Como instalar e configurar o Trend Micro Deep Security as a Service em uma VM do Windows](../virtual-machines/windows/classic/install-trend.md)
* [Como instalar e configurar o Symantec Endpoint Protection em uma VM do Windows](../virtual-machines/windows/classic/install-symantec.md)
* [Soluções de segurança no Azure Marketplace](https://azure.microsoft.com/marketplace/?term=security)

## <a name="hardware-security-module"></a>Módulos de segurança de hardware
As proteções de criptografia e autenticação podem ser aprimoradas, melhorando a segurança da chave. Você pode simplificar o gerenciamento e a segurança dos seus principais segredos e chaves armazenando-os no Cofre de Chaves do Azure. O Cofre de Chaves oferece a opção de armazenar as chaves em HSMs (módulos de segurança de hardware) certificados para os padrões FIPS 140-2 nível 2. Suas chaves de criptografia do SQL Server para backup ou [Transparent Data Encryption](https://msdn.microsoft.com/library/bb934049.aspx) podem ser armazenadas no Cofre de Chaves com quaisquer chaves ou segredos dos seus aplicativos. As permissões e o acesso a esses itens protegidos são gerenciados pelo [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

Saiba mais:

* [O que é o Cofre da Chave do Azure?](../key-vault/key-vault-whatis.md)
* [Introdução ao Cofre da Chave do Azure](../key-vault/key-vault-get-started.md)
* [Blog do Cofre de Chaves do Azure](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>Criptografia de disco da máquina virtual
O Azure Disk Encryption é uma nova funcionalidade que permite criptografar os discos de suas máquinas virtuais Windows e Linux do Azure. O Azure Disk Encryption usa o recurso [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) do Windows padrão do setor e o recurso [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer uma criptografia de volume para o sistema operacional e os discos de dados.

A solução é integrada ao Cofre de Chaves do Azure para ajudá-lo a controlar e a gerenciar as chaves de criptografia de disco e segredos em sua assinatura de cofre de chaves, garantindo ao mesmo tempo que todos os dados nos discos de máquina virtual sejam criptografados em repouso no armazenamento do Azure.

Saiba mais:

* [Criptografia de Disco do Azure para VMs IaaS Windows e Linux](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)
* [Azure Disk Encryption for Linux and Windows Virtual Machines (Azure Disk Encryption para máquinas virtuais Windows e Linux)](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview-now-available/)
* [Criptografar uma máquina virtual](../security-center/security-center-disk-encryption.md)

## <a name="virtual-machine-backup"></a>Backup de máquinas virtuais
O Backup do Azure é uma solução escalonável que protege os dados do seu aplicativo com zero investimento de capital e custos operacionais mínimos. Erros de aplicativo podem corromper seus dados e erros humanos podem introduzir bugs em seus aplicativos. Com o Backup do Azure, suas máquinas virtuais executando Windows e Linux estão protegidas.

Saiba mais:

* [O que é o Backup do Azure?](../backup/backup-introduction-to-azure-backup.md)
* [Roteiro de aprendizagem do Backup do Azure](https://azure.microsoft.com/documentation/learning-paths/backup/)
* [Serviço de Backup do Azure - Perguntas frequentes](../backup/backup-azure-backup-faq.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery
Uma parte importante da estratégia de BCDR de sua organização é descobrir como manter as cargas de trabalho corporativas e aplicativos em execução durante interrupções planejadas e não planejadas. O Azure Site Recovery ajuda a orquestrar a replicação, failover e recuperação dos aplicativos e cargas de trabalho para que eles estejam disponíveis a partir de um local secundário, caso o local principal fique inativo.

Recuperação de Site:

* **Simplifica sua estratégia de BCDR** : a Recuperação de Site facilita lidar com a replicação, failover e recuperação de várias cargas de trabalho e aplicativos corporativos a partir de um único local. A Recuperação de Site orquestra a replicação e o failover, mas não intercepta os dados do aplicativo ou tem quaisquer informações sobre ele.
* **Fornece replicação flexível** : com a Recuperação de Site, você pode replicar cargas de trabalho em execução em máquinas virtuais Hyper-V, máquinas virtuais VMware e servidores físicos com Windows/Linux.
* **Dá suporte ao failover e à recuperação** : a Recuperação de Site fornece failovers de teste para dar suporte a simulações de recuperação de desastre sem afetar os ambientes de produção. Você também pode executar failovers planejados sem perder qualquer dado durante interrupções esperadas, ou failovers não planejados com perda mínima de dados (dependendo da frequência de replicação) para desastres inesperados. Após o failover, você pode realizar o failback para seus sites principais. A Recuperação de Site fornece planos de recuperação que podem incluir scripts e pastas de trabalho de automação do Azure, para que você possa personalizar o failover e a recuperação de aplicativos de várias camadas.
* **Elimina o datacenter secundário** : você pode replicar para um site secundário local ou para o Azure. Usar o Azure como um destino de recuperação de desastres elimina o custo e a complexidade de manter um site secundário. Os dados replicados são colocados no Armazenamento do Azure.
* **Integra com tecnologias de BCDR existentes** : a Recuperação de Site estabelece uma parceria com outros recursos de BCDR do aplicativo. Por exemplo, você pode usar a Recuperação de Site para proteger o back-end do SQL Server das cargas de trabalho corporativas. Isso inclui o suporte nativo para o SQL Server AlwaysOn gerenciar o failover dos grupos de disponibilidade.

Saiba mais:

* [O que é o Azure Site Recovery?](../site-recovery/site-recovery-overview.md)
* [Como funciona o Azure Site Recovery?](../site-recovery/site-recovery-components.md)
* [Quais cargas de trabalho são protegidas pelo Azure Site Recovery?](../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>Rede Virtual
As máquinas virtuais precisam de conectividade de rede. Para dar suporte a esse requisito, o Azure exige que as máquinas virtuais sejam conectadas a uma Rede Virtual do Azure. Uma Rede Virtual do Azure é um constructo lógico criado na malha de rede física do Azure. Cada Rede Virtual do Azure lógica é isolada das todas as outras Redes Virtuais do Azure. Esse isolamento ajuda a garantir que o tráfego da rede nas implantações não seja acessível para os outros clientes do Microsoft Azure.

Saiba mais:

* [Azure Network Security Overview (Visão geral da segurança de rede do Azure)](security-network-overview.md)
* [Visão geral da Rede Virtual](../virtual-network/virtual-networks-overview.md)
* [Networking features and partnerships for Enterprise scenarios (Recursos de rede e parcerias para cenários empresariais)](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Gerenciamento de política de segurança e emissão de relatórios
A Central de Segurança do Azure ajuda você a impedir, detectar e responder a ameaças e oferece maior visibilidade e controle sobre a segurança dos recursos do Azure Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas do Azure, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

A Central de Segurança do Azure ajuda a otimizar e a monitorar a segurança da máquina virtual:

* Fornecendo [recomendações de segurança](../security-center/security-center-recommendations.md) para a máquina virtual, como aplicar atualizações do sistema, configurar pontos de extremidade de ACLs, habilitar o antimalware, habilitar grupos de segurança de rede e aplicar a criptografia de disco.
* Monitorando o estado de suas máquinas virtuais

Saiba mais:

* [Introdução à Central de Segurança do Azure](../security-center/security-center-intro.md)
* [Perguntas frequentes sobre a Central de Segurança do Azure](../security-center/security-center-faq.md)
* [Planejamento e operações da Central de Segurança do Azure](../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>Conformidade
As Máquinas Virtuais do Azure são certificados para FISMA, FedRAMP, HIPAA, PCI DSS Nível 1 e outros programas de conformidade de chaves. Essa certificação facilita que seus próprios aplicativos do Azure atendam aos requisitos de conformidade e que sua empresa enderece uma ampla variedade de requisitos normativos nacionais e internacionais.

Saiba mais:

* [Microsoft Trust Center: Compliance (Central de Confiabilidade da Microsoft: conformidade)](https://www.microsoft.com/TrustCenter/Compliance/default.aspx)
* [Trusted Cloud: Microsoft Azure Security, Privacy, and Compliance (A nuvem confiável: segurança, privacidade e conformidade do Microsoft Azure)](http://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)
