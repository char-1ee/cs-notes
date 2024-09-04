# PTD Parallelism

<figure><img src="../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption><p>PTD</p></figcaption></figure>

1. TP on intra-node transformer layer. TP is compute intensive and demands bandwidth (all-reduce), so run TP within node.
2. PP on inter-node transformer layer. PP less bandwidth (P2P), suitable for cross nodes.
3. DP scales the training with large dataset. But DP is limited by HBM.
