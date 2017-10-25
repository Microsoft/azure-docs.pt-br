---
title: Proteger aplicativos de PaaS usando o Armazenamento do Azure | Microsoft Docs
description: " Conheça as melhores práticas de segurança do Armazenamento do Azure para proteger aplicativos móveis e Web de PaaS. "
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: TomShinder
ms.openlocfilehash: 62c7b6706268e3c6e329f90651125fe299f61d67
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="securing-paas-web-and-mobile-applications-using-azure-storage"></a>Proteger aplicativos móveis e Web de PaaS usando o Armazenamento do Azure
Neste artigo, discutiremos um conjunto de práticas recomendadas de segurança do Armazenamento do Azure para proteger seus aplicativos móveis e Web de PaaS. Essas práticas recomendadas derivam da nossa experiência com o Azure e da experiência de clientes como você.

O [Guia de segurança do Armazenamento do Azure](../storage/common/storage-security-guide.md) é uma ótima fonte de informações detalhadas sobre o Armazenamento do Azure e sua segurança.  Este artigo aborda, em um nível mais amplo, alguns dos conceitos encontrados no guia de segurança e leva a ele, bem como a outras fontes, para mais informações.

## <a name="azure-storage"></a>Armazenamento do Azure
O Azure possibilita implantar e usar o armazenamento de formas que não são fáceis de alcançar localmente. Com o Armazenamento do Azure, você pode alcançar altos níveis de escalabilidade e disponibilidade com relativamente pouco esforço. O Armazenamento do Azure não é somente a base para as Máquinas Virtuais do Azure do Windows e Linux, ele também pode dar suporte a grandes aplicativos distribuídos.

O Armazenamento do Azure fornece os quatro seguintes serviços: Armazenamento de Blobs, Armazenamento de Tabelas, Armazenamento de Filas e Armazenamento de Arquivos. Para saber mais, consulte a [Introdução ao Armazenamento do Microsoft Azure](../storage/storage-introduction.md).

## <a name="best-practices"></a>Práticas recomendadas
Este artigo aborda as seguintes melhores práticas:

- Proteção de acesso:
   - SAS (Assinaturas de Acesso Compartilhado)
   - Disco gerenciado
   - RBAC (Controle de Acesso Baseado em Função)

- Criptografia de armazenamento:
   - Criptografia do lado do cliente para dados de alto valor
   - Azure Disk Encryption para VMs (máquinas virtuais)
   - Criptografia do Serviço de Armazenamento

## <a name="access-protection"></a>Proteção de acesso
### <a name="use-shared-access-signature-instead-of-a-storage-account-key"></a>Use a Assinatura de Acesso Compartilhado em vez de uma chave de conta de armazenamento

Em uma solução de IaaS, geralmente executando máquinas virtuais do Windows Server ou Linux, os arquivos são protegidos contra ameaças de divulgação e violação usando mecanismos de controle de acesso. No Windows, você usaria [ACLs (listas de controle de acesso)](../virtual-network/virtual-networks-acl.md) e, no Linux, provavelmente usaria [chmod](https://en.wikipedia.org/wiki/Chmod). Essencialmente, é exatamente isso o que você faria se estivesse protegendo arquivos em um servidor em seu próprio data center hoje.

Com PaaS, é diferente. Uma das maneiras mais comuns de armazenar arquivos no Microsoft Azure é usar o [Armazenamento de Blobs do Azure](../storage/storage-dotnet-how-to-use-blobs.md). Uma diferença entre o Armazenamento de Blobs e outros armazenamentos de arquivo é a E/S de arquivo, bem como os métodos de proteção que vêm com ela.

O controle de acesso é fundamental. Para ajudá-l a controlar o acesso ao armazenamento do Azure, o sistema gerará duas SAKs (chaves de conta de armazenamento) de 512 bits quando você [criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md). O nível de redundância das chaves possibilita evitar a interrupção do serviço durante a rotação de chaves de rotina.

Chaves de acesso de armazenamento são segredos de alta prioridade e só devem ser acessíveis aos responsáveis pelo controle de acesso de armazenamento. Se as pessoas erradas tiverem acesso a essas chaves, elas terão controle total sobre o armazenamento e poderão substituir, excluirá ou adicionar arquivos ao armazenamento. Isso inclui malware e outros tipos de conteúdo com potencial para comprometer seus clientes ou sua organização.

Você ainda precisa de uma maneira de fornecer acesso aos objetos no armazenamento. Para fornecer um acesso mais granular, você aproveitar a [SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md) (Assinatura de Acesso Compartilhado). A SAS possibilita compartilhar objetos específicos no armazenamento por um intervalo predefinido e com permissões específicas. Uma Assinatura de Acesso Compartilhado permite definir:

- O intervalo no qual a SAS é válida, incluindo a hora de início e a hora de expiração.
- As permissões concedidas pelas SAS. Por exemplo, um SAS em um blob pode conceder a um usuário permissões de leitura e gravação nesse blob, mas não permissões de exclusão.
- Um endereço IP opcional ou um intervalo de endereços IP dos quais o Armazenamento do Azure aceita a SAS. Por exemplo, você pode especificar um intervalo de endereços IP que pertencem à sua organização. Isso fornece outra medida de segurança para a SAS.
- O protocolo em que o Armazenamento do Azure aceita a SAS. Você pode usar esse parâmetro opcional para restringir o acesso a clientes usando HTTPS.

A SAS lhe permite compartilhar conteúdo da maneira desejada sem precisar fornecer suas Chaves de Conta de Armazenamento. Sempre usar SAS em seu aplicativo é uma maneira segura de compartilhar seus recursos de armazenamento sem comprometer as chaves de conta de armazenamento.

Para obter mais informações, consulte [Uso de SAS (Assinaturas de Acesso Compartilhado)](../storage/common/storage-dotnet-shared-access-signature-part-1.md). Para saber mais sobre riscos potenciais e as recomendações para mitigar esses riscos, consulte [Melhores práticas ao usar SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

### <a name="use-managed-disks-for-vms"></a>Usar discos gerenciados para VMs

Quando você escolhe [Azure Managed Disks](../storage/storage-managed-disks-overview.md), o Azure gerencia contas de armazenamento que você pode usar para os discos de VM. Tudo que você precisa fazer é escolher o tipo de disco (Premium ou Standard) e o tamanho do disco. O Armazenamento do Azure fará o restante. Você não precisa se preocupar com limites de escalabilidade que, de outra forma, poderiam exigir que você aumentasse a quantidade de contas de armazenamento.

Para obter mais informações, consulte [Perguntas frequentes sobre discos premium gerenciados e não gerenciados](../storage/storage-faq-for-disks.md).

### <a name="use-role-based-access-control"></a>Usar o controle de acesso baseado em função

Anteriormente, discutimos o uso de SAS (Assinatura de Acesso Compartilhado) para conceder acesso limitado a objetos em sua conta de armazenamento a outros clientes sem expor a chave de conta de armazenamento da conta. Às vezes, os riscos associados a uma determinada operação em relação à sua conta de armazenamento superam os benefícios da SAS. Às vezes, é mais simples gerenciar o acesso de outras maneiras.

Outra maneira de gerenciar o acesso é usar o [RBAC do Azure](../active-directory/role-based-access-control-what-is.md) (controle de acesso baseado em função). Com o RBAC, você se concentra em fornecer aos funcionários as permissões exatas de que eles precisam, com base nos princípios de segurança de privilégio mínimo e do que os usuários precisam saber. Muitas permissões podem expor uma conta a ataques. Permissões insuficientes significa que os funcionários não podem ter seu trabalho feito com eficiência. O RBAC ajuda a resolver esse problema oferecendo gerenciamento de acesso refinado ao Azure. Isso é fundamental para as organizações que desejam impor políticas de segurança para acesso a dados.

Você pode aproveitar as funções internas de RBAC do Azure para atribuir privilégios aos usuários. Considere usar o Colaborador da Conta de Armazenamento para operadores de nuvem que precisarem gerenciar contas de armazenamento e a função de Colaborador de Conta de Armazenamento Clássica para gerenciar contas de armazenamento clássicas. Para operadores de nuvem que precisam gerenciar VMs, mas não a rede virtual ou a conta de armazenamento a que estão conectados, considere adicioná-los à função de Colaborador de Máquina Virtual.

As organizações que não impõem o controle de acesso de dados utilizando recursos como o RBAC podem estar dando mais privilégios do que o necessário para seus usuários. Isso pode levar ao comprometimento de dados por permitir que alguns usuários acessem dados que a não deveriam ter acesso.

Para saber mais sobre o RBAC, consulte:

- [Controle de Acesso Baseado em Função do Azure](../active-directory/role-based-access-control-configure.md)
- [Funções internas para controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md)
- [Guia de Segurança do Armazenamento do Azure](../storage/common/storage-security-guide.md) para obter detalhes de como proteger sua conta de armazenamento com RBAC

## <a name="storage-encryption"></a>Criptografia do armazenamento
### <a name="use-client-side-encryption-for-high-value-data"></a>Use a criptografia do lado do cliente para dados de alto valor

A criptografia do lado do cliente permite criptografar programaticamente dados em trânsito antes de carregar no Armazenamento do Azure, bem como descriptografar os dados programaticamente ao recuperá-los do armazenamento.  Isso fornece criptografia de dados em trânsito, mas também fornece criptografia de dados em repouso.  A criptografia do lado do cliente é o método mais seguro de criptografar seus dados, mas ela requer que você faça alterações programáticas em seu aplicativo e implemente processos de gerenciamento de chaves.

A criptografia do lado do cliente também permite que você tenha controle exclusivo sobre suas chaves de criptografia.  Você pode gerar e gerenciar suas próprias chaves de criptografia.  A criptografia do lado do cliente usa uma técnica de envelope em que a biblioteca de cliente do Armazenamento do Azure gera uma CEK (chave de criptografia de conteúdo) que é encapsulada (criptografada) usando a KEK (chave de criptografia de chave). A KEK é identificada por um identificador de chave e pode ser um par de chaves assimétricas ou uma chave simétrica, e pode ser gerenciada localmente ou armazenada no [Azure Key Vault](../key-vault/key-vault-whatis.md).

A criptografia do lado do cliente é interna nas bibliotecas de cliente de armazenamento Java e .NET.  Consulte [Criptografia do lado do cliente e Azure Key Vault para o Armazenamento do Microsoft Azure](../storage/storage-client-side-encryption.md) para obter informações sobre a criptografia de dados em aplicativos cliente e sobre como gerar e gerenciar suas próprias chaves de criptografia.

### <a name="azure-disk-encryption-for-vms"></a>Azure Disk Encryption para máquinas virtuais
O Azure Disk Encryption é uma capacidade que ajuda você a criptografar seus discos de máquina virtual IaaS Windows e Linux. A Azure Disk Encryption aproveita o recurso padrão da indústria BitLocker do Windows e o recurso DM-Crypt do Linux para fornecer criptografia de volume para o SO e os discos de dados. A solução é integrada ao Azure Key Vault para ajudá-lo a controlar e gerenciar os segredos e chaves de criptografia de disco em sua assinatura do Key Vault. A solução também garante que todos os dados em discos da máquina virtual sejam criptografados em repouso no armazenamento do Azure.

Consulte [Azure Disk Encryption para VMs IaaS Windows e Linux](azure-security-disk-encryption.md).

### <a name="storage-service-encryption"></a>Criptografia do Serviço de Armazenamento
Quando a [Criptografia do serviço de armazenamento](../storage/storage-service-encryption.md) para o armazenamento de arquivos está habilitada, os dados são criptografados automaticamente usando criptografia AES-256. A Microsoft lida com toda a criptografia, a descriptografia e o gerenciamento de chaves. Este recurso está disponível para os tipos de redundância LRS e GRS.

## <a name="next-steps"></a>Próximas etapas
Este artigo apresentou a você um conjunto de melhores práticas de segurança do Armazenamento do Azure para proteger seus aplicativos móveis e Web de PaaS. Para saber mais sobre como proteger suas implantações de PaaS, confira:

- [Proteção de implantações de PaaS](security-paas-deployments.md)
- [Securing PaaS web and mobile applications using Azure App Services](security-paas-applications-using-app-services.md) (Proteção dos aplicativos Web e móveis de PaaS usando os Serviços de Aplicativos do Azure)
- [Protegendo bancos de dados de PaaS no Azure](security-paas-applications-using-sql.md)
