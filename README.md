# Wrapped Number Board

This wrapper allows you to hold and transfer Number Board cards in an ERC-721 complaint contract.

## Deployment

Wrapped Number Board deployment (Ethereum Mainnet): 0x2576691E74E02b57F2b7CaCE8129386A064A0793

[Play using Etherscan.](https://etherscan.io/address/0x2576691E74E02b57F2b7CaCE8129386A064A0793#writeContract)

## Underlying contract

Underlying Number Board deployment (Ethereum Mainnet): 0x9249133819102b2ed31680468c8c67F6Fe9E7505

The Number Board contract was deployed November 16, 2017 and has thousands of transactions, many of which are from 2021.

[:warning: Play using Etherscan.](https://etherscan.io/address/0x9249133819102b2ed31680468c8c67F6Fe9E7505#writeContract)

:warning: Avoid using the marketplace in the underlying contract. You can use this wrapped token to transfer ownership of your Number Board cards.

## How to use it—wrapping

*:stop_sign: Read all notes before performing.*

1. Use the underlying Number Board contract to call `placeBuyNowOffer` and set an ABOVE MARKET price for your Number Board card.
2. Use the Wrapped Number Board contract to call `wrap` for that Number Board card, send payment equal to the price above. This will happen from a different account and that will become the owner of the ERC-721 wrapped token.
3. Withdraw your payment from the underlying Number Board contact by calling `withdrawEarnings`.

When you choose which price to set, note that there is a balance. Setting the price too low places your Number Board card at risk of somebody else quickly jumping in and buying it at your low price in between the time that you do step 1 and step 2. :information_source: If you are using an externally-owned account (i.e. you are not a smart contract) then it is always possible somebody will jump in between any two of your transactions. Setting the price too high requires you to have that much funds available and also costs you 1% of that amount in transfer fees (as required by the underlying contract).

Example for wrapping number 2662 with a price of 0.05 Ether:

1. [Call underlying `placeBuyNowOffer`](https://etherscan.io/tx/0xe09ea1de507b6a9c25f280a558abd1ce32bb39c5a5e0653b7a6fc193c96995e5)
2. [Call wrapped `wrap`](https://etherscan.io/tx/0xfe8fddd7278cfdff1d7eef182a65913505f9912fbf2c27dc1f49559e070f6c81) from the benefiary account
   1. The payment price of 0.05 Ether [is transferred](https://etherscan.io/tx/0xfe8fddd7278cfdff1d7eef182a65913505f9912fbf2c27dc1f49559e070f6c81#internal)
   2. The wrapped token [2662 is created](https://etherscan.io/token/0x2576691e74e02b57f2b7cace8129386a064a0793?a=2662)
3. [Call urderlying `withdrawEarnings`](https://etherscan.io/tx/0x3365bc5f2fbf51bd0370c882e26c3827d32b23b19345d542903b6e3f9a5d09f8)
   1. The payment price of 0.0495 Ether [is paid out](https://etherscan.io/tx/0x3365bc5f2fbf51bd0370c882e26c3827d32b23b19345d542903b6e3f9a5d09f8#internal) (this is 0.05 minus a 1% from underlying contract)

## How to use it—unwrapping

Unwrapping requires is efficient in terms of fees—the minimum transfer fee is paid to the contract. And there is no risk of another person jumping in during your transfer. We achieve this by requirng all unwraps to be performed by a smart contract, and that allows the smart contract to ensure that several operations are performed in succession without any possible interjection by others.

1. Call `unwrap` on the Wrapped Number Board contract. :warning: At this point, ANYBODY can take the Number Board card for a low price.
2. Execute `acceptBuyNowOffer` on the underlying Number Board contract to take the Number Board card. Include a value transfer of `MIN_PRICE` which is 1 Finney (i.e. 0.001 Ether). :warning: At this time anybody can take your payment proceeds.
3. Call `withdrawEarnings` on the Wrapped Number Board contract. This pays out your payment proceeds (the amount you sent in step 2).
4. Revert if anything didn't work.