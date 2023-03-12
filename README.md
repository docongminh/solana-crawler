## Crawl solana transaction

```rust
use anyhow::Result;
use solana_client::rpc_client::RpcClient;
use solana_transaction_crawler::{
    crawler::{Crawler, IxAccount},
    filters::{IxNumberAccounts, SuccessfulTxFilter, TxHasProgramId},
};
use solana_program::pubkey;

#[tokio::main]
async fn main() -> Result<()> {
    let client = RpcClient::new("https://rpc.ankr.com/solana");
    let candy_machine_id = pubkey!("9MynErYQ5Qi6obp4YwwdoDmXkZ1hYVtPUqYmJJ3rZ9Kn");
    let mut crawler = Crawler::new(client, candy_machine_id);
    let has_program_id = TxHasProgramId::new("cndyAnrLdpjq1Ssp1z8xxDsB8dxe7u4HL5Nxi2K5WXZ");

    let successful_tx = SuccessfulTxFilter;
    let ix_num_accounts = IxNumberAccounts::EqualTo(14);
    let mint_account = IxAccount::unparsed("mint", 5);

    crawler
        .add_tx_filter(has_program_id)
        .add_tx_filter(successful_tx)
        .add_ix_filter(ix_num_accounts)
        .add_account_index(mint_account);

    let crawled_accounts = crawler.run().await?;
    
    let mint_addresses = &crawled_accounts["mint"];
    println!("Items found: {:?}", mint_addresses.len());

    Ok(())
}
```
