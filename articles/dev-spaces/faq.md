---
title: Perguntas frequentes sobre Azure Dev Spaces
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: Encontre respostas para algumas das perguntas mais comuns sobre Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Serviço de Kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, k8s '
ms.openlocfilehash: e83bed86714e4b92c63f4e7b7eb55df7a2a7eaff
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96548827"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Perguntas frequentes sobre Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Isso aborda as perguntas frequentes sobre Azure Dev Spaces.

## <a name="what-versions-of-kubernetes-are-supported-for-azure-dev-spaces"></a>Quais versões do kubernetes têm suporte para Azure Dev Spaces?

O Azure Dev Spaces dá suporte [a versões de GA (disponibilidade geral) com suporte no momento no AKs até 1,18][aks-supported-k8s]. O kubernetes 1,19 e superior no AKS usa o contêiner como o tempo de execução do contêiner, que não funciona com Azure Dev Spaces.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Quais regiões do Azure atualmente fornecem Azure Dev Spaces?

Consulte [regiões com suporte][supported-regions] para obter uma lista completa de regiões disponíveis.

## <a name="can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region"></a>Posso migrar meu cluster AKS com Azure Dev Spaces para outra região?

Sim, se você quiser mover o cluster AKS com Azure Dev Spaces para outra [região com suporte][supported-regions], recomendamos que você crie um novo cluster na outra região, em seguida, instale e configure o Azure dev Spaces e implante seus recursos e aplicativos em seu novo cluster. Para obter mais informações sobre como migrar AKS, consulte [migrar para o AKs (serviço kubernetes do Azure)][aks-migration].

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>Posso usar Azure Dev Spaces com gráficos Dockerfiles ou Helm existentes?

Sim, se o seu projeto já tiver um gráfico Dockerfile ou Helm, você poderá usar esses arquivos com Azure Dev Spaces. Ao executar `azds prep` , use o `--chart` parâmetro e especifique o local do gráfico. Azure Dev Spaces ainda irá gerar um arquivo *azds. YAML* e *Dockerfile. develop* , mas ele não substituirá ou modificará um gráfico Dockerfile ou Helm existente. Talvez seja necessário modificar os arquivos *azds. YAML* e *Dockerfile. develop* para que tudo funcione corretamente com o aplicativo existente durante a execução `azds up` .

Ao usar seu próprio gráfico Dockerfile ou Helm, há as seguintes limitações:
* Se estiver usando apenas um Dockerfile, ele deverá incluir tudo o que você precisa para habilitar cenários de desenvolvimento, como o SDK do idioma, não apenas o tempo de execução. Se estiver usando um Dockerfile separado para Azure Dev Spaces, como um Dockerfile. desenvolver, tudo o que você precisa para habilitar cenários de desenvolvimento deve ser incluído nesse Dockerfile.
* Seu gráfico de Helm deve dar suporte à passagem de parte da marca de imagem inteira como um valor de *Values. YAML*.
* Se você estiver modificando algo com entrada, também poderá atualizar seu gráfico do Helm para usar a solução de entrada fornecida pelo Azure Dev Spaces.
* Se você quiser usar os [recursos de roteamento fornecidos pelo Azure dev Spaces][dev-spaces-routing], todos os serviços para um projeto individual devem se ajustar em um único namespace kubernetes e devem ser implantados com nome simples, por exemplo, *serviço-a*. Em gráficos Helm padrão, essa atualização de nomenclatura pode ser feita especificando um valor para a propriedade *fullnameOverride* .

## <a name="can-i-modify-the-files-generated-by-azure-dev-spaces"></a>Posso modificar os arquivos gerados pelo Azure Dev Spaces?

Sim, você pode modificar o arquivo *azds. YAML* , o Dockerfile e o gráfico Helm [gerados pelo Azure dev Spaces ao preparar seu projeto][dev-spaces-prep]. Modificar esses arquivos altera o modo como o projeto é compilado e executado.

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>Posso usar Azure Dev Spaces sem um endereço IP público?

Não, você não pode provisionar Azure Dev Spaces em um cluster AKS sem um IP público. Um IP público é [necessário pelo Azure dev Spaces para roteamento][dev-spaces-routing].

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Posso usar minha própria entrada com Azure Dev Spaces?

Sim, você pode configurar sua própria entrada ao longo do Azure Dev Spaces de entrada criado. Por exemplo, você pode usar [traefik][ingress-traefik] ou [Nginx][ingress-nginx].

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Posso usar HTTPS com Azure Dev Spaces?

Sim, você pode configurar sua própria entrada com HTTPS usando [traefik][ingress-https-traefik] ou [Nginx][ingress-https-nginx].

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Posso usar Azure Dev Spaces em um cluster que usa CNI em vez de kubenet? 

Sim, você pode usar Azure Dev Spaces em um cluster AKS que usa o CNI para rede. Por exemplo, você pode usar Azure Dev Spaces em um cluster AKS com [contêineres do Windows existentes][windows-containers], que usa o CNI para rede. Mais informações sobre como usar o CNI para rede com o Azure Dev Spaces está disponível [aqui](configure-networking.md#using-azure-cni).

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Posso usar Azure Dev Spaces com contêineres do Windows?

Atualmente, o Azure Dev Spaces destina-se a ser executado somente em pods e nós do Linux, mas você pode executar Azure Dev Spaces em um cluster AKS com [contêineres do Windows existentes][windows-containers].

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>Posso usar Azure Dev Spaces em clusters AKS com intervalos de endereços IP autorizados do servidor de API habilitado?

Sim, você pode usar Azure Dev Spaces em clusters AKS com [intervalos de endereços IP autorizados do servidor de API][aks-auth-range] habilitados. Mais informações sobre como usar clusters AKS com intervalos de endereços IP autorizados do servidor de API habilitados com o Azure Dev Spaces estão disponíveis [aqui](configure-networking.md#using-api-server-authorized-ip-ranges).

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>Posso usar Azure Dev Spaces em clusters AKS com tráfego de saída restrito para nós de cluster?

Sim, você pode usar Azure Dev Spaces em clusters AKS com [tráfego de saída restrito para nós de cluster][aks-restrict-egress-traffic] habilitados assim que os FQDNs corretos tiverem sido permitidos. Mais informações sobre como usar clusters AKS com tráfego de saída restrito para nós de cluster habilitados com o Azure Dev Spaces estão disponíveis [aqui](configure-networking.md#ingress-and-egress-network-traffic-requirements).

## <a name="can-i-use-azure-dev-spaces-on-kubernetes-rbac-enabled-aks-clusters"></a>Posso usar Azure Dev Spaces em clusters AKS habilitados para RBAC kubernetes?

Sim, você pode usar Azure Dev Spaces em clusters AKS com ou sem o controle de acesso baseado em função do kubernetes (kubernetes RBAC) habilitado.

## <a name="what-happens-when-i-enable-ingress-for-project-in-visual-studio"></a>O que acontece quando eu habilito a entrada para o projeto no Visual Studio?

Ao usar o Visual Studio para preparar seu projeto, você tem a opção de habilitar a entrada para seu serviço. Habilitar a entrada cria um ponto de extremidade público para acessar seu serviço quando executado no cluster do AKS, que é opcional. Se você não habilitar a entrada, seu serviço só poderá ser acessado de dentro do cluster AKS.

## <a name="can-i-use-pod-managed-identities-with-azure-dev-spaces"></a>Posso usar identidades gerenciadas de Pod com Azure Dev Spaces?

Sim, você pode usar [identidades gerenciadas de Pod][aks-pod-managed-id] em clusters AKS com Azure dev Spaces habilitado, mas há [etapas de configuração adicionais][dev-spaces-pod-managed-id-steps] depois de habilitar o Azure dev Spaces em seu cluster com identidades gerenciadas de Pod. Se você tiver identidades gerenciadas de Pod instaladas e quiser desinstalá-la, poderá encontrar mais detalhes nas [notas de desinstalação][aks-pod-managed-id-uninstall].

## <a name="can-i-use-azure-dev-spaces-with-multiple-microservices-in-an-application"></a>Posso usar Azure Dev Spaces com vários microserviços em um aplicativo?

Sim, você pode usar Azure Dev Spaces em um aplicativo com vários microserviços, mas deve preparar e executar os microserviços individuais em sua raiz. A CLI do Azure Dev Spaces, Azure Dev Spaces VS Code extensão e a carga de trabalho de desenvolvimento do Azure do Visual Studio esperam que o arquivo *azds. YAML* esteja na raiz do Microservice para executar e depurar. Consulte o exemplo de [aplicativo de compartilhamento de bicicletas][bike-sharing] para obter um exemplo de vários microserviços em um único aplicativo.

No Visual Studio Code, é possível [abrir projetos separados em um único espaço de trabalho][vs-code-multi-root-workspaces] e depurá-los separadamente por meio de Azure dev Spaces. Cada um dos projetos deve ser autônomo e preparado para Azure Dev Spaces.

No Visual Studio, é possível configurar soluções do .NET Core para depuração por meio de Azure Dev Spaces.

## <a name="can-i-use-azure-dev-spaces-with-a-service-mesh"></a>Posso usar Azure Dev Spaces com uma malha de serviço?

Neste momento, você não pode usar Azure Dev Spaces com malhas de serviço como [İSTİO][istio] ou [Linkerd][linkerd]. Você pode executar Azure Dev Spaces e uma malha de serviço no mesmo cluster AKS, mas não pode ter Azure Dev Spaces e uma malha de serviço habilitada no mesmo namespace.

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-migration]: ../aks/aks-migration.md
[aks-pod-managed-id]: ../aks/developer-best-practices-pod-security.md#use-pod-managed-identities
[aks-pod-managed-id-uninstall]: https://github.com/Azure/aad-pod-identity#uninstall-notes
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[aks-supported-k8s]: ../aks/supported-kubernetes-versions.md#azure-portal-and-cli-versions
[bike-sharing]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[dev-spaces-pod-managed-id-steps]: troubleshooting.md#error-no-azureassignedidentity-found-for-podazdsazds-webhook-deployment-id-in-assigned-state
[dev-spaces-prep]: how-dev-spaces-works-prep.md
[dev-spaces-routing]: how-dev-spaces-works-routing.md#how-routing-works
[ingress-nginx]: how-to/ingress-https-nginx.md#configure-a-custom-nginx-ingress-controller
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-nginx]: how-to/ingress-https-nginx.md#configure-the-nginx-ingress-controller-to-use-https
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[istio]: https://istio.io/
[linkerd]: https://linkerd.io/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[vs-code-multi-root-workspaces]: https://code.visualstudio.com/docs/editor/multi-root-workspaces
[windows-containers]: how-to/run-dev-spaces-windows-containers.md