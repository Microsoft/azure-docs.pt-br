---
title: Visão geral do Azure Key Vault – Azure Key Vault
description: O Azure Key Vault é um armazenamento de segredos seguro, fornecendo gerenciamento de segredos, chaves e certificados, todos com suporte de módulos de segurança de hardware.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: overview
ms.custom: mvc
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: 3e6ed6bc8ba426a981b7a2390e7d00d9c3b1e785
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105643447"
---
# <a name="about-azure-key-vault"></a>Sobre o Azure Key Vault

O Azure Key Vault ajuda a resolver os problemas a seguir:

- **Gerenciamento de Segredos** – O Azure Key Vault pode ser usado para armazenar com segurança e controlar firmemente o acesso a tokens, senhas, certificados, chaves de API e outros segredos
- **Gerenciamento de Chaves** – O Azure Key Vault também pode ser usado como uma solução de gerenciamento de chaves. O Azure Key Vault torna fácil criar e controlar as chaves de criptografia usadas para criptografar seus dados. 
- **Gerenciamento de Certificado** – O Azure Key Vault também é um serviço que permite provisionar, gerenciar e implantar com facilidade certificados de protocolo TLS/SSL (Transport Layer Security/Secure Sockets Layer) públicos e privados para uso com o Azure e seus recursos internos conectados com facilidade.

O Azure Key Vault tem duas camadas de serviço: Standard, que faz a criptografia com uma chave de software, e uma camada Premium, que inclui chaves protegidas por HSM (módulo de segurança de hardware). Para ver uma comparação entre as camadas Standard e Premium, confira a [página de preços do Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="why-use-azure-key-vault"></a>Por que usar o Azure Key Vault?

### <a name="centralize-application-secrets"></a>Centralizar segredos do aplicativo

O armazenamento centralizado de segredos do aplicativo no Azure Key Vault permite que você controle sua distribuição. O Key Vault reduz consideravelmente a probabilidade de os segredos serem vazados acidentalmente. Ao usar o Key Vault, os desenvolvedores de aplicativos não precisam mais armazenar informações de segurança no aplicativo. A falta de necessidade de armazenar informações de segurança em aplicativos elimina a necessidade de inserir essas informações como parte do código. Por exemplo, um aplicativo pode precisar se conectar a um banco de dados. Em vez de armazenar a cadeia de conexão no código do aplicativo, armazene-o com segurança no Key Vault.

Os aplicativos podem acessar com segurança as informações necessárias usando URIs. Esses URIs permitem que os aplicativos recuperem versões específicas de um segredo. Não há necessidade de escrever um código personalizado para proteger as informações de segredo armazenadas no Key Vault.

### <a name="securely-store-secrets-and-keys"></a>Armazene segredos e chaves com segurança

O acesso a um cofre de chaves requer a devida autenticação e autorização antes de um chamador (usuário ou aplicativo) poder obter acesso. A autenticação estabelece a identidade do chamador e a autorização determina as operações que ele tem permissão para executar.

A autenticação é feita pelo Azure Active Directory. A autorização pode ser feita por meio do RBAC (controle de acesso baseado em função) do Azure ou da política de acesso do Key Vault. O RBAC do Azure é usado ao lidar com o gerenciamento dos cofres e a política de acesso do cofre de chaves é usada ao tentar acessar dados armazenados em um cofre.

Os Azure Key Vaults podem ser protegidos por software ou, com a camada do Azure Key Vault Premium, protegidos por hardware por HSMs (módulos de segurança de hardware). As chaves, os segredos e os certificados protegidos por software são protegidos pelo Azure por meio de algoritmos padrão do setor e comprimentos de chave.  Para situações que exijam garantia extra, você pode importar ou gerar chaves em HSMs que nunca deixam os limites do HSM. O Azure Key Vault usa HSMs nCipher, que são validados pelo FIPS 140-2 Nível 2. Você pode usar as ferramentas nCipher para mover uma chave de seu HSM para o Azure Key Vault.

Por fim, o Azure Key Vault foi projetado para que a Microsoft não veja nem extraia seus dados.

### <a name="monitor-access-and-use"></a>Monitorar o acesso e o uso

Depois de criar alguns Key Vaults, é bom monitorar como e quando suas chaves e segredos são acessados. Monitore a atividade habilitando o log para os cofres. Você pode configurar o Azure Key Vault para:

- Arquive em uma conta de armazenamento.
- Transmita para um hub de eventos.
- Enviar logs para os logs do Azure Monitor.

Você tem controle sobre os logs e pode protegê-los restringindo o acesso, e também pode excluir logs que não são mais necessários.

### <a name="simplified-administration-of-application-secrets"></a>Administração simplificada de segredos do aplicativo

Ao armazenar dados valiosos, é necessário executar várias etapas. As informações de segurança precisam ser protegidas, seguir um ciclo de vida e estar altamente disponíveis. O Azure Key Vault simplifica o processo de atender a esses requisitos por meio de:

- Remoção da necessidade de conhecimento interno sobre Módulos de Segurança de Hardware.
- Escalonamento vertical rápido para atender aos picos de uso da sua organização.
- Replicando o conteúdo de seu Key Vault dentro de uma região e para uma região secundária. A replicação de dados garante a alta disponibilidade e elimina a necessidade de qualquer ação por parte do administrador para disparar o failover.
- Fornecendo opções de administração do Azure padrão por meio do portal, da CLI do Azure e do PowerShell.
- Automatizando algumas tarefas em certificados que você compra de autoridades de certificação pública, como registro e renovação.

Além disso, os Azure Key Vaults permitem que você separe os segredos do aplicativo. Os aplicativos podem acessar apenas o cofre que eles têm permissão para acessar e podem estar limitados a executar operações específicas. Você pode criar um Azure Key Vault por aplicativo e restringir os segredos armazenados em um Key Vault para um aplicativo e uma equipe de desenvolvedores específicos.

### <a name="integrate-with-other-azure-services"></a>Integração com outros serviços do Azure

Como um repositório seguro no Azure, o Key Vault tem sido usado para simplificar cenários como:
-  [Criptografia de Disco do Azure](../../security/fundamentals/encryption-overview.md)
-  As funcionalidades [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) e [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) no SQL Server e no Banco de Dados SQL do Azure
- [Serviço de Aplicativo do Azure](/azure/app-service/configure-ssl-certificate).

O próprio Key Vault pode se integrar às contas de armazenamento, aos hubs de eventos e ao Log Analytics.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [chaves, segredos e certificados](about-keys-secrets-certificates.md)
- [Início Rápido: Criar um Azure Key Vault usando a CLI](../secrets/quick-create-cli.md)
- [Autenticação, solicitações e respostas](../general/authentication-requests-and-responses.md)
- [Guia do Desenvolvedor do Cofre de Chaves](../general/developers-guide.md)
