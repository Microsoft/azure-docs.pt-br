---
title: Atualizar o dispositivo Coletor nas Migrações para Azure | Microsoft Docs
description: Fornece informações sobre atualizações para o dispositivo Coletor de Migrações para Azure.
author: musa-57
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 03/29/2019
ms.author: hamusa
services: azure-migrate
ms.openlocfilehash: 7cd44318716200d665ece9ffecc45225bdfb85eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60685912"
---
# <a name="collector-appliance-updates"></a>Atualizações do dispositivo coletor

Este artigo resume as informações de atualização para o dispositivo Coletor em [Migrações para Azure](migrate-overview.md).

O Coletor de Migrações para Azure é um dispositivo leve usado para descobrir um ambiente do vCenter local para fins de avaliação antes da migração para o Azure. [Saiba mais](concepts-collector.md).

## <a name="how-to-upgrade-the-appliance"></a>Como atualizar o dispositivo

É possível atualizar o Coletor para a última versão sem baixar o OVA novamente.

1. Feche todas as janelas de navegador e qualquer abra arquivos/pastas no dispositivo.
2. Baixe o pacote de atualização mais recente da lista de atualizações mencionadas a seguir neste artigo.
3. Para garantir que o pacote baixado é seguro, abra a janela de comando de administrador e execute o seguinte comando para gerar o hash do arquivo ZIP. O hash gerado deve corresponder ao hash mencionado em relação à versão específica:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    Exemplo: **C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)**
4. Copie o arquivo zip para a VM do dispositivo Coletor.
5. Clique com o botão direito do mouse no arquivo zip > **Extrair tudo**.
6. Clique com o botão direito do mouse em **Setup.ps1** > **Executar com PowerShell** e siga as instruções de instalação.

## <a name="collector-update-release-history"></a>Histórico de versões de atualização do Coletor

### <a name="continuous-discovery-upgrade-versions"></a>Descoberta contínua: Versões de atualização

#### <a name="version-101014-released-on-03292019"></a>Versão 1.0.10.14 (lançado em 29/03/2019)

Contém alguns aprimoramentos de interface do usuário.

Valores para a atualização de hash [1.0.10.14 do pacote](https://aka.ms/migrate/col/upgrade_10_14)

**Algoritmo** | **Valor de hash**
--- | ---
MD5 | 846b1eb29ef2806bcf388d10519d78e6
SHA1 | 6243239fa49c6b3f5305f77e9fd4426a392d33a0
SHA256 | fb058205c945a83cc4a31842b9377428ff79b08247f3fb8bb4ff30c125aa47ad

#### <a name="version-101012-released-on-03132019"></a>Versão 1.0.10.12 (lançado em 13/03/2019)

Contém correções para problemas na seleção do Azure na nuvem no dispositivo.

Valores para a atualização de hash [1.0.10.12 do pacote](https://aka.ms/migrate/col/upgrade_10_12)

**Algoritmo** | **Valor de hash**
--- | ---
MD5 | 27704154082344c058238000dff9ae44
SHA1 | 41e9e2fb71a8dac14d64f91f0fd780e0d606785e
SHA256 | c6e7504fcda46908b636bfe25b8c73f067e3465b748f77e50027e66f2727c2a9

### <a name="one-time-discovery-deprecated-now-previous-upgrade-versions"></a>Descoberta avulsa (já preterido): Versões anteriores de atualização

> [!NOTE]
> O dispositivo de descoberta única já foi preterido, pois esse método se baseou nas configurações de estatísticas do vCenter Server para disponibilidade de pontos de dados de desempenho e contadores de desempenho médios coletados, o que resultou em subdimensionamento de VMs para migração para o Azure.

#### <a name="version-10916-released-on-10292018"></a>Versão 1.0.9.16 (lançada em 29 de outubro de 2018)

Contém correções para problemas de PowerCLI enfrentadas ao configurar o dispositivo.

Valores de hash para o pacote de [atualização 1.0.9.16](https://aka.ms/migrate/col/upgrade_9_16)

**Algoritmo** | **Valor de hash**
--- | ---
MD5 | d2c53f683b0ec7aaf5ba3d532a7382e1
SHA1 | e5f922a725d81026fa113b0c27da185911942a01
SHA256 | a159063ff508e86b4b3b7b9a42d724262ec0f2315bdba8418bce95d973f80cfc

#### <a name="version-10914"></a>Versão 1.0.9.14

Valores de hash para o pacote de [atualização 1.0.9.14](https://aka.ms/migrate/col/upgrade_9_14)

**Algoritmo** | **Valor de hash**
--- | ---
MD5 | c5bf029e9fac682c6b85078a61c5c79c
SHA1 | af66656951105e42680dfcc3ec3abd3f4da8fdec
SHA256 | 58b685b2707f273aa76f2e1d45f97b0543a8c4d017cd27f0bdb220e6984cc90e

#### <a name="version-10913"></a>Versão 1.0.9.13

Valores de hash para o pacote de [atualização 1.0.9.13](https://aka.ms/migrate/col/upgrade_9_13)

**Algoritmo** | **Valor de hash**
--- | ---
MD5 | 739f588fe7fb95ce2a9b6b4d0bf9917e
SHA1 | 9b3365acad038eb1c62ca2b2de1467cb8eed37f6
SHA256 | 7a49fb8286595f39a29085534f29a623ec2edb12a3d76f90c9654b2f69eef87e


## <a name="next-steps"></a>Próximas etapas

- [Saiba mais](concepts-collector.md) sobre o dispositivo Coletor.
- [Execute uma avaliação](tutorial-assessment-vmware.md) para VMs do VMware.
