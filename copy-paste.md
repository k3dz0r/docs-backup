## Testnet access

To set up SPCTL, you need Super Protocol Testnet access. If you do not have it yet, [apply to join](/testnet/). The Super Protocol team sends out invites daily, but it may take several days if the number of requests is high. You can ask any Community Manager on the [Super Protocol Discord server](https://discord.gg/superprotocol) about the status of your request. When your access is ready, you will receive a Testnet invitation email with your Testnet credentials:
  - _Testnet Account address_: the public key of the Testnet Account wallet
  - _Private Key_: the private key of the Testnet Account wallet
  - _Access Token_: necessary to receive free test TEE and MATIC tokens

## Support

If you have any issues or questions, contact Super Protocol on [Discord](https://discord.gg/superprotocol). The Community Managers will be happy to help you.

## From fundamentals/orders

Below are the most likely combinations when creating an order.

|  | **Solution**        | **Data** | **Base Image**  | **Storage** | **TEE Compute**
|:--|:------------|:------------|:------------|:------------|:------------|
| 1 | User-deployed         | User-deployed  | Offer | Offer | Offer | 
| 2 | Offer        | User-deployed  | Offer | Offer | Offer | 
| 3 | User-deployed        | Offer  | Offer | Offer | Offer | 
| 4| Offer | Offer        | Offer  | Offer | Offer | 

Where:
1. You use your own solution and data;
2. You use a solution offer from the Marketplace, but your own data;
3. You use your own solution, but data from the Marketplace;
4. Both your solution and its data come from the Marketplace.