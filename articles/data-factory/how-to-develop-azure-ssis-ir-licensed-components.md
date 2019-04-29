---
title: Instalar componentes licenciados para o tempo de execução de integração do Azure-SSIS | Microsoft Docs
description: Saiba como um ISV pode desenvolver e instalar componentes personalizados pagos ou licenciados para o tempo de execução de integração do Azure-SSIS
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/13/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 80d4fff03422beacccd3aff3cdd8cb1047d5f5af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61344628"
---
# <a name="install-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Instalar componentes personalizados pagos ou licenciados para o tempo de execução de integração do Azure-SSIS

Este artigo descreve como um ISV pode desenvolver e instalar componentes personalizados pagos ou licenciados para pacotes do SSIS (SQL Server Integration Services) executados no Azure no tempo de execução de integração do Azure-SSIS.

## <a name="the-problem"></a>O problema

A natureza do tempo de execução de integração do Azure-SSIS apresenta vários desafios, o que torna inadequados os métodos de licenciamento comuns usados para a instalação local de componentes personalizados. Consequentemente, o IR do Azure-SSIS exige uma abordagem diferente.

-   Os nós de IR do Azure-SSIS são voláteis e podem ser alocados ou liberados a qualquer momento. Por exemplo, você pode iniciar ou parar nós para gerenciar o custo ou aumentar e diminuir vários tamanhos de nó. Como resultado, a associação de uma licença de componente de terceiros a um nó específico usando as informações específicas do computador, como endereço MAC ou ID de CPU, não é mais viável.

-   Você também pode expandir ou reduzir o IR do Azure-SSIS para que o número de nós possa diminuir ou aumentar a qualquer momento.

## <a name="the-solution"></a>A solução

Como resultado das limitações dos métodos tradicionais de licenciamento descritos na seção anterior, o IR do Azure-SSIS fornece uma nova solução. Essa solução usa variáveis de ambiente do Windows e variáveis de sistema do SSIS para validação e associação da licença de componentes de terceiros. Os ISVs podem usar essas variáveis para obter informações exclusivas e persistente para um IR do Azure-SSIS, como ID do Cluster e Contagem de Nós do Cluster. Com essas informações, os ISVs podem, em seguida, associar a licença para seu componente ao IR do Azure-SSIS *como um cluster*. Esta associação usa uma ID que não se altera quando os clientes iniciam ou param, aumentam ou reduzem a escala verticalmente, aumentam ou reduzem a escala horizontalmente ou reconfigurar o IR do SSIS-Azure de qualquer outra forma.

O diagrama a seguir mostra a instalação, a ativação e a associação típicas da licença e os fluxos de validação para componentes de terceiros que usam essas novas variáveis:

![Instalação de componentes licenciados](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>Instruções
1. Os ISVs podem oferecer seus componentes licenciados em várias SKUs ou níveis (por exemplo, único nó, até 5 nós, até 10 nós e assim por diante). O ISV fornece a chave do produto (Product Key) correspondente quando clientes adquirem um produto. O ISV também pode fornecer um contêiner de blob de Armazenamento do Azure que contém um script de instalação do ISV e arquivos associados. Os clientes podem copiar esses arquivos em seu próprio contêiner de armazenamento e modificá-los com sua própria chave do produto (Product Key) (por exemplo, executando `IsvSetup.exe -pid xxxx-xxxx-xxxx`). Os clientes podem, então, provisionar ou reconfigurar o IR do Azure-SSIS com o URI de SAS do contêiner como parâmetro. Para obter mais informações, confira [Configuração personalizada para o tempo de execução de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

2. Quando o IR do Azure-SSIS é provisionado ou reconfigurado, a instalação do ISV é executada em cada nó para consultar as variáveis de ambiente do Windows, `SSIS_CLUSTERID` e `SSIS_CLUSTERNODECOUNT`. Em seguida, o IR do Azure-SSIS envia sua ID de Cluster e a chave do produto (Product Key) licenciado para o servidor de ativação do ISV para gerar uma chave de ativação.

3. Depois de receber a chave de ativação, a instalação do ISV pode armazenar a chave localmente em cada nó (por exemplo, no Registro).

4. Quando os clientes executam um pacote que usa o componente licenciado do ISV em um nó do IR do Azure-SSIS, o pacote lê a chave de ativação armazenada localmente e valida em relação à ID de Cluster do nó. O pacote também pode relatar a Contagem de Nós de Cluster para o servidor de ativação do ISV.

    Aqui está um exemplo de código que valida a chave de ativação e relata a contagem de nós do cluster:

    ```csharp
    public override DTSExecResult Validate(Connections, VariableDispenser, IDTSComponentEvents componentEvents, IDTSLogging log) 
                                                                                                                               
    {                                                                                                                             
                                                                                                                               
    Variables vars = null;                                                                                                        
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterID");                                                                           
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterNodeCount");                                                                    
                                                                                                                               
    variableDispenser.GetVariables(ref vars);                                                                                     
                                                                                                                               
    // Validate Activation Key with ClusterID                                                                                     
                                                                                                                               
    // Report on ClusterNodeCount                                                                                                 
                                                                                                                               
    vars.Unlock();                                                                                                                
                                                                                                                               
    return base.Validate(connections, variableDispenser, componentEvents, log);                                                   
                                                                                                                               
    }
    ```

## <a name="isv-partners"></a>Parceiros do ISV

Você pode encontrar uma lista de parceiros do ISV que adaptaram seus componentes e extensões para o IR do Azure-SSIS no final desta postagem de blog – [Enterprise Edition, Instalação Personalizada e Extensibilidade de Terceiros para SSIS no ADF](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/).

## <a name="next-steps"></a>Próximas etapas

-   [Instalação personalizada do tempo de execução de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Enterprise Edition do Tempo de Execução de Integração do Azure-SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)
