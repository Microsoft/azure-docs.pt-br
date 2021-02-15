---
title: Autenticação baseada em certificado X. 509 em um Cluster Service Fabric
description: Saiba mais sobre a autenticação baseada em certificado em clusters Service Fabric e como detectar, atenuar e corrigir problemas relacionados a certificados.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 8af0246e0e576f9877c4c5e3b1f1a4314ae29827
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901242"
---
# <a name="x509-certificate-based-authentication-in-service-fabric-clusters"></a>Autenticação baseada em certificado X. 509 em clusters Service Fabric

Este artigo complementa a introdução à [segurança de cluster Service Fabric](service-fabric-cluster-security.md)e entra em detalhes sobre a autenticação baseada em certificado em clusters Service Fabric. Supomos que o leitor esteja familiarizado com os conceitos fundamentais de segurança e também com os controles que Service Fabric expões para controlar a segurança de um cluster.  

Tópicos abordados sob este título:

* Noções básicas sobre autenticação baseada em certificado
* Identidades e suas respectivas funções
* Regras de configuração de certificado
* Solução de problemas e perguntas frequentes

## <a name="certificate-based-authentication-basics"></a>Noções básicas sobre autenticação baseada em certificado
Como um novo atualizador, em segurança, um certificado é um instrumento destinado a associar informações relacionadas a uma entidade (o assunto) à sua posse de um par de chaves de criptografia assimétricas e, portanto, constitui uma construção principal da criptografia de chave pública. As chaves representadas por um certificado podem ser usadas para proteger dados ou para provar a identidade dos detentores de chave; Quando usado em conjunto com um sistema de PKI (infraestrutura de chave pública), um certificado pode representar características adicionais de seu assunto, como a propriedade de um domínio de Internet ou certos privilégios concedidos a ele pelo emissor do certificado (conhecido como autoridade de certificação ou CA). Um aplicativo comum de certificados é compatível com o protocolo criptográfico TLS, permitindo comunicações seguras em uma rede de computadores. Especificamente, o cliente e o servidor usam certificados para garantir a privacidade e a integridade de sua comunicação, além de conduzir a autenticação mútua.

No Service Fabric, a camada fundamental de um cluster (Federação) também se baseia no TLS (entre outros protocolos) para obter uma rede confiável e segura de nós participantes. As conexões com o cluster via Service Fabric APIs de cliente usam o TLS também para proteger o tráfego e também para estabelecer as identidades das partes. Especificamente, quando usado para autenticação no Service Fabric, um certificado pode ser usado para provar as seguintes declarações: a) o apresentador da credencial do certificado tem posse da chave privada b do certificado. o hash SHA-1 do certificado (' impressão digital ') corresponde a uma declaração incluída na definição do cluster, ou c) o nome comum da entidade diferenciada do certificado corresponde a uma declaração incluída na e o emissor do certificado é conhecido ou confiável.

Na lista acima, ' b ' é coloquialmente conhecido como ' fixação de impressão digital '; Nesse caso, a declaração refere-se a um certificado específico e a força do esquema de autenticação se posiciona na premissa de que é computacionalmente impossível para forjar um certificado que produz o mesmo valor de hash que outro, enquanto ainda é um objeto válido e bem formado em todos os outros aspectos. O item ' C' representa uma forma alternativa de declarar um certificado, em que a força do esquema se posiciona na combinação do assunto do certificado e da autoridade emissora. Nesse caso, a declaração refere-se a uma classe de certificados-quaisquer dois certificados com as mesmas características são considerados totalmente equivalentes. 

As seções a seguir explicam detalhadamente como o tempo de execução do Service Fabric usa e valida certificados para garantir a segurança do cluster.

## <a name="identities-and-their-respective-roles"></a>Identidades e suas respectivas funções
Antes de mergulhar nos detalhes da autenticação ou da proteção dos canais de comunicação, é importante listar os atores participantes e as funções correspondentes que eles desempenham em um cluster:
- o tempo de execução de Service Fabric, chamado de "sistema": o conjunto de serviços que fornecem as abstrações e funcionalidades que representam o cluster. Ao se referir à comunicação no cluster entre instâncias do sistema, usaremos o termo ' identidade do cluster '; ao fazer referência ao cluster como o destinatário/destino do tráfego de fora do cluster, usaremos o termo ' identidade do servidor '.
- aplicativos hospedados, chamados de "aplicativos": código fornecido pelo proprietário do cluster, que é orquestrado e executado no cluster
- clientes: entidades com permissão para se conectar e executar a funcionalidade em um cluster, de acordo com a configuração do cluster. Nós nos distinguemos entre dois níveis de privilégios-' user ' e ' admin ', respectivamente. Um cliente de ' usuário ' é restrito principalmente a operações somente leitura (mas não a todas as funcionalidades somente leitura), enquanto um ' cliente admin ' tem acesso irrestrito à funcionalidade do cluster. (Para obter mais detalhes, consulte [funções de segurança em um cluster Service Fabric](service-fabric-cluster-security-roles.md).)
- (Somente Azure) os serviços de Service Fabric que orquestram e expõem controles para operação e gerenciamento de clusters de Service Fabric, chamados simplesmente de ' serviço '. Dependendo do ambiente, o ' serviço ' pode se referir ao provedor de recursos Service Fabric do Azure ou a outros provedores de recursos de propriedade e operados pela equipe de Service Fabric.

Em um cluster seguro, cada uma dessas funções pode ser configurada com sua própria identidade distinta, declarada como o emparelhamento de um nome de função predefinido e sua credencial correspondente. Service Fabric dá suporte à declaração de credenciais como certificados ou entidade de serviço baseada em domínio. (As identidades do Windows-/Kerberos-based também têm suporte, mas estão além do escopo deste artigo; consulte [segurança baseada no Windows em clusters Service Fabric](service-fabric-windows-cluster-windows-security.md).) Em clusters do Azure, as funções de cliente também podem ser declaradas como [identidades baseadas em Azure Active Directory](service-fabric-cluster-creation-setup-aad.md).

Como alusão acima, o tempo de execução de Service Fabric define dois níveis de privilégio em um cluster: ' admin ' e ' user '. Um cliente administrador e um componente "sistema" operariam com os privilégios "admin" e, portanto, não são diferenciais uns dos outros. Ao estabelecer uma conexão no/com o cluster, um chamador autenticado será concedido pelo Service Fabric Runtime uma das duas funções como base para a autorização subsequente. Examinaremos a autenticação em detalhes nas seções a seguir.

## <a name="certificate-configuration-rules"></a>Regras de configuração de certificado
### <a name="validation-rules"></a>Regras de validação
As configurações de segurança de um Cluster Service Fabric descrevem, em princípio, os seguintes aspectos:
- o tipo de autenticação; Essa é uma característica de tempo de criação e imutável do cluster. Exemplos dessas configurações são ' ClusterCredentialType ', ' ServerCredentialType ', e os valores permitidos são ' none ', ' X509 ' ou ' Windows '. Este artigo se concentra na autenticação do tipo X509.
- as regras de validação (autenticação); essas configurações são definidas pelo proprietário do cluster e descrevem quais credenciais devem ser aceitas para uma determinada função. Os exemplos serão examinados em detalhes imediatamente abaixo.
- configurações usadas para ajustar ou alterar sutilmente o resultado da autenticação; exemplos aqui incluem sinalizadores (de-) restringindo a imposição de listas de certificados revogados, etc.

> [!NOTE]
> Os exemplos de configuração de cluster fornecidos abaixo são trechos do manifesto do cluster em formato XML, como o formato mais resumido que dá suporte diretamente à funcionalidade Service Fabric descrita neste artigo. As mesmas configurações podem ser expressas diretamente nas representações de JSON de uma definição de cluster, seja um manifesto de cluster JSON autônomo ou um modelo de gerenciamento de recursos do Azure.

Uma regra de validação de certificado compreende os seguintes elementos:
- a função correspondente: cliente, cliente administrador (função com privilégios)
- a credencial aceita para a função, declarada por impressão digital ou nome comum da entidade

#### <a name="thumbprint-based-certificate-validation-declarations"></a>Declarações de validação de certificado baseadas em impressão digital
No caso de regras de validação baseadas em impressão digital, as credenciais apresentadas por um chamador solicitando uma conexão no/para o cluster serão validadas da seguinte maneira:
  - a credencial é um certificado válido e bem formado: sua cadeia pode ser compilada, as assinaturas correspondem
  - o certificado é um tempo válido (não antes de <= Now < não After)
  - o hash SHA-1 do certificado corresponde à declaração, como uma comparação de cadeia de caracteres que não diferencia maiúsculas de minúsculas, excluindo todos os espaços em branco

Quaisquer erros de confiança encontrados durante a criação ou validação de cadeia serão suprimidos para declarações baseadas em impressão digital, exceto os certificados expirados-embora as disposições também existam para esse caso. Especificamente, as falhas relacionadas a: status de revogação é desconhecido ou offline, raiz não confiável, uso de chave inválido, cadeia parcial são consideradas erros não fatais; a premissa, nesse caso, é que o certificado é meramente um envelope para um par de chaves – a segurança está no fato de que o proprietário do cluster definiu a medida Places para proteger a chave privada.

O trecho a seguir de um manifesto de cluster exemplifica esse conjunto de regras de validação baseadas em impressão digital:

```xml
<Section Name="Security">
  <Parameter Name="ClusterCredentialType" Value="X509" />
  <Parameter Name="ServerAuthCredentialType" Value="X509" />
  <Parameter Name="AdminClientCertThumbprints" Value="d5ec...4264" />
  <Parameter Name="ClientCertThumbprints" Value="7c8f...01b0" />
  <Parameter Name="ClusterCertThumbprints" Value="abcd...1234,ef01...5678" />
  <Parameter Name="ServerCertThumbprints" Value="ef01...5678" />
</Section>
```

Cada uma das entradas acima se refere a uma identidade específica, conforme descrito anteriormente; cada entrada também permite especificar vários valores, como uma lista separada por vírgulas de cadeias de caracteres. Neste exemplo, após a validação bem-sucedida das credenciais de entrada, o apresentador de um certificado com a impressão digital SHA-1 5ec... 4264 ' receberá a função ' admin '; por outro lado, um chamador Autenticando com o certificado ' 7c8f... ' 01b0 ' receberá uma função de ' usuário ', restrita a operações somente leitura. Um chamador de entrada que apresenta um certificado cuja impressão digital é ' abcd... 1234 ' ou ' EF01... 5678 ' será aceito como um nó par no cluster. Por fim, um cliente que se conecta a um ponto de extremidade de gerenciamento do cluster esperará que a impressão digital do certificado do servidor seja ' EF01... '... 5678 '. 

Como mencionado anteriormente, Service Fabric faz provisionar para aceitar certificados expirados; o motivo é que os certificados têm um tempo de vida limitado e, quando declarados pela impressão digital (que se refere a uma instância de certificado específica), permitir que um certificado expire, resultará em falha na conexão com o cluster ou um recolhimento preciso do cluster. É muito fácil esquecer ou negligenciar a rotação de um certificado fixado por impressão digital e, infelizmente, a recuperação dessa situação é difícil.

Para esse fim, o proprietário do cluster pode declarar explicitamente que os certificados autoassinados declarados pela impressão digital devem ser considerados válidos, da seguinte maneira:

```xml
  <Section Name="Security">
    <Parameter Name="AcceptExpiredPinnedClusterCertificate" Value="true" />
  </Section>
```
Esse comportamento não se estende aos certificados emitidos pela autoridade de certificação; Se esse for o caso, um certificado expirado, conhecido para ser comprometido, pode se tornar ' válido ' assim que não fosse mais uma figura na lista de certificados revogados da autoridade de certificação e, portanto, apresentar um risco à segurança. Com certificados autoassinados, o proprietário do cluster é considerado a única parte responsável por proteger a chave privada do certificado, que não é o caso de certificados emitidos por CA-o proprietário do cluster pode não estar ciente de como ou quando o certificado foi declarado como comprometido.

#### <a name="common-name-based-certificate-validation-declarations"></a>Declarações de validação de certificado com base no nome comum
Declarações baseadas em nome comum usam uma das seguintes formas:
- nome comum da entidade (somente)
- nome comum da entidade com fixação do emissor

Primeiro, vamos considerar um trecho de um manifesto de cluster que exemplifica ambos os estilos de declaração:
```xml
    <Section Name="Security/ServerX509Names">
      <Parameter Name="server.demo.system.servicefabric.azure-int" Value="" />
    </Section>
    <Section Name="Security/ClusterX509Names">
      <Parameter Name="cluster.demo.system.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```
As declarações referem-se às identidades do servidor e do cluster, respectivamente; Observe que as declarações baseadas em CN têm suas próprias seções no manifesto do cluster, separadas da ' segurança ' padrão. Em ambas as declarações, o ' nome ' representa o nome comum da entidade diferenciada do certificado e o campo ' valor ' representa o emissor esperado, da seguinte maneira:

- no primeiro caso, a declaração declara que o elemento de nome comum do assunto diferenciado do certificado do servidor deve corresponder à cadeia de caracteres "server.demo.system. Service Fabric. Azure-int"; o campo "valor" vazio denota a expectativa de que a raiz da cadeia de certificados é confiável no nó/computador em que o certificado do servidor está sendo validado; no Windows, isso significa que o certificado pode se encadear a qualquer um dos certificados instalados no repositório "AC raiz confiável";
- no segundo caso, a declaração declara que o apresentador de um certificado é aceito como um nó par no cluster se o nome comum do certificado corresponder à cadeia de caracteres "cluster.demo.system. Service Fabric. Azure-int", *e* a impressão digital do emissor direto do certificado corresponder a uma das entradas separadas por vírgula no campo ' valor '. (Esse tipo de regra é coloquialmente conhecido como ' nome comum com fixação do emissor '.)

Em ambos os casos, a cadeia do certificado é criada e deve ser livre de erros; ou seja, erros de revogação, cadeia parcial ou tempo-erros de confiança inválidos são considerados fatais e a validação do certificado falhará. Fixar os emissores resultará na consideração do status ' raiz não confiável ' como um erro não fatal; Apesar das aparências, essa é uma forma mais estrita de validação, pois permite que o proprietário do cluster restrinja o conjunto de emissores autorizados/aceitos para sua própria PKI.

Após a criação da cadeia de certificados, ela é validada em relação a uma política TLS/SSL padrão com o assunto declarado como o nome remoto; um certificado será considerado uma correspondência se o nome comum da entidade ou qualquer um de seus nomes alternativos da entidade corresponder à declaração de CN do manifesto do cluster. Há suporte para caracteres curinga nesse caso, e a correspondência de cadeia de caracteres não diferencia maiúsculas de minúsculas.

(Devemos esclarecer que a sequência descrita acima poderia ser executada para cada tipo de uso de chave declarado pelo certificado; se o certificado especificar o uso da chave de autenticação do cliente, a cadeia será criada e avaliada primeiro para uma função de cliente. No caso de êxito, a avaliação é concluída e a validação é bem-sucedida. Se o certificado não tiver o uso de autenticação do cliente ou a validação falhar, o tempo de execução do Service Fabric criará e avaliará a cadeia para a autenticação do servidor.)

Para concluir o exemplo, o trecho a seguir ilustra a declaração de certificados de cliente por nome comum:
```xml
    <Section Name="Security/AdminClientX509Names">
      <Parameter Name="admin.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
    <Section Name="Security/ClientX509Names">
      <Parameter Name="user.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```

As declarações acima correspondem às identidades de administrador e de usuário, respectivamente; a validação de certificados declarados dessa maneira é exatamente conforme descrito para os exemplos anteriores, de certificados de cluster e de servidor.

> [!NOTE]
> Declarações baseadas em nome comum são destinadas a simplificar a rotação e, em geral, o gerenciamento de certificados de cluster. No entanto, é recomendável aderir às seguintes recomendações para garantir a disponibilidade e a segurança contínuas do cluster:
  * preferir a fixação do emissor para depender de raízes confiáveis
  * Evite misturar emissores de PKIs diferentes
  * Verifique se todos os emissores esperados estão listados na declaração de certificado; um emissor incompatível resultará em uma falha na validação
  * Verifique se os pontos de extremidade de política de certificado da PKI são detectáveis, disponíveis e acessíveis-isso significa que os pontos de extremidade AIA, CRL ou OCSP são declarados no certificado de folha e que estão acessíveis para que a criação da cadeia de certificados possa ser concluída.

Juntando tudo isso, após receber uma solicitação de uma conexão em um cluster protegido com certificados X. 509, o tempo de execução do Service Fabric usará as configurações de segurança do cluster para validar as credenciais da parte remota, conforme descrito acima; Se for bem-sucedido, o chamador/parte remota será considerado como sendo autenticado. Se a credencial corresponder a várias regras de validação, o tempo de execução concederá ao chamador a função de maior privilégio de qualquer uma das regras correspondentes. 

### <a name="presentation-rules"></a>Regras de apresentação
A seção anterior descreveu como funciona a autenticação em um cluster protegido por certificado; Esta seção explicará como o tempo de execução da Service Fabric descobre e carrega os certificados que ele usa para a comunicação no cluster; Chamamos essas regras de "apresentação".

Assim como acontece com as regras de validação, as regras de apresentação especificam uma função e a declaração de credencial associada, expressa por impressão digital ou nome comum. Ao contrário das regras de validação, as declarações comuns baseadas em nome não têm provisões para fixação do emissor; Isso permite maior flexibilidade, bem como melhor desempenho. As regras de apresentação são declaradas na (s) seção (ões) do manifesto do cluster para cada tipo de nó distinto; as configurações são divididas nas seções segurança do cluster para permitir que cada tipo de nó tenha sua configuração completa em uma única seção. Nos clusters do Azure Service Fabric, as declarações de certificado do tipo de nó são padronizadas para suas configurações correspondentes na seção segurança da definição do cluster.

#### <a name="thumbprint-based-certificate-presentation-declarations"></a>Declarações de apresentação de certificado com base em impressão digital
Conforme descrito anteriormente, o tempo de execução de Service Fabric distingue entre sua função como o par de outros nós no cluster e como o servidor para operações de gerenciamento de cluster. Em princípio, essas configurações podem ser definidas de uma distinta, mas, na prática, elas tendem a se alinhar. Para o restante deste artigo, vamos supor que as configurações correspondam para simplificar.

Vamos considerar o seguinte trecho de um manifesto de cluster:
```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindByThumbprint" X509FindValue="cc71...1984" X509FindValueSecondary="49e2...19d6" X509StoreName="my" Name="ClusterCertificate" />
        <ServerCertificate X509FindValue="cc71...1984" Name="ServerCertificate" />
        <ClientCertificate X509FindValue="cc71...1984" Name="ClientCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```
O elemento ' ClusterCertificate ' demonstra o esquema completo, incluindo parâmetros opcionais (' X509FindValueSecondary ') ou aqueles com padrões apropriados (' X509StoreName '); as outras declarações mostram uma forma abreviada. A declaração de certificado de cluster acima declara que as configurações de segurança de nós do tipo ' nt1vm ' são inicializadas com o certificado ' cc71.. 1984 ' como o primário e o ' 49e2.. 19d6 ' certificado como secundário; Espera-se que os dois certificados sejam encontrados no \' repositório de certificados LocalMachine My (ou no caminho equivalente do Linux, *var/lib/sfcerts*).

#### <a name="common-name-based-certificate-presentation-declarations"></a>Declarações de apresentação de certificado com base no nome comum
Os certificados de tipo de nó também podem ser declarados pelo nome comum da entidade, como exemplificado abaixo:

```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindBySubjectName" X509FindValue="demo.cluster.azuredocpr.system.servicefabric.azure-int" Name="ClusterCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```

Para qualquer tipo de declaração, um nó Service Fabric lerá a configuração na inicialização, localizará e carregará os certificados especificados e os classificará em ordem decrescente de seu atributo nobefore; os certificados expirados são ignorados e o primeiro elemento da lista é selecionado como a credencial do cliente para qualquer conexão de Service Fabric tentada por esse nó. (Na verdade, Service Fabric favorece o certificado emitido mais recentemente.)

> [!NOTE]
> Antes da versão 7.2.445 (7,2 CU4), Service Fabric selecionou o certificado de expiração mais distante (o certificado com a propriedade ' não após ' mais distante)

Observe que, para declarações de apresentação com base em nome comum, um certificado será considerado uma correspondência se o nome comum da entidade for igual ao campo X509FindValue (ou X509FindValueSecondary) da declaração como uma comparação de cadeia de caracteres exata e diferenciada de maiúsculas e minúsculas. Isso está em contraste com as regras de validação, que dão suporte à correspondência de curinga, bem como comparações de cadeias de caracteres que não diferenciam maiúsculas de minúsculas.  

### <a name="miscellaneous-certificate-configuration-settings"></a>Definições de configuração de certificado diversas
Foi mencionado anteriormente que as configurações de segurança de um Cluster Service Fabric também permitem a alteração sutilmente do comportamento do código de autenticação. Embora o artigo sobre [Service Fabric configurações de cluster](service-fabric-cluster-fabric-settings.md) represente a lista abrangente e mais atualizada de configurações, expandiremos o significado de algumas das configurações de segurança selecionadas aqui, para concluir a exposição completa da autenticação baseada em certificado. Para cada configuração, explicaremos a intenção, o valor padrão/comportamento, como ele afeta a autenticação e quais valores são aceitáveis.

Conforme mencionado, a validação de certificado sempre implica na criação e na avaliação da cadeia do certificado. Para certificados emitidos por autoridades de certificação, essa chamada de API de so aparentemente simples geralmente envolve várias chamadas de saída para vários pontos de extremidade da PKI emissora, cache de respostas e assim por diante. Devido à prevalência de chamadas de validação de certificado em um Cluster Service Fabric, quaisquer problemas nos pontos de extremidade da PKI podem resultar em redução da disponibilidade do cluster ou de uma divisão totalmente. Embora as chamadas de saída não possam ser suprimidas (veja abaixo na seção de perguntas frequentes para saber mais sobre isso), as configurações a seguir podem ser usadas para mascarar erros de validação causados por falhas em chamadas de CRL.

  * CrlCheckingFlag-abaixo da seção ' Security ', Cadeia de caracteres convertida em UINT. O valor dessa configuração é usado pelo Service Fabric para mascarar erros de status da cadeia de certificados alterando o comportamento da construção da cadeia; Ele é passado para a chamada [CertGetCertificateChain](/windows/win32/api/wincrypt/nf-wincrypt-certgetcertificatechain) do CryptoAPI do Win32 como o parâmetro ' dwFlags ' e pode ser definido como qualquer combinação válida de sinalizadores aceitos pela função. Um valor de 0 força o tempo de execução do Service Fabric a ignorar qualquer erro de status de confiança-isso não é recomendado, pois seu uso constituiria uma exposição de segurança significativa. O valor padrão é 0x40000000 (CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT).

  Quando usar: para teste local, com certificados autoassinados ou certificados de desenvolvedor que não estão totalmente formados/não têm uma infraestrutura de chave pública adequada para dar suporte aos certificados. Também pode usar como mitigação em ambientes de ar-gapped durante a transição entre PKIs.

  Como usar: Vamos pegar um exemplo que força a verificação de revogação para acessar somente URLs em cache. Assumir
  ```C++
  #define CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY         0x80000000
  ```
  em seguida, a declaração no manifesto do cluster se torna:
  ```xml
    <Section Name="Security">
      <Parameter Name="CrlCheckingFlag" Value="0x80000000" />
    </Section>
  ```

  * IgnoreCrlOfflineError-na seção ' Security ', booliano com um valor padrão de ' false '. Representa um atalho para suprimir um status de erro de criação de uma cadeia de ' revogação offline ' (ou um status de erro de validação de política de cadeia subsequente).

  Quando usar: teste local ou com certificados de desenvolvedor não apoiados por uma PKI apropriada. Use como mitigação em ambientes de ar-gapped ou quando a PKI for conhecida como inacessível.

  Como usar:
  ```xml
    <Section Name="Security">
      <Parameter Name="IgnoreCrlOfflineError" Value="true" />
    </Section>
  ```

  Outras configurações notáveis (todas na seção "segurança"):
  * AcceptExpiredPinnedClusterCertificate – discutido na seção dedicada à validação de certificado baseada em impressão digital; permite aceitar certificados de cluster autoassinados expirados. 
  * CertificateExpirySafetyMargin-Interval, expresso em minutos antes do carimbo de data/hora de não após do certificado, e durante o qual o certificado é considerado em risco de expiração. Service Fabric monitora certificados de cluster e emite periodicamente relatórios de integridade sobre a disponibilidade restante. Dentro do intervalo de ' segurança ', esses relatórios de integridade são elevados para o status ' aviso '. O valor padrão é 30 dias.
  * CertificateHealthReportingInterval-controla a frequência de relatórios de integridade referentes à validade de tempo restante dos certificados de cluster. Os relatórios só serão emitidos uma vez a cada intervalo. O valor é expresso em segundos, com um padrão de 8 horas.
  * EnforcePrevalidationOnSecurityChanges-booliano, controla o comportamento da atualização do cluster na detecção de alterações de configurações de segurança. Se definido como 'true', a atualização do cluster tentará fazer com que pelo menos um dos certificados que correspondem a uma das regras de apresentação possa transmitir uma regra de validação correspondente. A pré-validação é executada antes que as novas configurações sejam aplicadas a algum nó, mas é executada somente no nó que hospeda a réplica primária do serviço do Gerenciador de Cluster no momento da inicialização da atualização. No momento da redação deste artigo, a configuração tem um padrão de ' false ' e será definida como ' true ' para novos clusters de Service Fabric do Azure com uma versão de tempo de execução começando com 7,1.
 
### <a name="end-to-end-scenario-examples"></a>Cenário de ponta a ponta (exemplos)
Vimos as regras de apresentação, as regras de validação e os sinalizadores de ajuste, mas como tudo isso funciona juntos? Nesta seção, vamos trabalhar com dois exemplos de ponta a ponta demonstrando como as configurações de segurança podem ser aproveitadas para atualizações de clusters seguras. Observe que isso não se destina a ser um dissertação abrangente sobre o gerenciamento de certificado adequado em Service Fabric, procure um artigo complementar sobre esse tópico.

A separação das regras de apresentação e de validação representa a pergunta óbvia (ou preocupação) de se elas podem divergir e quais são as consequências. Na verdade, é possível que a seleção de um nó de um certificado de autenticação não passe as regras de validação de outro nó. Na verdade, essa discrepância é a principal causa dos incidentes relacionados à autenticação. Ao mesmo tempo, a separação dessas regras permite que um cluster continue operando durante uma atualização que altera as configurações de segurança do cluster. Considere que, ao aumentar as regras de validação primeiro como uma primeira etapa, todos os nós do cluster convergirão nas novas configurações e ainda usarão as credenciais atuais. 

Lembre-se de que, em um Cluster Service Fabric, uma atualização progride por meio de (até 5) ' domínios de atualização ' ou UDs. Somente os nós no UD atual estão sendo atualizados/alterados em um determinado momento, e a atualização prosseguirá para o próximo UD somente se a disponibilidade do cluster permitir. (Consulte [Service Fabric atualizações de cluster](service-fabric-cluster-upgrade.md) e outros artigos no mesmo tópico para obter mais detalhes.) As alterações de certificado/segurança são particularmente arriscadas, pois podem isolar nós do cluster ou deixar o cluster na borda da perda de quorum.

Usaremos a seguinte notação para descrever as configurações de segurança de um nó:

NK: {P:{TP = A}, V:{TP = A}}, em que:
  - ' NK ' representa um nó no domínio de atualização *k*
  - ' P ' representa as regras de apresentação atuais do nó (supondo que estamos se referindo a certificados de cluster somente); 
  - ' V ' representa as regras de validação atuais do nó (somente certificado de cluster)
  - ' TP = A ' representa uma declaração baseada em impressão digital (TP), com ' A ' sendo uma impressão digital de certificado
  - ' CN = B ' representa uma declaração baseada em nome comum (CN), com ' B ' sendo o nome comum da entidade do certificado 

#### <a name="rotating-a-cluster-certificate-declared-by-thumbprint"></a>Girando um certificado de cluster declarado pela impressão digital
A sequência a seguir descreve como uma atualização de dois estágios pode ser usada para introduzir com segurança um certificado de cluster secundário, declarado pela impressão digital; a primeira fase apresenta a nova declaração de certificado nas regras de validação e a segunda fase a apresenta nas regras de apresentação:
  - estado inicial: n0 = {P:{TP = A}, V:{TP = A}},... NK = {P:{TP = A}, V:{TP = a}}-o cluster está em repouso, todos os nós compartilham uma configuração comum
  - ao concluir A atualização do domínio 0: n0 = {P:{TP = A}, V:{TP = A, TP = B}},... NK = {P:{TP = A}, V:{TP = A}}-os nós em UD0 apresentarão o certificado A e aceitarão certificados A ou B; todos os outros nós apresentam e aceitam apenas o certificado
  - Após concluir o último domínio de atualização: n0 = {P:{TP = A}, V:{TP = A, TP = B}},... NK = {P:{TP = A}, V:{TP = A, TP = B}}-todos os nós apresentam o certificado A, todos os nós aceitarão o certificado A ou B
      
Neste ponto, o cluster está novamente em equilíbrio, e a segunda fase das configurações de segurança de atualização/alteração pode começar:
  - Após concluir A atualização do domínio 0: n0 = {P:{TP = A, TP = B}, V:{TP = A, TP = B}},... NK = {P:{TP = A}, V:{TP = A, TP = B}}-os nós em UD0 começarão a apresentar o B, que é aceito por qualquer outro nó no cluster.
  - após a conclusão do último domínio de atualização: n0 = {P:{TP = A, TP = B}, V:{TP = A, TP = B}},... NK = {P:{TP = A, TP = B}, V:{TP = A, TP = B}}-todos os nós mudaram para apresentar o certificado B. o certificado A pode agora ser desativado/removido da definição de cluster com um conjunto subsequente de atualizações.

#### <a name="converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations"></a>Convertendo um cluster de impressão digital para declarações de certificado com base em nome comum
Da mesma forma, alterar o tipo de declaração de certificado (da impressão digital para o nome comum) seguirá o mesmo padrão mostrado acima. Observe que as regras de validação permitem declarar os certificados de uma determinada função por impressão digital e nome comum na mesma definição de cluster. Por outro lado, no entanto, as regras de apresentação permitem apenas uma forma de declaração. Incidentalmente, a abordagem segura para converter um certificado de cluster de impressão digital para nome comum é introduzir o certificado de destino pretendido primeiro pela impressão digital e, em seguida, alterar essa declaração para um nome comum. No exemplo a seguir, vamos supor que a impressão digital ' A ' e o nome comum da entidade ' B ' se referem ao mesmo certificado. 

  - estado inicial: n0 = {P:{TP = A}, V:{TP = A}},... NK = {P:{TP = A}, V:{TP = a}}-o cluster está em repouso, todos os nós compartilham uma configuração comum, com um sendo a impressão digital do certificado primário
  - Após concluir A atualização do domínio 0: n0 = {P:{TP = A}, V:{TP = A, CN = B}},... NK = {P:{TP = A}, V:{TP = A}}-os nós no UD0 apresentarão o certificado A e aceitarão certificados com a impressão digital A ou o nome comum B; todos os outros nós apresentam e aceitam apenas o certificado
  - Após concluir o último domínio de atualização: n0 = {P:{TP = A}, V:{TP = A, CN = B}},... NK = {P:{TP = A}, V:{TP = A, CN = B}}-todos os nós apresentam o certificado A, todos os nós aceitarão o certificado A (TP) ou B (CN)

Neste ponto, podemos continuar alterando as regras de apresentação com uma atualização subsequente:
  - Após concluir a atualização do domínio 0: n0 = {P:{CN = B}, V:{TP = A, CN = B}},... NK = {P:{TP = A}, V:{TP = A, CN = B}}-os nós em UD0 apresentarão o certificado B encontrado por CN e aceitarão certificados com a impressão digital A ou o nome comum B; todos os outros nós apresentam e aceitam apenas o certificado, selecionado pela impressão digital
  - Após concluir o último domínio de atualização: n0 = {P:{CN = B}, V:{TP = A, CN = B}},... NK = {P:{CN = B}, V:{TP = A, CN = B}}-todos os nós apresentam o certificado B encontrado por CN, todos os nós aceitarão o certificado A (TP) ou B (CN)
    
A conclusão da fase 2 também marca a conversão do cluster para certificados comuns baseados em nome; as declarações de validação baseadas em impressão digital podem ser removidas em uma atualização de cluster subsequente.

> [!NOTE]
> Nos clusters Service Fabric do Azure, os fluxos de trabalho apresentados acima são orquestrados pelo provedor de recursos Service Fabric; o proprietário do cluster ainda é responsável por provisionar certificados no cluster de acordo com as regras indicadas (apresentação ou validação) e é incentivado a realizar alterações em várias etapas.

Em um artigo separado, abordaremos o tópico de gerenciamento e provisionamento de certificados em um Cluster Service Fabric.

## <a name="troubleshooting-and-frequently-asked-questions"></a>Solução de problemas e perguntas frequentes
Embora a depuração de problemas relacionados à autenticação em clusters Service Fabric não seja fácil, estamos hopefulndo as dicas e dicas a seguir podem ajudar. A maneira mais fácil de começar as investigações é examinar os Service Fabric logs de eventos nos nós do cluster, não necessariamente apenas aqueles que mostram os sintomas, mas também os nós que estão ativos, mas não conseguem se conectar a um de seus vizinhos. No Windows, os eventos de significância normalmente são registrados nos canais ' Applications and Services Logs\Microsoft-ServiceFabric\Admin ' ou ' Operational ', respectivamente. Às vezes, pode ser útil [habilitar o log de CAPI2](/archive/blogs/benjaminperkins/enable-capi2-event-logging-to-troubleshoot-pki-and-ssl-certificate-issues)para capturar mais detalhes sobre a validação do certificado, a recuperação de CRL/CTL etc. (Lembre-se de desabilitá-lo depois de concluir a reprodução, pode ser bastante detalhado.)

Os sintomas típicos que se manifestam em um cluster que enfrenta problemas de autenticação são: 
  - os nós estão inativos/em ciclo 
  - tentativas de conexão são rejeitadas
  - as tentativas de conexão estão atingindo o tempo limite

Cada um dos sintomas pode ser causado por problemas diferentes, e a mesma causa raiz pode mostrar diferentes manifestações; como tal, vamos listar um pequeno exemplo de problemas típicos, com recomendações para corrigi-los. 

* Os nós podem trocar mensagens, mas não podem se conectar. Uma possível causa para as tentativas de conexão serem encerradas é o erro "certificado não correspondido"-uma das partes em conexões de Service Fabric para Service Fabric está apresentando um certificado que falha nas regras de validação do destinatário. Pode ser acompanhado por qualquer um dos seguintes erros: 
  ```C++
  0x80071c44    -2147017660 FABRIC_E_SERVER_AUTHENTICATION_FAILED
  ```
  Para diagnosticar/investigar ainda mais: em cada um dos nós que tentam a conexão, determine qual certificado está sendo apresentado; Examine o certificado e tente emular as regras de validação (verificar impressão digital ou igualdade de nome comum, verifique as impressões digitais do emissor, se especificado).

  Outro código de erro que acompanha o comum pode ser:
  ```C++
  0x800b0109    -2146762487 CERT_E_UNTRUSTEDROOT
  ```
  Nesse caso, o certificado é declarado pelo nome comum e qualquer uma das seguintes opções se aplica:
    - os emissores não estão fixados e o certificado raiz não é confiável ou
    - os emissores estão fixados, mas a declaração não inclui a impressão digital do emissor direto deste certificado

* Um nó está ativo, mas não pode se conectar a outros nós; outros nós não recebem tráfego de entrada do nó com falha. Nesse caso, é possível que o carregamento do certificado falhe no nó local. Procure os seguintes erros:
  - certificado não encontrado-verifique se os certificados declarados nas regras de apresentação podem ser resolvidos pelo conteúdo do repositório de certificados LocalMachine\My (ou como especificado). 
    As possíveis causas de falha podem incluir: 
      - caracteres inválidos na declaração de impressão digital
      - o certificado não está instalado
      - o certificado expirou
      - a declaração de nome comum inclui o prefixo ' CN = '
      - a declaração especifica um curinga e não existe nenhuma correspondência exata no repositório de certificados (declaração: CN = *. mydomain. com, certificado real: CN = Server. mydomain. com)

  - credenciais desconhecidas – indica uma chave privada ausente correspondente ao certificado, normalmente acompanhado pelo código de erro: 
    ```C++ 
    0x8009030d  -2146893043 SEC_E_UNKNOWN_CREDENTIALS
    0x8009030e  -2146893042 SEC_E_NO_CREDENTIALS
    ```
    Para corrigir, verifique a existência da chave privada; Verifique se SFAdmins recebeu o acesso ' Read | execute ' à chave privada.

  - tipo de provedor inadequado-indica um certificado CNG (criptografia de nova geração) ("provedor de armazenamento de chaves de software da Microsoft"); neste momento, Service Fabric dá suporte apenas a certificados CAPI1. Normalmente acompanhado pelo código de erro:
    ```C++
    0x80090014  -2146893804 NTE_BAD_PROV_TYPE
    ```
    Para corrigir, crie novamente o certificado de cluster usando um provedor de CAPI1 (por exemplo, "Microsoft Enhanced RSA e AES Cryptographic Provider"). Para obter mais detalhes sobre provedores de criptografia, consulte [noções básicas sobre provedores criptográficos](/windows/win32/seccertenroll/understanding-cryptographic-providers)
