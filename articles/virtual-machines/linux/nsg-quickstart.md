---
title: Abrir portas para uma VM com CLI do Azure
description: Saiba como abrir uma porta/criar um ponto de extremidade para sua VM usando o CLI do Azure.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4c57ec2edf43bf0a710f3c15f30836f751c4d52b
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87500156"
---
# <a name="open-ports-and-endpoints-to-a-vm-with-the-azure-cli"></a>Abrir portas e pontos de extremidade para uma VM com o CLI do Azure

No Azure, você abre uma porta, ou cria um ponto de extremidade, para uma VM (máquina virtual) criando um filtro de rede ou uma sub-rede ou interface de rede de VM. Coloque os filtros, que controlam o tráfego de entrada e saída, em um Grupo de Segurança de Rede anexado ao recurso que recebe o tráfego. Vamos usar um exemplo comum de tráfego da Web na porta 80. Este artigo mostra como abrir uma porta para uma VM usando a CLI do Azure. 


Para criar regras e um Grupo de Segurança de Rede, é necessário ter a última [CLI do Azure](/cli/azure/install-az-cli2) instalada e conectada a uma conta do Azure usando [az login](/cli/azure/reference-index).

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Os nomes de parâmetro de exemplo incluem *myResourceGroup*, *myNetworkSecurityGroup* e *myVnet*.


## <a name="quickly-open-a-port-for-a-vm"></a>Abrir uma porta rapidamente para uma máquina virtual
Se você precisar abrir uma porta rapidamente para uma máquina virtual em um cenário de desenvolvimento/teste, você pode usar o comando [az vm open-port](/cli/azure/vm). Este comando cria um Grupo de Segurança de Rede, adiciona uma regra e a aplica a uma VM ou sub-rede. O exemplo a seguir abre a porta *80* na VM chamada *myVM* no grupo de recursos chamado *myResourceGroup*.

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 80
```

Para obter mais controle sobre as regras, como a definição de um intervalo de endereços IP de origem, continue com as etapas adicionais neste artigo.


## <a name="create-a-network-security-group-and-rules"></a>Criar um Grupo de Segurança de Rede e suas regras
Crie o grupo de segurança de rede com [az network nsg create](/cli/azure/network/nsg). O exemplo a seguir cria um grupo de segurança de rede chamado *myNetworkSecurityGroup* no local *eastus* :

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Adicione uma regra com [az network nsg rule create](/cli/azure/network/nsg/rule) para permitir o tráfego HTTP para o servidor Web (ou faça ajustes para seu próprio cenário, como acesso ao SSH ou conectividade de banco de dados). O exemplo a seguir cria uma regra chamada *myNetworkSecurityGroupRule* para permitir o tráfego TCP na porta 80:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 80
```


## <a name="apply-network-security-group-to-vm"></a>Aplicar o Grupo de Segurança de Rede à VM
Associe o Grupo de Segurança de Rede à NIC (adaptador de rede) da VM com [az network nic update](/cli/azure/network/nic). O exemplo a seguir associa uma NIC existente chamada *myNic* ao Grupo de Segurança de Rede chamado *myNetworkSecurityGroup*:

```azurecli
az network nic update \
    --resource-group myResourceGroup \
    --name myNic \
    --network-security-group myNetworkSecurityGroup
```

Como alternativa, você pode associar o Grupo de Segurança de Rede a uma sub-rede de uma rede virtual com [az network vnet subnet update](/cli/azure/network/vnet/subnet) em vez de apenas ao adaptador de rede em uma única VM. O exemplo a seguir associa uma sub-rede existente chamada *mySubnet* na rede virtual *myVnet* ao Grupo de Segurança de Rede chamado *myNetworkSecurityGroup*:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>Mais informações sobre os Grupos de Segurança de Rede
Os comandos rápidos aqui permitem que você coloque tudo em funcionamento com o tráfego que flui para sua VM. Os Grupos de Segurança de Rede fornecem muitos recursos excelentes e granularidade para controlar o acesso aos recursos. Você pode ler mais sobre a [criação de um Grupo de Segurança de Rede e as regras ACL aqui](tutorial-virtual-network.md#secure-network-traffic).

Para aplicativos Web altamente disponíveis, você deve colocar suas VMs atrás de um balanceador de carga do Azure. O balanceador de carga distribui o tráfego para VMs, com um Grupo de Segurança de rede que fornece filtragem. Para saber mais, veja [Como balancear a carga de máquinas virtuais Linux no Azure para criar um aplicativo altamente disponível](tutorial-load-balancer.md).

## <a name="next-steps"></a>Próximas etapas
Neste exemplo, você criou uma regra simples para permitir o tráfego HTTP. Você pode encontrar informações sobre a criação de ambientes mais detalhados nos seguintes artigos:

* [Visão geral do Azure Resource Manager](../../azure-resource-manager/management/overview.md)
* [O que é um Grupo de Segurança de Rede (NSG)?](../../virtual-network/security-overview.md)
