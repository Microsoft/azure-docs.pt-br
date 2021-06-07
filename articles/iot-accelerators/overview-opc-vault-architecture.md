---
title: Arquitetura de Cofre OPC – Azure | Microsoft Docs
description: Arquitetura do serviço de gerenciamento de certificados do Cofre OPC
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: eb558d967ad657d14158684fba92b13979ea5fe2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646501"
---
# <a name="opc-vault-architecture"></a>Arquitetura do OPC Vault

> [!IMPORTANT]
> Enquanto atualizamos este artigo, confira [IoT Industrial do Azure](https://azure.github.io/Industrial-IoT/) para obter o conteúdo mais atualizado.

Este artigo fornece uma visão geral sobre o microsserviço do OPC Vault e o módulo de IoT Edge do OPC Vault.

Os aplicativos OPC UA usam certificados de instância de aplicativo para fornecer segurança de nível de aplicativo. Uma conexão segura é estabelecida usando a criptografia assimétrica, para a qual os certificados do aplicativo oferecem o par de chaves públicas e privadas. Os certificados podem ser autoassinados ou assinados por uma AC (Autoridade de Certificação).

Um aplicativo OPC UA tem uma lista de certificados confiáveis que representam os aplicativos nos quais ele confia. Esses certificados podem ser autoassinados, assinados por uma AC ou podem ser uma AC Raiz ou Sub-CA. Se um certificado confiável fizer parte de uma cadeia de certificados maior, o aplicativo confiará em todos os certificados que se encadeiam ao certificado na lista de confiança. Isso é verdadeiro, desde que a cadeia de certificados completa possa ser validada.

A principal diferença entre confiar em certificados autoassinados e confiar em um Certificado de Autoridade de Certificação é o esforço de instalação necessário para implantar e manter a confiança. Também há um esforço adicional para hospedar uma AC específica da empresa. 

Para distribuir a confiança para certificados autoassinados para vários servidores com um único aplicativo cliente, você deve instalar todos os certificados de aplicativo para servidores na lista de confiança do aplicativo cliente. Além disso, você deve instalar o certificado do aplicativo cliente em todas as listas de confiança do aplicativo para servidores. Esse esforço administrativo sobrecarrega bastante e aumenta quando os tempos de vida dos certificados precisam ser considerados e os certificados são renovados.

O uso de uma AC específica da empresa pode simplificar bastante o gerenciamento de confiança com vários servidores e clientes. Nesse caso, o administrador gera um certificado de instância de aplicativo assinado pela AC uma vez para cada cliente e servidor usado. Além disso, o Certificado de Autoridade de Certificação é instalado em cada lista de confiança de aplicativos, em todos os servidores e clientes. Com essa abordagem, apenas os certificados expirados precisam ser renovados e substituídos para os aplicativos afetados.

O serviço de gerenciamento de certificado do OPC UA da IoT industrial do Azure ajuda a gerenciar uma AC específica da empresa para aplicativos do OPC UA. Esse serviço é baseado no microsserviço do OPC Vault. O OPC Vault fornece um microsserviço para hospedar uma AC específica da empresa em uma nuvem segura. Essa solução é apoiada por serviços protegidos pelo Azure AD (Azure Active Directory), pelo Azure Key Vault com HSMs (Módulos de Segurança de Hardware), pelo Azure Cosmos DB e, opcionalmente, pelo Hub IoT como um repositório de aplicativos.

O microsserviço do OPC Vault foi criado para dar suporte ao fluxo de trabalho baseado em função, em que os administradores da segurança e aprovadores com direitos de assinatura no Azure Key Vault aprovam ou rejeitam essas solicitações.

Para compatibilidade com as soluções de OPC UA existentes, os serviços incluem suporte para um módulo de borda com backup em microsserviço do OPC Vault. Isso implementa a interface do **Gerenciamento de Certificado e Servidor de Descoberta Global de OPC UA**, para distribuir certificados e listas de confiança de acordo com a parte 12 da especificação. 


## <a name="architecture"></a>Arquitetura

A arquitetura é baseada no microsserviço do OPC Vault com um módulo do IoT Edge do OPC Vault para a rede de fábrica e uma experiência de usuário de exemplo da Web para controlar o fluxo de trabalho:

![Diagrama da arquitetura do OPC Vault](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="opc-vault-microservice"></a>Microsserviço do Cofre OPC

O microsserviço do OPC Vault é composto pelas seguintes interfaces para implementar o fluxo de trabalho para distribuir e gerenciar uma AC específica da empresa para Aplicativos OPC UA.

### <a name="application"></a>Aplicativo 
- Um aplicativo OPC UA pode ser um servidor ou um cliente ou ambos. O Cofre OPC funciona, nesse caso, como uma autoridade de registro de aplicativo. 
- Além das operações básicas para registrar, atualizar e cancelar o registro de aplicativos, também há interfaces para localizar e consultar aplicativos com expressões de pesquisa. 
- As solicitações de certificado precisam referenciar um aplicativo válido para processar uma solicitação e emitir um certificado assinado com todas as extensões específicas do OPC UA. 
- O serviço de aplicativo é apoiado por um banco de dados no Azure Cosmos DB.

### <a name="certificate-group"></a>Grupo de certificados
- Um grupo de certificados é uma entidade que armazena uma AC raiz ou um subcertificado de Autoridade de Certificação, incluindo a chave privada para assinar certificados. 
- O comprimento da chave RSA, o comprimento do hash SHA-2 e o tempo de vida podem ser configurados pela AC Emissora e por certificados de aplicativo assinados. 
- Você armazena os certificados de AC no Azure Key Vault, com suporte ao HSM FIPS 140-2 Nível 2. A chave privada nunca deixa o armazenamento seguro, porque a assinatura é realizada por uma operação do Key Vault protegida pelo Azure AD. 
- Os certificados de AC podem ser renovados ao longo do tempo e ainda permanecem no armazenamento seguro devido ao histórico do Key Vault. 
- A lista de revogação para cada Certificado de Autoridade de Certificação também é armazenada no Key Vault como um segredo. Quando um aplicativo tem o registro cancelado, o certificado do aplicativo também é revogado na CRL (lista de certificados revogados) por um administrador.
- Você pode revogar certificados únicos, bem como certificados em lote.

### <a name="certificate-request"></a>Solicitação de certificado
Uma solicitação de certificado implementa o fluxo de trabalho para gerar um novo par de chaves ou um certificado assinado usando uma CSR (Solicitação de Assinatura de Certificado) de um aplicativo OPC UA. 
- A solicitação é armazenada em um banco de dados com informações complementares, como o assunto ou uma CSR e uma referência ao aplicativo OPC UA. 
- A lógica de negócios no serviço valida a solicitação em relação às informações armazenadas no banco de dados do aplicativo. Por exemplo, o URI do aplicativo no banco de dados deve corresponder ao URI do aplicativo na CSR.
- Um administrador da segurança com direitos de assinatura (função de aprovador) aprova ou rejeita a solicitação. Se a solicitação for aprovada, um novo par de chaves ou um certificado assinado (ou ambos) serão gerados. A nova chave privada é armazenada com segurança no Key Vault e o novo certificado público assinado é armazenado no banco de dados da solicitação de certificado.
- O solicitante pode sondar o status da solicitação até que ela seja aprovada ou revogada. Se a solicitação tiver sido aprovada, a chave privada e o certificado poderão ser baixados e instalados no repositório de certificados do aplicativo OPC UA.
- Agora o solicitante pode aceitar a solicitação para excluir informações desnecessárias do banco de dados de solicitação. 

Durante o tempo de vida de um certificado assinado, um aplicativo pode ser excluído ou uma chave pode ficar comprometida. Nesse caso, um gerenciador de AC pode:
- Excluir um aplicativo, o que também exclui todas as solicitações de certificado pendentes e aprovadas do aplicativo. 
- Excluir somente uma única solicitação de certificado se apenas uma chave for renovada ou estiver comprometida.

Agora as solicitações de certificado aprovadas e aceitas comprometidas são marcadas como excluídas.

Um gerente pode renovar regularmente a CRL da AC do emissor do certificado. No momento da renovação, todas as solicitações de certificado excluídas são revogadas e os números de série do certificado são adicionados à lista de revogação de CRL. As solicitações de certificado revogadas são marcadas como revogadas. Em eventos urgentes, solicitações de certificado únicas podem ser revogadas também.

Por fim, as CRLs atualizadas estão disponíveis para distribuição para os clientes e servidores do OPC UA participantes.

## <a name="opc-vault-iot-edge-module"></a>Módulo do IoT Edge do Cofre OPC
Para dar suporte a um Servidor de Descoberta Global de rede de fábrica, você pode implantar o módulo do OPC Vault na borda. Execute-o como um aplicativo local do .NET Core ou inicie-o em um contêiner do Docker. Observe que, devido à falta de suporte à autenticação Auth2 na pilha do .NET Standard do OPC UA atual, a funcionalidade do módulo de borda do OPC Vault é limitada a uma função de leitor. Um usuário não pode ser representado com o módulo de borda para o microsserviço usando a interface padrão do GDS do OPC UA.

## <a name="next-steps"></a>Próximas etapas

Agora que aprendeu sobre a arquitetura do OPC Vault, você pode:

> [!div class="nextstepaction"]
> [Criar e implantar o Cofre OPC](howto-opc-vault-deploy.md)
