---
title: O que é o Cofre da Chave do Azure? | Microsoft Docs
description: Saiba como o Azure Key Vault protege chaves criptográficas e segredos que aplicativos e serviços de nuvem usam.
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: barclayn
ms.openlocfilehash: 48ac0c3efe74723099e87a77871aa1a78834efbd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60640526"
---
# <a name="what-is-azure-key-vault"></a>O que é o Cofre da Chave do Azure?

Serviços e aplicativos de nuvem usam chaves de criptografia e seguro de segredos para ajudar a manter as informações. Azure Key Vault protege essas chaves e segredos. Quando você usa o Cofre de chaves, você pode criptografar chaves de autenticação, chaves de conta de armazenamento, chaves de criptografia de dados, arquivos. pfx e senhas usando chaves que são protegidas por módulos de segurança de hardware (HSMs).

Key Vault ajuda a resolver os problemas a seguir:

- **Gerenciamento de segredos**: Armazene com segurança e controlar o acesso à tokens, senhas, certificados, chaves de API e outros segredos.
- **Gerenciamento de chaves**: Criar e controlar as chaves de criptografia para criptografam seus dados. 
- **Gerenciamento de certificado**: Provisionar, gerenciar e implantar certificados de Secure Sockets Layer/Transport Layer Security (SSL/TLS) públicos e privados para uso com o Azure e seus recursos internos de conectada. 
- **Store segredos apoiados por HSMs**: Use o software ou certificação FIPS 140-2 nível 2 HSMs para ajudar a proteger segredos e chaves.

## <a name="basic-concepts"></a>Conceitos básicos

O Azure Key Vault é uma ferramenta para armazenar e acessar segredos de forma segura. Um segredo é tudo o que você deseja controlar rigorosamente o acesso, como certificados, senhas ou chaves de API. Um cofre é um grupo lógico de segredos.

Aqui estão outros termos importantes:

- **Tenant**: um locatário é a organização que possui e gerencia uma instância específica de serviços em nuvem da Microsoft. Ele geralmente é usado para referir-se ao conjunto de serviços do Azure e do Office 365 para uma organização.

- **Proprietário do cofre**: pode criar um cofre de chaves e obter acesso e controle totais sobre ele. O proprietário do cofre também pode configurar a auditoria para registrar quem acessa os segredos e as chaves. Os administradores podem controlar o ciclo de vida da chave. Eles podem reverter para uma nova versão da chave, fazer o backup e tarefas relacionadas.

- **Consumidor do cofre**: pode executar ações nos ativos dentro do cofre de chaves quando seu proprietário concede acesso ao cliente. As ações disponíveis dependem das permissões concedidas.

- **Recurso**: Trata-se de um item gerenciável que está disponível por meio do Azure. Exemplos comuns são a máquina virtual, conta de armazenamento, aplicativo web, banco de dados e rede virtual. Há muito mais.

- **Grupo de recursos**: Um grupo de recursos é um contêiner que mantém os recursos relacionados a uma solução do Azure. O grupo de recursos pode incluir todos os recursos para a solução ou apenas os recursos que você deseja gerenciar como um grupo. Você decide como deseja alocar recursos para grupos de recursos com base no que faz mais sentido para sua organização.

- **Entidade de serviço**: Uma entidade de serviço do Azure é uma identidade de segurança criados pelo usuário de aplicativos, serviços e ferramentas de automação usam para acessar recursos específicos do Azure. Pense nela como uma "identidade de usuário" (nome de usuário e senha ou certificado) com uma função específica e permissões rigidamente controladas. Uma entidade de serviço só precisa fazer coisas específicas, ao contrário de uma identidade de usuário geral. Ele melhora a segurança se você conceder apenas o nível mínimo de permissão que ele precisa para realizar suas tarefas de gerenciamento.

- [Azure AD (Azure Active Directory)](../active-directory/active-directory-whatis.md): o Azure AD é o serviço do Active Directory de um locatário. Cada diretório tem um ou mais domínios. Um diretório pode ter várias assinaturas associadas a ele, mas apenas um locatário.

- **ID de Locatário do Azure**: uma ID de locatário é uma maneira exclusiva para identificar uma instância do Azure AD dentro de uma assinatura do Azure.

- **Identidades gerenciadas**: O Azure Key Vault fornece uma maneira de armazenar com segurança as credenciais e outras chaves e segredos, mas seu código precisa autenticar para o Key Vault para recuperá-los. Usar a identidade gerenciada torna a solução desse problema mais simples, fornecendo aos serviços do Azure uma identidade gerenciada automaticamente no Microsoft Azure Active Directory. Você pode usar essa identidade para autenticar o Key Vault ou qualquer serviço que dê suporte à autenticação do Azure AD sem ter as credenciais no código. Para obter mais informações, consulte a imagem a seguir e o [visão geral das identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).

    ![Diagrama de identidades como gerenciados para o trabalho de recursos do Azure](./media/key-vault-whatis/msi.png)

## <a name="authentication"></a>Authentication
Para fazer todas as operações com o Key Vault, você primeiro precisará autenticar-se a ele. Há três maneiras de se autenticar no Key Vault:

- [Identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview): Quando você implanta um aplicativo em uma máquina virtual no Azure, você pode atribuir uma identidade para sua máquina virtual que tem acesso ao Cofre de chaves. Você também pode atribuir identidades [outros recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). O benefício dessa abordagem é que o aplicativo ou serviço não estiver gerenciando a rotação do segredo primeiro. Azure gira automaticamente a identidade. Recomendamos essa abordagem como uma prática recomendada. 
- **Entidade de segurança e certificado de serviço**: Você pode usar uma entidade de serviço e um certificado associado que tem acesso ao Cofre de chaves. Não recomendamos essa abordagem porque o proprietário do aplicativo ou o desenvolvedor deve girar o certificado.
- **Entidade de serviço e o segredo**: Embora você possa usar uma entidade de serviço e um segredo para autenticar para o Key Vault, não é recomendável. É difícil Girar automaticamente o bootstrap segredo que é usado para autenticar para o Cofre de chaves.


## <a name="key-vault-roles"></a>Funções do Key Vault

Use a tabela a seguir para entender melhor como o Cofre da Chave pode ajudar a atender às necessidades de desenvolvedores e administradores de segurança.

| Função | Problema declarado | Solucionado pelo Cofre da Chave do Azure |
| --- | --- | --- |
| Desenvolvedor de um aplicativo do Azure |"Eu quero escrever um aplicativo do Azure que usa chaves de assinatura e criptografia. Mas quero que as chaves sejam externas ao aplicativo para que a solução seja adequada para um aplicativo distribuído geograficamente. <br/><br/>Quero que essas chaves e segredos sejam protegidos, sem precisar escrever o código sozinho. E também que essas chaves e segredos sejam fáceis de usar em meu aplicativo, com desempenho ideal.” |√ As chaves são armazenadas em um cofre e invocadas pelo URI quando necessário.<br/><br/> √ As chaves são protegidas pelo Azure, usando módulos de segurança de hardware, comprimentos da chave e algoritmos padrão da indústria.<br/><br/>  √ As chaves são processadas em HSMs que residem nos mesmos datacenters do Azure que os aplicativos. Esse método permite uma maior confiabilidade e uma latência reduzida em comparação às chaves que residem em um local separado, como no local. |
| Desenvolvedor de SaaS (software como serviço) |"Eu não quero a responsabilidade ou ser o possível culpado por problemas com as chaves e segredos de locatário dos meus clientes. <br/><br/>Quero que os clientes tenham e gerenciem suas chaves para que eu possa me concentrar em fazer o que faço melhor, que é fornecer os recursos centrais do software." |√ Os clientes podem importar suas próprias chaves para o Azure e gerenciá-las. Quando um aplicativo de SaaS precisa executar operações criptográficas usando as chaves dos clientes, o Cofre de chaves faz essas operações em nome do aplicativo. O aplicativo não vê as chaves dos clientes. |
| Diretor-chefe de segurança (CSO) |"Quero saber que nossos aplicativos estão em conformidade com HSMs FIPS 140-2 Nível 2, para um gerenciamento de chaves seguro. <br/><br/>Quero garantir que minha organização controle o ciclo de vida da chave e possa monitorar seu uso. <br/><br/>E embora usemos vários recursos e serviços do Azure, quero gerenciar as chaves de um único local no Azure. |√ Os HSMs têm certificação FIPS 140-2 Nível 2.<br/><br/>√ O Cofre de Chaves foi projetado para que a Microsoft não veja nem extraia suas chaves.<br/><br/>√ Uso de chave é registrado praticamente em tempo real.<br/><br/>√ O cofre fornece uma única interface, independentemente de quantos cofres você tenha no Azure, quais sejam as regiões com suporte e quais aplicativos as usem. |

Qualquer pessoa com uma assinatura do Azure pode criar e usar cofres de chaves. Embora o Cofre de chaves beneficie os desenvolvedores e administradores de segurança, pode ser implementado e gerenciado pelo administrador de uma organização que gerencia outros serviços do Azure. Por exemplo, esse administrador poderá entrar com uma assinatura do Azure, crie um cofre para a organização na qual armazenar as chaves e, em seguida, ser responsável por tarefas operacionais como estes:

- Criar ou importar uma chave ou segredo
- Revogar ou excluir uma chave ou segredo
- Autorizar usuários ou aplicativos a acessar o cofre da chave para que eles possam gerenciar ou usar suas chaves e segredos
- Configurar o uso de chaves (por exemplo, assinar ou criptografar)
- Monitorar o uso de chaves

Esse administrador, em seguida, fornece aos desenvolvedores URIs a serem chamados de seus aplicativos. Esse administrador também fornece informações de log de uso de chave para o administrador de segurança. 

![Visão geral do funcionamento do Azure Key Vault][1]

Os desenvolvedores também podem gerenciar as chaves diretamente, por meio de APIs. Para saber mais, confira o [guia do desenvolvedor do Cofre da Chave](key-vault-developers-guide.md).

## <a name="next-steps"></a>Próximas etapas

Saiba como [proteger seu cofre](key-vault-secure-your-key-vault.md).

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
O Cofre da Chave do Azure está disponível na maioria das regiões. Para obter mais informações, consulte a [Página de preços do Cofre da Chave](https://azure.microsoft.com/pricing/details/key-vault/).
