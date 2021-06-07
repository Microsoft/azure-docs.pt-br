---
title: Conceitos de gerenciamento para Azure AD Domain Services | Microsoft Docs
description: Saiba mais sobre como administrar um Azure Active Directory Domain Services domínio gerenciado e o comportamento de contas de usuário e senhas
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: justinha
ms.openlocfilehash: 5473ef46751d64fdbbf1d52f39c66f49d707e615
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102631380"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Conceitos de gerenciamento para contas de usuário, senhas e administração no Azure Active Directory Domain Services

Quando você cria e executa um domínio gerenciado Azure Active Directory Domain Services (AD DS), há algumas diferenças no comportamento em comparação com um ambiente de AD DS local tradicional. Você usa as mesmas ferramentas administrativas do Azure AD DS como um domínio autogerenciado, mas não pode acessar diretamente os controladores de domínio (DC). Há também algumas diferenças no comportamento de políticas de senha e hashes de senha, dependendo da origem da criação da conta de usuário.

Este artigo conceitual fornece detalhes sobre como administrar um domínio gerenciado e o comportamento diferente de contas de usuário, dependendo da maneira como eles são criados.

## <a name="domain-management"></a>Gerenciamento de domínios

Um domínio gerenciado é um namespace DNS e um diretório correspondente. Em um domínio gerenciado, os controladores de domínio (DCs) que contêm todos os recursos, como usuários e grupos, credenciais e políticas, fazem parte do serviço gerenciado. Para redundância, dois DCs são criados como parte de um domínio gerenciado. Você não pode entrar nesses DCs para executar tarefas de gerenciamento. Em vez disso, você cria uma VM de gerenciamento que ingressou no domínio gerenciado e, em seguida, instala suas ferramentas de gerenciamento de AD DS regulares. Você pode usar o Centro Administrativo do Active Directory ou snap-ins do MMC (console de gerenciamento Microsoft), como o DNS ou objetos Política de Grupo, por exemplo.

## <a name="user-account-creation"></a>Criação de conta de usuário

As contas de usuário podem ser criadas em um domínio gerenciado de várias maneiras. A maioria das contas de usuário é sincronizada no Azure AD, que também pode incluir a conta de usuário sincronizada de um ambiente de AD DS local. Você também pode criar manualmente as contas diretamente no domínio gerenciado. Alguns recursos, como a sincronização de senha inicial ou a política de senha, se comportam de forma diferente dependendo de como e onde as contas de usuário são criadas.

* A conta de usuário pode ser sincronizada no Azure AD. Isso inclui contas de usuário somente em nuvem criadas diretamente no Azure AD e contas de usuário híbridos sincronizadas de um ambiente de AD DS local usando Azure AD Connect.
    * A maioria das contas de usuário em um domínio gerenciado é criada por meio do processo de sincronização do Azure AD.
* A conta de usuário pode ser criada manualmente em um domínio gerenciado e não existe no Azure AD.
    * Se você precisar criar contas de serviço para aplicativos que só são executados no domínio gerenciado, você pode criá-los manualmente no domínio gerenciado. Como a sincronização é unidirecional do Azure AD, as contas de usuário criadas no domínio gerenciado não são sincronizadas de volta para o Azure AD.

## <a name="password-policy"></a>Política de senha

O AD DS do Azure inclui uma política de senha padrão que define as configurações de itens como o bloqueio de conta, a duração máxima da senha e a complexidade da senha. Configurações como política de bloqueio de conta se aplicam a todos os usuários em um domínio gerenciado, independentemente de como o usuário foi criado, conforme descrito na seção anterior. Algumas configurações, como o comprimento mínimo da senha e a complexidade da senha, só se aplicam a usuários criados diretamente em um domínio gerenciado.

Você pode criar suas próprias políticas de senha personalizadas para substituir a política padrão em um domínio gerenciado. Essas políticas personalizadas podem ser aplicadas a grupos de usuários específicos, conforme necessário.

Para obter mais informações sobre as diferenças em como as políticas de senha são aplicadas dependendo da origem da criação do usuário, consulte [políticas de bloqueio de senha e conta em domínios gerenciados][password-policy].

## <a name="password-hashes"></a>Hashes de senha

Para autenticar os usuários no domínio gerenciado, o Azure AD DS precisa de hashes de senha em um formato adequado para a autenticação NTLM (Gerenciador de LAN NT) e Kerberos. O Azure AD não gera nem armazena hashes de senha no formato necessário para a autenticação NTLM ou Kerberos, até que você habilite o Azure AD DS para seu locatário. Por motivos de segurança, o Azure AD também não armazena credenciais de senha no formato de texto não criptografado. Portanto, o Azure AD não pode gerar automaticamente essas hashes de senha NTLM ou Kerberos com base nas credenciais existentes dos usuários.

Para contas de usuário somente em nuvem, os usuários devem alterar suas senhas antes de poderem usar o domínio gerenciado. Esse processo de alteração de senhas faz com que as hashes de senha para a autenticação Kerberos e NTLM sejam geradas e armazenadas no Azure AD. A conta não é sincronizada do Azure AD para o Azure AD DS até que a senha seja alterada.

Para usuários sincronizados de um ambiente de AD DS local usando Azure AD Connect, [habilite a sincronização de hashes de senha][hybrid-phs].

> [!IMPORTANT]
> Azure AD Connect sincroniza somente os hashes de senha herdados quando você habilita o Azure AD DS para seu locatário do Azure AD. Os hashes de senha herdados não serão usados se você usar apenas Azure AD Connect para sincronizar um ambiente de AD DS local com o Azure AD.
>
> Se seus aplicativos herdados não usam autenticação NTLM ou associações simples LDAP, recomendamos que você desabilite a sincronização de hash de senha NTLM para o Azure AD DS. Para obter mais informações, consulte [desabilitar pacotes de criptografia fracos e sincronização de hash de credencial NTLM][secure-domain].

Uma vez configurado adequadamente, as hashes de senha utilizáveis são armazenadas no domínio gerenciado. Se você excluir o domínio gerenciado, todas as hashes de senha armazenadas nesse ponto também serão excluídas. As informações de credenciais sincronizadas no Azure AD não podem ser reutilizadas se você criar mais tarde outro domínio gerenciado-você deve reconfigurar a sincronização de hash de senha para armazenar os hashes de senha novamente. As VMs ou os usuários previamente unidos ao domínio não poderão autenticar imediatamente – o Azure AD precisa gerar e armazenar as hashes de senha no novo domínio gerenciado. Para obter mais informações, confira [Processo de sincronização de hash de senha para o Azure AD DS e o Azure AD Connect][azure-ad-password-sync].

> [!IMPORTANT]
> O Azure AD Connect só deve ser instalado e configurado para sincronização com ambientes do AD DS locais. A instalação do Azure AD Connect em um domínio gerenciado a fim de sincronizar objetos com o Azure AD não é um procedimento compatível.

## <a name="forests-and-trusts"></a>Florestas e relações de confiança

Uma *floresta* é um constructo lógico usado pelo AD DS (Active Directory Domain Services) para agrupar um ou mais *domínios*. Em seguida, os domínios armazenam objetos para usuários ou grupos e fornecem serviços de autenticação.

No Azure AD DS, a floresta contém apenas um domínio. As florestas do AD DS locais geralmente contêm muitos domínios. Em grandes organizações, especialmente depois de fusões e aquisições, você poderá terminar com várias florestas locais que contêm vários domínios.

Por padrão, um domínio gerenciado é criado como uma floresta de *usuário* . Esse tipo de floresta sincroniza todos os objetos do Azure AD, incluindo qualquer conta de usuário criada em um ambiente do AD DS local. As contas de usuário podem se autenticar diretamente no domínio gerenciado, como para entrar em uma VM ingressada no domínio. Uma floresta de usuário funciona quando os hashes de senha podem ser sincronizados e os usuários não estão usando métodos de conexão exclusivos, como a autenticação de cartão inteligente.

Em uma floresta de *recursos* do Azure AD DS, os usuários se autenticam em uma *relação de confiança* da floresta unidirecional nos AD DS locais. Com essa abordagem, os objetos de usuário e os hashes de senha não são sincronizados com o Azure AD DS. Os objetos de usuário e as credenciais existem somente no AD DS local. Essa abordagem permite que as empresas hospedem recursos e plataformas de aplicativo no Azure que dependem da autenticação clássica, tais como LDAPS, Kerberos ou NTLM, mas todas as preocupações ou problemas de autenticação são removidos.

Para obter mais informações sobre tipos de floresta no Azure AD DS, consulte [o que são florestas de recursos?][concepts-forest] e [como as relações de confiança de floresta funcionam no AD DS do Azure?][concepts-trust]

## <a name="azure-ad-ds-skus"></a>SKUs de AD DS do Azure

No Azure AD DS, o desempenho e os recursos disponíveis são baseados na SKU. Você seleciona uma SKU ao criar o domínio gerenciado e pode alternar SKUs conforme os requisitos de negócios mudam após o domínio gerenciado ter sido implantado. A tabela a seguir descreve as SKUs disponíveis e as diferenças entre elas:

| Nome do SKU   | Contagem máxima de objetos | Frequência de backup | Número máximo de relações de confiança de floresta de saída |
|------------|----------------------|------------------|----|
| Standard   | Ilimitado            | A cada 5 dias     | 0  |
| Enterprise | Ilimitado            | A cada 3 dias     | 5  |
| Premium    | Ilimitado            | Diariamente            | 10 |

Antes dessas SKUs do Azure AD DS, um modelo de cobrança baseado no número de objetos (contas de usuário e de computador) no domínio gerenciado foi usado. Não há mais preços variáveis com base no número de objetos no domínio gerenciado.

Para obter mais informações, consulte a [página de preços do Azure AD DS][pricing].

### <a name="managed-domain-performance"></a>Desempenho de domínio gerenciado

O desempenho do domínio varia de acordo com a forma como a autenticação é implementada para um aplicativo. Recursos de computação adicionais podem ajudar a melhorar o tempo de resposta da consulta e reduzir o tempo gasto em operações de sincronização. À medida que o nível de SKU aumenta, os recursos de computação disponíveis para o domínio gerenciado são aumentados. Monitore o desempenho de seus aplicativos e planeje os recursos necessários.

Se suas demandas de negócios ou aplicativos mudarem e você precisar de poder de computação adicional para seu domínio gerenciado, você poderá alternar para um SKU diferente.

### <a name="backup-frequency"></a>Frequência de backup

A frequência de backup determina com que frequência um instantâneo do domínio gerenciado é obtido. Os backups são um processo automatizado gerenciado pela plataforma Azure. No caso de um problema com seu domínio gerenciado, o suporte do Azure pode ajudá-lo na restauração a partir do backup. Como a sincronização ocorre apenas de uma maneira *do* Azure AD, quaisquer problemas em um domínio gerenciado não afetarão os ambientes e a funcionalidade do Azure ad ou local AD DS.

À medida que o nível de SKU aumenta, a frequência desses instantâneos de backup aumenta. Examine os requisitos de negócios e o objetivo de ponto de recuperação (RPO) para determinar a frequência de backup necessária para seu domínio gerenciado. Se seus requisitos de negócios ou aplicativos forem alterados e você precisar de backups mais frequentes, você poderá alternar para um SKU diferente.

### <a name="outbound-forest-trusts"></a>Relações de confiança de floresta de saída

A seção anterior detalha a floresta de saída unidirecional de um domínio gerenciado para um ambiente de AD DS local. A SKU determina o número máximo de relações de confiança de floresta que você pode criar para um domínio gerenciado. Examine os requisitos de negócios e aplicativos para determinar quantas relações de confiança você realmente precisa e escolha o SKU de AD DS do Azure apropriado. Novamente, se os requisitos de negócios mudarem e você precisar criar relações de confiança de floresta adicionais, poderá alternar para um SKU diferente.

## <a name="next-steps"></a>Próximas etapas

Para começar, [crie um domínio gerenciado do Azure AD DS][create-instance].

<!-- INTERNAL LINKS -->
[password-policy]: password-policy.md
[hybrid-phs]: tutorial-configure-password-hash-sync.md#enable-synchronization-of-password-hashes
[secure-domain]: secure-your-domain.md
[azure-ad-password-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[create-instance]: tutorial-create-instance.md
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
