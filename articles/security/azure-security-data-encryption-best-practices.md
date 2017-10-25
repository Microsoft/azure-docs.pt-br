---
title: "Práticas Recomendadas de Segurança de Dados e Criptografia | Microsoft Docs"
description: "Este artigo fornece um conjunto de práticas recomendadas de segurança de dados e criptografia usando recursos internos do Azure."
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 17ba67ad-e5cd-4a8f-b435-5218df753ca4
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2017
ms.author: yurid
ms.openlocfilehash: 81136e53756adfdba2f07c103b042499fe2967db
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-data-security-and-encryption-best-practices"></a>Práticas Recomendadas de Segurança de Dados e Criptografia do Azure
Uma das chaves de proteção de dados na nuvem é responsável por possíveis estados em que os dados podem ocorrer e quais controles estão disponíveis para esse estado. Em relação às práticas recomendadas de segurança de dados e criptografia do Azure, as recomendações serão feitas para os seguintes estados de dados:

* Em repouso: isso inclui todos os objetos de armazenamento, contêineres e tipos de informações que existem estaticamente em mídia física, seja ela magnética ou disco óptico.
* Em trânsito: quando dados estão sendo transferidos entre componentes, locais ou programas, como através da rede, através de um barramento de serviço (do local para a nuvem e vice-versa, incluindo conexões híbridas, como o ExpressRoute) ou durante um processo de entrada/saída, ele é considerado como estando em trânsito.

Neste artigo, veremos uma coleção de práticas de recomendadas de segurança de dados e criptografia do Azure. Essas práticas recomendadas derivam da nossa experiência com segurança de dados e criptografia do Azure e da experiência de clientes como você.

Para cada prática recomendada, vamos explicar:

* O que é a prática recomendada
* Por que é ideal habilitar essa prática recomendada
* O que poderá acontecer se você não habilitar a prática recomendada
* Possíveis alternativas à prática recomendada
* Como você pode aprender a habilitar a prática recomendada

Este artigo Práticas Recomendadas de Segurança de Dados e Criptografia do Azure baseiam-se em um consenso e nos recursos da plataforma Azure e em conjuntos de recursos, tal como existiam no momento em que o artigo foi escrito. As opiniões e as tecnologias mudam ao longo do tempo e este artigo será atualizado regularmente para refletir essas alterações.

As práticas recomendadas de segurança de dados e criptografia do Azure debatidas neste artigo incluem:

* Impor autenticação multifator
* Usar RBAC (controle de acesso baseado em função)
* Criptografar máquinas virtuais do Azure
* Usar modelos de segurança de hardware
* Gerenciar com Estações de Trabalho Protegidas
* Habilitar a criptografia de dados SQL
* Proteger dados em trânsito
* Impor criptografia de dados no nível do arquivo

## <a name="enforce-multi-factor-authentication"></a>Impor Autenticação Multifator
A primeira etapa do acesso a dados e do controle no Microsoft Azure é autenticar o usuário. A [Azure MFA (Autenticação Multifator)](../multi-factor-authentication/multi-factor-authentication.md) é um método de verificação da identidade do usuário que utiliza outro método além do nome de usuário e senha. Esse método de autenticação ajuda a proteger o acesso a dados e aplicativos enquanto atende à demanda dos usuários para um processo de entrada simples.

Ao habilitar o Azure MFA para seus usuários, você estará adicionando uma segunda camada de segurança nas entradas de usuário e transações. Nesse caso, uma transação pode ser o acesso a um documento localizado em um servidor de arquivos ou no SharePoint Online. O Azure MFA também ajuda o departamento de TI a reduzir a probabilidade de que uma credencial comprometida tenha acesso aos dados da organização.

Por exemplo: ao impor o Azure MFA para seus usuários e configurá-lo para usar uma chamada telefônica ou mensagem de texto como verificação, se a credencial do usuário for comprometida, o invasor não poderá acessar os recursos, já que não terá acesso ao telefone do usuário. As organizações que não adicionam essa camada adicional de proteção de identidade são mais suscetíveis a ataques de roubo de credenciais, que podem levar ao comprometimento dos dados.

Uma alternativa para as organizações que desejam manter o controle da autenticação localmente é usar o [Servidor da Autenticação Multifator do Azure](../multi-factor-authentication/multi-factor-authentication-get-started-server.md), também chamado de MFA local. Usando esse método você ainda poderá impor a autenticação multifator, mantendo o servidor MFA local.

Para saber mais sobre a MFA do Azure, leia o artigo [Introdução à Autenticação Multifator do Azure na nuvem](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## <a name="use-role-based-access-control-rbac"></a>Usar RBAC (Controle de Acesso Baseado em Função)
Restringir o acesso com base nos princípios de segurança de [divulgação restrita àqueles diretamente interessados](https://en.wikipedia.org/wiki/Need_to_know) e [privilégios mínimos](https://en.wikipedia.org/wiki/Principle_of_least_privilege). Isso é fundamental para as organizações que desejam impor políticas de segurança para acesso a dados. O RBAC (Controle de Acesso Baseado em Função) pode ser usado para atribuir permissões a usuários, grupos e aplicativos em um determinado escopo. O escopo de uma atribuição de função pode ser uma assinatura, um grupo de recursos ou um único recurso.

Você pode aproveitar funções [RBAC internas](../active-directory/role-based-access-built-in-roles.md) no Azure para atribuir privilégios aos usuários. Considere usar a função *Colaborador da Conta de Armazenamento* para operadores de nuvem que precisam gerenciar contas de armazenamento e a função *Colaborador da Conta de Armazenamento Clássica* para gerenciar contas de armazenamento clássico. Para operadores de nuvem que precisam gerenciar VMs e contas de armazenamento, considere adicioná-los à função *Colaborador da Máquina Virtual*.

As organizações que não impõem o controle de acesso de dados utilizando recursos como o RBAC podem estar dando mais privilégios do que o necessário para seus usuários. Isso pode levar ao comprometimento de dados, já que alguns usuários têm acesso a dados que não deveriam ter desde o início.

Você pode aprender mais sobre o RBAC do Azure lendo o artigo [Controle de Acesso Baseado em Função do Azure](../active-directory/role-based-access-control-configure.md).

## <a name="encrypt-azure-virtual-machines"></a>Criptografar Máquinas Virtuais do Azure
Para muitas organizações, a [criptografia de dados em repouso](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) é uma etapa obrigatória no sentido de garantir a soberania, a privacidade e a conformidade dos dados. A Azure Disk Encryption permite que os administradores de TI criptografem discos de VM (Máquina Virtual) Iaas Windows e Linux. A Azure Disk Encryption aproveita o recurso padrão da indústria BitLocker do Windows e o recurso DM-Crypt do Linux para fornecer criptografia de volume para o SO e os discos de dados.

Você pode aproveitar a Azure Disk Encryption para ajudar a proteger seus dados e atender a requisitos de conformidade e segurança da organização. As organizações devem cogitar também usar a criptografia para ajudar a reduzir riscos relativos ao acesso a dados não autorizado. Também é recomendável que você criptografe as unidades antes de gravar dados confidenciais nelas.

Não deixe de criptografar os volumes de dados da VM e o volume de inicialização para proteger dados em repouso na sua conta de armazenamento do Azure. Proteja as chaves de criptografia e segredos aproveitando o [Cofre de Chaves do Azure](../key-vault/key-vault-whatis.md).

Para os servidores Windows locais, considere as seguinte práticas recomendadas de criptografia:

* Usar [BitLocker](https://technet.microsoft.com/library/dn306081.aspx) para criptografia de dados
* Armazene informações de recuperação no AD DS.
* Se houver suspeita de que chaves do BitLocker foram comprometidas, recomendamos que você formate a unidade para remover todas as instâncias dos metadados do BitLocker da unidade ou descriptografe e criptografe a unidade inteira novamente.

As organizações que não impõem criptografia de dados têm maior probabilidade de ficarem expostas a problemas de integridade de dados, como usuários não autorizados ou mal-intencionados que roubam dados e contas comprometidas tendo acesso não autorizado aos dados em formato não criptografado. Além desses riscos, as empresas que estejam em conformidade com as normas do setor devem provar que estão atentas e usando os controles de segurança corretos para aprimorar a segurança dos dados.

Você pode aprender mais sobre a Azure Disk Encryption lendo o artigo [Criptografia de Disco do Azure para VMs IaaS Windows e Linux](azure-security-disk-encryption.md).

## <a name="use-hardware-security-modules"></a>Usar Módulos de Segurança de Hardware
As soluções de criptografia do setor usam chaves secretas para criptografar dados. Portanto, é importante que essas chaves sejam armazenadas com segurança. O gerenciamento de chaves é uma parte integrante da proteção de dados, pois será aproveitado para armazenar chaves secretas usadas para criptografar dados.

A Azure Disk Encryption usa o [Cofre de Chaves do Azure](https://azure.microsoft.com/services/key-vault/) para ajudá-lo a controlar e a gerenciar as chaves de criptografia de disco e segredos em sua assinatura de cofre de chaves, garantindo que todos os dados nos discos de máquina virtual sejam criptografados em repouso no Armazenamento do Azure. Você deve usar o Cofre de Chaves do Azure para auditar as chaves e o uso de políticas.

Há muitos riscos inerentes relativos à falta de controles de segurança apropriados para proteger as chaves secretas que foram usadas na criptografia dos dados. Se os invasores tiverem acesso às chaves secretas, serão capazes de descriptografar os dados e ter acesso a informações confidenciais.

Você pode aprender mais sobre recomendações gerais para o gerenciamento de certificados no Azure lendo o artigo [Gerenciamento de Certificados no Azure: Regras](https://blogs.msdn.microsoft.com/azuresecurity/2015/07/13/certificate-management-in-azure-dos-and-donts/).

Para saber mais sobre o Cofre de Chaves do Azure, leia [Introdução ao Cofre de Chaves do Azure](../key-vault/key-vault-get-started.md).

## <a name="manage-with-secure-workstations"></a>Gerenciar com Estações de Trabalho Protegidas
Como a maioria dos ataques tem o usuário final como alvo, o ponto de extremidade se torna um dos principais pontos de ataque. Se um invasor comprometer o ponto de extremidade, ele poderá aproveitar as credenciais do usuário para obter acesso aos dados da organização. A maioria dos ataques de ponto de extremidade aproveita o fato dos usuários finais serem os administradores em suas estações de trabalho locais.

Você pode reduzir esses riscos usando uma estação de trabalho de gerenciamento segura. Recomendamos que você use [PAW (estações de trabalho de acesso privilegiado)](https://technet.microsoft.com/library/mt634654.aspx) para reduzir a superfície de ataque em estações de trabalho. Essas estações de trabalho de gerenciamento seguras podem ajudar a atenuar alguns desses ataques e a garantir que seus dados ficarão mais seguros. Não deixe de usar PAWs para proteger e bloquear sua estação de trabalho. Essa é uma etapa importante para fornecer garantias de alta segurança para contas confidenciais, tarefas e proteção de dados.

A falta de proteção no ponto de extremidade pode colocar seus dados em risco. Não deixe de impor políticas de segurança em todos os dispositivos que são usados para consumir dados, independentemente do local dos dados (nuvem ou local).

Você pode aprender mais sobre estações de trabalho com acesso privilegiado lendo o artigo [Garantindo o Acesso Privilegiado](https://technet.microsoft.com/library/mt631194.aspx).

## <a name="enable-sql-data-encryption"></a>Habilitar a criptografia de dados SQL
O [TDE (Transparent Data Encryption) de Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/dn948096.aspx) ajuda a proteger contra atividades mal-intencionadas por meio da execução de criptografia e descriptografia em tempo real do banco de dados, de backups associados e de arquivos de log de transações em repouso, sem exigir mudanças no aplicativo.  A TDE criptografa o armazenamento de um banco de dados inteiro usando uma chave simétrica chamada de chave de criptografia de banco de dados.

Mesmo quando todo o armazenamento é criptografado, é muito importante também criptografar seu próprio banco de dados. Essa é uma implementação com abordagem de defesa profunda para proteção de dados. Se você estiver usando o [Banco de Dados SQL Azure](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx) e desejar proteger dados confidenciais, como números de previdência social ou de cartão de crédito, poderá criptografar os bancos de dados com criptografia AES de 256 bits validada por FIPS 140-2, que atende aos requisitos de muitos padrões do setor (por exemplo, HIPAA, PCI).

É importante compreender que os arquivos relacionados à [BPE](https://msdn.microsoft.com/library/dn133176.aspx) (extensão do pool de buffers) não são criptografados quando um banco de dados é criptografado usando TDE. Você deve usar ferramentas de criptografia no nível do sistema de arquivos, como BitLocker ou [EFS](https://technet.microsoft.com/library/cc700811.aspx) (Encrypting File System) para arquivos relacionados à BPE.

Já que um usuário autorizado, como um administrador de segurança ou um administrador de banco de dados, pode acessar os dados mesmo se o banco de dados estiver criptografado com TDE, você também deve seguir as recomendações abaixo:

* Autenticação SQL no nível do banco de dados
* Autenticação do Azure AD usando funções RBAC
* Os usuários e os aplicativos devem usar contas separadas para autenticar. Dessa forma, você pode limitar as permissões concedidas a usuários e aplicativos e reduzir os riscos de atividades mal-intencionadas
* Implemente segurança no nível do banco de dados usando funções de banco de dados fixas (como db_datareader ou db_datawriter) ou crie funções personalizadas para seu aplicativo conceder permissões explícitas para objetos de banco de dados selecionados

As organizações que não estiverem usando criptografia no nível do banco de dados estarão mais suscetíveis a ataques que podem comprometer os dados localizados em bancos de dados SQL.

Você pode aprender mais sobre a criptografia TDE SQL lendo o artigo [Transparent Data Encryption com o Banco de Dados SQL Azure](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx).

## <a name="protect-data-in-transit"></a>Proteger dados em trânsito
A proteção dos dados em trânsito deve ser parte essencial de sua estratégia de proteção de dados. Já que os dados se moverão entre vários locais, a recomendação geral é que você sempre use protocolos SSL/TLS para trocar os dados entre diferentes locais. Em alguns casos, convém isolar todo o canal de comunicação entre suas infraestruturas locais e na nuvem usando uma VPN (rede virtual privada).

Para dados que se movem entre sua infraestrutura local e o Azure, você deve considerar proteções adequadas, como HTTPS ou VPN.

Para organizações que precisam proteger o acesso de várias estações de trabalho localizadas localmente ao Azure, use [VPN site a site do Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md).

Para organizações que precisam proteger o acesso de uma estação de trabalho localizada localmente local ao Azure, use [VPN Ponto a Site](../vpn-gateway/vpn-gateway-point-to-site-create.md).

Conjuntos de dados maiores podem ser movidos em um link WAN de alta velocidade dedicado, como o [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Se você optar por usar o ExpressRoute, também poderá criptografar os dados no nível do aplicativo usando [SSL/TLS](https://support.microsoft.com/kb/257591) ou outros protocolos para proteção adicional.

Se você estiver interagindo com o Armazenamento do Azure pelo Portal do Azure, todas as transações ocorrerão via HTTPS. A [API REST de armazenamento](https://msdn.microsoft.com/library/azure/dd179355.aspx) sobre HTTPS também pode ser usada para interagir com o [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) e com o [Banco de Dados SQL](https://azure.microsoft.com/services/sql-database/).

As organizações que não protegem os dados em trânsito são mais suscetíveis de [ataques man-in-the-middle](https://technet.microsoft.com/library/gg195821.aspx), [eavesdropping](https://technet.microsoft.com/library/gg195641.aspx) e sequestro de sessão. Esses ataques podem ser a primeira etapa na obtenção de acesso a dados confidenciais.

Você pode aprender mais sobre a opção de VPN do Azure lendo o artigo [Planejamento e design para o Gateway de VPN](../vpn-gateway/vpn-gateway-plan-design.md).

## <a name="enforce-file-level-data-encryption"></a>Impor criptografia de dados no nível do arquivo
Outra camada de proteção que pode aumentar o nível de segurança de seus dados é a criptografia do arquivo propriamente dito, independentemente do local do arquivo.

O [Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) usa políticas de criptografia, identidade e autorização para ajudar a proteger seus arquivos e emails. O Azure RMS funciona em vários dispositivos, como telefones, tablets e PCs, protegendo-os dentro e fora da sua organização. Esse recurso é possível porque o Azure RMS adiciona um nível de proteção que permanece com os dados, mesmo quando eles saem dos limites da organização.

Quando você usa o Azure RMS para proteger seus arquivos, está usando criptografia padrão do setor com suporte total de [FIPS 140-2](http://csrc.nist.gov/groups/STM/cmvp/standards.html). Ao utilizar o Azure RMS para proteção de dados, você tem a garantia de que a proteção permanece com o arquivo, mesmo quando ele é copiado para um armazenamento que não está sob o controle do departamento de TI, como um serviço de armazenamento de nuvem. O mesmo ocorre com arquivos compartilhados por email: o arquivo é protegido como um anexo a uma mensagem de email, com instruções de como abrir o anexo protegido.

Ao planejar a adoção do Azure RMS, recomendamos o seguinte:

* Instale o [aplicativo RMS sharing](https://technet.microsoft.com/library/dn339006.aspx). Esse aplicativo se integra a aplicativos do Office, instalando um suplemento do Office para que os usuários possam proteger seus arquivos de forma direta e fácil.
* Configurar aplicativos e serviços para dar suporte ao Azure RMS
* Crie [modelos personalizados](https://technet.microsoft.com/library/dn642472.aspx) que reflitam as necessidades dos negócios. Por exemplo: um modelo de dados secretos que deve ser aplicado a todos os emails secretos relacionados.

As organizações que não priorizam a [classificação de dados](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) e a proteção de arquivos podem estar mais vulneráveis à perda de dados. Sem a devida proteção de arquivos, as organizações não conseguirão obter percepções sobre os negócios, monitorar o abuso e impedir o acesso mal-intencionado a arquivos.

Você pode aprender mais sobre o Azure RMS lendo o artigo [Introdução ao Azure Rights Management](https://technet.microsoft.com/library/jj585016.aspx).
