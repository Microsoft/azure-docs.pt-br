---
title: Atualizar um cluster para usar o nome comum do certificado
description: Saiba como converter um certificado de Cluster Service Fabric do Azure de declarações baseadas em impressão digital para nomes comuns.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: f719b1eb39da776827c6babec61e9e6701bb4602
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97900779"
---
# <a name="convert-cluster-certificates-from-thumbprint-based-declarations-to-common-names"></a>Converter certificados de cluster de declarações baseadas em impressão digital para nomes comuns

A assinatura de um certificado (normalmente conhecida como impressão digital) é exclusiva. Um certificado de cluster declarado pela impressão digital refere-se a uma instância específica de um certificado. Essa especificidade torna a substituição de certificado e o gerenciamento em geral, difícil e explícito. Cada alteração requer a orquestração das atualizações do cluster e dos hosts de computação subjacentes.

A conversão de declarações de certificado de um cluster do Azure Service Fabric de uma impressão digital baseada em declarações com base no nome comum da entidade do certificado (CN) simplifica consideravelmente o gerenciamento. Em particular, a sobreversão de um certificado não requer mais uma atualização de cluster. Este artigo descreve como converter um cluster existente em declarações baseadas em CN sem tempo de inatividade.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-to-certificate-authority-signed-certificates"></a>Mover para certificados assinados por autoridade de certificação

A segurança de um cluster cujo certificado é declarado pela impressão digital deixa o fato de que ele é impossível ou não é possível, de forma computacional, de forjar um certificado com a mesma assinatura de outro. Nesse caso, a comprovação do certificado é menos importante, portanto, os certificados autoassinados são adequados.

Por outro lado, a segurança de um cluster cujos certificados são declarados por fluxos CN da confiança implícita que o proprietário do cluster tem em seu provedor de certificados. O provedor é o serviço PKI (infraestrutura de chave pública) que emitiu o certificado. A confiança é baseada, entre outros fatores, nas práticas de certificação da PKI, se sua segurança operacional é auditada e aprovada por outras partes confiáveis, e assim por diante.

O proprietário do cluster também deve ter conhecimento detalhado de quais CAs (autoridades de certificação) estão emitindo seus certificados, pois esse é um aspecto fundamental da validação de certificados por assunto. Isso também implica que os certificados autoassinados são totalmente inadequados para essa finalidade. Literalmente, qualquer pessoa pode gerar um certificado com um determinado assunto.

Um certificado declarado por CN normalmente é considerado válido se:

* Sua cadeia pode ser compilada com êxito.
* O assunto tem o elemento CN esperado.
* Seu emissor (imediato ou superior na cadeia) é confiável para o agente que executa a validação.

O Service Fabric dá suporte à declaração de certificados por CN de duas maneiras:

* Com emissores *implícitos* , o que significa que a cadeia deve terminar em uma âncora de confiança.
* Com os emissores declarados pela impressão digital, que é conhecido como fixação do emissor.

Para obter mais informações, consulte [declarações de validação de certificado com base em nome comum](cluster-security-certificates.md#common-name-based-certificate-validation-declarations).

Para converter um cluster usando um certificado autoassinado declarado pela impressão digital para CN, o certificado assinado por AC deve ser introduzido primeiro no cluster por impressão digital. Somente a conversão de impressão digital para CN é possível.

Para fins de teste, um certificado autoassinado *pode* ser declarado por CN, mas somente se o emissor for fixado em sua própria impressão digital. Do ponto de vista da segurança, essa ação é quase equivalente a declarar o mesmo certificado por impressão digital. Uma conversão bem-sucedida desse tipo não garante uma conversão bem-sucedida de impressão digital em CN com um certificado assinado por CA. Recomendamos que você teste a conversão com um certificado correto assinado por uma autoridade de certificação. Existem opções gratuitas para este teste.

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Carregar o certificado e instalá-lo no conjunto de dimensionamento

No Azure, o mecanismo recomendado para obter e provisionar certificados envolve Azure Key Vault e suas ferramentas. Um certificado correspondente à declaração de certificado de cluster deve ser provisionado em todos os nós dos conjuntos de dimensionamento de máquinas virtuais que compõem o cluster. Para obter mais informações, consulte [segredos em conjuntos de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-securely-ship-a-certificate-to-the-vm).

É importante instalar os certificados de cluster atuais e de destino nas máquinas virtuais de cada tipo de nó do cluster antes de fazer alterações nas declarações de certificado do cluster. A jornada da emissão de certificados para o provisionamento em um nó de Service Fabric é discutida em detalhes na [jornada de um certificado](cluster-security-certificate-management.md#the-journey-of-a-certificate).

## <a name="bring-the-cluster-to-an-optimal-starting-state"></a>Colocar o cluster em um estado de inicialização ideal

Convertendo uma declaração de certificado de base de impressão digital para impactos baseados em CN:

- Como cada nó no cluster localiza e apresenta suas credenciais para outros nós.
- Como cada nó valida as credenciais de sua contraparte ao estabelecer uma conexão segura.

Examine as [regras de validação e apresentação para ambas as configurações](cluster-security-certificates.md#certificate-configuration-rules) antes de continuar. A consideração mais importante quando você executa uma conversão de impressão digital para CN é que os nós atualizados e não atualizados (ou seja, os nós que pertencem a domínios de atualização diferentes) devem ser capazes de executar uma autenticação mútua bem-sucedida a qualquer momento durante a atualização. A maneira recomendada para atingir esse comportamento é declarar o destino ou o certificado de meta por impressão digital em uma atualização inicial. Em seguida, conclua a transição para CN em um subseqüente. Se o cluster já estiver em um estado de início recomendado, você poderá ignorar esta seção.

Há vários Estados iniciais válidos para uma conversão. A constante é que o cluster já está usando o certificado de destino (declarado pela impressão digital) no início da atualização para CN. Consideramos `GoalCert` , `OldCert1` e `OldCert2` neste artigo.

#### <a name="valid-starting-states"></a>Estados de inicialização válidos

- `Thumbprint: GoalCert, ThumbprintSecondary: None`
- `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, onde `GoalCert` tem uma `NotBefore` data posterior à de `OldCert1`
- `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, onde `GoalCert` tem uma `NotBefore` data posterior à de `OldCert1`

> [!NOTE]
> Antes da versão 7.2.445 (7,2 CU4), Service Fabric selecionou o certificado de expiração mais distante (o certificado com a propriedade ' não após ' mais distante); portanto, os Estados de início acima antes de 7,2 CU4 exigem que o GoalCert tenha uma data posterior a `NotAfter``OldCert1`

Se o cluster não estiver em um dos Estados válidos descritos anteriormente, consulte informações sobre como atingir esse estado na seção no final deste artigo.

## <a name="select-the-desired-cn-based-certificate-validation-scheme"></a>Selecione o esquema de validação de certificado baseado em CN desejado

Conforme descrito anteriormente, Service Fabric dá suporte à declaração de certificados por CN com uma âncora de confiança implícita ou com a fixação explícita das impressões digitais do emissor. Para obter mais informações, consulte [declarações de validação de certificado com base em nome comum](cluster-security-certificates.md#common-name-based-certificate-validation-declarations).

Verifique se você tem uma boa compreensão das diferenças e das implicações de escolher um dos mecanismos. Sintaticamente, essa diferença ou opção é determinada pelo valor do `certificateIssuerThumbprintList` parâmetro. Vazio significa depender de uma AC raiz confiável (âncora de confiança), enquanto um conjunto de impressões digitais restringe os emissores permitidos diretos de certificados de cluster.

   > [!NOTE]
   > O `certificateIssuerThumbprint` campo permite que você especifique os emissores diretos esperados de certificados declarados pelo assunto CN. Os valores aceitáveis são uma ou mais impressões digitais SHA1 separadas por vírgula. Essa ação reforça a validação do certificado.
   >
   > Se nenhum emissor for especificado ou a lista estiver vazia, o certificado será aceito para autenticação se sua cadeia puder ser criada. O certificado então termina em uma raiz confiável pelo validador. Se uma ou mais impressões digitais do emissor forem especificadas, o certificado será aceito se a impressão digital de seu emissor direto, conforme extraído da cadeia, corresponder a qualquer um dos valores especificados neste campo. O certificado será aceito se a raiz for confiável ou não.
   >
   > Uma PKI pode usar diferentes autoridades de certificação (também conhecidas como *emissores*) para assinar certificados com um determinado assunto. Por esse motivo, é importante especificar todas as impressões digitais do emissor esperado para esse assunto. Em outras palavras, a renovação de um certificado não tem garantia de ser assinada pelo mesmo emissor do certificado que está sendo renovado.
   >
   > A especificação do emissor é considerada uma prática recomendada. Omitir o emissor continuará a funcionar para o encadeamento de certificados para uma raiz confiável, mas esse comportamento tem limitações e pode ser dividido em um futuro próximo. Os clusters implantados no Azure, protegidos com certificados X509 emitidos por uma PKI privada, e declarados pelo assunto, talvez não possam ser validados pelo Service Fabric (para comunicação de cluster para serviço). A validação exige que a política de certificado PKI seja detectável, disponível e acessível.

## <a name="update-the-clusters-azure-resource-manager-template-and-deploy"></a>Atualizar o modelo de Azure Resource Manager do cluster e implantar

Gerencie seus clusters de Service Fabric com modelos de Azure Resource Manager (ARM). Uma alternativa, que também usa artefatos JSON, é a [Azure Resource Explorer (versão prévia)](https://resources.azure.com). Uma experiência equivalente não está disponível no portal do Azure no momento.

Se o modelo original correspondente a um cluster existente não estiver disponível, um modelo equivalente poderá ser obtido no portal do Azure. Vá para o grupo de recursos que contém o cluster e selecione **Exportar modelo** no menu **automação** à esquerda. Em seguida, selecione os recursos desejados. No mínimo, o conjunto de dimensionamento de máquinas virtuais e os recursos de cluster, respectivamente, devem ser exportados. O modelo gerado também pode ser baixado. Este modelo pode exigir alterações antes de ser totalmente implantável. O modelo também pode não corresponder exatamente ao original. É uma reflexão do estado atual do recurso de cluster.

As alterações necessárias são as seguintes:

- Atualizando a definição da extensão do nó de Service Fabric (no recurso de máquina virtual). Se o cluster definir vários tipos de nó, você precisará atualizar a definição de cada conjunto de dimensionamento de máquinas virtuais correspondente.
- Atualizando a definição de recurso de cluster.

Exemplos detalhados estão incluídos aqui.

### <a name="update-the-virtual-machine-scale-set-resources"></a>Atualizar os recursos do conjunto de dimensionamento de máquinas virtuais
De:
```json
"virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            ...
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            ...
                            "certificate": {
                                "thumbprint": "[parameters('certificateThumbprint')]",
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        ...
                    }
                },
```
Para:
```json
"virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            ...
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            ...
                            "certificate": {
                                "commonNames": [
                                    "[parameters('certificateCommonName')]"
                                ],
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        ...
                    }
                },
```

### <a name="update-the-cluster-resource"></a>Atualizar o recurso de cluster

No recurso **Microsoft. perfabric/clusters** , adicione uma propriedade **certificateCommonNames** com uma configuração **commonnames** e remova totalmente a propriedade do **certificado** (todas as suas configurações).

De:
```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            ...
        ],
        "properties": {
            "addonFeatures": [
                ...
            ],
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
```
Para:
```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            ...
        ],
        "properties": {
            "addonFeatures": [
                ...
            ],
            "certificateCommonNames": {
                "commonNames": [
                    {
                        "certificateCommonName": "[parameters('certificateCommonName')]",
                        "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprintList')]"
                    }
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
```

Para obter mais informações, consulte [implantar um Service Fabric cluster que usa o nome comum do certificado em vez da impressão digital](./service-fabric-create-cluster-using-cert-cn.md).

## <a name="deploy-the-updated-template"></a>Implantar o modelo atualizado

Reimplante o modelo atualizado depois de fazer as alterações.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="achieve-a-valid-starting-state-for-converting-a-cluster-to-cn-based-certificate-declarations"></a>Obter um estado de inicialização válido para converter um cluster para declarações de certificado baseadas em CN

| Estado inicial | Atualização 1 | Atualização 2 |
| :--- | :--- | :--- |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` e `GoalCert` tem uma `NotBefore` data posterior a `OldCert1` | `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` e `OldCert1` tem uma `NotBefore` data posterior a `GoalCert` | `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1` | `Thumbprint: GoalCert, ThumbprintSecondary: None` |
| `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, onde `OldCert1` tem uma `NotBefore` data posterior a `GoalCert` | Atualizar para o `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, onde `OldCert1` tem uma `NotBefore` data posterior a `GoalCert` | Atualizar para o `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: OldCert2` | Remover um de `OldCert1` ou `OldCert2` para chegar ao estado `Thumbprint: OldCertx, ThumbprintSecondary: None` | Continuar a partir do novo estado inicial |

> [!NOTE]
> Para um cluster em uma versão anterior à versão 7.2.445 (7,2 CU4), substitua `NotBefore` por `NotAfter` nos Estados acima.

Para obter instruções sobre como realizar qualquer uma dessas atualizações, consulte [gerenciar certificados em um cluster de Service Fabric do Azure](service-fabric-cluster-security-update-certs-azure.md).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [segurança do cluster](service-fabric-cluster-security.md).
* Saiba como [sobrepor um certificado de cluster por nome comum](service-fabric-cluster-rollover-cert-cn.md).
* Saiba como [configurar um cluster para substituição](cluster-security-certificate-management.md)automática sem toque.

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
