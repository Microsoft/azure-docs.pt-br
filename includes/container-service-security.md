---
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/09/2018
ms.author: danlep
ms.openlocfilehash: 39bb75a6f834789f91cb590ffebb72f45624eb25
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172475"
---
# <a name="deprecated-securing-docker-containers-in-azure-container-service"></a>(PRETERIDO) Proteger contêineres do Docker no Serviço de Contêiner do Azure

[!INCLUDE [ACS deprecation](container-service-deprecation.md)]

Este artigo apresenta as considerações e as recomendações para proteger os contêineres do Docker implantados no Serviço de Contêiner do Azure. Várias dessas considerações aplicam-se em geral aos contêineres do Docker implantados no Azure ou outros ambientes. 

## <a name="image-security"></a>Segurança da imagem

Os contêineres são criados a partir de imagens que são armazenadas em um ou mais repositórios. Esses repositórios podem pertencer a registros de contêineres públicos ou privados. Um exemplo de um registro público é o [Docker Hub](https://hub.docker.com/). Um exemplo de um registro privado é o [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/2.0/), que pode ser instalado localmente ou em uma nuvem privada virtual. Há também serviços de registro de contêiner privado baseados em nuvem incluindo [Registro de Contêiner do Azure](../articles/container-registry/container-registry-intro.md).

### <a name="public-and-private-images"></a>Imagens públicas e privadas
Em geral, assim como acontece com qualquer pacote de software publicado publicamente, uma imagem de contêiner disponível publicamente não garante a segurança. As imagens de contêiner são compostas por várias camadas de software e cada camada de software pode ter vulnerabilidades. É fundamental compreender a origem da imagem do contêiner, incluindo o proprietário da imagem (para determinar se é uma fonte confiável ou não), as camadas de software que a compõem e as versões do software. 

Por exemplo, se você for ao [repositório nginx](https://hub.docker.com/_/nginx/) original no Docker Hub e navegar até a guia **Marcas**, verá vulnerabilidades codificadas por cores em cada imagem. Cada cor representa a vulnerabilidade de uma camada de software da imagem. Para saber mais sobre a verificação de vulnerabilidades no Docker Hub, veja [Noções básicas sobre repositórios oficiais no Docker Hub](https://blog.docker.com/2015/06/understanding-official-repos-docker-hub/).

![Imagens de nginx no Docker Hub](./media/container-service-security/docker-hub-nginx.png)

As empresas preocupam-se profundamente com segurança e, para se protegerem contra ataques, devem armazenar e recuperar imagens de um registro privado, como o Registro de Contêiner do Azure ou Docker Trusted Registry. Além de fornecer um registro privado gerenciado, o Registro de Contêiner do Azure dá suporte à [autenticação baseada em entidade de serviço](../articles/container-registry/container-registry-authentication.md) através do Active Directory do Azure para fluxos de autenticação básica, incluindo o acesso baseado em função para permissões de somente leitura, gravação e proprietário.

### <a name="image-security-scanning"></a>Verificação de segurança de imagem

Mesmo ao usar um registro privado, é uma boa ideia usar soluções de verificação de imagem para obter validação de segurança adicional. Cada camada de software em uma imagem de contêiner é potencialmente propensa a vulnerabilidades independentes de outras camadas na imagem de contêiner. À medida que cada vez mais empresas começam a implantar cargas de trabalho de produção com base nas tecnologias de contêiner, a verificação de imagem torna-se importante para garantir a prevenção de ameaças à segurança em suas organizações. 

As soluções de verificação e monitoramento de segurança, como [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry) e [Aqua Security](http://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry), dentre outros, podem ser usadas para verificar as imagens de contêiner em um registro privado e identificar possíveis vulnerabilidades. É importante compreender a profundidade da verificação fornecida pelas diferentes soluções. Por exemplo, algumas soluções podem apenas realizar a verificação cruzada entre as camadas de imagem em relação a vulnerabilidades conhecidas. Essas soluções podem não ser capazes de verificar o software de camada de imagem criada por meio de determinados softwares de gerenciadores de pacote. Outras soluções têm integração de verificação mais profunda e podem encontrar vulnerabilidades em qualquer software de pacote.

### <a name="production-deployment-rules-and-audit"></a>Regras de implantação de produção e auditoria
Quando um aplicativo é implantado em produção, é essencial estabelecer algumas regras para garantir que as imagens usadas em ambientes de produção sejam seguras e não contenham vulnerabilidades.

* Como regra, as imagens com vulnerabilidades, até mesmo pequenas, não devem ter permissão para serem executadas em um ambiente de produção. Além disso, todas as imagens implantadas em produção idealmente devem ser salvas em um registro privado acessível para um seleto grupo. Também é importante manter poucas imagens de produção para garantir que elas possam ser gerenciadas com eficiência.

* Como é difícil identificar a origem do software de uma imagem de contêiner disponível publicamente, é uma boa prática para criar imagens de origem para garantir o conhecimento da origem da camada. Quando surge uma vulnerabilidade em uma imagem de contêiner criada automaticamente, os clientes podem encontrar um caminho mais rápido para uma resolução. Com uma imagem pública, os clientes precisam encontrar a raiz de uma imagem pública para corrigi-la ou obter outra imagem segura do publicador.

* Não há garantia de que uma imagem digitalizada totalmente implantada em produção esteja atualizada durante o tempo de vida do aplicativo. As vulnerabilidades de segurança podem ser reportadas para camadas da imagem que não eram conhecidas anteriormente ou que foram introduzidas após a implantação em produção. A auditoria periódica das imagens implantadas em produção é necessária para identificar as imagens que estão desatualizadas ou que não foram atualizadas há um tempo. É possível usar metodologias de implantação blue-green e mecanismos de atualização sem interrupção para atualizar as imagens de contêiner sem tempo de inatividade. A verificação de imagem pode ser feita com as ferramentas descritas na seção anterior. 

* Um pipeline de CI (integração contínua) para criar imagens e a verificação de segurança integrada pode ajudar a manter registros particulares seguros com imagens de contêiner protegidas. A verificação de vulnerabilidade incorporada à solução de CI garante que as imagens que passam em todos os testes sejam enviadas para o registro privado a partir do qual as cargas de trabalho de produção são implantadas. Uma falha de pipeline de CI garante que as imagens vulneráveis não sejam enviadas para o registro privado usado para implantações de cargas de trabalho de produção. Isso também automatiza a verificação de segurança de imagem se houver um número significativo de imagens. Caso contrário, a auditoria manual de imagens em busca de vulnerabilidades de segurança pode ser muito demorada e sujeita a erros.

## <a name="host-level-container-isolation"></a>Isolamento de contêiner de nível de host
Quando um cliente implanta aplicativos de contêiner nos recursos do Azure, eles são implantados em um nível de assinatura em grupos de recursos e não são multilocatários. Isso significa que, se um cliente compartilhar uma assinatura com outras pessoas, não haverá nenhum limite que possa ser criado entre duas implantações na mesma assinatura. Portanto, a segurança de nível de contêiner não estará garantida. 

Também é fundamental entender que os contêineres compartilham o kernel e os recursos do host (que, no Serviço de Contêiner do Azure, é uma VM do Azure em um cluster). Portanto, os contêineres em produção devem ser executados no modo de usuário sem privilégios. Executar um contêiner com privilégios de raiz pode comprometer todo o ambiente. Com o acesso de nível de raiz em um contêiner, um hacker pode ter privilégios de raiz completos no host. Além disso, é importante executar contêineres com sistemas de arquivos somente leitura. Isso impede que alguém que tenha acesso ao contêiner comprometido grave scripts mal-intencionados no sistema de arquivos e obtenha acesso a outros arquivos. Da mesma forma, é importante limitar os recursos (como memória, CPU e largura de banda de rede) alocados para um contêiner. Isso ajuda a impedir que hackers consigam mais recursos e busquem atividades ilegais como fraude de cartão de crédito ou mineração de bitcoin, que pode impedir a execução de outros contêineres no host ou cluster.

## <a name="orchestrator-considerations"></a>Considerações sobre o Orchestrator

Cada orquestrador disponível no Serviço de Contêiner do Azure tem suas próprias considerações de segurança. Por exemplo, você deve limitar o acesso direto de SSH para nós do orquestrador no serviço de contêiner. Em vez disso, use a interface de usuário de cada orquestrador ou ferramentas de linha de comando (como `kubectl` para Kubernetes) para gerenciar o ambiente de contêiner sem acessar os hosts. Para saber mais, veja [Fazer uma conexão remota com um cluster Kubernetes, DC/OS ou Docker Swarm](../articles/container-service/kubernetes/container-service-connect.md).

Para saber mais adicionais de segurança específicas do orquestrador, veja os seguintes recursos:

* **Kubernetes**: [Práticas recomendadas de segurança para implantação de Kubernetes](https://kubernetes.io/blog/2016/08/security-best-practices-kubernetes-deployment/)

* **DC/OS**: [Proteção do cluster](http://docs.mesosphere.com/1.12/administering-clusters/securing-your-cluster)

* **Docker Swarm**: [Segurança do Docker](https://www.docker.com/docker-security)

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre a arquitetura do Docker e a segurança do contêiner, veja [Introdução à segurança do contêiner](https://www.docker.com/sites/default/files/WP_IntrotoContainerSecurity_08.19.2016.pdf).

* Para saber mais sobre segurança da plataforma do Azure, veja a [Central de Segurança do Azure](https://www.microsoft.com/en-us/trustcenter/cloudservices/azure).
