---
title: Guia de segurança do Armazenamento do Azure | Microsoft Docs
description: Detalha os vários métodos de proteger o Armazenamento do Azure, incluindo, entre outros, RBAC, Criptografia do Serviço de Armazenamento, Criptografia do Cliente, SMB 3.0 e Azure Disk Encryption.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 74be21629a884313e74a824bb34fb5b66f5db61d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60729014"
---
# <a name="azure-storage-security-guide"></a>Guia de segurança do Armazenamento do Azure

O Armazenamento do Azure fornece um conjunto abrangente de recursos de segurança que, juntos, permitem aos desenvolvedores criar aplicativos seguros:

- Todos os dados gravados no Armazenamento do Azure são criptografados automaticamente usando a [Criptografia do Serviço de Armazenamento (SSE)](storage-service-encryption.md). Veja mais informações em [Anúncio da criptografia padrão para o armazenamento de Blobs, arquivos, tabelas e filas do Azure](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).
- O Azure AD (Azure Active Directory) e o RBAC (controle de acesso baseado em função) têm suporte para o Armazenamento do Azure em relação a operações de gerenciamento de recursos e operações de dados, da seguinte maneira:   
    - Você pode atribuir funções RBAC, no escopo da conta de armazenamento, a entidades de segurança e usar o Azure AD para autorizar operações de gerenciamento de recursos, por exemplo, gerenciamento de chaves.
    - Integração do Azure AD é suportada para operações de dados de blob e fila. É possível atribuir funções RBAC, no escopo de uma assinatura, um grupo de recursos, uma conta de armazenamento ou um contêiner individual ou fila, a uma entidade de segurança ou a uma identidade gerenciada para recursos do Azure. Para saber mais, confira [Autenticar o acesso ao Armazenamento do Azure usando o Azure Active Directory](storage-auth-aad.md).   
- Os dados podem ser protegidos em trânsito, entre um aplicativo e o Azure usando a [Criptografia do cliente](../storage-client-side-encryption.md), HTTPS ou SMB 3.0.  
- Os discos do SO e de dados usados pelas máquinas virtuais do Azure podem ser criptografados usando o [Azure Disk Encryption](../../security/azure-security-disk-encryption.md). 
- O acesso delegado aos objetos de dados no Armazenamento do Microsoft Azure pode ser concedido usando [Assinaturas de Acesso Compartilhado](../storage-dotnet-shared-access-signature-part-1.md).

Este artigo apresenta uma visão geral de cada um desses recursos de segurança que podem ser usados com o Armazenamento do Azure. São fornecidos links para artigos que darão detalhes de cada recurso, para que você possa investigar mais cada tópico mais aprofundadamente.

Estes são os tópicos abordados neste artigo:

* [Segurança do plano de gerenciamento](#management-plane-security) – proteção da conta de armazenamento

  O plano de gerenciamento consiste em recursos usados para gerenciar a conta de armazenamento. Esta seção trata do Modelo de implantação do Azure Resource Manager e de como usar o RBAC (Controle de Acesso Baseado em Função) para controlar o acesso às contas de armazenamento. Também trata de como gerenciar as chaves da conta de armazenamento e como regenerá-las.
* [Segurança do plano de dados](#data-plane-security) – proteção do acesso aos seus dados

  Nesta seção, examinaremos a permissão de acesso aos objetos de dados reais na sua conta de armazenamento como blobs, arquivos, filas e tabelas, usando as Assinaturas de Acesso Compartilhado e as Políticas de Acesso Armazenado. Vamos abordar a SAS de nível de serviço e de nível de conta. Também veremos como limitar o acesso a um endereço IP específico (ou a um intervalo de endereços IP), como limitar o protocolo usado para HTTPS e como revogar uma Assinatura de Acesso Compartilhado sem esperar que ela expire.
* [Criptografia em trânsito](#encryption-in-transit)

  Esta seção ensina a proteger os dados quando você os transfere para dentro ou para fora do Armazenamento do Microsoft Azure. Falaremos sobre o uso recomendado de HTTPS e a criptografia usada pelo SMB 3.0 para compartilhamentos de arquivos do Azure. Também examinaremos a Criptografia do Cliente, que permite criptografar os dados antes que eles sejam transferidos para o Armazenamento em um aplicativo cliente e a descriptografá-los depois que eles são transferidos para fora do Armazenamento.
* [Criptografia em repouso](#encryption-at-rest)

  Trataremos da SSE (Criptografia do Serviço de Armazenamento), que agora é habilitada automaticamente para as contas de armazenamento novas e existentes. Também veremos como você pode usar o Azure Disk Encryption e explorar as diferenças básicas e os casos do Disk Encryption em relação à SSE e à Criptografia do cliente. Examinaremos rapidamente a compatibilidade de FIPS com os computadores do governo norte-americano.
* Uso da [Análise de Armazenamento](#storage-analytics) para auditar o acesso do Armazenamento do Azure

  Esta seção descreve como encontrar informações nos logs de análise de armazenamento de uma solicitação. Analisaremos dados reais do log de análise de armazenamento para saber como diferenciar uma solicitação que foi feita com a chave de conta de Armazenamento, com uma Assinatura de Acesso Compartilhado ou anonimamente e se ela foi bem-sucedida ou se falhou.
* [Habilitando clientes com base no navegador usando CORS](#cross-origin-resource-sharing-cors)

  Esta seção trata de como permitir o CORS (compartilhamento de recursos entre origens). Falaremos sobre acesso entre domínios e como lidar com ele usando os recursos do CORS incorporados no Armazenamento do Microsoft Azure.

## <a name="management-plane-security"></a>Segurança do plano de gerenciamento
O plano de gerenciamento consiste em operações que afetam a conta de armazenamento em si. Por exemplo, você pode criar ou excluir uma conta de armazenamento, obter uma lista de contas de armazenamento em uma assinatura, recuperar as chaves da conta de armazenamento ou regenerar as chaves da conta de armazenamento.

Ao criar uma nova conta de armazenamento, você seleciona um modelo de implantação: Clássico ou Resource Manager. O modelo Clássico de criação de recursos no Azure permite apenas acesso tudo ou nada à assinatura e, por sua vez, à conta de armazenamento.

Este guia se concentra no modelo Gerenciador de Recursos, que é o meio recomendado para a criação de contas de armazenamento. Com as contas de armazenamento do Gerenciador de Recursos, em vez de fornecer acesso à toda a assinatura, você pode controlar o acesso em um nível mais limitado no plano de gerenciamento usando o RBAC (Controle de Acesso Baseado em Função).

### <a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>Como proteger a conta de armazenamento com o RBAC (Controle de Acesso Baseado em Função)
Vamos falar sobre o que é o RBAC e como você pode usá-lo. Cada assinatura do Azure tem um Azure Active Directory. Os usuários, grupos e aplicativos desse diretório podem receber acesso para gerenciar recursos na assinatura do Azure que usam o modelo de implantação Resource Manager. Esse tipo de segurança é chamado de Controle de Acesso Baseado em função (RBAC). Para gerenciar esse acesso, é possível usar o [Portal do Azure](https://portal.azure.com/), as [ferramentas da CLI do Azure](../../cli-install-nodejs.md), o [PowerShell](/powershell/azureps-cmdlets-docs) ou as [APIs REST do Provedor de Recursos de Armazenamento do Azure](https://msdn.microsoft.com/library/azure/mt163683.aspx).

Com o modelo Resource Manager, você coloca a conta de armazenamento em um grupo de recursos e controla o acesso ao plano de gerenciamento dessa conta de armazenamento específica usando o Azure Active Directory. Por exemplo, é possível permitir que usuários específicos acessem as chaves da conta de armazenamento, enquanto outros usuários podem exibir informações sobre a conta de armazenamento, mas não podem acessar suas chaves.

#### <a name="granting-access"></a>Concessão de acesso
O acesso é concedido atribuindo-se a função RBAC apropriada a usuários, grupos e aplicativos no escopo correto. Para conceder acesso à assinatura inteira, você atribui uma função no nível de assinatura. É possível conceder acesso a todos os recursos em um grupo de recursos concedendo permissões ao próprio grupo de recursos. Você também pode atribuir funções específicas a recursos específicos, como contas de armazenamento.

Veja os principais pontos que você precisa saber sobre como usar o RBAC para acessar as operações de gerenciamento de uma conta de armazenamento do Azure:

* Ao atribuir acesso, você basicamente atribui uma função à conta que você quer ter acesso. Você pode controlar o acesso às operações usadas para gerenciar essa conta de armazenamento, mas não aos objetos de dados da conta. Por exemplo, é possível conceder permissão para recuperar as propriedades da conta de armazenamento (como a redundância), mas não a um contêiner ou dados dentro de um contêiner no Armazenamento de Blobs.
* Para que alguém tenha permissão para acessar os objetos de dados na conta de armazenamento, você pode conceder a ele permissão para ler as chaves da conta de armazenamento, e esse usuário poderá então usar essas chaves para acessar os blobs, as filas, as tabelas e os arquivos.
* As funções podem ser atribuídas a uma conta de usuário específica, a um grupo de usuários ou a um aplicativo específico.
* Cada função tem uma lista de Ações e de Não Ações. Por exemplo, a função Colaborador de Máquina Virtual tem uma Ação de "listKeys" que permite que as chaves da conta de armazenamento sejam lidas. O Colaborador tem "Não Ações", tais como atualizar o acesso de usuários no Active Directory.
* As funções de armazenamento incluem (entre outras):

  * Proprietário – ele pode gerenciar tudo, inclusive o acesso.
  * Colaborador – ele pode fazer tudo que o proprietário pode fazer, menos atribuir acesso. Alguém com essa função pode exibir e regenerar as chaves da conta de armazenamento. Com as chaves da conta de armazenamento, ele pode acessar os objetos de dados.
  * Leitor – ele pode exibir informações sobre a conta de armazenamento, exceto os segredos. Por exemplo, se você atribuir a alguém uma função com permissões de leitor na conta de armazenamento, ele poderá exibir as propriedades da conta de armazenamento, mas não poderá fazer alterações nas propriedades nem exibir as chaves da conta de armazenamento.
  * Colaborador da Conta de Armazenamento – ele pode gerenciar a conta de armazenamento; ele pode ler os grupos de recursos e os recursos da assinatura, bem como criar e gerenciar implantações de grupo de recursos da assinatura. Eles também podem acessar as chaves da conta de armazenamento que, por sua vez, significa que eles podem acessar o plano de dados.
  * Administrador de Acesso do Usuário – ele pode gerenciar o acesso do usuário à conta de armazenamento. Por exemplo, ele pode conceder acesso de Leitor a um usuário específico.
  * Colaborador de Máquina Virtual – ele pode gerenciar máquinas virtuais, mas não a conta de armazenamento à qual ele está conectado. Essa função pode listar as chaves da conta de armazenamento, o que significa que o usuário a quem você atribui essa função pode atualizar o plano de dados.

    Para que um usuário crie uma máquina virtual, ele deve poder criar o arquivo VHD correspondente em uma conta de armazenamento. Para fazer isso, ele precisa poder recuperar a chave da conta de armazenamento e passá-la à API que está criando a VM. Portanto, ele deve ter essa permissão para que possa listar as chaves da conta de armazenamento.
* A capacidade de definir funções personalizadas é um recurso que permite compor um conjunto de ações usando uma lista de ações disponíveis que podem ser executadas nos recursos do Azure.
* O usuário deve ser configurado no Azure Active Directory antes de você atribuir uma função a ele.
* Usando o PowerShell ou a CLI do Azure, você pode criar um relatório de quem concedeu/revogou que tipo de acesso a/de quem e em que escopo.

#### <a name="resources"></a>Recursos
* [Controle de acesso baseado em função do Active Directory do Azure](../../role-based-access-control/role-assignments-portal.md)

  Esse artigo explica o Controle de Acesso Baseado em Função do Azure Active Directory.
* [RBAC: Funções internas](../../role-based-access-control/built-in-roles.md)

  Esse artigo detalha todas as funções disponíveis internas no RBAC.
* [Noções básicas sobre a implantação do Gerenciador de Recursos e a implantação clássica](../../azure-resource-manager/resource-manager-deployment-model.md)

  Esse artigo explica os modelos de implantação clássica e implantação Resource Manager, além de explicar os benefícios de usar o Resource Manager e os grupos de recursos. Explica como os Provedores de Computação, Rede e Armazenamento do Azure funcionam no modelo do Gerenciador de Recursos.
* [Gerenciar o controle de acesso com base em função com a API REST](../../role-based-access-control/role-assignments-rest.md)

  Esse artigo mostra como usar a API REST para gerenciar o RBAC.
* [Azure Storage Resource Provider REST API Reference (Referência à API REST do provedor de recursos de armazenamento do Azure)](https://msdn.microsoft.com/library/azure/mt163683.aspx)

  Esta referência de API descreve as APIs que você pode usar para gerenciar sua conta de armazenamento de modo programático.
* [Usar API de autenticação do Resource Manager para acessar assinaturas](../../azure-resource-manager/resource-manager-api-authentication.md)

  Este artigo mostra como autenticar usando as APIs do Gerenciador de Recursos.
* [Role-Based Access Control for Microsoft Azure from Ignite (Controle de Acesso Baseado em Função do Microsoft Azure do Ignite)](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

  Este é um link para um vídeo da conferência MS Ignite de 2015, no Channel 9. Nessa sessão, falam sobre gerenciamento de acesso e recursos de relatório no Azure, além de explorar práticas recomendadas em torno de proteção do acesso às assinaturas do Azure usando o Azure Active Directory.

### <a name="managing-your-storage-account-keys"></a>Gerenciando as chaves da conta de armazenamento
As chaves da conta de armazenamento são cadeias de caracteres de 512 bits criadas pelo Azure que, com o nome da conta de armazenamento, podem ser usadas para acessar os objetos de dados armazenados na conta de armazenamento, por exemplo, blobs, entidades em uma tabela, mensagens na fila e arquivos em um compartilhamento de arquivos do Azure. O controle do acesso às chaves da conta de armazenamento controla o acesso ao plano de dados dessa conta de armazenamento.

Cada conta de armazenamento tem duas chaves, conhecidas como "Chave 1" e "Chave 2" no [Portal do Azure](https://portal.azure.com/) e nos cmdlets do PowerShell. Elas podem ser regeneradas manualmente usando um dos vários métodos, incluindo, entre outros, o [Portal do Azure](https://portal.azure.com/), o PowerShell, a CLI do Azure ou, de modo programático, a Biblioteca de Cliente de Armazenamento .NET ou a API REST dos Serviços de Armazenamento do Microsoft Azure.

Há vários motivos para regenerar suas chaves da conta de armazenamento.

* Você pode regenerá-las regularmente por motivos de segurança.
* Você poderia regenerar as chaves da conta de armazenamento se alguém conseguisse invadir um aplicativo e acessasse a chave que estava codificada ou salva em um arquivo de configuração, dando a ele acesso completo à sua conta de armazenamento.
* Outro caso para nova geração de chave é se sua equipe estivesse usando um aplicativo Gerenciador de Armazenamento que mantivesse a chave da conta de armazenamento e um dos membros da equipe saísse. O aplicativo continuaria funcionando, dando a ele acesso à sua conta de armazenamento depois que ele fosse embora. Na verdade, esse é o principal motivo para a criação das Assinaturas de Acesso Compartilhado de nível de conta – você pode usar uma SAS de nível de conta em vez de armazenar as chaves de acesso em um arquivo de configuração.

#### <a name="key-regeneration-plan"></a>Plano de nova geração de chave
Você não desejará apenas regenerar a chave que está usando sem nenhum planejamento. Se fizer isso, você poderá cortar todo o acesso a essa conta de armazenamento, o que pode causar uma grande interrupção. É por isso que há duas chaves. Você deve regenerar uma chave de cada vez.

Antes de regenerar suas chaves, certifique-se de que tenha uma lista de todos os seus aplicativos que dependem da conta de armazenamento, bem como de todos os outros serviços que está usando no Azure. Por exemplo, se você estiver usando os Serviços de Mídia do Azure que dependem da sua conta de armazenamento, será preciso ressincronizar as chaves de acesso com o serviço de mídia depois de regenerar a chave. Se estiver usando quaisquer aplicativos, como um Explorador de Armazenamento, você precisará fornecer as novas chaves para esses aplicativos também. Se você tiver VMs cujos arquivos VHD são armazenados na conta de armazenamento, eles não serão afetados pela regeneração das chaves da conta de armazenamento.

É possível regenerar suas chaves no Portal do Azure. Depois que as chaves forem regeneradas, pode levar até 10 minutos até serem sincronizadas entre os Serviços de Armazenamento.

Quando você estiver pronto, aqui está o processo geral que detalha como você deve alterar sua chave. Nesse caso, a suposição é a de que você esteja usando no momento a Chave 1 e que vai mudar tudo para usar a Chave 2 no lugar.

1. Regenere a Chave 2 para garantir que ela seja protegida. Você pode fazer isso no Portal do Azure.
2. Em todos os aplicativos em que a chave de armazenamento é armazenada, altere a chave de armazenamento para usar o novo valor da Chave 2. Teste e publique o aplicativo.
3. Depois que todos os aplicativos e serviços estiverem funcionando perfeitamente, regenere a Chave 1. Isso garante que qualquer pessoa a quem você não concedeu expressamente a nova chave não terá mais acesso à conta de armazenamento.

Se, no momento, você estiver usando a Chave 2, será possível usar o mesmo processo, mas inverta os nomes das chaves.

É possível fazer a migração em dois dias, alterando cada aplicativo para usar a nova chave e publicando-o. Depois de fazer isso com todos, você deverá voltar e regenerar a chave antiga, pois ela não funciona mais.

Outra opção é colocar a chave de conta de armazenamento em um [Cofre de Chaves do Azure](https://azure.microsoft.com/services/key-vault/) como um segredo e fazer com que os aplicativos recuperam a chave de lá. Assim, quando você regenerar a chave e atualizar o Cofre de Chaves do Azure, os aplicativos não precisarão ser reimplantados porque eles selecionarão a nova chave no Cofre de Chaves do Azure automaticamente. Observe que você pode fazer com que o aplicativo leia a chave toda vez que for preciso ou pode armazená-la em cache na memória e, caso ela falhe na hora que você for usá-la, recupere-a novamente no Cofre de Chaves do Azure.

Usar o Cofre de Chaves do Azure também acrescenta outro nível de segurança para suas chaves de armazenamento. Ao usar esse método, você nunca terá a chave de armazenamento codificada em um arquivo de configuração, o que remove a possibilidade de alguém obter acesso às chaves sem permissão específica.

Outra vantagem de usar o Cofre de Chaves do Azure é que você também pode controlar o acesso às suas chaves usando o Azure Active Directory Isso significa que é possível conceder acesso a um punhado de aplicativos que precisam recuperar as chaves no Cofre de Chaves do Azure e saber que outros aplicativos não poderão acessar as chaves sem que você conceda especificamente permissão a eles.

> [!NOTE]
> A Microsoft recomenda usar apenas uma das chaves em todos os aplicativos ao mesmo tempo. Se você usar a Chave 1 em alguns lugares e a Chave 2 em outros, nãos será possível alternar as chaves sem que algum aplicativo perca o acesso.

#### <a name="resources"></a>Recursos

* [Gerenciar configurações de conta de armazenamento no portal do Azure](storage-account-manage.md)
* [Azure Storage Resource Provider REST API Reference (Referência à API REST do provedor de recursos de armazenamento do Azure)](https://msdn.microsoft.com/library/mt163683.aspx)

## <a name="data-plane-security"></a>Segurança do plano de dados
A Segurança do Plano de Dados refere-se aos métodos usados para proteger os objetos de dados armazenados no Armazenamento do Azure – blobs, filas, tabelas e arquivos. Já vimos métodos para criptografar os dados e a segurança durante a transferência de dados. Mas e quanto a controlar o acesso aos objetos?

Você tem três opções para autorizar o acesso a objetos de dados no Armazenamento do Azure, incluindo:

- Usando o Azure AD para autorizar o acesso aos contêineres e filas. O Azure AD oferece vantagens em relação a outras abordagens para autorização, incluindo a eliminação da necessidade de armazenar segredos em seu código. Para saber mais, confira [Autenticar o acesso ao Armazenamento do Azure usando o Azure Active Directory](storage-auth-aad.md). 
- Usar as chaves da conta de armazenamento para autorizar o acesso via Chave Compartilhada. A autorização via Chave Compartilhada requer o armazenamento das chaves da conta de armazenamento em seu aplicativo e, portanto, a Microsoft recomenda usar o Azure AD sempre que possível.
- Usar Assinaturas de Acesso Compartilhado para conceder permissões controladas a objetos de dados específicos por um determinado período de tempo.

Além disso, para o Armazenamento de Blobs, você pode permitir o acesso público aos blobs configurando o nível de acesso para o contêiner que contém os blobs de acordo. Se você definir o acesso de um contêiner para Blob ou Contêiner, isso permitirá acesso público de leitura para os blobs desse contêiner. Isso significa que qualquer pessoa com uma URL apontando para um blob nesse contêiner poderá abri-lo em um navegador sem usar uma Assinatura de Acesso Compartilhado ou sem ter as chaves da conta de armazenamento.

Além de limitar o acesso por meio da autorização, você também pode usar [Firewalls e redes virtuais](storage-network-security.md) para limitar o acesso à conta de armazenamento com base nas regras de rede.  Essa abordagem permite que você negue o acesso ao tráfego de Internet pública e conceda acesso apenas às Redes Virtuais do Azure ou intervalos de endereços IP da Internet pública específicos.

### <a name="storage-account-keys"></a>Chaves da conta de armazenamento
As chaves da conta de armazenamento são cadeias de caracteres de 512 bits criadas pelo Azure que, com o nome da conta de armazenamento, podem ser usadas para acessar os objetos de dados armazenados na conta de armazenamento.

Por exemplo, você pode ler blobs, gravar nas filas, criar tabelas e modificar arquivos. Muitas dessas ações podem ser executadas no Portal do Azure ou usando um dos muitos aplicativos do Gerenciador de Armazenamento. Você também pode escrever código para usar a API REST ou uma das Bibliotecas de Cliente de Armazenamento para executar essas operações.

Conforme discutido na seção sobre [Segurança do plano de gerenciamento](#management-plane-security), o acesso às chaves de armazenamento para uma conta de armazenamento Clássica pode ser concedido dando acesso completo à assinatura do Azure. O acesso às chaves de armazenamento para uma conta de armazenamento usando o modelo Azure Resource Manager pode ser controlado por meio do RBAC (Controle de Acesso Baseado em Função).

### <a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>Como delegar acesso a objetos em sua conta usando Assinaturas de Acesso Compartilhado e Políticas de Acesso Armazenado
Uma Assinatura de Acesso Compartilhado é uma cadeia de caracteres que contém um token de segurança que pode ser anexado a um URI que permite delegar acesso aos objetos de armazenamento e especificar restrições, como as permissões e o intervalo de data/hora de acesso.

Você pode conceder acesso a blobs, contêineres, mensagens de fila, arquivos e tabelas. Com tabelas, na verdade, você pode conceder permissão para acessar um intervalo de entidades na tabela especificando os intervalos de chaves de linha e partição ao quais você quer que o usuário tenha acesso. Por exemplo, se você tiver dados armazenados com uma chave de partição de estado geográfico, será possível conceder a alguém acesso apenas aos dados da Califórnia.

Em outro exemplo, você pode fornecer a um aplicativo Web um token SAS que o permita gravar entradas em uma fila, bem como fornecer a um aplicativo de função de trabalho um token SAS para obter mensagens da fila e processá-las. Ou você pode fornecer a um cliente um token SAS que ele pode usar para carregar imagens em um contêiner no Armazenamento de Blobs, bem como fornecer a um aplicativo Web permissão para ler essas imagens. Em ambos os casos, há uma separação de interesses – cada aplicativo pode receber apenas o acesso que eles precisam para realizar as respectivas tarefas. Isso é possível graças às Assinaturas de Acesso Compartilhado.

#### <a name="why-you-want-to-use-shared-access-signatures"></a>Por que usar as Assinaturas de Acesso Compartilhado
Por que você ia querer usar uma SAS em vez de apenas fornecer sua chave da conta de armazenamento, o que é muito mais fácil? Fornecer a chave da conta de armazenamento é como compartilhar as chaves do seu reino de armazenamento. Isto é, ela concede acesso a tudo. Um indivíduo poderia usar as chaves e carregar toda a biblioteca de músicas dele na sua conta de armazenamento. Ele também poderia substituir seus arquivos por versões infectadas por vírus ou até mesmo roubar seus dados. Dar acesso ilimitado à sua conta de armazenamento é algo que não se pode fazer de modo leviano.

Com as Assinaturas de Acesso Compartilhado, você pode dar a um cliente apenas as permissões necessárias por um período limitado. Por exemplo, se uma pessoa estiver carregando um blob em sua conta, você poderá conceder acesso de gravação a ela apenas pelo tempo suficiente para carregar o blob (dependendo do tamanho do blob, é claro). E se você mudar de ideia, poderá revogar esse acesso.

Além disso, você pode especificar que as solicitações feitas usando uma SAS sejam restritas a um determinado endereço IP ou intervalo de endereços IP fora do Azure. Você também pode exigir que as solicitações sejam feitas usando um protocolo específico (HTTPS ou HTTP/HTTPS). Isso significa que se você deseja permitir apenas o tráfego HTTPS, será possível definir o protocolo necessário para apenas HTTPS, e o tráfego HTTP será bloqueado.

#### <a name="definition-of-a-shared-access-signature"></a>Definição de uma Assinatura de Acesso Compartilhado
Uma Assinatura de Acesso Compartilhado é um conjunto de parâmetros de consulta acrescentado à URL que aponta para o recurso

que fornece informações sobre o acesso permitido e por quanto tempo o acesso é permitido. Veja um exemplo; este URI fornece acesso de leitura a um blob por cinco minutos. Observe que os parâmetros de consulta SAS devem ser Codificados pela URL, como %3A para dois-pontos (:) e %20 para um espaço.

```
http://mystorage.blob.core.windows.net/mycontainer/myblob.txt (URL to the blob)
?sv=2015-04-05 (storage service version)
&st=2015-12-10T22%3A18%3A26Z (start time, in UTC time and URL encoded)
&se=2015-12-10T22%3A23%3A26Z (end time, in UTC time and URL encoded)
&sr=b (resource is a blob)
&sp=r (read access)
&sip=168.1.5.60-168.1.5.70 (requests can only come from this range of IP addresses)
&spr=https (only allow HTTPS requests)
&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D (signature used for the authentication of the SAS)
```

#### <a name="how-the-shared-access-signature-is-authorized-by-the-azure-storage-service"></a>Como a Assinatura de Acesso Compartilhado é autorizada pelo Serviço de Armazenamento do Azure
Quando o serviço de armazenamento recebe a solicitação, ele usa os parâmetros de consulta de entrada e cria uma assinatura usando o mesmo método que o programa de chamada. Ele então compara as duas assinaturas. Se elas coincidirem, o serviço de armazenamento poderá verificar a versão do serviço de armazenamento para garantir que ele seja válido, verificar se a data e a hora atuais estão dentro da janela especificada, ter certeza de que o acesso solicitado corresponde à solicitação feita, etc.

Por exemplo, se a URL acima estivesse apontando para um arquivo em vez de para um blob, essa solicitação falharia, pois ela especifica que a Assinatura de Acesso Compartilhado é para um blob. Se o comando REST que está sendo chamado fosse para atualizar um blob, ele falharia, pois a Assinatura de Acesso Compartilhado especifica que somente o acesso de leitura é permitido.

#### <a name="types-of-shared-access-signatures"></a>Tipos de Assinatura de Acesso Compartilhado
* Uma SAS de nível de serviço pode ser usada para acessar recursos específicos em uma conta de armazenamento. Alguns exemplos disso são a recuperação de uma lista de blobs em um contêiner, o download de um blob, a atualização de uma entidade em uma tabela, a adição de mensagens a uma fila ou o carregamento de um arquivo em um compartilhamento de arquivos.
* Uma SAS de nível de conta pode ser usada para acessar tudo aquilo para o que ela pode ser usada. Além disso, ela pode fornecer opções para recursos que não são permitidas com uma SAS de nível de serviço, como a capacidade de criar contêineres, tabelas, filas e compartilhamentos de arquivo. Você também pode especificar acesso a vários serviços ao mesmo tempo. Por exemplo, você pode conceder a uma pessoa acesso aos blobs e aos arquivos na sua conta de armazenamento.

#### <a name="creating-a-sas-uri"></a>Criar um URI de SAS
1. Você pode criar um URI sob demanda, definindo todos os parâmetros de consulta todas as vezes.

   Essa abordagem é flexível, mas se você tiver um conjunto lógico de parâmetros que sempre são semelhantes, usar uma Política de Acesso Armazenado é uma opção mais adequada.
2. É possível criar uma Política de Acesso Armazenado para um contêiner inteiro, um compartilhamento de arquivos, uma tabela ou uma fila. Desse modo, você pode usar isso como a base para os URIs SAS que cria. As permissões com base em Políticas de Acesso Armazenado podem ser facilmente revogadas. Você pode ter até cinco políticas definidas em cada contêiner, fila, tabela ou compartilhamento de arquivos.

   Por exemplo, se muitas pessoas lessem os blobs em um contêiner específico, você poderia criar uma Política de Acesso Armazenado que informasse "fornecer acesso de leitura" e todas as outras configurações que seriam as mesmas todas as vezes. Assim, você poderia criar um URI de SAS usando as configurações da Política de Acesso Armazenado e especificando a data/hora de expiração. A vantagem disso é que você não precisa especificar todos os parâmetros de consulta todas as vezes.

#### <a name="revocation"></a>Revogação
Suponha que a SAS foi comprometida ou que você queira alterá-la devido a requisitos de conformidade normativa ou segurança corporativa. Como você revoga o acesso a um recurso usando essa SAS? Depende de como você criou o URI da SAS.

Se estiver usando URIs ad hoc, você terá três opções. Você pode emitir tokens SAS com políticas de expiração curta e aguardar a SAS expirar. Você pode renomear ou excluir o recurso (supondo que o escopo do token fosse para um único objeto). Você pode alterar as chaves da conta de armazenamento. Essa última opção pode ter um impacto significativo dependendo de quantos serviços estão usando essa conta de armazenamento, o que provavelmente não é algo que você queira fazer sem planejamento.

Se estiver usando uma SAS derivada de uma Política de Acesso Armazenado, você poderá remover o acesso revogando a Política de Acesso Armazenado. Basta alterá-la para que ela expire ou removê-la completamente. Isso entra em vigor imediatamente e invalida cada SAS criada usando essa Política de Acesso Armazenado. Atualizar ou remover a Política de Acesso Armazenado pode afetar pessoas que estão acessando um contêiner, um compartilhamento de arquivos, uma tabela ou uma fila específicos por meio de SAS, mas se os clientes estiverem gravando, eles solicitam uma nova SAS quando a antiga se tornar inválida; isso funciona sem problemas.

Como usar uma SAS derivada de uma Política de Acesso Armazenado dá a possibilidade de revogar essa SAS imediatamente, a prática recomendada é, sempre que possível, usar Políticas de Acesso Armazenado.

#### <a name="resources"></a>Recursos
Obtenha informações mais detalhadas sobre como usar as Assinatura de Acesso Compartilhado e as Políticas de Acesso Armazenado, com exemplos, nos artigos a seguir:

* Estes são os artigos de referência.

  * [Service SAS (SAS de serviço)](https://msdn.microsoft.com/library/dn140256.aspx)

    Esse artigo fornece exemplos de como usar uma SAS de nível de serviço com blobs, mensagens da fila, intervalos de tabelas e arquivos.
  * [Constructing a service SAS (Criação de uma SAS de serviço)](https://msdn.microsoft.com/library/dn140255.aspx)
  * [Constructing an account SAS (Criação de uma SAS de conta)](https://msdn.microsoft.com/library/mt584140.aspx)
* Estes são os tutoriais de como usar a biblioteca de cliente .NET para criar Assinatura de Acesso Compartilhado e Políticas de Acesso Armazenado.

  * [Uso de SAS (Assinaturas de Acesso Compartilhado)](../storage-dotnet-shared-access-signature-part-1.md)
  * [Assinatura de Acesso Compartilhado, Parte 2: Criar e usar uma SAS com o Serviço Blob](../blobs/storage-dotnet-shared-access-signature-part-2.md)

    Esse artigo inclui uma explicação do modelo SAS, exemplos de Assinatura de Acesso Compartilhado e recomendações para uso da prática recomendada de SAS. Também é abordada a revogação da permissão concedida.

* Autenticação

  * [Autenticação para os Serviços de Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* Tutorial de introdução às Assinaturas de Acesso Compartilhado

  * [SAS Getting Started Tutorial (Tutorial de introdução à SAS)](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

## <a name="encryption-in-transit"></a>Criptografia em trânsito
### <a name="transport-level-encryption--using-https"></a>Criptografia no nível de transporte – usando HTTPS
Outra etapa que você deve executar para garantir a segurança dos dados do Armazenamento do Azure é criptografá-los entre o cliente e o Armazenamento do Microsoft Azure. A primeira recomendação é sempre usar o protocolo [HTTPS](https://en.wikipedia.org/wiki/HTTPS) , que garante a comunicação segura na Internet pública.

Para ter um canal de comunicação seguro, você sempre deve usar o HTTPS ao chamar as APIs REST ou acessar objetos no armazenamento. Além disso, as **Assinaturas de Acesso Compartilhado**, que podem ser usadas para delegar acesso a objetos do Armazenamento do Azure, incluem uma opção para especificar que apenas o protocolo HTTPS pode ser utilizado ao usar as Assinaturas de Acesso Compartilhado, garantindo que qualquer pessoa que envie links com tokens SAS usará o protocolo adequado.

É possível impor o uso de HTTPS ao chamar as APIs REST para acessar objetos em contas de armazenamento habilitando a opção [Transferência segura necessária](../storage-require-secure-transfer.md) na conta de armazenamento. As conexões que usam HTTP serão recusadas depois que essa opção for habilitada.

### <a name="using-encryption-during-transit-with-azure-file-shares"></a>Usando criptografia durante a transferência com compartilhamentos de arquivos do Azure
Os [Arquivos do Azure](../files/storage-files-introduction.md) dão suporte à criptografia com SMB 3.0 e com HTTPS ao usar a API REST do arquivo. Ao montar fora da região do Azure em que está o compartilhamento de arquivos do Azure, como localmente ou em outra região do Azure, o SMB 3.0 com criptografia será sempre exigido. O SMB 2.1 não dá suporte a criptografia e, portanto, por padrão, as conexões só são permitidas dentro da mesma região no Azure, mas no SMB 3.0 com criptografia, elas podem ser impostas pela [exigência de transferência segura](../storage-require-secure-transfer.md) para a conta de armazenamento.

O SMB 3.0 com criptografia está disponível em [todos os sistemas operacionais Windows e Windows Server com suporte](../files/storage-how-to-use-files-windows.md), exceto o Windows 7 e o Windows Server 2008 R2, que só dão suporte a SMB 2.1. Também há suporte para SMB 3.0 no [macOS](../files/storage-how-to-use-files-mac.md) e nas distribuições de [Linux](../files/storage-how-to-use-files-linux.md) usando o kernel do Linux 4.11 e superiores. O suporte à criptografia para SMB 3.0 também teve backport para versões mais antigas do kernel do Linux por várias distribuições do Linux; confira [Noções básicas de requisitos do cliente SMB](../files/storage-how-to-use-files-linux.md#smb-client-reqs).

### <a name="using-client-side-encryption-to-secure-data-that-you-send-to-storage"></a>Uso da criptografia do cliente para proteger os dados enviados para o armazenamento
Outra opção que ajuda a garantir que os dados sejam protegidos enquanto estão sendo transferidos entre um aplicativo cliente e o Armazenamento é a Criptografia do Cliente. Os dados são criptografados antes de serem transferidos para o Armazenamento do Microsoft Azure. Na recuperação dos dados do Armazenamento do Azure, os dados são descriptografados depois que são recebidos no cliente. Mesmo que os dados estejam criptografados durante a transferência, é recomendável usar também o HTTPS, pois ele tem as verificações de integridade de dados internas que ajudam a reduzir os erros de rede que afetam a integridade dos dados.

A criptografia do cliente também é um método para criptografar os dados em repouso, já que os dados são armazenados em sua forma criptografada. Falaremos sobre isso mais detalhadamente na seção sobre [Criptografia em repouso](#encryption-at-rest).

## <a name="encryption-at-rest"></a>Criptografia em repouso
Há três recursos do Azure que fornecem criptografia em repouso. O Azure Disk Encryption é usado para criptografar os discos de dados e do sistema operacional em Máquinas Virtuais IaaS. A Criptografia do Cliente e a SSE são usadas para criptografar dados no Armazenamento do Azure. 

Embora seja possível usar a Criptografia do Cliente para criptografar os dados em trânsito (que também são armazenados em sua forma criptografada no Armazenamento), talvez você prefira usar HTTPS durante a transferência e que os dados sejam criptografados automaticamente de alguma maneira quando são armazenados. Há duas maneiras de fazer isso: com o Azure Disk Encryption e com a SSE. Uma é usada para criptografar os dados diretamente nos discos de dados e do sistema operacional usados pelas VMs e a outra é usada para criptografar os dados gravados no Armazenamento de Blobs do Azure.

### <a name="storage-service-encryption-sse"></a>SSE (Criptografia do Serviço de Armazenamento)

A SSE é habilitada para todas as contas de armazenamento e não pode ser desabilitada. Ela criptografa automaticamente seus dados ao gravá-los no Armazenamento do Azure. Quando você faz a leitura de dados do Armazenamento do Azure, eles são descriptografados pelo armazenamento do Azure antes de serem retornados. A SSE permite proteger os dados sem precisar modificar código nem adicionar código a nenhum aplicativo.

Você pode usar chaves gerenciadas pela Microsoft ou suas próprias chaves personalizadas. A Microsoft gera as chaves gerenciadas e administra o armazenamento seguro delas, bem como sua rotatividade regular, conforme definido pela política interna da Microsoft. Para obter mais informações sobre como usar chaves personalizadas, consulte [Criptografia do Serviço de Armazenamento usando chaves gerenciadas pelo cliente no Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

A SSE criptografa automaticamente os dados em todos os níveis de desempenho (Standard e Premium), em todos os modelos de implantação (Azure Resource Manager e Clássico) e em todos os serviços do Armazenamento do Azure (Blobs, Filas, Tabelas e Arquivos). 

### <a name="client-side-encryption"></a>Criptografia do cliente
Mencionamos a criptografia do cliente ao abordar a criptografia dos dados em trânsito. Esse recurso permite criptografar os dados de modo programático em um aplicativo cliente antes de enviá-los para serem gravados no Armazenamento do Microsoft Azure, assim como descriptografá-los de modo programático depois de recuperá-los no Armazenamento do Microsoft Azure.

Ele fornece criptografia em trânsito, mas também fornece o recurso de Criptografia em Repouso. Embora os dados sejam criptografados em trânsito, ainda é aconselhável usar HTTPS para aproveitar as verificações de integridade de dados internas que ajudam a reduzir os erros de rede que afetam a integridade dos dados.

Um exemplo de onde é possível usar esse recurso é quando você tiver um aplicativo Web que armazene blobs e recupere blobs, e quando você quiser que o aplicativo e os dados sejam protegidos ao máximo. Nesse caso, você usa a criptografia do cliente. O tráfego entre o cliente e o Serviço Blob do Azure contém o recurso criptografado, e ninguém pode interpretar os dados em trânsito e reconstitui-los em seus blobs privados.

A criptografia do cliente é incorporada no Java e nas bibliotecas de cliente de armazenamento .NET que, por sua vez, usam as APIs do Azure Key Vault, o que facilita a implementação. O processo de criptografia e descriptografia de dados usa a técnica de envelope e armazena os metadados usados pela criptografia em cada objeto de armazenamento. Por exemplo, no caso de blobs, ele os armazena nos metadados do blob e, no caso de filas, ele os adiciona a cada mensagem da fila.

Para a criptografia em si, você pode gerar e gerenciar suas próprias chaves de criptografia. Você também pode usar as chaves geradas pela Biblioteca de Cliente de Armazenamento do Azure ou fazer com que o Cofre de Chaves do Azure as gere. É possível armazenar as chaves de criptografia em seu armazenamento de chaves local ou armazená-las no Cofre de Chaves do Azure. O Cofre de Chaves do Azure permite conceder a usuários específicos acesso aos segredos do Cofre de Chaves do Azure usando o Azure Active Directory. Isso significa que não é qualquer pessoa que pode ler o Azure Key Vault e recuperar as chaves que você está usando para criptografia do lado do cliente.

#### <a name="resources"></a>Recursos
* [Criptografar e Descriptografar Blobs no Armazenamento do Microsoft Azure usando o Cofre da Chave do Azure](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)

  Esse artigo mostra como usar a criptografia do cliente com o Cofre de Chaves do Azure, incluindo como criar o KEK e armazená-lo no cofre usando o PowerShell.
* [Criptografia do lado do cliente e o Cofre da Chave do Azure para o Armazenamento do Microsoft Azure](../storage-client-side-encryption.md)

  Esse artigo fornece uma explicação da criptografia do cliente, bem como exemplos de como usar a biblioteca de cliente de armazenamento para criptografar e descriptografar recursos dos quatro serviços de armazenamento. Ele também fala sobre o Cofre de Chaves do Azure.

### <a name="using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines"></a>Usando o Azure Disk Encryption para criptografar discos usados pelas máquinas virtuais
O Azure Disk Encryption permite criptografar os discos do sistema operacional e discos de dados usados por uma máquina Virtual IaaS. No Windows, as unidades são criptografadas usando a tecnologia de criptografia BitLocker padrão do setor. No Linux, os discos são criptografados usando a tecnologia DM-Crypt. Esse recurso é integrado ao Cofre de Chaves do Azure para permitir que você controle e gerencie as chaves de criptografia de disco.

A solução dá suporte aos seguintes cenários para VMs IaaS quando habilitados no Microsoft Azure:

* Integração com o Cofre da Chave do Azure
* VMs da camada padrão: [VMs IaaS das séries A, D, DS, G, GS e assim por diante](https://azure.microsoft.com/pricing/details/virtual-machines/)
* Como habilitar a criptografia em VMs IaaS Windows e Linux
* Como desabilitar a criptografia em unidades do sistema operacional e de dados para VMs IaaS do Windows
* Como desabilitar a criptografia em unidades de dados para VMs IaaS do Linux
* Ativando a criptografia em VMs IaaS que estão executando o sistema operacional cliente Windows
* Como habilitar a criptografia em volumes com caminhos de montagem
* Habilitação da criptografia em VMs do Linux que estão configuradas com distribuição de discos (RAID) usando o mdadm
* Como habilitar a criptografia em VMs do Linux usando LVM para discos de dados
* Ativando a criptografia em VMs do Windows que são configurados usando espaços de armazenamento
* Há suporte para todas as regiões públicas do Azure

A solução não dá suporte aos seguintes cenários, recursos e tecnologia na versão:

* VMs IaaS da camada Básica
* Como desabilitar a criptografia em unidades do sistema operacional para VMs IaaS do Linux
* VMs de IaaS que são criadas usando o método de criação de VM clássico
* Integração com o Serviço de Gerenciamento de Chaves no local
* Arquivos do Azure (sistema de arquivos compartilhados), NFS (Network File System), volumes dinâmicos e VMs do Windows configuradas com Sistemas RAID baseados em software


> [!NOTE]
> No momento, há suporte para a criptografia de disco do sistema operacional Linux nas seguintes distribuições Linux: RHEL 7.2, CentOS 7.2n e Ubuntu 16.04.
>
>

Esse recurso garante que todos os dados nos discos da máquina virtual sejam criptografados em repouso no Armazenamento do Microsoft Azure.

#### <a name="resources"></a>Recursos
* [Azure Disk Encryption para VMs IaaS Windows e Linux](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)

### <a name="comparison-of-azure-disk-encryption-sse-and-client-side-encryption"></a>Comparação do Azure Disk Encryption, da SSE e da Criptografia do Cliente

#### <a name="iaas-vms-and-their-vhd-files"></a>VMs IaaS e seus arquivos VHD

Para discos de dados usados por VMs IaaS, recomenda-se a Azure Disk Encryption. Se você criar uma VM com discos não gerenciados usando uma imagem do Azure Marketplace, o Azure realizara uma [cópia superficial](https://en.wikipedia.org/wiki/Object_copying) da imagem na sua conta de armazenamento no Armazenamento do Microsoft Azure e ela não será criptografada mesmo que você tenha habilitado a SSE. Depois de criar a VM e iniciar a atualização da imagem, a SSE começará a criptografia dos dados. Por esse motivo, é recomendável utilizar o Azure Disk Encryption em VMs com discos não gerenciados criados a partir de imagens no Azure Marketplace, caso queira que sejam totalmente criptografadas. Se você criar uma VM com discos gerenciados, a SSE criptografará todos os dados por padrão usando as chaves gerenciadas por plataforma. 

Ao transferir uma VM local previamente criptografada para o Azure, você pode carregar as chaves de criptografia no Cofre de Chaves do Azure e continuar usando a criptografia que estava usando no local para essa VM. O Azure Disk Encryption está habilitado para lidar com esse cenário.

Caso você tenha um VHD não criptografado no local, será possível carregá-lo na galeria como uma imagem personalizada e provisionar uma VM dela. Se fizer isso usando os modelos do Gerenciador de Recursos, você poderá pedir para ativar o Azure Disk Encryption quando a VM for inicializada.

Ao adicionar um disco de dados e montá-lo na VM, você pode ativar o Azure Disk Encryption nesse disco de dados. Ele criptografará primeiro esse disco de dados localmente e depois a camada de modelo de implantação clássica fará uma gravação lenta no armazenamento para que o conteúdo do armazenamento seja criptografado.

#### <a name="client-side-encryption"></a>Criptografia do cliente
A criptografia do lado do cliente é o método mais seguro de criptografar seus dados, porque faz isso antes da transmissão.  No entanto, ela exige a adição de código aos aplicativos usando armazenamento, o que talvez não seja conveniente. Nesses casos, você pode usar HTTPS para proteger os dados em trânsito. Quando os dados alcançam o Armazenamento do Azure, são criptografados por SSE.

Com a criptografia do cliente, você pode criptografar entidades de tabela, mensagens da fila e blobs. 

A criptografia do cliente é totalmente gerenciada pelo aplicativo. Essa é a abordagem mais segura, mas exige que você faça alterações programáticas no aplicativo e implemente processos de gerenciamento de chaves. Você a usa quando deseja segurança extra durante a transferência e que os dados armazenados sejam criptografados.

A criptografia do cliente significa mais carga no cliente, e você deve levar isso em conta em seus planos de escalabilidade, especialmente se estiver criptografando e transferindo uma enorme quantidade de dados.

#### <a name="storage-service-encryption-sse"></a>SSE (Criptografia do Serviço de Armazenamento)

A SSE é gerenciada pelo Armazenamento do Microsoft Azure. A SSE não fornece segurança aos dados em trânsito, mas ela criptografa os dados conforme eles são gravados no Armazenamento do Azure. A SSE não afeta o desempenho do Armazenamento do Azure.

Você pode criptografar qualquer tipo de dados da conta de armazenamento usando SSE (blobs de blocos, blobs de acréscimo, blobs de página, dados de tabela, dados da fila e arquivos).

Se tiver um arquivo ou uma biblioteca de arquivos VHD que usa como base para a criação de novas máquinas virtuais, você poderá criar uma nova conta de armazenamento e carregar os arquivos VHD nessa conta. Esses arquivos VHD serão criptografados pelo Armazenamento do Microsoft Azure.

Se você tiver habilitado o Azure Disk Encryption para os discos em uma VM, todos os dados recém-gravados serão criptografados tanto pela SSE como pela Azure Disk Encryption.

## <a name="storage-analytics"></a>Análise de Armazenamento
### <a name="using-storage-analytics-to-monitor-authorization-type"></a>Usando a Análise de Armazenamento para monitorar o tipo de autorização
Para cada conta de armazenamento, você pode habilitar a Análise de Armazenamento do Azure para realizar o registro em log e armazenar dados de métricas. Essa é uma excelente ferramenta a ser usada quando você quiser verificar as métricas de desempenho de uma conta de armazenamento ou quando precisar solucionar problemas de uma conta de armazenamento porque está tendo problemas de desempenho.

Outra parte dos dados que você pode ver nos logs da análise de armazenamento é o método de autenticação usado por uma pessoa quando ela acessa o armazenamento. Por exemplo, no Armazenamento de Blobs, você pode ver se a pessoa usou uma Assinatura de Acesso Compartilhado ou as chaves da conta de armazenamento, ou se o blob acessado era público.

Isso pode ser útil se você estiver protegendo rigorosamente o acesso ao armazenamento. Por exemplo, no Armazenamento de Blobs, é possível definir todos os contêineres para privado e implementar o uso de um serviço SAS em todos os aplicativos. Desse modo, você pode verificar os logs regularmente para ver se seus blobs são acessados usando as chaves da conta de armazenamento, que pode indicar uma violação de segurança ou se os blobs são públicos, mas não deveriam ser.

#### <a name="what-do-the-logs-look-like"></a>Como os logs devem ser?
Depois de habilitar o registro em log e as métricas da conta de armazenamento usando o Portal do Azure, os dados de análise começarão a se acumular rapidamente. O registro em log e as métricas para cada serviço são distintos; o registro em log é feito apenas quando há atividade na conta de armazenamento, enquanto as métricas são registradas a cada minuto, de hora em hora, ou todos os dias, de acordo com a configuração.

Os logs são armazenados em blobs de blocos em um contêiner chamado $logs na conta de armazenamento. Esse contêiner é criado automaticamente quando a Análise de Armazenamento é habilitada. Uma vez criado, esse contêiner não pode ser excluído, embora você possa excluir seu conteúdo.

No contêiner $logs, há uma pasta para cada serviço e há subpastas para ano/mês/dia/hora. Em hora, os logs são numerados. Esta é a aparência da estrutura do diretório:

![Exibição dos arquivos de log](./media/storage-security-guide/image1.png)

Cada solicitação no Armazenamento do Azure é registrada. Veja um instantâneo de um arquivo de log, mostrando os primeiros campos.

![Instantâneo de um arquivo de log](./media/storage-security-guide/image2.png)

É possível ver que você pode usar os logs para rastrear qualquer tipo de chamada para uma conta de armazenamento.

#### <a name="what-are-all-of-those-fields-for"></a>Para que servem todos esses campos?
Há um artigo listado nos recursos abaixo que fornece a lista dos muitos campos nos logs e para que eles são usados. Veja a lista de campos na ordem:

![Instantâneo de campos em um arquivo de log](./media/storage-security-guide/image3.png)

Estamos interessados nas entradas para GetBlob e em como elas são autorizadas, de modo que precisamos procurar entradas com operation-type "Get-Blob" e verificar request-status (quarta</sup> coluna) e authorization-type (oitava</sup> coluna).

Por exemplo, nas primeiras linhas na lista acima, request-status é "Success" e authorization-type é "authenticated". Isso significa que a solicitação foi autorizada usando a chave da conta de armazenamento.

#### <a name="how-is-access-to-my-blobs-being-authorized"></a>Como o acesso aos meus blobs está sendo autorizado?
Temos três casos que nos interessam.

1. O blob é público e é acessado usando uma URL sem uma Assinatura de Acesso Compartilhado. Nesse caso, request-status é "AnonymousSuccess" e authorization-type é "anonymous".

   1.0;2015-11-17T02:01:29.0488963Z;GetBlob;**AnonymousSuccess**;200;124;37;**anonymous**;;mystorage…
2. O blob é privado e foi usado com uma Assinatura de Acesso Compartilhado. Nesse caso, request-status é "SASSuccess" e authorization-type é "sas".

   1.0;2015-11-16T18:30:05.6556115Z;GetBlob;**SASSuccess**;200;416;64;**sas**;;mystorage…
3. O blob é privado e a chave de armazenamento foi usada para acessá-lo. Nesse caso, request-status é "**Success**" e authorization-type é "**authenticated**".

   1.0;2015-11-16T18:32:24.3174537Z;GetBlob;**Success**;206;59;22;**authenticated**;mystorage…

Você pode usar o Analisador de Mensagem da Microsoft para exibir e analisar esses logs. Ele inclui recursos de pesquisa e filtro. Por exemplo, talvez você queira procurar instâncias de GetBlob para ver se o uso é o esperado, isto é, para garantir que ninguém esteja acessando a conta de armazenamento indevidamente.

#### <a name="resources"></a>Recursos
* [Análise de Armazenamento](../storage-analytics.md)

  Esse artigo é uma visão geral da análise de armazenamento e de como habilitá-la.
* [Formato de Log de análise de armazenamento](https://msdn.microsoft.com/library/azure/hh343259.aspx)

  Esse artigo ilustra o Formato de Log da Análise de Armazenamento, além de detalhar os campos disponíveis nele, incluindo authentication-type, que indica o tipo de autenticação usado para a solicitação.
* [Monitorar uma conta de armazenamento no Portal do Azure](../storage-monitor-storage-account.md)

  Esse artigo mostra como configurar o monitoramento de métricas e registro em log para uma conta de armazenamento.
* [Solução de problemas ponta a ponta usando Métricas de Armazenamento do Azure e Registro em Log, AzCopy e Analisador de Mensagem](../storage-e2e-troubleshooting.md)

  Esse artigo aborda a solução de problemas usando a Análise de Armazenamento e mostra como usar o Analisador de Mensagem da Microsoft.
* [Guia Operacional do Analisador de Mensagem da Microsoft](https://technet.microsoft.com/library/jj649776.aspx)

  Esse artigo é a referência do Analisador de Mensagem da Microsoft e inclui links para um tutorial, início rápido e resumo do recurso.

## <a name="cross-origin-resource-sharing-cors"></a>CORS (Compartilhamento de Recursos entre Origens)
### <a name="cross-domain-access-of-resources"></a>Acesso de recursos entre domínios
Quando um navegador da Web em execução em um domínio faz uma solicitação HTTP a um recurso de outro domínio, isso é chamado de solicitação HTTP entre origens. Por exemplo, uma página HTML no site da contoso.com faz uma solicitação para um jpeg hospedado em fabrikam.blob.core.windows.net. Por motivos de segurança, os navegadores restringem as solicitações HTTP entre origens iniciadas dentro de scripts, como JavaScript. Isso significa que quando algum código JavaScript em uma página da Web de contoso.com solicita esse jpeg em fabrikam.blob.core.windows.net, o navegador não permite a solicitação.

O que isso tem a ver com o Armazenamento do Azure? Bem, se você estiver armazenando ativos estáticos, como arquivos de dados XML ou JSON no Armazenamento de Blobs usando uma conta de armazenamento chamada Fabrikam, o domínio para os ativos será fabrikam.blob.core.windows.net e o aplicativo Web contoso.com não poderá acessá-los usando JavaScript porque os domínios são diferentes. Isso também se aplicará se você estiver tentando chamar um dos Serviços de Armazenamento do Azure (como o Armazenamento de Tabelas) que retornam dados JSON para serem processados pelo cliente JavaScript.

#### <a name="possible-solutions"></a>Soluções possíveis
Uma maneira de resolver isso é atribuir um domínio personalizado, como "storage.contoso.com" a fabrikam.blob.core.windows.net. O problema é que você só pode atribuir esse domínio personalizado a uma conta de armazenamento. E se os ativos estiverem armazenados em várias contas de armazenamento?

Outra maneira de resolver esse problema é ter o aplicativo Web que atua como proxy para as chamadas de armazenamento. Isso significa que se você estiver carregando um arquivo no Armazenamento de Blobs, o aplicativo Web o gravará localmente e, em seguida, o copiará no Armazenamento de Blobs, ou lerá todo ele na memória e depois o gravará no Armazenamento de Blobs. Como alternativa, você escrever um aplicativo Web dedicado (como uma API Web) que carrega os arquivos localmente e os grava no Armazenamento de Blobs. De qualquer forma, é preciso prestar contas a essa função ao determinar a escalabilidade necessária.

#### <a name="how-can-cors-help"></a>Como o CORS pode ajudar?
O Armazenamento do Azure permite habilitar o CORS – Compartilhamento de Recursos entre Origens. Para cada conta de armazenamento, é possível especificar os domínios que podem acessar os recursos na conta de armazenamento. Por exemplo, no caso descrito acima, podemos pode habilitar o CORS na conta de armazenamento fabrikam.blob.core.windows.net e configurá-lo para permitir o acesso em contoso.com. Assim, o aplicativo Web de contoso.com pode acessar diretamente os recursos em fabrikam.blob.core.windows.net.

Observe que o CORS permite o acesso, mas não fornece autenticação, que é obrigatória para todo acesso não público dos recursos de armazenamento. Isso significa que você poderá acessar blobs somente se eles forem públicos ou se incluir uma Assinatura de Acesso Compartilhado dando a você permissão adequada. Arquivos, filas e tabelas não têm acesso público e exigem uma SAS.

Por padrão, o CORS está desabilitado em todos os serviços Você pode habilitar o CORS usando a API REST ou a biblioteca de cliente de armazenamento para chamar um dos métodos e definir as políticas de serviço. Ao fazer isso, você inclui uma regra de CORS, que está em XML. Veja um exemplo de uma regra de CORS que foi definida usando a operação Definir Propriedades de Serviço para o Serviço Blob de uma conta de armazenamento. Você pode executar essa operação usando a biblioteca de cliente de armazenamento ou as APIs REST do Armazenamento do Microsoft Azure.

```xml
<Cors>    
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
        <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
        <MaxAgeInSeconds>200</MaxAgeInSeconds>
    </CorsRule>
<Cors>
```

Veja o que cada linha significa:

* **AllowedOrigins** Informa quais domínios não correspondentes podem solicitar e receber dados do serviço de armazenamento. Isso significa que contoso.com e fabrikam.com podem solicitar dados do Armazenamento de Blobs para uma conta de armazenamento específica. Você também pode defini-lo para um caractere curinga (\*) a fim de permitir que todos os domínios acessem solicitações.
* **AllowedMethods** Essa é a lista de métodos (verbos de solicitação HTTP) que podem ser usados ao fazer a solicitação. Neste exemplo, apenas PUT e GET são permitidos. Você pode defini-lo para um caractere curinga (\*) a fim de permitir que todos os métodos sejam usados.
* **AllowedHeaders** Esses são os cabeçalhos de solicitação que o domínio de origem pode especificar ao fazer a solicitação. No exemplo acima, todos os cabeçalhos de metadados, começando com x-ms-meta-data, x-ms-meta-target e x-ms-meta-abc, são permitidos. O caractere curinga (\*) indica que todos os cabeçalhos que começam com o prefixo especificado são permitidos.
* **ExposedHeaders** Informa que os cabeçalhos de resposta devem ser expostos pelo navegador ao emissor da solicitação. Neste exemplo, qualquer cabeçalho que começar com "x-ms-meta-" será exposto.
* **MaxAgeInSeconds** Essa é a quantidade máxima de tempo que um navegador armazenará em cache a solicitação OPTIONS de simulação. (Para obter mais informações sobre a solicitação de simulação, verifique o primeiro artigo abaixo).

#### <a name="resources"></a>Recursos
Para obter mais informações sobre o CORS e como habilitá-lo, verifique estes recursos.

* [Suporte para o compartilhamento de recursos entre origens (CORS) para os serviços de armazenamento do Azure](../storage-cors-support.md)

  Este artigo fornece uma visão geral do CORS e como definir as regras para os diferentes serviços de armazenamento.
* [Cross-Origin Resource Sharing (CORS) Support for the Azure Storage Services on MSDN (Suporte ao CORS [Compartilhamento de Recursos entre Origens] para os Serviços de Armazenamento do Azure no MSDN)](https://msdn.microsoft.com/library/azure/dn535601.aspx)

  Esta é a documentação de referência do suporte ao CORS para os Serviços de Armazenamento do Microsoft Azure. Ela tem links para artigos que se aplicam a cada serviço de armazenamento, além de mostrar um exemplo e explicar cada elemento no arquivo CORS.
* [Armazenamento do Microsoft Azure: Introdução ao CORS](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/02/03/windows-azure-storage-introducing-cors.aspx)

  Esse é um link para o artigo do blog inicial que anuncia o CORS e mostra como usá-lo.

## <a name="frequently-asked-questions-about-azure-storage-security"></a>Perguntas frequentes sobre a segurança do Armazenamento do Azure
1. **Como poderei verificar a integridade dos blobs que estou transferindo para dentro ou para fora do Armazenamento do Azure se eu não puder usar o protocolo HTTPS?**

   Se, por algum motivo, você precisar usar HTTP em vez de HTTPS e estiver trabalhando com blobs de bloco, você poderá usar a verificação MD5 para ajudar a averiguar a integridade dos blobs que estão sendo transferidos. Isso ajudará na proteção contra erros na camada de rede/transporte, mas não necessariamente contra ataques de intermediários.

   Se você puder usar HTTPS, que fornece segurança em nível de transporte, o uso da verificação MD5 será redundante e desnecessário.

   Para saber mais, confira [Azure Blob MD5 Overview](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/18/windows-azure-blob-md5-overview.aspx)(Visão geral do MD5 do Blob do Azure).
2. **E quanto à conformidade com o padrão FIPS do governo dos EUA norte-americano?**

   O FIPS (Federal Information Processing Standard) dos Estados Unidos da América define algoritmos criptográficos aprovados para uso pelos sistemas de computadores do governo federal dos EUA para proteção de dados confidenciais. Habilitar o modo FIPS em um servidor ou desktop Windows informa ao sistema operacional que apenas algoritmos criptográficos validados pelo FIPS devem ser usados. Se um aplicativo usar algoritmos fora de conformidade, ele será interrompido. Com as versões 4.5.2 ou superiores do .NET Framework, o aplicativo alterna automaticamente os algoritmos da criptografia para usar algoritmos em conformidade com o FIPS quando o computador estiver no modo FIPS.

   A Microsoft deixa que cada cliente decida se habilita ou não o modo FIPS. Acreditamos que não há nenhum motivo convincente para os clientes que não estão sujeitos a normas governamentais habilitarem o modo FIPS por padrão.

### <a name="resources"></a>Recursos
* [Por que não estamos recomendando mais o "Modo FIPS"](https://blogs.technet.microsoft.com/secguide/2014/04/07/why-were-not-recommending-fips-mode-anymore/)

  Esse artigo de blog fornece uma visão geral do FIPS e explica por que o modo FIPS não é habilitado por padrão.
* [FIPS 140 Validation (Validação do FIPS 140)](https://technet.microsoft.com/library/cc750357.aspx)

  Esse artigo fornece informações sobre como os produtos da Microsoft e os módulos criptográficos cumprem o padrão FIPS para o governo federal dos EUA.
* [“Criptografia do sistema: Usar algoritmos em conformidade com o FIPS para criptografia, hash e assinatura", efeitos das configurações de segurança no Windows XP e em versões posteriores do Windows](https://support.microsoft.com/kb/811833)

  Esse artigo fala sobre o uso do modo FIPS em computadores Windows antigos.
