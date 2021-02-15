---
title: Programa de dispositivo certificado pelo Azure | Microsoft Docs
description: Entenda o programa de dispositivo certificado pelo Azure.
author: koichih
ms.author: koichih
ms.date: 09/25/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 1fcfc7a9e632e5db1fb809dba7a938c8641c9ddc
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92048093"
---
# <a name="what-is-the-azure-certified-device-program"></a>O que é o programa de dispositivo certificado pelo Azure?

O programa de dispositivo certificado pelo Azure garante que as soluções de clientes trabalhem diretamente com os serviços do Azure. O programa usa ferramentas, serviços e um Marketplace para compartilhar o conhecimento do setor e as práticas recomendadas com uma comunidade de integradores de soluções e dispositivos.

Este programa foi projetado para:

- **Dê confiança aos clientes:** Os clientes podem comprar com confiança os dispositivos que são certificados pela Microsoft para uso com os serviços do Azure.

- **Ajude os clientes a encontrar os dispositivos certos para sua solução:** Os criadores de dispositivos podem publicar os recursos exclusivos de seus dispositivos como parte do processo de certificação. Os clientes podem encontrar facilmente os produtos que atendem às suas necessidades.

- **Promover dispositivos certificados:** Os criadores de dispositivos têm maior visibilidade, entre em contato com os clientes e o uso da marca do dispositivo certificado pelo Azure.

Este artigo descreve como:

- Integre sua empresa ao programa de dispositivo certificado pelo Azure.
- Determine qual certificação é aplicável para seu dispositivo.
- Encontre os requisitos do programa e outros recursos para começar a usar o teste.
- Use o portal do dispositivo do Azure Certified para validar seu dispositivo.

## <a name="onboarding"></a>Integração

Para certificar seus dispositivos no [portal do dispositivo certificado pelo Azure](https://aka.ms/acdp), você deve concluir as seguintes etapas:

1. Verifique se sua empresa tem uma conta de Azure Active Directory usando um locatário corporativo ou de estudante.
2. Ingresse no [Microsoft Partner Network (MPN)](https://partner.microsoft.com/) usando sua conta.
3. Entre no portal do [dispositivo certificado do Azure](https://aka.ms/acdp) depois de ingressar no MPN.
4. Revisar e assinar o [contrato de programa](https://aka.ms/acdagreement) na página de perfil da empresa

### <a name="company-profile"></a>Perfil da empresa

Para gerenciar o perfil da sua empresa no portal do dispositivo certificado pelo Azure, selecione **perfil da empresa**. O perfil da empresa inclui a URL da empresa, o endereço de email e o logotipo. Aceite o contrato de programa nesta página antes de continuar com as operações de certificação.

A página de descrição do dispositivo no catálogo de dispositivos do Azure Certified usa as informações de perfil da empresa.

## <a name="choose-the-certification"></a>Escolha a certificação

Há três certificações diferentes, cada uma concentrada em fornecer um valor diferente para o cliente. Dependendo do tipo de dispositivo que você está criando e do público-alvo, você pode escolher a certificação ou as certificações que são mais aplicáveis:

### <a name="azure-certified-device"></a>Dispositivo certificado pelo Azure

A _certificação de dispositivo certificado pelo Azure_ valida que um dispositivo pode se conectar ao Hub IOT do Azure e provisionar com segurança por meio do DPS (serviço de provisionamento de dispositivos). Essa certificação reflete a funcionalidade de um dispositivo e a interoperabilidade que é uma linha de base necessária para certificações mais avançadas.

- Para saber mais, confira os [requisitos de certificação](https://aka.ms/acdrequirements).
- Para saber mais sobre como usar o DPS para conectar seu dispositivo ao Hub IoT do Azure, consulte [visão geral de dispositivos de provisionamento](../iot-dps/about-iot-dps.md).

### <a name="iot-plug-and-play"></a>IoT Plug and Play

A _certificação de plug and Play IOT_, uma certificação incremental para a certificação de dispositivo do Azure Certified, simplifica o processo de criação de dispositivos sem código de dispositivo personalizado. O IoT Plug and Play permite que os parceiros de hardware criem dispositivos que se integram facilmente com soluções de nuvem baseadas em IoT Central do Azure e soluções de terceiros.

- Para saber mais, confira os [requisitos de certificação](https://aka.ms/acdiotpnprequirements).
- Para saber mais sobre como preparar um dispositivo para teste de Plug and Play de IoT, confira [como certificar dispositivos iot plug and Play](howto-certify-device.md).

### <a name="edge-managed"></a>Gerenciado por borda

A _certificação gerenciada de borda_, uma certificação incremental para a certificação de dispositivo do Azure Certified, se concentra nos padrões de gerenciamento de dispositivos para dispositivos IOT do Azure que executam Windows, Linux ou RTOs. Atualmente, essa certificação de programa concentra-se na compatibilidade de tempo de execução do Edge para implantação e gerenciamento de módulo.

> [!TIP]
> Esse programa era conhecido anteriormente como _programa de certificação IOT Edge_.

- Para saber mais, confira os [requisitos de certificação](https://aka.ms/acdedgemanagedrequirements).
- Para saber mais sobre IoT Edge, consulte [IOT Edge visão geral](../iot-edge/about-iot-edge.md).
- Para saber mais sobre os sistemas operacionais e contêineres com suporte, confira [IOT Edge sistemas com suporte](../iot-edge/support.md).

## <a name="use-the-azure-certified-device-portal"></a>Usar o portal do dispositivo certificado pelo Azure

Esta seção resume como usar o portal de [dispositivos do Azure Certified](https://certify.azure.com). Para saber mais, consulte o [Guia de introdução ao portal](https://aka.ms/acdhelp).

Para certificar um dispositivo no programa de dispositivo certificado pelo Azure, conclua as quatro etapas a seguir:

1. Fornecer os detalhes do dispositivo
2. Testar o dispositivo
3. Enviar e concluir a revisão
4. Publicar no catálogo de dispositivos certificados do Azure (opcional)

### <a name="provide-device-details"></a>Fornecer detalhes do dispositivo

Para cada dispositivo que você deseja certificar, use os formulários no portal de certificação para registrar detalhes sobre o hardware do dispositivo, as instruções de configuração e o material de marketing:

- **Informações do dispositivo:** Coleta informações sobre o dispositivo, como nome, descrição, detalhes de hardware e sistema operacional.
- **Guia de introdução**: um documento PDF que um cliente pode usar para usar rapidamente seu produto. Os [modelos de exemplo](https://aka.ms/GSTemplate) estão disponíveis.
- **Detalhes de marketing:** Forneça informações de marketing prontas para o cliente para seu dispositivo, como informações de imagem e distribuidor.
- **Certificações adicionais do setor:** Uma seção opcional que permite que você forneça informações sobre quaisquer outras certificações que o dispositivo tem.

### <a name="test-the-device"></a>Testar o dispositivo

Esta fase interage com seu dispositivo e executa uma série de testes depois que o dispositivo usa o DPS para se conectar ao Hub IoT. Ao concluir, você pode exibir um conjunto de arquivos de log com os resultados de teste do dispositivo.

O portal de certificação tem instruções sobre como se conectar à instância do Hub IoT usada para teste. Você pode estabelecer a conexão de DPS por meio de qualquer um dos [métodos de atestado com suporte](../iot-dps/concepts-service.md#attestation-mechanism).

A equipe do dispositivo Azure Certified pode entrar em contato com o Device Builder para mais validação manual do dispositivo.

### <a name="submit-and-publish"></a>Enviar e publicar

O estágio final necessário é enviar o projeto para revisão. Esta etapa notifica um membro da equipe de dispositivos certificados do Azure para revisar seu projeto para fins de integridade, incluindo os detalhes do dispositivo e de marketing e o guia de introdução. Um membro da equipe pode entrar em contato com você no endereço de email da empresa fornecido anteriormente com perguntas ou editar solicitações antes da aprovação.

Se seu dispositivo exigir validação manual adicional como parte da certificação, você receberá um aviso no momento.

Quando um dispositivo é certificado, você pode optar por publicar os detalhes do produto no catálogo de dispositivos certificado pelo Azure usando o recurso **publicar no catálogo** na página Resumo do produto.

## <a name="if-you-have-questions"></a>Se você tiver dúvidas

Entre em contato com a equipe em [iotcert@microsoft.com](mailto:iotcert@microsoft.com?subject=Azure%20Certified%20Device%20question) caso de dúvidas sobre os programas de certificação, o portal de certificação ou o catálogo de dispositivos certificado pelo Azure.

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma visão geral do programa de dispositivo certificado pelo Azure, uma próxima etapa sugerida é aprender a [certificar os dispositivos de plug and Play IOT](howto-certify-device.md).