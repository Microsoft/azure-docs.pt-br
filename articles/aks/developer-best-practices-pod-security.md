---
title: Melhores práticas do desenvolvedor - Segurança do Pod no Azure Kubernetes Services (AKS)
description: Conheça as práticas recomendadas do desenvolvedor para saber como proteger os pods no serviço de Kubernetes do Azure (AKS)
services: container-service
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: zarhoads
ms.openlocfilehash: 1c7143b6d3479cf3083cfc730301c68dcf4eb705
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92900818"
---
# <a name="best-practices-for-pod-security-in-azure-kubernetes-service-aks"></a>Práticas recomendadas de segurança do pod no Serviço de Kubernetes do Azure (AKS)

Ao desenvolver e executar aplicativos no serviço de Kubernetes do Azure (AKS), a segurança de seus pods é uma consideração importante. Os aplicativos devem ser criados para a entidade de segurança do número mínimo de privilégios necessários. Manter os dados privados seguros é prioritário para clientes. Você não quer que as credenciais como cadeias de caracteres de conexão de banco de dados, chaves ou segredos e certificados expostos ao mundo externo, onde um ataque poderia tirar proveito desses segredos para fins mal-intencionados. Não os adicione ao seu código ou insira-os em suas imagens de contêiner. Essa abordagem criaria um risco de exposição e limitaria a capacidade de girar as credenciais uma vez que as imagens de contêiner precisam ser recriadas.

Este artigo sobre melhores práticas se concentra em como proteger os pods no AKS. Você aprenderá como:

> [!div class="checklist"]
> * Usar o contexto de segurança do pod para limitar o acesso a serviços e processos ou elevação de privilégios
> * Autenticar com outros recursos do Azure usando identidades gerenciadas do pod
> * Solicitar e recuperar as credenciais de um cofre digital, como o Azure Key Vault

Você também pode ler as melhores práticas para [segurança do cluster][best-practices-cluster-security] e para [gerenciamento da imagem de contêiner][best-practices-container-image-management].

## <a name="secure-pod-access-to-resources"></a>Proteger o acesso de pod para recursos

**Diretrizes de práticas recomendadas** - executar como usuário ou grupo diferente e limitar o acesso para os processos de nó e serviços subjacentes, definir as configurações do contexto de segurança do pod. Atribua o menor número de privilégios necessários.

Para que seus aplicativos sejam executados corretamente, os pods devem ser executados como um grupo ou usuário definido e não como *raiz*. O `securityContext` para um pod ou contêiner permite que você defina configurações, como *runAsUser* ou *fsGroup* para presumir as permissões apropriadas. Somente atribua as permissões de usuário ou grupo necessárias e não use o contexto de segurança como um meio para admitir permissões adicionais. O *runAsUser*, a elevação de privilégio e outras configurações de recursos do Linux estão disponíveis apenas em nós e pods do Linux.

Ao executar como usuário não raiz, os contêineres não poderão associar às portas privilegiadas em 1024. Nesse cenário, os Serviços de Kubernetes podem ser usados para distinguir o fato de que um aplicativo é executado em uma porta específica.

Um contexto de segurança de pod também pode definir permissões para acessar os processos e serviços ou recursos adicionais. As seguintes definições de contexto de segurança comuns podem ser definidas:

* **allowPrivilegeEscalation** define se o pod pode assumir privilégios *raiz*. Projete seus aplicativos de forma que essa configuração seja sempre definida como *false*.
* **Os recursos do Linux** permitem que o pod acesse os processos de nó subjacente. Tome cuidado com a atribuição a esses recursos. Atribua o menor número de privilégios necessários. Para obter mais informações, consulte [Recursos do Linux][linux-capabilities].
* **Rótulos do SELinux** é um módulo de segurança de kernel do Linux que permite que você defina políticas de acesso para serviços, processos e acesso a sistema de arquivos. Novamente, atribua o menor número de privilégios necessários. Para obter mais informações, consulte [Opções SELinux no Kubernetes][selinux-labels]

O manifesto YAML do pod de exemplo a seguir define as configurações de contexto de segurança a definir:

* O pod é executado com a ID de usuário *1000* e faz parte da ID do grupo *2000*
* Não é possível escalonar os privilégios para usar `root`
* Permite que os recursos do Linux acessem as interfaces de rede e o relógio (hardware) em tempo real do host

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    fsGroup: 2000
  containers:
    - name: security-context-demo
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      securityContext:
        runAsUser: 1000
        allowPrivilegeEscalation: false
        capabilities:
          add: ["NET_ADMIN", "SYS_TIME"]
```

Trabalhe com seu operador de cluster para determinar quais configurações de contexto de segurança são necessárias. Tente projetar seus aplicativos para minimizar o acesso que requer o pod e as permissões do pod. Há recursos de segurança adicionais para limitar o acesso usando o AppArmor e seccomp (computação segura) que pode ser implementado por operadores do cluster. Para obter mais informações, consulte [Proteger acesso do contêiner para recursos][apparmor-seccomp].

## <a name="limit-credential-exposure"></a>Exposição de credencial de limite

**Diretrizes de práticas recomendadas** - não defina as credenciais no código do aplicativo. Use identidades gerenciadas para recursos do Azure para permitir sua solicitação de acesso de pod para outros recursos. Um cofre digital, como o Azure Key Vault, também deve ser usado para armazenar e recuperar as credenciais e chaves digitais. As identidades gerenciadas por pod devem ser usadas somente com os pods e imagens de contêiner do Linux.

Para limitar o risco de credenciais que estão sendo expostas no código do aplicativo, evite o uso de credenciais compartilhadas ou fixas. As credenciais ou chaves não devem ser incluídas diretamente em seu código. Se essas credenciais são expostas, o aplicativo precisa ser atualizado e reimplantado. Uma abordagem melhor é dar aos pods sua própria identidade e maneira de se autenticar ou recuperar credenciais automaticamente de um cofre digital.

### <a name="use-azure-container-compute-upstream-projects"></a>Usar projetos de upstream de computação em contêineres do Azure

> [!IMPORTANT]
> Os projetos de software livre AKS associados não têm suporte do suporte técnico do Azure. Eles são fornecidos para que os usuários se instalem em clusters e obtenham comentários de nossa comunidade.

Os seguintes [projetos de software livre AKS associados][aks-associated-projects] permitem autenticar automaticamente pods ou solicitar credenciais e chaves de um cofre digital. Esses projetos são mantidos pela equipe de upstream de computação em contêineres do Azure e fazem parte de uma [lista mais ampla de projetos disponíveis para uso](https://github.com/Azure/container-compute-upstream/blob/master/README.md#support).

 * [Identidade do pod do Azure Active Directory][aad-pod-identity]
 * [Provedor de Azure Key Vault para driver do CSI de armazenamento de segredos](https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage)

#### <a name="use-pod-managed-identities"></a>Usar identidades gerenciadas de pod

Uma identidade gerenciada para recursos do Azure permite que um pod se autentique nos serviços do Azure que oferecem suporte, como o Armazenamento ou o SQL. O pod é atribuído a uma identidade do Azure Identity que permite que eles se autentiquem no Azure Active Directory e recebam um token digital. Esse token digital pode ser apresentado aos outros serviços do Azure que verificam se o pod está autorizado a acessar o serviço e executar as ações necessárias. Essa abordagem significa que não há segredos necessários para cadeias de caracteres de conexão de banco de dados, por exemplo. O fluxo de trabalho simplificado para identidade gerenciada de pod é mostrado no diagrama a seguir:

:::image type="content" source="media/developer-best-practices-pod-security/basic-pod-identity.svg" alt-text="Fluxo de trabalho simplificado para pod gerenciado de identidade no Azure":::

Com uma identidade gerenciada, seu código do aplicativo não precisa incluir as credenciais para acessar um serviço, como o Armazenamento do Microsoft Azure. Uma vez que cada pod é autenticado com sua própria identidade, então, você pode auditar e revisar o acesso. Se seu aplicativo se conecta com outros serviços do Azure, use identidades gerenciadas para reutilização de credenciais de limite e o risco de exposição.

Para obter mais informações sobre identidades de pod, consulte [Configurar um cluster do AKS para usar identidades gerenciadas de pod e com seus aplicativos][aad-pod-identity]

#### <a name="use-azure-key-vault-with-secrets-store-csi-driver"></a>Use o Azure Key Vault com o driver do CSI de armazenamento de segredos

O uso do projeto de identidade do pod habilita a autenticação no suporte aos serviços do Azure. Para seus próprios serviços ou aplicativos sem identidades gerenciadas para recursos do Azure, você ainda pode autenticar usando credenciais ou chaves. Um cofre digital pode ser usado para armazenar esses conteúdos de segredos.

Quando os aplicativos precisam de uma credencial, eles se comunicam com o cofre digital, recuperam o conteúdo secreto mais recente e, em seguida, conectam-se ao serviço necessário. O Azure Key Vault pode ser este cofre digital. O fluxo de trabalho simplificado para recuperar uma credencial de Cofre de Chaves do Azure usando identidades gerenciadas de pod é mostrado no diagrama a seguir:

:::image type="content" source="media/developer-best-practices-pod-security/basic-key-vault.svg" alt-text="Identidade do fluxo de trabalho simplificado para recuperar uma credencial de Cofre de Chaves usando uma identidade gerenciada de pod":::

Com o Azure Key Vault, você pode armazenar e regularmente girar segredos, como certificados, chaves de conta de armazenamento ou as credenciais. Você pode integrar o Azure Key Vault com um cluster AKS usando o [Provedor do Azure Key Vault para o driver do CSI de armazenamento de segredos](https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage). O driver do CSI de armazenamento de segredos permite que o cluster AKS recupere nativamente o conteúdo secreto do cofre de chaves e os forneça com segurança apenas para o pod solicitante. Trabalhe com seu operador de cluster para implantar o driver do CSI de armazenamento de segredos nos nós de trabalho do AKS. Você pode usar uma identidade gerenciada de pod para solicitar acesso ao cofre de chaves e recuperar o conteúdo secreto necessário através do driver do CSI de armazenamento de segredos.

O Azure Key Vault com o driver do CSI de armazenamento de segredos pode ser usado para nós e pods do Linux que exigem um Kubernetes versão 1.16 ou superior. Para nós e pods do Windows, é necessária um Kubernetes versão 1.18 ou superior.

## <a name="next-steps"></a>Próximas etapas

Este artigo se concentrou em como proteger seus pods. Para implementar algumas dessas áreas, confira os seguintes artigos:

* [Usar identidades gerenciadas para recursos do Azure com AKS][aad-pod-identity]
* [Integrar o Azure Key Vault ao AKS][aks-keyvault-csi-driver]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity#demo
[aks-keyvault-csi-driver]: https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage
[linux-capabilities]: http://man7.org/linux/man-pages/man7/capabilities.7.html
[selinux-labels]: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.18/#selinuxoptions-v1-core
[aks-associated-projects]: https://awesomeopensource.com/projects/aks?categoryPage=11

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[aks-pod-identities]: operator-best-practices-identity.md#use-pod-identities
[apparmor-seccomp]: operator-best-practices-cluster-security.md#secure-container-access-to-resources
