---
title: O que é o Azure Key Vault? | Microsoft Docs
description: Saiba como o Azure Key Vault protege chaves criptográficas e segredos usados por aplicativos e serviços de nuvem.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: mbaldwin
ms.openlocfilehash: cc00a4f1c1551932b4a30a8ef9b27cb1d4082667
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99071589"
---
# <a name="azure-key-vault-basic-concepts"></a>Conceitos básicos do Azure Key Vault

Azure Key Vault é um serviço de nuvem para armazenar e acessar segredos com segurança. Um segredo é qualquer coisa que você deseja controlar rigidamente o acesso ao, como chaves de API, senhas, certificados ou chaves de criptografia. Key Vault serviço dá suporte a dois tipos de contêineres: cofres e pools HSM gerenciados. Os cofres dão suporte ao armazenamento de chaves, segredos e certificados com respaldo de software e HSM. Os pools HSM gerenciados dão suporte apenas a chaves apoiadas por HSM. Confira [Azure Key Vault visão geral da API REST](about-keys-secrets-certificates.md) para obter detalhes completos.

Aqui estão outros termos importantes:

- **Locatário**: um locatário é a organização que possui e gerencia uma instância específica de serviços em nuvem da Microsoft. Geralmente, é usado para se referir ao conjunto de serviços do Azure e Microsoft 365 para uma organização.

- **Proprietário do cofre**: pode criar um cofre de chaves e obter acesso e controle totais sobre ele. O proprietário do cofre também pode configurar a auditoria para registrar quem acessa os segredos e as chaves. Os administradores podem controlar o ciclo de vida da chave. Eles podem reverter para uma nova versão da chave, fazer o backup e tarefas relacionadas.

- **Consumidor do cofre**: pode executar ações nos ativos dentro do cofre de chaves quando seu proprietário concede acesso ao cliente. As ações disponíveis dependem das permissões concedidas.

- **Administradores HSM gerenciados**: os usuários que recebem a função de administrador têm controle total sobre um pool de HSM gerenciado. Eles podem criar mais atribuições de função para delegar o acesso controlado a outros usuários.

- **Usuário/funcionário de criptografia HSM gerenciado**: funções internas que geralmente são atribuídas a usuários ou entidades de serviço que executarão operações criptográficas usando chaves no HSM gerenciado. O usuário de criptografia pode criar novas chaves, mas não pode excluir chaves.

- **Criptografia do serviço de criptografia HSM gerenciada**: função interna que geralmente é atribuída a uma identidade de serviço gerenciada de contas de serviço (por exemplo, conta de armazenamento) para criptografia de dados em repouso com a chave gerenciada pelo cliente.

- **Recurso**: trata-se de um item gerenciável que está disponível por meio do Azure. Exemplos comuns são máquina virtual, conta de armazenamento, aplicativo Web, banco de dados e rede virtual. Há muito mais.

- **Grupo de recursos**: trata-se de um contêiner que mantém os recursos relacionados de uma solução do Azure. O grupo de recursos pode incluir todos os recursos para a solução ou apenas os recursos que você deseja gerenciar como um grupo. Você decide como deseja alocar recursos para grupos de recursos com base no que faz mais sentido para sua organização.

- **Entidade** de segurança: uma entidade de segurança do Azure é uma identidade de segurança que os aplicativos, serviços e ferramentas de automação criados pelo usuário usam para acessar recursos específicos do Azure. Imagine-o como uma "identidade do usuário" (nome de usuários e senha ou certificado) com uma função específica e permissões rigidamente controladas. Uma entidade de segurança deve precisar apenas fazer coisas específicas, diferentemente de uma identidade de usuário geral. Ele melhora a segurança se você conceder apenas o nível mínimo de permissão necessário para executar suas tarefas de gerenciamento. Uma entidade de segurança usada com um aplicativo ou serviço é especificamente chamada de **entidade de serviço**.

- [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md): o Azure AD é o serviço do Active Directory de um locatário. Cada diretório tem um ou mais domínios. Um diretório pode ter várias assinaturas associadas a ele, mas apenas um locatário.

- **ID do locatário do Azure**: uma ID de locatário é uma maneira exclusiva para identificar uma instância do Azure AD dentro de uma assinatura do Azure.

- **Identidades gerenciadas**: o Azure Key Vault fornece uma maneira de armazenar credenciais e outras chaves e segredos com segurança, mas seu código precisa autenticar-se no Key Vault para recuperá-los. Usar a identidade gerenciada torna a solução desse problema mais simples, fornecendo aos serviços do Azure uma identidade gerenciada automaticamente no Microsoft Azure Active Directory. Você pode usar essa identidade para autenticar o Key Vault ou qualquer serviço que dê suporte à autenticação do Azure AD sem ter as credenciais no código. Para obter mais informações, consulte a imagem a seguir e a [visão geral de identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="authentication"></a>Autenticação
Para realizar operações com Key Vault, primeiro você precisa se autenticar nele. Há três maneiras de se autenticar no Key Vault:

- [Identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md): ao implantar um aplicativo em uma máquina virtual no Azure, você pode atribuir uma identidade à sua máquina virtual que tem acesso ao key Vault. Você também pode atribuir identidades a [outros recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md). O benefício dessa abordagem é que o aplicativo ou serviço não está gerenciando a rotação do primeiro segredo. Azure gira automaticamente a identidade. Recomendamos essa abordagem como uma prática recomendada. 
- **Entidade de serviço e certificado**: você pode usar uma entidade de serviço e um certificado associado que tenha acesso a Key Vault. Não recomendamos essa abordagem porque o proprietário do aplicativo ou o desenvolvedor deve girar o certificado.
- **Entidade de serviço e segredo**: embora você possa usar uma entidade de serviço e um segredo para autenticar para Key Vault, não é recomendável. É difícil girar automaticamente o segredo de inicialização que é usado para autenticar para Key Vault.


## <a name="key-vault-roles"></a>Funções do Key Vault

Use a tabela a seguir para entender melhor como o Cofre da Chave pode ajudar a atender às necessidades de desenvolvedores e administradores de segurança.

| Função | Problema declarado | Solucionado pelo Cofre da Chave do Azure |
| --- | --- | --- |
| Desenvolvedor de um aplicativo do Azure |"Desejo escrever um aplicativo para o Azure que usa chaves para assinatura e criptografia. Mas quero que essas chaves sejam externas do meu aplicativo para que a solução seja adequada para um aplicativo distribuído geograficamente. <br/><br/>Quero que essas chaves e segredos sejam protegidos, sem precisar escrever o código sozinho. Também quero que essas chaves e segredos sejam fáceis de usar em meus aplicativos, com um desempenho ideal. " |√ As chaves são armazenadas em um cofre e invocadas pelo URI quando necessário.<br/><br/> √ As chaves são protegidas pelo Azure, usando módulos de segurança de hardware, comprimentos da chave e algoritmos padrão da indústria.<br/><br/>  √ As chaves são processadas em HSMs que residem nos mesmos datacenters do Azure que os aplicativos. Esse método permite uma maior confiabilidade e uma latência reduzida em comparação às chaves que residem em um local separado, como no local. |
| Desenvolvedor de SaaS (software como serviço) |"Não quero a responsabilidade ou a responsabilidade potencial dos segredos e das chaves de locatário dos meus clientes. <br/><br/>Quero que os clientes tenham e gerenciem suas chaves para que eu possa me concentrar em fazer o que eu faço melhor, que fornece os principais recursos de software. " |√ Os clientes podem importar suas próprias chaves para o Azure e gerenciá-las. Quando um aplicativo SaaS precisa executar operações criptográficas usando as chaves dos clientes, Key Vault realiza essas operações em nome do aplicativo. O aplicativo não vê as chaves dos clientes. |
| Diretor-chefe de segurança (CSO) |"Quero saber que nossos aplicativos estão em conformidade com os HSMs FIPS 140-2 nível 2 ou FIPS 140-2 nível 3 para o gerenciamento de chaves seguro. <br/><br/>Quero garantir que minha organização controle o ciclo de vida da chave e possa monitorar seu uso. <br/><br/>E, embora possamos usar vários serviços e recursos do Azure, quero gerenciar as chaves de um único local no Azure. " |√ Escolha **cofres** para HSMs validados pelo FIPS 140-2 nível 2.<br/>√ Escolher **pools HSM gerenciados** para HSMs validados pelo FIPS 140-2 nível 3.<br/><br/>√ O Cofre de Chaves foi projetado para que a Microsoft não veja nem extraia suas chaves.<br/>√ Uso de chave é registrado praticamente em tempo real.<br/><br/>√ O cofre fornece uma única interface, independentemente de quantos cofres você tenha no Azure, quais sejam as regiões com suporte e quais aplicativos as usem. |

Qualquer pessoa com uma assinatura do Azure pode criar e usar cofres de chaves. Embora Key Vault beneficia os desenvolvedores e administradores de segurança, eles podem ser implementados e gerenciados pelo administrador de uma organização que gerencia outros serviços do Azure. Por exemplo, esse administrador pode entrar com uma assinatura do Azure, criar um cofre para a organização na qual armazenar chaves e, em seguida, ser responsável por tarefas operacionais como estas:

- Criar ou importar uma chave ou segredo
- Revogar ou excluir uma chave ou segredo
- Autorizar usuários ou aplicativos a acessar o cofre da chave para que eles possam gerenciar ou usar suas chaves e segredos
- Configurar o uso de chaves (por exemplo, assinar ou criptografar)
- Monitorar o uso de chaves

Esse administrador então fornece aos desenvolvedores os URIs para chamar de seus aplicativos. Esse administrador também fornece informações de log de uso de chave para o administrador de segurança. 

![Visão geral do funcionamento do Azure Key Vault][1]

Os desenvolvedores também podem gerenciar as chaves diretamente, por meio de APIs. Para saber mais, confira o [guia do desenvolvedor do Cofre da Chave](developers-guide.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba como [proteger seu cofre](secure-your-key-vault.md).
- Saiba como [proteger seus pools HSM gerenciados](../managed-hsm/access-control.md)

<!--Image references-->
[1]: ../media/key-vault-whatis/AzureKeyVault_overview.png
O Cofre da Chave do Azure está disponível na maioria das regiões. Para obter mais informações, consulte a [Página de preços do Cofre da Chave](https://azure.microsoft.com/pricing/details/key-vault/).