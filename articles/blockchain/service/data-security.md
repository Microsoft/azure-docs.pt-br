---
title: Segurança do serviço Blockchain do Azure
description: Conceitos de segurança e acesso a dados do serviço Blockchain do Azure
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 989d9f2afad30517a85185878d694c0b6640e987
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "80879591"
---
# <a name="azure-blockchain-service-security"></a>Segurança do serviço Blockchain do Azure

O serviço Blockchain do Azure usa vários recursos do Azure para manter seus dados seguros e disponíveis. Os dados são protegidos usando isolamento, criptografia e autenticação.

## <a name="isolation"></a>Isolamento

Os recursos do serviço Blockchain do Azure são isolados em uma rede virtual privada. Cada nó de validação e transação é uma VM (máquina virtual). As VMs em uma rede virtual não podem se comunicar diretamente com as VMs em uma rede virtual diferente. O isolamento garante que a comunicação permaneça privada na rede virtual. Para obter mais informações sobre o isolamento de rede virtual do Azure, consulte [isolamento na nuvem pública do Azure](../../security/fundamentals/isolation-choices.md#networking-isolation).

![Diagrama de VNET](./media/data-security/vnet.png)

## <a name="encryption"></a>Criptografia

Os dados do usuário são armazenados no armazenamento do Azure. Os dados do usuário são criptografados em movimento e em repouso para segurança e confidencialidade. Para obter mais informações, consulte: [Guia de segurança do armazenamento do Azure](../../storage/blobs/security-recommendations.md).

## <a name="authentication"></a>Autenticação

As transações podem ser enviadas para nós blockchain por meio de um ponto de extremidade RPC. Os clientes se comunicam com um nó de transação usando um servidor proxy reverso que manipula a autenticação do usuário e criptografa os dados por TLS.

![Diagrama de autenticação](./media/data-security/authentication.png)

Há três modos de autenticação para acesso RPC.

### <a name="basic-authentication"></a>Autenticação básica

A autenticação básica usa um cabeçalho de autenticação HTTP que contém o nome de usuário e a senha. Nome de usuário é o nome do nó blockchain. A senha é definida durante o provisionamento de um membro ou nó. A senha pode ser alterada usando o portal do Azure ou a CLI.

### <a name="access-keys"></a>Chaves de acesso

As chaves de acesso usam uma cadeia de caracteres gerada aleatoriamente incluída na URL do ponto de extremidade. Duas chaves de acesso ajudam a habilitar a rotação de chaves. As chaves podem ser geradas novamente a partir do portal do Azure e da CLI.

### <a name="azure-active-directory"></a>Azure Active Directory

O Azure Active Directory (AD do Azure) usa um mecanismo de autenticação baseado em declaração em que o usuário é autenticado pelo Azure AD usando as credenciais de usuário do Azure AD. O Azure AD fornece gerenciamento de identidade baseado em nuvem e permite que os clientes usem uma única identidade em toda a empresa e acessem aplicativos na nuvem. O serviço Blockchain do Azure integra-se ao Azure AD habilitando a Federação de ID, logon único e autenticação multifator. Você pode atribuir usuários, grupos e funções de aplicativo em sua organização para acesso de membro e nó do blockchain.

O proxy de cliente do Azure AD está disponível no [GitHub](https://github.com/Microsoft/azure-blockchain-connector/releases). O proxy do cliente direciona o usuário para a página de entrada do Azure AD e Obtém um token de portador após a autenticação bem-sucedida. Subsequentemente, o usuário conecta um aplicativo cliente Ethereum, como Geth ou Truffle ao ponto de extremidade do proxy do cliente. Por fim, quando uma transação é enviada, o proxy do cliente injeta o token de portador no cabeçalho HTTP e o proxy reverso valida o token usando o protocolo OAuth.

## <a name="keys-and-ethereum-accounts"></a>Contas de chaves e Ethereum

Ao provisionar um membro do serviço Blockchain do Azure, uma conta do Ethereum e um par de chaves pública e privada são gerados. A chave privada é usada para enviar transações para o blockchain. A conta Ethereum é os últimos 20 bytes do hash da chave pública. A conta Ethereum também é chamada de carteira.

O par de chaves pública e privada é armazenado como um keyfile no formato JSON. A chave privada é criptografada usando a senha inserida quando o serviço do razão do blockchain é criado.

As chaves privadas são usadas para assinar digitalmente as transações. No blockchains privado, um contrato inteligente assinado por uma chave privada representa a identidade do signatário. Para verificar a validade da assinatura, o receptor pode comparar a chave pública do Assinante com o endereço computado da assinatura.

As chaves Constellation são usadas para identificar exclusivamente um nó de quorum. As chaves Constellation são geradas no momento do provisionamento do nó e são especificadas no parâmetro privateFor de uma transação privada no quorum.

## <a name="next-steps"></a>Próximas etapas

Consulte [como configurar o acesso de Azure Active Directory para o serviço Blockchain do Azure](configure-aad.md).
