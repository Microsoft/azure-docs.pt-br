---
title: Inicialização segura de firmware-segurança do Azure
description: Visão geral técnica da inicialização segura do firmware do Azure.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: feb28b1d448d0146046ed789d1389a3a42f344de
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94557501"
---
# <a name="secure-boot"></a>Inicialização Segura

A inicialização segura é um recurso do [Unified Extensible Firmware Interface](https://en.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface) (UEFI) que exige que todos os componentes de software e firmware de nível baixo sejam verificados antes do carregamento. Durante a inicialização, a inicialização segura de UEFI verifica a assinatura de cada parte do software de inicialização, incluindo drivers de firmware UEFI (também conhecidos como ROMs de opção), aplicativos EFI (Extensible Firmware Interface) e os drivers e binários do sistema operacional. Se as assinaturas forem válidas ou forem confiáveis pelo OEM (fabricante original do equipamento), o computador será inicializado e o firmware dará controle ao sistema operacional.

## <a name="components-and-process"></a>Componentes e processo

A inicialização segura depende desses componentes críticos:

- Chave de plataforma (CP) – estabelece a relação de confiança entre o proprietário da plataforma (Microsoft) e o firmware. A metade pública é PKpub e a metade privada é PKpriv.
- Banco de dados de chave de registro de chave (KEK) – estabelece a relação de confiança entre o sistema operacional e o firmware da plataforma. A metade pública é KEKpub e a metade privada é KEKpriv.
- Banco de dados de assinatura (DB) – mantém os resumos de assinantes confiáveis (chaves públicas e certificados) do firmware e dos módulos de código de software autorizados a interagir com o firmware de plataforma.
- Banco de dados de assinaturas revogadas (dbx) – mantém resumos revogados de módulos de código que foram identificados como mal-intencionados, vulneráveis, comprometidos ou não confiáveis. Se um hash estiver no BD de assinatura e no BD de assinaturas revogadas, o banco de dados de assinaturas revogadas terá o precedente.

A figura e o processo a seguir explicam como esses componentes são atualizados:

![Diagrama que mostra os componentes de inicialização segura.](./media/secure-boot/secure-boot.png)

O OEM armazena os resumos de inicialização segura na RAM não volátil (NV-RAM) do computador no momento da fabricação.

1. O banco de dados de assinatura (DB) é preenchido com os codificadores ou hashes de imagem de aplicativos UEFI, carregadores de sistema operacional (como o carregador do sistema operacional da Microsoft ou o Gerenciador de inicialização) e drivers UEFI que são confiáveis.
2. O dbx (banco de dados de assinaturas revogados) é preenchido com resumos de módulos que não são mais confiáveis.
3. O banco de dados KEK (chave de registro de chave) é preenchido com chaves de assinatura que podem ser usadas para atualizar o banco de dados de assinatura e o banco de dados de assinaturas revogadas. Os bancos de dados podem ser editados por meio de atualizações que são assinadas com a chave correta ou por meio de atualizações por um usuário autorizado fisicamente presente usando menus de firmware.
4. Depois que os bancos de dados DB, dbx e KEK tiverem sido adicionados e a validação e o teste do firmware final forem concluídos, o OEM bloqueará a edição do firmware e gerará uma CP (chave de plataforma). A CP pode ser usada para assinar atualizações para o KEK ou para desativar a inicialização segura.

Durante cada estágio do processo de inicialização, os resumos do firmware, do carregador de inicialização, do sistema operacional, dos drivers de kernel e de outros artefatos da cadeia de boot são calculados e comparados com valores aceitáveis. O firmware e o software que são descobertos como não confiáveis não têm permissão para serem carregados. Assim, a injeção de malware de nível baixo ou os ataques de malware de pré-inicialização podem ser bloqueados.

## <a name="secure-boot-on-the-azure-fleet"></a>Inicialização segura no Azure frota
Hoje, todos os computadores que são integrados e implantados na computação do Azure da frota para hospedar cargas de trabalho do cliente vêm de andares de fábrica com a inicialização segura habilitada. Ferramentas e processos de destino estão em vigor em todos os estágios no pipeline de integração e desenvolvimento de hardware para garantir que a habilitação de inicialização segura não seja revertida por acidente ou por más intenções.

A validação dos resumos do DB e do dbx está correta garante:

- O carregador de Blogs está presente em uma das entradas de BD
- A assinatura do carregador de pacarregado é válida
- Inicializações de host com software confiável

 Ao validar as assinaturas de KEKpub e PKpub, podemos confirmar que apenas partes confiáveis têm permissão para modificar as definições de qual software é considerado confiável. Por fim, ao garantir que a inicialização segura está ativa, podemos validar que essas definições estão sendo impostas.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o que fazemos para impulsionar a integridade e a segurança da plataforma, consulte:

- [Segurança do firmware](firmware.md)
- [Atestado de inicialização e host medidos](measured-boot-host-attestation.md)
- [Projeto Cerberus](project-cerberus.md)
- [Criptografia em repouso](encryption-atrest.md)
- [Segurança do hipervisor](hypervisor.md)