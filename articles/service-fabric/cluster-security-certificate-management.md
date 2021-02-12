---
title: Gerenciamento de certificados em um Cluster Service Fabric
description: Saiba mais sobre como gerenciar certificados em um Cluster Service Fabric protegido com certificados X. 509.
ms.topic: conceptual
ms.date: 04/10/2020
ms.custom: sfrev
ms.openlocfilehash: 722c84c25cb5188e45dd96363bab9af6ff93f6dc
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901259"
---
# <a name="certificate-management-in-service-fabric-clusters"></a>Gerenciamento de certificados em clusters Service Fabric

Este artigo aborda os aspectos de gerenciamento de certificados usados para proteger a comunicação nos clusters de Service Fabric do Azure e complementa a introdução à [segurança de cluster Service Fabric](service-fabric-cluster-security.md) , bem como o explicador sobre a [autenticação baseada em certificado X. 509 no Service Fabric](cluster-security-certificates.md). Supomos que o leitor esteja familiarizado com os conceitos fundamentais de segurança e também com os controles que Service Fabric expões para configurar a segurança de um cluster.  

Aspectos cobertos por este título:

* O que é exatamente o ' Certificate Management '?
* Funções e entidades envolvidas no gerenciamento de certificados
* A jornada de um certificado
* Aprofunde-se em um exemplo
* Solução de problemas e perguntas frequentes

Mas, primeiro, um aviso de isenção de responsabilidade: Este artigo tenta emparelhar seu lado teórico com exemplos práticos, que exigem, bem, especificações de serviços, tecnologias e assim por diante. Como uma parte considerável do público é interna da Microsoft, nos referiremos a serviços, tecnologias e produtos específicos do Microsoft Azure. Sinta-se à vontade para perguntar na seção de comentários para obter esclarecimentos ou diretrizes, onde os detalhes específicos da Microsoft não se aplicam em seu caso.

## <a name="defining-certificate-management"></a>Definindo o gerenciamento de certificados
Como vimos no [artigo complementar](cluster-security-certificates.md), um certificado é um objeto criptográfico, essencialmente ligando um par de chaves assimétricas com atributos que descrevem a entidade que ele representa. No entanto, ele também é um objeto ' perishable ', no qual seu tempo de vida é finito e é suscetível a comprometimentos – a divulgação acidental ou uma exploração bem-sucedida pode tornar um certificado inútil de um ponto de vista de segurança. Isso implica a necessidade de alterar certificados – rotineiramente ou em resposta a um incidente de segurança. Outro aspecto do gerenciamento (e é um tópico inteiro por conta própria) é a proteção das chaves privadas do certificado ou dos segredos que protegem as identidades das entidades envolvidas no adquirir e no provisionamento de certificados. Nós nos referimos aos processos e procedimentos usados para obter certificados e para transportá-los com segurança (e com segurança) para onde eles são necessários como ' Certificate Management '. Algumas das operações de gerenciamento – como registro, configuração de política e controles de autorização-estão além do escopo deste artigo. Outros ainda, como provisionamento, renovação, recriação de chaves ou revogação, estão relacionados incidentalmente a Service Fabric; no entanto, vamos solucioná-los aqui para algum grau, já que entender essas operações pode ajudar com a proteção adequada do cluster de um. 

O objetivo é automatizar o gerenciamento de certificados o máximo possível para garantir a disponibilidade ininterrupta do cluster e oferecer garantias de segurança, Considerando que o processo é gratuito ao usuário. Essa meta é atingível atualmente em clusters de Service Fabric do Azure; o restante do artigo irá primeiro desconstruir o gerenciamento de certificados e, posteriormente, se concentrará na habilitação da autosubstituição.

Especificamente, os tópicos no escopo são:
  - suposições relacionadas à separação de atribuições entre o proprietário e a plataforma, no contexto de gerenciamento de certificados
  - o pipeline longo de certificados de emissão para consumo
  - rotação de certificado-por quê, como e quando
  - o que poderia dar errado?

O aspecto, como proteção/gerenciamento de nomes de domínio, registro em certificados ou configuração de controles de autorização para impor a emissão de certificados, está fora do escopo deste artigo. Consulte a autoridade de registro (RA) do seu serviço de PKI (infraestrutura de chave pública) favorito. Consumidores internos da Microsoft: entre em contato com a segurança do Azure.

## <a name="roles-and-entities-involved-in-certificate-management"></a>Funções e entidades envolvidas no gerenciamento de certificados
A abordagem de segurança em um Cluster Service Fabric é um caso de "o proprietário do cluster o declara, Service Fabric o tempo de execução impõe isso". Por isso, queremos dizer que quase nenhum dos certificados, chaves ou outras credenciais de identidades que participam do funcionamento de um cluster é proveniente do próprio serviço; Eles são todos declarados pelo proprietário do cluster. Além disso, o proprietário do cluster também é responsável por provisionar os certificados no cluster, renová-los conforme necessário e garantir a segurança dos certificados em todos os momentos. Mais especificamente, o proprietário do cluster deve garantir que:
  - os certificados declarados na seção NodeType do manifesto do cluster podem ser encontrados em cada nó desse tipo, de acordo com as [regras de apresentação](cluster-security-certificates.md#presentation-rules)
  - os certificados declarados acima estão instalados, inclusive de suas chaves privadas correspondentes
  - os certificados declarados nas regras de apresentação devem passar as [regras de validação](cluster-security-certificates.md#validation-rules) 

Service Fabric, por sua parte, assume as responsabilidades de:
  - Localizando/localizando certificados que correspondem às declarações na definição de cluster  
  - concedendo acesso às chaves privadas correspondentes a entidades controladas pelo Service Fabric de acordo com uma base ' necessária '
  - validação de certificados de acordo com as práticas recomendadas de segurança estabelecidas e a definição de cluster
  - gerando alertas de expiração iminente de certificados ou falhas para executar as etapas básicas de validação de certificado
  - Validando (em algum grau) que os aspectos relacionados ao certificado da definição de cluster são atendidos pela configuração subjacente dos hosts 

Segue que a carga do gerenciamento de certificados (como operações ativas) se enquadra exclusivamente no proprietário do cluster. Nas seções a seguir, examinaremos mais detalhadamente cada uma das operações de gerenciamento, com mecanismos disponíveis e seu impacto no cluster.

## <a name="the-journey-of-a-certificate"></a>A jornada de um certificado
Vamos revisitar rapidamente a progressão de um certificado de emissão para consumo no contexto de um Cluster Service Fabric:

  1. Um proprietário de domínio registra com a RA de um domínio PKI ou assunto que gostaria de associar a certificados informados; os certificados, por sua vez, constituem a prova de propriedade da forma como domínio ou assunto
  2. O proprietário do domínio também designa na RA as identidades de solicitantes autorizadas – entidades que têm direito a solicitar o registro de certificados com o domínio ou assunto especificado; no Microsoft Azure, o provedor de identidade padrão é Azure Active Directory e os solicitantes autorizados são designados por sua identidade do AAD correspondente (ou por meio de grupos de segurança)
  3. Um solicitante autorizado então se registra em um certificado por meio de um serviço de gerenciamento secreto; no Microsoft Azure, o SMS de escolha é Azure Key Vault (AKV), que armazena com segurança e permite a recuperação de segredos/certificados por entidades autorizadas. AKV também renova/recria novamente o certificado conforme configurado na política de certificado associada. (O AKV usa o AAD como o provedor de identidade.)
  4. Um recuperador autorizado – para o qual vamos nos referir como um ' agente de provisionamento '-recupera o certificado, inclusive sua chave privada, do cofre e instala-o nos computadores que hospedam o cluster
  5. O serviço de Service Fabric (com execução privilegiada em cada nó) concede acesso ao certificado para Service Fabric entidades permitidas; Eles são designados por grupos locais e divididos entre ServiceFabricAdministrators e ServiceFabricAllowedUsers
  6. O tempo de execução do Service Fabric acessa e usa o certificado para estabelecer a Federação ou para autenticar para solicitações de entrada de clientes autorizados
  7. O agente de provisionamento monitora o certificado do cofre e dispara o fluxo de provisionamento na detecção de renovação; subsequentemente, o proprietário do cluster atualiza a definição do cluster, se necessário, para indicar a intenção de sobrepor o certificado.
  8. O agente de provisionamento ou o proprietário do cluster também é responsável por limpar/excluir certificados não utilizados
  
Para nossos propósitos, as duas primeiras etapas na seqüência acima não estão relacionadas em grande parte; a única conexão é que o nome comum da entidade do certificado é o nome DNS declarado na definição de cluster.

Essas etapas estão ilustradas abaixo; Observe as diferenças no provisionamento entre certificados declarados pela impressão digital e pelo nome comum, respectivamente.

*Figura 1.* Fluxo de emissão e provisionamento de certificados declarados pela impressão digital.
![Provisionando certificados declarados pela impressão digital][Image1]

*Fig. 2.* Fluxo de emissão e provisionamento de certificados declarados pelo nome comum da entidade.
![Provisionando certificados declarados pelo nome comum da entidade][Image2]

### <a name="certificate-enrollment"></a> Registro de certificado
Este tópico é abordado em detalhes na [documentação](../key-vault/certificates/create-certificate.md)do Key Vault; Estamos incluindo uma sinopse aqui para continuidade e referência mais fácil. Continuando com o Azure como o contexto e usando Azure Key Vault como o serviço de gerenciamento de segredo, um solicitante de certificado autorizado deve ter pelo menos permissões de gerenciamento de certificados no cofre, concedido pelo proprietário do cofre; o solicitante então se registraria em um certificado da seguinte maneira:
    - Cria uma política de certificado em Azure Key Vault (AKV), que especifica o domínio/assunto do certificado, o emissor desejado, o tipo de chave e o comprimento, o uso de chave pretendido e muito mais; consulte [certificados no Azure Key Vault](../key-vault/certificates/certificate-scenarios.md) para obter detalhes. 
    - Cria um certificado no mesmo cofre com a política especificada acima; isso, por sua vez, gera um par de chaves como objetos de cofre, uma solicitação de assinatura de certificado assinada com a chave privada e que, em seguida, é encaminhado para o emissor designado para assinatura
    - Depois que o emissor (autoridade de certificação) responde com o certificado assinado, o resultado é mesclado no cofre e o certificado está disponível para as seguintes operações:
      - em {vaultUri}/Certificates/{Name}: o certificado incluindo a chave pública e os metadados
      - em {vaultUri}/Keys/{Name}: a chave privada do certificado, disponível para operações de criptografia (encapsular/desencapsular, assinar/verificar)
      - em {vaultUri}/Secrets/{Name}: o certificado inclusivo de sua chave privada, disponível para download como um arquivo PFX ou PEM desprotegido  
    Lembre-se de que um certificado de cofre é, na verdade, uma linha cronológica de instâncias de certificado, compartilhando uma política. As versões do certificado serão criadas de acordo com os atributos de tempo de vida e renovação da política. É altamente recomendável que os certificados do cofre não compartilhem entidades ou domínios/nomes DNS; pode ser interrompido em um cluster para provisionar instâncias de certificado de diferentes certificados de cofre, com assuntos idênticos, mas outros atributos diferentes, como emissor, usos de chave etc.

Neste ponto, existe um certificado no cofre, pronto para consumo. Em diante a:

### <a name="certificate-provisioning"></a>Provisionamento de certificado
Mencionamos um ' agente de provisionamento ', que é a entidade que recupera o certificado, inclusive sua chave privada, do cofre e o instala em cada um dos hosts do cluster. (Lembre-se de que Service Fabric não provisiona certificados.) Em nosso contexto, o cluster será hospedado em uma coleção de VMs do Azure e/ou conjuntos de dimensionamento de máquinas virtuais. No Azure, o provisionamento de um certificado de um cofre para uma VM/VMSS pode ser obtido com os mecanismos a seguir, supondo que, como acima, que o agente de provisionamento tenha recebido permissões ' Get ' anteriormente no cofre pelo proprietário do cofre: 
  - ad-hoc: um operador recupera o certificado do cofre (como PFX/PKCS #12 ou PEM) e o instala em cada nó
  - como um conjunto de dimensionamento de máquinas virtuais ' Secret ' durante a implantação: o serviço de computação recupera, usando sua própria identidade de terceiros em nome do operador, o certificado de um cofre habilitado para implantação de modelo e o instala em cada nó do conjunto de dimensionamento de máquinas virtuais ([assim](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#certificates)); Observe que isso permite o provisionamento apenas de segredos com controle de versão
  - usando a [extensão de VM Key Vault](../virtual-machines/extensions/key-vault-windows.md); Isso permite o provisionamento de certificados usando declarações sem versão, com atualização periódica de certificados observados. Nesse caso, espera-se que a VM/VMSS tenha uma [identidade gerenciada](../virtual-machines/security-policy.md#managed-identities-for-azure-resources), uma identidade que tenha recebido acesso aos cofres que contêm os certificados observados.

O mecanismo ad hoc não é recomendado por vários motivos, desde a segurança até a disponibilidade e não será discutido aqui mais tarde; para obter detalhes, consulte [certificados em conjuntos de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#certificates).

O provisionamento VMSS-/Compute-based apresenta vantagens de segurança e disponibilidade, mas também apresenta restrições. Ele requer-por design-declarando certificados como segredos com controle de versão, o que o torna adequado somente para clusters protegidos com certificados declarados pela impressão digital. Por outro lado, o provisionamento baseado na extensão de VM Key Vault sempre instalará a versão mais recente de cada certificado observado, o que o torna adequado somente para clusters protegidos com certificados declarados pelo nome comum da entidade. Para enfatizar, não use um mecanismo de provisionamento de atualização AutoAtualizar (como a extensão KVVM) para certificados declarados pela instância (ou seja, por impressão digital)-o risco de perder a disponibilidade é considerável.

Outros mecanismos de provisionamento podem existir; os itens acima são aceitos no momento para clusters do Azure Service Fabric.

### <a name="certificate-consumption-and-monitoring"></a>Consumo e monitoramento de certificado
Como mencionado anteriormente, o tempo de execução de Service Fabric é responsável por localizar e usar os certificados declarados na definição de cluster. O artigo sobre a [autenticação baseada em certificado](cluster-security-certificates.md) explicou detalhadamente como Service Fabric implementa as regras de apresentação e de validação, respectivamente, e não serão revisitadas aqui. Vamos examinar o acesso e a concessão de permissão, bem como o monitoramento.

Lembre-se de que os certificados em Service Fabric são usados para uma infinidade de finalidades, desde a autenticação mútua na camada de Federação até a autenticação TLS para os pontos de extremidade de gerenciamento; Isso requer que vários componentes ou serviços do sistema tenham acesso à chave privada do certificado. O tempo de execução de Service Fabric verifica regularmente o repositório de certificados, procurando por correspondências para cada uma das regras de apresentação conhecidas; para cada um dos certificados correspondentes, a chave privada correspondente está localizada e sua lista de controle de acesso condicional é atualizada para incluir permissões – normalmente, leitura e execução, concedidas à respectiva identidade que os exige. (Esse processo é chamado informalmente de ' atuação '.) O processo é executado em uma cadência de 1 minuto e também abrange os certificados de ' aplicativo ', como aqueles usados para criptografar configurações ou como certificados de ponto de extremidade. O atuação segue as regras de apresentação, portanto, é importante ter em mente que os certificados declarados pela impressão digital e que são atualizados com atualizações automáticas sem a atualização de configuração de cluster não estarão acessíveis.    

### <a name="certificate-rotation"></a>Rotação de certificado
Como observação adicional: o IETF [RFC 3647](https://tools.ietf.org/html/rfc3647) define formalmente a [renovação](https://tools.ietf.org/html/rfc3647#section-4.4.6) como a emissão de um certificado com os mesmos atributos que o certificado que está sendo substituído – o emissor, a chave pública da entidade e as informações são preservadas e a [nova criação](https://tools.ietf.org/html/rfc3647#section-4.4.7) de chaves como a emissão de um certificado com um novo par de chaves e nenhuma restrição sobre se o emissor pode ou não ser alterado. Considerando que a distinção pode ser importante (considere o caso de certificados declarados pelo nome comum da entidade com fixação do emissor), vamos optar pelo termo neutro ' Rotation ' para cobrir ambos os cenários. (Tenha em mente que, quando usado informalmente, ' renovar ' se refere, na verdade, à nova criação de chaves.) 

Vimos anteriormente que Azure Key Vault dá suporte à rotação automática de certificado: a política de certificado associado define o ponto no tempo, seja por dias antes da expiração ou da porcentagem do tempo de vida total, quando o certificado é girado no cofre. O agente de provisionamento deve ser invocado após esse ponto no tempo e antes da expiração do certificado agora anterior, para distribuir esse novo certificado para todos os nós do cluster. Service Fabric ajudará a gerar avisos de integridade quando a data de expiração de um certificado (e que está em uso no momento no cluster) ocorrer antes de um intervalo predeterminado. Um agente de provisionamento automático (ou seja, a extensão de VM do keyvault), configurado para observar o certificado do cofre, fará o polling periódico do cofre, detectará a rotação e recuperará e instalará o novo certificado. O provisionamento feito por meio do recurso "segredos" da VM/VMSS exigirá que um operador autorizado atualize a VM/VMSS com o URI do keyvault com versão correspondente ao novo certificado.

Em ambos os casos, o certificado girado agora é provisionado para todos os nós e descrevemos o mecanismo Service Fabric empregar para detectar rotações; Vamos examinar o que acontece em seguida, supondo que a rotação seja aplicada ao certificado de cluster declarado pelo nome comum do assunto
  - para novas conexões dentro, bem como no cluster, o tempo de execução de Service Fabric localizará e selecionará o certificado de correspondência emitido mais recentemente (maior valor da propriedade ' nobefore '). Observe que essa é uma alteração de versões anteriores do tempo de execução de Service Fabric.
  - as conexões existentes serão mantidas ativas/permitidas para expirar naturalmente ou terminar de outra forma; um manipulador interno terá sido notificado de que existe uma nova correspondência

> [!NOTE] 
> Antes da versão 7.2.445 (7,2 CU4), Service Fabric selecionou o certificado de expiração mais distante (o certificado com a propriedade ' não após ' mais distante)

Isso se traduz nas seguintes observações importantes:
  - O certificado de renovação poderá ser ignorado se a data de validade for mais cedo do que o certificado atualmente em uso.
  - A disponibilidade do cluster, ou dos aplicativos hospedados, tem precedência sobre a diretiva para girar o certificado; o cluster convergirá no novo certificado eventualmente, mas sem garantias de tempo. Isso é o seguinte:
  - Pode não ser imediatamente óbvio para um observador que o certificado girado substituiu completamente seu antecessor; a única maneira de garantir que seja (para certificados de cluster) Reinicialize os computadores host. Observe que não é suficiente reiniciar os nós de Service Fabric, pois os componentes de modo kernel que formam conexões de concessão em um cluster não serão afetados. Observe também que a reinicialização da VM/VMSS pode causar perda temporária de disponibilidade. (Para certificados de aplicativo, é suficiente reiniciar apenas as respectivas instâncias de aplicativo.)
  - A introdução de um certificado rechaveado que não atenda às regras de validação pode efetivamente interromper o cluster. O exemplo mais comum disso é o caso de um emissor inesperado: os certificados de cluster são declarados pelo nome comum da entidade com fixação do emissor, mas o certificado girado foi emitido por um emissor novo/não declarado.     

### <a name="certificate-cleanup"></a>Limpeza de certificado
Neste momento, não há provisões no Azure para remoção explícita de certificados. Geralmente, é uma tarefa não trivial determinar se um determinado certificado está sendo usado em um determinado momento. Isso é mais difícil para certificados de aplicativo do que para certificados de cluster. Service Fabric em si, não sendo o agente de provisionamento, o não excluirá um certificado declarado pelo usuário em qualquer circunstância. Para os mecanismos de provisionamento padrão:
  - Os certificados declarados como segredos de VM/VMSS serão provisionados desde que sejam referenciados na definição de VM/VMSS e poderão ser recuperados do cofre (a exclusão de um segredo/certificado de cofre falhará em implantações de VM/VMSS subsequentes; da mesma forma, desabilitar uma versão secreta no cofre também falhará em implantações de VM/VMSS
  - As versões anteriores dos certificados provisionados por meio da extensão de VM do keyvault podem ou não estar presentes em um nó VM/VMSS. O agente somente recupera e instala a versão atual e não remove nenhum certificado. A recriação de imagem de um nó (que normalmente ocorre todos os meses) redefinirá o repositório de certificados para o conteúdo da imagem do sistema operacional e, portanto, as versões anteriores serão removidas implicitamente. No entanto, considere que escalar verticalmente um conjunto de dimensionamento de máquinas virtuais resultará somente na versão atual dos certificados observados que estão sendo instalados; Não assuma a homogeneidade dos nós em relação aos certificados instalados.   

## <a name="simplifying-management---an-autorollover-example"></a>Simplificando o gerenciamento – um exemplo de sobreposição
Descrevemos mecanismos, restrições, regras e definições complexas e tornamos previsões Dires de interrupções. É, talvez, tempo para mostrar como configurar o gerenciamento automático de certificados para evitar todas essas preocupações. Estamos fazendo isso no contexto de um cluster de Service Fabric do Azure em execução em um conjunto de dimensionamento de máquinas virtuais PaaSv2, usando Azure Key Vault para gerenciamento de segredos e aproveitando identidades gerenciadas, da seguinte maneira:
  - A validação de certificados é alterada de fixação de impressão digital para entidade + fixação do emissor: qualquer certificado com um determinado assunto de um determinado emissor é igualmente confiável
    - Os certificados são registrados no e obtidos de um repositório confiável (Key Vault) e atualizados por um agente – nesse caso, a extensão de VM do keyvault
    - O provisionamento de certificados é alterado de tempo de implantação e baseado em versão (como feito pelo ComputeRP) para pós-implantação e usando URIs de keyvault sem versão
    - O acesso ao keyvault é concedido por meio de identidades gerenciadas atribuídas pelo usuário; a identidade UA é criada e atribuída ao conjunto de dimensionamento de máquinas virtuais durante a implantação
    - Após a implantação, o agente (a extensão de VM KV) sondará e atualizará os certificados observados em cada nó do conjunto de dimensionamento de máquinas virtuais; a rotação de certificado é, portanto, totalmente automatizada, pois ela selecionará automaticamente o certificado válido mais distante

A sequência é totalmente programável por script/automatizada e permite uma implantação inicial sem intervenção do usuário de um cluster configurado para substituição automática de certificado. As etapas detalhadas são fornecidas abaixo. Usaremos uma mistura de cmdlets do PowerShell e fragmentos de modelos JSON. A mesma funcionalidade é atingível com todos os meios com suporte de interação com o Azure.

> [!NOTE]
> Este exemplo supõe que já exista um certificado no cofre; registrar e renovar um certificado gerenciado por keyvault requer etapas manuais de pré-requisito, conforme descrito anteriormente neste artigo. Para ambientes de produção, use certificados gerenciados por keyvault – um script de exemplo específico para uma PKI interna da Microsoft está incluído abaixo.

> [!NOTE]
> A autosubstituição de certificado faz sentido apenas para certificados emitidos por autoridades de certificação; o uso de certificados autoassinados, incluindo aqueles gerados ao implantar um cluster de Service Fabric no portal do Azure, é não-atestado, mas ainda é possível para implantações locais/hospedadas pelo desenvolvedor, declarando que a impressão digital do emissor seja a mesma do certificado de folha.

### <a name="starting-point"></a>Ponto de partida
Para resumir, vamos pressupor o seguinte estado de inicialização:
  - O Cluster Service Fabric existe e é protegido com um certificado emitido por uma autoridade de certificação declarado pela impressão digital.
  - O certificado é armazenado em um cofre e provisionado como um segredo do conjunto de dimensionamento de máquinas virtuais
  - O mesmo certificado será usado para converter o cluster para declarações de certificado com base em nome comum e, portanto, pode ser validado por assunto e emissor; Se esse não for o caso, obtenha o certificado emitido pela autoridade de certificação para essa finalidade e adicione-o à definição de cluster por impressão digital, conforme explicado [aqui](service-fabric-cluster-security-update-certs-azure.md)

Aqui está um trecho JSON de um modelo correspondente a tal estado-Observe que omite muitas configurações necessárias e ilustra apenas os aspectos relacionados ao certificado:
```json
  "resources": [
    {   ## VMSS definition
      "apiVersion": "[variables('vmssApiVersion')]",
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('vmNodeTypeName')]",
      "location": "[variables('computeLocation')]",
      "properties": {
        "virtualMachineProfile": {
          "extensionProfile": {
            "extensions": [
            {
                "name": "[concat('ServiceFabricNodeVmExt','_vmNodeTypeName')]",
                "properties": {
                  "type": "ServiceFabricNode",
                  "autoUpgradeMinorVersion": true,
                  "publisher": "Microsoft.Azure.ServiceFabric",
                  "settings": {
                    "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                    "nodeTypeRef": "[variables('vmNodeTypeName')]",
                    "dataPath": "D:\\SvcFab",
                    "durabilityLevel": "Bronze",
                    "certificate": {
                        "thumbprint": "[parameters('primaryClusterCertificateTP')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                    }
                  },
                  "typeHandlerVersion": "1.1"
                }
            },}},
          "osProfile": {
            "adminPassword": "[parameters('adminPassword')]",
            "adminUsername": "[parameters('adminUsername')]",
            "secrets": [
            {
                "sourceVault": {
                    "id": "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
                },
                "vaultCertificates": [
                {
                    "certificateStore": "[parameters('certificateStoreValue')]",
                    "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
                },
            ]}]
        },
    },
    {   ## cluster definition
        "apiVersion": "[variables('sfrpApiVersion')]",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "certificate": {
            "thumbprint": "[parameters('primaryClusterCertificateTP')]",
            "x509StoreName": "[parameters('certificateStoreValue')]"
        },
    }
  ]
```   

Basicamente, a parte superior diz que o certificado com impressão digital ```json [parameters('primaryClusterCertificateTP')] ``` e encontrado no URI do keyvault ```json [parameters('clusterCertificateUrlValue')] ``` é declarado como o certificado exclusivo do cluster, por impressão digital. Em seguida, vamos configurar os recursos adicionais necessários para garantir a substituição da autoposição do certificado.

### <a name="setting-up-prerequisite-resources"></a>Configurando recursos de pré-requisito
Como mencionado anteriormente, um certificado provisionado como um segredo do conjunto de dimensionamento de máquinas virtuais é recuperado do cofre pelo serviço do provedor de recursos Microsoft. Compute, usando sua identidade de primeira empresa e em nome do operador de implantação. Para substituição de autoposição, que mudará – iremos mudar para o uso de uma identidade gerenciada, atribuída ao conjunto de dimensionamento de máquinas virtuais e que recebe permissões para os segredos do cofre.

Todos os trechos subsequentes devem ser implantados concomitantly-eles são listados individualmente para análise e explicações de reprodução por reprodução.

Primeiro, defina uma identidade atribuída pelo usuário (os valores padrão são incluídos como exemplos) – consulte a [documentação oficial](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity) para obter informações atualizadas:
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "userAssignedIdentityName": {
      "type": "string",
      "defaultValue": "sftstuaicus",
      "metadata": {
        "description": "User-assigned managed identity name"
      }
    },
  },
  "variables": {
      "vmssApiVersion": "2018-06-01",
      "sfrpApiVersion": "2018-02-01",
      "miApiVersion": "2018-11-30",
      "kvApiVersion": "2018-02-14",
      "userAssignedIdentityResourceId": "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('userAssignedIdentityName'))]"
  },    
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('userAssignedIdentityName')]",
      "apiVersion": "[variables('miApiVersion')]",
      "location": "[resourceGroup().location]"
    },
  ]}
```

Em seguida, conceda a essa identidade acesso aos segredos do cofre – consulte a [documentação oficial](/rest/api/keyvault/vaults/updateaccesspolicy) para obter informações atuais:
```json
  "resources":
  [{
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "[variables('kvApiVersion')]",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[reference(variables('userAssignedIdentityResourceId'), variables('miApiVersion')).tenantId]",
            "objectId": "[reference(variables('userAssignedIdentityResourceId'), variables('miApiVersion')).principalId]",
            "dependsOn": [
              "[variables('userAssignedIdentityResourceId')]"
            ],
            "permissions": {
              "secrets": [
                "get",
                "list"
              ]}}]}}]
```

Na próxima etapa, vamos:
  - atribuir a identidade atribuída pelo usuário ao conjunto de dimensionamento de máquinas virtuais
  - declarar a dependência do conjunto de dimensionamento de máquinas virtuais na criação da identidade gerenciada e no resultado da concessão de acesso ao cofre
  - declare a extensão de VM do keyvault, exigindo que ela recupere os certificados observados na inicialização ([documentação oficial](../virtual-machines/extensions/key-vault-windows.md))
  - Atualize a definição do Service Fabric extensão de VM para depender da extensão KVVM e converta o certificado do cluster para o nome comum (estamos fazendo essas alterações em uma única etapa, já que elas se enquadram no escopo do mesmo recurso).

```json
  "parameters": {
    "kvvmextPollingInterval": {
      "type": "string",
      "defaultValue": "3600",
      "metadata": {
        "description": "kv vm extension polling interval in seconds"
      }
    },
    "kvvmextLocalStoreName": {
      "type": "string",
      "defaultValue": "MY",
      "metadata": {
        "description": "kv vm extension local store name"
      }
    },
    "kvvmextLocalStoreLocation": {
      "type": "string",
      "defaultValue": "LocalMachine",
      "metadata": {
        "description": "kv vm extension local store location"
      }
    },
    "kvvmextObservedCertificates": {
      "type": "array",
      "defaultValue": [
                "https://sftestcus.vault.azure.net/secrets/sftstcncluster",
                "https://sftestcus.vault.azure.net/secrets/sftstcnserver"
            ],
      "metadata": {
        "description": "kv vm extension observed certificates versionless uri"
      }
    },
    "certificateCommonName": {
      "type": "string",
      "defaultValue": "cus.cluster.sftstcn.system.servicefabric.azure-int",
      "metadata": {
        "description": "Certificate Common name"
      }
    },
  },
  "resources": [
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[variables('vmNodeTypeName')]",
    "location": "[variables('computeLocation')]",
    "dependsOn": [
      "[variables('userAssignedIdentityResourceId')]",
      "[concat('Microsoft.KeyVault/vaults/', concat(parameters('keyVaultName'), '/accessPolicies/add'))]"
    ],
    "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
        "[variables('userAssignedIdentityResourceId')]": {}
      }
    },
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
        {
          "name": "KVVMExtension",
          "properties": {
            "publisher": "Microsoft.Azure.KeyVault",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": "[parameters('kvvmextPollingInterval')]",
                    "linkOnRenewal": false,
                    "observedCertificates": "[parameters('kvvmextObservedCertificates')]",
                    "requireInitialSync": true
                }
            }
          }
        },
        {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeTypeName')]",
        "properties": {
          "type": "ServiceFabricNode",
          "provisionAfterExtensions" : [ "KVVMExtension" ],
          "publisher": "Microsoft.Azure.ServiceFabric",
          "settings": {
            "certificate": {
                "commonNames": [
                    "[parameters('certificateCommonName')]"
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            }
            },
            "typeHandlerVersion": "1.0"
          }
        },
  ] } ## extension profile
  },  ## VM profile
  "osProfile": {
    "adminPassword": "[parameters('adminPassword')]",
    "adminUsername": "[parameters('adminUsername')]",
  } 
  }
  ]
```
Observe, embora não explicitamente listado acima, que removemos a URL do certificado do cofre da seção ' OsProfile ' do conjunto de dimensionamento de máquinas virtuais.
A última etapa é atualizar a definição de cluster para alterar a declaração de certificado de impressão digital para nome comum – aqui, também estamos fixando as impressões digitais do emissor:

```json
  "parameters": {
    "certificateCommonName": {
      "type": "string",
      "defaultValue": "cus.cluster.sftstcn.system.servicefabric.azure-int",
      "metadata": {
        "description": "Certificate Common name"
      }
    },
    "certificateIssuerThumbprint": {
      "type": "string",
      "defaultValue": "1b45ec255e0668375043ed5fe78a09ff1655844d,d7fe717b5ff3593764f4d90654d86e8362ec26c8,3ac7c3cac8de0dd392c02789c8be97474f456960,96ea05926e2e42cc207e358668be2c316857fb5e",
      "metadata": {
        "description": "Certificate issuer thumbprints separated by comma"
      }
    },
  },
  "resources": [
    {
      "apiVersion": "[variables('sfrpApiVersion')]",
      "type": "Microsoft.ServiceFabric/clusters",
      "name": "[parameters('clusterName')]",
      "location": "[parameters('clusterLocation')]",
      "properties": {
        "certificateCommonNames": {
          "commonNames": [{
              "certificateCommonName": "[parameters('certificateCommonName')]",
              "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
          }],
          "x509StoreName": "[parameters('certificateStoreValue')]"
        },
  ]
```

Neste ponto, você pode executar as atualizações mencionadas acima em uma única implantação; para sua parte, o serviço do provedor de recursos de Service Fabric dividirá a atualização do cluster em várias etapas, conforme descrito no segmento sobre [como converter certificados de cluster de impressão digital em nome comum](cluster-security-certificates.md#converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations).

### <a name="analysis-and-observations"></a>Análise e observações
Esta seção é uma questão para explicar as etapas detalhadas acima, bem como a atenção de desenho para aspectos importantes.

#### <a name="certificate-provisioning-explained"></a>Provisionamento de certificado, explicado
A extensão KVVM, como um agente de provisionamento, é executada continuamente em uma frequência predeterminada. Ao falhar ao recuperar um certificado observado, ele continuaria no próximo linha e, em seguida, hibernaria até o próximo ciclo. A extensão SFVM, como o agente de inicialização do cluster, exigirá os certificados declarados antes que o cluster possa formar. Isso, por sua vez, significa que a extensão SFVM só pode ser executada após a recuperação bem-sucedida dos certificados do cluster, indicada aqui pela ```json "provisionAfterExtensions" : [ "KVVMExtension" ]"``` cláusula e pela configuração da extensão KeyVaultVM ```json "requireInitialSync": true``` . Isso indica para a extensão KVVM que na primeira execução (após a implantação ou uma reinicialização) ele deve percorrer seus certificados observados até que todos sejam baixados com êxito. Definir esse parâmetro como false, junto com uma falha ao recuperar os certificados de cluster, resultaria em uma falha da implantação do cluster. Por outro lado, exigir uma sincronização inicial com uma lista incorreta/inválida de certificados observados resultaria em uma falha da extensão KVVM e, assim, novamente, uma falha na implantação do cluster.  

#### <a name="certificate-linking-explained"></a>Vinculação de certificado, explicada
Talvez você tenha notado o sinalizador ' linkOnRenewal ' da extensão KVVM e o fato de que ele está definido como false. Estamos abordando aqui detalhadamente o comportamento controlado por esse sinalizador e suas implicações no funcionamento de um cluster. Observe que esse comportamento é específico do Windows.

De acordo com sua [definição](../virtual-machines/extensions/key-vault-windows.md#extension-schema):
```json
"linkOnRenewal": <Only Windows. This feature enables auto-rotation of SSL certificates, without necessitating a re-deployment or binding.  e.g.: false>,
```

Os certificados usados para estabelecer uma conexão TLS normalmente são [adquiridos como um identificador](/windows/win32/api/sspi/nf-sspi-acquirecredentialshandlea) por meio do provedor de suporte de segurança S-Channel – ou seja, o cliente não acessa diretamente a chave privada do próprio certificado. O S-Channel dá suporte ao redirecionamento (vinculação) de credenciais na forma de uma extensão de certificado ([CERT_RENEWAL_PROP_ID](/windows/win32/api/wincrypt/nf-wincrypt-certsetcertificatecontextproperty#cert_renewal_prop_id)): se essa propriedade for definida, seu valor representará a impressão digital do certificado de ' renovação ' e, portanto, S-Channel tentará carregar o certificado vinculado. Na verdade, ele percorrerá essa lista vinculada (e, felizmente, acíclico) até que ela termine com o certificado "final" – um sem uma marca de renovação. Esse recurso, quando usado criteriosamente, é uma grande mitigação contra perda de disponibilidade causada por certificados expirados (por exemplo). Em outros casos, pode ser a causa de interrupções que são difíceis de diagnosticar e mitigar. O S-Channel executa a passagem de certificados em suas propriedades de renovação incondicionalmente, independentemente do assunto, dos emissores ou de quaisquer outros atributos específicos que participem da validação do certificado resultante pelo cliente. É possível, de fato, que o certificado resultante não tenha uma chave privada associada ou que a chave não tenha sido ACLedda para o consumidor potencial. 
 
Se a vinculação estiver habilitada, a extensão de VM do keyvault, após a recuperação de um certificado observado do cofre, tentará encontrar certificados correspondentes existentes para vinculá-los por meio da propriedade de extensão de renovação. A correspondência é (exclusivamente) com base no nome alternativo da entidade (SAN) e funciona como exemplificado abaixo.
Suponha que dois certificados existentes, como segue: A: CN = "acessórios de Alice", SAN = {"alice.universalexports.com"}, renovar = ' ' B: CN = "bits de Bob", SAN = {"bob.universalexports.com", "bob.universalexports.net"}, renovar = ' '
 
Suponha que um certificado C seja recuperado pelo KVVM ext: CN = "malware de Mallory", SAN = {"alice.universalexports.com", "bob.universalexports.com", "mallory.universalexports.com"}
 
A lista de SAN é totalmente incluída em C e assim por diante. renovar = C. impressão digital; A lista de SAN de B tem uma interseção comum com C, mas não está totalmente incluída nela, portanto, B. a renovação permanece vazia.
 
Qualquer tentativa de invocar falha AcquireCredentialsHandle (S-Channel) nesse estado no certificado A acabará enviando C para a parte remota. No caso de Service Fabric, o [subsistema de transporte](service-fabric-architecture.md#transport-subsystem) de um cluster usa o S-Channel para autenticação mútua e, portanto, o comportamento descrito acima afeta a comunicação fundamental do cluster diretamente. Continuando o exemplo acima e supondo que um é o certificado do cluster, o que acontece em seguida depende:
  - se a chave privada de C não for ACLd à conta em que a malha está sendo executada, veremos falhas para adquirir a chave privada (SEC_E_UNKNOWN_CREDENTIALS ou semelhante)
  - se a chave privada de C estiver acessível, veremos as falhas de autorização retornadas pelos outros nós (CertificateNotMatched, não autorizado, etc.) 
 
Em ambos os casos, o transporte falha e o cluster pode ficar inativo; os sintomas variam. Para piorar as coisas, a vinculação depende da ordem de renovação – que é ditada pela ordem da lista de certificados observados da extensão KVVM, o agendamento de renovação no cofre ou até mesmo erros transitórios que alterariam a ordem de recuperação.

Para mitigar esses incidentes, recomendamos:
  - Não misture as SANs de diferentes certificados de cofre; cada certificado de cofre deve atender a uma finalidade distinta e seu assunto e SAN devem refletir isso com a especificidade
  - inclua o nome comum da entidade na lista SAN (como, literalmente, "CN = <subject common name> ")  
  - Se não tiver certeza, desabilite a vinculação na renovação de certificados provisionados com a extensão KVVM 

#### <a name="why-use-a-user-assigned-managed-identity-what-are-the-implications-of-using-it"></a>Por que usar uma identidade gerenciada atribuída pelo usuário? Quais são as implicações de usá-lo?
Como surgiu dos trechos de JSON acima, um sequenciamento específico das operações/atualizações é necessário para garantir o sucesso da conversão e para manter a disponibilidade do cluster. Especificamente, o recurso do conjunto de dimensionamento de máquinas virtuais declara e usa sua identidade para recuperar segredos em uma única atualização (da perspectiva do usuário). A extensão de VM Service Fabric (que inicializa o cluster) depende da conclusão da extensão de VM do keyvault, que depende da recuperação bem-sucedida de certificados observados. A extensão KVVM usa a identidade do conjunto de dimensionamento de máquinas virtuais para acessar o cofre, o que significa que a política de acesso no cofre já deve ter sido atualizada antes da implantação do conjunto de dimensionamento de máquinas virtuais. 

Para descartar a criação de uma identidade gerenciada ou atribuí-la a outro recurso, o operador de implantação deve ter a função necessária (ManagedIdentityOperator) na assinatura ou no grupo de recursos, além das funções necessárias para gerenciar os outros recursos referenciados no modelo. 

Do ponto de vista da segurança, lembre-se de que a máquina virtual (conjunto de dimensionamento) é considerada um limite de segurança com relação à sua identidade do Azure. Isso significa que qualquer aplicativo hospedado na VM poderia, em princípio, obter um token de acesso que represente os tokens de acesso de identidade gerenciado por VM são obtidos do ponto de extremidade IMDS não autenticado. Se você considerar que a VM é um ambiente compartilhado ou de vários locatários, talvez esse método de recuperação de certificados de cluster não seja indicado. No entanto, é o único mecanismo de provisionamento adequado para autosubstituição de certificado.

## <a name="troubleshooting-and-frequently-asked-questions"></a>Solução de problemas e perguntas frequentes

*P*: como se registrar de forma programática em um certificado gerenciado por keyvault?
*R*: Descubra o nome do emissor na documentação do keyvault e, em seguida, substitua-o no script abaixo.  
```PowerShell
  $issuerName=<depends on your PKI of choice>
    $clusterVault="sftestcus"
    $clusterCertVaultName="sftstcncluster"
    $clusterCertCN="cus.cluster.sftstcn.system.servicefabric.azure-int"
    Set-AzKeyVaultCertificateIssuer -VaultName $clusterVault -Name $issuerName -IssuerProvider $issuerName
    $distinguishedName="CN=" + $clusterCertCN
    $policy = New-AzKeyVaultCertificatePolicy `
        -IssuerName $issuerName `
        -SubjectName $distinguishedName `
        -SecretContentType 'application/x-pkcs12' `
        -Ekus "1.3.6.1.5.5.7.3.1", "1.3.6.1.5.5.7.3.2" `
        -ValidityInMonths 4 `
        -KeyType 'RSA' `
        -RenewAtPercentageLifetime 75        
    Add-AzKeyVaultCertificate -VaultName $clusterVault -Name $clusterCertVaultName -CertificatePolicy $policy
    
    # poll the result of the enrollment
    Get-AzKeyVaultCertificateOperation -VaultName $clusterVault -Name $clusterCertVaultName 
```

*P*: o que acontece quando um certificado é emitido por um emissor não declarado/não especificado? Onde posso obter a lista completa de emissores ativos de uma determinada PKI?
*R*: se a declaração de certificado especificar impressões digitais do emissor e o emissor direto do certificado não estiver incluído na lista de emissores fixados, o certificado será considerado inválido – independentemente de sua raiz ser confiável ou não. Portanto, é essencial garantir que a lista de emissores esteja atualizada. A introdução de um novo emissor é um evento raro e deve ser amplamente divulgada antes que ele comece a emitir certificados. 

Em geral, uma PKI publicará e manterá uma instrução de prática de certificação, de acordo com a [RFC 7382](https://tools.ietf.org/html/rfc7382)da IETF. Entre outras informações, incluirá todos os emissores ativos. Recuperar essa lista programaticamente pode ser diferente de uma PKI para outra.   

Para a Microsoft-Internal PKIs, consulte a documentação interna sobre os pontos de extremidade/SDKs usados para recuperar os emissores autorizados; é responsabilidade do proprietário do cluster investigar essa lista periodicamente e garantir que sua definição de cluster inclua *todos os* emissores esperados.

*P*: há suporte para várias PKIs? 
*R*: Sim; Você não pode declarar várias entradas de CN no manifesto do cluster com o mesmo valor, mas pode listar os emissores de várias PKIs correspondentes ao mesmo CN. Não é uma prática recomendada, e as práticas de transparência de certificado podem impedir que esses certificados sejam emitidos. No entanto, como significa migrar de uma PKI para outra, esse é um mecanismo aceitável.

*P*: e se o certificado de cluster atual não for emitido pela autoridade de certificação ou não tiver o assunto pretendido? 
*R*: Obtenha um certificado com o assunto pretendido e adicione-o à definição do cluster como um secundário, por impressão digital. Depois que a atualização for concluída com êxito, inicie outra atualização de configuração de cluster para converter a declaração de certificado em nome comum. 

[Image1]:./media/security-cluster-certificate-mgmt/certificate-journey-thumbprint.png
[Image2]:./media/security-cluster-certificate-mgmt/certificate-journey-common-name.png
