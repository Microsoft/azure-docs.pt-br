---
title: Perguntas frequentes - HSM Dedicado do Azure | Microsoft Docs
description: Encontre respostas para perguntas comuns sobre o módulo de segurança de hardware dedicado do Azure, como informações básicas, interoperabilidade, alta disponibilidade e suporte.
services: dedicated-hsm
author: keithp
manager: rkarlin
tags: azure-resource-manager
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: f6705b5f72aaa8f990aaf3b1daa33697b690d1f1
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608877"
---
# <a name="frequently-asked-questions-faq"></a>Perguntas frequentes

Encontre respostas para perguntas comuns sobre o HSM Dedicado do Azure da Microsoft.

## <a name="the-basics"></a>Noções básicas

### <a name="q-what-is-a-hardware-security-module-hsm"></a>P: O que é um módulo de segurança de hardware (HSM)?

Um Módulo de Segurança de Hardware (HSM) é um dispositivo de computação física usado para proteger e gerenciar chaves criptográficas. Chaves armazenadas em HSMs podem ser usadas para operações criptográficas. O material da chave permanece com segurança em módulos de hardware invioláveis e invioláveis. O HSM permite apenas que aplicativos autenticados e autorizados usem as chaves. O material chave nunca sai do limite de proteção do HSM.

### <a name="q-what-is-the-azure-dedicated-hsm-offering"></a>P: Qual é a oferta HSM dedicada do Azure?

O HSM Dedicado do Azure é um serviço baseado em nuvem que fornece HSMs hospedados em datacenters do Azure que são conectados diretamente à rede virtual de um cliente. Esses HSMs são dispositivos dedicados de rede [HSM Thales Luna 7](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) . Eles são implementados diretamente no espaço de endereço IP privado de um cliente e a Microsoft não tem acesso à funcionalidade criptográfica dos HSMs. Somente o cliente tem total controle administrativo e criptográfico sobre esses dispositivos. Os clientes são responsáveis pelo gerenciamento do dispositivo e podem obter logs de atividade completos diretamente de seus dispositivos. Os HSMs Dedicados ajudam os clientes a atender aos requisitos normativos/de conformidade, como FIPS 140-2 Nível 3, HIPAA, PCI-DSS e eIDAS e muitos outros.

### <a name="q-what-hardware-is-used-for-dedicated-hsm"></a>P: qual hardware é usado para HSM dedicado?

A Microsoft estabeleceu uma parceria com a Thales para fornecer o serviço HSM dedicado do Azure. O dispositivo específico usado é o [modelo de HSM Thales Luna 7 A790](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms). Esse dispositivo não apenas fornece um firmware validado [de nível 3 de FIPS 140-2](https://csrc.nist.gov/publications/detail/fips/140/2/final) , mas também oferece baixa latência, alto desempenho e alta capacidade por meio de 10 partições. 

### <a name="q-what-is-an-hsm-used-for"></a>P: o que um HSM é usado?

Os HSMs são usados para armazenar chaves criptográficas que são usadas para funcionalidades criptográficas, como TLS (segurança da camada de transporte), criptografia de dados, PKI (infraestrutura de chave pública), DRM (gerenciamento de direitos digitais) e documentos de assinatura.

### <a name="q-how-does-dedicated-hsm-work"></a>P: Como funciona o HSM Dedicado?

Os clientes podem provisionar HSMs em regiões específicas usando o PowerShell ou a interface de linha de comando. O cliente especifica a qual rede virtual os HSMs serão conectados e, uma vez provisionados, os HSMs estarão disponíveis na sub-rede designada nos endereços IP designados no espaço de endereço IP privado do cliente. Em seguida, os clientes podem se conectar aos HSMs usando SSH para gerenciamento e administração de dispositivos, configurar conexões de cliente HSM, inicializar HSMs, criar partições, definir e designar funções como o oficial de partição, o agente de criptografia e o usuário de criptografia. Em seguida, o cliente usará as ferramentas de cliente HSM/SDK/software do Thales para executar operações criptográficas de seus aplicativos.

### <a name="q-what-software-is-provided-with-the-dedicated-hsm-service"></a>P: Qual software é fornecido com o serviço HSM Dedicado?

O Thales fornece todo o software para o dispositivo HSM depois de provisionado pela Microsoft. O software está disponível no [portal de atendimento ao cliente do Thales](https://supportportal.thalesgroup.com/csm). Os clientes que usam o serviço HSM dedicado devem ser registrados para o suporte do Thales e ter uma ID de cliente que habilite o acesso e o download do software relevante. O software cliente com suporte é a versão 7,2, que é compatível com a versão de firmware validada pelo FIPS 140-2 nível 3 7.0.3. 

### <a name="q-what-extra-costs-may-be-incurred-with-dedicated-hsm-service"></a>P: quais custos extras podem ser incorridos com o serviço HSM dedicado?

Os itens a seguir incorrerão em custo extra ao usar o serviço HSM dedicado. 
* O uso de um dispositivo de backup local dedicado é viável para usar com o serviço HSM dedicado, no entanto, isso incorrerá em um custo extra e deverá ser diretamente originado do Thales.
* O HSM dedicado é fornecido com uma licença de 10 partições. Se um cliente precisar de mais partições, isso incorrerá em um custo extra para licenças adicionais diretamente originadas do Thales.
* O HSM dedicado requer infraestrutura de rede (VNET, gateway de VPN, etc.) e recursos como máquinas virtuais para configuração de dispositivo. Esses recursos adicionais incorrerão em custos adicionais e não serão incluídos no preço do serviço HSM dedicado.

### <a name="q-does-azure-dedicated-hsm-offer-password-based-and-ped-based-authentication"></a>P: o HSM dedicado do Azure oferece autenticação baseada em senha e em PED?

Neste momento, o HSM Dedicado do Azure fornece apenas HSMs com autenticação baseada em senha.

### <a name="q-will-azure-dedicated-hsm-host-my-hsms-for-me"></a>P: o HSM dedicado do Azure vai hospedar meus HSMs para mim?

A Microsoft oferece apenas o modelo Thales Luna 7 HSM A790 por meio do serviço HSM dedicado e não pode hospedar nenhum dispositivo fornecido pelo cliente.

### <a name="q-does-azure-dedicated-hsm-support-payment-pineft-features"></a>P: os recursos de pagamento (PIN/TEF) de suporte do HSM dedicado do Azure?

O serviço HSM dedicado do Azure usa HSMs Thales Luna 7. Esses dispositivos não dão suporte à funcionalidade específica do HSM de pagamento (como PIN ou TEF) ou certificações. Se você quiser que o serviço HSM dedicado do Azure ofereça suporte a HSMs de pagamento no futuro, passe os comentários para seu representante de conta da Microsoft.

### <a name="q-which-azure-regions-is-dedicated-hsm-available-in"></a>P: em quais regiões do Azure o HSM dedicado está disponível?

Desde o final de março de 2019, o HSM dedicado está disponível nas 14 regiões listadas abaixo. Outras regiões são planejadas e podem ser discutidas por meio de seu representante de conta da Microsoft.

* Leste dos EUA
* Leste dos EUA 2
* Oeste dos EUA
* Oeste dos EUA 2
* Centro-Sul dos Estados Unidos
* Sudeste Asiático
* Leste da Ásia
* Centro da Índia
* Sul da Índia
* Leste do Japão
* Oeste do Japão
* Norte da Europa
* Europa Ocidental
* Sul do Reino Unido
* Oeste do Reino Unido
* Canadá Central
* Leste do Canadá
* Leste da Austrália
* Sudeste da Austrália
* Norte da Suíça
* Oeste da Suíça
* Gov. dos EUA – Virgínia
* Governo dos EUA do Texas

## <a name="interoperability"></a>Interoperabilidade

### <a name="q-how-does-my-application-connect-to-a-dedicated-hsm"></a>P: Como meu aplicativo se conecta a um HSM Dedicado?

Você usa Thales ferramentas de cliente HSM/SDK/software para executar operações criptográficas de seus aplicativos. O software está disponível no [portal de atendimento ao cliente do Thales](https://supportportal.thalesgroup.com/csm). Os clientes que usam o serviço HSM dedicado devem ser registrados para o suporte do Thales e ter uma ID de cliente que habilite o acesso e o download do software relevante.

### <a name="q-can-an-application-connect-to-dedicated-hsm-from-a-different-vnet-in-or-across-regions"></a>P: Um aplicativo pode se conectar ao HSM Dedicado de uma VNET diferente em regiões ou entre regiões?

Sim, você precisará usar [emparelhamento VNET](../virtual-network/virtual-network-peering-overview.md) dentro de uma região para estabelecer conectividade entre redes virtuais. Para conectividade entre regiões, você deve usar [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="q-can-i-synchronize-dedicated-hsm-with-on-premises-hsms"></a>P: Posso sincronizar o HSM Dedicado com HSMs locais?

Sim, você pode sincronizar HSMs locais com o HSM Dedicado. [VPN ponto-a-ponto ou conectividade ponto-a-ponto](../vpn-gateway/vpn-gateway-about-vpngateways.md) pode ser usada para estabelecer conectividade com sua rede local.

### <a name="q-can-i-encrypt-data-used-by-other-azure-services-using-keys-stored-in-dedicated-hsm"></a>P: Posso criptografar dados usados por outros serviços do Azure usando chaves armazenadas no HSM Dedicado?

Não. Os HSMs Dedicados do Azure só podem ser acessados de dentro da sua rede virtual.

### <a name="q-can-i-import-keys-from-an-existing-on-premises-hsm-to-dedicated-hsm"></a>P: Posso importar chaves de um HSM local para um HSM Dedicado?

Sim, se você tiver Thaless locais na Luna 7. Existem vários métodos. Consulte a [documentação do Thales HSM](https://thalesdocs.com/gphsm/luna/7.2/docs/network/Content/Home_network.htm).

### <a name="q-what-operating-systems-are-supported-by-dedicated-hsm-client-software"></a>P: Quais sistemas operacionais são suportados pelo software cliente de HSM Dedicado?

* Windows, Linux, Solaris, AIX, HP-UX, FreeBSD
* Virtual: VMware, Hyper-V, Xen, KVM

### <a name="q-how-do-i-configure-my-client-application-to-create-a-high-availability-configuration-with-multiple-partitions-from-multiple-hsms"></a>P: Como configuro meu aplicativo cliente para criar uma configuração de alta disponibilidade com várias partições de vários HSMs?

Para ter alta disponibilidade, você precisa configurar sua configuração de aplicativo cliente HSM para usar partições de cada HSM. Consulte a documentação do software cliente do Thales HSM.

### <a name="q-what-authentication-mechanisms-are-supported-by-dedicated-hsm"></a>P: Quais mecanismos de autenticação são suportados pelo HSM Dedicado?

O HSM dedicado do Azure usa dispositivos [A790 de modelo HSM Thales Luna 7](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) e eles oferecem suporte à autenticação baseada em senha.

### <a name="q-what-sdks-apis-client-software-is-available-to-use-with-dedicated-hsm"></a>P: Quais SDKs, APIs, software cliente estão disponíveis para uso com o HSM Dedicado?

PKCS#11, Java (JCA / JCE), CAPI da Microsoft e CNG, OpenSSL

### <a name="q-can-i-importmigrate-keys-from-luna-56-hsms-to-azure-dedicated-hsms"></a>P: Posso importar/migrar chaves de HSMs do Luna 5/6 HSMs para  HMS Dedicado do Azure?

Sim. Entre em contato com o representante do Thales para obter o guia de migração do Thales apropriado. 

## <a name="using-your-hsm"></a>Usando seu HSM

### <a name="q-how-do-i-decide-whether-to-use-azure-key-vault-or-azure-dedicated-hsm"></a>P: Como decidir se devo usar o Azure Key Vault ou o HSM Dedicado do Azure?

O HSM Dedicado do Azure é a escolha apropriada para empresas migrando para aplicativos locais do Azure que usam HSMs. HSMs Dedicados apresentam uma opção para migrar um aplicativo com alterações mínimas. Se operações criptográficas forem executadas no código do aplicativo em execução em uma VM do Azure ou no Web App, elas poderão usar o HSM Dedicado. Em geral, o software comprimido em execução em modelos IaaS (infraestrutura como serviço), que dão suporte a HSMs como um repositório de chaves pode usar HSM dedicado, como o gateway de aplicativo ou o Gerenciador de tráfego para o TLS, ADCS (serviços de certificados Active Directory) ou ferramentas de PKI semelhantes, ferramentas/aplicativos usados para assinatura de documento, assinatura de código ou um SQL Server (IaaS) configurado com TDE (criptografia de banco de dados transparente) com chave mestra em um HSM usando um provedor Azure Key Vault é adequado para aplicativos "transmitidos para a nuvem" ou para cenários de criptografia em repouso em que os dados do cliente são processados por cenários de PaaS (plataforma como serviço) ou SaaS (software como serviço), como a chave do cliente do Office 365, a proteção de informações do Azure, Azure Disk Encryption, Azure Data Lake Store criptografia com chave gerenciada pelo cliente, criptografia de armazenamento do Azure com chave gerencia

### <a name="q-what-usage-scenarios-best-suit-azure-dedicated-hsm"></a>P: Quais cenários de uso melhor se adequam ao HSM Dedicado do Azure?

O HSM Dedicado do Azure é mais adequado para cenários de migração. Isso significa que, se você estiver migrando aplicativos locais para o Azure que já estão usando HSMs. Isso fornece uma opção de baixa fricção para migrar para o Azure com alterações mínimas no aplicativo. Se operações criptográficas forem executadas no código do aplicativo em execução na VM do Azure ou no Web App, o HSM Dedicado pode ser usado. Em geral, o software encapsulado em execução nos modelos IaaS (Infraestrutura como Serviço), que suportam HSMs como armazenamento de chaves, pode usar o Dedicate HSM, como:

* Gateway de aplicativo ou Gerenciador de tráfego para TLS de menos
* ADCS (Serviços de Certificados do Active Directory)
* Ferramentas semelhantes de PKI
* Ferramentas/aplicativos usados para assinatura de documentos
* Assinatura de código
* SQL Server (IaaS) configurado com TDE (criptografia de banco de dados transparente) com chave mestra em um HSM usando um provedor EKM (gerenciamento extensível de chaves)

### <a name="q-can-dedicated-hsm-be-used-with-office-365-customer-key-azure-information-protection-azure-data-lake-store-disk-encryption-azure-storage-encryption-azure-sql-tde"></a>P: O HSM dedicado pode ser usado com a chave do cliente do Office 365, a Proteção de Informações do Azure, o armazenamento do Azure Data Lake, a criptografia de disco, a criptografia de armazenamento do Azure e o Azure SQL TDE?

Não. O HSM dedicado é provisionado diretamente no espaço de endereço IP privado de um cliente para que ele não possa ser acessado por outros serviços do Azure ou da Microsoft.

## <a name="administration-access-and-control"></a>Administração, acesso e controle

### <a name="q-does-the-customer-get-full-exclusive-control-over-the-hsms-with-dedicated-"></a>P: o cliente obtém total controle exclusivo sobre os HSMs com o dedicado '?

Sim. Cada dispositivo HSM é totalmente dedicado a um único cliente e ninguém mais tem controle administrativo depois de provisionado e a senha do administrador é alterada.

### <a name="q-what-level-of-access-does-microsoft-have-to-my-hsm"></a>P: Qual o nível de acesso da Microsoft ao meu HSM?

A Microsoft não possui nenhum controle administrativo ou criptográfico sobre o HSM. A Microsoft possui acesso em nível de monitor via conexão de porta serial para recuperar telemetria básica, como temperatura e integridade de componentes. Isso permite que a Microsoft forneça notificação proativa de problemas de integridade. Se necessário, o cliente pode desabilitar essa conta.

### <a name="q-what-is-the-tenant-admin-account-microsoft-uses-i-am-used-to-the-admin-user-being-admin-on-thales-luna-hsms"></a>P: Qual é a conta do "administrador de locatários" que a Microsoft usa, estou acostumado com o usuário administrador que é "admin" em HSMs Thales de Luna?

O dispositivo HSM é fornecido com um usuário padrão administrador com sua senha padrão usual. A Microsoft não queria ter senhas padrão em uso enquanto qualquer dispositivo estiver em um pool aguardando para ser provisionado pelos clientes. Isso não atenderia aos nossos requisitos de segurança estritos. Por esse motivo, definimos uma senha forte, que é descartada no momento do provisionamento. Além disso, no tempo de provisionamento, criamos um novo usuário na função de administrador chamado "administrador de locatários". Esse usuário tem a senha padrão e os clientes alteram isso como a primeira ação ao fazer logon primeiro no dispositivo provisionado recentemente. Esse processo garante altos níveis de segurança e mantém nossa promessa de controle administrativo exclusivo para nossos clientes. Deve-se observar que o usuário "administrador de locatários" pode ser usado para redefinir a senha de usuário do administrador se um cliente preferir usar essa conta. 

### <a name="q-can-microsoft-or-anyone-at-microsoft-access-keys-in-my-dedicated-hsm"></a>P: A Microsoft ou qualquer pessoa na Microsoft pode acessar as chaves no meu HSM Dedicado?

Não. A Microsoft não tem acesso às chaves armazenadas no HSM Dedicado alocado pelo cliente.

### <a name="q-can-i-upgrade-softwarefirmware-on-hsms-allocated-to-me"></a>P: Posso atualizar o software/firmware nos HSMs alocados para mim?

O cliente tem controle administrativo total, incluindo atualização de software/firmware se forem necessários recursos específicos de versões de firmware diferentes. Antes de fazer alterações, consulte a Microsoft sobre sua atualização contatando HSMRequest@microsoft.com  

### <a name="q-how-do-i-manage-dedicated-hsm"></a>P: Como gerencio o HSM Dedicado?

Você pode gerenciar os HSMs Dedicados acessando-os usando o SSH.

### <a name="q-how-do-i-manage-partitions-on-the-dedicated-hsm"></a>P: Como gerencio partições no HSM Dedicado?

O software cliente Thales HSM é usado para gerenciar os HSMs e as partições.

### <a name="q-how-do-i-monitor-my-hsm"></a>P: Como faço para monitorar meu HSM?

P: Como monitoro meu HSM? Um cliente tem acesso total aos logs de atividade do HSM via syslog e SNMP. Um cliente precisará configurar um servidor syslog ou um servidor SNMP para receber os logs ou eventos dos HSMs.

### <a name="q-can-i-get-full-access-log-of-all-hsm-operations-from-dedicated-hsm"></a>P: Posso obter log de acesso completo de todas as operações do HSM Dedicado?

Sim. Você pode enviar logs do dispositivo HSM para um servidor syslog

## <a name="high-availability"></a>Alta disponibilidade

### <a name="q-is-it-possible-to-configure-high-availability-in-the-same-region-or-across-multiple-regions"></a>P: é possível configurar a alta disponibilidade na mesma região ou em várias regiões?

Sim. A configuração de alta disponibilidade e a configuração são executadas no software cliente HSM fornecido pelo Thales. Os HSMs da mesma rede virtual ou outros VNETs na mesma região ou entre regiões, ou HSMs locais conectados a uma VNET usando VPN site a site ou ponto a ponto podem ser adicionados à mesma configuração de alta disponibilidade. Deve-se observar que isso sincroniza somente o material da chave e não itens de configuração específicos, como funções.

### <a name="q-can-i-add-hsms-from-my-on-premises-network-to-a-high-availability-group-with-azure-dedicated-hsm"></a>P: posso adicionar HSMs de minha rede local a um grupo de alta disponibilidade com o HSM dedicado do Azure?

Sim. Eles devem atender aos requisitos de alta disponibilidade para [HSMs Thales Luna 7](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms)

### <a name="q-can-i-add-luna-56-hsms-from-on-premises-networks-to-a-high-availability-group-with-azure-dedicated-hsm"></a>P: posso adicionar HSMs de Luna 5/6 de redes locais a um grupo de alta disponibilidade com o HSM dedicado do Azure?

Não.

### <a name="q-how-many-hsms-can-i-add-to-the-same-high-availability-configuration-from-one-single-application"></a>P: Quantos HSMs posso adicionar à mesma configuração de alta disponibilidade a partir de um único aplicativo?

16 membros de um grupo de HA têm testes de controle total, com excelentes resultados.

## <a name="support"></a>Suporte

### <a name="q-what-is-the-sla-for-dedicated-hsm-service"></a>P: Qual é o SLA do serviço de HSM Dedicado?

Não há garantia de tempo de atividade específica fornecida para o serviço HSM dedicado. A Microsoft garantirá o acesso em nível de rede ao dispositivo e, portanto, os SLAs de rede padrão do Azure serão aplicados.

### <a name="q-how-are-the-hsms-used-in-azure-dedicated-hsm-protected"></a>P: Como os HSMs usados no HSM Dedicado do Azure são protegidos?

Os datacenters do Azure têm extensos controles de segurança físicos e de procedimentos. Além disso, os HSMs Dedicados são hospedados em uma área de acesso restrito adicional do datacenter. Essas áreas têm controles adicionais de acesso físico e vigilância por câmeras de vídeo para maior segurança.

### <a name="q-what-happens-if-there-is-a-security-breach-or-hardware-tampering-event"></a>P: o que acontece se houver uma violação de segurança ou o evento de violação de hardware?

O serviço HSM dedicado usa os dispositivos [HSM Thales Luna 7](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) . Esses dispositivos dão suporte à detecção de adulteração física e lógica. Se houver algum evento de violação, os HSMs serão automaticamente zerados.

### <a name="q-how-do-i-ensure-that-keys-in-my-dedicated-hsms-are-not-lost-due-to-error-or-a-malicious-insider-attack"></a>P: Como posso garantir que as chaves em meus HSMs Dedicados não sejam perdidas devido a erros ou ataques internos maliciosos?

É altamente recomendável usar um dispositivo de backup de HSM local para executar backup periódico regular dos HSMs para recuperação de desastre. Você precisará usar uma conexão VPN ponto a ponto ou site a site para uma estação de trabalho local conectada a um dispositivo de backup do HSM.

### <a name="q-how-do-i-get-support-for-dedicated-hsm"></a>P: Como obtenho suporte para o HSM Dedicado?

O suporte é fornecido pela Microsoft e pelo Thales.  Se você tiver um problema com o acesso ao hardware ou à rede, gere uma solicitação de suporte com a Microsoft e, se você tiver um problema com a configuração, o software e o desenvolvimento de aplicativos do HSM, aumente uma solicitação de suporte com o Thales. Se você tiver um problema indeterminado, gere uma solicitação de suporte com a Microsoft e, em seguida, Thales poderá ser envolvido conforme necessário. 

### <a name="q-how-do-i-get-the-client-software-documentation-and-access-to-integration-guidance-for-the-thales-luna-7-hsm"></a>P: Como fazer obter o software cliente, a documentação e o acesso às diretrizes de integração do Thales Luna 7 HSM?

Após o registro para o serviço, será fornecida uma ID de cliente Thales que permite o registro no portal de suporte ao cliente do Thales. Isso permitirá o acesso a todos os softwares e documentações, bem como a habilitação de solicitações de suporte diretamente com o Thales.

### <a name="q-if-there-is-a-security-vulnerability-found-and-a-patch-is-released-by-thales-who-is-responsible-for-upgradingpatching-osfirmware"></a>P: se houver uma vulnerabilidade de segurança encontrada e um patch for lançado pelo Thales, que é responsável por atualizar/aplicar patches no sistema operacional/firmware?

A Microsoft não tem a capacidade de se conectar a HSMs alocados a clientes. Os clientes devem atualizar e corrigir seus HSMs.

### <a name="q-what-if-i-need-to-reboot-my-hsm"></a>P: e se eu precisar reinicializar meu HSM?

O HSM tem uma opção de reinicialização de linha de comando. no entanto, estamos enfrentando problemas em que a reinicialização para de responder intermitentemente e, por esse motivo, é recomendável para a reinicialização mais segura que você gera uma solicitação de suporte com a Microsoft para que o dispositivo seja reinicializado fisicamente. 

## <a name="cryptography-and-standards"></a>Criptografia e Padrões

### <a name="q-is-it-safe-to-store-encryption-keys-for-my-most-important-data-in-dedicated-hsm"></a>P: É seguro armazenar chaves de criptografia para meus dados mais importantes no HSM Dedicado?

Sim, o HSM dedicado provisiona Thales da Luna 7 com o [FIPS 140-2 nível-3](https://csrc.nist.gov/publications/detail/fips/140/2/final) validado. 

### <a name="q-what-cryptographic-keys-and-algorithms-are-supported-by-dedicated-hsm"></a>P: Quais chaves e algoritmos criptográficos são suportados pelo HSM Dedicado?

O serviço HSM dedicado provisiona dispositivos HSM Thales Luna 7. Eles suportam uma ampla gama de tipos de chaves criptográficas e algoritmos, incluindo: Suporte completo ao pacote B

* Assimétrica:
  * RSA
  * DSA
  * Diffie-Hellman
  * Curva elíptica
  * Criptografia (ECDSA, ECDH, Ed25519, ECIES) com curvas nomeadas, definidas pelo usuário e de Brainpool, KCDSA
* Simétrica:
  * AES-GCM
  * DES triplo
  * DES
  * ARIA, SEED
  * RC2
  * RC4
  * RC5
  * CAST
  * Hash/Message Digest/HMAC: SHA-1, SHA-2, SM3
  * Derivação de chave: modo de contador do SP 800-108
  * Encapsulamento de chaves: SP 800-38F
  * Geração de Números Aleatórios: DRBG aprovado pelo FIPS 140-2 (modo SP 800-90 CTR), em conformidade com o BSI DRG.4

### <a name="q-is-dedicated-hsm-fips-140-2-level-3-validated"></a>P: O HSM Dedicado são validados por FIPS 140-2 nível 3?

Sim. O serviço HSM dedicado provisiona o [modelo HSM Thales Luna 7 A790](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) dispositivos com [FIPS 140-2 nível-3](https://csrc.nist.gov/publications/detail/fips/140/2/final) validados.

### <a name="q-what-do-i-need-to-do-to-make-sure-i-operate-dedicated-hsm-in-fips-140-2-level-3-validated-mode"></a>P: O que preciso fazer para ter certeza de operar o HSM Dedicado no modo validado FIPS 140-2 Nível 3?

O serviço HSM dedicado provisiona dispositivos HSM Thales Luna 7. Esses dispositivos são HSMs validados pelo FIPS 140-2 nível 3. A configuração padrão implementada, o sistema operacional e o firmware também são validados pelo FIPS. Você não precisa executar nenhuma ação para conformidade com o FIPS 140-2 Nível 3.

### <a name="q-how-does-a-customer-ensure-that-when-an-hsm-is-deprovisioned-all-the-key-material-is-wiped-out"></a>P: como um cliente garante que quando um HSM for desprovisionado, todo o material da chave será apagado?

Antes de solicitar o desprovisionamento, um cliente deve ter o HSM zerado usando as ferramentas de cliente HSM fornecidas pelo Thales.

## <a name="performance-and-scale"></a>Desempenho e escala

### <a name="q-how-many-cryptographic-operations-are-supported-per-second-with-dedicated-hsm"></a>P: Quantas operações criptográficas são suportadas por segundo, com o HSM Dedicado?

O HSM dedicado provisiona os HSMs Thales Luna 7. Aqui está um resumo do desempenho máximo para algumas operações: 

* RSA-2048: 10.000 transações por segundo
* ECC P256: 20.000 transações por segundo
* AES-GCM: 17.000 transações por segundo

### <a name="q-how-many-partitions-can-be-created-in-dedicated-hsm"></a>P: Quantas partições podem ser criadas no HSM Dedicado?

O [modelo HSM Thales Luna 7 A790](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) usado inclui uma licença para 10 partições no custo do serviço. O dispositivo tem um limite de 100 partições e a adição de partições até esse limite incorrerá em custos de licenciamento extras e exigirá a instalação de um novo arquivo de licença no dispositivo.

### <a name="q-how-many-keys-can-be-supported-in-dedicated-hsm"></a>P: Quantas chaves podem ser suportadas no HSM Dedicado?

O número máximo de chaves é uma função da memória disponível. O modelo SafeNet Luna 7 A790 em uso tem 32 MB de memória. Os números a seguir também se aplicam a pares de chaves se estiver usando chaves assimétricas.

* RSA-2048 - 19,000
* ECC-P256 - 91,000

A capacidade variará dependendo dos atributos-chave específicos definidos no modelo de geração de chaves e no número de partições.
