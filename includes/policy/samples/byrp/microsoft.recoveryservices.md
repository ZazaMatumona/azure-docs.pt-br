---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e5a725fc6e6bebbc58f25d1f4a1893fe0313e230
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86276135"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Backup do Azure deve ser habilitado para máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Essa política ajuda a auditar se o serviço de Backup do Azure está habilitado para todas as máquinas virtuais. O Backup do Azure é uma solução de backup econômica e com um clique que simplifica a recuperação de dados e é mais fácil de habilitar do que outros serviços de backup em nuvem. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|[Configurar o backup em VMs de um local para um cofre central existente no mesmo local](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |Essa política configura a proteção do Backup do Azure em VMs em um determinado local para um cofre central existente no mesmo local. Ele se aplica apenas às VMs que ainda não estão configuradas para backup. É recomendável que essa política seja atribuída a no máximo 200 VMs. Se a política for atribuída a mais de 200 VMs, isso poderá fazer com que o backup seja disparado algumas horas além do agendamento definido. Essa política será aprimorada para dar suporte a mais imagens de VM. |deployIfNotExists, auditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json) |
|[Implantar as configurações de diagnóstico do cofre dos Serviços de Recuperação no workspace do Log Analytics para categorias específicas do recurso.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc717fb0c-d118-4c43-ab3d-ece30ac81fb3) |Implante as configurações de diagnóstico do cofre dos Serviços de Recuperação para transmitir para o workspace do Log Analytics de categorias específicas do recurso. Se uma das categorias específicas do recurso não estiver habilitada, uma configuração de diagnóstico será criada. |deployIfNotExists |[1.0.0 – versão prévia](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/EnableRecoveryServiceVaultDiagnosticSetting_Backup_DeployIfNotExist.json) |
