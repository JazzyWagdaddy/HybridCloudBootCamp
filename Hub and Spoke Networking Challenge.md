## Hub and Spoke Networking Challenge 
Now that you have vNet1 and vNet2 peered, resources in each virtual network and subnet can communicate with each other.  A common networking architecture is a hub-spoke network topology where the hub is a single virtual network (VNet) in Azure that acts as a central point of connectivity to your virtual networks (VNets). The spokes are VNets that peer with the hub, and can be used to isolate workloads.

Your challenge is to configure vNet2 as the hub in a hub and spoke topology.  vNet1 and vNet3 are not directly connected together.  vNet1 talks to vNet3 via vNet2 and vNet3 talks to vNet1 via vNet2.

Attempt to configure this on your own, otherwise follow these instructions.  You should configure spokes to use the hub VNet gateway to communicate with remote networks. To allow gateway traffic to flow from spoke to hub, and connect to remote networks, you must:
* Configure the VNet peering connection in the hub to allow gateway transit.
* Configure the VNet peering connection in each spoke to use remote gateways.
* Configure all VNet peering connections to allow forwarded traffic.

### Configure vNet3
1. In the Search box at the top of the Azure portal, begin typing **vNet3**. When vNet3 appears in the search results, select it.
2. Select **SETTINGS** then **Peerings**, and then select **+ Add**.
3. Enter, or select, the following information, accept the defaults for the remaining settings:
    * Name: **vNet3-vNet2**
    * Virtual network: **vNet2 (MyVNets)**
    * Configure virtual network access settings: **Enabled**
    * Configure forwarded traffic settings: **Disabled**
    * Enable the checkbox for **Configure gateway transit settings**
    * select **OK**

Peering status - If you don't see the status, refresh your browser.  Notice the status is *Initiated*.

### Configure vNet1
1. In the Search box at the top of the Azure portal, begin typing **vNet3**. When vNet3 appears in the search results, select it.
2. Select **SETTINGS** then **Peerings**, and then select **vNet1-vNet2**.
3. Enable the checkbox for **Configure gateway transit settings**.
4. Select **OK**

