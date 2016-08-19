# TheDAOETCTokenBalance
Scripts and data to compute the balances of The DAO token holders on the Ethereum Classic chain

There is a plan for the Goodies who have retrieved ethers from The DAO and it's child DAOs on the Ethereum Classic chain to distribute the ethers (ETC) to The DAO token holders (DTH) based on the DTH's holding just prior to the hard-fork at block 1,919,999 - see [The White Hats and DAO Wars: Behind the Scenes](https://blog.bity.com/2016/08/13/the-white-hats-and-dao-wars-behind-the-scenes/). 

Some DTH have stated that they would prefer to have the ETCs distributed according to the current DTH's holding - see [Reddit - The White Hats and DAO Wars: Behind the Scenes](https://www.reddit.com/r/ethereum/comments/4xlxd3/the_white_hats_and_dao_wars_behind_the_scenes/). I've computed the change in the token balances both at block 1,919,999 and the latest block when I started my script running - block 2,074,689 . There has been some movement in the token balances. This could be due to the replaying of transfer events on the hard-forked blockchain, or through ETC executed transfers (although no exchanges have offered any trading of The DAO tokens on the Classic chain). The data is in the balances file if anyone wants to analyse these post-fork transfers on the Classic chain.

<br />

---

## Summary
The script extracts all `CreatedToken` events in [theDAOTokenBalance_20160815_144633UTC_creation.zip](https://github.com/bokkypoobah/TheDAOETCTokenBalance/blob/master/theDAOTokenBalance_20160815_144633UTC_creation.zip) and all `Transfer` events in [theDAOTokenBalance_20160815_144633UTC_transfer.zip](https://github.com/bokkypoobah/TheDAOETCTokenBalance/blob/master/theDAOTokenBalance_20160815_144633UTC_transfer.zip). All addresses from the `CreatedToken` and `Transfer` events then have their The DAO balance checked at block 1,919,999. The balances of all these addresses match the queried balances from Goodies' `DAOBalanceSnapShot` contract. The results are in [theDAOTokenBalance_20160815_144633UTC_balance.zip](https://github.com/bokkypoobah/TheDAOETCTokenBalance/blob/master/theDAOTokenBalance_20160815_144633UTC_balance.zip) with the spreadsheet version in [theDAOTokenBalance_20160815_144633UTC_balance.xlsx](https://github.com/bokkypoobah/TheDAOETCTokenBalance/blob/master/theDAOTokenBalance_20160815_144633UTC_balance.xlsx). When comparing the differences to 9 decimal places in a spreadsheet - the `PreHardForkDAODiff` column is all 0. And from the summary statistics at the bottom of the _balances text file:

    Stats	nonZeroAccounts	23012
    Stats	daosPreHardForkTotal	1153816598.702436
    Stats	daosPreHardForkContractTotal	1153816598.702436

The number of non-zero accounts is slightly different to the 22092 figure from the Goodies' `DAOBalanceSnapShot` contract, but to 9 decimal places, the total calculated by evaluating each addresss at block 1,919,999 is the same as the total calculated using the data in the Goodies' `DAOBalanceSnapShot` contract.

### Note - Slight Difference In Total Supply

The Goodies' `DAOBalanceSnapShot` contract has a total supply of 11538165987024671407837618 or 1,153,816,598.7024671407837618 DAOs. The sum of my DAO balances for each address totals 1,153,816,598.702436 . There is a difference at the 5 decimal place onwards (which I'm not going to worry about). The Goodies' `DAOBalanceSnapShot` contract is pretty accurate. 

<br />

---

## Goodies' `DAOBalanceSnapShot` Contract
From [ETC withdraw contract to be reviewed](https://www.reddit.com/r/ethereum/comments/4yhz8h/etc_withdraw_contract_to_be_reviewed/), the [DAOBalanceSnapShot](https://github.com/BitySA/whetcwithdraw/blob/master/daobalance/dao_balance_snapshot.sol) contract has been deployed to the ETC chain at [0x180826b05452ce96e157f0708c43381fee64a6b8](http://unforked.info/addr/0x180826b05452ce96e157f0708c43381fee64a6b8). This contract provides the balance for each DTH account just before the hard-fork block 1,919,999. The ABI for this contract is `[{"constant":true,"inputs":[],"name":"totalSupply","outputs":[{"name":"","type":"uint256"}],"type":"function"},{"constant":false,"inputs":[],"name":"seal","outputs":[],"type":"function"},{"constant":true,"inputs":[],"name":"totalAccounts","outputs":[{"name":"","type":"uint256"}],"type":"function"},{"constant":true,"inputs":[{"name":"","type":"address"}],"name":"balanceOf","outputs":[{"name":"","type":"uint256"}],"type":"function"},{"constant":false,"inputs":[{"name":"data","type":"uint256[]"}],"name":"fill","outputs":[],"type":"function"},{"constant":true,"inputs":[],"name":"owner","outputs":[{"name":"","type":"address"}],"type":"function"},{"constant":true,"inputs":[],"name":"sealed","outputs":[{"name":"","type":"bool"}],"type":"function"},{"inputs":[],"type":"constructor"}]`. 

At 01:23 15 Aug 2016 UTC, this contract has the attributes Total supply = 11538165987024671407837618, Total accounts = 22092, Sealed = yes. 

<br />

---

## The Script
A script has been written to extract The DAO token and ether balances of DTHs just prior to the hard-fork and currently - see [getTheDAOTokenBalance](https://github.com/bokkypoobah/TheDAOETCTokenBalance/blob/master/getTheDAOTokenBalance). 

The script:
* Extracts all `TheDAO.CreatedToken` events from the creation of The DAO contract in block 1,428,757 Apr-30-2016 01:42:58 AM +UTC until the end of the creation phase in block 1,599,205 May-28-2016 08:59:47 AM +UTC
* Extracts all `TheDAO.Transfer` events from block 1,599,207 May-28-2016 09:00:07 AM +UTC to the current block
* Save a hashmap of the addresses involved in the `TheDAO.CreatedToken` and `TheDAO.Transfer` events
* Query the blockchain at block 1,919,999 just prior to the hard-fork and currently for all the collected addresses for The DAO token balances and ether balances.

I'm now running the data extraction process.

<br />

---

## Require Full Classic Blockchain
Note that to run this script you will need the full ETC blockchain including intermediate states, not the version downloaded using the `geth --fast --oppose-dao-fork`. The reason for this is that the `debug.traceTransaction(...)` call is used to extract the owner in The DAO token creation process as the origin account could be an exchange (proxy) or an account instructing a wallet contract to purchase The DAO tokens.

If you have already downloaded a `--fast` version, you can `geth export chaindatafile`, rename `.ethereum/chaindata` and `geth import chaindatafile` to rebuild the intermediate states. Note that it may be faster to just re-sync the whole blockchain.

<br />

Enjoy. (c) BokkyPooBah 2016. The MIT licence.
