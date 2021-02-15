---
title: Segurança e governança corporativas
titleSuffix: Azure Machine Learning
description: 'Use o Azure Machine Learning com segurança: autenticação, autorização, segurança de rede, criptografia de dados e monitoramento.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 11/20/2020
ms.openlocfilehash: a079504872eaf3840416a99e784c4d33a6828b0c
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992022"
---
# <a name="enterprise-security-and-governance-for-azure-machine-learning"></a>Segurança e governança corporativas para Azure Machine Learning

Neste artigo, você aprenderá sobre os recursos de segurança e governança disponíveis para Azure Machine Learning. Esses recursos são úteis para administradores, DevOps e MLOps que desejam criar uma configuração segura que seja compatível com as políticas de suas empresas. Com o Azure Machine Learning e a plataforma Azure, você pode:

* Restringir o acesso a recursos e operações por conta de usuário ou grupos
* Restringir as comunicações de rede de entrada e saída
* Criptografar dados em trânsito e em repouso
* Verificar se há vulnerabilidades
* Aplicar e auditar políticas de configuração

## <a name="restrict-access-to-resources-and-operations"></a>Restringir o acesso a recursos e operações

[Azure Active Directory (AD do Azure)](../active-directory/fundamentals/active-directory-whatis.md) é o provedor de serviços de identidade para Azure Machine Learning. Ele permite que você crie e gerencie os objetos de segurança (usuário, grupo, entidade de serviço e identidade gerenciada) que são usados para _autenticar_ os recursos do Azure. A autenticação multifator terá suporte se o Azure AD estiver configurado para usá-lo.

Aqui está o processo de autenticação para Azure Machine Learning usando a autenticação multifator no Azure AD:

1. O cliente entra no Azure AD e obtém um token do Azure Resource Manager.
1. O cliente apresenta o token para o Azure Resource Manager e para todos os Azure Machine Learning.
1. Azure Machine Learning fornece um token de serviço de Machine Learning para o destino de computação do usuário (por exemplo, Azure Machine Learning cluster de computação). Esse token é usado pelo destino de computação do usuário para retornar ao serviço do Machine Learning depois de a execução ter sido concluída. O escopo é limitado ao espaço de trabalho.

[![Autenticação no Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

Cada espaço de trabalho tem uma [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) atribuída pelo sistema associada que tem o mesmo nome que o espaço de trabalho. Essa identidade gerenciada é usada para acessar com segurança os recursos usados pelo espaço de trabalho. Ele tem as seguintes permissões de RBAC do Azure em recursos anexados:

| Recurso | Permissões |
| ----- | ----- |
| Workspace | Colaborador |
| Conta de armazenamento | Colaborador de dados de blob de armazenamento |
| Cofre de chaves | Acesso a todas as chaves, segredos, certificados |
| Registro de Contêiner do Azure | Colaborador |
| Grupo de recursos que contém os workspace | Colaborador |
| Grupo de recursos que contém o cofre de chaves (caso seja diferente daquele que contém o workspace) | Colaborador |

Não recomendamos que os administradores revoguem o acesso da identidade gerenciada dos recursos mencionados na tabela anterior. Você pode restaurar o acesso usando a [operação de ressincronização de chaves](how-to-change-storage-access-key.md).

Azure Machine Learning também cria um aplicativo adicional (o nome começa com `aml-` ou `Microsoft-AzureML-Support-App-` ) com acesso no nível de colaborador em sua assinatura para cada região do espaço de trabalho. Por exemplo, caso você tenha um workspace no Leste dos EUA e outro no Norte da Europa na mesma assinatura, verá dois desses aplicativos. Esses aplicativos habilitam o Azure Machine Learning a ajudar você a gerenciar recursos de computação.

Você também pode configurar suas próprias identidades gerenciadas para uso com máquinas virtuais do Azure e Azure Machine Learning cluster de computação. Com uma VM, a identidade gerenciada pode ser usada para acessar seu espaço de trabalho do SDK, em vez da conta do Azure AD do usuário individual. Com um cluster de computação, a identidade gerenciada é usada para acessar recursos, como repositórios de armazenamento protegidos que o usuário que está executando o trabalho de treinamento pode não ter acesso ao. Para obter mais informações, consulte [autenticação para Azure Machine Learning espaço de trabalho](how-to-setup-authentication.md).

> [!TIP]
> Há algumas exceções ao uso do Azure AD e do RBAC do Azure no Azure Machine Learning:
> * Opcionalmente, você pode habilitar o acesso __SSH__ para computar recursos como Azure Machine Learning instância de computação e cluster de computação. O acesso SSH se baseia em pares de chaves pública/privada, não no Azure AD. O acesso SSH não é regido pelo RBAC do Azure.
> * Você pode autenticar para modelos implantados como serviços Web (pontos de extremidade de inferência) usando a autenticação baseada em __token__ ou __chave__ . As chaves são cadeias de caracteres estáticas, enquanto os tokens são recuperados usando um objeto de segurança do Azure AD. Para obter mais informações, consulte [Configurar a autenticação para modelos implantados como um serviço Web](how-to-authenticate-web-service.md).

Para obter mais informações, consulte os seguintes artigos:
* [Autenticação para Azure Machine Learning espaço de trabalho](how-to-setup-authentication.md)
* [Gerenciar o acesso ao Azure Machine Learning](how-to-assign-roles.md)
* [Conectar-se aos serviços de armazenamento](how-to-access-data.md)
* [Use Azure Key Vault para obter segredos ao treinar](how-to-use-secrets-in-runs.md)
* [Usar identidade gerenciada do Azure AD com o Azure Machine Learning](how-to-use-managed-identities.md)
* [Usar a identidade gerenciada do Azure AD com seu serviço Web](how-to-use-azure-ad-identity.md)

## <a name="network-security-and-isolation"></a>Segurança e isolamento de rede

Para restringir o acesso à rede para Azure Machine Learning recursos, você pode usar a [VNet (rede virtual) do Azure](../virtual-network/virtual-networks-overview.md). O VNets permite que você crie ambientes de rede que são parcialmente ou totalmente isolados da Internet pública. Isso reduz a superfície de ataque para sua solução, bem como as chances de vazamento de dados.

Você pode usar um gateway de VPN (rede virtual privada) para conectar clientes individuais ou sua própria rede à VNet

O espaço de trabalho Azure Machine Learning pode usar o [link privado do Azure](../private-link/private-link-overview.md) para criar um ponto de extremidade privado por trás da VNet. Isso fornece um conjunto de endereços IP privados que podem ser usados para acessar o espaço de trabalho de dentro da VNet. Alguns dos serviços dos quais Azure Machine Learning depende também podem usar o link privado do Azure, mas alguns dependem de grupos de segurança de rede ou de roteamento definido pelo usuário.

Para obter mais informações, consulte um dos seguintes documentos:

* [Visão geral de isolamento de rede virtual e privacidade](how-to-network-security-overview.md)
* [Recursos de workspace seguro](how-to-secure-workspace-vnet.md)
* [Ambiente de treinamento seguro](how-to-secure-training-vnet.md)
* [Ambiente de inferência seguro](how-to-secure-inferencing-vnet.md)
* [Usar o estúdio em uma rede virtual segura](how-to-enable-studio-virtual-network.md)
* [Usar o DNS personalizado](how-to-custom-dns.md)
* [Configurar o firewall](how-to-access-azureml-behind-firewall.md)

<a id="encryption-at-rest"></a><a id="azure-blob-storage"></a>

## <a name="data-encryption"></a>Criptografia de dados

O Azure Machine Learning usa uma variedade de recursos de computação e armazenamentos de dados na plataforma Azure. Para saber mais sobre como cada um deles dá suporte à criptografia de dados em repouso e em trânsito, consulte [criptografia de dados com Azure Machine Learning](concept-data-encryption.md).

Ao implantar modelos como serviços Web, você pode habilitar o TLS (segurança de camada de transporte) para criptografar dados em trânsito. Para obter mais informações, consulte [configurar um serviço Web seguro](how-to-secure-web-service.md).

## <a name="vulnerability-scanning"></a>Verificação de vulnerabilidade

A [central de segurança do Azure](../security-center/security-center-introduction.md) fornece gerenciamento de segurança unificado e proteção avançada contra ameaças em cargas de trabalho de nuvem híbrida. Para o Azure Machine Learning, você deve habilitar a verificação do recurso de [registro de contêiner do Azure](../container-registry/container-registry-intro.md) e os recursos do serviço kubernetes do Azure. Para obter mais informações, consulte [verificação de imagem do registro de contêiner do Azure por central de segurança](../security-center/defender-for-container-registries-introduction.md) e [integração dos serviços Kubernetess do Azure com a central de segurança](../security-center/defender-for-kubernetes-introduction.md).

## <a name="audit-and-manage-compliance"></a>Auditar e gerenciar conformidade

[Azure Policy](../governance/policy/index.yml) é uma ferramenta de governança que permite garantir que os recursos do Azure estejam em conformidade com suas políticas. Você pode definir políticas para permitir ou impor configurações específicas, como se o espaço de trabalho do Azure Machine Learning usa um ponto de extremidade privado. Para obter mais informações sobre Azure Policy, consulte a [documentação do Azure Policy](../governance/policy/overview.md). Para obter mais informações sobre as políticas específicas para Azure Machine Learning, consulte [auditar e gerenciar a conformidade com o Azure Policy](how-to-integrate-azure-policy.md).

## <a name="next-steps"></a>Próximas etapas

* [Proteger serviços Web do Azure Machine Learning com TLS](how-to-secure-web-service.md)
* [Consumir um modelo de Machine Learning implantado como um serviço Web](how-to-consume-web-service.md)
* [Usar o Azure Machine Learning com o Firewall do Azure](how-to-access-azureml-behind-firewall.md)
* [Usar o Azure Machine Learning com a Rede Virtual do Azure](how-to-network-security-overview.md)
* [Criptografia de dados em repouso e em trânsito](concept-data-encryption.md)
* [Compilar uma API de recomendação em tempo real no Azure](/azure/architecture/reference-architectures/ai/real-time-recommendation)
