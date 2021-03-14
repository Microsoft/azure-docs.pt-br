---
title: Conectar-se ao Azure Percept DK por SSH
description: Saiba como entrar em SSH em seu Azure Percept DK com a saída
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 8dda18271de9b7d65246f0882ee7a68191031c05
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096608"
---
# <a name="connect-to-your-azure-percept-dk-over-ssh"></a>Conectar-se ao Azure Percept DK por SSH

Siga as etapas abaixo para configurar uma conexão SSH para o Azure Percept [DK por meio](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)de uma reinicialização.

## <a name="prerequisites"></a>Pré-requisitos

- Um computador host baseado em Windows, Linux ou OS X com Wi-Fi recurso
- Um cliente SSH
    - Se o computador host executar o Windows [, o](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) reinício será um cliente SSH efetivo e será usado em todo este guia.
    - Se o computador host executar o Linux ou o OS X, os serviços SSH serão incluídos nesses sistemas operacionais e poderão ser executados sem um aplicativo cliente separado. Verifique a documentação do produto do sistema operacional para obter mais informações sobre como executar serviços SSH.
- Azure Percept DK
- Configurar uma conta de logon SSH durante a [experiência de integração do Azure PERCEPT DK](./quickstart-percept-dk-set-up.md)

## <a name="initiate-the-ssh-connection"></a>Iniciar a conexão SSH

1. Ligar o Azure Percept DK (Kit de desenvolvimento)

1. Se o seu kit de desenvolvimento já estiver conectado a uma rede por meio de Ethernet ou Wi-Fi, pule para a próxima etapa. Caso contrário, conecte o computador host diretamente ao ponto de acesso Wi-Fi do kit de desenvolvimento, assim como se conectar a qualquer outra rede de Wi-Fi:
    - **nome da rede**: scz-xxxx (em que "xxxx" são os últimos quatro dígitos do endereço de rede MAC do kit de desenvolvimento)
    - **senha**: pode ser encontrada no Cartão de boas-vindas que acompanha o kit de desenvolvimento

    > [!WARNING]
    > Enquanto estiver conectado ao ponto de acesso Wi-Fi do Azure Percept DK, seu computador host perderá temporariamente a conexão com a Internet. Chamadas de conferência de vídeo ativas, web streaming ou outras experiências baseadas em rede serão interrompidas até que a etapa 3 da experiência de integração do Azure Percept DK seja concluída.

1. Abra o PuTTY. Insira o seguinte e clique em **abrir** para SSH em seu devkit:

    1. Nome do host: 10.1.1.1
    1. Porta: 22
    1. Tipo de conexão: SSH

    > [!NOTE]
    > O **nome do host** é o endereço IP do seu dispositivo. Se o seu kit de desenvolvimento estiver conectado ao ponto de acesso Wi-Fi do kit de desenvolvimento, o endereço IP será 10.1.1.1. Se o seu kit de desenvolvimento estiver conectado pela Ethernet, use o endereço IP local do dispositivo, que você pode obter do roteador ou hub Ethernet. Se o dispositivo estiver conectado por Wi-Fi, você deverá usar o endereço IP fornecido durante a [experiência de integração do Azure PERCEPT DK](./quickstart-percept-dk-set-up.md).

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/ssh-putty.png" alt-text="Imagem.":::

1. Faça logon no terminal de saída com o nome de usuário e senha do SSH criados durante a experiência de integração.

## <a name="next-steps"></a>Próximas etapas

Depois de se conectar com êxito ao Percept do Azure DK por meio do SSH, você pode executar uma variedade de tarefas, incluindo solução de problemas, atualizações de USB e executar a ferramenta DiagTool ou SoftAP.