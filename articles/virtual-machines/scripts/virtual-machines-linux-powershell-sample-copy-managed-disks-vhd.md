---
title: VHD do disco gerenciado para a conta de outra região (Linux) – PowerShell
description: Script de exemplo do Azure PowerShell – Exportar/copiar o VHD de um disco gerenciado para uma conta de armazenamento na mesma ou em outra região
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/17/2018
ms.author: ramankum
ms.openlocfilehash: 125779fcc547aa725f8c218663be23c177b42b97
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87028627"
---
# <a name="exportcopy-the-vhd-of-a-managed-disk-to-a-storage-account-in-different-region-with-powershell-linux"></a>Exportar/copiar o VHD de um disco gerenciado para uma conta de armazenamento em outra região com o PowerShell (Linux)

Esse script exporta o VHD de um disco gerenciado para uma conta de armazenamento em outra região. Primeiro, ele gera o URI do SAS do disco gerenciado e, em seguida, o usa para copiar o VHD subjacente para uma conta de armazenamento em outra região. Use esse script para copiar discos gerenciados para outra região para expansão regional.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Exemplo de script

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-managed-disks-vhd-to-storage-account/copy-managed-disks-vhd-to-storage-account.ps1 "Copy the VHD of a managed disk")]


## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para gerar o URI de SAS de um disco gerenciado e copia o VHD subjacente para uma conta de armazenamento usando o URI de SAS. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [Grant-AzDiskAccess](/powershell/module/az.compute/grant-azdiskaccess) | Gera o URI de SAS para um disco gerenciado usado para copiar o VHD subjacente para uma conta de armazenamento. |
| [New-AzureStorageContext](/powershell/module/azure.storage/new-azurestoragecontext) | Cria um contexto de conta de armazenamento usando o nome da conta e a chave. Esse contexto pode ser usado para executar operações de leitura/gravação na conta de armazenamento. |
| [Start-AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy) | Copia o VHD subjacente de um instantâneo para uma conta de armazenamento |

## <a name="next-steps"></a>Próximas etapas

[Criar um disco gerenciado com base em um VHD](virtual-machines-linux-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Criar uma máquina virtual com base em um disco gerenciado](./virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/).

Amostras de script do PowerShell da máquina virtual adicionais podem ser encontrados na [documentação da VM Linux do Azure](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
