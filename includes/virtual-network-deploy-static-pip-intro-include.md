您可以在 Azure 中建立虛擬機器 (VM)，並使用公用 IP 位址將它們公開至公用網際網路。 根據預設，公用 Ip 是動態而且與它們相關聯的位址可能會變更當 VM 已刪除或停止/取消配置。 若要保證 VM 一律使用相同的公用 IP 位址，您需要建立靜態公用 IP。 

您必須先了解可以使用靜態公用 IP 的時機和使用方式，才能在 VM 中實作靜態公用 IP。 請閱讀 [IP 位址指定概觀](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md) 以詳細了解 Azure 中的 IP 位址指定。

