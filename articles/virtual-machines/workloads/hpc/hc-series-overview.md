---
title: Visão geral da VM da série HC-máquinas virtuais do Azure | Microsoft Docs
description: Saiba mais sobre o suporte de visualização para o tamanho da VM da série HC no Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/19/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: de6051e8880bbe3df42031a0d0d7b60abc27d2b0
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689792"
---
# <a name="hc-series-virtual-machine-overview"></a>Visão geral da máquina virtual da série HC

Maximizar o desempenho do aplicativo HPC em processadores escalonáveis Intel Xeon requer uma abordagem cuidadosa para processar o posicionamento nessa nova arquitetura. Aqui, descrevemos nossa implementação de ti em VMs da série Azure HC para aplicativos HPC. Usaremos o termo "pNUMA" para fazer referência a um domínio NUMA físico e "vNUMA" para fazer referência a um domínio NUMA virtualizado. Da mesma forma, usaremos o termo "pCore" para se referir a núcleos de CPU físicos e "vCore" para se referir a núcleos de CPU virtualizados.

Fisicamente, um servidor da [série HC](../../hc-series.md) é de 2 a 24 CPUs Intel Xeon Platinum 8168 de 3 núcleos para um total de 48 núcleos físicos. Cada CPU é um domínio pNUMA único e tem acesso unificado a seis canais de DRAM. As CPUs do Intel Xeon Platinum apresentam um cache L2 maior do que nas gerações anteriores (256 KB/núcleo-> 1 MB/núcleo), enquanto também reduz o cache L3 em comparação com as CPUs anteriores da Intel (2,5 MB/núcleo-> 1,375 MB/núcleo).

A topologia acima também é transferida para a configuração de hipervisor da série HC. Para fornecer espaço para o hipervisor do Azure operar sem interferir na VM, reservamos pCores 0-1 e 24-25 (ou seja, os primeiros 2 pCores em cada soquete). Em seguida, atribuímos domínios pNUMA todos os núcleos restantes à VM. Assim, a VM verá:

`(2 vNUMA domains) * (22 cores/vNUMA) = 44` núcleos por VM

A VM não tem conhecimento de que pCores 0-1 e 24-25 não foram dadas. Portanto, ele expõe cada vNUMA como se tivesse, nativamente, 22 núcleos.

As CPUs Intel Xeon Platinum, Gold e prata também introduzem uma rede de malha 2D em chip para comunicação dentro e externa ao soquete da CPU. É altamente recomendável fixar o processo para desempenho e consistência ideais. A fixação do processo funcionará em VMs da série HC porque o silício subjacente é exposto no estado em que se encontra à VM convidada.

O diagrama a seguir mostra a diferenciação de núcleos reservada para o hipervisor do Azure e a VM da série HC.

![Segregação de núcleos reservada para a VM do Azure hypervisor e da série HC](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Especificações de hardware

| Especificações de hardware          | VM da série HC                     |
|----------------------------------|----------------------------------|
| Núcleos                            | 44 (HT desabilitado)                 |
| CPU                              | Intel Xeon Platinum 8168         |
| Frequência de CPU (não AVX)          | 3,7 GHz (núcleo único), 2.7-3.4 GHz (todos os núcleos) |
| Memória                           | 8 GB/núcleo (total de 352)            |
| Disco local                       | SSD DE 700 GB                       |
| Infiniband                       | 100 GB EDR Mellanox ConnectX-5   |
| Rede                          | 50 GB de Ethernet (40 GB utilizáveis) Azure Second Gen SmartNIC    |

## <a name="software-specifications"></a>Especificações de software

| Especificações de software     |VM da série HC           |
|-----------------------------|-----------------------|
| Tamanho máximo do trabalho MPI            | 13200 núcleos (300 VMs em um único conjunto de dimensionamento de máquinas virtuais com singlePlacementGroup = true)  |
| Suporte a MPI                 | HPC-X, Intel MPI, OpenMPi, MVAPICH2, MPICH, plataforma MPI  |
| Estruturas adicionais       | Comunicação unificada X, libfabric, PGAS |
| Suporte do armazenamento do Azure       | Discos Standard e Premium (máximo de 4 discos) |
| Suporte do so para SRIOV RDMA   | CentOS/RHEL 7.6 +, SLES 12 SP4 +, WinServer 2016 +  |
| Suporte do Orchestrator        | CycleCloud, lote  |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [arquitetura do Intel Xeon SP](https://bit.ly/2RCYkiE).
- Leia os comunicados mais recentes e alguns exemplos e resultados da HPC nos [Blogs da Tech Community da Computação do Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Para obter uma visão de nível superior da arquitetura de execução de cargas de trabalho de HPC, confira [HPC (computação de alto desempenho) no Azure](/azure/architecture/topics/high-performance-computing/).
