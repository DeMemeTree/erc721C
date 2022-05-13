# ERC721C

## About The Project

The goal of ERC721C somewhat of a fair compromise between NFT project creator and NFT project investor

## Beta

Use at your own risk.

This code is still in beta and undergoing reviews. Ive done about 20 minutes of testing after spending like an hour writing this code. Currently I believe all rounding errors in refunds will go to the project that can be collected at the very end.

## Motivation

https://twitter.com/leonidasnft/status/1524811692307365890?s=21&t=Mx4b0wFh8rQSgREgqsv8qw

## How it works

The NFT creator can split the mint funds into different parts over a time period. Upon the first mint event the first time period begins. The owner can call the withdraw function one time within the first waiting period. However, after that first period refunds are issued when the time index changes.

If the creators has rugs on their mind, the NFT investor can unrug themselves depending on how long the time period has been. The amount of ETH that one recieves back is directly related to what time period the project is currrently in and how many NFT's they return to the owner.

```solidity
uint constant maxSupply = 10000;
uint constant mintPrice = 0.1 ether;
uint constant amountOfPartsToBreakMintFundsInto = 10; // dont make this zero lol
uint constant totalPaymentTimeForPeriods = 365 days;
```

## Usage

When you are feeling suspicion of a rug call unrugMePlz

```solidity
function unrugMePlz(uint[] calldata tokenIds) external nonReentrant {
    require(currentDevCutIndex > 0, "The dev hasnt minted anything");
    updateIndexPotentially();
    require(currentDevCutIndex < amountOfPartsToBreakMintFundsInto);

    uint count = tokenIds.length;
        
    unchecked {
        for (uint256 i = 0; i < count; i++) {
            uint256 tokenId = tokenIds[i];
            require(msg.sender == ownerOf(tokenId), "Not token owner");
            transferFrom(msg.sender, owner(), tokenId);
        }
    }

    uint wheresMyMoney = cutFor(count);
    require(wheresMyMoney > 0, "Hmm nothing to give you back");
    (bool sent, ) = payable(msg.sender).call{value: wheresMyMoney}("");
    require(sent, "Failed to send Ether");
}
```
